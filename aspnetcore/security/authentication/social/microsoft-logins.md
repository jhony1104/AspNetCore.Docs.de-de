---
title: Setup von Microsoft Account externen Anmeldung mit ASP.NET Core
author: rick-anderson
description: Dieses Tutorial veranschaulicht die Integration von Microsoft-Konto-Benutzerauthentifizierung in eine vorhandene ASP.NET Core-app.
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 1733d049d6752c24d7749b5b5ae2a4b866492358
ms.sourcegitcommit: 3e9e1f6d572947e15347e818f769e27dea56b648
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/30/2019
ms.locfileid: "58750998"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="b5e7c-103">Setup von Microsoft Account externen Anmeldung mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b5e7c-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="b5e7c-104">Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b5e7c-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b5e7c-105">In diesem Tutorial erfahren Sie, wie Sie Benutzern die Anmeldung mit ihrem Microsoft-Kontos mit einem ASP.NET Core 2.0-Beispielprojekt auf erstellt ermöglichen, sich die [Vorgängerseite](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="b5e7c-105">This tutorial shows you how to enable your users to sign in with their Microsoft account using a sample ASP.NET Core 2.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="b5e7c-106">Erstellen einer app in Microsoft-Entwicklerportal</span><span class="sxs-lookup"><span data-stu-id="b5e7c-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="b5e7c-107">Navigieren Sie zu [ https://apps.dev.microsoft.com ](https://apps.dev.microsoft.com) erstellen, und melden Sie sich bei einem Microsoft-Konto:</span><span class="sxs-lookup"><span data-stu-id="b5e7c-107">Navigate to [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com) and create or sign into a Microsoft account:</span></span>

![Anmeldedialogfeld](index/_static/MicrosoftDevLogin.png)

<span data-ttu-id="b5e7c-109">Wenn Sie bereits über ein Microsoft-Konto haben, tippen Sie auf  **[erstellen!](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=13&ct=1478151035&rver=6.7.6643.0&wp=SAPI_LONG&wreply=https%3a%2f%2fapps.dev.microsoft.com%2fLoginPostBack&id=293053&aadredir=1&contextid=D70D4F21246BAB50&bk=1478151036&uiflavor=web&uaid=f0c3de863a914c358b8dc01b1ff49e85&mkt=EN-US&lc=1033&lic=1)**</span><span class="sxs-lookup"><span data-stu-id="b5e7c-109">If you don't already have a Microsoft account, tap **[Create one!](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=13&ct=1478151035&rver=6.7.6643.0&wp=SAPI_LONG&wreply=https%3a%2f%2fapps.dev.microsoft.com%2fLoginPostBack&id=293053&aadredir=1&contextid=D70D4F21246BAB50&bk=1478151036&uiflavor=web&uaid=f0c3de863a914c358b8dc01b1ff49e85&mkt=EN-US&lc=1033&lic=1)**</span></span> <span data-ttu-id="b5e7c-110">Nach der Anmeldung werden Sie zur weitergeleitet **meiner Anwendungen** Seite:</span><span class="sxs-lookup"><span data-stu-id="b5e7c-110">After signing in you are redirected to **My applications** page:</span></span>

![Microsoft Developer-Portal in Microsoft Edge öffnen](index/_static/MicrosoftDev.png)

* <span data-ttu-id="b5e7c-112">Tippen Sie auf **fügen Sie eine app** in der oberen rechten Ecke, und geben Sie Ihre **Anwendungsname** und **Contact Email**:</span><span class="sxs-lookup"><span data-stu-id="b5e7c-112">Tap **Add an app** in the upper right corner and enter your **Application Name** and **Contact Email**:</span></span>

![Dialogfeld "neue Anwendungsregistrierung"](index/_static/MicrosoftDevAppCreate.png)

* <span data-ttu-id="b5e7c-114">Deaktivieren Sie für die Zwecke dieses Tutorials die **geführtes Setup** Kontrollkästchen.</span><span class="sxs-lookup"><span data-stu-id="b5e7c-114">For the purposes of this tutorial, clear the **Guided Setup** check box.</span></span>

* <span data-ttu-id="b5e7c-115">Tippen Sie auf **erstellen** , weiterhin die **Registrierung** Seite.</span><span class="sxs-lookup"><span data-stu-id="b5e7c-115">Tap **Create** to continue to the **Registration** page.</span></span> <span data-ttu-id="b5e7c-116">Geben Sie eine **Name** und notieren Sie den Wert der **Anwendungs-Id**, die Sie als verwenden `ClientId` später im Tutorial:</span><span class="sxs-lookup"><span data-stu-id="b5e7c-116">Provide a **Name** and note the value of the **Application Id**, which you use as `ClientId` later in the tutorial:</span></span>

![Registrierungsseite](index/_static/MicrosoftDevAppReg.png)

* <span data-ttu-id="b5e7c-118">Tippen Sie auf **Plattform hinzufügen** in die **Plattformen** aus, und wählen Sie die **Web** Plattform:</span><span class="sxs-lookup"><span data-stu-id="b5e7c-118">Tap **Add Platform** in the **Platforms** section and select the **Web** platform:</span></span>

![Plattform-Dialogfeld "hinzufügen"](index/_static/MicrosoftDevAppPlatform.png)

* <span data-ttu-id="b5e7c-120">In der neuen **Web** Plattform Geben Sie Ihre Entwicklung-URL mit `/signin-microsoft` angefügt, die in der **Umleitungs-URLs** Feld (z. B.: `https://localhost:44320/signin-microsoft`).</span><span class="sxs-lookup"><span data-stu-id="b5e7c-120">In the new **Web** platform section, enter your development URL with `/signin-microsoft` appended into the **Redirect URLs** field (for example: `https://localhost:44320/signin-microsoft`).</span></span> <span data-ttu-id="b5e7c-121">Das Microsoft-Authentifizierungsschema, das später in diesem Tutorial konfiguriert automatisch behandelt Anforderungen an `/signin-microsoft` Route zum Implementieren des OAuth-Flows:</span><span class="sxs-lookup"><span data-stu-id="b5e7c-121">The Microsoft authentication scheme configured later in this tutorial will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow:</span></span>

![Web-Plattform-Abschnitt](index/_static/MicrosoftRedirectUri.png)

> [!NOTE]
> <span data-ttu-id="b5e7c-123">Der URI-Segment `/signin-microsoft` ist als der standardrückruf des Authentifizierungsanbieters Microsoft festgelegt.</span><span class="sxs-lookup"><span data-stu-id="b5e7c-123">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="b5e7c-124">Sie können den standardrückruf-URI ändern, während der Konfiguration der Microsoft-Authentifizierung-Middleware über die geerbte [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) Eigenschaft der [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) Klasse.</span><span class="sxs-lookup"><span data-stu-id="b5e7c-124">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

* <span data-ttu-id="b5e7c-125">Tippen Sie auf **-URL hinzufügen** um sicherzustellen, dass die URL wurde hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b5e7c-125">Tap **Add URL** to ensure the URL was added.</span></span>

* <span data-ttu-id="b5e7c-126">Füllen Sie ggf. Weitere Anwendungseinstellungen, bei Bedarf, und tippen Sie auf **speichern** am unteren Rand der Seite, app-Konfiguration zu speichern.</span><span class="sxs-lookup"><span data-stu-id="b5e7c-126">Fill out any other application settings if necessary and tap **Save** at the bottom of the page to save changes to app configuration.</span></span>

* <span data-ttu-id="b5e7c-127">Bei der Bereitstellung der Website müssen Sie noch einmal die **Registrierung** Seite, und legen Sie eine neue Öffentliche URL.</span><span class="sxs-lookup"><span data-stu-id="b5e7c-127">When deploying the site you'll need to revisit the **Registration** page and set a new public URL.</span></span>

## <a name="store-microsoft-application-id-and-password"></a><span data-ttu-id="b5e7c-128">Store Microsoft Anwendungs-Id und Kennwort</span><span class="sxs-lookup"><span data-stu-id="b5e7c-128">Store Microsoft Application Id and Password</span></span>

* <span data-ttu-id="b5e7c-129">Beachten Sie die `Application Id` angezeigt, auf die **Registrierung** Seite.</span><span class="sxs-lookup"><span data-stu-id="b5e7c-129">Note the `Application Id` displayed on the **Registration** page.</span></span>

* <span data-ttu-id="b5e7c-130">Tippen Sie auf **neues Kennwort generieren** in die **Anwendungsgeheimnisse** Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="b5e7c-130">Tap **Generate New Password** in the **Application Secrets** section.</span></span> <span data-ttu-id="b5e7c-131">Dieser Befehl zeigt ein Feld, in dem Sie das anwendungskennwort kopieren können:</span><span class="sxs-lookup"><span data-stu-id="b5e7c-131">This displays a box where you can copy the application password:</span></span>

![Dialogfeld "Neues Kennwort wurde generiert"](index/_static/MicrosoftDevPassword.png)

<span data-ttu-id="b5e7c-133">Verknüpfen Sie sensible Einstellungen wie Microsoft `Application ID` und `Password` zu Ihrer Anwendungskonfigurationsdatei mithilfe der [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="b5e7c-133">Link sensitive settings like Microsoft `Application ID` and `Password` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="b5e7c-134">Benennen Sie die Token für die Zwecke dieses Lernprogramms, `Authentication:Microsoft:ApplicationId` und `Authentication:Microsoft:Password`.</span><span class="sxs-lookup"><span data-stu-id="b5e7c-134">For the purposes of this tutorial, name the tokens `Authentication:Microsoft:ApplicationId` and `Authentication:Microsoft:Password`.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="b5e7c-135">Konfigurieren von Microsoft-Kontoauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="b5e7c-135">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="b5e7c-136">Die Projektvorlage, die in diesem Tutorial verwendete wird sichergestellt, dass [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount) Paket ist bereits installiert.</span><span class="sxs-lookup"><span data-stu-id="b5e7c-136">The project template used in this tutorial ensures that [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount) package is already installed.</span></span>

* <span data-ttu-id="b5e7c-137">Um dieses Paket mit Visual Studio 2017 zu installieren, mit der Maustaste, auf das Projekt, und wählen **NuGet-Pakete verwalten**.</span><span class="sxs-lookup"><span data-stu-id="b5e7c-137">To install this package with Visual Studio 2017, right-click on the project and select **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="b5e7c-138">Führen Sie Folgendes in Ihrem Projektverzeichnis, um mit .NET Core-CLI zu installieren:</span><span class="sxs-lookup"><span data-stu-id="b5e7c-138">To install with .NET Core CLI, execute the following in your project directory:</span></span>

   `dotnet add package Microsoft.AspNetCore.Authentication.MicrosoftAccount`

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b5e7c-139">Fügen Sie den Microsoft-Account-Dienst in der `ConfigureServices` -Methode in der *"Startup.cs"* Datei:</span><span class="sxs-lookup"><span data-stu-id="b5e7c-139">Add the Microsoft Account service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

```csharp
services.AddDefaultIdentity<IdentityUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();

services.AddAuthentication().AddMicrosoftAccount(microsoftOptions =>
{
    microsoftOptions.ClientId = Configuration["Authentication:Microsoft:ApplicationId"];
    microsoftOptions.ClientSecret = Configuration["Authentication:Microsoft:Password"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b5e7c-140">Fügen Sie die Microsoft-Account-Middleware in der `Configure` -Methode in der *"Startup.cs"* Datei:</span><span class="sxs-lookup"><span data-stu-id="b5e7c-140">Add the Microsoft Account middleware in the `Configure` method in *Startup.cs* file:</span></span>

```csharp
app.UseMicrosoftAccountAuthentication(new MicrosoftAccountOptions()
{
    ClientId = Configuration["Authentication:Microsoft:ApplicationId"],
    ClientSecret = Configuration["Authentication:Microsoft:Password"]
});
```

::: moniker-end

<span data-ttu-id="b5e7c-141">Obwohl die Terminologie, die auf Microsoft-Entwicklerportal diese Token Namen `ApplicationId` und `Password`, sie als verfügbar gemacht werden `ClientId` und `ClientSecret` zum Konfigurations-API.</span><span class="sxs-lookup"><span data-stu-id="b5e7c-141">Although the terminology used on Microsoft Developer Portal names these tokens `ApplicationId` and `Password`, they're exposed as `ClientId` and `ClientSecret` to the configuration API.</span></span>

<span data-ttu-id="b5e7c-142">Finden Sie unter den [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API-Referenz für Weitere Informationen zu den Konfigurationsoptionen, die von der Microsoft-Account-Authentifizierung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="b5e7c-142">See the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference for more information on configuration options supported by Microsoft Account authentication.</span></span> <span data-ttu-id="b5e7c-143">Dies kann verwendet werden, um verschiedene Informationen über den Benutzer anzufordern.</span><span class="sxs-lookup"><span data-stu-id="b5e7c-143">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="b5e7c-144">Melden Sie sich mit Microsoft-Konto</span><span class="sxs-lookup"><span data-stu-id="b5e7c-144">Sign in with Microsoft Account</span></span>

<span data-ttu-id="b5e7c-145">Führen Sie die Anwendung, und klicken Sie auf **melden Sie sich bei**.</span><span class="sxs-lookup"><span data-stu-id="b5e7c-145">Run your application and click **Log in**.</span></span> <span data-ttu-id="b5e7c-146">Eine Option aus, um sich mit Microsoft anmelden wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="b5e7c-146">An option to sign in with Microsoft appears:</span></span>

![Webanwendung Log auf: Benutzer nicht authentifiziert.](index/_static/DoneMicrosoft.png)

<span data-ttu-id="b5e7c-148">Wenn Sie auf Microsoft klicken, werden Sie für die Authentifizierung an Microsoft weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="b5e7c-148">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="b5e7c-149">Nachdem Sie sich mit Ihrem Microsoft Account (sofern nicht bereits angemeldet) werden Sie aufgefordert, zu der app den Zugriff auf Ihre Infos ermöglichen:</span><span class="sxs-lookup"><span data-stu-id="b5e7c-149">After signing in with your Microsoft Account (if not already signed in) you will be prompted to let the app access your info:</span></span>

![Dialogfeld "Microsoft-Authentifizierung"](index/_static/MicrosoftLogin.png)

<span data-ttu-id="b5e7c-151">Tippen Sie auf **Ja** und Sie gelangen zurück auf der Website, in dem Sie Ihre e-Mail-Adresse festlegen können.</span><span class="sxs-lookup"><span data-stu-id="b5e7c-151">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="b5e7c-152">Sie sind jetzt angemeldet, sich mit Ihren Microsoft-Anmeldeinformationen:</span><span class="sxs-lookup"><span data-stu-id="b5e7c-152">You are now logged in using your Microsoft credentials:</span></span>

![-Webanwendung: Benutzerauthentifizierung](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="b5e7c-154">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="b5e7c-154">Troubleshooting</span></span>

* <span data-ttu-id="b5e7c-155">Wenn der Microsoft-Account-Anbieter auf eine Fehler-Anmeldeseite umleitet, beachten Sie die Fehler Titel und Beschreibung Abfragezeichenfolgen-Parameter direkt nach der `#` (Hashtag) im Uri.</span><span class="sxs-lookup"><span data-stu-id="b5e7c-155">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="b5e7c-156">Obwohl die Fehlermeldung ein Problem mit der Microsoft-Authentifizierung angeben anscheinend, ist die häufigste Ursache Ihrer Anwendungs-Uri, die nicht mit einer der der **Umleitungs-URIs** für die **Web** Plattform .</span><span class="sxs-lookup"><span data-stu-id="b5e7c-156">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="b5e7c-157">**ASP.NET Core 2.x nur:** Wenn Identität nicht, durch den Aufruf konfiguriert ist `services.AddIdentity` in `ConfigureServices`, Authentifizierungsversuch führt zu *ArgumentException: Die Option 'SignInScheme' muss angegeben werden*.</span><span class="sxs-lookup"><span data-stu-id="b5e7c-157">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="b5e7c-158">Die Projektvorlage, die in diesem Tutorial verwendete wird sichergestellt, dass dies geschehen ist.</span><span class="sxs-lookup"><span data-stu-id="b5e7c-158">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="b5e7c-159">Wenn die Standortdatenbank nicht erstellt wurde, indem die ursprüngliche Migration anwenden, erhalten Sie *Fehler bei ein Datenbankvorgang beim Verarbeiten der Anforderung* Fehler.</span><span class="sxs-lookup"><span data-stu-id="b5e7c-159">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="b5e7c-160">Tippen Sie auf **Migrationen anwenden** der Datenbank zu erstellen und aktualisieren, um den Fehler hinaus fortgesetzt.</span><span class="sxs-lookup"><span data-stu-id="b5e7c-160">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b5e7c-161">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="b5e7c-161">Next steps</span></span>

* <span data-ttu-id="b5e7c-162">In diesem Artikel wurde gezeigt, wie Sie mit Microsoft authentifizieren können.</span><span class="sxs-lookup"><span data-stu-id="b5e7c-162">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="b5e7c-163">Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="b5e7c-163">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="b5e7c-164">Nachdem Sie Ihre Website in Azure-Web-app veröffentlichen, erstellen Sie eine neue `Password` im Microsoft Developer Portal.</span><span class="sxs-lookup"><span data-stu-id="b5e7c-164">Once you publish your web site to Azure web app, you should create a new `Password` in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="b5e7c-165">Legen Sie die `Authentication:Microsoft:ApplicationId` und `Authentication:Microsoft:Password` Anwendungseinstellungen im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="b5e7c-165">Set the `Authentication:Microsoft:ApplicationId` and `Authentication:Microsoft:Password` as application settings in the Azure portal.</span></span> <span data-ttu-id="b5e7c-166">Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="b5e7c-166">The configuration system is set up to read keys from environment variables.</span></span>

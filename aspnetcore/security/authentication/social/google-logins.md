---
title: Google externe Anmeldung Setup in ASP.NET Core
author: rick-anderson
description: Dieses Tutorial veranschaulicht die Integration von Google-Konto der Benutzerauthentifizierung in eine vorhandene ASP.NET Core-app.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/19/2019
uid: security/authentication/google-logins
ms.openlocfilehash: e12d831d2e0a5c9acae5ea41fb4187ad4ca6b0ea
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082488"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="b5e05-103">Google externe Anmeldung Setup in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b5e05-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="b5e05-104">Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b5e05-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b5e05-105">[Ältere Google +-APIs wurden seit dem 7. März 2019 heruntergefahren](https://developers.google.com/+/api-shutdown).</span><span class="sxs-lookup"><span data-stu-id="b5e05-105">[Legacy Google+ APIs have been shut down as of March 7, 2019](https://developers.google.com/+/api-shutdown).</span></span> <span data-ttu-id="b5e05-106">Google +-Anmeldung und Entwickler müssen zu einem neuen Google-Anmeldesystem wechseln.</span><span class="sxs-lookup"><span data-stu-id="b5e05-106">Google+ sign in and developers must move to a new Google sign in system.</span></span> <span data-ttu-id="b5e05-107">Die ASP.net Core 2,1-und 2,2-Pakete für die Google-Authentifizierung wurden aktualisiert, um die Änderungen zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="b5e05-107">The ASP.NET Core 2.1 and 2.2 packages for Google Authentication have be updated to accommodate the changes.</span></span> <span data-ttu-id="b5e05-108">Weitere Informationen und vorübergehende entschärfungen für ASP.net Core finden Sie in [diesem GitHub-Problem](https://github.com/aspnet/AspNetCore/issues/6486).</span><span class="sxs-lookup"><span data-stu-id="b5e05-108">For more information and temporary mitigations for ASP.NET Core, see [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/6486).</span></span> <span data-ttu-id="b5e05-109">Dieses Tutorial wurde mit dem neuen Setup Vorgang aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="b5e05-109">This tutorial has been updated with the new setup process.</span></span>

<span data-ttu-id="b5e05-110">In diesem Tutorial wird gezeigt, wie Sie es Benutzern mithilfe des auf der [vorherigen Seite](xref:security/authentication/social/index)erstellten Projekts ASP.net Core 2,2 ermöglichen, sich mit Ihrem Google-Konto anzumelden.</span><span class="sxs-lookup"><span data-stu-id="b5e05-110">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 2.2 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="b5e05-111">Erstellen Sie ein Google API-Konsolen Projekt und eine Client-ID.</span><span class="sxs-lookup"><span data-stu-id="b5e05-111">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="b5e05-112">Navigieren Sie zu [integrieren der Google-Anmeldung in Ihre Web-App](https://developers.google.com/identity/sign-in/web/devconsole-project) , und wählen Sie **Projekt konfigurieren**aus.</span><span class="sxs-lookup"><span data-stu-id="b5e05-112">Navigate to [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/devconsole-project) and select **CONFIGURE A PROJECT**.</span></span>
* <span data-ttu-id="b5e05-113">Wählen Sie im Dialogfeld **Konfigurieren des OAuth-Clients** den **Webserver**aus.</span><span class="sxs-lookup"><span data-stu-id="b5e05-113">In the **Configure your OAuth client** dialog, select **Web server**.</span></span>
* <span data-ttu-id="b5e05-114">Legen Sie im Textfeld **autorisierte Umleitungs-URIs** den Umleitungs-URI fest.</span><span class="sxs-lookup"><span data-stu-id="b5e05-114">In the **Authorized redirect URIs** text entry box, set the redirect URI.</span></span> <span data-ttu-id="b5e05-115">Beispiel: `https://localhost:5001/signin-google`</span><span class="sxs-lookup"><span data-stu-id="b5e05-115">For example, `https://localhost:5001/signin-google`</span></span>
* <span data-ttu-id="b5e05-116">Speichern Sie die **Client-ID** und den **geheimen Client**Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="b5e05-116">Save the **Client ID** and **Client Secret**.</span></span>
* <span data-ttu-id="b5e05-117">Wenn Sie den Standort bereitstellen, registrieren Sie die neue öffentliche URL in der **Google-Konsole**.</span><span class="sxs-lookup"><span data-stu-id="b5e05-117">When deploying the site, register the new public url from the **Google Console**.</span></span>

## <a name="store-google-clientid-and-clientsecret"></a><span data-ttu-id="b5e05-118">Store-Google-ClientID und ClientSecret</span><span class="sxs-lookup"><span data-stu-id="b5e05-118">Store Google ClientID and ClientSecret</span></span>

<span data-ttu-id="b5e05-119">Speichern Sie sensible Einstellungen wie Google `Client ID` und `Client Secret` mit dem [Geheimnis-Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="b5e05-119">Store sensitive settings such as the Google `Client ID` and `Client Secret` with the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="b5e05-120">Benennen Sie die Token `Authentication:Google:ClientId` für die Zwecke dieses Tutorials und: `Authentication:Google:ClientSecret`</span><span class="sxs-lookup"><span data-stu-id="b5e05-120">For the purposes of this tutorial, name the tokens `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

```dotnetcli
dotnet user-secrets set "Authentication:Google:ClientId" "X.apps.googleusercontent.com"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="b5e05-121">Sie können Ihre API-Anmelde Informationen und die Verwendung in der [API-Konsole](https://console.developers.google.com/apis/dashboard)verwalten.</span><span class="sxs-lookup"><span data-stu-id="b5e05-121">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="b5e05-122">Konfigurieren der Google-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="b5e05-122">Configure Google authentication</span></span>

<span data-ttu-id="b5e05-123">Fügen Sie den Google- `Startup.ConfigureServices`Dienst hinzu:</span><span class="sxs-lookup"><span data-stu-id="b5e05-123">Add the Google service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupGoogle.cs?name=snippet_ConfigureServices&highlight=10-18)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="b5e05-124">Mit Google anmelden</span><span class="sxs-lookup"><span data-stu-id="b5e05-124">Sign in with Google</span></span>

* <span data-ttu-id="b5e05-125">Führen Sie die APP aus, und klicken Sie auf **Anmelden**.</span><span class="sxs-lookup"><span data-stu-id="b5e05-125">Run the app and click **Log in**.</span></span> <span data-ttu-id="b5e05-126">Eine Option zum Anmelden mit Google wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b5e05-126">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="b5e05-127">Klicken Sie auf die **Google** -Schaltfläche, die zur Authentifizierung an Google umgeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="b5e05-127">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="b5e05-128">Nachdem Sie Ihre Google-Anmelde Informationen eingegeben haben, werden Sie an die Website zurückgeleitet.</span><span class="sxs-lookup"><span data-stu-id="b5e05-128">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="b5e05-129">Weitere Informationen <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> zu von der Google-Authentifizierung unterstützten Konfigurationsoptionen finden Sie in der API-Referenz.</span><span class="sxs-lookup"><span data-stu-id="b5e05-129">See the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="b5e05-130">Dies kann verwendet werden, um verschiedene Informationen über den Benutzer anzufordern.</span><span class="sxs-lookup"><span data-stu-id="b5e05-130">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="b5e05-131">Ändern des Standard-Rückruf-URI</span><span class="sxs-lookup"><span data-stu-id="b5e05-131">Change the default callback URI</span></span>

<span data-ttu-id="b5e05-132">Der URI-Segment `/signin-google` als den standardrückruf des Google-Authentifizierungsanbieter festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="b5e05-132">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="b5e05-133">Sie können den standardrückruf-URI ändern, während der Konfiguration die Middleware für die Google-Authentifizierung über die geerbte [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) Eigenschaft der [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) Klasse.</span><span class="sxs-lookup"><span data-stu-id="b5e05-133">You can change the default callback URI while configuring the Google authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="b5e05-134">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="b5e05-134">Troubleshooting</span></span>

* <span data-ttu-id="b5e05-135">Wenn die Anmeldung nicht funktioniert und Sie keine Fehler erhalten, wechseln Sie in den Entwicklungsmodus, um das Problem zu vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="b5e05-135">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="b5e05-136">Wenn `services.AddIdentity` dieIdentität`ConfigureServices`nicht durch Aufrufen von konfiguriert wird, führt der Versuch der Authentifizierung zu einer argumumtexception: *Die Option "signinscheme" muss angegeben*werden.</span><span class="sxs-lookup"><span data-stu-id="b5e05-136">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="b5e05-137">Die Projektvorlage, die in diesem Tutorial verwendete wird sichergestellt, dass dies geschehen ist.</span><span class="sxs-lookup"><span data-stu-id="b5e05-137">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="b5e05-138">Wenn die Standortdatenbank nicht erstellt wurde, indem die ursprüngliche Migration anwenden, erhalten Sie *Fehler bei ein Datenbankvorgang beim Verarbeiten der Anforderung* Fehler.</span><span class="sxs-lookup"><span data-stu-id="b5e05-138">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="b5e05-139">Wählen Sie **Migrationen anwenden** aus, um die Datenbank zu erstellen, und aktualisieren Sie die Seite, um den Fehler fortzusetzen.</span><span class="sxs-lookup"><span data-stu-id="b5e05-139">Select **Apply Migrations** to create the database, and refresh the page to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b5e05-140">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="b5e05-140">Next steps</span></span>

* <span data-ttu-id="b5e05-141">In diesem Artikel wurde gezeigt, wie Sie mit Google authentifiziert werden können.</span><span class="sxs-lookup"><span data-stu-id="b5e05-141">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="b5e05-142">Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="b5e05-142">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="b5e05-143">Nachdem Sie die app in Azure veröffentlicht haben, setzen `ClientSecret` Sie den in der Google-API-Konsole zurück.</span><span class="sxs-lookup"><span data-stu-id="b5e05-143">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="b5e05-144">Legen Sie die `Authentication:Google:ClientId` und `Authentication:Google:ClientSecret` Anwendungseinstellungen im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="b5e05-144">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="b5e05-145">Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="b5e05-145">The configuration system is set up to read keys from environment variables.</span></span>

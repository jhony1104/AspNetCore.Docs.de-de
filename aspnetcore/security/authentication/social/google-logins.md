---
title: Google externe Anmeldung Setup in ASP.NET Core
author: rick-anderson
description: Dieses Tutorial veranschaulicht die Integration von Google-Konto der Benutzerauthentifizierung in eine vorhandene ASP.NET Core-app.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 1/11/2019
uid: security/authentication/google-logins
ms.openlocfilehash: 98857a84238124e75d695242c8d421b9a29f02e7
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396103"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="22cd4-103">Google externe Anmeldung Setup in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="22cd4-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="22cd4-104">Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="22cd4-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="22cd4-105">Im Januar 2019 Google gestartet, um [Herunterfahren](https://developers.google.com/+/api-shutdown) Google + melden Sie sich, und Entwickler müssen in einer neuen Google-Anmeldung im System verschieben, März.</span><span class="sxs-lookup"><span data-stu-id="22cd4-105">In January 2019 Google started to [shut down](https://developers.google.com/+/api-shutdown) Google+ sign in and developers must move to a new Google sign in system by March.</span></span> <span data-ttu-id="22cd4-106">Die ASP.NET Core 2.1 und 2.2 Pakete für die Google-Authentifizierung werden im Februar, um die Änderungen zu berücksichtigen, aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="22cd4-106">The ASP.NET Core 2.1 and 2.2 packages for Google Authentication will be updated in February to accommodate the changes.</span></span> <span data-ttu-id="22cd4-107">Weitere Informationen und temporäre entschärfungen für ASP.NET Core, finden Sie unter [GitHub-Problem](https://github.com/aspnet/AspNetCore/issues/6486).</span><span class="sxs-lookup"><span data-stu-id="22cd4-107">For more information and temporary mitigations for ASP.NET Core, see [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/6486).</span></span> <span data-ttu-id="22cd4-108">In diesem Tutorial wurde mit dem neuen Setup-Prozess aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="22cd4-108">This tutorial has been updated with the new setup process.</span></span>

<span data-ttu-id="22cd4-109">In diesem Tutorial erfahren Sie, wie Sie Benutzern die Anmeldung mithilfe des ASP.NET Core-2.2-Projekts erstellt, die auf ihr Google-Kontos aktivieren die [Vorgängerseite](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="22cd4-109">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 2.2 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="22cd4-110">Erstellen Sie eine Google-API-Konsole Projekt- und Client-ID</span><span class="sxs-lookup"><span data-stu-id="22cd4-110">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="22cd4-111">Navigieren Sie zu [die Integration von Google Sign-In für Ihre Web-app](https://developers.google.com/identity/sign-in/web/devconsole-project) , und wählen Sie **konfigurieren Sie ein Projekt**.</span><span class="sxs-lookup"><span data-stu-id="22cd4-111">Navigate to [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/devconsole-project) and select **CONFIGURE A PROJECT**.</span></span>
* <span data-ttu-id="22cd4-112">In der **Konfigurieren Ihres OAuth-Clients** wählen Sie im Dialogfeld **Webserver**.</span><span class="sxs-lookup"><span data-stu-id="22cd4-112">In the **Configure your OAuth client** dialog, select **Web server**.</span></span>
* <span data-ttu-id="22cd4-113">In der **autorisierte weiterleitungs-URIs** Texteingabefeld, legen Sie den umleitungs-URI.</span><span class="sxs-lookup"><span data-stu-id="22cd4-113">In the **Authorized redirect URIs** text entry box, set the redirect URI.</span></span> <span data-ttu-id="22cd4-114">Beispiel: `https://localhost:5001/signin-google`</span><span class="sxs-lookup"><span data-stu-id="22cd4-114">For example, `https://localhost:5001/signin-google`</span></span>
* <span data-ttu-id="22cd4-115">Speichern Sie die **Client-ID** und **Clientgeheimnis**.</span><span class="sxs-lookup"><span data-stu-id="22cd4-115">Save the **Client ID** and **Client Secret**.</span></span>
* <span data-ttu-id="22cd4-116">Wenn Sie den Standort bereitstellen, registrieren Sie die neue öffentliche Url aus der **Webkonsole von Google**.</span><span class="sxs-lookup"><span data-stu-id="22cd4-116">When deploying the site, register the new public url from the **Google Console**.</span></span>

## <a name="store-google-clientid-and-clientsecret"></a><span data-ttu-id="22cd4-117">Store-Google-ClientID und ClientSecret</span><span class="sxs-lookup"><span data-stu-id="22cd4-117">Store Google ClientID and ClientSecret</span></span>

<span data-ttu-id="22cd4-118">Sensible Einstellungen wie z. B. Google Store `Client ID` und `Client Secret` mit der [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="22cd4-118">Store sensitive settings such as the Google `Client ID` and `Client Secret` with the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="22cd4-119">Benennen Sie die Token für die Zwecke dieses Lernprogramms, `Authentication:Google:ClientId` und `Authentication:Google:ClientSecret`:</span><span class="sxs-lookup"><span data-stu-id="22cd4-119">For the purposes of this tutorial, name the tokens `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

```console
dotnet user-secrets set "Authentication:Google:ClientId" "X.apps.googleusercontent.com"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

<span data-ttu-id="22cd4-120">Sie können verwalten, Ihre API-Anmeldeinformationen und die Nutzung in der [-API-Konsole](https://console.developers.google.com/apis/dashboard).</span><span class="sxs-lookup"><span data-stu-id="22cd4-120">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="22cd4-121">Konfigurieren der Google-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="22cd4-121">Configure Google authentication</span></span>

<span data-ttu-id="22cd4-122">Hinzufügen den Google-Dienst `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="22cd4-122">Add the Google service to `Startup.ConfigureServices`.</span></span>

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="22cd4-123">Mit Google anmelden</span><span class="sxs-lookup"><span data-stu-id="22cd4-123">Sign in with Google</span></span>

* <span data-ttu-id="22cd4-124">Führen Sie die app, und klicken Sie auf **melden Sie sich bei**.</span><span class="sxs-lookup"><span data-stu-id="22cd4-124">Run the app and click **Log in**.</span></span> <span data-ttu-id="22cd4-125">Eine Option aus, um sich mit Google anmelden wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="22cd4-125">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="22cd4-126">Klicken Sie auf die **Google** Schaltfläche, die für die Authentifizierung an Google umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="22cd4-126">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="22cd4-127">Nach dem Eingeben Ihrer Google-Anmeldeinformationen, werden Sie zurück zur Website weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="22cd4-127">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="22cd4-128">Finden Sie unter den [GoogleOptions](/dotnet/api/microsoft.aspnetcore.builder.googleoptions) API-Referenz für Weitere Informationen zu den Konfigurationsoptionen, die von Google-Authentifizierung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="22cd4-128">See the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.builder.googleoptions) API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="22cd4-129">Dies kann verwendet werden, um verschiedene Informationen über den Benutzer anzufordern.</span><span class="sxs-lookup"><span data-stu-id="22cd4-129">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="22cd4-130">Ändern Sie den standardrückruf-URI</span><span class="sxs-lookup"><span data-stu-id="22cd4-130">Change the default callback URI</span></span>

<span data-ttu-id="22cd4-131">Der URI-Segment `/signin-google` als den standardrückruf des Google-Authentifizierungsanbieter festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="22cd4-131">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="22cd4-132">Sie können den standardrückruf-URI ändern, während der Konfiguration die Middleware für die Google-Authentifizierung über die geerbte [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) Eigenschaft der [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) Klasse.</span><span class="sxs-lookup"><span data-stu-id="22cd4-132">You can change the default callback URI while configuring the Google authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="22cd4-133">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="22cd4-133">Troubleshooting</span></span>

* <span data-ttu-id="22cd4-134">Wenn die Anmeldung funktioniert nicht, und Sie sind keine Fehler erhalten, wechseln Sie in Development-Modus, um das Problem einfacher Debuggen lässt.</span><span class="sxs-lookup"><span data-stu-id="22cd4-134">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="22cd4-135">Wenn Identität nicht, durch den Aufruf konfiguriert ist `services.AddIdentity` in `ConfigureServices`, bei dem Versuch führt zu authentifizieren *ArgumentException: Die Option 'SignInScheme' muss angegeben werden*.</span><span class="sxs-lookup"><span data-stu-id="22cd4-135">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="22cd4-136">Die Projektvorlage, die in diesem Tutorial verwendete wird sichergestellt, dass dies geschehen ist.</span><span class="sxs-lookup"><span data-stu-id="22cd4-136">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="22cd4-137">Wenn die Standortdatenbank nicht erstellt wurde, indem die ursprüngliche Migration anwenden, erhalten Sie *Fehler bei ein Datenbankvorgang beim Verarbeiten der Anforderung* Fehler.</span><span class="sxs-lookup"><span data-stu-id="22cd4-137">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="22cd4-138">Tippen Sie auf **Migrationen anwenden** der Datenbank zu erstellen und aktualisieren, um den Fehler hinaus fortgesetzt.</span><span class="sxs-lookup"><span data-stu-id="22cd4-138">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="22cd4-139">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="22cd4-139">Next steps</span></span>

* <span data-ttu-id="22cd4-140">In diesem Artikel wurde gezeigt, wie Sie mit Google authentifiziert werden können.</span><span class="sxs-lookup"><span data-stu-id="22cd4-140">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="22cd4-141">Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="22cd4-141">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="22cd4-142">Nachdem Sie die app in Azure veröffentlichen, Zurücksetzen der `ClientSecret` in der Google-API-Konsole.</span><span class="sxs-lookup"><span data-stu-id="22cd4-142">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="22cd4-143">Legen Sie die `Authentication:Google:ClientId` und `Authentication:Google:ClientSecret` Anwendungseinstellungen im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="22cd4-143">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="22cd4-144">Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="22cd4-144">The configuration system is set up to read keys from environment variables.</span></span>

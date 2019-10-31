---
title: Google externe Anmeldung Setup in ASP.NET Core
author: rick-anderson
description: Dieses Tutorial veranschaulicht die Integration von Google-Konto der Benutzerauthentifizierung in eine vorhandene ASP.NET Core-app.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/30/2019
uid: security/authentication/google-logins
ms.openlocfilehash: 83f45143eca1be43410880bfd875a3fce1d2e9c9
ms.sourcegitcommit: de0fc77487a4d342bcc30965ec5c142d10d22c03
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73143454"
---
# <a name="google-external-login-setup-in-aspnet-core"></a>Google externe Anmeldung Setup in ASP.NET Core

Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)

This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).

## <a name="create-a-google-api-console-project-and-client-id"></a>Create a Google API Console project and client ID

* Install [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google).
* Navigate to [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/devconsole-project) and select **CONFIGURE A PROJECT**.
* In the **Configure your OAuth client** dialog, select **Web server**.
* In the **Authorized redirect URIs** text entry box, set the redirect URI. Beispiel: `https://localhost:44312/signin-google`
* Save the **Client ID** and **Client Secret**.
* When deploying the site, register the new public url from the **Google Console**.

## <a name="store-google-clientid-and-clientsecret"></a>Store-Google-ClientID und ClientSecret

Store sensitive settings such as the Google `Client ID` and `Client Secret` with the [Secret Manager](xref:security/app-secrets). For the purposes of this tutorial, name the tokens `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:

```dotnetcli
dotnet user-secrets set "Authentication:Google:ClientId" "<client id>"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).

## <a name="configure-google-authentication"></a>Configure Google authentication

Add the Google service to `Startup.ConfigureServices`:

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a>Mit Google anmelden

* Run the app and click **Log in**. An option to sign in with Google appears.
* Click the **Google** button, which redirects to Google for authentication.
* After entering your Google credentials, you are redirected back to the web site.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

See the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API reference for more information on configuration options supported by Google authentication. Dies kann verwendet werden, um verschiedene Informationen über den Benutzer anzufordern.

## <a name="change-the-default-callback-uri"></a>Change the default callback URI

Der URI-Segment `/signin-google` als den standardrückruf des Google-Authentifizierungsanbieter festgelegt ist. Sie können den standardrückruf-URI ändern, während der Konfiguration die Middleware für die Google-Authentifizierung über die geerbte [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) Eigenschaft der [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) Klasse.

## <a name="troubleshooting"></a>Problembehandlung

* If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.
* If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided*. Die Projektvorlage, die in diesem Tutorial verwendete wird sichergestellt, dass dies geschehen ist.
* Wenn die Standortdatenbank nicht erstellt wurde, indem die ursprüngliche Migration anwenden, erhalten Sie *Fehler bei ein Datenbankvorgang beim Verarbeiten der Anforderung* Fehler. Select **Apply Migrations** to create the database, and refresh the page to continue past the error.

## <a name="next-steps"></a>Nächste Schritte

* In diesem Artikel wurde gezeigt, wie Sie mit Google authentifiziert werden können. Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).
* Nachdem Sie die app in Azure veröffentlicht haben, setzen Sie die `ClientSecret` in der Google-API-Konsole zurück.
* Legen Sie die `Authentication:Google:ClientId` und `Authentication:Google:ClientSecret` Anwendungseinstellungen im Azure-Portal. Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.

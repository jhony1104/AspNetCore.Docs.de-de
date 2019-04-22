---
title: Google externe Anmeldung Setup in ASP.NET Core
author: rick-anderson
description: Dieses Tutorial veranschaulicht die Integration von Google-Konto der Benutzerauthentifizierung in eine vorhandene ASP.NET Core-app.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 1/11/2019
uid: security/authentication/google-logins
ms.openlocfilehash: 44c79b3279db7946b6d89a726bd3f5acfb5f51af
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59705590"
---
# <a name="google-external-login-setup-in-aspnet-core"></a>Google externe Anmeldung Setup in ASP.NET Core

Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)

[Legacy-APIs Google + heruntergefahren wurden seit 7 März 2019](https://developers.google.com/+/api-shutdown). Google + melden Sie sich, und Entwickler müssen eine neue Google-Anmeldung im System verschieben. Die ASP.NET Core 2.1 und 2.2-Pakete für die Google-Authentifizierung werden aktualisiert, damit um die Änderungen zu berücksichtigen. Weitere Informationen und temporäre entschärfungen für ASP.NET Core, finden Sie unter [GitHub-Problem](https://github.com/aspnet/AspNetCore/issues/6486). In diesem Tutorial wurde mit dem neuen Setup-Prozess aktualisiert.

In diesem Tutorial erfahren Sie, wie Sie Benutzern die Anmeldung mithilfe des ASP.NET Core-2.2-Projekts erstellt, die auf ihr Google-Kontos aktivieren die [Vorgängerseite](xref:security/authentication/social/index).

## <a name="create-a-google-api-console-project-and-client-id"></a>Erstellen Sie eine Google-API-Konsole Projekt- und Client-ID

* Navigieren Sie zu [die Integration von Google Sign-In für Ihre Web-app](https://developers.google.com/identity/sign-in/web/devconsole-project) , und wählen Sie **konfigurieren Sie ein Projekt**.
* In der **Konfigurieren Ihres OAuth-Clients** wählen Sie im Dialogfeld **Webserver**.
* In der **autorisierte weiterleitungs-URIs** Texteingabefeld, legen Sie den umleitungs-URI. Beispiel: `https://localhost:5001/signin-google`
* Speichern Sie die **Client-ID** und **Clientgeheimnis**.
* Wenn Sie den Standort bereitstellen, registrieren Sie die neue öffentliche Url aus der **Webkonsole von Google**.

## <a name="store-google-clientid-and-clientsecret"></a>Store-Google-ClientID und ClientSecret

Sensible Einstellungen wie z. B. Google Store `Client ID` und `Client Secret` mit der [Secret Manager](xref:security/app-secrets). Benennen Sie die Token für die Zwecke dieses Lernprogramms, `Authentication:Google:ClientId` und `Authentication:Google:ClientSecret`:

```console
dotnet user-secrets set "Authentication:Google:ClientId" "X.apps.googleusercontent.com"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Sie können verwalten, Ihre API-Anmeldeinformationen und die Nutzung in der [-API-Konsole](https://console.developers.google.com/apis/dashboard).

## <a name="configure-google-authentication"></a>Konfigurieren der Google-Authentifizierung

Hinzufügen den Google-Dienst `Startup.ConfigureServices`.

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a>Mit Google anmelden

* Führen Sie die app, und klicken Sie auf **melden Sie sich bei**. Eine Option aus, um sich mit Google anmelden wird angezeigt.
* Klicken Sie auf die **Google** Schaltfläche, die für die Authentifizierung an Google umgeleitet.
* Nach dem Eingeben Ihrer Google-Anmeldeinformationen, werden Sie zurück zur Website weitergeleitet.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Finden Sie unter den [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) API-Referenz für Weitere Informationen zu den Konfigurationsoptionen, die von Google-Authentifizierung unterstützt. Dies kann verwendet werden, um verschiedene Informationen über den Benutzer anzufordern.

## <a name="change-the-default-callback-uri"></a>Ändern Sie den standardrückruf-URI

Der URI-Segment `/signin-google` als den standardrückruf des Google-Authentifizierungsanbieter festgelegt ist. Sie können den standardrückruf-URI ändern, während der Konfiguration die Middleware für die Google-Authentifizierung über die geerbte [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) Eigenschaft der [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) Klasse.

## <a name="troubleshooting"></a>Problembehandlung

* Wenn die Anmeldung funktioniert nicht, und Sie sind keine Fehler erhalten, wechseln Sie in Development-Modus, um das Problem einfacher Debuggen lässt.
* Wenn Identität nicht, durch den Aufruf konfiguriert ist `services.AddIdentity` in `ConfigureServices`, bei dem Versuch führt zu authentifizieren *ArgumentException: Die Option 'SignInScheme' muss angegeben werden*. Die Projektvorlage, die in diesem Tutorial verwendete wird sichergestellt, dass dies geschehen ist.
* Wenn die Standortdatenbank nicht erstellt wurde, indem die ursprüngliche Migration anwenden, erhalten Sie *Fehler bei ein Datenbankvorgang beim Verarbeiten der Anforderung* Fehler. Tippen Sie auf **Migrationen anwenden** der Datenbank zu erstellen und aktualisieren, um den Fehler hinaus fortgesetzt.

## <a name="next-steps"></a>Nächste Schritte

* In diesem Artikel wurde gezeigt, wie Sie mit Google authentifiziert werden können. Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).
* Nachdem Sie die app in Azure veröffentlichen, Zurücksetzen der `ClientSecret` in der Google-API-Konsole.
* Legen Sie die `Authentication:Google:ClientId` und `Authentication:Google:ClientSecret` Anwendungseinstellungen im Azure-Portal. Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.

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
# <a name="google-external-login-setup-in-aspnet-core"></a>Google externe Anmeldung Setup in ASP.NET Core

Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)

[Ältere Google +-APIs wurden seit dem 7. März 2019 heruntergefahren](https://developers.google.com/+/api-shutdown). Google +-Anmeldung und Entwickler müssen zu einem neuen Google-Anmeldesystem wechseln. Die ASP.net Core 2,1-und 2,2-Pakete für die Google-Authentifizierung wurden aktualisiert, um die Änderungen zu unterstützen. Weitere Informationen und vorübergehende entschärfungen für ASP.net Core finden Sie in [diesem GitHub-Problem](https://github.com/aspnet/AspNetCore/issues/6486). Dieses Tutorial wurde mit dem neuen Setup Vorgang aktualisiert.

In diesem Tutorial wird gezeigt, wie Sie es Benutzern mithilfe des auf der [vorherigen Seite](xref:security/authentication/social/index)erstellten Projekts ASP.net Core 2,2 ermöglichen, sich mit Ihrem Google-Konto anzumelden.

## <a name="create-a-google-api-console-project-and-client-id"></a>Erstellen Sie ein Google API-Konsolen Projekt und eine Client-ID.

* Navigieren Sie zu [integrieren der Google-Anmeldung in Ihre Web-App](https://developers.google.com/identity/sign-in/web/devconsole-project) , und wählen Sie **Projekt konfigurieren**aus.
* Wählen Sie im Dialogfeld **Konfigurieren des OAuth-Clients** den **Webserver**aus.
* Legen Sie im Textfeld **autorisierte Umleitungs-URIs** den Umleitungs-URI fest. Beispiel: `https://localhost:5001/signin-google`
* Speichern Sie die **Client-ID** und den **geheimen Client**Schlüssel.
* Wenn Sie den Standort bereitstellen, registrieren Sie die neue öffentliche URL in der **Google-Konsole**.

## <a name="store-google-clientid-and-clientsecret"></a>Store-Google-ClientID und ClientSecret

Speichern Sie sensible Einstellungen wie Google `Client ID` und `Client Secret` mit dem [Geheimnis-Manager](xref:security/app-secrets). Benennen Sie die Token `Authentication:Google:ClientId` für die Zwecke dieses Tutorials und: `Authentication:Google:ClientSecret`

```dotnetcli
dotnet user-secrets set "Authentication:Google:ClientId" "X.apps.googleusercontent.com"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Sie können Ihre API-Anmelde Informationen und die Verwendung in der [API-Konsole](https://console.developers.google.com/apis/dashboard)verwalten.

## <a name="configure-google-authentication"></a>Konfigurieren der Google-Authentifizierung

Fügen Sie den Google- `Startup.ConfigureServices`Dienst hinzu:

[!code-csharp[](~/security/authentication/social/social-code/StartupGoogle.cs?name=snippet_ConfigureServices&highlight=10-18)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a>Mit Google anmelden

* Führen Sie die APP aus, und klicken Sie auf **Anmelden**. Eine Option zum Anmelden mit Google wird angezeigt.
* Klicken Sie auf die **Google** -Schaltfläche, die zur Authentifizierung an Google umgeleitet wird.
* Nachdem Sie Ihre Google-Anmelde Informationen eingegeben haben, werden Sie an die Website zurückgeleitet.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Weitere Informationen <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> zu von der Google-Authentifizierung unterstützten Konfigurationsoptionen finden Sie in der API-Referenz. Dies kann verwendet werden, um verschiedene Informationen über den Benutzer anzufordern.

## <a name="change-the-default-callback-uri"></a>Ändern des Standard-Rückruf-URI

Der URI-Segment `/signin-google` als den standardrückruf des Google-Authentifizierungsanbieter festgelegt ist. Sie können den standardrückruf-URI ändern, während der Konfiguration die Middleware für die Google-Authentifizierung über die geerbte [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) Eigenschaft der [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) Klasse.

## <a name="troubleshooting"></a>Problembehandlung

* Wenn die Anmeldung nicht funktioniert und Sie keine Fehler erhalten, wechseln Sie in den Entwicklungsmodus, um das Problem zu vereinfachen.
* Wenn `services.AddIdentity` dieIdentität`ConfigureServices`nicht durch Aufrufen von konfiguriert wird, führt der Versuch der Authentifizierung zu einer argumumtexception: *Die Option "signinscheme" muss angegeben*werden. Die Projektvorlage, die in diesem Tutorial verwendete wird sichergestellt, dass dies geschehen ist.
* Wenn die Standortdatenbank nicht erstellt wurde, indem die ursprüngliche Migration anwenden, erhalten Sie *Fehler bei ein Datenbankvorgang beim Verarbeiten der Anforderung* Fehler. Wählen Sie **Migrationen anwenden** aus, um die Datenbank zu erstellen, und aktualisieren Sie die Seite, um den Fehler fortzusetzen.

## <a name="next-steps"></a>Nächste Schritte

* In diesem Artikel wurde gezeigt, wie Sie mit Google authentifiziert werden können. Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).
* Nachdem Sie die app in Azure veröffentlicht haben, setzen `ClientSecret` Sie den in der Google-API-Konsole zurück.
* Legen Sie die `Authentication:Google:ClientId` und `Authentication:Google:ClientSecret` Anwendungseinstellungen im Azure-Portal. Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.

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
# <a name="facebook-external-login-setup-in-aspnet-core"></a>Einrichtung externer Facebook-Anmelde Informationen in ASP.net Core

Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Tutorial mit Codebeispielen wird veranschaulicht, wie Sie es Ihren Benutzern ermöglichen können, sich mit Ihrem Facebook-Konto zu anmelden, indem Sie ein Beispiel ASP.net Core auf der [vorherigen Seite](xref:security/authentication/social/index)erstellten Beispiel Projekt 3,0. Wir beginnen damit, eine Facebook-APP-ID zu erstellen, indem wir die [offiziellen Schritte](https://developers.facebook.com)befolgen.

## <a name="create-the-app-in-facebook"></a>Erstellen der app in Facebook

* Fügen Sie das nuget-Paket [Microsoft. aspnetcore. Authentication. Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) zum Projekt hinzu.

* Navigieren Sie zur Seite [Facebook-Entwickler-App](https://developers.facebook.com/apps/) , und melden Sie sich an. Wenn Sie noch nicht über ein Facebook-Konto verfügen, verwenden Sie den Link **für Facebook registrieren** auf der Anmeldeseite, um einen zu erstellen.  Wenn Sie über ein Facebook-Konto verfügen, befolgen Sie die Anweisungen, um sich als Facebook-Entwickler zu registrieren.

* Wählen Sie im Menü **meine apps** die Option **app erstellen** aus, um eine neue APP-ID zu erstellen.

   ![Facebook für Entwickler Portal in Microsoft Edge geöffnet](index/_static/FBMyApps.png)

* Füllen Sie das Formular aus, und tippen Sie auf die Schaltfläche **App-ID erstellen** .

  ![Erstellen Sie ein neues APP-ID-Formular.](index/_static/FBNewAppId.png)

* Wählen Sie auf der neuen App-Karte **Produkt hinzufügen**aus.  Klicken Sie auf der **Facebook-Anmelde** Karte auf **Einrichten** . 

  ![Seite "Produkt Setup"](index/_static/FBProductSetup.png)

* Der **Schnellstart** -Assistent wird gestartet, und **Wählen Sie eine Plattform** als erste Seite aus. Umgehen Sie den Assistenten, indem Sie im Menü unten links auf den Link **Facebook Login** **Settings** (Facebook-Anmelde Einstellungen) klicken:

  ![Schnellstart überspringen](index/_static/FBSkipQuickStart.png)

* Die Seite "Client- **OAuth-Einstellungen** " wird angezeigt:

  ![Client-OAuth-Einstellungsseite](index/_static/FBOAuthSetup.png)

* Geben Sie Ihren Entwicklungs-URI mit */SignIn-Facebook* an das **gültige OAuth-Umleitungs-URIs** -Feld an (z. b.: `https://localhost:44320/signin-facebook`). Bei der Facebook-Authentifizierung, die später in diesem Tutorial konfiguriert wird, werden Anforderungen an */SignIn-Facebook* Route automatisch verarbeitet, um den OAuth-Fluss zu implementieren.

> [!NOTE]
> Der URI */SignIn-Facebook* wird als Standard Rückruf des Facebook-Authentifizierungs Anbieters festgelegt. Sie können den Standard-Rückruf-URI beim Konfigurieren der Facebook-Authentifizierungs Middleware über die geerbte [remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) -Eigenschaft der [facebookoptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) -Klasse ändern.

* Klicken Sie auf **Änderungen speichern**.

* Klicken Sie im linken Navigationsbereich auf **Einstellungen** > **grundlegenden** Link.

  Notieren Sie sich auf dieser Seite Ihren `App ID` und ihren `App Secret`. Sie fügen beide im nächsten Abschnitt Ihrer ASP.net Core Anwendung hinzu:

* Wenn Sie den Standort bereitstellen, müssen Sie die Seite für die **Facebook-Anmelde** Einrichtung erneut aufrufen und einen neuen öffentlichen URI registrieren.

## <a name="store-facebook-app-id-and-app-secret"></a>Speichern der Facebook-APP-ID und des App-Geheimnisses

Verknüpfen Sie sensible Einstellungen wie Facebook `App ID` und `App Secret` mit ihrer Anwendungskonfiguration mithilfe des [geheimen Managers](xref:security/app-secrets). Benennen Sie die Token für die Zwecke dieses Tutorials `Authentication:Facebook:AppId` und `Authentication:Facebook:AppSecret`.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Führen Sie die folgenden Befehle aus, um `App ID` und `App Secret` mithilfe von Secret Manager sicher zu speichern:

```dotnetcli
dotnet user-secrets set Authentication:Facebook:AppId <app-id>
dotnet user-secrets set Authentication:Facebook:AppSecret <app-secret>
```

## <a name="configure-facebook-authentication"></a>Konfigurieren der Facebook-Authentifizierung

Fügen Sie den Facebook-Dienst in der `ConfigureServices`-Methode in der Datei *Startup.cs* hinzu:

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Weitere Informationen zu den von der Facebook-Authentifizierung unterstützten Konfigurationsoptionen finden Sie in der [facebookoptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) -API-Referenz. Konfigurationsoptionen können für Folgendes verwendet werden:

* Anfordern von anderen Informationen über den Benutzer.
* Fügen Sie Abfrage Zeichen folgen Argumente hinzu, um die Anmeldung anzupassen.

## <a name="sign-in-with-facebook"></a>Mit Facebook anmelden

Führen Sie die Anwendung aus, und klicken Sie auf **Anmelden**. Sie sehen eine Option zum Anmelden mit Facebook.

![Webanwendung: Benutzer ist nicht authentifiziert.](index/_static/DoneFacebook.png)

Wenn Sie auf **Facebook**klicken, werden Sie zur Authentifizierung an Facebook umgeleitet:

![Facebook-Authentifizierungsseite](index/_static/FBLogin.png)

Facebook-Authentifizierung fordert standardmäßig Öffentliches Profil und e-Mail-Adresse an:

![Zustimmungs Bildschirm der Facebook-Authentifizierungsseite](index/_static/FBLoginDone.png)

Nachdem Sie Ihre Facebook-Anmelde Informationen eingegeben haben, werden Sie zurück zu Ihrer Website umgeleitet, auf der Sie Ihre e-Mail festlegen können.

Sie sind jetzt mit ihren Facebook-Anmelde Informationen angemeldet:

![Webanwendung: Benutzer authentifiziert](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Problembehandlung

* **Nur ASP.net Core 2. x:** Wenn die Identität nicht durch Aufrufen von `services.AddIdentity` in `ConfigureServices`konfiguriert wird, führt der Versuch, die Authentifizierung zu authentifizieren, zu *argumumtexception: die Option "signinscheme" muss angegeben werden*. Die Projektvorlage, die in diesem Tutorial verwendete wird sichergestellt, dass dies geschehen ist.
* Wenn die Standortdatenbank nicht erstellt wurde, indem die ursprüngliche Migration anwenden, erhalten Sie *Fehler bei ein Datenbankvorgang beim Verarbeiten der Anforderung* Fehler. Tippen Sie auf **Migrations anwenden** , um die Datenbank zu erstellen und zu aktualisieren, um den Fehler zu überschreiten.

## <a name="next-steps"></a>Nächste Schritte

* In diesem Artikel wurde gezeigt, wie Sie sich mit Facebook authentifizieren können. Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).

* Nachdem Sie die Website in der Azure-Web-App veröffentlicht haben, sollten Sie die `AppSecret` im Facebook-Entwickler Portal zurücksetzen.

* Legen Sie die `Authentication:Facebook:AppId` und `Authentication:Facebook:AppSecret` Anwendungseinstellungen im Azure-Portal. Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.

---
title: Facebook externe Anmeldung Setup in ASP.NET Core
author: rick-anderson
description: Tutorial mit Codebeispielen, die die Integration der Authentifizierung von Facebook-Konto Benutzern in eine vorhandene ASP.net Core-App veranschaulichen.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 12/02/2019
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: 2e4cc04c6e7ff8e5f5701cc7f9ede73dbc1b4685
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654883"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>Facebook externe Anmeldung Setup in ASP.NET Core

Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Tutorial mit Codebeispielen wird veranschaulicht, wie Sie es Ihren Benutzern ermöglichen können, sich mit Ihrem Facebook-Konto zu anmelden, indem Sie ein Beispiel ASP.net Core auf der [vorherigen Seite](xref:security/authentication/social/index)erstellten Beispiel Projekt 3,0. Wir beginnen damit, eine Facebook-APP-ID zu erstellen, indem wir die [offiziellen Schritte](https://developers.facebook.com)befolgen.

## <a name="create-the-app-in-facebook"></a>Erstellen einer app in Facebook

* Fügen Sie das nuget-Paket [Microsoft. aspnetcore. Authentication. Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) zum Projekt hinzu.

* Navigieren Sie zur Seite [Facebook-Entwickler-App](https://developers.facebook.com/apps/) , und melden Sie sich an. Wenn Sie noch nicht über ein Facebook-Konto verfügen, verwenden Sie den Link **für Facebook registrieren** auf der Anmeldeseite, um einen zu erstellen.  Wenn Sie über ein Facebook-Konto verfügen, befolgen Sie die Anweisungen, um sich als Facebook-Entwickler zu registrieren.

* Wählen Sie im Menü **meine apps** die Option **app erstellen** aus, um eine neue APP-ID zu erstellen.

   ![Facebook Entwicklerportal in Microsoft Edge geöffnete](index/_static/FBMyApps.png)

* Füllen Sie das Formular aus, und tippen Sie auf die Schaltfläche **App-ID erstellen** .

  ![Erstellen Sie ein Formular neue App-ID](index/_static/FBNewAppId.png)

* Wählen Sie auf der neuen App-Karte **Produkt hinzufügen**aus.  Klicken Sie auf der **Facebook-Anmelde** Karte auf **Einrichten** . 

  ![Setup-Produktseite](index/_static/FBProductSetup.png)

* Der **Schnellstart** -Assistent wird gestartet, und **Wählen Sie eine Plattform** als erste Seite aus. Umgehen Sie den Assistenten, indem Sie im Menü unten links auf den Link **Facebook Login** **Settings** (Facebook-Anmelde Einstellungen) klicken:

  ![Skip-Schnellstart](index/_static/FBSkipQuickStart.png)

* Die Seite "Client- **OAuth-Einstellungen** " wird angezeigt:

  ![Seite "Client-OAuth-Einstellungen"](index/_static/FBOAuthSetup.png)

* Geben Sie Ihren Entwicklungs-URI mit */SignIn-Facebook* an das **gültige OAuth-Umleitungs-URIs** -Feld an (z. b.: `https://localhost:44320/signin-facebook`). Bei der Facebook-Authentifizierung, die später in diesem Tutorial konfiguriert wird, werden Anforderungen an */SignIn-Facebook* Route automatisch verarbeitet, um den OAuth-Fluss zu implementieren.

> [!NOTE]
> Der URI */SignIn-Facebook* wird als Standard Rückruf des Facebook-Authentifizierungs Anbieters festgelegt. Sie können den Standard-Rückruf-URI beim Konfigurieren der Facebook-Authentifizierungs Middleware über die geerbte [remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) -Eigenschaft der [facebookoptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) -Klasse ändern.

* Klicken Sie auf **Änderungen speichern**.

* Klicken Sie im linken Navigationsbereich auf **Einstellungen** > **grundlegenden** Link.

  Notieren Sie sich auf dieser Seite Ihren `App ID` und ihren `App Secret`. Im nächsten Abschnitt fügen Sie beide Zertifikate in Ihrer ASP.NET Core-Anwendung:

* Wenn Sie den Standort bereitstellen, müssen Sie die Seite für die **Facebook-Anmelde** Einrichtung erneut aufrufen und einen neuen öffentlichen URI registrieren.

## <a name="store-facebook-app-id-and-app-secret"></a>Store-Facebook-App-ID und App-Geheimnis

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

Weitere Informationen zu den von der Facebook-Authentifizierung unterstützten Konfigurationsoptionen finden Sie in der [facebookoptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) -API-Referenz. Optionen für die Konfiguration können zu verwendet werden:

* Fordern Sie verschiedene Informationen über den Benutzer.
* Fügen Sie die Abfragezeichenfolge-Argumente zum Anpassen der Anmeldung hinzu.

## <a name="sign-in-with-facebook"></a>Melden Sie sich mit Facebook

Führen Sie die Anwendung aus, und klicken Sie auf **Anmelden**. Daraufhin wird eine Option aus, um sich mit Facebook anmelden.

![Web-Anwendung: Benutzer nicht authentifiziert.](index/_static/DoneFacebook.png)

Wenn Sie auf **Facebook**klicken, werden Sie zur Authentifizierung an Facebook umgeleitet:

![Seite der Facebook-Authentifizierung](index/_static/FBLogin.png)

Facebook-Authentifizierung standardmäßige öffentliche Profil und e-Mail-Adresse anfordert:

![Facebook-Authentifizierung Seite zustimmungsbildschirm](index/_static/FBLoginDone.png)

Nachdem Sie Ihre Facebook-Anmeldeinformationen eingeben, werden Sie an Ihrem Standort weitergeleitet, in dem Sie Ihre e-Mail-Adresse festlegen können.

Sie sind jetzt angemeldet, sich mit Ihren Facebook-Anmeldeinformationen:

![Web-Anwendung: Benutzerauthentifizierung](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Problembehandlung

* **Nur ASP.net Core 2. x:** Wenn die Identität nicht durch Aufrufen von `services.AddIdentity` in `ConfigureServices`konfiguriert wird, führt der Versuch, die Authentifizierung zu authentifizieren, zu *argumumtexception: die Option "signinscheme" muss angegeben werden*. Die Projektvorlage, die in diesem Tutorial verwendete wird sichergestellt, dass dies geschehen ist.
* Wenn die Standortdatenbank nicht erstellt wurde, indem die ursprüngliche Migration anwenden, erhalten Sie *Fehler bei ein Datenbankvorgang beim Verarbeiten der Anforderung* Fehler. Tippen Sie auf **Migrations anwenden** , um die Datenbank zu erstellen und zu aktualisieren, um den Fehler zu überschreiten.

## <a name="next-steps"></a>Nächste Schritte

* In diesem Artikel wurde gezeigt, wie Sie mit Facebook authentifizieren können. Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).

* Nachdem Sie die Website in der Azure-Web-App veröffentlicht haben, sollten Sie die `AppSecret` im Facebook-Entwickler Portal zurücksetzen.

* Legen Sie die `Authentication:Facebook:AppId` und `Authentication:Facebook:AppSecret` Anwendungseinstellungen im Azure-Portal. Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.

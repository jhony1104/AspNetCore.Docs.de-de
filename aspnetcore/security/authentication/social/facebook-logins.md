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
# <a name="facebook-external-login-setup-in-aspnet-core"></a>Facebook externe Login-Setup in ASP.NET Core

Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

In diesem Tutorial mit Codebeispielen wird gezeigt, wie Sie Ihren Benutzern die Anmeldung mit ihrem Facebook-Konto mithilfe eines Beispiels ASP.NET Core 3.0-Projekts ermöglichen, das auf der [vorherigen Seite](xref:security/authentication/social/index)erstellt wurde. Wir beginnen mit der Erstellung einer Facebook App ID, indem wir die [offiziellen Schritte](https://developers.facebook.com)befolgen.

## <a name="create-the-app-in-facebook"></a>Erstellen der App in Facebook

* Fügen Sie dem Projekt das [Paket Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet hinzu.

* Navigieren Sie zur [Facebook Developers App-Seite,](https://developers.facebook.com/apps/) und melden Sie sich an. Wenn du noch kein Facebook-Konto hast, nutze den Link **Registrieren für Facebook** auf der Anmeldeseite, um eines zu erstellen.  Sobald sie ein Facebook-Konto haben, folgen Sie den Anweisungen, um sich als Facebook-Entwickler zu registrieren.

* Wählen Sie im Menü **Meine Apps** **App erstellen** aus, um eine neue App-ID zu erstellen.

   ![Facebook für Entwicklerportal in Microsoft Edge geöffnet](index/_static/FBMyApps.png)

* Füllen Sie das Formular aus, und tippen Sie auf die Schaltfläche **App-ID erstellen.**

  ![Erstellen eines neuen App-ID-Formulars](index/_static/FBNewAppId.png)

* Wählen Sie auf der neuen App-Karte **Ein Produkt**hinzufügen aus.  Klicken Sie auf der **Facebook-Login-Karte** auf **Einrichten** 

  ![Produkt-Setup-Seite](index/_static/FBProductSetup.png)

* Der **Schnellstart-Assistent** wird mit "Plattform als erste Seite **auswählen"** gestartet. Umgehen Sie den Assistenten vorerst, indem Sie im Menü unten links auf den Link **FaceBook-Anmeldeeinstellungen** **Settings** klicken:

  ![Schnellstart überspringen](index/_static/FBSkipQuickStart.png)

* Sie werden mit der **Seite Client OAuth-Einstellungen** angezeigt:

  ![Seite Client-OAuth-Einstellungen](index/_static/FBOAuthSetup.png)

* Geben Sie Ihren Entwicklungs-URI mit */signin-facebook* in das Feld **Gültige OAuth-Umleitungs-URIs** (z. B.: `https://localhost:44320/signin-facebook`) ein. Die später in diesem Tutorial konfigurierte Facebook-Authentifizierung verarbeitet automatisch Anforderungen auf */signin-facebook-Route,* um den OAuth-Fluss zu implementieren.

> [!NOTE]
> Der URI */signin-facebook* wird als Standardrückruf des Facebook-Authentifizierungsanbieters festgelegt. Sie können den Standardrückruf-URI ändern, während Sie die Facebook-Authentifizierungs-Middleware über die geerbte [RemoteAuthenticationOptions.CallbackPath-Eigenschaft](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) der [FacebookOptions-Klasse](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) konfigurieren.

* Klicken Sie auf **Save Changes** (Änderungen speichern).

* Klicken Sie in der linken Navigation auf den Link **Einstellungen** > **Basic.**

  Notieren Sie sich auf `App ID` dieser `App Secret`Seite Ihre und Ihre . Sie fügen beide zu Ihrer ASP.NET Core-Anwendung im nächsten Abschnitt hinzu:

* Wenn Sie die Website bereitstellen, müssen Sie die **Facebook-Anmeldeeinrichtungsseite** erneut aufrufen und einen neuen öffentlichen URI registrieren.

## <a name="store-the-facebook-app-id-and-secret"></a>Speichern der Facebook-App-ID und des geheimen

Speichern Sie vertrauliche Einstellungen wie die Facebook-App-ID und geheime Werte mit [Secret Manager](xref:security/app-secrets). Gehen Sie für dieses Beispiel wie folgt vor:

1. Initialisieren Sie das Projekt für die geheime Speicherung gemäß den Anweisungen unter [Geheime Speicherung aktivieren](xref:security/app-secrets#enable-secret-storage).
1. Speichern Sie die vertraulichen Einstellungen im `Authentication:Facebook:AppId` lokalen `Authentication:Facebook:AppSecret`geheimen Speicher mit den geheimen Schlüsseln und:

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a>Konfigurieren der Facebook-Authentifizierung

Fügen Sie den `ConfigureServices` Facebook-Dienst in der Methode in der *Startup.cs-Datei* hinzu:

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a>Melden Sie sich mit Facebook an

* Führen Sie die App aus, und wählen Sie **Anmelden**aus. 
* Wählen Sie unter **Verwenden eines anderen Dienstes zum Anmelden aus.**, wählen Sie Facebook aus.
* Sie werden zur Authentifizierung zu **Facebook** weitergeleitet.
* Geben Sie Ihre Facebook-Anmeldeinformationen ein.
* Sie werden zurück zu Ihrer Website weitergeleitet, wo Sie Ihre E-Mail-Adresse festlegen können.

Sie sind jetzt mit Ihren Facebook-Anmeldeinformationen angemeldet:

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a>Reagieren, um die Autorisierung externer Anmeldungen aufzuheben

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>kann einen Umleitungspfad für den Benutzer-Agent bereitstellen, wenn der Benutzer die angeforderte Autorisierungsanforderung nicht genehmigt.

Der folgende Code `AccessDeniedPath` `"/AccessDeniedPathInfo"`legt fest:

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

Wir empfehlen, dass die `AccessDeniedPath` Seite die folgenden Informationen enthält:

*  Die Remoteauthentifizierung wurde abgebrochen.
* Diese App erfordert eine Authentifizierung.
* Um sich erneut anzumelden, wählen Sie den Anmeldelink aus.

### <a name="test-accessdeniedpath"></a>Testen von AccessDeniedPath

* Navigieren zu [facebook.com](https://www.facebook.com/)
* Wenn Sie angemeldet sind, müssen Sie sich abmelden.
* Führen Sie die App aus, und wählen Sie die Facebook-Anmeldung aus.
* Wählen Sie **Jetzt nicht**aus . Sie werden zur angegebenen `AccessDeniedPath` Seite umgeleitet.

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Weitere [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) Informationen zu Konfigurationsoptionen, die von der Facebook-Authentifizierung unterstützt werden, finden Sie in der FacebookOptions-API-Referenz. Konfigurationsoptionen können verwendet werden, um:

* Fordern Sie andere Informationen über den Benutzer an.
* Fügen Sie Abfragezeichenfolgenargumente hinzu, um die Anmeldeerfahrung anzupassen.

## <a name="troubleshooting"></a>Problembehandlung

* **ASP.NET Core 2.x nur:** Wenn Identity nicht durch Aufrufen `services.AddIdentity` konfiguriert `ConfigureServices`ist, führt der Versuch, sich zu authentifizieren, zu *ArgumentException: Die Option 'SignInScheme' muss bereitgestellt werden.* Die Projektvorlage, die in diesem Tutorial verwendete wird sichergestellt, dass dies geschehen ist.
* Wenn die Standortdatenbank nicht erstellt wurde, indem die ursprüngliche Migration anwenden, erhalten Sie *Fehler bei ein Datenbankvorgang beim Verarbeiten der Anforderung* Fehler. Tippen Sie auf **Migrations anwenden,** um die Datenbank zu erstellen und zu aktualisieren, um nach dem Fehler fortzufahren.

## <a name="next-steps"></a>Nächste Schritte

* Dieser Artikel zeigte, wie Sie sich bei Facebook authentifizieren können. Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).

* Nachdem Sie Ihre Website in der Azure-Web-App veröffentlicht haben, sollten Sie die `AppSecret` im Facebook-Entwicklerportal zurücksetzen.

* Legen Sie die `Authentication:Facebook:AppId` und `Authentication:Facebook:AppSecret` Anwendungseinstellungen im Azure-Portal. Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.

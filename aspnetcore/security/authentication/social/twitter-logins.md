---
title: Externe S-Anmeldung von Twitter mit ASP.NET Core
author: rick-anderson
description: In diesem Tutorial wird die Integration der Benutzerauthentifizierung des Twitter-Kontos in eine vorhandene ASP.NET Core-App veranschaulicht.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: 1f5d667e905e49ae05f5aa31bd5b69ad126f6e28
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277287"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>Externe S-Anmeldung von Twitter mit ASP.NET Core

Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)

Dieses Beispiel zeigt, wie Benutzer sich [mit ihrem Twitter-Konto](https://dev.twitter.com/web/sign-in/desktop-browser) mit einem Beispiel ASP.NET Core 3.0-Projekt anmelden können, das auf der [vorherigen Seite](xref:security/authentication/social/index)erstellt wurde.

## <a name="create-the-app-in-twitter"></a>Erstellen der App in Twitter

* Fügen Sie dem Projekt das [Paket Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet hinzu.

* Navigieren [https://apps.twitter.com/](https://apps.twitter.com/) Sie zu und melden Sie sich an. Wenn du noch kein Twitter-Konto hast, verwende den Link **[Jetzt anmelden,](https://twitter.com/signup)** um eines zu erstellen.

* Wählen **Sie App erstellen**aus. Füllen Sie den **App-Namen**, **die Anwendungsbeschreibung** und den öffentlichen **Website-URI** aus (dies kann vorübergehend sein, bis Sie den Domänennamen registrieren):

* Aktivieren Sie das Kontrollkästchen neben **Anmelden bei Twitter aktivieren**

* Microsoft.AspNetCore.Identity erfordert standardmäßig eine E-Mail-Adresse für Benutzer. Wechseln Sie zur Registerkarte **Berechtigungen,** klicken Sie auf die Schaltfläche **Bearbeiten,** und aktivieren Sie das Kontrollkästchen neben **E-Mail-Adresse von Benutzern anfordern**.

* Geben Sie Ihren `/signin-twitter` Entwicklungs-URI mit Anfügt in das `https://webapp128.azurewebsites.net/signin-twitter`Feld **Rückruf-URLs** ein (z. B.: ). Das später in diesem Beispiel konfigurierte Twitter-Authentifizierungsschema verarbeitet automatisch Anforderungen auf der `/signin-twitter` Route, um den OAuth-Fluss zu implementieren.

  > [!NOTE]
  > Das URI-Segment `/signin-twitter` wird als Standardrückruf des Twitter-Authentifizierungsanbieters festgelegt. Sie können den Standardrückruf-URI ändern, während Sie die Twitter-Authentifizierungs-Middleware über die geerbte [RemoteAuthenticationOptions.CallbackPath-Eigenschaft](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) der [TwitterOptions-Klasse](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) konfigurieren.

* Füllen Sie den Rest des Formulars aus, und wählen Sie **Erstellen**aus. Neue Anwendungsdetails werden angezeigt:

## <a name="store-the-twitter-consumer-api-key-and-secret"></a>Speichern des Twitter-Verbraucher-API-Schlüssels und des geheimen

Speichern Sie vertrauliche Einstellungen wie den Twitter-Verbraucher-API-Schlüssel und den geheimen Schlüssel mit [Secret Manager](xref:security/app-secrets). Gehen Sie für dieses Beispiel wie folgt vor:

1. Initialisieren Sie das Projekt für die geheime Speicherung gemäß den Anweisungen unter [Geheime Speicherung aktivieren](xref:security/app-secrets#enable-secret-storage).
1. Speichern Sie die vertraulichen Einstellungen im `Authentication:Twitter:ConsumerKey` lokalen `Authentication:Twitter:ConsumerSecret`geheimen Speicher mit den geheimen Schlüsseln und:

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Diese Token finden Sie auf der Registerkarte **Schlüssel und Zugriffstoken,** nachdem Sie eine neue Twitter-Anwendung erstellt haben:

## <a name="configure-twitter-authentication"></a>Konfigurieren der Twitter-Authentifizierung

Fügen Sie den `ConfigureServices` Twitter-Dienst in der Methode in *Startup.cs* Datei hinzu:

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Weitere [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) Informationen zu Konfigurationsoptionen, die von der Twitter-Authentifizierung unterstützt werden, finden Sie in der TwitterOptions-API-Referenz. Dies kann verwendet werden, um verschiedene Informationen über den Benutzer anzufordern.

## <a name="sign-in-with-twitter"></a>Melden Sie sich mit Twitter an

Führen Sie die App aus, und wählen Sie **Anmelden**aus. Es wird eine Option zur Anmeldung bei Twitter angezeigt:

Ein Klick auf **Twitter** leitet zur Authentifizierung zu Twitter um:

Nachdem Sie Ihre Twitter-Anmeldeinformationen eingegeben haben, werden Sie zurück zur Website weitergeleitet, auf der Sie Ihre E-Mail-Adresse festlegen können.

Sie sind jetzt mit Ihren Twitter-Anmeldeinformationen angemeldet:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a>Problembehandlung

* **ASP.NET Core 2.x nur:** Wenn Identity nicht durch Aufrufen `services.AddIdentity` konfiguriert `ConfigureServices`ist, führt der Versuch, sich zu authentifizieren, zu *ArgumentException: Die Option 'SignInScheme' muss bereitgestellt werden.* Die in diesem Beispiel verwendete Projektvorlage stellt sicher, dass dies geschieht.
* Wenn die Standortdatenbank nicht durch Anwenden der ersten Migration erstellt wurde, wird bei der Verarbeitung des Anforderungsfehlers *ein Datenbankvorgang fehlgeschlagen.* Tippen Sie auf **Migrations anwenden,** um die Datenbank zu erstellen und zu aktualisieren, um nach dem Fehler fortzufahren.

## <a name="next-steps"></a>Nächste Schritte

* Dieser Artikel zeigte, wie Sie sich bei Twitter authentifizieren können. Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).

* Nachdem Sie Ihre Website in der Azure-Web-App veröffentlicht haben, sollten Sie die `ConsumerSecret` im Twitter-Entwicklerportal zurücksetzen.

* Legen Sie die `Authentication:Twitter:ConsumerKey` und `Authentication:Twitter:ConsumerSecret` Anwendungseinstellungen im Azure-Portal. Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.

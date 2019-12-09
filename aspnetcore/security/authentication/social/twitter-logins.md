---
title: Externe Twitter-Anmelde Einrichtung mit ASP.net Core
author: rick-anderson
description: In diesem Tutorial wird die Integration der Authentifizierung von Twitter-Konto Benutzern in eine vorhandene ASP.net Core-App veranschaulicht.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2019
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: 5d0695160d90d0c5d31b8e35bc6c4cc984829333
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944212"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>Externe Twitter-Anmelde Einrichtung mit ASP.net Core

Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)

Dieses Beispiel zeigt, wie Sie es Benutzern mithilfe eines auf der [vorherigen Seite](xref:security/authentication/social/index)erstellten Beispiels ASP.net Core 3,0-Projekts ermöglichen, sich [mit Ihrem Twitter-Konto anzumelden](https://dev.twitter.com/web/sign-in/desktop-browser) .

## <a name="create-the-app-in-twitter"></a>Erstellen der app in Twitter

* Fügen Sie dem Projekt das nuget-Paket [Microsoft. aspnetcore. Authentication. Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) hinzu.

* Navigieren Sie zu [ https://apps.twitter.com/ ](https://apps.twitter.com/) und melden Sie sich. Wenn Sie noch nicht über ein Twitter-Konto verfügen, verwenden Sie den Link **[jetzt registrieren](https://twitter.com/signup)** , um einen zu erstellen.

* Klicken Sie auf **App erstellen**. Geben Sie den **APP-Namen**, die **Anwendungsbeschreibung** und den URI der öffentlichen **Website** an (Dies kann temporär sein, bis Sie den Domänen Namen registrieren):

* Geben Sie Ihren Entwicklungs-URI ein, wobei `/signin-twitter` an das Feld **Rückruf-URLs** angehängt ist (z. b.: `https://webapp128.azurewebsites.net/signin-twitter`). Das Twitter-Authentifizierungsschema, das weiter unten in diesem Beispiel konfiguriert wird, verarbeitet automatisch Anforderungen auf `/signin-twitter` Route, um den OAuth-Fluss zu implementieren.

  > [!NOTE]
  > Das URI-Segment `/signin-twitter` wird als Standard Rückruf des Twitter-Authentifizierungs Anbieters festgelegt. Sie können den Standard-Rückruf-URI ändern, während Sie die Twitter-Authentifizierungs Middleware über die geerbte [remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) -Eigenschaft der [twitteroptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) -Klasse konfigurieren.

* Füllen Sie das restliche Formular aus, und klicken Sie auf **Erstellen**. Es werden neue Anwendungsdetails angezeigt:

## <a name="storing-twitter-consumer-api-key-and-secret"></a>Speichern von Twitter-Consumer-API-Schlüssel und-Geheimnis

Führen Sie die folgenden Befehle aus, um `ClientId` und `ClientSecret` mithilfe von [Secret Manager](xref:security/app-secrets)sicher zu speichern:

```dotnetcli
dotnet user-secrets set Authentication:Twitter:ConsumerAPIKey <Key>
dotnet user-secrets set Authentication:Twitter:ConsumerSecret <Secret>
```

Verknüpfen Sie sensible Einstellungen wie Twitter `Consumer Key` und `Consumer Secret` mit ihrer Anwendungskonfiguration mithilfe des [geheimen Managers](xref:security/app-secrets). Benennen Sie die Token für die Zwecke dieses Beispiels `Authentication:Twitter:ConsumerKey` und `Authentication:Twitter:ConsumerSecret`.

Diese Token finden Sie auf der Registerkarte **Schlüssel und Zugriffs Token** , nachdem Sie eine neue Twitter-Anwendung erstellt haben:

## <a name="configure-twitter-authentication"></a>Konfigurieren der Twitter-Authentifizierung

Fügen Sie den Twitter-Dienst in der `ConfigureServices`-Methode in der Datei *Startup.cs* hinzu:

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Weitere Informationen zu von der Twitter-Authentifizierung unterstützten Konfigurationsoptionen finden Sie in der [twitteroptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) -API-Referenz. Dies kann verwendet werden, um verschiedene Informationen über den Benutzer anzufordern.

## <a name="sign-in-with-twitter"></a>Mit Twitter anmelden

Führen Sie die APP aus, und wählen Sie **Anmelden aus**. Eine Option zum Anmelden bei Twitter wird angezeigt:

Beim Klicken auf **Twitter** wird die Authentifizierung an Twitter umgeleitet:

Nachdem Sie Ihre Twitter-Anmelde Informationen eingegeben haben, werden Sie zurück an die Website umgeleitet, auf der Sie Ihre e-Mail festlegen können.

Sie sind jetzt mit Ihren Twitter-Anmelde Informationen angemeldet:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Problembehandlung

* **ASP.NET Core 2.x nur:** Wenn Identität ist nicht konfiguriert, durch den Aufruf `services.AddIdentity` in `ConfigureServices`, Authentifizierungsversuch führt zu *ArgumentException: die Option 'SignInScheme' muss angegeben werden*. Die in diesem Beispiel verwendete Projektvorlage stellt sicher, dass dies abgeschlossen ist.
* Wenn die Standortdatenbank nicht erstellt wurde, indem die ursprüngliche Migration anwenden, erhalten Sie *Fehler bei ein Datenbankvorgang beim Verarbeiten der Anforderung* Fehler. Tippen Sie auf **Migrationen anwenden** der Datenbank zu erstellen und aktualisieren, um den Fehler hinaus fortgesetzt.

## <a name="next-steps"></a>Nächste Schritte

* In diesem Artikel wurde gezeigt, wie Sie sich mit Twitter authentifizieren können. Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).

* Nachdem Sie die Website in der Azure-Web-App veröffentlicht haben, sollten Sie die `ConsumerSecret` im Twitter-Entwickler Portal zurücksetzen.

* Legen Sie die `Authentication:Twitter:ConsumerKey` und `Authentication:Twitter:ConsumerSecret` Anwendungseinstellungen im Azure-Portal. Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.

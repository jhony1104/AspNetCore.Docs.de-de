---
title: Twitter-externen Anmeldung Setup mit ASP.NET Core
author: rick-anderson
description: Dieses Tutorial veranschaulicht die Integration von Twitter-Konto der Benutzerauthentifizierung in eine vorhandene ASP.NET Core-app.
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: security/authentication/twitter-logins
ms.openlocfilehash: d816ed27898639b0af6896a51ac035d5526c5d29
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814073"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>Twitter-externen Anmeldung Setup mit ASP.NET Core

Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Beispiel wird gezeigt, wie Sie es Benutzern ermöglichen [melden Sie sich mit ihrem Twitter-Konto](https://dev.twitter.com/web/sign-in/desktop-browser) erstellt mit einem ASP.NET Core-2.2-Beispielprojekt die [Vorgängerseite](xref:security/authentication/social/index).

## <a name="create-the-app-in-twitter"></a>Erstellen einer app in Twitter

* Navigieren Sie zu [ https://apps.twitter.com/ ](https://apps.twitter.com/) und melden Sie sich. Wenn Sie noch kein Twitter-Konto besitzen, verwenden Sie die **[registrieren Sie sich jetzt](https://twitter.com/signup)** Link zu erstellen.

* Tippen Sie auf **Create New App** , und füllen Sie die Anwendung **Namen**, **Beschreibung** und öffentliche **Website** URI (Dies kann temporär sein bis Registrieren Sie den Domänennamen):

* Geben Sie Ihre Entwicklung URI mit `/signin-twitter` angefügt, die in der **gültige OAuth-Umleitungs-URIs** Feld (z. B.: `https://webapp128.azurewebsites.net/signin-twitter`). Das Twitter-Authentifizierungsschema, das weiter unten in diesem Beispiel konfiguriert übernimmt automatisch die Anforderungen an `/signin-twitter` Route zum Implementieren von OAuth-Fluss.

  > [!NOTE]
  > Der URI-Segment `/signin-twitter` ist als der standardrückruf des Authentifizierungsanbieters Twitter festgelegt. Sie können den standardrückruf-URI ändern, während der Konfiguration der Twitter-Authentifizierung-Middleware über die geerbte [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) Eigenschaft der [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) -Klasse.

* Geben Sie im Rest des Formulars, und tippen Sie auf **Erstellen Ihrer Twitter-Anwendung**. Die Anwendungsdetails der neuen werden angezeigt:

## <a name="storing-twitter-consumer-api-key-and-secret"></a>Das Speichern von Twitter-Consumer-API-Schlüssel und geheimen Schlüssel

Führen Sie die folgenden Befehle zum sicheren Speichern von `ClientId` und `ClientSecret` mit [Secret Manager](xref:security/app-secrets):

```console
dotnet user-secrets set Authentication:Twitter:ConsumerAPIKey <Key>
dotnet user-secrets set Authentication:Twitter:ConsumerAPISecret <Secret>
```

Verknüpfen Sie sensible Einstellungen wie Twitter `Consumer Key` und `Consumer Secret` zu Ihrer Anwendungskonfigurationsdatei mithilfe der [Secret Manager](xref:security/app-secrets). Benennen Sie im Rahmen dieses Beispiels werden die Token `Authentication:Twitter:ConsumerKey` und `Authentication:Twitter:ConsumerSecret`.

Diese Token finden Sie auf die **Keys and Access Tokens** Registerkarte nach dem Erstellen einer neuen Twitter-Anwendung:

## <a name="configure-twitter-authentication"></a>Konfigurieren der Twitter-Authentifizierung

Fügen Sie den Twitter-Dienst in der `ConfigureServices` -Methode in der *"Startup.cs"* Datei:

[!code-csharp[](~/security/authentication/social/social-code/StartupTwitter.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Finden Sie unter den [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API-Referenz für Weitere Informationen zu den Konfigurationsoptionen, die von Twitter-Authentifizierung unterstützt. Dies kann verwendet werden, um verschiedene Informationen über den Benutzer anzufordern.

## <a name="sign-in-with-twitter"></a>Melden Sie sich mit Twitter

Führen Sie die app, und wählen Sie **melden Sie sich bei**. Eine Option aus, um sich mit Twitter anmelden wird angezeigt:

Durch Klicken auf **Twitter** an Twitter umgeleitet wird, für die Authentifizierung:

Geben Sie Ihre Twitter-Anmeldeinformationen, werden Sie zurück zur Website weitergeleitet, in dem Sie Ihre e-Mail-Adresse festlegen können.

Sie sind jetzt angemeldet, sich mit Ihren Twitter-Anmeldeinformationen:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Problembehandlung

* **ASP.NET Core 2.x nur:** Wenn Identität nicht, durch den Aufruf konfiguriert ist `services.AddIdentity` in `ConfigureServices`, Authentifizierungsversuch führt zu *ArgumentException: Die Option 'SignInScheme' muss angegeben werden*. Die Projektvorlage, die in diesem Beispiel verwendete wird sichergestellt, dass dies geschehen ist.
* Wenn die Standortdatenbank nicht erstellt wurde, indem die ursprüngliche Migration anwenden, erhalten Sie *Fehler bei ein Datenbankvorgang beim Verarbeiten der Anforderung* Fehler. Tippen Sie auf **Migrationen anwenden** der Datenbank zu erstellen und aktualisieren, um den Fehler hinaus fortgesetzt.

## <a name="next-steps"></a>Nächste Schritte

* In diesem Artikel wurde gezeigt, wie Sie Twitter authentifizieren können. Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).

* Nachdem Sie Ihre Website in Azure-Web-app veröffentlichen, sollten Sie Zurücksetzen der `ConsumerSecret` im Twitter-Entwicklerportal.

* Legen Sie die `Authentication:Twitter:ConsumerKey` und `Authentication:Twitter:ConsumerSecret` Anwendungseinstellungen im Azure-Portal. Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.
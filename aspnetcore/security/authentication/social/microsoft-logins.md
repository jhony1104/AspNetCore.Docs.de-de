---
title: Setup von Microsoft Account externen Anmeldung mit ASP.NET Core
author: rick-anderson
description: Dieses Beispiel veranschaulicht die Integration von Microsoft-Konto-Benutzerauthentifizierung in eine vorhandene ASP.NET Core-app.
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 2c690e5bd8465806d42091616917cfdd747ef8f0
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815574"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>Setup von Microsoft Account externen Anmeldung mit ASP.NET Core

Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)

Dieses Beispiel zeigt, wie Sie Benutzern die Anmeldung mit ihrem Microsoft-Kontos mithilfe des ASP.NET Core-2.2-Projekts erstellt ermöglichen, sich die [Vorgängerseite](xref:security/authentication/social/index).

## <a name="create-the-app-in-microsoft-developer-portal"></a>Erstellen einer app in Microsoft-Entwicklerportal

* Navigieren Sie zu der [Azure-Portal – App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908) Seite, und erstellen, oder melden Sie sich bei einem Microsoft-Konto:

Wenn Sie nicht über ein Microsoft-Konto verfügen, wählen Sie **erstellen Sie eine**. Nach der Anmeldung werden Sie umgeleitet die **App-Registrierungen** Seite:

* Wählen Sie **neue Registrierung**
* Geben Sie einen **Namen**.
* Wählen Sie eine Option für **unterstützt Kontotypen**.  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts -->
* Klicken Sie unter **Umleitungs-URI**, geben Sie Ihre Entwicklung-URL mit `/signin-microsoft` angefügt. Beispielsweise `https://localhost:44389/signin-microsoft`. Das Microsoft-Authentifizierungsschema, das weiter unten in diesem Beispiel konfiguriert übernimmt automatisch die Anforderungen an `/signin-microsoft` Route zum Implementieren von OAuth-Fluss.
* Wählen Sie **registrieren**

### <a name="create-client-secret"></a>Erstellen Sie den geheimen Clientschlüssel

* Wählen Sie im linken Bereich **Zertifikate und Geheimnisse**.
* Klicken Sie unter **geheime Clientschlüssel**Option **neuer geheimer Clientschlüssel**

  * Fügen Sie eine Beschreibung für den geheimen Clientschlüssel hinzu.
  * Wählen Sie die **hinzufügen** Schaltfläche.

* Klicken Sie unter **geheime Clientschlüssel**, kopieren Sie den Wert der geheime Clientschlüssel.

> [!NOTE]
> Der URI-Segment `/signin-microsoft` ist als der standardrückruf des Authentifizierungsanbieters Microsoft festgelegt. Sie können den standardrückruf-URI ändern, während der Konfiguration der Microsoft-Authentifizierung-Middleware über die geerbte [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) Eigenschaft der [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) Klasse.

## <a name="store-the-microsoft-client-id-and-client-secret"></a>Der geheime Clientschlüssel-ID und den Microsoft Store

Führen Sie die folgenden Befehle zum sicheren Speichern von `ClientId` und `ClientSecret` mit [Secret Manager](xref:security/app-secrets):

```console
dotnet user-secrets set Authentication:Microsoft:ClientId <Client-Id>
dotnet user-secrets set Authentication:Microsoft:ClientSecret <Client-Secret>
```

Verknüpfen Sie sensible Einstellungen wie Microsoft `ClientId` und `ClientSecret` zu Ihrer Anwendungskonfigurationsdatei mithilfe der [Secret Manager](xref:security/app-secrets). Benennen Sie im Rahmen dieses Beispiels werden die Token `Authentication:Microsoft:ClientId` und `Authentication:Microsoft:ClientSecret`.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Konfigurieren von Microsoft-Kontoauthentifizierung

Fügen Sie den Dienst Microsoft Account, `Startup.ConfigureServices`:

[!code-csharp[](~/security/authentication/social/social-code/StartupMS.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Finden Sie unter den [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API-Referenz für Weitere Informationen zu den Konfigurationsoptionen, die von der Microsoft-Account-Authentifizierung unterstützt. Dies kann verwendet werden, um verschiedene Informationen über den Benutzer anzufordern.

## <a name="sign-in-with-microsoft-account"></a>Melden Sie sich mit Microsoft-Konto

Führen Sie die, und klicken Sie auf **melden Sie sich bei**. Eine Option aus, um sich mit Microsoft anmelden wird angezeigt. Wenn Sie auf Microsoft klicken, werden Sie für die Authentifizierung an Microsoft weitergeleitet. Nachdem Sie sich mit Ihrem Microsoft Account (sofern nicht bereits angemeldet) werden Sie aufgefordert, zu der app den Zugriff auf Ihre Infos ermöglichen:

Tippen Sie auf **Ja** und Sie gelangen zurück auf der Website, in dem Sie Ihre e-Mail-Adresse festlegen können.

Sie sind jetzt angemeldet, sich mit Ihren Microsoft-Anmeldeinformationen:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Problembehandlung

* Wenn der Microsoft-Account-Anbieter auf eine Fehler-Anmeldeseite umleitet, beachten Sie die Fehler Titel und Beschreibung Abfragezeichenfolgen-Parameter direkt nach der `#` (Hashtag) im Uri.

  Obwohl die Fehlermeldung ein Problem mit der Microsoft-Authentifizierung angeben anscheinend, ist die häufigste Ursache Ihrer Anwendungs-Uri, die nicht mit einer der der **Umleitungs-URIs** für die **Web** Plattform .
* Wenn Identität nicht, durch den Aufruf konfiguriert ist `services.AddIdentity` in `ConfigureServices`, Authentifizierungsversuch führt zu *ArgumentException: Die Option 'SignInScheme' muss angegeben werden*. Die Projektvorlage, die in diesem Beispiel verwendete wird sichergestellt, dass dies geschehen ist.
* Wenn die Standortdatenbank nicht erstellt wurde, indem die ursprüngliche Migration anwenden, erhalten Sie *Fehler bei ein Datenbankvorgang beim Verarbeiten der Anforderung* Fehler. Tippen Sie auf **Migrationen anwenden** der Datenbank zu erstellen und aktualisieren, um den Fehler hinaus fortgesetzt.

## <a name="next-steps"></a>Nächste Schritte

* In diesem Artikel wurde gezeigt, wie Sie mit Microsoft authentifizieren können. Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).

* Nachdem Sie Ihre Website in Azure-Web-app veröffentlichen, erstellen Sie einen neuen Client geheimer Schlüssel im Microsoft Developer Portal.

* Legen Sie die `Authentication:Microsoft:ClientId` und `Authentication:Microsoft:ClientSecret` Anwendungseinstellungen im Azure-Portal. Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.
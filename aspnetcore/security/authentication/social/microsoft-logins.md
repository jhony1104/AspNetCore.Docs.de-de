---
title: Einrichtung externer Anmelde Informationen für Microsoft-Konto mit ASP.net Core
author: rick-anderson
description: In diesem Beispiel wird die Integration von Microsoft-Konto Benutzerauthentifizierung in eine vorhandene ASP.net Core-App veranschaulicht.
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 91ace293fd16cd180b3d5c183c637af6db1d08c3
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082345"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>Einrichtung externer Anmelde Informationen für Microsoft-Konto mit ASP.net Core

Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Beispiel wird gezeigt, wie Sie es Benutzern mithilfe des auf der [vorherigen Seite](xref:security/authentication/social/index)erstellten ASP.net Core 2,2-Projekts ermöglichen, sich mit Ihrem Microsoft-Konto anzumelden.

## <a name="create-the-app-in-microsoft-developer-portal"></a>Erstellen der APP im Microsoft-Entwickler Portal

* Navigieren Sie zur Seite " [Azure-Portal-App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908) ", und erstellen Sie eine Microsoft-Konto, oder melden Sie sich an:

Wenn Sie keine Microsoft-Konto haben, wählen Sie **Erstellen**aus. Nachdem Sie sich angemeldet haben, werden Sie auf die Seite **App-Registrierungen** umgeleitet:

* **Neue Registrierung** auswählen
* Geben Sie einen **Namen**ein.
* Wählen Sie eine Option für **unterstützte Konto Typen**aus.  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts -->
* Geben Sie unter **Umleitungs-URI**Ihre Entwicklungs `/signin-microsoft` -URL mit angefügt ein. Beispielsweise `https://localhost:44389/signin-microsoft`. Das Microsoft-Authentifizierungsschema, das weiter unten in diesem Beispiel konfiguriert wird `/signin-microsoft` , verarbeitet Anforderungen bei der Route automatisch, um den OAuth-Fluss zu implementieren.
* **Register** auswählen

### <a name="create-client-secret"></a>Geheimen Client Schlüssel erstellen

* Klicken Sie im linken Bereich auf **Zertifikate & Geheimnisse**.
* Wählen Sie unter geheime **Client**Schlüssel die Option **neuer geheimer Client** Schlüssel

  * Fügen Sie eine Beschreibung für den geheimen Client Schlüssel hinzu.
  * Wählen Sie die Schaltfläche **Hinzufügen** .

* Kopieren Sie unter geheime **Client**Schlüssel den Wert des geheimen Client Schlüssels.

> [!NOTE]
> Das URI- `/signin-microsoft` Segment wird als Standard Rückruf des Microsoft-Authentifizierungs Anbieters festgelegt. Sie können den Standard-Rückruf-URI beim Konfigurieren der Microsoft-Authentifizierungs Middleware über die geerbte [remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) -Eigenschaft der [microsoftaccountoptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) -Klasse ändern.

## <a name="store-the-microsoft-client-id-and-client-secret"></a>Speichern der Microsoft-Client-ID und des geheimen Client Schlüssels

Führen Sie die folgenden Befehle zum sicheren `ClientId` speichern `ClientSecret` und Verwenden von [Secret Manager](xref:security/app-secrets)aus:

```dotnetcli
dotnet user-secrets set Authentication:Microsoft:ClientId <Client-Id>
dotnet user-secrets set Authentication:Microsoft:ClientSecret <Client-Secret>
```

Verknüpfen Sie sensible Einstellungen wie `ClientId` Microsoft `ClientSecret` und die Anwendungskonfiguration mithilfe des [geheimen Managers](xref:security/app-secrets). Benennen Sie für dieses Beispiel die Token `Authentication:Microsoft:ClientId` und. `Authentication:Microsoft:ClientSecret`

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Konfigurieren der Microsoft-Konto Authentifizierung

Fügen Sie den Microsoft-Konto `Startup.ConfigureServices`Dienst hinzu:

[!code-csharp[](~/security/authentication/social/social-code/StartupMS.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Weitere Informationen zu Konfigurationsoptionen, die von der Microsoft-Konto Authentifizierung unterstützt werden, finden Sie in der [microsoftaccountoptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) -API Dies kann verwendet werden, um verschiedene Informationen über den Benutzer anzufordern.

## <a name="sign-in-with-microsoft-account"></a>Bei Microsoft anmelden Konto

Führen Sie aus, und klicken Sie auf **Anmelden**. Eine Option zum Anmelden bei Microsoft wird angezeigt. Wenn Sie auf Microsoft klicken, werden Sie zur Authentifizierung an Microsoft umgeleitet. Nachdem Sie sich mit Ihrem Microsoft-Konto angemeldet haben (wenn es nicht bereits angemeldet ist), werden Sie aufgefordert, der APP den Zugriff auf Ihre Informationen zu gestatten:

Tippen Sie auf **Ja** , und Sie werden zurück an die Website umgeleitet, auf der Sie Ihre e-Mail festlegen können.

Sie sind jetzt mit Ihren Microsoft-Anmelde Informationen angemeldet:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Problembehandlung

* Wenn der Microsoft-Konto Anbieter Sie an eine Anmeldefehler Seite weiterleitet, beachten Sie die Parameter für die Fehler Titel und die Beschreibungs `#` Abfrage Zeichenfolge direkt nach dem (hashtag) im URI.

  Obwohl in der Fehlermeldung ein Problem mit der Microsoft-Authentifizierung angezeigt wird, ist die häufigste Ursache, dass ihr Anwendungs-URI mit keinem der für die **Webplattform** angegebenen **Umleitungs-URIs** übereinstimmt.
* Wenn die Identität nicht durch Aufrufen `services.AddIdentity` `ConfigureServices`von konfiguriert wird, führt der Versuch der *Authentifizierung zu argumumtexception: Die Option "signinscheme" muss angegeben*werden. Die in diesem Beispiel verwendete Projektvorlage stellt sicher, dass dies abgeschlossen ist.
* Wenn die Standortdatenbank nicht erstellt wurde, indem die ursprüngliche Migration anwenden, erhalten Sie *Fehler bei ein Datenbankvorgang beim Verarbeiten der Anforderung* Fehler. Tippen Sie auf **Migrationen anwenden** der Datenbank zu erstellen und aktualisieren, um den Fehler hinaus fortgesetzt.

## <a name="next-steps"></a>Nächste Schritte

* In diesem Artikel wurde gezeigt, wie Sie sich bei Microsoft authentifizieren können. Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).

* Nachdem Sie die Website in der Azure-Web-App veröffentlicht haben, erstellen Sie im Microsoft-Entwickler Portal ein neues Client Geheimnis.

* Legen Sie die `Authentication:Microsoft:ClientId` und `Authentication:Microsoft:ClientSecret` Anwendungseinstellungen im Azure-Portal. Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.
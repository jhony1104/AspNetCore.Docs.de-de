---
title: Einrichtung externer Anmelde Informationen für Microsoft-Konto mit ASP.net Core
author: rick-anderson
description: In diesem Beispiel wird die Integration von Microsoft-Konto Benutzerauthentifizierung in eine vorhandene ASP.net Core-App veranschaulicht.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: bd75efb1d7ce08538d1a67be74d2f40f3964614f
ms.sourcegitcommit: 9b6e7f421c243963d5e419bdcfc5c4bde71499aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/21/2020
ms.locfileid: "79989757"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>Einrichtung externer Anmelde Informationen für Microsoft-Konto mit ASP.net Core

Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Beispiel wird gezeigt, wie Sie es Benutzern mithilfe des auf der [vorherigen Seite](xref:security/authentication/social/index)erstellten ASP.net Core 3,0-Projekts ermöglichen, sich mit Ihrem Microsoft-Konto anzumelden.

## <a name="create-the-app-in-microsoft-developer-portal"></a>Erstellen der APP im Microsoft-Entwickler Portal

* Fügen Sie dem Projekt das nuget-Paket [Microsoft. aspnetcore. Authentication. microsoftaccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) hinzu.
* Navigieren Sie zur Seite " [Azure-Portal-App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908) ", und erstellen Sie eine Microsoft-Konto, oder melden Sie sich an:

Wenn Sie keine Microsoft-Konto haben, wählen Sie **Erstellen**aus. Nachdem Sie sich angemeldet haben, werden Sie auf die Seite **App-Registrierungen** umgeleitet:

* Wählen Sie **Neue Registrierung** aus.
* Geben Sie einen **Namen** ein.
* Wählen Sie eine Option für **unterstützte Konto Typen**aus.  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts -->
* Geben Sie unter **Umleitungs-URI**Ihre Entwicklungs-URL mit angefügtem `/signin-microsoft` ein. Beispielsweise `https://localhost:5001/signin-microsoft`. Das Microsoft-Authentifizierungsschema, das weiter unten in diesem Beispiel konfiguriert wird, verarbeitet automatisch Anforderungen auf `/signin-microsoft` Route, um den OAuth-Fluss zu implementieren.
* Wählen Sie **Registrieren** aus.

### <a name="create-client-secret"></a>Erstellen eines geheimen Clientschlüssels

* Wählen Sie im linken Bereich **Zertifikate und Geheimnisse** aus.
* Wählen Sie unter geheime **Client**Schlüssel die Option **neuer geheimer Client** Schlüssel

  * Fügen Sie eine Beschreibung für den geheimen Client Schlüssel hinzu.
  * Wählen Sie die Schaltfläche **Hinzufügen** aus.

* Kopieren Sie unter geheime **Client**Schlüssel den Wert des geheimen Client Schlüssels.

> [!NOTE]
> Das URI-Segment `/signin-microsoft` wird als Standard Rückruf des Microsoft-Authentifizierungs Anbieters festgelegt. Sie können den Standard-Rückruf-URI beim Konfigurieren der Microsoft-Authentifizierungs Middleware über die geerbte [remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) -Eigenschaft der [microsoftaccountoptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) -Klasse ändern.

## <a name="store-the-microsoft-client-id-and-secret"></a>Speichern der Microsoft-Client-ID und des geheimen Schlüssels

Speichern Sie sensible Einstellungen wie die Microsoft-Client-ID und die geheimen Werte mit [Secret Manager](xref:security/app-secrets). Führen Sie für dieses Beispiel die folgenden Schritte aus:

1. Initialisieren Sie das Projekt für die geheime Speicherung gemäß den Anweisungen unter [Aktivieren der geheimen Speicherung](xref:security/app-secrets#enable-secret-storage).
1. Speichern Sie die sensiblen Einstellungen im lokalen Geheimnis Speicher mit den geheimen Schlüsseln `Authentication:Microsoft:ClientId` und `Authentication:Microsoft:ClientSecret`:

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Konfigurieren der Microsoft-Konto Authentifizierung

Fügen Sie dem `Startup.ConfigureServices`den Microsoft-Konto Dienst hinzu:

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Weitere Informationen zu den Konfigurationsoptionen, die von der Microsoft-Konto Authentifizierung unterstützt werden, finden Sie in der [microsoftaccountoptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) -API Dies kann verwendet werden, um verschiedene Informationen über den Benutzer anzufordern.

## <a name="sign-in-with-microsoft-account"></a>Bei Microsoft anmelden Konto

Führen Sie die APP aus, und klicken Sie auf **Anmelden**. Eine Option zum Anmelden bei Microsoft wird angezeigt. Wenn Sie auf Microsoft klicken, werden Sie zur Authentifizierung an Microsoft umgeleitet. Nachdem Sie sich mit Ihrem Microsoft-Konto angemeldet haben, werden Sie aufgefordert, der APP den Zugriff auf Ihre Informationen zu gestatten:

Tippen Sie auf **Ja** , und Sie werden zurück an die Website umgeleitet, auf der Sie Ihre e-Mail festlegen können.

Sie sind jetzt mit Ihren Microsoft-Anmelde Informationen angemeldet:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Problembehandlung

* Wenn der Microsoft-Konto Anbieter Sie an eine Anmeldefehler Seite weiterleitet, notieren Sie sich die Parameter für die Fehler Titel und die Beschreibungs Abfrage Zeichenfolge direkt nach dem `#` (hashtag) im URI.

  Obwohl in der Fehlermeldung ein Problem mit der Microsoft-Authentifizierung angezeigt wird, ist die häufigste Ursache, dass ihr Anwendungs-URI mit keinem der für die **Webplattform** angegebenen **Umleitungs-URIs** übereinstimmt.
* Wenn die Identität nicht durch Aufrufen von `services.AddIdentity` in `ConfigureServices`konfiguriert wird, führt der Versuch, die Authentifizierung zu authentifizieren, zu *argumumtexception: die Option "signinscheme" muss angegeben werden*. Die in diesem Beispiel verwendete Projektvorlage stellt sicher, dass dies abgeschlossen ist.
* Wenn die Standortdatenbank nicht durch Anwenden der anfänglichen Migration erstellt wurde, wird *bei der Verarbeitung des Anforderungs Fehlers ein Daten Bank Vorgang* nicht ausgeführt. Tippen Sie auf **Migrations anwenden** , um die Datenbank zu erstellen und zu aktualisieren, um den Fehler zu überschreiten.

## <a name="next-steps"></a>Nächste Schritte

* In diesem Artikel wurde gezeigt, wie Sie sich bei Microsoft authentifizieren können. Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).

* Nachdem Sie die Website in der Azure-Web-App veröffentlicht haben, erstellen Sie im Microsoft-Entwickler Portal ein neues Client Geheimnis.

* Legen Sie die `Authentication:Microsoft:ClientId` und `Authentication:Microsoft:ClientSecret` Anwendungseinstellungen im Azure-Portal. Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.

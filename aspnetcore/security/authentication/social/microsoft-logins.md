---
title: Externe S-Anmeldung für Microsoft-Konten mit ASP.NET Core
author: rick-anderson
description: In diesem Beispiel wird die Integration der Benutzerauthentifizierung von Microsoft-Konten in eine vorhandene ASP.NET Core-App veranschaulicht.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 12c86456dad86731b86487a3a4dd725f36677002
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384049"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>Externe S-Anmeldung für Microsoft-Konten mit ASP.NET Core

Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Beispiel wird gezeigt, wie Benutzer sich mit ihrem Geschäfts-, Schul- oder persönlichen Microsoft-Konto mit dem auf der [vorherigen Seite](xref:security/authentication/social/index)erstellten ASP.NET Core 3.0-Projekt anmelden können.

## <a name="create-the-app-in-microsoft-developer-portal"></a>Erstellen der App im Microsoft Developer Portal

* Fügen Sie dem Projekt das [Paket Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet hinzu.
* Navigieren Sie zum [Azure-Portal - App-Registrierungsseite,](https://go.microsoft.com/fwlink/?linkid=2083908) und erstellen oder melden Sie sich bei einem Microsoft-Konto an:

Wenn Sie kein Microsoft-Konto haben, wählen Sie **Erstellen eines**aus. Nach der Anmeldung werden Sie zur **App-Registrierungsseite** weitergeleitet:

* Neue **Registrierung auswählen**
* Geben Sie einen **Namen**ein.
* Wählen Sie eine Option für **Unterstützte Kontotypen**aus.  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* Geben Sie unter **Redirect**URI `/signin-microsoft` Ihre Entwicklungs-URL mit angehängten ein. Beispiel: `https://localhost:5001/signin-microsoft`. Das später in diesem Beispiel konfigurierte Microsoft-Authentifizierungsschema verarbeitet automatisch Anforderungen auf `/signin-microsoft` der Route, um den OAuth-Fluss zu implementieren.
* Wählen Sie **Registrieren** aus.

### <a name="create-client-secret"></a>Erstellen eines geheimen Clientschlüssels

* Wählen Sie im linken Bereich **Zertifikate und Geheimnisse** aus.
* Wählen Sie unter **Clientgeheimnisse**die Option **Neuer geheimer Clientschlüssel** aus

  * Fügen Sie eine Beschreibung für den geheimen Clientschlüssel hinzu.
  * Wählen Sie die Schaltfläche **Hinzufügen** aus.

* Kopieren Sie unter **Clientgeheimnisse**den Wert des geheimen Clientschlüssels.

Das URI-Segment `/signin-microsoft` wird als Standardrückruf des Microsoft-Authentifizierungsanbieters festgelegt. Sie können den Standardrückruf-URI ändern, während Sie die Microsoft-Authentifizierungs-Middleware über die geerbte [RemoteAuthenticationOptions.CallbackPath-Eigenschaft](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) der [MicrosoftAccountOptions-Klasse](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) konfigurieren.

## <a name="store-the-microsoft-client-id-and-secret"></a>Speichern der Microsoft-Client-ID und des geheimen Schlüssels

Speichern Sie vertrauliche Einstellungen wie die Microsoft-Client-ID und geheime Werte mit [Secret Manager](xref:security/app-secrets). Gehen Sie für dieses Beispiel wie folgt vor:

1. Initialisieren Sie das Projekt für die geheime Speicherung gemäß den Anweisungen unter [Geheime Speicherung aktivieren](xref:security/app-secrets#enable-secret-storage).
1. Speichern Sie die vertraulichen Einstellungen im `Authentication:Microsoft:ClientId` lokalen `Authentication:Microsoft:ClientSecret`geheimen Speicher mit den geheimen Schlüsseln und:

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Konfigurieren der Microsoft-Kontoauthentifizierung

Fügen Sie den Microsoft-Kontodienst hinzu: `Startup.ConfigureServices`

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

Weitere Informationen zu Konfigurationsoptionen, die von der Microsoft-Kontoauthentifizierung unterstützt werden, finden Sie in der [MicrosoftAccountOptions-API-Referenz.](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) Dies kann verwendet werden, um verschiedene Informationen über den Benutzer anzufordern.

## <a name="sign-in-with-microsoft-account"></a>Anmelden mit Microsoft-Konto

Führen Sie die App aus, und klicken Sie auf **Anmelden**. Es wird eine Option zum Anmelden bei Microsoft angezeigt. Wenn Sie auf Microsoft klicken, werden Sie zur Authentifizierung zu Microsoft weitergeleitet. Nachdem Sie sich mit Ihrem Microsoft-Konto angemeldet haben, werden Sie aufgefordert, der App den Zugriff auf Ihre Informationen zu gewähren:

Tippen Sie auf **Ja** und Sie werden zurück zur Website weitergeleitet, auf der Sie Ihre E-Mail-Adresse festlegen können.

Sie sind jetzt mit Ihren Microsoft-Anmeldeinformationen angemeldet:

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Problembehandlung

* Wenn der Microsoft-Kontoanbieter Sie zu einer Anmeldefehlerseite umleitet, beachten Sie den `#` Fehlertitel und die Zeichenfolgenzeichenfolgenparameter der Beschreibung direkt nach dem (Hashtag) im Uri.

  Obwohl die Fehlermeldung auf ein Problem mit der Microsoft-Authentifizierung zu hinweisen scheint, ist die häufigste Ursache, dass Ihre Anwendung URI keinem der für die **Webplattform** angegebenen **Redirect-URIs** entspricht.
* Wenn Identity nicht durch Aufrufen `services.AddIdentity` konfiguriert `ConfigureServices`ist, führt der Versuch, sich zu authentifizieren, zu *ArgumentException: Die Option 'SignInScheme' muss bereitgestellt werden.* Die in diesem Beispiel verwendete Projektvorlage stellt sicher, dass dies geschieht.
* Wenn die Standortdatenbank nicht durch Anwenden der ersten Migration erstellt wurde, wird bei der Verarbeitung des Anforderungsfehlers *ein Datenbankvorgang fehlgeschlagen.* Tippen Sie auf **Migrations anwenden,** um die Datenbank zu erstellen und zu aktualisieren, um nach dem Fehler fortzufahren.

## <a name="next-steps"></a>Nächste Schritte

* In diesem Artikel wurde gezeigt, wie Sie sich bei Microsoft authentifizieren können. Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).

* Nachdem Sie Ihre Website in der Azure-Web-App veröffentlicht haben, erstellen Sie eine neue Client-Entsendestelle im Microsoft Developer Portal.

* Legen Sie die `Authentication:Microsoft:ClientId` und `Authentication:Microsoft:ClientSecret` Anwendungseinstellungen im Azure-Portal. Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.

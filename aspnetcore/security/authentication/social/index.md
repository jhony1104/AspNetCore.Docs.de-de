---
title: Authentifizierung über Facebook, Google und externe Anbieter in ASP.NET Core
author: rick-anderson
description: Dieses Tutorial veranschaulicht, wie Sie eine ASP.NET Core 2.x-App mithilfe von OAuth 2.0 und externen Authentifizierungsanbietern entwickeln.
ms.author: riande
ms.custom: mvc
ms.date: 05/10/2019
uid: security/authentication/social/index
ms.openlocfilehash: 8dac8a8a2276388414b6bb1211e970617b001637
ms.sourcegitcommit: ccbb84ae307a5bc527441d3d509c20b5c1edde05
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/19/2019
ms.locfileid: "65874806"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a>Authentifizierung über Facebook, Google und externe Anbieter in ASP.NET Core

Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Tutorial wird veranschaulicht, wie Sie eine ASP.NET Core 2.2-App erstellen, mit der Benutzer sich mithilfe von OAuth 2.0 und Anmeldeinformationen von externen Authentifizierungsanbietern anmelden können.

Die Anbieter [Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins) und [Microsoft](xref:security/authentication/microsoft-logins) werden in den folgenden Abschnitten behandelt. Andere Anbieter stehen in Paketen von Drittanbietern wie z.B. [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) und [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers) zur Verfügung.

![Symbole sozialer Medien wie Facebook, Twitter, Google Plus und Windows](index/_static/social.png)

Das Anmelden mit vorhandenen Anmeldeinformationen:
* ist für Benutzer sehr praktisch.
* lagert die Verwaltung des Anmeldevorgangs größtenteils an einen Drittanbieter aus. 

Beispiel dazu, wie Anmeldungen bei sozialen Medien für mehr Datenverkehr und gewonnene Kunden sorgen, finden Sie in Fallstudien von [Facebook](https://www.facebook.com/unsupportedbrowser) und [Twitter](https://dev.twitter.com/resources/case-studies).

## <a name="create-a-new-aspnet-core-project"></a>Erstellen eines neuen ASP.NET Core-Projekts

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Erstellen Sie ein neues Projekt.
* Wählen Sie **ASP.NET Core-Webanwendung** und **Weiter** aus.
* Geben Sie einen **Projektnamen** ein, und bestätigen oder ändern Sie den **Speicherort**. Wählen Sie **Erstellen** aus.
* Wählen Sie in der Dropdownliste **ASP.NET Core 2.2** aus. Wählen Sie in der Vorlagenliste **Webanwendung** aus.
* Wählen Sie unter **Authentifizierung** die Option **Ändern** aus, und legen Sie die Authentifizierung auf **Einzelne Benutzerkonten** fest. Klicken Sie auf **OK**.
* Wählen Sie im Fenster **Neue ASP.NET Core-Webanwendung erstellen** die Option **Erstellen** aus.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.

* Führen Sie die folgenden Befehle aus:

  ```console
  dotnet new webapp -o WebApp1 -au Individual -uld
  code -r WebApp1
  ```

  * Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *WebApp1*.
  * `-uld` verwendet LocalDB anstelle von SQLite. Lassen Sie `-uld` weg, um SQLite zu verwenden.
  * `-au Individual` erstellt den Code für die einzelne Authentifizierung.
  * Mit dem Befehl `code` wird der Ordner *WebApp1* in einer neuen Instanz von Visual Studio Code geöffnet.

* Es wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Objekte zum Erstellen und Debuggen sind in „WebApp1“ nicht vorhanden. Sollen sie hinzugefügt werden?** Wählen Sie **Ja**.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Wählen Sie **Datei** > **Neue Projektmappe** aus.
* Wählen Sie in der Randleiste **.NET Core** > **App** aus. Wählen Sie die Vorlage für **Webanwendungen** aus. Klicken Sie auf **Weiter**.
* Wählen Sie in der Dropdownliste **Zielframework** die Option **.NET Core 2.2** aus. Klicken Sie auf **Weiter**.
* Geben Sie einen **Projektnamen** an. Bestätigen oder ändern Sie den **Speicherort**. Wählen Sie **Erstellen** aus.

---

## <a name="apply-migrations"></a>Anwenden von Migrationen

* Führen Sie die App aus, und klicken Sie auf den Link **Registrieren**.
* Geben Sie die E-Mail-Adresse und das Kennwort für das neue Konto ein, und wählen Sie dann **Registrieren** aus.
* Befolgen Sie die Anweisungen zum Anwenden von Migrationen.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a>Verwenden von SecretManager zum Speichern von Token, die von Anmeldeanbietern zugewiesen wurden

Anmeldeanbieter aus dem Bereich der sozialen Medien weisen während des Registrierungsvorgangs Token des Typs **Anwendungs-ID** und **Anwendungsgeheimnis** zu. Die genauen Tokennamen unterscheiden sich je nach Anbieter. Diese Token stellen die Anmeldeinformationen dar, mit denen Ihre App auf ihre API zugreift. Diese Token setzen sich zu „Geheimnissen“ zusammen, die mithilfe von [Secret Manager](xref:security/app-secrets#secret-manager) mit Ihrer App-Konfiguration verknüpft werden können. Secret Manager ist eine sicherere Alternative zum Speichern von Token in einer Konfigurationsdatei wie z.B. *appsettings.json*.

> [!IMPORTANT]
> Secret Manager ist nur zur Entwicklung vorgesehen. Sie können Azure-Test- und -Produktionsgeheimnisse mit dem [Konfigurationsanbieter Azure Key Vault](xref:security/key-vault-configuration) speichern und schützen.

Führen Sie die im Artikel [Sichere Speicherung von App-Geheimnissen bei einer Entwicklung in ASP.NET Core](xref:security/app-secrets) beschriebenen Schritte durch, um Token zu speichern, die von den folgenden Anmeldeanbietern zugewiesen werden.

## <a name="setup-login-providers-required-by-your-application"></a>Einrichten der für Ihre Anwendung erforderlichen Anmeldeanbietern

In den folgenden Themen erfahren Sie, wie Sie Ihre Anwendung für die entsprechenden Anbieter konfigurieren:

* Anweisungen für [Facebook](xref:security/authentication/facebook-logins)
* Anweisungen für [Twitter](xref:security/authentication/twitter-logins)
* Anweisungen für [Google](xref:security/authentication/google-logins)
* Anweisungen für [Microsoft](xref:security/authentication/microsoft-logins)
* Anweisungen für [andere Anbieter](xref:security/authentication/otherlogins)

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a>Optionales Festlegen des Kennworts

Wenn Sie sich bei einem externen Anmeldeanbieter registrieren, wird kein Kennwort bei der App registriert. Dadurch entfällt für Sie Erstellen und Merken eines Kennworts für die Website. Sie werden allerdings auch vom externen Anmeldeanbieter abhängig. Wenn der externe Anmeldeanbieter nicht verfügbar ist, können Sie sich nicht bei der Website anmelden.

So erstellen Sie ein Kennwort und melden sich mithilfe Ihrer E-Mail-Adresse an, die Sie während des Anmeldevorgangs bei externen Anbietern festgelegt haben:

* Klicken Sie rechts oben auf den Link **Hallo &lt;E-Mail-Alias&gt;** , um zur Ansicht **Verwalten** zu gelangen.

![Ansicht „Verwalten“ der Webanwendung](index/_static/pass1a.png)

* Klicken Sie auf **Erstellen**.

![Seite zum Festlegen Ihres Kennworts](index/_static/pass2a.png)

* Legen Sie ein gültiges Kennwort fest, das Sie anschließend mit Ihrer E-Mail-Adresse zum Anmelden nutzen können.

## <a name="next-steps"></a>Nächste Schritte

* In diesem Artikel wurde die externe Authentifizierung behandelt. Ferner wurden die Voraussetzungen für das Hinzufügen externer Anmeldungen zu Ihrer ASP.NET Core-App erläutert.

* Konsultieren Sie anbieterspezifische Seiten zum Konfigurieren von Anmeldungen für die von Ihrer App angeforderten Anbieter.

* Möglicherweise möchten Sie zusätzliche Daten zum Benutzer und seinen Zugriffs- und Aktualisierungstoken persistent speichern. Weitere Informationen finden Sie unter <xref:security/authentication/social/additional-claims>.

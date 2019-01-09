---
title: Authentifizierung über Facebook, Google und externe Anbieter in ASP.NET Core
author: rick-anderson
description: Dieses Tutorial veranschaulicht, wie Sie eine ASP.NET Core 2.x-App mithilfe von OAuth 2.0 und externen Authentifizierungsanbietern entwickeln.
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
uid: security/authentication/social/index
ms.openlocfilehash: 063d452fb6ab91b712ade7f7b7ed99823dbdc657
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098817"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a>Authentifizierung über Facebook, Google und externe Anbieter in ASP.NET Core

Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)

Dieses Tutorial veranschaulicht, wie Sie eine ASP.NET Core 2.x-App erstellen, die Benutzern ermöglicht, sich mithilfe von OAuth 2.0 und Anmeldeinformationen von externen Authentifizierungsanbietern anzumelden.

Die Anbieter [Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins) und [Microsoft](xref:security/authentication/microsoft-logins) werden in den folgenden Abschnitten behandelt. Andere Anbieter stehen in Paketen von Drittanbietern wie z.B. [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) und [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers) zur Verfügung.

![Symbole sozialer Medien wie Facebook, Twitter, Google Plus und Windows](index/_static/social.png)

Den Benutzern zu ermöglichen, sich mit ihren vorhandenen Anmeldeinformationen anzumelden, ist für diese komfortabel und verlagert einen Großteil der Komplexität der Verwaltung des Anmeldevorgangs an einen Drittanbieter. Beispiel dazu, wie Anmeldungen bei sozialen Medien für mehr Datenverkehr und gewonnene Kunden sorgen, finden Sie in Fallstudien von [Facebook](https://www.facebook.com/unsupportedbrowser) und [Twitter](https://dev.twitter.com/resources/case-studies).

## <a name="create-a-new-aspnet-core-project"></a>Erstellen eines neuen ASP.NET Core-Projekts

* Erstellen Sie in Visual Studio 2017 ein neues Projekt auf der Startseite oder über **Date** > **Neu** > **Projekt**.

* Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, die in der Kategorie **Visual C#** > **.NET Core** verfügbar ist:

![Dialogfeld "Neues Projekt"](index/_static/new-project.png)

* Tippen Sie auf **Webanwendung**, und vergewissern Sie sich, dass **Authentifizierung** auf **Einzelne Benutzerkonten** festgelegt ist:

![Dialogfeld „Neue Webanwendung“](index/_static/select-project.png)

Hinweis: Dieses Tutorial bezieht sich auf die ASP.NET Core 2.0 SDK-Version, die oben im Assistenten ausgewählt werden kann.

## <a name="apply-migrations"></a>Anwenden von Migrationen

* Führen Sie die App aus, und klicken Sie auf den Link **Anmelden**.
* Klicken Sie auf den Link **Als neuer Benutzer registrieren**.
* Geben Sie die E-Mail-Adresse und das Kennwort für das neue Konto ein, und wählen Sie dann **Registrieren** aus.
* Befolgen Sie die Anweisungen zum Anwenden von Migrationen.

## <a name="require-https"></a>Erforderlichkeit von HTTPS

OAuth 2.0 erfordert die Verwendung von SSL/TLS für die Authentifizierung über das HTTPS-Protokoll.

Projekte, die mithilfe der Projektvorlagen **Webanwendung** oder **Web-API** mit ASP.NET Core 2.1 oder höher erstellt wurden, werden automatisch zur Aktivierung von HTTPS konfiguriert. Die App wird mit einem sicheren Standardendpunkt gestartet, wenn die Option **Einzelne Benutzerkonten** im Dialogfeld **Authentifizierung ändern** des Projekt-Assistenten ausgewählt ist.

Weitere Informationen finden Sie unter <xref:security/enforcing-ssl>.

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

* Tippen Sie rechts oben auf den Link **Hallo &lt;E-Mail-Alias&gt;**, um zur Ansicht **Verwalten** zu gelangen.

![Ansicht „Verwalten“ der Webanwendung](index/_static/pass1a.png)

* Tippen Sie auf **Erstellen**.

![Seite zum Festlegen Ihres Kennworts](index/_static/pass2a.png)

* Legen Sie ein gültiges Kennwort fest, das Sie anschließend mit Ihrer E-Mail-Adresse zum Anmelden nutzen können.

## <a name="next-steps"></a>Nächste Schritte

* In diesem Artikel wurde die externe Authentifizierung behandelt. Ferner wurden die Voraussetzungen für das Hinzufügen externer Anmeldungen zu Ihrer ASP.NET Core-App erläutert.

* Konsultieren Sie anbieterspezifische Seiten zum Konfigurieren von Anmeldungen für die von Ihrer App angeforderten Anbieter.

* Möglicherweise möchten Sie zusätzliche Daten zum Benutzer und seinen Zugriffs- und Aktualisierungstoken persistent speichern. Weitere Informationen finden Sie unter <xref:security/authentication/social/additional-claims>.

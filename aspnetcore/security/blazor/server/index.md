---
title: Absichern von Blazor Server-Apps als ASP.NET Core-Anwendungen
author: guardrex
description: Erfahren Sie, wie Sie Blazor Server-Apps als ASP.NET Core-Anwendungen absichern.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/27/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server/index
ms.openlocfilehash: 0021911b731e57bc6eabf857c27a13462e7400ae
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82206329"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>Absichern von Blazor Server-Apps als ASP.NET Core-Anwendungen

Von [Luke Latham](https://github.com/guardrex)

## <a name="blazor-server-project-template"></a>Projektvorlage „Blazor Server“

Die Projektvorlage „Blazor Server“ kann beim Erstellen des Projekts für die Authentifizierung konfiguriert werden.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Befolgen Sie die Visual Studio-Anweisungen im Artikel <xref:blazor/get-started>, um ein neues Blazor Server-Projekt mit einem Authentifizierungsmechanismus zu erstellen.

Nachdem Sie im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Vorlage **Blazor-Server-App** ausgewählt haben, wählen Sie im Dialogfeld **Authentifizierung** die Option **Ändern**.

Ein Dialogfeld wird geöffnet, in dem dieselben Authentifizierungsmechanismen angeboten werden, die auch für andere ASP.NET-Core-Projekte verfügbar sind:

* **Keine Authentifizierung**
* **Einzelne Benutzerkonten** &ndash; Benutzerkonten können gespeichert werden:
  * Innerhalb der App anhand des Systems [Identität](xref:security/authentication/identity) von ASP.NET Core.
  * Mit [Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* **Geschäfts-, Schul- oder Unikonten**
* **Windows-Authentifizierung**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Befolgen Sie die Visual Studio Code-Anweisungen im Artikel <xref:blazor/get-started>, um ein neues Blazor Server-Projekt mit einem Authentifizierungsmechanismus zu erstellen:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Zulässige Authentifizierungswerte (`{AUTHENTICATION}`) werden in der folgenden Tabelle angezeigt.

| Authentifizierungsmechanismus | Beschreibung |
| ------------------------ | ----------- |
| `None` (Standardwert)         | Keine Authentifizierung |
| `Individual`             | In der App mit ASP.NET Core-Identität gespeicherte Benutzer |
| `IndividualB2C`          | In [Azure AD B2C](xref:security/authentication/azure-ad-b2c) gespeicherte Benutzer |
| `SingleOrg`              | Organisationsauthentifizierung für einen einzelnen Mandanten |
| `MultiOrg`               | Organisationsauthentifizierung für mehrere Mandanten |
| `Windows`                | Windows-Authentifizierung |

Bei Verwenden der Option `-o|--output` nutzt der Befehl den für den Platzhalter `{APP NAME}` angegebenen Wert:

* Erstellen Sie einen Ordner für das Projekt.
* Benennen Sie das Projekt.

Weitere Informationen finden Sie im Befehl [dotnet new](/dotnet/core/tools/dotnet-new) im Leitfaden für .NET Core.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

1. Befolgen Sie die Anleitungen für Visual Studio für Mac im Artikel <xref:blazor/get-started>.

1. Wählen Sie im Schritt **Configure your new Blazor Server App** (Ihre neue Blazor Server-App konfigurieren) in der Dropdownliste **Authentifizierung** die Option **Individual Authentication (in-app)** (Individuelle Authentifizierung [in der App]) aus.

1. Die Anwendung wird für einzelne Benutzer erstellt, die in der App mit ASP.NET Core-Identität gespeichert sind.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli/)

Befolgen Sie die Anleitungen für die .NET Core-CLI im Artikel <xref:blazor/get-started>, um ein neues Blazor Server-Projekt mit einem Authentifizierungsmechanismus zu erstellen:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Zulässige Authentifizierungswerte (`{AUTHENTICATION}`) werden in der folgenden Tabelle angezeigt.

| Authentifizierungsmechanismus | Beschreibung |
| ------------------------ | ----------- |
| `None` (Standardwert)         | Keine Authentifizierung |
| `Individual`             | In der App mit ASP.NET Core-Identität gespeicherte Benutzer |
| `IndividualB2C`          | In [Azure AD B2C](xref:security/authentication/azure-ad-b2c) gespeicherte Benutzer |
| `SingleOrg`              | Organisationsauthentifizierung für einen einzelnen Mandanten |
| `MultiOrg`               | Organisationsauthentifizierung für mehrere Mandanten |
| `Windows`                | Windows-Authentifizierung |

Bei Verwenden der Option `-o|--output` nutzt der Befehl den für den Platzhalter `{APP NAME}` angegebenen Wert:

* Erstellen Sie einen Ordner für das Projekt.
* Benennen Sie das Projekt.

Weitere Informationen finden Sie im Befehl [dotnet new](/dotnet/core/tools/dotnet-new) im Leitfaden für .NET Core.

---

---
title: Absichern von Blazor Server-Apps als ASP.NET Core-Anwendungen
author: guardrex
description: Erfahren Sie, wie Sie Blazor Server-Apps als ASP.NET Core-Anwendungen absichern.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/server/index
ms.openlocfilehash: 5ba7bbde49bfc232795d375a1ec644825a0dee1e
ms.sourcegitcommit: 67eadd7bf28eae0b8786d85e90a7df811ffe5904
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2020
ms.locfileid: "84454635"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>Absichern von Blazor Server-Apps als ASP.NET Core-Anwendungen

Von [Luke Latham](https://github.com/guardrex)

## <a name="blazor-server-project-template"></a>Blazor Server-Projektvorlage

Die Projektvorlage „Blazor Server“ kann beim Erstellen des Projekts für die Authentifizierung konfiguriert werden.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Befolgen Sie die Visual Studio-Anweisungen im Artikel <xref:blazor/get-started>, um ein neues Blazor Server-Projekt mit einem Authentifizierungsmechanismus zu erstellen.

Nachdem Sie im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Vorlage **Blazor Server-App** ausgewählt haben, wählen Sie im Dialogfeld **Authentifizierung** die Option **Ändern** aus.

Ein Dialogfeld wird geöffnet, in dem dieselben Authentifizierungsmechanismen angeboten werden, die auch für andere ASP.NET-Core-Projekte verfügbar sind:

* **Keine Authentifizierung**
* **Einzelne Benutzerkonten**: Benutzerkonten können wie folgt gespeichert werden:
  * Innerhalb der App anhand des Systems [Identity](xref:security/authentication/identity) von ASP.NET Core.
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

Befolgen Sie die Anleitungen für die .NET Core-CLI im Artikel <xref:blazor/get-started>, um ein neues Blazor Blazor-Projekt mit einem Authentifizierungsmechanismus zu erstellen:

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

## <a name="secure-an-existing-app"></a>Schützen einer vorhandenen App

Das Schützen von Blazor Server-Apps funktioniert genau wie bei ASP.NET Core-Apps. Weitere Informationen finden Sie in den Artikeln unter <xref:security/index>.

## <a name="scaffold-identity"></a>Gerüst Identity

Gerüst Identity in einem Blazor Server-Projekt:

* [Ohne vorhandene Autorisierung](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization)
* [Mit Autorisierung](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization)

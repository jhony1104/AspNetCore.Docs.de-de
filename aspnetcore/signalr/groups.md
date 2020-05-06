---
title: Verwalten von Benutzern und Gruppen inSignalR
author: bradygaster
description: Übersicht über ASP.net Core SignalR Benutzer-und Gruppenverwaltung.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/groups
ms.openlocfilehash: af498575899fcfa407aba9f9f49c0bfeabadc7a7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776297"
---
# <a name="manage-users-and-groups-in-signalr"></a>Verwalten von Benutzern und Gruppen inSignalR

Von [Brennan](https://github.com/BrennanConroy) "a"

SignalRermöglicht das Senden von Nachrichten an alle Verbindungen, die einem bestimmten Benutzer zugeordnet sind, sowie an benannte Verbindungs Gruppen.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(Vorgehensweise zum herunterladen)](xref:index#how-to-download-a-sample)

## <a name="users-in-signalr"></a>Benutzer inSignalR

SignalRermöglicht das Senden von Nachrichten an alle Verbindungen, die einem bestimmten Benutzer zugeordnet sind. Standardmäßig verwendet SignalR die `ClaimTypes.NameIdentifier` aus der `ClaimsPrincipal` , die der Verbindung zugeordnet ist, als Benutzer Bezeichner. Ein einzelner Benutzer kann über mehrere Verbindungen mit einer SignalR App verfügen. Beispielsweise könnte ein Benutzer sowohl auf seinem Desktop als auch auf seinem Telefon verbunden sein. Jedes Gerät verfügt über eine SignalR separate Verbindung, aber alle sind dem gleichen Benutzer zugeordnet. Wenn eine Nachricht an den Benutzer gesendet wird, erhalten alle Verbindungen, die diesem Benutzer zugeordnet sind, die Nachricht. Der Benutzer Bezeichner für eine Verbindung kann über die `Context.UserIdentifier` -Eigenschaft in ihrem Hub aufgerufen werden.

Senden Sie eine Nachricht an einen bestimmten Benutzer, indem Sie die Benutzer- `User` ID an die Funktion in der Hub-Methode übergeben, wie im folgenden Beispiel gezeigt:

> [!NOTE]
> Bei der Benutzerkennung wird die Groß-/Kleinschreibung beachtet

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-signalr"></a>Gruppen inSignalR

Eine Gruppe ist eine Auflistung von Verbindungen, die einem Namen zugeordnet sind. Nachrichten können an alle Verbindungen in einer Gruppe gesendet werden. Gruppen sind die empfohlene Vorgehensweise zum Senden an eine Verbindung oder mehrere Verbindungen, da die Gruppen von der Anwendung verwaltet werden. Eine Verbindung kann Mitglied mehrerer Gruppen sein. Dadurch eignen sich Gruppen ideal für eine Chat-Anwendung, bei der jeder Raum als Gruppe dargestellt werden kann. Verbindungen können über die-Methode und die `AddToGroupAsync` - `RemoveFromGroupAsync` Methode zu Gruppen hinzugefügt oder aus diesen entfernt werden.

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

Die Gruppenmitgliedschaft wird nicht beibehalten, wenn eine Verbindung erneut hergestellt wird. Die Verbindung muss der Gruppe erneut beitreten, wenn Sie wieder hergestellt wird. Es ist nicht möglich, die Mitglieder einer Gruppe zu zählen, da diese Informationen nicht verfügbar sind, wenn die Anwendung auf mehrere Server skaliert wird.

Zum Schutz des Zugriffs auf Ressourcen bei der Verwendung von Gruppen verwenden Sie die [Authentifizierungs-und Autorisierungs](xref:signalr/authn-and-authz) Funktionen in ASP.net Core. Wenn Sie nur Benutzer zu einer Gruppe hinzufügen, wenn die Anmelde Informationen für diese Gruppe gültig sind, werden an diese Gruppe gesendete Nachrichten nur an autorisierte Benutzer übermittelt. Allerdings handelt es sich bei Gruppen nicht um eine Sicherheitsfunktion. Authentifizierungs Ansprüche verfügen über Funktionen, die von Gruppen nicht unterliegen, z. b. Ablauf und Sperrung. Wenn die Berechtigung eines Benutzers für den Zugriff auf die Gruppe aufgehoben wird, müssen Sie diese manuell erkennen und aus der Gruppe entfernen.

> [!NOTE]
> Bei Gruppennamen wird die Groß-/Kleinschreibung beachtet.

## <a name="related-resources"></a>Zugehörige Ressourcen

* [Erste Schritte](xref:tutorials/signalr)
* [Hubs](xref:signalr/hubs)
* [Veröffentlichen in Azure](xref:signalr/publish-to-azure-web-app)

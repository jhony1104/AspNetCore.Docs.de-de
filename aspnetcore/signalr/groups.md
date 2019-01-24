---
title: Verwalten von Benutzern und Gruppen in SignalR
author: bradygaster
description: Übersicht über ASP.NET Core SignalR Benutzer-und Gruppenverwaltung.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/04/2018
uid: signalr/groups
ms.openlocfilehash: 0a4836cfa3cf79136b56da1ff05ce8533b4df16c
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837883"
---
# <a name="manage-users-and-groups-in-signalr"></a>Verwalten von Benutzern und Gruppen in SignalR

Durch [Brennan Conroy](https://github.com/BrennanConroy)

SignalR ermöglicht, Nachrichten an alle Verbindungen, die einen bestimmten Benutzer zugeordneten gesendet werden, sowie um benannte Gruppen von Verbindungen.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/groups/sample/) [(Herunterladen von)](xref:index#how-to-download-a-sample)

## <a name="users-in-signalr"></a>Benutzer in SignalR

SignalR können Sie zum Senden von Nachrichten für alle Verbindungen, die einen bestimmten Benutzer zugeordnet. SignalR verwendet standardmäßig die `ClaimTypes.NameIdentifier` aus der `ClaimsPrincipal` der Verbindung als die Benutzer-ID zugeordnet. Ein einzelner Benutzer kann mehrere Verbindungen mit einer SignalR-app haben. Beispielsweise kann ein Benutzer auf ihrem Desktop als auch ihre Rufnummer bestehen. Jedes Gerät verfügt über eine separate SignalR-Verbindung, aber sie alle mit demselben Benutzer zugeordnet sind. Wenn eine Nachricht an den Benutzer gesendet wird, empfangen alle Verbindungen mit diesem Benutzer verknüpften der Nachricht. Die Benutzer-ID für eine Verbindung zugegriffen werden kann, indem die `Context.UserIdentifier` Eigenschaft in Ihrem Hub.

Senden einer Nachricht an einen bestimmten Benutzer durch die Benutzer-ID zum Übergeben der `User` Funktion in der hubmethode, wie im folgenden Beispiel gezeigt:

> [!NOTE]
> Die Benutzer-ID wird Groß-/Kleinschreibung beachtet.

```csharp
public Task SendPrivateMessage(string user, string message)
{
    return Clients.User(user).SendAsync("ReceiveMessage", message);
}
```

Die Benutzer-ID kann angepasst werden, durch das Erstellen einer `IUserIdProvider`, und registrieren ihn in `ConfigureServices`.

[!code-csharp[UserIdProvider](groups/sample/customuseridprovider.cs?range=4-10)]

[!code-csharp[Configure service](groups/sample/startup.cs?range=21-22,39-42)]

> [!NOTE]
> AddSignalR muss vor der Registrierung Ihrer benutzerdefinierten SignalR-Dienste aufgerufen werden.

## <a name="groups-in-signalr"></a>Gruppen in SignalR

Eine Gruppe ist eine Auflistung von Verbindungen mit einem Namen zugeordnet. Nachrichten können für alle Verbindungen in einer Gruppe gesendet werden. Gruppen sind die empfohlene Vorgehensweise, um eine Verbindung oder mehrere Verbindungen gesendet werden, da es sich bei die Gruppen von der Anwendung verwaltet werden. Eine Verbindung kann Mitglied mehrerer Gruppen sein. Dadurch ist Gruppen ideal für Anwendungen wie eine Chat-Anwendung, in dem jeweiligen Raums kann als eine Gruppe dargestellt werden. Verbindungen hinzugefügt oder aus Gruppen über entfernt werden können, die `AddToGroupAsync` und `RemoveFromGroupAsync` Methoden.

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

Der Gruppenmitgliedschaft wird nicht beibehalten, wenn eine Verbindung erneut hergestellt. Die Verbindung muss die Gruppe erneut beitreten, wenn er erneut hergestellt wird. Es ist nicht möglich, um die Mitglieder einer Gruppe zu zählen, da diese Informationen sind nicht verfügbar, wenn die Anwendung auf mehreren Servern skaliert wird.

Verwenden, um den Zugriff auf Ressourcen zu schützen, bei der Verwendung von Gruppen [Authentifizierung und Autorisierung](xref:signalr/authn-and-authz) Funktionen in ASP.NET Core. Wenn Sie nur Benutzer zu einer Gruppe hinzufügen, wenn die Anmeldeinformationen für diese Gruppe gültig sind, werden Nachrichten an diese Gruppe nur auf autorisierte Benutzer geleitet. Gruppen sind jedoch nicht über eine Sicherheitsfunktion. Authentifizierungsansprüchen haben Funktionen, die Gruppen nicht, wie z. B. Ablauf und Sperrung sind. Wenn die Berechtigung eines Benutzers auf die Gruppe aufgehoben wird, müssen Sie manuell zu erkennen, und entfernen sie aus der Gruppe ein.

> [!NOTE]
> Gruppennamen werden Groß-/Kleinschreibung beachtet.

## <a name="related-resources"></a>Weitere Informationen

* [Erste Schritte](xref:tutorials/signalr)
* [Hubs](xref:signalr/hubs)
* [Veröffentlichen in Azure](xref:signalr/publish-to-azure-web-app)

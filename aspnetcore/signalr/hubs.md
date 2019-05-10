---
title: Verwenden von Hubs in ASP.NET Core SignalR
author: bradygaster
description: Erfahren Sie, wie Sie mit Hubs in ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/20/2018
uid: signalr/hubs
ms.openlocfilehash: eb87aab2b7f3a58c6cec80f48f7616749f0809e2
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897077"
---
# <a name="use-hubs-in-signalr-for-aspnet-core"></a>Verwenden von Hubs in SignalR für ASP.NET Core

Durch [Rachel Appel](https://twitter.com/rachelappel) und [Kevin Griffin](https://twitter.com/1kevgriff)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(Herunterladen von)](xref:index#how-to-download-a-sample)

## <a name="what-is-a-signalr-hub"></a>Was ist eine SignalR-Hub

Der SignalR-Hubs-API können Sie zum Aufrufen von Methoden auf verbundene Clients vom Server. In den Servercode definieren Sie Methoden, die vom Client aufgerufen werden. Im Clientcode definieren Sie Methoden, die vom Server aufgerufen werden. SignalR kümmert sich um alles, was hinter den Kulissen, die in Echtzeit-Client-zu-Server und Server-zu-Client-Kommunikation möglich macht.

## <a name="configure-signalr-hubs"></a>Konfigurieren von SignalR-hubs

Die SignalR-Middleware ist erforderlich, einige Dienste, die durch den Aufruf konfiguriert werden `services.AddSignalR`.

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

Beim Hinzufügen von SignalR-Funktionen zu einer ASP.NET Core-app einrichten SignalR-Routen, durch den Aufruf `app.UseSignalR` in die `Startup.Configure` Methode.

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

## <a name="create-and-use-hubs"></a>Erstellen und Verwenden von hubs

Erstellen Sie einen Hub durch Deklarieren einer Klasse, die von erbt `Hub`, und fügen Sie öffentliche Methoden hinzu. Clients können als definierten Methoden aufrufen `public`.

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

Sie können einen Rückgabetyp und Parameter, einschließlich von komplexen Typen und Arrays, wie in jeder C#-Methode angeben. SignalR verarbeitet die Serialisierung und Deserialisierung von komplexen Objekten und Arrays in Ihre Parameter und Rückgabewerte.

> [!NOTE]
> Hubs sind vorübergehend:
>
> * Speichern Sie Status nicht in einer Eigenschaft für die hubklasse. Jeder Aufruf der Hub-Methode wird eine neue hubinstanz ausgeführt.  
> * Verwendung `await` beim Aufrufen von asynchroner Methoden, die auf dem Hub bleiben aktiv abhängig sind. Zum Beispiel eine Methode, z. B. `Clients.All.SendAsync(...)` kann fehlschlagen, wenn er, ohne aufgerufen wird `await` und die hubmethode abgeschlossen wird, bevor Sie `SendAsync` abgeschlossen ist.

## <a name="the-context-object"></a>Das Context-Objekt

Die `Hub` -Klasse verfügt über eine `Context` Eigenschaft, die mit Informationen über die Verbindung die folgenden Eigenschaften enthält:

| Eigenschaft | Beschreibung |
| ------ | ----------- |
| `ConnectionId` | Ruft die eindeutige ID für die Verbindung von SignalR zugewiesen. Eine Verbindungs­id für jede Verbindung ist vorhanden.|
| `UserIdentifier` | Ruft die [Benutzerbezeichner](xref:signalr/groups). SignalR verwendet standardmäßig die `ClaimTypes.NameIdentifier` aus der `ClaimsPrincipal` der Verbindung als die Benutzer-ID zugeordnet. |
| `User` | Ruft die `ClaimsPrincipal` mit dem aktuellen Benutzer verknüpft ist. |
| `Items` | Ruft eine Auflistung von Schlüssel-Wert, die zum Freigeben von Daten innerhalb des Bereichs dieser Verbindung verwendet werden kann. Daten können in dieser Auflistung gespeichert werden, und bleiben für die Verbindung in anderen hubmethodenaufrufe. |
| `Features` | Ruft die Auflistung der verfügbaren Funktionen für die Verbindung ab. Vorerst ist nicht dieser Auflistung in den meisten Szenarien erforderlich, damit noch ausführlich dokumentiert ist nicht. |
| `ConnectionAborted` | Ruft eine `CancellationToken` , die benachrichtigt werden, wenn die Verbindung abgebrochen wird. |

`Hub.Context` Außerdem enthält die folgenden Methoden:

| Methode | Beschreibung |
| ------ | ----------- |
| `GetHttpContext` | Gibt die `HttpContext` für die Verbindung oder `null` , wenn die Verbindung nicht mit einer HTTP-Anforderung verknüpft ist. Für HTTP-Verbindungen können Sie diese Methode zum Abrufen von Informationen wie z. B. HTTP-Header und Abfragezeichenfolgen. |
| `Abort` | Bricht die Verbindung ab. |

## <a name="the-clients-object"></a>Das Objekt des Clients

Die `Hub` -Klasse verfügt über eine `Clients` -Eigenschaft, die folgenden Eigenschaften für die Kommunikation zwischen Server und Client enthält:

| Eigenschaft | Beschreibung |
| ------ | ----------- |
| `All` | Ruft eine Methode für alle verbundenen clients |
| `Caller` | Ruft eine Methode auf dem Client, der die hubmethode aufgerufen hat. |
| `Others` | Ruft eine Methode für alle verbundenen Clients mit Ausnahme des Clients, die die Methode aufgerufen hat. |

`Hub.Clients` Außerdem enthält die folgenden Methoden:

| Methode | Beschreibung |
| ------ | ----------- |
| `AllExcept` | Ruft eine Methode für alle verbundenen Clients mit Ausnahme der angegebenen Verbindungen |
| `Client` | Ruft eine Methode auf einem bestimmten verbundenen client |
| `Clients` | Ruft eine Methode für bestimmte verbundene clients |
| `Group` | Ruft eine Methode für alle Verbindungen in der angegebenen Gruppe  |
| `GroupExcept` | Ruft eine Methode für alle Verbindungen in der angegebenen Gruppe an, mit Ausnahme der angegebenen Verbindungen. |
| `Groups` | Ruft eine Methode für mehrere Gruppen von Verbindungen  |
| `OthersInGroup` | Ruft eine Methode für eine Gruppe von Verbindungen mit Ausnahme des Clients, der die hubmethode aufgerufen hat  |
| `User` | Ruft eine Methode für alle Verbindungen, die einen bestimmten Benutzer zugeordnet sind. |
| `Users` | Ruft eine Methode für alle Verbindungen, die die angegebenen Benutzer zugeordnet sind. |

Jede Eigenschaft oder Methode in den vorhergehenden Tabellen gibt ein Objekt mit einem `SendAsync` Methode. Die `SendAsync` Methode können Sie den Namen und Parameter, der die Methode aufrufen, angeben.

## <a name="send-messages-to-clients"></a>Senden von Nachrichten an clients

Um Aufrufe von bestimmten Clients zu machen, verwenden Sie die Eigenschaften der `Clients` Objekt. Im folgenden Beispiel gibt es drei Methoden des Hubs:

* `SendMessage` sendet eine Nachricht an alle verbundenen Clients, die mit `Clients.All`.
* `SendMessageToCaller` sendet eine Nachricht an den Aufrufer, die mit `Clients.Caller`.
* `SendMessageToGroups` sendet eine Nachricht an alle Clients in der `SignalR Users` Gruppe.

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a>Stark typisierte hubs

Ein Nachteil der Verwendung von `SendAsync` besteht darin, dass dabei auf magische Zeichenfolge an die Methode aufgerufen werden. Dadurch wird Code öffnen, um Laufzeitfehler, wenn Sie der Methodennamen falsch geschrieben ist, oder auf dem Client fehlt.

Eine Alternative zur Verwendung `SendAsync` stark typisiert werden die `Hub` mit <xref:Microsoft.AspNetCore.SignalR.Hub%601>. Im folgenden Beispiel die `ChatHub` Clientmethoden wurden, in eine Schnittstelle namens extrahiert `IChatClient`.  

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

Diese Schnittstelle kann verwendet werden, auf der vorherigen Umgestalten `ChatHub` Beispiel.

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

Mithilfe von `Hub<IChatClient>` ermöglicht während der Kompilierung der Clientmethoden überprüfen. Dies verhindert, dass Probleme verursacht werden, da mit der Magic-Zeichenfolgen, `Hub<T>` können nur Zugriff auf die in der Schnittstelle definierten Methoden bereitstellen.

Verwenden eines stark typisierten `Hub<T>` deaktiviert die Möglichkeit, verwenden Sie `SendAsync`. Alle für die Schnittstelle definierten Methoden können immer noch als asynchron definiert werden. Jede dieser Methoden sollten in der Tat Zurückgeben einer `Task`. Da es sich um eine Schnittstelle ist, verwenden Sie nicht die `async` Schlüsselwort. Zum Beispiel:

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> Die `Async` Suffix wird nicht aus der Name der Methode entfernt. Wenn die Clientmethode definiert ist, mit `.on('MyMethodAsync')`, verwenden Sie nicht `MyMethodAsync` als Namen.

## <a name="change-the-name-of-a-hub-method"></a>Ändern Sie den Namen einer Hub-Methode

Standardmäßig ist ein Servername der Hub-Methode den Namen der .NET-Methode. Sie können jedoch die [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) Attribut, um diese Standardeinstellung ändern, und geben Sie manuell einen Namen für die Methode. Der Client sollte diesem Namen gibt, anstatt des Methodennamens von .NET verwenden, beim Aufruf der Methode.

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a>Behandeln von Ereignissen für eine Verbindung

Die SignalR-Hubs-API bietet die `OnConnectedAsync` und `OnDisconnectedAsync` virtuelle Methoden zum Verwalten und Nachverfolgen von Verbindungen. Überschreiben der `OnConnectedAsync` virtuelle Methode, um die Aktionen ausführen, wenn einem Client an den Hub zwischen, z. B. zu einer Gruppe hinzufügen.

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

Überschreiben der `OnDisconnectedAsync` virtuelle Methode, um die Aktionen ausführen, wenn ein Client die Verbindung trennt. Wenn der Client die Verbindung absichtlich trennt (durch Aufrufen von `connection.stop()`, z. B.), wird die `exception` Parameter `null`. Jedoch, wenn der Client aufgrund eines Fehlers (z. B. einem Netzwerkausfall) getrennt ist die `exception` -Parameter enthält eine Ausnahme, die den Fehler beschreibt.

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

## <a name="handle-errors"></a>Behandeln von Fehlern

In den hubmethoden ausgelöste Ausnahmen werden an den Client gesendet, die die Methode aufgerufen. Für JavaScript-Client die `invoke` Methode gibt eine [JavaScript-Zusage](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). Wenn der Client einen Fehler mit einem Handler empfängt die Zusicherung mit angefügten `catch`, es wurde aufgerufen, und übergeben Sie als JavaScript- `Error` Objekt.

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

Wenn es sich bei Ihrem Hub eine Ausnahme auslöst, werden nicht die Verbindungen geschlossen. Standardmäßig gibt SignalR eine generische Fehlermeldung an den Client zurück. Zum Beispiel:

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

Unerwartete Ausnahmen enthalten oft vertrauliche Informationen wie den Namen eines Datenbankservers zu einer Ausnahme wird ausgelöst, wenn die Verbindung mit der ein Fehler auftritt. SignalR nicht diese detaillierten Fehlermeldungen werden standardmäßig als Sicherheitsmaßnahme verfügbar machen. Finden Sie unter den [Security Considerations Artikel](xref:signalr/security#exceptions) für Weitere Informationen darüber, warum die Details der Ausnahme unterdrückt werden.

Wenn Sie haben eine außergewöhnliche Bedingung Sie *führen* an den Client weitergegeben werden soll, können Sie mit der `HubException` Klasse. Wenn Sie Auslösen einer `HubException` aus der hubmethode, SignalR **wird** die gesamte Nachricht an den Client, unverändert gesendet.

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> SignalR sendet nur die `Message` -Eigenschaft der Ausnahme an den Client. Die stapelüberwachung und andere Eigenschaften auf die Ausnahme nicht an den Client zur Verfügung.

## <a name="related-resources"></a>Weitere Informationen

* [Einführung in ASP.NET Core SignalR](xref:signalr/introduction)
* [JavaScript-Client](xref:signalr/javascript-client)
* [Veröffentlichen in Azure](xref:signalr/publish-to-azure-web-app)

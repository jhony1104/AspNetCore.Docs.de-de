---
title: Verwenden von Hubs in ASP.net Core SignalR
author: bradygaster
description: Erfahren Sie, wie Sie Hubs in ASP.net Core SignalRverwenden können.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- SignalR
uid: signalr/hubs
ms.openlocfilehash: e5bc12c5ccafe2b5273d72e6bde0f631ca043428
ms.sourcegitcommit: f259889044d1fc0f0c7e3882df0008157ced4915
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294628"
---
# <a name="use-hubs-in-opno-locsignalr-for-aspnet-core"></a>Verwenden von Hubs in SignalR für ASP.net Core

Von [Rachel Appel](https://twitter.com/rachelappel) und [Kevin Griffin](https://twitter.com/1kevgriff)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(Vorgehensweise zum herunterladen)](xref:index#how-to-download-a-sample)

## <a name="what-is-a-opno-locsignalr-hub"></a>Was ist ein SignalR Hub?

Mit der SignalR Hubs-API können Sie Methoden auf verbundenen Clients vom Server aus aufzurufen. Im Servercode definieren Sie Methoden, die vom Client aufgerufen werden. Im Client Code definieren Sie Methoden, die vom Server aufgerufen werden. SignalR kümmert sich um alles hinter den Kulissen, das die Kommunikation zwischen Client und Server und Server-zu-Client-Kommunikation ermöglicht.

## <a name="configure-opno-locsignalr-hubs"></a>Konfigurieren von SignalR Hubs

Die SignalR Middleware erfordert einige Dienste, die durch Aufrufen von `services.AddSignalR`konfiguriert werden.

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

::: moniker range=">= aspnetcore-3.0"

Wenn Sie einer ASP.net Core-App SignalR-Funktionalität hinzufügen, richten Sie die Routen ein SignalR, indem Sie `endpoint.MapHub` im `Startup.Configure` Rückruf der `app.UseEndpoints` Methode aufrufen.

```csharp
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/chathub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Wenn Sie einer ASP.net Core-App SignalR-Funktionalität hinzufügen, richten Sie SignalR Routen durch Aufrufen von `app.UseSignalR` in der `Startup.Configure`-Methode ein.

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

::: moniker-end

## <a name="create-and-use-hubs"></a>Erstellen und Verwenden von Hubs

Erstellen Sie einen Hub, indem Sie eine Klasse deklarieren, die von `Hub`erbt, und fügen Sie Ihr öffentliche Methoden hinzu. Clients können Methoden aufzurufen, die als `public`definiert sind.

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

Sie können einen Rückgabetyp und Parameter, einschließlich komplexer Typen und Arrays, wie in jeder beliebigen C# Methode angeben. SignalR behandelt die Serialisierung und Deserialisierung komplexer Objekte und Arrays in ihren Parametern und Rückgabe Werten.

> [!NOTE]
> Hubs sind flüchtig:
>
> * Speichern Sie den Zustand nicht in einer Eigenschaft der Hub-Klasse. Jeder Hub-Methodenaufrufe wird auf einer neuen Hub-Instanz ausgeführt.
> * Verwenden Sie `await`, wenn Sie asynchrone Methoden aufrufen, die vom Hub abhängen, der aktiv bleibt. Beispielsweise kann eine Methode wie `Clients.All.SendAsync(...)` fehlschlagen, wenn Sie ohne `await` aufgerufen wird und die Hub-Methode abgeschlossen ist, bevor `SendAsync` abgeschlossen ist.

## <a name="the-context-object"></a>Das Kontext Objekt

Die `Hub`-Klasse verfügt über eine `Context`-Eigenschaft mit den folgenden Eigenschaften, die Informationen über die Verbindung enthalten:

| Die Eigenschaften- | Beschreibung |
| ------ | ----------- |
| `ConnectionId` | Ruft die eindeutige ID für die Verbindung ab, die von SignalRzugewiesen wird. Es gibt eine Verbindungs-ID für jede Verbindung.|
| `UserIdentifier` | Ruft den [Benutzer Bezeichner](xref:signalr/groups)ab. Standardmäßig verwendet SignalR die `ClaimTypes.NameIdentifier` aus der `ClaimsPrincipal`, die der Verbindung zugeordnet ist, als Benutzer Bezeichner. |
| `User` | Ruft den `ClaimsPrincipal` ab, der dem aktuellen Benutzer zugeordnet ist. |
| `Items` | Ruft eine Schlüssel-/Wert-Auflistung ab, die verwendet werden kann, um Daten innerhalb des Gültigkeits Bereichs dieser Verbindung freizugeben. Die Daten können in dieser Sammlung gespeichert werden, und Sie werden für die Verbindung über verschiedene hubmethoden Aufrufe beibehalten. |
| `Features` | Ruft die Auflistung der Funktionen ab, die für die Verbindung verfügbar sind. Diese Sammlung wird in den meisten Szenarien nicht benötigt, sodass Sie noch nicht ausführlich dokumentiert wird. |
| `ConnectionAborted` | Ruft ein `CancellationToken` ab, das benachrichtigt, wenn die Verbindung abgebrochen wird. |

`Hub.Context` enthält auch die folgenden Methoden:

| -Methode | Beschreibung |
| ------ | ----------- |
| `GetHttpContext` | Gibt die `HttpContext` für die Verbindung zurück, oder `null`, wenn die Verbindung nicht mit einer HTTP-Anforderung verknüpft ist. Für http-Verbindungen können Sie diese Methode verwenden, um Informationen wie HTTP-Header und Abfrage Zeichenfolgen zu erhalten. |
| `Abort` | Bricht die Verbindung ab. |

## <a name="the-clients-object"></a>Das Client-Objekt

Die `Hub`-Klasse verfügt über eine `Clients`-Eigenschaft, die die folgenden Eigenschaften für die Kommunikation zwischen Server und Client enthält:

| Die Eigenschaften- | Beschreibung |
| ------ | ----------- |
| `All` | Ruft eine Methode auf allen verbundenen Clients auf. |
| `Caller` | Ruft eine Methode auf dem Client auf, der die Hub-Methode aufgerufen hat. |
| `Others` | Ruft eine Methode auf allen verbundenen Clients mit Ausnahme des Clients auf, der die Methode aufgerufen hat. |

`Hub.Clients` enthält auch die folgenden Methoden:

| -Methode | Beschreibung |
| ------ | ----------- |
| `AllExcept` | Ruft eine Methode auf allen verbundenen Clients mit Ausnahme der angegebenen Verbindungen auf. |
| `Client` | Ruft eine Methode auf einem bestimmten verbundenen Client auf. |
| `Clients` | Ruft eine Methode auf bestimmten verbundenen Clients auf. |
| `Group` | Ruft eine Methode für alle Verbindungen in der angegebenen Gruppe auf.  |
| `GroupExcept` | Ruft eine Methode für alle Verbindungen in der angegebenen Gruppe mit Ausnahme der angegebenen Verbindungen auf. |
| `Groups` | Ruft eine Methode für mehrere Gruppen von Verbindungen auf.  |
| `OthersInGroup` | Ruft eine Methode für eine Gruppe von Verbindungen auf, ausgenommen des Clients, der die Hub-Methode aufgerufen hat.  |
| `User` | Ruft eine Methode für alle Verbindungen auf, die einem bestimmten Benutzer zugeordnet sind. |
| `Users` | Ruft für alle Verbindungen, die den angegebenen Benutzern zugeordnet sind, eine Methode auf. |

Jede Eigenschaft oder Methode in den vorangehenden Tabellen gibt ein-Objekt mit einer `SendAsync`-Methode zurück. Mit der `SendAsync`-Methode können Sie den Namen und die Parameter der aufzurufenden Client Methode angeben.

## <a name="send-messages-to-clients"></a>Senden von Nachrichten an Clients

Um Aufrufe an bestimmte Clients durchführen zu können, verwenden Sie die Eigenschaften des `Clients` Objekts. Im folgenden Beispiel gibt es drei hubmethoden:

* `SendMessage` sendet mithilfe `Clients.All`eine Nachricht an alle verbundenen Clients.
* `SendMessageToCaller` sendet eine Nachricht mithilfe `Clients.Caller`an den Aufrufer zurück.
* `SendMessageToGroups` sendet eine Nachricht an alle Clients in der `SignalR Users` Gruppe.

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a>Stark typisierte Hubs

Ein Nachteil der Verwendung von `SendAsync` besteht darin, dass eine magische Zeichenfolge verwendet wird, um die aufzurufende Client Methode anzugeben. Dadurch bleibt der Code für Laufzeitfehler geöffnet, wenn der Methodenname falsch geschrieben wurde oder auf dem Client fehlt.

Eine Alternative zur Verwendung von `SendAsync` besteht darin, die `Hub` mit <xref:Microsoft.AspNetCore.SignalR.Hub%601>stark einzugeben. Im folgenden Beispiel wurden die `ChatHub` Client Methoden in eine Schnittstelle mit dem Namen `IChatClient`extrahiert.

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

Diese Schnittstelle kann verwendet werden, um das vorangehende `ChatHub` Beispiel zu umgestalten.

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

Die Verwendung von `Hub<IChatClient>` ermöglicht die Überprüfung der Client Methoden über die Kompilierzeit. Dies verhindert Probleme, die durch die Verwendung von magischen Zeichen folgen verursacht werden, da `Hub<T>` nur den Zugriff auf die in der Schnittstelle definierten Methoden bereitstellen kann.

Durch die Verwendung eines stark typisierten `Hub<T>` wird die Verwendung `SendAsync`deaktiviert. Alle für die Schnittstelle definierten Methoden können weiterhin als asynchron definiert werden. Tatsächlich sollte jede dieser Methoden eine `Task`zurückgeben. Da es sich um eine Schnittstelle handelt, verwenden Sie das `async`-Schlüsselwort nicht. Beispiel:

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> Das `Async` Suffix wird nicht aus dem Methodennamen entfernt. Wenn die Client Methode nicht mit `.on('MyMethodAsync')`definiert ist, sollten Sie `MyMethodAsync` nicht als Namen verwenden.

## <a name="change-the-name-of-a-hub-method"></a>Ändern des Namens einer Hub-Methode

Standardmäßig ist ein serverhub-Methodenname der Name der .NET-Methode. Sie können jedoch das [hubmethodname](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) -Attribut verwenden, um diesen Standardwert zu ändern und manuell einen Namen für die Methode anzugeben. Der Client sollte beim Aufrufen der-Methode anstelle des .NET-Methoden namens diesen Namen verwenden.

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a>Behandeln von Ereignissen für eine Verbindung

Die SignalR Hubs-API stellt die `OnConnectedAsync` und `OnDisconnectedAsync` virtuellen Methoden zum Verwalten und Nachverfolgen von Verbindungen bereit. Überschreiben Sie die virtuelle `OnConnectedAsync`-Methode, um Aktionen auszuführen, wenn ein Client eine Verbindung mit dem Hub herstellt, z. b. das Hinzufügen zu einer Gruppe

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

Überschreiben Sie die virtuelle `OnDisconnectedAsync`-Methode, um Aktionen auszuführen, wenn ein Client die Verbindung trennt. Wenn der Client die Verbindung absichtlich trennt (z. b. durch Aufrufen von `connection.stop()`), wird der `exception` Parameter `null`. Wenn der Client jedoch aufgrund eines Fehlers (z. b. eines Netzwerk Fehlers) getrennt wird, enthält der `exception` Parameter eine Ausnahme, die den Fehler beschreibt.

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

[!INCLUDE[](~/includes/connectionid-signalr.md)]

## <a name="handle-errors"></a>Behandeln von Fehlern

Ausnahmen, die in ihren hubmethoden ausgelöst werden, werden an den Client gesendet, der die Methode aufgerufen hat. Auf dem JavaScript-Client gibt die `invoke`-Methode eine [JavaScript-Zusage](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises)zurück. Wenn der Client einen Fehler mit einem Handler empfängt, der mithilfe von `catch`an die Zusage angefügt ist, wird er aufgerufen und als JavaScript-`Error` Objekt übermittelt.

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

Wenn Ihr Hub eine Ausnahme auslöst, werden die Verbindungen nicht geschlossen. Standardmäßig gibt SignalR eine generische Fehlermeldung an den Client zurück. Beispiel:

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

Unerwartete Ausnahmen enthalten häufig vertrauliche Informationen, wie z. b. den Namen eines Datenbankservers in einer Ausnahme, die ausgelöst wird, wenn die Datenbankverbindung fehlschlägt. SignalR diese detaillierten Fehlermeldungen nicht standardmäßig als Sicherheitsmaßnahme verfügbar machen. Weitere Informationen dazu, warum Ausnahme Details unterdrückt werden, finden Sie im [Artikel Sicherheitsüberlegungen](xref:signalr/security#exceptions) .

Wenn Sie eine Ausnahme Bedingung haben, die Sie an den Client weiter *geben* möchten, können Sie die `HubException`-Klasse verwenden. Wenn Sie eine `HubException` aus der Hub-Methode auslösen, **sendet SignalR die** gesamte Nachricht unverändert an den Client.

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> SignalR sendet nur die `Message`-Eigenschaft der Ausnahme an den Client. Die Stapel Überwachung und andere Eigenschaften der Ausnahme sind für den Client nicht verfügbar.

## <a name="related-resources"></a>Weitere Informationen

* [Einführung in ASP.net Core SignalR](xref:signalr/introduction)
* [JavaScript-Client](xref:signalr/javascript-client)
* [Veröffentlichen in Azure](xref:signalr/publish-to-azure-web-app)

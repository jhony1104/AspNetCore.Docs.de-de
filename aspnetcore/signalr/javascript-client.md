---
title: ASP.net Core SignalR JavaScript-Client
author: bradygaster
description: Übersicht über ASP.net Core SignalR JavaScript-Client.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: eaf737642cdbd7ab2b1b5c16538b47a70cddd332
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354695"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a>ASP.net Core SignalR JavaScript-Client

Von [Rachel Appel](https://twitter.com/rachelappel)

Die ASP.net Core SignalR JavaScript-Client Bibliothek ermöglicht es Entwicklern, serverseitigen Hub-Code aufzurufen.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="install-the-opno-locsignalr-client-package"></a>Installieren des SignalR-Client Pakets

Die SignalR JavaScript-Client Bibliothek wird als [NPM](https://www.npmjs.com/) -Paket bereitgestellt. Wenn Sie Visual Studio verwenden, führen Sie `npm install` aus der **-Paket-Manager-Konsole** während Sie sich in den Stammordner. Führen Sie für Visual Studio Code, den Befehl in der **integriertes Terminal**.

::: moniker range=">= aspnetcore-3.0"

  ```console
  npm init -y
  npm install @microsoft/signalr
  ```

Npm installiert den Inhalt des Pakets in der *"node_modules"\\@microsoft\signalr\dist\browser* Ordner. Erstellen Sie einen neuen Ordner namens *Signalr* unter der *"Wwwroot"\\Lib* Ordner. Kopieren der *signalr.js* -Datei in die *Wwwroot\lib\signalr* Ordner.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

  ```console
  npm init -y
  npm install @aspnet/signalr
  ```

Npm installiert den Inhalt des Pakets in der *"node_modules"\\@aspnet\signalr\dist\browser* Ordner. Erstellen Sie einen neuen Ordner namens *Signalr* unter der *"Wwwroot"\\Lib* Ordner. Kopieren der *signalr.js* -Datei in die *Wwwroot\lib\signalr* Ordner.

::: moniker-end

## <a name="use-the-opno-locsignalr-javascript-client"></a>Verwenden des SignalR JavaScript-Clients

Verweisen Sie im `<script>`-Element auf den SignalR JavaScript-Client.

```html
<script src="~/lib/signalr/signalr.js"></script>
```

## <a name="connect-to-a-hub"></a>Verbinden mit einem hub

Der folgende Code erstellt und startet eine Verbindung. Die Hub-Name ist Groß-/Kleinschreibung.

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a>Cross-Origin-Verbindungen

Browser werden in der Regel Verbindungen aus der gleichen Domäne wie die angeforderte Seite laden. Es gibt jedoch Situationen, wenn eine Verbindung mit einer anderen Domäne erforderlich ist.

Um zu verhindern, dass eine schädliche Website sensible Daten von einem anderen Standort lesen [Cross-Origin-Verbindungen](xref:security/cors) sind standardmäßig deaktiviert. Um eine cors-Anforderung zu ermöglichen, aktivieren Sie ihn in das `Startup` Klasse.

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>Aufrufen von Hub-Methoden von client

JavaScript-Clients rufen Sie öffentliche Methoden für Hubs über die [Aufrufen](/javascript/api/%40aspnet/signalr/hubconnection#invoke) Methode der [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection). Die `invoke` -Methode akzeptiert zwei Argumente:

* Der Name der hubmethode. Im folgenden Beispiel wird der Name der Methode auf dem Hub `SendMessage`.
* In der hubmethode definierten Argumente. Im folgenden Beispiel wird der Name des Arguments `message`. Im Beispielcode wird eine Pfeil Funktions Syntax verwendet, die in den aktuellen Versionen aller wichtigen Browser mit Ausnahme von Internet Explorer unterstützt wird.

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> Wenn Sie Azure SignalR Service im *Server losen Modus*verwenden, können Sie keine hubmethoden von einem Client aus abrufen. Weitere Informationen finden Sie in der [Dokumentation zumSignalR-Dienst](/azure/azure-signalr/signalr-concept-serverless-development-config).

Die `invoke`-Methode gibt eine JavaScript- [Zusage](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)zurück. Die `Promise` wird mit dem Rückgabewert (sofern vorhanden) aufgelöst, wenn die-Methode auf dem Server zurückgibt. Wenn die-Methode auf dem Server einen Fehler auslöst, wird der `Promise` mit der Fehlermeldung abgelehnt. Verwenden Sie die Methoden `then` und `catch` für die `Promise` selbst, um diese Fälle (oder `await` Syntax) zu verarbeiten.

Die `send`-Methode gibt einen JavaScript-`Promise`zurück. Die `Promise` wird aufgelöst, wenn die Nachricht an den Server gesendet wurde. Wenn beim Senden der Nachricht ein Fehler auftritt, wird der `Promise` mit der Fehlermeldung abgelehnt. Verwenden Sie die Methoden `then` und `catch` für die `Promise` selbst, um diese Fälle (oder `await` Syntax) zu verarbeiten.

> [!NOTE]
> Die Verwendung von `send` wartet nicht, bis der Server die Nachricht empfangen hat. Folglich ist es nicht möglich, Daten oder Fehler vom Server zurückzugeben.

## <a name="call-client-methods-from-hub"></a>Rufen Sie Client-Methoden von Hub-Instanz

Zum Empfangen von Nachrichten aus dem Hub Definieren einer Methode mit dem [auf](/javascript/api/%40aspnet/signalr/hubconnection#on) Methode der `HubConnection`.

* Der Name des Client-JavaScript-Methode. Im folgenden Beispiel wird der Name der Methode `ReceiveMessage`.
* Argumente übergibt der Hub, an die Methode. Im folgenden Beispiel wird den Wert des Arguments `message`.

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

Der vorangehende Code in `connection.on` ausgeführt wird, wenn Sie serverseitigen Code aufruft, mit der [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) Methode.

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR bestimmt, welche Client Methode aufgerufen werden soll, indem der Methodenname und die in `SendAsync` und `connection.on`definierten Argumente abgeglichen werden.

> [!NOTE]
> Rufen Sie als bewährte Methode, die [starten](/javascript/api/%40aspnet/signalr/hubconnection#start) Methode für die `HubConnection` nach `on`. Dadurch wird sichergestellt, dass die Handler registriert sind, bevor alle Nachrichten empfangen werden.

## <a name="error-handling-and-logging"></a>Fehlerbehandlung und Protokollierung

Kette eine `catch` Methode am Ende der `start` Methode zum Behandeln von clientseitigen Fehlern. Verwendung `console.error` zu Ausgabefehler in der Browserkonsole.

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

Einrichten von clientseitigen Protokollierung, Ablaufverfolgung durch Übergeben einer Protokollierung und der Typ des Ereignisses zu protokollieren, wenn die Verbindung hergestellt wird. Nachrichten werden mit der angegebenen Ebene oder höher protokolliert. Verfügbaren Protokollebenen lauten wie folgt aus:

* `signalR.LogLevel.Error` &ndash; Fehlermeldungen. Protokolle `Error` nur Nachrichten.
* `signalR.LogLevel.Warning` &ndash; Warnmeldungen zu potenziellen Fehlern. Protokolle `Warning`, und `Error` Nachrichten.
* `signalR.LogLevel.Information` &ndash; Statusmeldungen, die ohne Fehler. Protokolle `Information`, `Warning`, und `Error` Nachrichten.
* `signalR.LogLevel.Trace` &ndash; Ablaufverfolgungsmeldungen Sie aus. Protokolliert alle Elemente einschließlich Daten, die zwischen Hub und dem Client übertragen.

Verwenden der ["configurelogging"](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) Methode [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) so konfigurieren Sie die Protokollebene. Nachrichten werden in der Browserkonsole protokolliert.

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>Verbinden von clients

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Automatisch wiederherstellen der Verbindung

Der JavaScript-Client für SignalR kann so konfiguriert werden, dass er mit der `withAutomaticReconnect`-Methode auf [hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)automatisch erneut eine Verbindung herstellt. Standardmäßig wird die Verbindung nicht automatisch wieder hergestellt.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Ohne Parameter konfiguriert `withAutomaticReconnect()` den Client so, dass 0, 2, 10 und 30 Sekunden gewartet wird, bevor versucht wird, jeden erneuten Verbindungsversuch durchzusetzen, wobei nach vier fehlgeschlagenen Versuchen angehalten wird.

Vor dem Starten eines erneuten Verbindungsversuchs wechselt der `HubConnection` in den `HubConnectionState.Reconnecting`-Zustand und löst seine `onreconnecting` Rückrufe aus, anstatt in den `Disconnected` Zustand zu wechseln und seine `onclose` Rückrufe wie eine `HubConnection` auszulösen, ohne dass eine automatische erneute Verbindung konfiguriert wird. Dies bietet die Möglichkeit, die Benutzer zu warnen, dass die Verbindung unterbrochen wurde, und Benutzeroberflächen Elemente zu deaktivieren.

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Wenn der Client innerhalb der ersten vier Versuche erfolgreich erneut eine Verbindung herstellt, wechselt der `HubConnection` zurück in den `Connected` Zustand und löst seine `onreconnected` Rückrufe aus. Dies bietet die Möglichkeit, den Benutzern mitzuteilen, dass die Verbindung wieder hergestellt wurde.

Da die Verbindung mit dem Server völlig neu ist, wird eine neue `connectionId` für den `onreconnected` Rückruf bereitgestellt.

> [!WARNING]
> Der `connectionId` Parameter des `onreconnected` Rückrufs ist nicht definiert, wenn die `HubConnection` zum über [springen der Aushandlung](xref:signalr/configuration#configure-client-options)konfiguriert wurde.

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()` die `HubConnection` nicht so konfigurieren, dass anfängliche Start Fehler erneut auftreten, müssen Start Fehler manuell behandelt werden:

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("connected");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

Wenn der Client nicht innerhalb der ersten vier Versuche erneut eine Verbindung herstellt, wechselt der `HubConnection` in den `Disconnected` Zustand und auslöst seine [OnClose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) -Rückrufe. Dies bietet die Möglichkeit, den Benutzern mitzuteilen, dass die Verbindung dauerhaft verloren gegangen ist, und es wird empfohlen, die Seite zu aktualisieren:

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Um eine benutzerdefinierte Anzahl von Wiederholungs versuchen zu konfigurieren, bevor die Verbindung getrennt wird, oder um die Zeit für die Wiederherstellung der Verbindung zu ändern, nimmt `withAutomaticReconnect` ein Array von Zahlen an, das die Wartezeit in Millisekunden angibt, die vor dem Start der einzelnen Wiederholungs Versuche

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

Im vorherigen Beispiel wird der `HubConnection` so konfiguriert, dass nach dem Verlust der Verbindung erneut versucht wird, die Verbindung herzustellen. Dies gilt auch für die Standardkonfiguration.

Wenn der erste Wiederholungsversuch fehlschlägt, wird der zweite Wiederholungsversuch auch sofort gestartet, anstatt zwei Sekunden zu warten, wie in der Standardkonfiguration.

Wenn der zweite Wiederholungsversuch fehlschlägt, wird der dritte Versuch der erneuten Verbindungs Herstellung in 10 Sekunden gestartet. Dies entspricht der Standardkonfiguration.

Das benutzerdefinierte Verhalten weicht dann erneut vom Standardverhalten ab, indem nach dem dritten Wiederholungsversuch beendet wird, anstatt einen weiteren Wiederholungsversuch in weiteren 30 Sekunden auszuführen, wie in der Standardkonfiguration.

Wenn Sie noch mehr Kontrolle über die zeitliche Steuerung und die Anzahl der automatischen Wiederholungs Versuche benötigen, akzeptiert `withAutomaticReconnect` ein Objekt, das die `IRetryPolicy`-Schnittstelle implementiert, die über eine einzige Methode mit dem Namen `nextRetryDelayInMilliseconds`verfügt.

`nextRetryDelayInMilliseconds` nimmt ein einzelnes Argument mit dem Typ `RetryContext`. Der `RetryContext` verfügt über drei Eigenschaften: `previousRetryCount`, `elapsedMilliseconds` und `retryReason` die eine `number`, eine `number` bzw. eine `Error` sind. Vor dem ersten Versuch, die Verbindung wiederherzustellen, sind sowohl `previousRetryCount` als auch `elapsedMilliseconds` 0 (null), und die `retryReason` ist der Fehler, der bewirkt hat, dass die Verbindung unterbrochen wurde. Nach jedem fehlgeschlagenen Wiederholungsversuch werden `previousRetryCount` um eins inkrementiert, `elapsedMilliseconds` entsprechend der Zeitspanne in Millisekunden, die zum erneuten Herstellen der Verbindung aufgewendet wurde, aktualisiert, und die `retryReason` ist der Fehler, durch den der letzte Wiederholungsversuch für die Verbindung verursacht wurde.

`nextRetryDelayInMilliseconds` muss entweder eine Zahl zurückgeben, die die Anzahl der Millisekunden angibt, die vor dem nächsten erneuten Verbindungsversuch gewartet werden soll, oder `null`, wenn die `HubConnection` die erneute Verbindungs Herstellung abbrechen soll.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
            if (retryContext.elapsedMilliseconds < 60000) {
                // If we've been reconnecting for less than 60 seconds so far,
                // wait between 0 and 10 seconds before the next reconnect attempt.
                return Math.random() * 10000;
            } else {
                // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
                return null;
            }
        }
    })
    .build();
```

Alternativ dazu können Sie auch Code schreiben, mit dem der Client manuell erneut eine Verbindung herstellt, wie in [Manuelles Wiederherstellen der Verbindung](#manually-reconnect)gezeigt.

::: moniker-end

### <a name="manually-reconnect"></a>Verbindung manuell wiederherstellen

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Vor 3,0 wird die Verbindung des JavaScript-Clients für SignalR nicht automatisch wieder hergestellt. Sie müssen Code schreiben, die den Client manuell neu verbunden wird.

::: moniker-end

Der folgende Code veranschaulicht einen typischen manuellen Verbindungs Ansatz:

1. Eine Funktion (in diesem Fall die `start` Funktion) wird erstellt, um die Verbindung zu starten.
1. Rufen Sie die `start` -Funktion in der Verbindungs `onclose` -Ereignishandler.

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

Eine Implementierung unter realen Bedingungen würde ein Exponentielles Backoff verwendet, oder Wiederholen Sie eine angegebene Anzahl von Malen, bevor aufgegeben wird.

## <a name="additional-resources"></a>Weitere Ressourcen

* [JavaScript-API-Referenz](/javascript/api/?view=signalr-js-latest)
* [JavaScript-tutorial](xref:tutorials/signalr)
* [Webpack- und TypeScript-tutorial](xref:tutorials/signalr-typescript-webpack)
* [Hubs](xref:signalr/hubs)
* [.NET-Client](xref:signalr/dotnet-client)
* [Veröffentlichen in Azure](xref:signalr/publish-to-azure-web-app)
* [Cross-Origin Requests (CORS)](xref:security/cors)
* [Server lose Dokumentation zu Azure SignalR Service](/azure/azure-signalr/signalr-concept-serverless-development-config)

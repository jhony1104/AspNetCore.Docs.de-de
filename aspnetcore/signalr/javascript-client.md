---
title: ASP.net Core SignalR JavaScript-Client
author: bradygaster
description: Übersicht über ASP.net Core SignalR JavaScript-Client.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: 966e8e171752edb230f6da82203fd901b0fdeaab
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768941"
---
# <a name="aspnet-core-signalr-javascript-client"></a>ASP.net Core SignalR JavaScript-Client

Von [Rachel Appel](https://twitter.com/rachelappel)

Die ASP.net Core SignalR JavaScript-Client Bibliothek ermöglicht es Entwicklern, serverseitigen Hub-Code aufzurufen.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="install-the-signalr-client-package"></a>Installieren des SignalR Client Pakets

Die SignalR JavaScript-Client Bibliothek wird als [NPM](https://www.npmjs.com/) -Paket bereitgestellt. In den folgenden Abschnitten werden verschiedene Möglichkeiten zum Installieren der-Client Bibliothek erläutert.

### <a name="install-with-npm"></a>Installieren mit NPM

Wenn Sie Visual Studio verwenden, führen Sie die folgenden Befehle über die **Paket-Manager-Konsole** im Stamm Ordner aus. Führen Sie für Visual Studio Code die folgenden Befehle über das **integrierte Terminal**aus.

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

NPM installiert den Paket Inhalt im Ordner *"\\ node_modules* ". Erstellen Sie einen neuen Ordner mit dem Namen *signalr* unter dem Ordner *\\wwwroot lib* . Kopieren Sie die Datei " *signalr. js* " in den Ordner " *wwwroot\lib\signalr* ".

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

NPM installiert den Paket Inhalt im Ordner *"\\ node_modules* ". Erstellen Sie einen neuen Ordner mit dem Namen *signalr* unter dem Ordner *\\wwwroot lib* . Kopieren Sie die Datei " *signalr. js* " in den Ordner " *wwwroot\lib\signalr* ".

::: moniker-end

Verweisen Sie SignalR auf den JavaScript- `<script>` Client im-Element. Beispiel:

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a>Verwenden eines Content Delivery Network (CDN)

Wenn Sie die Client Bibliothek ohne die erforderliche Komponente für NPM verwenden möchten, verweisen Sie auf eine CDN-gehostete Kopie der Client Bibliothek. Beispiel:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

Die Client Bibliothek ist unter den folgenden CDNs verfügbar:

::: moniker range=">= aspnetcore-3.0"

* [cdnjs verfügbar](https://cdnjs.com/libraries/microsoft-signalr)
* [jsdelivr](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [unpkg](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [cdnjs verfügbar](https://cdnjs.com/libraries/aspnet-signalr)
* [jsdelivr](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [unpkg](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a>Installieren mit Libman

[Libman](xref:client-side/libman/index) kann verwendet werden, um bestimmte Client Bibliotheksdateien aus der CDN-gehosteten Client Bibliothek zu installieren. Fügen Sie z. b. nur die minierte JavaScript-Datei zum Projekt hinzu. Weitere Informationen zu diesem Ansatz finden [Sie unter hinzu SignalR fügen der Client Bibliothek](xref:tutorials/signalr#add-the-signalr-client-library).

## <a name="connect-to-a-hub"></a>Herstellen einer Verbindung mit einem Hub

Mit dem folgenden Code wird eine Verbindung erstellt und gestartet. Der Name des Hubs wird nicht beachtet.

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a>Ursprungs übergreifende Verbindungen

Normalerweise laden Browser Verbindungen aus derselben Domäne wie die angeforderte Seite. Es gibt jedoch Situationen, in denen eine Verbindung mit einer anderen Domäne erforderlich ist.

Um zu verhindern, dass ein böswilliger Standort vertrauliche Daten von einem anderen Standort liest, sind [Ursprungs übergreifende Verbindungen](xref:security/cors) standardmäßig deaktiviert. Um eine Ursprungs übergreifende Anforderung zuzulassen, aktivieren Sie Sie in der `Startup` -Klasse.

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>Hub-Methoden vom Client abrufen

JavaScript-Clients rufen öffentliche Methoden für Hubs über die [Aufruf Methode](/javascript/api/%40aspnet/signalr/hubconnection#invoke) von [hubconnection](/javascript/api/%40aspnet/signalr/hubconnection)auf. Die `invoke` -Methode akzeptiert zwei Argumente:

* Der Name der Hub-Methode. Im folgenden Beispiel ist `SendMessage`der Methodenname auf dem Hub.
* Alle Argumente, die in der Hub-Methode definiert sind. Im folgenden Beispiel lautet `message`der Argument Name. Im Beispielcode wird eine Pfeil Funktions Syntax verwendet, die in den aktuellen Versionen aller wichtigen Browser mit Ausnahme von Internet Explorer unterstützt wird.

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> Wenn Sie den Azure SignalR -Dienst im *Server losen Modus*verwenden, können Sie keine hubmethoden von einem Client aus abrufen. Weitere Informationen finden Sie in der [ SignalR Dokumentation zum Dienst](/azure/azure-signalr/signalr-concept-serverless-development-config).

Die `invoke` -Methode gibt eine JavaScript- [Zusage](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)zurück. Die `Promise` wird mit dem Rückgabewert (sofern vorhanden) aufgelöst, wenn die-Methode auf dem Server zurückgibt. Wenn die-Methode auf dem Server einen Fehler auslöst, `Promise` wird mit der Fehlermeldung zurückgewiesen. Verwenden Sie `then` die `catch` -Methode und `Promise` die-Methode für den selbst, `await` um diese Fälle (oder Syntax) zu verarbeiten.

Die `send` -Methode gibt ein `Promise`JavaScript zurück. Der `Promise` wird aufgelöst, wenn die Nachricht an den Server gesendet wurde. Wenn beim Senden der Nachricht ein Fehler auftritt, `Promise` wird mit der Fehlermeldung zurückgewiesen. Verwenden Sie `then` die `catch` -Methode und `Promise` die-Methode für den selbst, `await` um diese Fälle (oder Syntax) zu verarbeiten.

> [!NOTE]
> Die `send` Verwendung von wartet nicht, bis der Server die Nachricht empfangen hat. Folglich ist es nicht möglich, Daten oder Fehler vom Server zurückzugeben.

## <a name="call-client-methods-from-hub"></a>Client Methoden aus Hub abrufen

Um Nachrichten vom Hub zu empfangen, definieren Sie mithilfe der [on](/javascript/api/%40aspnet/signalr/hubconnection#on) -Methode von `HubConnection`eine Methode.

* Der Name der JavaScript-Client Methode. Im folgenden Beispiel lautet `ReceiveMessage`der Methodenname.
* Argumente, die der Hub an die Methode übergibt. Im folgenden Beispiel ist `message`der Argument Wert.

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

Der vorangehende Code `connection.on` in wird ausgeführt, wenn der serverseitige Code ihn mithilfe der [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) -Methode aufruft.

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalRbestimmt, welche Client Methode aufgerufen werden soll, indem der Methodenname und die `SendAsync` in `connection.on`und definierten Argumente abgeglichen werden.

> [!NOTE]
> Es wird empfohlen, die [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) -Methode für den `HubConnection` after `on`-Befehl aufzurufen. Dadurch wird sichergestellt, dass ihre Handler registriert werden, bevor Nachrichten empfangen werden.

## <a name="error-handling-and-logging"></a>Fehlerbehandlung und Protokollierung

Verketten `catch` Sie eine Methode an das Ende `start` der Methode, um Client seitige Fehler zu behandeln. Verwenden `console.error` Sie, um Fehler an die Konsole des Browsers auszugeben.

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

Richten Sie die Client seitige Protokoll Ablauf Verfolgung ein, indem Sie eine Protokollierung und einen Ereignistyp an die Protokollierung übergeben, wenn die Verbindung hergestellt wird. Nachrichten werden mit der angegebenen Protokollebene und höher protokolliert. Folgende Protokoll Ebenen sind verfügbar:

* `signalR.LogLevel.Error`&ndash; Fehlermeldungen. Protokolliert `Error` nur Meldungen.
* `signalR.LogLevel.Warning`&ndash; Warnmeldungen zu möglichen Fehlern. Protokolle `Warning`und `Error` Nachrichten.
* `signalR.LogLevel.Information`&ndash; Status Meldungen ohne Fehler. Protokolliert `Information`die `Warning`Meldungen, `Error` und.
* `signalR.LogLevel.Trace`&ndash; Ablauf Verfolgungs Meldungen. Protokolliert alles, einschließlich Daten, die zwischen dem Hub und dem Client transportiert werden.

Verwenden Sie die Methode configure [relogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) für [hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) , um die Protokollebene zu konfigurieren. Nachrichten werden in der Browser Konsole protokolliert.

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>Clients erneut verbinden

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Automatisch wiederherstellen der Verbindung

Der JavaScript-Client SignalR für kann so konfiguriert werden, dass er mithilfe `withAutomaticReconnect` der-Methode auf [hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)automatisch erneut eine Verbindung herstellt. Standardmäßig wird die Verbindung nicht automatisch wieder hergestellt.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Ohne Parameter `withAutomaticReconnect()` konfiguriert den Client so, dass 0, 2, 10 und 30 Sekunden gewartet wird, bevor versucht wird, jeden erneuten Verbindungsversuch durchzusetzen, der nach vier fehlgeschlagenen Versuchen angehalten wird.

Vor dem Starten von Wiederholungs versuchen wechselt der `HubConnection` in den `HubConnectionState.Reconnecting` -Zustand und löst `onreconnecting` seine Rückrufe aus, anstatt in den-Zustand `Disconnected` zu wechseln und seine `onclose` Rückrufe wie eine `HubConnection` auszulösen, ohne dass die automatische Verbindungs Wiederherstellung konfiguriert wurde. Dies bietet die Möglichkeit, die Benutzer zu warnen, dass die Verbindung unterbrochen wurde, und Benutzeroberflächen Elemente zu deaktivieren.

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Wenn der Client innerhalb der ersten vier Versuche erfolgreich erneut eine Verbindung herstellt `HubConnection` , wechselt zurück in den `Connected` -Zustand und löst `onreconnected` seine Rückrufe aus. Dies bietet die Möglichkeit, den Benutzern mitzuteilen, dass die Verbindung wieder hergestellt wurde.

Da die Verbindung mit dem Server vollständig neu ist, wird `connectionId` dem `onreconnected` Rückruf ein neuer bereitgestellt.

> [!WARNING]
> Der `onreconnected` -Parameter `connectionId` des Rückrufs ist nicht definiert `HubConnection` , wenn der zum über [springen der Aushandlung](xref:signalr/configuration#configure-client-options)konfiguriert wurde.

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()`konfiguriert nicht, `HubConnection` um anfängliche Start Fehler zu wiederholen, sodass Start Fehler manuell behandelt werden müssen:

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

Wenn der Client nicht innerhalb der ersten vier Versuche erneut eine Verbindung herstellt, `HubConnection` wechselt in den `Disconnected` -Zustand und auslöst seine [OnClose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) -Rückrufe. Dies bietet die Möglichkeit, den Benutzern mitzuteilen, dass die Verbindung dauerhaft verloren gegangen ist, und es wird empfohlen, die Seite zu aktualisieren:

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Um eine benutzerdefinierte Anzahl von Wiederholungs versuchen zu konfigurieren, bevor die Verbindung getrennt wird, oder um die zeitliche `withAutomaticReconnect` Verbindung wiederherzustellen, akzeptiert ein Array von Zahlen, das die Verzögerung in Millisekunden angibt, die gewartet werden soll, bevor die einzelnen Wiederholungs Versuche gestartet werden.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

Im vorangehenden Beispiel wird der `HubConnection` so konfiguriert, dass nach dem Verlust der Verbindung erneut versucht wird, die Verbindung herzustellen. Dies gilt auch für die Standardkonfiguration.

Wenn der erste Wiederholungsversuch fehlschlägt, wird der zweite Wiederholungsversuch auch sofort gestartet, anstatt zwei Sekunden zu warten, wie in der Standardkonfiguration.

Wenn der zweite Wiederholungsversuch fehlschlägt, wird der dritte Versuch der erneuten Verbindungs Herstellung in 10 Sekunden gestartet. Dies entspricht der Standardkonfiguration.

Das benutzerdefinierte Verhalten weicht dann erneut vom Standardverhalten ab, indem nach dem dritten Wiederholungsversuch beendet wird, anstatt einen weiteren Wiederholungsversuch in weiteren 30 Sekunden auszuführen, wie in der Standardkonfiguration.

Wenn Sie eine noch größere Kontrolle über die zeitliche Steuerung und die Anzahl der automatischen Wiederholungs Versuche `withAutomaticReconnect` wünschen, akzeptiert ein Objekt `IRetryPolicy` , das die-Schnittstelle implementiert, `nextRetryDelayInMilliseconds`die über eine einzige Methode mit dem Namen verfügt.

`nextRetryDelayInMilliseconds`nimmt ein einzelnes Argument mit dem Typ `RetryContext`an. `RetryContext` Verfügt über drei Eigenschaften: `previousRetryCount`, `elapsedMilliseconds` und `retryReason` die sind `number`, `number` `Error` und. Vor dem ersten Versuch, die Verbindung wieder `previousRetryCount` herzustellen `elapsedMilliseconds` , sind sowohl als auch NULL `retryReason` , und der Fehler, der bewirkt hat, dass die Verbindung unterbrochen wurde. Nach jedem fehlgeschlagenen Wiederholungsversuch `previousRetryCount` wird der Wert um eins `elapsedMilliseconds` erhöht. der Wert wird aktualisiert, um die Zeitspanne in Millisekunden wiederherzustellen, die bis zum letzten Verbindungs `retryReason` Versuch aufgewendet wurde. Dies ist der Fehler, der zum Fehlschlagen des letzten Wiederholungs Versuchs geführt hat.

`nextRetryDelayInMilliseconds`muss entweder eine Zahl zurückgeben, die die Anzahl der Millisekunden angibt, die vor dem nächsten erneuten `null` Verbindungsversuch `HubConnection` gewartet werden soll, oder, wenn die Verbindung nicht mehr herstellen soll.

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
> Vor 3,0 wird vom JavaScript-Client für SignalR nicht automatisch eine erneute Verbindung hergestellt. Sie müssen Code schreiben, mit dem der Client manuell erneut eine Verbindung herstellt.

::: moniker-end

Der folgende Code veranschaulicht einen typischen manuellen Verbindungs Ansatz:

1. Eine Funktion (in diesem Fall die `start` Funktion) wird erstellt, um die Verbindung zu starten.
1. Die `start` -Funktion wird im- `onclose` Ereignishandler der Verbindung aufgerufen.

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

Eine reale Implementierung würde einen exponentiellen Backoff verwenden oder eine angegebene Anzahl von Wiederholungen wiederholen, bevor Sie zurückgegeben wird.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [JavaScript-API-Referenz](/javascript/api/?view=signalr-js-latest)
* [JavaScript-Tutorial](xref:tutorials/signalr)
* [WebPack und typescript-Tutorial](xref:tutorials/signalr-typescript-webpack)
* [Hubs](xref:signalr/hubs)
* [.NET-Client](xref:signalr/dotnet-client)
* [Veröffentlichen in Azure](xref:signalr/publish-to-azure-web-app)
* [Cross-Origin-Anforderungen (cors)](xref:security/cors)
* [Azure SignalR -Dienst Server lose Dokumentation](/azure/azure-signalr/signalr-concept-serverless-development-config)

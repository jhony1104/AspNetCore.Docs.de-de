---
title: ASP.NET SignalR Core JavaScript-Client
author: bradygaster
description: Überblick über SignalR ASP.NET Core JavaScript-Client.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: 43b2cacf9f415ec422a00b28246f30c8ad74de29
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440856"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a>ASP.NET SignalR Core JavaScript-Client

Von [Rachel Appel](https://twitter.com/rachelappel)

Die ASP.NET SignalR Core JavaScript-Clientbibliothek ermöglicht Entwicklern den Aufruf von serverseitigem Hubcode.

[Beispielcode anzeigen oder herunterladen](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([downloaden](xref:index#how-to-download-a-sample))

## <a name="install-the-opno-locsignalr-client-package"></a>Installieren SignalR des Clientpakets

Die SignalR JavaScript-Clientbibliothek wird als [npm-Paket](https://www.npmjs.com/) bereitgestellt. In den folgenden Abschnitten werden verschiedene Möglichkeiten zum Installieren der Clientbibliothek beschrieben.

### <a name="install-with-npm"></a>Installieren mit npm

Wenn Sie Visual Studio verwenden, führen Sie die folgenden Befehle aus der **Package Manager-Konsole** aus, während Sie sich im Stammordner befinden. Führen Sie für Visual Studio Code die folgenden Befehle vom **integrierten Terminal**aus aus.

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

npm installiert den Paketinhalt im *Ordner node_modules.\\ * Erstellen Sie einen neuen Ordner mit dem Namen *Signalr* unter dem Ordner *wwwroot\\lib.* Kopieren Sie die Datei *signalr.js* in den Ordner *wwwroot-lib-signalr.*

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

npm installiert den Paketinhalt im *Ordner node_modules.\\ * Erstellen Sie einen neuen Ordner mit dem Namen *Signalr* unter dem Ordner *wwwroot\\lib.* Kopieren Sie die Datei *signalr.js* in den Ordner *wwwroot-lib-signalr.*

::: moniker-end

Verweisen SignalR Sie auf den `<script>` JavaScript-Client im Element. Beispiel:

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a>Verwenden eines Content Delivery Network (CDN)

Um die Clientbibliothek ohne die npm-Voraussetzung zu verwenden, verweisen Sie auf eine von CDN gehostete Kopie der Clientbibliothek. Beispiel:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

Die Clientbibliothek ist auf den folgenden CDNs verfügbar:

::: moniker range=">= aspnetcore-3.0"

* [cdnjs](https://cdnjs.com/libraries/microsoft-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [unpkg](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [cdnjs](https://cdnjs.com/libraries/aspnet-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [unpkg](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a>Installieren mit LibMan

[LibMan](xref:client-side/libman/index) kann verwendet werden, um bestimmte Clientbibliotheksdateien aus der cdN-gehosteten Clientbibliothek zu installieren. Fügen Sie dem Projekt beispielsweise nur die vermählte JavaScript-Datei hinzu. Weitere Informationen zu diesem Ansatz finden Sie unter [Hinzufügen SignalR der Clientbibliothek](xref:tutorials/signalr#add-the-signalr-client-library).

## <a name="connect-to-a-hub"></a>Herstellen einer Verbindung mit einem Hub

Der folgende Code erstellt und startet eine Verbindung. Der Name des Hubs ist nicht berücksichtigt.

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a>Ursprungsübergreifende Verbindungen

In der Regel laden Browser Verbindungen aus derselben Domäne wie die angeforderte Seite. Es gibt jedoch Fälle, in denen eine Verbindung zu einer anderen Domäne erforderlich ist.

Um zu verhindern, dass eine schädliche Site vertrauliche Daten von einer anderen Site liest, sind [ursprungsübergreifende Verbindungen](xref:security/cors) standardmäßig deaktiviert. Um eine ursprungsübergreifende Anforderung zuzulassen, `Startup` aktivieren Sie sie in der Klasse.

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>Call Hub-Methoden vom Client

JavaScript-Clients rufen öffentliche Methoden auf Hubs über die [Aufrufmethode](/javascript/api/%40aspnet/signalr/hubconnection#invoke) der [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection)auf. Die `invoke` Methode akzeptiert zwei Argumente:

* Der Name der Hub-Methode. Im folgenden Beispiel lautet `SendMessage`der Methodenname auf dem Hub .
* Alle in der Hub-Methode definierten Argumente. Im folgenden Beispiel lautet `message`der Argumentname . Der Beispielcode verwendet pfeilfunktionssyntax, die in aktuellen Versionen aller gängigen Browser außer Internet Explorer unterstützt wird.

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> Wenn Sie Azure SignalR Service im *serverlosen Modus*verwenden, können Sie Hubmethoden nicht von einem Client aufrufen. Weitere Informationen finden Sie in der [ SignalR Servicedokumentation](/azure/azure-signalr/signalr-concept-serverless-development-config).

Die `invoke` Methode gibt ein JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)zurück. Der `Promise` wird mit dem Rückgabewert (falls vorhanden) aufgelöst, wenn die Methode auf dem Server zurückgegeben wird. Wenn die Methode auf dem Server `Promise` einen Fehler auslöst, wird die mit der Fehlermeldung zurückgewiesen. Verwenden `then` Sie `catch` die `Promise` und Methoden auf der `await` selbst, um diese Fälle (oder Syntax) zu behandeln.

Die `send` Methode gibt `Promise`ein JavaScript zurück. Der `Promise` wird aufgelöst, wenn die Nachricht an den Server gesendet wurde. Wenn beim Senden der Nachricht `Promise` ein Fehler auftritt, wird der mit der Fehlermeldung zurückgewiesen. Verwenden `then` Sie `catch` die `Promise` und Methoden auf der `await` selbst, um diese Fälle (oder Syntax) zu behandeln.

> [!NOTE]
> Die `send` Verwendung wartet nicht, bis der Server die Nachricht empfangen hat. Daher ist es nicht möglich, Daten oder Fehler vom Server zurückzugeben.

## <a name="call-client-methods-from-hub"></a>Aufrufen von Clientmethoden vom Hub

Um Nachrichten vom Hub zu empfangen, definieren Sie `HubConnection`eine Methode mit der [on-Methode](/javascript/api/%40aspnet/signalr/hubconnection#on) der .

* Der Name der JavaScript-Clientmethode. Im folgenden Beispiel lautet `ReceiveMessage`der Methodenname .
* Argumente, die der Hub an die Methode übergibt. Im folgenden Beispiel lautet `message`der Argumentwert .

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

Der vorherige `connection.on` Code in wird ausgeführt, wenn serverseitiger Code ihn mithilfe der [SendAsync-Methode](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) aufruft.

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalRbestimmt, welche Clientmethode aufruft werden soll, `SendAsync` `connection.on`indem der in und definierte Methodenname und die in definierten Argumente übereinstimmen.

> [!NOTE]
> Als bewährte Methode rufen Sie die `HubConnection` `on` [Startmethode](/javascript/api/%40aspnet/signalr/hubconnection#start) für die after auf. Dadurch wird sichergestellt, dass Ihre Handler registriert sind, bevor Nachrichten empfangen werden.

## <a name="error-handling-and-logging"></a>Fehlerbehandlung und Protokollierung

Verketten `catch` Sie eine Methode `start` an das Ende der Methode, um clientseitige Fehler zu behandeln. Verwenden `console.error` Sie diese Datei, um Fehler an der Konsole des Browsers auszugeben.

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

Richten Sie die clientseitige Protokollablaufverfolgung ein, indem Sie eine Protokollierung und einen Ereignistyp übergeben, um zu protokollieren, wenn die Verbindung hergestellt wird. Nachrichten werden mit der angegebenen Protokollebene und höher protokolliert. Die verfügbaren Protokollebenen sind wie folgt:

* `signalR.LogLevel.Error`&ndash; Fehlermeldungen. Protokolliert `Error` nur Nachrichten.
* `signalR.LogLevel.Warning`&ndash; Warnmeldungen zu möglichen Fehlern. Protokolle `Warning`und `Error` Nachrichten.
* `signalR.LogLevel.Information`&ndash; Statusmeldungen ohne Fehler. Protokolle `Information` `Warning`, `Error` und Nachrichten.
* `signalR.LogLevel.Trace`&ndash; Verfolgen Sie Nachrichten. Protokolliert alles, einschließlich Daten, die zwischen Hub und Client transportiert werden.

Verwenden Sie die [configureLogging-Methode](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) auf [HubConnectionBuilder,](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) um die Protokollebene zu konfigurieren. Nachrichten werden in der Browserkonsole protokolliert.

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>Wiederherstellen von Clients

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Automatische Wiederverbindung

Der JavaScript-Client für SignalR kann so konfiguriert `withAutomaticReconnect` werden, dass die Verbindung mit der Methode auf [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)automatisch wiederhergestellt wird. Die Verbindung wird nicht standardmäßig wiederhergestellt.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Konfiguriert den `withAutomaticReconnect()` Client ohne Parameter so, dass er 0, 2, 10 und 30 Sekunden wartet, bevor jeder erneute Verbindungsversuch versucht wird, und wird nach vier fehlgeschlagenen Versuchen angehalten.

Vor dem Starten von `HubConnection` Wiederverbindungsversuchen `HubConnectionState.Reconnecting` wird der `onreconnecting` in den Zustand übergehen `Disconnected` und seine `onclose` Rückrufe auslösen, `HubConnection` anstatt in den Zustand zu wechseln und seine Rückrufe wie eine ohne automatische Wiederverbindung zu auslösen. Dies bietet die Möglichkeit, Benutzer vor dem Verlust der Verbindung zu warnen und UI-Elemente zu deaktivieren.

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Wenn der Client innerhalb der ersten vier `HubConnection` Versuche erfolgreich `Connected` eine Verbindung `onreconnected` herstellt, wechselt der in den Zustand zurück und feuert seine Rückrufe ab. Dies bietet die Möglichkeit, die Benutzer über die Wiederherzustellen der Verbindung zu informieren.

Da die Verbindung für den Server `connectionId` völlig neu aussieht, wird eine neue für den `onreconnected` Rückruf bereitgestellt.

> [!WARNING]
> Der `onreconnected` Parameter des `connectionId` Rückrufs ist nicht `HubConnection` definiert, wenn der zum Überspringen der [Aushandlung](xref:signalr/configuration#configure-client-options)konfiguriert wurde.

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()`konfiguriert die `HubConnection` nicht, um anfängliche Startfehler erneut zu versuchen, daher müssen Startfehler manuell behandelt werden:

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

Wenn der Client innerhalb der ersten vier Versuche `HubConnection` keine erneute `Disconnected` Verbindung herstellt, wechselt der in den Zustand und feuert seine [Onclose-Rückrufe](/javascript/api/%40aspnet/signalr/hubconnection#onclose) ab. Dies bietet die Möglichkeit, Benutzer darüber zu informieren, dass die Verbindung dauerhaft unterbrochen wurde, und empfehlen, die Seite zu aktualisieren:

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Um eine benutzerdefinierte Anzahl von Wiederverbindungsversuchen zu konfigurieren, bevor `withAutomaticReconnect` das Zeitzeichen für die erneute Verbindung getrennt oder geändert wird, wird ein Array von Zahlen akzeptiert, die die Verzögerung in Millisekunden darstellen, um zu warten, bevor jeder erneute Verbindungsversuch gestartet wird.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

Im vorherigen Beispiel `HubConnection` wird konfiguriert, dass der Versuch sofort nach Verbindungsverlust erneut hergestellt wird. Dies gilt auch für die Standardkonfiguration.

Wenn der erste Wiederherstellungsversuch fehlschlägt, wird der zweite Wiederherstellungsversuch ebenfalls sofort gestartet, anstatt 2 Sekunden wie in der Standardkonfiguration zu warten.

Wenn der zweite Wiederherstellungsversuch fehlschlägt, beginnt der dritte Wiederherstellungsversuch in 10 Sekunden, was wiederum der Standardkonfiguration entspricht.

Das benutzerdefinierte Verhalten weicht dann erneut vom Standardverhalten ab, indem es nach dem dritten Erneutverbindungsversuchsfehler beendet wird, anstatt einen weiteren erneuten Verbindungsversuch in weiteren 30 Sekunden wie in der Standardkonfiguration zu versuchen.

Wenn Sie noch mehr Kontrolle über das Timing und `withAutomaticReconnect` die Anzahl `IRetryPolicy` der automatischen Wiederverbindungsversuche `nextRetryDelayInMilliseconds`wünschen, akzeptiert ein Objekt, das die Schnittstelle implementiert, die über eine einzelne Methode mit dem Namen verfügt.

`nextRetryDelayInMilliseconds`nimmt ein einzelnes `RetryContext`Argument mit dem Typ . Der `RetryContext` hat drei `previousRetryCount` `elapsedMilliseconds` Eigenschaften: `retryReason` , `number`und `number` die `Error` sind a , a bzw. ein. Vor dem ersten erneuten `previousRetryCount` `elapsedMilliseconds` Verbindungsversuch sind beide `retryReason` und Null, und der wird der Fehler sein, der den Verlust der Verbindung verursacht hat. Nach jedem fehlgeschlagenen `previousRetryCount` Wiederholungsversuch wird um `elapsedMilliseconds` eins erhöht, wird aktualisiert, um die Zeit widerzuspiegeln, die bisher in Millisekunden für die erneute Verbindung aufgewendet wurde, und es `retryReason` wird der Fehler sein, der den letzten erneuten Verbindungsversuch verursacht hat.

`nextRetryDelayInMilliseconds`muss entweder eine Zahl zurückgeben, die die Anzahl der Millisekunden darstellt, die vor dem nächsten erneuten Verbindungsversuch gewartet werden soll, oder `null` wenn die `HubConnection` erneute Verbindung beendet werden soll.

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

Alternativ können Sie Code schreiben, der den Client manuell wieder verbindet, wie unter [Manuelle Wiederherstellung der Verbindung](#manually-reconnect)gezeigt.

::: moniker-end

### <a name="manually-reconnect"></a>Manuelle Verbindung wieder herstellen

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Vor 3.0 stellt der JavaScript-Client für SignalR nicht automatisch eine erneute Verbindung her. Sie müssen Code schreiben, der den Client manuell wieder verbindet.

::: moniker-end

Der folgende Code veranschaulicht einen typischen manuellen Wiederverbindungsansatz:

1. Eine Funktion (in diesem `start` Fall die Funktion) wird erstellt, um die Verbindung zu starten.
1. Rufen `start` Sie die Funktion `onclose` im Ereignishandler der Verbindung auf.

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

Eine reale Implementierung würde einen exponentiellen Back-off verwenden oder eine bestimmte Anzahl von Wiederholungen wiederholen, bevor sie aufgibt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [JavaScript-API-Referenz](/javascript/api/?view=signalr-js-latest)
* [JavaScript-Tutorial](xref:tutorials/signalr)
* [WebPack- und TypeScript-Tutorial](xref:tutorials/signalr-typescript-webpack)
* [Hubs](xref:signalr/hubs)
* [.NET-Client](xref:signalr/dotnet-client)
* [Veröffentlichen in Azure](xref:signalr/publish-to-azure-web-app)
* [Ursprungsübergreifende Anforderungen (CORS)](xref:security/cors)
* [Serverlose SignalR Azure Service-Dokumentation](/azure/azure-signalr/signalr-concept-serverless-development-config)

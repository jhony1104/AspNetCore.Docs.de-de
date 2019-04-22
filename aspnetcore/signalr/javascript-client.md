---
title: ASP.NET Core SignalR-JavaScript-client
author: bradygaster
description: Übersicht über ASP.NET Core SignalR JavaScript-Client.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/17/2019
uid: signalr/javascript-client
ms.openlocfilehash: e58015221497a9f962edf9f9fdba7ea3025d7694
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59705603"
---
# <a name="aspnet-core-signalr-javascript-client"></a>ASP.NET Core SignalR-JavaScript-client

Von [Rachel Appel](http://twitter.com/rachelappel)

Die ASP.NET Core SignalR JavaScript-Clientbibliothek ermöglicht Entwicklern von serverseitigen hubcode aufrufen.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="install-the-signalr-client-package"></a>Installieren Sie das Paket der SignalR-client

Die SignalR-JavaScript-Clientbibliothek wird bereitgestellt, als ein [Npm](https://www.npmjs.com/) Paket. Wenn Sie Visual Studio verwenden, führen Sie `npm install` aus der **-Paket-Manager-Konsole** während Sie sich in den Stammordner. Führen Sie für Visual Studio Code, den Befehl in der **integriertes Terminal**.

  ```console
  npm init -y
  npm install @aspnet/signalr
  ```

Npm installiert den Inhalt des Pakets in der *"node_modules"\\@aspnet\signalr\dist\browser* Ordner. Erstellen Sie einen neuen Ordner namens *Signalr* unter der *"Wwwroot"\\Lib* Ordner. Kopieren der *signalr.js* -Datei in die *Wwwroot\lib\signalr* Ordner.

## <a name="use-the-signalr-javascript-client"></a>Verwenden Sie den SignalR JavaScript-client

Verweisen auf den SignalR JavaScript-Client in der `<script>` Element.

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
* In der hubmethode definierten Argumente. Im folgenden Beispiel wird der Name des Arguments `message`. Der Beispielcode verwendet die pfeilfunktionssyntax, die in aktuellen Versionen von allen wichtigen Browsern mit Ausnahme von Internet Explorer unterstützt wird.

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> Bei Verwendung von Azure SignalR Service in *serverlose Modus*, Sie können nicht von einem Client hubmethoden aufrufen. Weitere Informationen finden Sie unter den [SignalR Service-Dokumentation](/azure/azure-signalr/signalr-concept-serverless-development-config).

## <a name="call-client-methods-from-hub"></a>Rufen Sie Client-Methoden von Hub-Instanz

Zum Empfangen von Nachrichten aus dem Hub Definieren einer Methode mit dem [auf](/javascript/api/%40aspnet/signalr/hubconnection#on) Methode der `HubConnection`.

* Der Name des Client-JavaScript-Methode. Im folgenden Beispiel wird der Name der Methode `ReceiveMessage`.
* Argumente übergibt der Hub, an die Methode. Im folgenden Beispiel wird den Wert des Arguments `message`.

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

Der vorangehende Code in `connection.on` ausgeführt wird, wenn Sie serverseitigen Code aufruft, mit der [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) Methode.

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR bestimmt, welche Clientmethode aufrufen, die nach übereinstimmenden Namen der Methode und Argumente, die in definierten `SendAsync` und `connection.on`.

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

### <a name="automatically-reconnect"></a>Automatisch erneut eine Verbindung herstellen

Für SignalR JavaScript-Client konfiguriert werden kann automatisch verbunden werden, mithilfe der `withAutomaticReconnect` Methode [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder). Es wird nicht standardmäßig automatisch wiederherzustellen.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Ohne Parameter `withAutomaticReconnect()` konfiguriert den Client, um zu warten, 0, 2, 10 und 30 Sekunden bzw. jedem Versuch eine erneute Verbindung beenden nach vier fehlgeschlagenen versuchen.

Vor jedem erneuten Verbindungsversuche, die `HubConnection` wird der Übergang zu der `HubConnectionState.Reconnecting` Status und ausgelöst werden die `onreconnecting` Rückrufe anstelle der Übergang in die `Disconnected` Zustand und Auslösen von seine `onclose` Rückrufe wie eine `HubConnection`ohne automatische verbindungswiederherstellung konfiguriert. Dies bietet die Möglichkeit, Benutzer zu warnen, dass die Verbindung unterbrochen wurde und UI-Elemente zu deaktivieren.

```javascript
connection.onreconnecting((error) => {
  console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

  document.getElementById("messageInput").disabled = true;

  const li = document.createElement("li");
  li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
  document.getElementById("messagesList").appendChild(li);
});
```

Wenn der Client erfolgreich in die ersten vier Versuche, erneut eine Verbindung herstellt der `HubConnection` erfolgt ein Wechsel zurück in die `Connected` Status und ausgelöst werden die `onreconnected` Rückrufe. Dies bietet die Möglichkeit, Benutzer zu informieren, die die Verbindung wiederhergestellt wurde.

Da die Verbindung vollständig neu auf dem Server, sieht eine neue `connectionId` bereitgestellt der `onreconnected` Rückruf.

> [!WARNING]
> Die `onreconnected` des Rückrufs `connectionId` Parameter wird nicht definiert sein, wenn die `HubConnection` wurde so konfiguriert, dass [überspringen Aushandlung](xref:signalr/configuration#configure-client-options).

```javascript
connection.onreconnected((connectionId) => {
  console.assert(connection.state === signalR.HubConnectionState.Connected);

  document.getElementById("messageInput").disabled = false;

  const li = document.createElement("li");
  li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
  document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()` Konfigurieren Sie wird nicht die `HubConnection` zum Wiederholen von Fehlern der ersten Start, sodass Fehler beim Starten von manuell verarbeitet werden müssen:

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

Wenn der Client erfolgreich in die ersten vier Versuche, erneut eine Verbindung herstellen nicht die `HubConnection` wird der Übergang in die `Disconnected` Status und ausgelöst werden die [Onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) Rückrufe. Dies bietet die Möglichkeit zum Informieren der Benutzer die Verbindung wurde dauerhaft verloren und wird empfohlen, die Seite zu aktualisieren:

```javascript
connection.onclose((error) => {
  console.assert(connection.state === signalR.HubConnectionState.Disconnected);

  document.getElementById("messageInput").disabled = true;

  const li = document.createElement("li");
  li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
  document.getElementById("messagesList").appendChild(li);
})
```

Um eine benutzerdefinierte Anzahl von verbindungswiederherstellungsversuchen vor dem Trennen der Verbindung zu konfigurieren oder ändern die zeitplanung eine erneute Verbindung `withAutomaticReconnect` akzeptiert ein Array von Zahlen, die, die die Verzögerung in Millisekunden darstellt, die Wartezeit vor dem Starten von jedem Versuch eine erneute Verbindung.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

Im vorhergehende Beispiel konfiguriert die `HubConnection` zu versuchen, Verbindungen, unmittelbar nachdem die Verbindung unterbrochen wird. Dies gilt auch für die Standardkonfiguration.

Wenn eine erneute Verbindung beim erste Versuch fehlschlägt, wird die zweite Versuch für eine erneute Verbindung auch sofort starten, anstatt abzuwarten, 2 Sekunden, wie in der Standardkonfiguration.

Wenn eine erneute Verbindung beim zweite Versuch ein Fehler auftritt, startet der dritte Versuch für eine erneute Verbindung innerhalb von 10 Sekunden, die wiederum wie die Standardkonfiguration.

Das benutzerdefinierte Verhalten dann weicht erneut das Standardverhalten durch Beenden dritte Wiederherstellen der Verbindung versucht haben Fehler statt zu versuchen, eine weitere Verbindung versucht, die in einer anderen 30 Sekunden, wie in der Standardkonfiguration.

Wenn Sie möchten noch mehr Kontrolle über Zeitpunkt und Anzahl automatisch erneut eine Verbindung herzustellen versucht, `withAutomaticReconnect` akzeptiert ein Objekt, das die `IReconnectPolicy` -Schnittstelle, die eine einzige namens Methode `nextRetryDelayInMilliseconds`.

`nextRetryDelayInMilliseconds` akzeptiert zwei Argumente `previousRetryCount` und `elapsedMilliseconds`, welche der beiden Zahlen sind. Vor dem ersten Versuch eine erneute Verbindung sowohl `previousRetryCount` und `elapsedMilliseconds` wird 0 (null) sein. Nach den fehlerhaften Wiederholungsversuchen beim Senden `previousRetryCount` um eins erhöht und `elapsedMilliseconds` die viel Zeit aufgewendet, die bisher in Millisekunden Verbindung entsprechend aktualisiert werden.

`nextRetryDelayInMilliseconds` muss entweder eine Anzahl, der die Anzahl von Millisekunden wartet, bevor der nächste Versuch für eine erneute Verbindung zurückgeben oder `null` Wenn die `HubConnection` beendet werden sollte erneut eine Verbindung herzustellen.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: (previousRetryCount, elapsedMilliseconds) => {
          if (elapsedMilliseconds < 60000) {
            // If we've been reconnecting for less than 60 seconds so far,
            // wait between 0 and 10 seconds before the next reconnect attempt.
            return Math.random() * 10000;
          } else {
            // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
            return null;
          }
        })
    .build();
```

Alternativ können Sie schreiben Code, der den Client manuell, wie in wiederhergestellt [manuell erneut verbinden](#manually-reconnect).

::: moniker-end

### <a name="manually-reconnect"></a>Manuell erneut verbinden

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Vor 3.0 nicht für SignalR JavaScript-Client automatisch erneut eine Verbindung herzustellen. Sie müssen Code schreiben, die den Client manuell neu verbunden wird.

::: moniker-end

Der folgende Code veranschaulicht einen typischen manuellen erneuten Herstellen einer Verbindung Ansatz:

1. Eine Funktion (in diesem Fall die `start` Funktion) wird erstellt, um die Verbindung zu starten.
1. Rufen Sie die `start` -Funktion in der Verbindungs `onclose` -Ereignishandler.

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

Eine Implementierung unter realen Bedingungen würde ein Exponentielles Backoff verwendet, oder Wiederholen Sie eine angegebene Anzahl von Malen, bevor aufgegeben wird. 

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [JavaScript-API-Referenz](/javascript/api/?view=signalr-js-latest)
* [JavaScript-tutorial](xref:tutorials/signalr)
* [Webpack- und TypeScript-tutorial](xref:tutorials/signalr-typescript-webpack)
* [Hubs](xref:signalr/hubs)
* [.NET-Client](xref:signalr/dotnet-client)
* [Veröffentlichen in Azure](xref:signalr/publish-to-azure-web-app)
* [Cross-Origin Requests (CORS)](xref:security/cors)
* [Serverlose Azure SignalR Service-Dokumentation](/azure/azure-signalr/signalr-concept-serverless-development-config)

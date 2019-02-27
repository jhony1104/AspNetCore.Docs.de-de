---
title: ASP.NET Core SignalR-JavaScript-client
author: bradygaster
description: Übersicht über ASP.NET Core SignalR JavaScript-Client.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/14/2018
uid: signalr/javascript-client
ms.openlocfilehash: db9a8bbc8f111728f0827e3639e40785149bf79e
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56899215"
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

Für SignalR JavaScript-Client nicht automatisch erneut eine Verbindung herzustellen. Sie müssen Code schreiben, die den Client manuell neu verbunden wird. Der folgende Code veranschaulicht einen typischen verbindungswiederherstellungs-Ansatz:

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

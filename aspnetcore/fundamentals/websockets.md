---
title: WebSockets-Unterstützung in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie mit WebSockets in ASP.NET beginnen.
monikerRange: '>= aspnetcore-1.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 05/10/2019
uid: fundamentals/websockets
ms.openlocfilehash: bba9cf051deaf57efdd82ca2fb1318fce79bd6cc
ms.sourcegitcommit: e1623d8279b27ff83d8ad67a1e7ef439259decdf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/25/2019
ms.locfileid: "66223218"
---
# <a name="websockets-support-in-aspnet-core"></a>WebSockets-Unterstützung in ASP.NET Core

Von [Tom Dykstra](https://github.com/tdykstra) und [Andrew Stanton-Nurse](https://github.com/anurse)

In diesem Artikel erfahren Sie, wie Sie mit WebSockets in ASP.NET beginnen. Bei [WebSockets](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) handelt es sich um ein Protokoll, das bidirektionale persistente Kommunikationskanäle über TCP-Verbindungen ermöglicht. Es wird in Apps verwendet, die von schneller Kommunikation in Echtzeit profitieren, z.B. Chats, Dashboards und Spiele-Apps.

[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)). Weitere Informationen finden Sie im Abschnitt [Nächste Schritte](#next-steps).

## <a name="prerequisites"></a>Erforderliche Komponenten

* ASP.NET Core 1.1 oder höher
* Jedes Betriebssystem, das ASP.NET Core unterstützt:
  
  * Windows 7/Windows Server 2008 und höher
  * Linux
  * macOS
  
* Wenn die App unter Windows mit IIS ausgeführt wird:

  * Windows 8/Windows Server 2012 und höher
  * IIS 8/IIS 8 Express
  * WebSockets müssen in IIS aktiviert sein (weitere Informationen finden Sie im Abschnitt [Unterstützung für IIS/IIS Express](#iisiis-express-support)).
  
* Wenn die App unter [HTTP.sys](xref:fundamentals/servers/httpsys) ausgeführt wird:

  * Windows 8/Windows Server 2012 und höher

* Weitere Informationen zu unterstützten Browsern finden Sie unter https://caniuse.com/#feat=websockets.

## <a name="when-to-use-websockets"></a>Verwendungszweck von WebSockets

Verwenden Sie WebSockets, um direkt mit einer Socketverbindung zu arbeiten. Verwenden Sie WebSockets beispielsweise für die bestmögliche Leistung bei einem Echtzeitspiel.

[ASP.NET Core SignalR](xref:signalr/introduction) ist eine Bibliothek, die das Hinzufügen von Echtzeitwebfunktionalität zu Apps erleichtert. Sie verwendet wenn möglich immer WebSockets.

## <a name="how-to-use-websockets"></a>So verwenden Sie WebSockets

* Installieren Sie das Paket [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/).
* Konfigurieren Sie die Middleware.
* Akzeptieren Sie Anforderungen von WebSocket.
* Senden Sie Nachrichten, und empfangen Sie diese.

### <a name="configure-the-middleware"></a>Konfigurieren der Middleware

Fügen Sie die WebSockets-Middleware zur `Configure`-Methode der `Startup`-Klasse hinzu.

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](websockets/samples/1.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Sie können die folgenden Einstellungen konfigurieren:

* `KeepAliveInterval`: Legt fest, wie oft Ping-Frames an den Client gesendet werden, um sicherzustellen, dass Proxys die Verbindung aufrechterhalten Der Standardwert beträgt zwei Minuten.
* `ReceiveBufferSize`: Legt die Größe des Puffers fest, der zum Empfang von Daten verwendet wird Fortgeschrittene Benutzer müssen diese Angaben möglicherweise ändern, um die Leistung auf Grundlage der Größe ihrer Daten anzupassen. Der Standardwert ist 4 KB.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Sie können die folgenden Einstellungen konfigurieren:

* `KeepAliveInterval`: Legt fest, wie oft Ping-Frames an den Client gesendet werden, um sicherzustellen, dass Proxys die Verbindung aufrechterhalten Der Standardwert beträgt zwei Minuten.
* `ReceiveBufferSize`: Legt die Größe des Puffers fest, der zum Empfang von Daten verwendet wird Fortgeschrittene Benutzer müssen diese Angaben möglicherweise ändern, um die Leistung auf Grundlage der Größe ihrer Daten anzupassen. Der Standardwert ist 4 KB.
* `AllowedOrigins` – Eine Liste der zulässigen Origin-Headerwerte für WebSocket-Anforderungen. Alle Ursprünge sind standardmäßig zulässig. Weitere Informationen finden Sie unter „Beschränkung von WebSocket-Ursprüngen“ weiter unten.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](websockets/samples/1.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

::: moniker-end

### <a name="accept-websocket-requests"></a>Akzeptieren der Anforderungen von WebSocket

Prüfen Sie zu einem späteren Zeitpunkt im Lebenszyklus einer Anforderung (z.B. später in der `Configure`-Methode oder in einer MVC-Aktion), ob es sich um eine WebSocket-Anforderung handelt, und akzeptieren Sie diese.

Das folgende Beispiel ist ein späterer Auszug der `Configure`-Methode.

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](websockets/samples/1.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

::: moniker-end

WebSocket-Anforderungen können bei jeder URL eingehen. Dieser Beispielcode akzeptiert jedoch nur Anforderungen für `/ws`.

Bei Verwendung eines WebSockets **muss** die Middlewarepipeline während der gesamten Dauer der Verbindung ausgeführt werden. Wenn Sie versuchen, eine WebSocket-Nachricht zu senden oder zu empfangen, nachdem die Middlewarepipeline beendet wurde, erhalten Sie möglicherweise eine Ausnahme wie die folgende:

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

Wenn Sie einen Hintergrunddienst zum Schreiben von Daten an ein WebSocket verwenden, stellen Sie sicher, dass die Middlewarepipeline dauerhaft ausgeführt wird. Verwenden Sie dafür ein <xref:System.Threading.Tasks.TaskCompletionSource%601>. Übergeben Sie die `TaskCompletionSource` an Ihren Hintergrunddienst, und lassen Sie sie <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> aufrufen, wenn Sie das WebSocket beenden. Warten Sie dann während der Anforderung mithilfe von `await` auf die <xref:System.Threading.Tasks.TaskCompletionSource%601.Task>-Eigenschaft.

### <a name="send-and-receive-messages"></a>Senden und Empfangen von Nachrichten

Die `AcceptWebSocketAsync`-Methode ändert die TCP-Verbindung in eine WebSocket-Verbindung und stellt ein [WebSocket](/dotnet/core/api/system.net.websockets.websocket)-Objekt bereit. Verwenden Sie das `WebSocket`-Objekt, um Nachrichten zu senden und zu empfangen.

Der weiter oben gezeigte Code, der WebSocket-Anforderungen akzeptiert, übergibt das `WebSocket`-Objekt an eine `Echo`-Methode. Der Code empfängt eine Nachricht und sendet diese umgehend wieder zurück. Nachrichten werden in einer Schleife gesendet und empfangen, bis der Client die Verbindung schließt:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](websockets/samples/1.x/WebSocketsSample/Startup.cs?name=Echo)]

::: moniker-end

Wenn Sie die WebSocket-Verbindung vor Beginn der Schleife akzeptieren, endet die Middlewarepipeline. Wenn Sie das Socket schließen, wird die Pipeline entladen. Das bedeutet, dass sich die Anforderung in der Pipeline nicht mehr weiter bewegt, wenn der WebSocket akzeptiert wird. Wenn die Schleife beendet und der Socket geschlossen ist, wird die Anforderung in der Pipeline weiter verarbeitet.

::: moniker range=">= aspnetcore-2.2"

### <a name="handle-client-disconnects"></a>Behandeln der Trennung der Verbindung mit dem Client

Der Server wird nicht automatisch informiert, wenn die Verbindung mit dem Client wegen Konnektivitätsverlust getrennt wird. Der Server empfängt nur dann eine Trennungsnachricht, wenn der Client sie sendet, was bei Verlust der Verbindung nicht möglich ist. Wenn Sie Maßnahmen ergreifen möchten, wenn dies der Fall ist, legen Sie ein Timeout fest für den Fall, dass innerhalb eines bestimmten Zeitfensters keine Eingabe vom Client empfangen wird.

Wenn der Client nicht immer Nachrichten sendet und Sie kein Timeout festlegen möchten, nur weil die Verbindung in den Leerlauf übergeht, lassen Sie den Client einen Timer verwenden, um alle X Sekunden eine Ping-Nachricht zu senden. Wenn auf dem Server nicht innerhalb von 2\*X Sekunden nach einer Nachricht die nächste eingeht, beenden Sie die Verbindung, und melden Sie, dass der Client die Verbindung getrennt hat. Warten Sie für den doppelten Zeitraum des erwarteten Zeitintervalls, um zusätzliche Zeit für Netzwerkverzögerungen einzuräumen, die die Ping-Nachricht aufhalten könnten.

### <a name="websocket-origin-restriction"></a>Beschränkung von WebSocket-Ursprüngen

Der von CORS erzeugte Schutz gilt nicht für WebSockets. Für Browser gilt Folgendes **nicht**:

* Ausführen von CORS-Preflightanforderungen
* Berücksichtigen der Einschränkungen, die in den `Access-Control`-Headern bei der Erstellung von WebSocket-Anforderungen angegeben sind

Allerdings senden Browser den `Origin`-Header, wenn die WebSocket-Anforderungen ausgegeben werden. Anwendungen sollten zur Überprüfung dieser Header konfiguriert werden, um sicherzustellen, dass nur WebSockets von den erwarteten Ursprüngen zulässig sind.

Wenn Sie Ihren Server unter „https://server.com“ und Ihren Client unter „https://client.com“ hosten, fügen Sie „https://client.com“ zur Liste `AllowedOrigins` hinzu, damit sie von WebSockets überprüft wird.

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> Der `Origin`-Header wird vom Client gesteuert und kann wie der `Referer`-Header überlistet werden. Verwenden Sie diese Header **nicht** als Authentifizierungsmechanismus.

::: moniker-end

## <a name="iisiis-express-support"></a>Unterstützung für IIS und IIS Express

In Windows Server 2012 oder höher und in Windows 8 oder höher mit IIS 8 oder IIS Express 8 oder höher ist die Unterstützung für das WebSocket-Protokoll enthalten.

> [!NOTE]
> WebSockets sind immer aktiviert, wenn Sie IIS Express verwenden.

### <a name="enabling-websockets-on-iis"></a>Aktivieren von WebSockets in IIS

So aktivieren Sie die Unterstützung für das WebSocket-Protokoll unter Windows Server 2012 oder höher:

> [!NOTE]
> Diese Schritte sind nicht erforderlich, wenn Sie IIS Express verwenden.

1. Verwenden Sie den Assistenten **Rollen und Features hinzufügen** im Menü **Verwalten** oder den Link in **Server-Manager**.
1. Klicken Sie auf **Rollenbasierte oder featurebasierte Installation**. Klicken Sie auf **Weiter**.
1. Wählen Sie den entsprechenden Server aus (standardmäßig ist der lokale Server ausgewählt). Klicken Sie auf **Weiter**.
1. Erweitern Sie **Webserver (IIS)** in der Struktur **Rollen**, und erweitern Sie **Webserver** und anschließend **Anwendungsentwicklung**.
1. Wählen Sie **WebSocket-Protokoll** aus. Klicken Sie auf **Weiter**.
1. Wenn keine zusätzlichen Features erforderlich sind, klicken Sie auf **Weiter**.
1. Klicken Sie auf **Installieren**.
1. Wenn die Installation abgeschlossen ist, klicken Sie auf **Schließen**, um den Assistenten zu beenden.

So aktivieren Sie die Unterstützung für das WebSocket-Protokoll unter Windows 8 oder höher:

> [!NOTE]
> Diese Schritte sind nicht erforderlich, wenn Sie IIS Express verwenden.

1. Navigieren Sie zu **Systemsteuerung** > **Programme** > **Programme und Features** > **Windows-Features aktivieren oder deaktivieren** (links auf dem Bildschirm).
1. Öffnen Sie die folgenden Knoten: **Internetinformationsdienste** > **WWW-Dienste** > **Anwendungsentwicklungsfeatures**.
1. Wählen Sie das Feature **WebSocket-Protokoll** aus. Klicken Sie auf **OK**.

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a>Deaktivieren von WebSocket bei Verwendung von „socket.io“ in „Node.js“

Wenn Sie die WebSocket-Unterstützung in [socket.io](https://socket.io/) in [Node.js](https://nodejs.org/) verwenden, deaktivieren Sie das IIS-WebSocket-Standardmodul mithilfe des `webSocket`-Elements in *web.config* oder *applicationHost.config*. Wenn dieser Schritt nicht durchgeführt wird, versucht das IIS-WebSocket-Modul, die WebSocket-Kommunikation statt Node.js und der App zu verarbeiten.

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="next-steps"></a>Nächste Schritte

Die [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples), die in diesem Artikel verwendet wird, ist eine Echo-App. Sie verfügt über eine Webseite, die WebSocket-Verbindungen herstellt. Der Server schickt alle empfangenen Nachrichten zurück an den Client. Führen Sie die App über eine Eingabeaufforderung aus (sie ist nicht darauf ausgelegt, von Visual Studio mit IIS Express ausgeführt zu werden), und navigieren Sie zu http://localhost:5000. Die Webseite zeigt den Verbindungsstatus in der oberen linken Ecke an:

![Erster Zustand der Webseite](websockets/_static/start.png)

Klicken Sie auf **Connect** (Verbinden), um eine WebSocket-Anforderung an die gezeigte URL zu senden. Geben Sie einen Testtext ein, und klicken Sie auf **Send** (Senden). Wenn dies abgeschlossen ist, klicken Sie auf **Close Socket** (Socket schließen). Der Abschnitt **Kommunikationsprotokoll** meldet jede open-, send- und close-Aktion, wenn diese durchgeführt wird.

![Erster Zustand der Webseite](websockets/_static/end.png)

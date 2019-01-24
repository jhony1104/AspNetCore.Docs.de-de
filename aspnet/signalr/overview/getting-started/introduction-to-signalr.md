---
uid: signalr/overview/getting-started/introduction-to-signalr
title: Einführung zu SignalR | Microsoft-Dokumentation
author: bradygaster
description: Dieser Artikel beschreibt, wie SignalR ist, und einige der Lösungen, die ihm zugedachte erstellen.
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: 0fab5e35-8c1f-43d4-8635-b8aba8766a71
msc.legacyurl: /signalr/overview/getting-started/introduction-to-signalr
msc.type: authoredcontent
ms.openlocfilehash: 7b9dae3e5d79319a9fefee41f4525a59f950746a
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837766"
---
<a name="introduction-to-signalr"></a>Einführung zu SignalR
====================

durch [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]


> Dieser Artikel beschreibt, wie SignalR ist, und einige der Lösungen, die ihm zugedachte erstellen. 
> 
> ## <a name="questions-and-comments"></a>Fragen und Kommentare
> 
> Lassen Sie Feedback, auf wie Ihnen in diesem Tutorial gefallen hat und was wir in den Kommentaren am unteren Rand der Seite verbessern können. Wenn Sie Fragen, die nicht direkt mit dem Tutorial verknüpft sind haben, können Sie sie veröffentlichen das [ASP.NET SignalR-Forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) oder [StackOverflow.com](https://stackoverflow.com/questions/tagged/signalr).

## <a name="what-is-signalr"></a>Was ist SignalR?

ASP.NET SignalR ist eine Bibliothek für ASP.NET-Entwickler,, die das Hinzufügen von echtzeitwebfunktionalität zu Anwendungen vereinfacht. Echtzeit-Webfunktionen bieten, ist die Möglichkeit, Server Code Inhalte mithilfe von Push sofort an verbundene Clients sobald diese verfügbar werden, statt den Server, die für einen Client nach neuen Daten warten.

SignalR kann verwendet werden, um jede Art von "Echtzeit" Webfunktionen zu Ihrer ASP.NET-Anwendung hinzufügen. Beim Chat häufig als Beispiel verwendet wird, können Sie viel mehr tun. Jedes Mal ein Benutzer aktualisiert eine Webseite, um neue Daten anzuzeigen, oder die Seite implementiert [lange](http://en.wikipedia.org/wiki/Push_technology#Long_polling) um neue Daten abzurufen, ist es ein Kandidat für die Verwendung von SignalR. Beispiele für Dashboards und Überwachen von Anwendungen, gemeinschaftliche Anwendungen (wie Gleichzeitiges Bearbeiten von Dokumenten), Projekt-Statusupdates und Echtzeit-Formulare.

SignalR ermöglicht auch völlig neue Typen von Webanwendungen, die eine hohe Frequenz von Updates vom Server erfordern z. B. in Echtzeit Spiele.

SignalR stellt eine einfache API zum Erstellen von Server-zu-Client von Remoteprozeduraufrufen (RPC), die JavaScript-Funktionen im Client Browser (und anderen Clientplattformen) vom serverseitigen .NET Code aufrufen. SignalR enthält außerdem eine API für die verbindungsverwaltung (z. B. eine Verbindung herstellen und trennungsereignisse), und Gruppieren von Verbindungen.

![Aufrufen von Methoden mit SignalR](introduction-to-signalr/_static/image1.png)

SignalR verbindungsverwaltung automatisch behandelt und können Sie Nachrichten an alle verbundenen Clients gleichzeitig, wie einem Chatraum. Sie können auch Nachrichten an bestimmte Clients senden. Die Verbindung zwischen Client und Server ist persistent, im Gegensatz zu einer klassischen HTTP-Verbindung, die für die einzelnen mitteillungen erneut hergestellt wird.

SignalR unterstützt die "Serverpush-Funktion in der Server-Code an den Clientcode im Browser aufrufen Remoteprozeduraufruf (RPC), anstatt das allgemeine Anforderung / Antwort-Modell im Web noch heute mit aufrufen kann.

SignalR-Anwendungen können schnell horizontal hochskaliert werden Tausende von Clients mithilfe von Service Bus, SQL Server oder [Redis](http://redis.io).

SignalR ist Open Source, über [GitHub](https://github.com/signalr).

## <a name="signalr-and-websocket"></a>SignalR und WebSocket

SignalR verwendet die neuen WebSocket-Transport, sofern verfügbar und wechselt zurück zu älteren Transporte, bei Bedarf. Sicherlich schreiben, der Ihre app mit dem WebSocket direkt, verwenden SignalR bedeutet, dass, die viele der zusätzlichen Funktionen, die Sie implementieren müssen bereits für Sie erledigt wird. Wichtiger ist, bedeutet dies, dass Sie Ihre app um WebSocket nutzen, ohne sich Gedanken, erstellen einen separaten Codepfad für ältere Clients programmieren können. SignalR schützt auch erspart, um Updates für den WebSocket, kümmern, da SignalR aktualisiert wird, um Änderungen in den zugrunde liegenden Transport unterstützen Ihre Anwendung eine konsistente Schnittstelle für Versionen von WebSocket bereit.

<a id="transports"></a>

## <a name="transports-and-fallbacks"></a>Transporte und fallbacks

SignalR ist eine Abstraktion über einige der Transporte, die in Echtzeit Aufgaben zwischen Client und Server erforderlich sind. Eine SignalR-Verbindung HTTP startet und wird dann in eine WebSocket-Verbindung heraufgestuft, sofern diese verfügbar ist. WebSocket ist der ideale Transport für SignalR, da ist die effizienteste Nutzung des Serverarbeitsspeichers, die geringste Latenz und verfügt über die zugrunde liegenden Funktionen (z. B. Vollduplexkommunikation zwischen Client und Server), aber sie auch die strengste hat Anforderungen: WebSocket muss der Server Windows Server 2012 oder Windows 8 und .NET Framework 4.5 verwenden. Wenn diese Anforderungen nicht erfüllt sind, versucht SignalR, andere Transporte verwenden, um die Verbindungen herstellen.

### <a name="html-5-transports"></a>HTML 5-Transporte

Diese Transporte verwendet abhängig von Unterstützung für [HTML 5](http://en.wikipedia.org/wiki/HTML5). Wenn der Clientbrowser den HTML5-Standard nicht unterstützt, werden ältere Transporte verwendet werden.

- **WebSocket** (wenn die dem Server und dem Browser anzugeben, können sie Unterstützung von Websocket). WebSocket ist die einzige Transport, der eine true persistente, bidirektionale Verbindung zwischen Client und Server hergestellt wird. WebSocket weist jedoch auch die strengsten Anforderungen; Es wird nur in den neuesten Versionen von Microsoft Internet Explorer, Google Chrome und Mozilla Firefox vollständig unterstützt und verfügt über nur eine partielle Implementierung in anderen Browsern wie z. B. Opera und Safari.
- **Server gesendete Ereignisse**, auch bekannt als EventSource (wenn der Browser Server gesendete Ereignisse, die im Grunde ist von allen Browsern mit Ausnahme von Internet Explorer unterstützt.)

### <a name="comet-transports"></a>Comet-Transporte

Die folgenden Transportoptionen basieren auf der [Comet](http://en.wikipedia.org/wiki/Comet_(programming)) -Modell, bei der ein Browser oder anderen Clients, eine lange aufrechterhalten HTTP-Anforderung, die der Server verwenden können verwaltet, um Daten an den Client ohne den Client übertragen, insbesondere fordert an diese.

- **Forever Frame** (für nur InternetExplorer). Frame erstellt immer einen ausgeblendeten IFrame, wodurch eine Anforderung an einen Endpunkt auf dem Server, die nicht abgeschlossen wird. Der Server sendet dann kontinuierlich Skript auf den Client, der sofort ausgeführt wird, stellt eine unidirektionale Echtzeit-Verbindung vom Server an Client. Die Verbindung vom Client zum Server verwendet eine separate Verbindung vom Server zu Clientverbindung, und wie ein standard-HTTP-Anforderung, eine neue Verbindung erstellt wird, für jedes Datenelement, die gesendet werden muss.
- **AJAX langer Abruf**. Langes Abrufintervall erstellt eine permanente Verbindung nicht, aber stattdessen fragt den Server mit einer Anforderung, die geöffnet bleibt, bis der Server antwortet, die an diesem Punkt die Verbindung wird geschlossen, und eine neue Verbindung sofort angefordert wird. Dies kann einige Latenz kommen, während die Verbindung zurückgesetzt.

Weitere Informationen dazu, welche Transporte unter welche Konfigurationen unterstützt werden, finden Sie unter [unterstützte Plattformen](supported-platforms.md).

### <a name="transport-selection-process"></a>Auswahl-Transport-Prozess

Die folgende Liste enthält die Schritte, die SignalR verwendet wird, um zu entscheiden, welcher transport verwendet.

1. Wenn der Browser InternetExplorer 8 oder niedriger ist, wird die Long Polling verwendet.
2. Wenn JSONP konfiguriert ist (d. h. die `jsonp` Parameter auf festgelegt ist `true` Wenn die Verbindung gestartet wird), Long Polling verwendet wird.
3. Wenn eine domänenübergreifende-Verbindung hergestellt wird (d.h., wenn die SignalR-Endpunkt nicht in derselben Domäne wie der Hostseite vorhanden ist), wird WebSocket verwendet werden, wenn die folgenden Kriterien erfüllt sind:

   - Der Client unterstützt die CORS (Cross-Origin Resource Sharing). Details zu den Clients CORS unterstützen, finden Sie unter [CORS auf caniuse.com](http://www.caniuse.com/CORS).
   - Der Client unterstützt WebSocket
   - Der Server unterstützt WebSocket

     Wenn diese Kriterien nicht erfüllt sind, werden Long Polling verwendet werden. Weitere Informationen zu domänenübergreifenden Verbindungen, finden Sie unter [Gewusst wie: Herstellen einer Verbindung domänenübergreifende](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain).
4. Wenn JSONP ist nicht konfiguriert, und die Verbindung keine domänenübergreifende ist, wird bei der Client- und Unterstützung WebSocket verwendet.
5. Wenn der Client oder Server WebSocket nicht unterstützen, wird Server gesendete Ereignisse verwendet, sofern diese verfügbar ist.
6. Wenn der Server gesendete Ereignisse nicht verfügbar ist, wird die Forever Frame versucht.
7. Wenn Forever Frame ein Fehler auftritt, wird die Long Polling verwendet.

<a id="MonitoringTransports"></a>
### <a name="monitoring-transports"></a>Überwachen von Transporten

Sie können bestimmen, welcher Transport Ihrer Anwendung verwendet wird, durch Aktivieren der Protokollierung für Ihren Hub, und öffnen in Ihrem Browser das Konsolenfenster.

Fügen Sie den folgenden Befehl zum Aktivieren der Protokollierung für Ihren Hub Ereignisse in einem Browser Ihrer Clientanwendung:

`$.connection.hub.logging = true;`

- Klicken Sie in Internet Explorer öffnen Sie der Entwicklertools durch Drücken von F12, und klicken Sie auf der Registerkarte "Konsole".

    ![-Konsole in Microsoft Internet Explorer](introduction-to-signalr/_static/image2.png)
- Öffnen Sie in Chrome die Konsole durch Drücken von STRG + UMSCHALT + J.

    ![-Konsole in Google Chrome](introduction-to-signalr/_static/image3.png)

Die Konsole öffnen und die Protokollierung aktiviert werden Sie sehen, welcher Transport von SignalR verwendet wird.

![In Internet Explorer mit der WebSocket-Transport-Konsole](introduction-to-signalr/_static/image4.png)

### <a name="specifying-a-transport"></a>Angeben eines Transports

Aushandeln von Transport dauert eine gewisse Zeit und Client/Server-Ressourcen. Wenn die Funktionen der bekannt ist, kann ein Transport angegeben werden, wenn die Clientverbindung gestartet wird. Der folgende Codeausschnitt veranschaulicht, eine Verbindung mit den Transport Ajax Long Polling aus, wie genutzt wird, wenn bekannt war, dass der Client nicht über ein anderes Protokoll unterstützte ab:

`connection.start({ transport: 'longPolling' });`

Sie können eine fallback Reihenfolge angeben, wenn Sie einen Client zur jeweiligen Transporte in der Reihenfolge versuchen Sie es möchten. Der folgende Codeausschnitt zeigt beim WebSocket, nicht verfügbar war, und navigieren direkt auf Long Polling.

`connection.start({ transport: ['webSockets','longPolling'] });`

Zeichenfolgenkonstanten zum Angeben von Transporten sind wie folgt definiert:

- `webSockets`
- `foreverFrame`
- `serverSentEvents`
- `longPolling`

## <a name="connections-and-hubs"></a>Verbindungen und-Hubs

Der SignalR-API enthält zwei Modelle für die Kommunikation zwischen Clients und Servern: Dauerhafte Verbindungen und Hubs.

Eine Verbindung stellt einen einfachen Endpunkt für die einzelnen-Empfänger, gruppiert oder per broadcast Senden von Nachrichten dar. Der persistente Verbindungs-API (dargestellt in .NET-Code durch die PersistentConnection-Klasse) bietet der Entwickler direkten Zugriff auf das Low-Level-Kommunikationsprotokoll, das SignalR verfügbar macht. Verwenden das Kommunikationsmodell Verbindungen werden-Entwicklern vertraut, die Verbindung-basierten APIs wie z. B. Windows Communication Foundation verwendet haben.

Ein Hub ist eine weitere allgemeine Pipeline, die auf den Verbindungs-API, die Ihre Client- und Methoden direkt aufeinander aufrufen können. SignalR übernimmt die Verteilung über Computergrenzen hinweg, wie von Zauberhand, zulassen, dass Clients zum Aufrufen von Methoden auf dem Server als einfach als lokalen Methoden (und umgekehrt). Verwenden das Kommunikationsmodell für die Hubs werden-Entwicklern vertraut, die Remoteaufruf APIs wie z. B. .NET Remoting verwendet haben. Mit einem Hub können Sie auch zum Übergeben von stark typisierter Parameters an Methoden, die modellbindung zu aktivieren.

### <a name="architecture-diagram"></a>Architekturdiagramm

Das folgende Diagramm zeigt die Beziehung zwischen Hubs, dauerhafte Verbindungen und die zugrunde liegenden Technologien für Transporte verwendet werden.

![SignalR-Architekturdiagramm mit APIs, Transporte und -clients](introduction-to-signalr/_static/image5.png)

### <a name="how-hubs-work"></a>Funktionsweise von Hubs

Bei der serverseitigen Code eine Methode auf dem Client aufruft, wird ein Paket gesendet, in der aktive Transport mit dem Namen und Parameter der Methode, die aufgerufen werden (wenn ein Objekt als Parameter der Methode gesendet wird, wird es serialisiert mithilfe von JSON). Dem Client entspricht dann der Name der Methode für Methoden, die in der clientseitige Code definiert. Wenn eine Übereinstimmung vorliegt, wird die Methode verwendet die deserialisierte Parameterdaten ausgeführt werden.

Der Methodenaufruf kann überwacht werden, mithilfe von Tools wie [Fiddler.](http://fiddler2.com/) Die folgende Abbildung zeigt den Aufruf einer Methode, die an einen Webbrowserclient im Bereich "Protokolle" Fiddler von einem SignalR-Server gesendet. Aufruf der Methode wird vom Hub mit dem Namen gesendeten `MoveShapeHub`, und die aufgerufene Methode wird aufgerufen, `updateShape`.

![Anzeigen von Fiddler-Protokoll, die mit SignalR-Datenverkehr](introduction-to-signalr/_static/image6.png)

In diesem Beispiel wird der Hub-Namen identifiziert, mit der `H` Parameter; die Methode gekennzeichnet mit der `M` Parameter und die Daten gesendet werden, an die Methode wird mit identifiziert die `A` Parameter. Die Anwendung, die diese Nachricht generiert wird erstellt, der [Echtzeitnachrichten](tutorial-high-frequency-realtime-with-signalr.md) Tutorial.

### <a name="choosing-a-communication-model"></a>Auswählen eines Modells für die Kommunikation

Die meisten Anwendungen sollten die Hubs-API verwenden. Die API-Verbindungen kann in folgenden Situationen verwendet werden:

- Das Format der tatsächlichen Nachricht muss angegeben werden.
- Der Entwickler bevorzugt ein Modell für messaging und Verteilung, statt ein Remoteaufruf Modell standardprogrammiersprachen.
- Verwendung von SignalR ist eine vorhandene Anwendung, die eine e-Mail-Modell verwendet portiert.

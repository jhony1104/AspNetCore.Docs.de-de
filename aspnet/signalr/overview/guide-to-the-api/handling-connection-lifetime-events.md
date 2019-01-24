---
uid: signalr/overview/guide-to-the-api/handling-connection-lifetime-events
title: Überblick und Behandeln von Verbindung Objektlebensdauer-Ereignisse in SignalR | Microsoft-Dokumentation
author: bradygaster
description: Dieser Artikel beschreibt, wie durch die Hubs-API verfügbar gemachten Ereignisse beschrieben.
ms.author: bradyg
ms.date: 01/15/2019
ms.assetid: 03960de2-8d95-4444-9169-4426dcc64913
msc.legacyurl: /signalr/overview/guide-to-the-api/handling-connection-lifetime-events
msc.type: authoredcontent
ms.openlocfilehash: 53151d369a14ef9a6fa034043d40ae30098a5067
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836167"
---
<a name="understanding-and-handling-connection-lifetime-events-in-signalr"></a>Überblick und Behandeln von Verbindung Objektlebensdauer-Ereignisse in SignalR
====================

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Dieser Artikel enthält eine Übersicht über die SignalR-Verbindung, erneuten Herstellen einer Verbindung und Trennung-Ereignisse, die Sie behandeln können, und Timeout und Keepalive-Einstellungen, die Sie konfigurieren können.
>
> Der Artikel wird davon ausgegangen, dass Sie bereits über Grundkenntnisse der Objektlebensdauer-Ereignisse von SignalR und Verbindung verfügen. Eine Einführung zu SignalR finden Sie unter [Einführung zu SignalR](../getting-started/introduction-to-signalr.md). Lebensdauer der Ereignisse der Verbindung finden Sie unter den folgenden Ressourcen:
>
> - [Gewusst wie: Behandeln der Objektlebensdauer-Ereignisse in der hubklasse Verbindung](hubs-api-guide-server.md#connectionlifetime)
> - [Das Durchführen von Verbindung Objektlebensdauer-Ereignisse in JavaScript-clients](hubs-api-guide-javascript-client.md#connectionlifetime)
> - [Das Durchführen von Verbindung Objektlebensdauer-Ereignisse in .NET-clients](hubs-api-guide-net-client.md#connectionlifetime)
>
> ## <a name="software-versions-used-in-this-topic"></a>In diesem Thema verwendeten Softwareversionen
>
>
> - [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
> - .NET 4.5
> - SignalR-Version 2
>
>
>
> ## <a name="previous-versions-of-this-topic"></a>Vorherige Versionen dieses Themas
>
> Weitere Informationen zu früheren Versionen von SignalR, finden Sie unter [ältere Versionen von SignalR](../older-versions/index.md).
>
> ## <a name="questions-and-comments"></a>Fragen und Kommentare
>
> Lassen Sie Feedback, auf wie Ihnen in diesem Tutorial gefallen hat und was wir in den Kommentaren am unteren Rand der Seite verbessern können. Wenn Sie Fragen, die nicht direkt mit dem Tutorial verknüpft sind haben, können Sie sie veröffentlichen das [ASP.NET SignalR-Forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) oder [StackOverflow.com](http://stackoverflow.com/).

## <a name="overview"></a>Übersicht

Dieser Artikel enthält folgende Abschnitte:

- [Verbindung Lebensdauer Terminologie und Szenarien](#terminology)

    - [SignalR-Verbindungen, Transport und physische Verbindungen](#signalrvstransport)
    - [Transport Trennung von Szenarien](#transportdisconnect)
    - [Trennung von Clientszenarien](#clientdisconnect)
    - [Trennung von Serverszenarios](#serverdisconnect)
- [Timeout und Keepalive-Einstellungen](#timeoutkeepalive)

    - [ConnectionTimeout](#connectiontimeout)
    - [DisconnectTimeout](#disconnecttimeout)
    - [KeepAlive](#keepalive)
    - [Ändern der Einstellungen für Timeout und keepalive](#changetimeout)
- [Wie Sie die Benachrichtigung des Benutzers zu Trennvorgänge](#notifydisconnect)
- [Wiederherstellen der Verbindung fortlaufend](#continuousreconnect)
- [Gewusst wie: Trennen einen Client im Servercode](#disconnectclientfromserver)
- [Den Grund für eine Trennung der Verbindung ermitteln](#detectingreasonfordisconnection)

Links zu Themen,-API-Referenz sind, .NET 4.5-Version der API. Wenn Sie .NET 4 verwenden, finden Sie unter [.NET 4-Version der API-Themen](https://msdn.microsoft.com/library/jj891075(v=vs.100).aspx).

<a id="terminology"></a>

## <a name="connection-lifetime-terminology-and-scenarios"></a>Verbindung Lebensdauer Terminologie und Szenarien

Die `OnReconnected` -Ereignishandler in einer SignalR-Hub kann direkt nach dem Ausführen `OnConnected` , aber nicht nach dem `OnDisconnected` für einen bestimmten Client. Der Grund, dass Sie eine erneute Verbindung ohne eine Trennung der Verbindung haben, können ist, dass es gibt mehrere Möglichkeiten, die in denen das Wort "Connection" in SignalR verwendet wird.

<a id="signalrvstransport"></a>

### <a name="signalr-connections-transport-connections-and-physical-connections"></a>SignalR-Verbindungen, Transport und physische Verbindungen

In diesem Artikel wird unterschieden zwischen *SignalR-Verbindungen*, *transport Verbindungen*, und *physische Verbindungen*:

- **SignalR-Verbindung** bezieht sich auf eine logische Beziehung zwischen einem Client und Server-URL, von der SignalR-API verwaltet und durch eine Verbindungs-ID eindeutig identifiziert Die Daten zu dieser Beziehung werden von SignalR verwaltet und werden verwendet, um keine transportverbindung herstellen. Die Beziehungsenden und SignalR Löscht Daten, wenn der Client Ruft die `Stop` Methode oder ein Zeitlimit erreicht ist, während es sich bei SignalR versucht, eine verlorene transportverbindung wiederherzustellen.
- **Verbindung Transport** bezieht sich auf eine logische Beziehung zwischen einem Client und einem Server, die von einer der vier Transport APIs verwaltet wird: WebSockets, vom Server gesendeten Ereignisse, forever Frame oder lange Abrufvorgänge. SignalR verwendet des Transports-API, um eine transportverbindung zu erstellen, und die Transport-API setzt das Vorhandensein einer physischen Verbindung zum Erstellen der transportverbindung. Die transportverbindung endet, wenn es sich bei SignalR beendet wird oder der Transport-API erkennt, dass die physische Verbindung unterbrochen wird.
- **Physische Verbindung** bezieht sich auf dem physischen Netzwerk-Links – verbindet, drahtlose Signale, Router usw. –, die Kommunikation zwischen einem Clientcomputer und einem Server-Computer zu vereinfachen. Muss die physische Verbindung vorhanden, damit keine transportverbindung herstellen, und eine transportverbindung hergestellt werden muss, um eine SignalR-Verbindung herzustellen. Allerdings endet jedoch nicht unterbrechen die physische Verbindung immer sofort die transportverbindung oder den SignalR-Verbindung, wie weiter unten in diesem Thema erläutert wird.

In der folgenden Abbildung die SignalR-Verbindung wird durch die Hubs-API und SignalR für PersistentConnection-API-Ebene dargestellt, die transportverbindung wird durch die Transporte Ebene dargestellt und die physische Verbindung wird dargestellt, durch die Linien zwischen dem server und den Clients.

![SignalR-Architekturdiagramm](handling-connection-lifetime-events/_static/image1.png)

Beim Aufrufen der `Start` -Methode in einer SignalR-Client, stellen Sie SignalR-Client-Code bereit, mit den Informationen, die für die es benötigt, um eine physische Verbindung mit einem Server herzustellen. SignalR-Client-Code verwendet diese Informationen stellen eine HTTP-Anforderung, und stellen eine physische Verbindung, die eine der vier Transportmethoden verwendet. Fällt die transportverbindung oder der Server ausfällt, verschwindet nicht einfach die SignalR-Verbindung sofort, da der Client immer die Informationen, die es benötigt noch, um automatisch eine neue transportverbindung hergestellt, um die gleiche URL für die SignalR wiederherzustellen. In diesem Szenario keinen Eingriff der benutzeranwendung beteiligt ist, und wenn der SignalR-Client-Code mit eine neuen transportverbindung hergestellt wird, wird es eine neue SignalR-Verbindung nicht gestartet. Die Kontinuität der SignalR-Verbindung wird in der Faktentabelle berücksichtigt, die die Verbindungs-ID, die erstellt wird, beim Aufrufen der `Start` -Methode, ändert sich nicht.

Die `OnReconnected` -Ereignishandler für den Hub, das ausgeführt wird, wenn eine transportverbindung automatisch wiederhergestellt wird, nachdem er sich zuvor verloren gehen. Die `OnDisconnected` -Ereignishandler ausgeführt wird, am Ende einer SignalR-Verbindung. Eine SignalR-Verbindung kann in einem der folgenden Methoden beenden:

- Wenn der Client Ruft die `Stop` -Methode, eine stoppmeldung an den Server gesendet wird, und beenden Sie den SignalR-Verbindung sowohl Client-als auch sofort.
- Nachdem die Verbindung zwischen Client und Server verloren geht, versucht der Client eine Verbindung herzustellen, und der Server wartet des Clients eine Verbindung herzustellen. Wenn die Versuche der verbindungsherstellung nicht erfolgreich sind, und das Disconnect-Timeout endet, enden sowohl Client-als auch die SignalR-Verbindung. Der Client beendet wird, erneut eine Verbindung herstellen möchten, und der Server frei, der die Darstellung der SignalR-Verbindung.
- Wenn der Client nicht ausgeführt wird, ohne Möglichkeit zum Aufrufen der `Stop` -Methode der Server wartet, für den Client erneut eine Verbindung herzustellen, und beendet dann die SignalR-Verbindung nach dem Timeoutzeitraum trennen.
- Wenn der Server beendet wird, versucht der Client eine Verbindung herzustellen (die transportverbindung neu erstellen), und beendet dann die SignalR-Verbindung nach dem Timeoutzeitraum trennen.

Wenn keine Verbindung Probleme auftreten, und der Benutzer die Anwendung beendet die SignalR-Verbindung durch Aufrufen der `Stop` -Methode, die SignalR-Verbindung und die transportverbindung beginnen und enden auf etwa zur gleichen Zeit. Die folgenden Abschnitte beschreiben ausführlicher die anderen Szenarien.

<a id="transportdisconnect"></a>

### <a name="transport-disconnection-scenarios"></a>Transport Trennung von Szenarien

Physische Verbindungen ist möglicherweise langsam, oder gibt es möglicherweise Unterbrechungen in Verbindung. Abhängig von Faktoren wie die Länge der Unterbrechung kann die transportverbindung gelöscht werden. SignalR versucht, die die transportverbindung erneut herzustellen. Manchmal die transportverbindung API erkennt die Unterbrechung und löscht die transportverbindung und SignalR findet heraus, sofort, dass die Verbindung unterbrochen wird. In anderen Fällen sofort weder die transportverbindung API und SignalR Beachten Sie, dass die Verbindung unterbrochen wurde. Für alle Transporte außer lange Abrufvorgänge verwendet der SignalR-Client eine Funktion namens *Keepalive* zu prüfen, dass der Verlust der Verbindung, die der Transport-API-wurde nicht erkannt wird. Informationen zu lange Abruf-Verbindungen finden Sie unter [Einstellungen für Timeout und Keepalive](#timeoutkeepalive) weiter unten in diesem Thema.

Wenn eine Verbindung inaktiv ist, sendet in regelmäßigen Abständen der Server ein Keepalive-Paket an den Client. Ab dem Datum, das in diesem Artikel geschrieben wird, ist das Standardintervall für alle 10 Sekunden. Durch diese Pakete überwachen, können Clients Teilen, liegt ein Verbindungsproblem. Wenn ein Keepalive-Paket nicht empfangen wird, wenn davon ausgegangen, nimmt nach kurzer Zeit der Client, Verbindungsprobleme, z. B. Langsamkeit oder Unterbrechungen vorhanden sind. Wenn die Keepalive noch nicht nach einem längeren Zeitraum empfangen wird, nimmt der Client an, dass die Verbindung wurde gelöscht, und sie beginnt, erneut eine Verbindung herstellen möchten.

Das folgende Diagramm veranschaulicht die Client- und Ereignisse, die in einem typischen Szenario ausgelöst werden, wenn Probleme mit der physischen Verbindung, die sofort von den Transport-API erkannt werden nicht vorhanden sind. Das Diagramm gilt für den folgenden Situationen:

- Der Transport ist WebSockets, forever Frame oder vom Server gesendeten Ereignisse.
- Es gibt unterschiedliche Zeiträume die physische Netzwerkverbindung unterbrochen.
- Der Transport-API wird, über die Unterbrechungen, daher SignalR auf den Keepalive-Funktionen zu erkennen.

![Transport Trennvorgänge](handling-connection-lifetime-events/_static/image2.png)

Wenn der Client wechselt in die Verbindung der Modus, aber keine transportverbindung ein innerhalb des Timeout-Grenzwerts für trennen herstellen, beendet der Server die SignalR-Verbindung. In diesem Fall wird der Server führt des Hubs `OnDisconnected` -Methode und stellt eine Trennungsnachricht an den Client gesendet werden soll, für den Fall, dass der Client verwaltet werden, um später eine Verbindung herzustellen. Wenn der Client dann erneut eine Verbindung herstellen, empfängt es der Disconnect-Befehl und ruft die `Stop` Methode. In diesem Szenario `OnReconnected` wird nicht ausgeführt werden, wenn der Client die Verbindung wiederherstellt, und `OnDisconnected` wird nicht ausgeführt werden, wenn der Client ruft `Stop`. Das folgende Diagramm veranschaulicht dieses Szenario.

![Unterbrechungen der Transport - Server-Timeouts](handling-connection-lifetime-events/_static/image3.png)

Der SignalR-Verbindung Objektlebensdauer-Ereignisse, die auf dem Client ausgelöst werden können, sind die folgenden:

- `ConnectionSlow` Clientereignis.

    Ausgelöst, wenn Sie ein vordefinierten Anteil der Keepalive-Timeoutzeitraum verstrichen seit der letzten Nachricht oder Keepalive Ping wurde empfangen. Keepalive Warnung Timeoutdauer ist 2/3 des Timeouts Keepalive. Keepalive als Timeout wird 20 Sekunden, damit die Warnung bei etwa 13 Sekunden tritt.

    Standardmäßig sendet der Server Keepalive-Pings alle 10 Sekunden, und wird vom Client nach Keepalive-Ping-Nachrichten über alle 2 Sekunden (ein Drittel des Unterschieds zwischen den Keepalive-Wert für Timeout und den Keepalive-Timeoutwert von Warnung).

    Wenn der Transport-API über eine Trennung ist, kann für das Trennen SignalR informiert werden, bevor das Zeitlimit des Warnung Keepalive übergeben. In diesem Fall die `ConnectionSlow` Ereignis nicht ausgelöst werden würde, und SignalR würde direkt mit der `Reconnecting` Ereignis.
- `Reconnecting` Clientereignis.

    Wird ausgelöst, wenn (a) der Transport-API erkennt, dass die Verbindung verloren geht, ist, (b) das Keepalive-Zeitlimit seit der letzten Nachricht verstrichen oder Keepalive Ping wurde empfangen. Der SignalR-Client-Code beginnt, erneut eine Verbindung herstellen möchten. Sie können dieses Ereignis behandeln, wenn Sie möchten die Anwendung aus, um Maßnahmen zu ergreifen, wenn eine transportverbindung verloren geht. Das Standardtimeout für Keepalive beträgt derzeit 20 Sekunden.

    Wenn Clientcode versucht, die eine hubmethode aufrufen, solange SignalR im Modus Wiederherstellen der Verbindung, versucht SignalR, den Befehl zu senden. In den meisten Fällen, solche Versuche fehl, jedoch in einigen Fällen können sie erfolgreich. Für die vom Server gesendeten Ereignisse forever Frame und long Polling Transporte verwendet die SignalR zwei Kommunikationskanäle, eine, die der Client verwendet, um Nachrichten zu senden und eine, die zum Empfangen von Nachrichten verwendet. Kanal für den Empfang dauerhaft geöffnet wird, und das ist die, die geschlossen wird, wenn die physische Verbindung unterbrochen wird. Der Kanal für das Senden bleibt verfügbar ist, verwendet werden, wenn physische Verbindung wiederhergestellt wird, ein Methodenaufruf vom Client zum Server erfolgreich möglicherweise vor der Receive-Kanal erneut hergestellt wird. Der Rückgabewert wird nicht empfangen werden, bis den Kanal verwendet wird, für den Empfang von SignalR erneut geöffnet.
- `Reconnected` Clientereignis.

    Wird ausgelöst, wenn erneut die transportverbindung hergestellt wird. Die `OnReconnected` -Ereignishandler im Hub ausgeführt wird.
- `Closed` Clientereignis (`disconnected` Ereignis in JavaScript).

    Ausgelöst, wenn das Disconnect-Timeout abläuft, während der SignalR-Clientcode versucht, erneut eine Verbindung herstellen, nach dem Verlust der transportverbindung. Trennen Sie die Standardeinstellung beträgt 30 Sekunden. (Dieses Ereignis wird auch ausgelöst, wenn die Verbindung beendet, da die `Stop` Methode wird aufgerufen.)

Transport Verbindung Unterbrechungen, die nicht vom Transport API erkannt werden und nicht verzögert den Empfang von Keepalive-Pings vom Server länger als das Zeitlimit des Warnung Keepalive bewirkt möglicherweise keine Verbindung Objektlebensdauer-Ereignisse ausgelöst werden soll.

Einige netzwerkumgebungen schließen absichtlich Verbindungen im Leerlauf, und eine Funktion mit dem Keepalive-Pakete ist, um zu verhindern, dies durch ermöglicht, die diese Netzwerke wissen, dass es sich bei eine SignalR-Verbindung verwendet wird. In extremen Fällen der Standardrate von Keepalive-Pings genug, um zu verhindern, dass geschlossene Verbindungen möglicherweise nicht. In diesem Fall können Sie Keepalive-Pings häufiger zu sendende konfigurieren. Weitere Informationen finden Sie unter [Einstellungen für Timeout und Keepalive](#timeoutkeepalive) weiter unten in diesem Thema.

> [!NOTE]
>
> **Wichtig:** Die Abfolge der Ereignisse, die hier beschriebenen ist nicht garantiert. SignalR versucht jede Verbindung Objektlebensdauer-Ereignisse in einer vorhersagbaren Weise nach diesem Schema heraufstufen, aber es gibt viele Varianten der Netzwerkereignisse und viele Möglichkeiten, die in denen zugrunde liegenden Communications-Frameworks wie Transport APIs diese behandeln. Z. B. die `Reconnected` -Ereignis kann nicht ausgelöst werden, wenn der Client die Verbindung wiederherstellt, oder die `OnConnected` Handler auf dem Server kann ausgeführt werden, wenn der Versuch zum Herstellen einer Verbindung nicht erfolgreich ist. Dieses Thema beschreibt nur die Effekte, die normalerweise durch bestimmte normalen Betrieb erstellt werden würde.


<a id="clientdisconnect"></a>

### <a name="client-disconnection-scenarios"></a>Trennung von Clientszenarien

Führt in einem Browserclient der SignalR-Client-Code, der eine SignalR-Verbindung verwaltet in der JavaScript-Kontext einer Webseite ein. Verfügt über Grund die SignalR-Verbindung zu beenden, wenn Sie von einem navigieren Seiten in ein anderes festgelegt ist, und der Grund müssen Sie mehrere Verbindungen mit mehreren Verbindungs-IDs, wenn Sie von mehreren Browserfenstern oder Registerkarten verbinden. Wenn der Benutzer ein Browserfenster oder einer Registerkarte schließt oder zu einer neuen Seite navigiert oder die Seite aktualisiert wird, die SignalR-Verbindung sofort beendet wird, da SignalR-Client-Code für Sie und ruft diese Browser Ereignisbehandlung der `Stop` Methode. In diesen Szenarien oder auf allen Clientplattformen, wenn die Anwendung aufruft der `Stop` -Methode, die `OnDisconnected` -Ereignishandler wird sofort ausgeführt, auf dem Server und der Client löst die `Closed` Ereignis (das Ereignis trägt den Namen `disconnected` in JavaScript).

Wenn eine Clientanwendung oder der Computer, den er ausgeführt wird, auf abstürzt oder in den Ruhezustand versetzt (z. B. wenn der Benutzer den Laptop schließen), wird der Server nicht darüber informiert, was passiert ist. Als der Server weiß, der Verlust des Clients möglicherweise aufgrund einer Unterbrechung der Netzwerkverbindung, und der Client eine Verbindung herzustellen versucht möglicherweise. Aus diesem Grund in diesen Szenarien, die der Server wartet, bis dem Client die Möglichkeit, die Verbindung wiederherzustellen, geben und `OnDisconnected` wird nicht ausgeführt werden, bis das Zeitlimit für die Verbindung trennen (ungefähr 30 Sekunden in der Standardeinstellung) abläuft. Das folgende Diagramm veranschaulicht dieses Szenario.

![Fehler bei der Computer.](handling-connection-lifetime-events/_static/image4.png)

<a id="serverdisconnect"></a>

### <a name="server-disconnection-scenarios"></a>Trennung von Serverszenarios

Wenn ein Server ausfällt – Neustart, ein Fehler auftritt, wird die Anwendungsdomäne wiederverwendet, usw. – möglicherweise ist das Ergebnis ähnelt einer Verbindung wurde unterbrochen oder der Transport-API und SignalR möglicherweise sofort zu wissen, dass der Server nicht mehr vorhanden ist, und SignalR könnte versuchen, ohne eine Verbindung zu beginnen. Auslösen der `ConnectionSlow` Ereignis. Wenn der Client wird in Verbindung Modus und der Server wiederhergestellt wird oder neu gestartet oder ein neuer Server online geschaltet wird vor Ablauf des Zeitlimits für die Verbindung trennen, wird der Client mit dem wiederhergestellten oder neuen Verbindung wiederherstellen. In diesem Fall die SignalR-Verbindung wird fortgesetzt, auf dem Client und dem `Reconnected` Ereignis wird ausgelöst. Auf dem ersten Server `OnDisconnected` nie ausgeführt wird, und klicken Sie auf dem neuen Server, `OnReconnected` ausgeführt wird, obwohl `OnConnected` wurde für diesen Client auf dem Server, bevor Sie nie ausgeführt. (Die Auswirkung ist gleich, wenn der Client mit dem gleichen Server nach einem Neustart oder das app-Anwendungsdomänen-Wiederverwendung, erneut eine Verbindung herstellt, da beim Neustart des Servers es keinen Speicher für vorherige Verbindungsaktivität.) Im folgende Diagramm wird davon ausgegangen, dass der Transport-API-sofort, beachten Sie die Verbindung unterbrochen wird also die `ConnectionSlow` Ereignis wird nicht ausgelöst.

![Ausfall des Servers und erneute Verbindungen](handling-connection-lifetime-events/_static/image5.png)

Wenn ein Server nicht verfügbar innerhalb des Timeoutzeitraums getrennt wird, endet die SignalR-Verbindung. In diesem Szenario die `Closed` Ereignis (`disconnected` in JavaScript-Clients) wird auf dem Client ausgelöst, aber `OnDisconnected` wird nie aufgerufen werden, auf dem Server. Im folgende Diagramm wird davon ausgegangen, dass der Transport-API nicht mehr Beachten Sie die Verbindung unterbrochen, damit es von SignalR Keepalive-Funktionalität erkannt wird und die `ConnectionSlow` Ereignis wird ausgelöst.

![Ausfall des Servers und timeout](handling-connection-lifetime-events/_static/image6.png)

<a id="timeoutkeepalive"></a>

## <a name="timeout-and-keepalive-settings"></a>Timeout und Keepalive-Einstellungen

Der Standardwert `ConnectionTimeout`, `DisconnectTimeout`, und `KeepAlive` Werte für die meisten Szenarien geeignet sind, aber kann geändert werden, wenn Ihre Umgebung besondere Anforderungen hat. Wenn Ihre Netzwerkumgebung Verbindungen geschlossen, die 5 Sekunden lang im Leerlauf sind wird, möglicherweise Sie z. B. den Keepalive-Wert zu verringern.

<a id="connectiontimeout"></a>

### <a name="connectiontimeout"></a>ConnectionTimeout

Diese Einstellung steht für die Zeitspanne, während eine transportverbindung öffnen und zu warten auf eine Antwort vor dem Schließen und öffnen eine neue Verbindung zu bleiben. Der Standardwert ist 110 Sekunden.

Diese Einstellung gilt nur wenn Keepalive-Funktionalität deaktiviert ist, die für gilt normalerweise nur des langen, abruftransport. Das folgende Diagramm veranschaulicht die Auswirkungen dieser Einstellung auf einen Long polling transportverbindung.

![Long Polling-transportverbindung](handling-connection-lifetime-events/_static/image7.png)

<a id="disconnecttimeout"></a>

### <a name="disconnecttimeout"></a>DisconnectTimeout

Diese Einstellung steht für die Zeitspanne, die gewartet wird, nachdem eine transportverbindung, vor dem Auslösen verloren geht der `Disconnected` Ereignis. Der Standardwert ist 30 Sekunden. Wenn Sie festlegen, `DisconnectTimeout`, `KeepAlive` automatisch auf 1/3 des festgelegt, die `DisconnectTimeout` Wert.

<a id="keepalive"></a>

### <a name="keepalive"></a>KeepAlive

Diese Einstellung steht für die Zeitdauer vor dem Senden eines Keepalive-Pakets über eine Verbindung im Leerlauf. Der Standardwert beträgt 10 Sekunden. Dieser Wert darf nicht sein mehr als 1/3 des der `DisconnectTimeout` Wert.

Wenn Sie festlegen möchten `DisconnectTimeout` und `KeepAlive`legen `KeepAlive` nach `DisconnectTimeout`. Andernfalls Ihre `KeepAlive` Einstellung wird überschrieben werden Wenn `DisconnectTimeout` automatisch `KeepAlive` 1/3, der den Timeoutwert.

Wenn Sie Keepalive-Funktion deaktivieren möchten, legen Sie `KeepAlive` auf Null. Keepalive-Funktionalität wird automatisch deaktiviert, für den langen abruftransport.

<a id="changetimeout"></a>

### <a name="how-to-change-timeout-and-keepalive-settings"></a>Ändern der Einstellungen für Timeout und keepalive

Um die Standardwerte für diese Einstellungen zu ändern, legen Sie diese `Application_Start` in Ihre *"Global.asax"* Datei, wie im folgenden Beispiel gezeigt. Die Werte, die im Beispielcode gezeigt sind identisch mit den Standardwerten.

[!code-csharp[Main](handling-connection-lifetime-events/samples/sample1.cs)]

<a id="notifydisconnect"></a>

## <a name="how-to-notify-the-user-about-disconnections"></a>Wie Sie die Benachrichtigung des Benutzers zu Trennvorgänge

In einigen Anwendungen empfiehlt es sich um eine Nachricht an den Benutzer angezeigt wird, wenn Verbindungsprobleme vorliegen. Sie haben verschiedene Möglichkeiten und wann dies erfolgen. Die folgenden Codebeispiele gelten für einen JavaScript-Client mit den generierten Proxy.

- Behandeln der `connectionSlow` Ereignis, um eine Meldung angezeigt, sobald SignalR behoben werden, beachtet bevor es in Verbindung Modus.

    [!code-javascript[Main](handling-connection-lifetime-events/samples/sample2.js)]
- Behandeln der `reconnecting` Ereignis, um eine Meldung angezeigt wird, wenn es sich bei SignalR ist eine Trennung bekannt und wird in Verbindung Modus geschaltet.

    [!code-javascript[Main](handling-connection-lifetime-events/samples/sample3.js)]
- Behandeln der `disconnected` Ereignis zum Anzeigen einer Nachricht, die beim Versuch, die Verbindung wurde überschritten. In diesem Szenario ist die einzige Möglichkeit, erneut eine Verbindung mit dem Server herstellen, die einen Neustart der SignalR-Verbindung durch Aufrufen der `Start` -Methode, die erstellen eine neuen Verbindungs-ID. Im folgenden Codebeispiel wird ein Flag verwendet, um sicherzustellen, dass Sie die Benachrichtigung nur nach einem Timeout erneut eine Verbindung herstellt, nicht nach einem normalen End für die SignalR-Verbindung durch den Aufruf verursacht ausgeben der `Stop` Methode.

    [!code-javascript[Main](handling-connection-lifetime-events/samples/sample4.js)]

<a id="continuousreconnect"></a>

## <a name="how-to-continuously-reconnect"></a>Wiederherstellen der Verbindung fortlaufend

In einigen Anwendungen empfiehlt es sich, automatisch eine Verbindung erneut herzustellen, nachdem verloren gegangen ist und beim Versuch, erneut eine Verbindung herzustellen Timeout. Zu diesem Zweck rufen Sie die `Start` aus Ihrem `Closed` -Ereignishandler (`disconnected` Ereignishandler für JavaScript-Clients). Möglicherweise möchten Sie einen Zeitraum vor dem Aufruf warten `Start` um dies zu verhindern zu häufig Wenn der Server oder die physische Verbindung nicht verfügbar sind. Im folgenden Codebeispiel wird ein JavaScript-Client mit den generierten Proxy.

[!code-javascript[Main](handling-connection-lifetime-events/samples/sample5.js)]

Ein potenzielles Problem bei mobilen Clients berücksichtigen ist, dass die Folge unnötige akkuentleerung fortlaufende erneuten Herstellen einer Verbindung versucht, wenn der Server oder die physische Verbindung nicht verfügbar ist.

<a id="disconnectclientfromserver"></a>

## <a name="how-to-disconnect-a-client-in-server-code"></a>Gewusst wie: Trennen einen Client im Servercode

Eine integrierte Server-API für die Trennung der Clientverbindungen keinen für SignalR-Version 2. Es gibt [Pläne für diese Funktion in Zukunft hinzuzufügen](https://github.com/SignalR/SignalR/issues/2101). Die einfachste Möglichkeit, trennen einen Client vom Server werden in der aktuellen Version von SignalR aus eine Disconnect-Methode auf dem Client implementieren und Aufrufen dieser Methode auf dem Server. Das folgende Codebeispiel zeigt eine Disconnect-Methode für einen JavaScript-Client mit den generierten Proxy.

[!code-javascript[Main](handling-connection-lifetime-events/samples/sample6.js)]

> [!WARNING]
> Sicherheit – weder diese Methode zum Trennen der Clientverbindungen noch die vorgeschlagene integrierte-API geht es um das Szenario der gehackte Clients, die bösartigen Code ausgeführt werden, da die Clients können herzustellen, oder der gehackte Code möglicherweise entfernt. die `stopClient` -Methode, oder ändern welcher Overhead entsteht. Die entsprechende Stelle zum Implementieren von zustandsbehafteten Denial-of-Service (DOS) Protection ist nicht in das Framework oder der Server-Ebene, sondern im Front-End-Infrastruktur.


<a id="detectingreasonfordisconnection"></a>
## <a name="detecting-the-reason-for-a-disconnection"></a>Den Grund für eine Trennung der Verbindung ermitteln

SignalR 2.1 wird eine Überladung hinzugefügt, mit dem Server `OnDisconnect` -Ereignis, das angibt, ob der Client absichtlich getrennt, anstatt ein Timeout erfolgt. Die `StopCalled` Parameter ist "true", wenn der Client explizit die Verbindung geschlossen. In JavaScript, wenn ein Serverfehler den Client die Verbindung trennen, führte die Fehlerinformationen werden übergeben werden an den Client als `$.connection.hub.lastError`.

**Server-Code in c#: `stopCalled` Parameter**

[!code-csharp[Main](handling-connection-lifetime-events/samples/sample7.cs?highlight=1,3)]

**Client-JavaScript-Code: Zugreifen auf `lastError` in die `disconnect` Ereignis.**

[!code-javascript[Main](handling-connection-lifetime-events/samples/sample8.js?highlight=2-3)]

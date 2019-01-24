---
uid: signalr/overview/testing-and-debugging/enabling-signalr-tracing
title: Aktivieren der Ablaufverfolgung für SignalR | Microsoft-Dokumentation
author: bradygaster
description: Dieses Dokument beschreibt, wie Sie aktivieren und Konfigurieren der Ablaufverfolgung für SignalR-Server und Clients. Ablaufverfolgung können Sie Diagnoseinformationen zu Ereignissen anzeigen...
ms.author: bradyg
ms.date: 08/08/2014
ms.assetid: 30060acb-be3e-4347-996f-3870f0c37829
msc.legacyurl: /signalr/overview/testing-and-debugging/enabling-signalr-tracing
msc.type: authoredcontent
ms.openlocfilehash: c6515e3653798ef50e2d2dcb7354ffed407a190e
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836466"
---
<a name="enabling-signalr-tracing"></a>Aktivieren der Ablaufverfolgung für SignalR
====================
durch [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Dieses Dokument beschreibt, wie Sie aktivieren und Konfigurieren der Ablaufverfolgung für SignalR-Server und Clients. Ablaufverfolgung können Sie Diagnoseinformationen über Ereignisse in der SignalR-Anwendung anzeigen.
>
> In diesem Thema wurde ursprünglich von Patrick Fletcher geschrieben.
>
> ## <a name="software-versions-used-in-the-tutorial"></a>Softwareversionen, die in diesem Tutorial verwendet werden.
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - .NET Framework 4.5
> - SignalR-Version 2
>
>
>
> ## <a name="questions-and-comments"></a>Fragen und Kommentare
>
> Lassen Sie Feedback, auf wie Ihnen in diesem Tutorial gefallen hat und was wir in den Kommentaren am unteren Rand der Seite verbessern können. Wenn Sie Fragen, die nicht direkt mit dem Tutorial verknüpft sind haben, können Sie sie veröffentlichen das [ASP.NET SignalR-Forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) oder [StackOverflow.com](http://stackoverflow.com/).


Wenn die Ablaufverfolgung aktiviert ist, erstellt eine SignalR-Anwendung Protokolleinträge für Ereignisse. Sie können Ereignisse aus dem Client und dem Server protokollieren. Ablaufverfolgung für die Serververbindung für Protokolle, mit horizontaler Skalierung-Anbieter und Message Bus-Ereignisse. Ablaufverfolgung für die Client-Protokolle-Verbindungsereignisse. In SignalR 2.1 und höher, protokolliert der Ablaufverfolgung auf dem Client den vollständigen Inhalt des Hub-Aufruf-Nachrichten.

## <a name="contents"></a>Inhalt

- [Aktivieren der Ablaufverfolgung auf dem server](#server)

    - [Protokollierung von Server-Ereignisse in Textdateien](#server_text)
    - [Server-Ereignisse protokollieren im Ereignisprotokoll](#server_eventlog)
- [Aktivieren der Ablaufverfolgung in der .NET Client (Windows-Desktop-apps)](#net_client)

    - [Protokollieren von Ereignissen an die Konsole Desktop-client](#desktop_console)
    - [Protokollieren von Ereignissen in eine Textdatei-Desktopclient](#desktop_text)
- [Aktivieren der Ablaufverfolgung in Windows Phone 8-clients](#phone)

    - [Protokollierung von Ereignissen für Windows Phone-Client an die Benutzeroberfläche](#phone_ui)
    - [Windows Phone-Clientereignisse Anmelden die Debugging-Konsole](#phone_debug)
- [Aktivieren der Ablaufverfolgung in der JavaScript-client](#javascript)

<a id="server"></a>
## <a name="enabling-tracing-on-the-server"></a>Aktivieren der Ablaufverfolgung auf dem server

Sie Aktivieren der Ablaufverfolgung auf dem Server in der Anwendungskonfigurationsdatei ("App.config" oder "Web.config", je nach Art des Projekts.) Sie angeben, welche Kategorien von Ereignissen, die Sie protokollieren möchten. In der Konfigurationsdatei, Sie auch angeben, ob die Ereignisse zu protokollieren, um eine Textdatei, die Windows-Ereignisprotokoll oder ein benutzerdefiniertes Protokoll, das mit einer Implementierung des [TraceListener](https://msdn.microsoft.com/library/system.diagnostics.tracelistener(v=vs.110).aspx).

Die Server-Ereigniskategorien umfassen die folgenden Arten von Nachrichten:

| Source | Mitteilungen |
| --- | --- |
| SignalR.SqlMessageBus | Anbieter-Setup für SQL-Nachrichtenbus mit horizontaler Skalierung, Database-Vorgang, Fehler und Timeout-Ereignisse |
| SignalR.ServiceBusMessageBus | Service Bus mit horizontaler Skalierung-Anbieter-Topic erstellen und Abonnements, Fehler und messagingereignisse |
| SignalR.RedisMessageBus | Redis-Verbindung trennen und Fehler Anbieterereignisse des mit horizontaler Skalierung |
| SignalR.ScaleoutMessageBus | Messagingereignisse mit horizontaler Skalierung |
| SignalR.Transports.WebSocketTransport | WebSocket-Transport-Verbindung, die Verbindungstrennung, messaging und Fehler-Ereignisse |
| SignalR.Transports.ServerSentEventsTransport | ServerSentEvents transport Verbindung, die Verbindungstrennung, messaging und Fehler Ereignisse |
| SignalR.Transports.ForeverFrameTransport | Verbindung trennen, messaging und Fehler Transportereignisse des ForeverFrame |
| SignalR.Transports.LongPollingTransport | Verbindung trennen, messaging und Fehler Transportereignisse des LongPolling |
| SignalR.Transports.TransportHeartBeat | Übertragen Sie die Verbindung trennen und Keepalive-Ereignisse |
| SignalR.ReflectedHubDescriptorProvider | Hub-Ermittlungsereignisse |

<a id="server_text"></a>
### <a name="logging-server-events-to-text-files"></a>Protokollierung von Server-Ereignisse in Textdateien

Der folgende Code zeigt, wie die Ablaufverfolgung für jede Kategorie des Ereignisses aktiviert wird. In diesem Beispiel konfiguriert die Anwendung zum Protokollieren von Ereignissen in Textdateien.

**XML-Server-Code zum Aktivieren der Ablaufverfolgung**

[!code-html[Main](enabling-signalr-tracing/samples/sample1.html)]

Im obigen Code die `SignalRSwitch` Eintrag gibt an, die [TraceLevel](https://msdn.microsoft.com/library/system.diagnostics.tracelevel(v=vs.110).aspx) für Ereignisse, die gesendet, um das angegebene Protokoll verwendet. In diesem Fall es festgelegt ist `Verbose` d.h. alle Debug- und Ablaufverfolgungsmeldungen werden protokolliert.

Die folgende Ausgabe zeigt die Einträge aus der `transports.log.txt` Datei für eine Anwendung mithilfe der oben genannten Konfigurationsdatei. Es wird eine neue Verbindung, eine Verbindung entfernt und Transport Heartbeat-Ereignisse.

[!code-console[Main](enabling-signalr-tracing/samples/sample2.cmd)]

<a id="server_eventlog"></a>
### <a name="logging-server-events-to-the-event-log"></a>Server-Ereignisse protokollieren im Ereignisprotokoll

Ändern Sie zum Protokollieren von Ereignissen in das Ereignisprotokoll, anstatt eine Textdatei, die Werte für die Einträge in der `sharedListeners` Knoten. Der folgende Code zeigt, wie Serverereignisse im Ereignisprotokoll protokolliert:

**XML-Server-Code zum Protokollieren von Ereignissen im Ereignisprotokoll**

[!code-xml[Main](enabling-signalr-tracing/samples/sample3.xml)]

Ereignisse werden im Anwendungsprotokoll protokolliert und stehen über die Ereignisanzeige, wie unten dargestellt:

![Ereignisanzeige mit SignalR-Protokollen](enabling-signalr-tracing/_static/image1.png)

> [!NOTE]
> Wenn Sie das Ereignisprotokoll verwenden zu können, legen Sie die **TraceLevel** zu **Fehler** auf die Anzahl der Nachrichten überschaubar zu halten.

<a id="net_client"></a>
## <a name="enabling-tracing-in-the-net-client-windows-desktop-apps"></a>Aktivieren der Ablaufverfolgung in der .NET Client (Windows-Desktop-apps)

Der .NET Client kann Ereignisse protokollieren, auf der Konsole, einer Textdatei oder in ein benutzerdefiniertes Protokoll, das mit einer Implementierung des [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx).

Legen Sie zum Aktivieren der Protokollierung in der .NET Client, der Verbindungs `TraceLevel` Eigenschaft, um eine [TraceLevels](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.tracelevels(v=vs.118).aspx) Wert und die `TraceWriter` -Eigenschaft auf einen gültigen [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) Instanz.

<a id="desktop_console"></a>
### <a name="logging-desktop-client-events-to-the-console"></a>Protokollieren von Ereignissen an die Konsole Desktop-client

Der folgende C#-Code veranschaulicht das Protokollieren von Ereignissen in der .NET Client an die Konsole:

[!code-csharp[Main](enabling-signalr-tracing/samples/sample4.cs?highlight=2-3)]

<a id="desktop_text"></a>
### <a name="logging-desktop-client-events-to-a-text-file"></a>Protokollieren von Ereignissen in eine Textdatei-Desktopclient

Der folgende C#-Code zeigt, wie für das Protokollieren von Ereignissen in der .NET Client in einer Textdatei gespeichert wird:

[!code-csharp[Main](enabling-signalr-tracing/samples/sample5.cs?highlight=4-5)]

Die folgende Ausgabe zeigt die Einträge aus der `ClientLog.txt` Datei für eine Anwendung mithilfe der oben genannten Konfigurationsdatei. Es zeigt, dass des Clients eine Verbindung mit dem Server, und der Hub, die das Aufrufen einer Clientmethode wird aufgerufen, `addMessage`:

[!code-console[Main](enabling-signalr-tracing/samples/sample6.cmd)]

<a id="phone"></a>
## <a name="enabling-tracing-in-windows-phone-8-clients"></a>Aktivieren der Ablaufverfolgung in Windows Phone 8-clients

SignalR-Anwendungen für Windows Phone-apps verwenden die gleichen .NET Client als desktop-apps, aber [Console.Out](https://msdn.microsoft.com/library/system.console.out(v=vs.110).aspx) der und Schreiben in eine Datei mit [StreamWriter](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx) sind nicht verfügbar. Stattdessen müssen Sie zum Erstellen einer benutzerdefinierten Implementierung des [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) für die Ablaufverfolgung.

<a id="phone_ui"></a>
### <a name="logging-windows-phone-client-events-to-the-ui"></a>Protokollierung von Ereignissen für Windows Phone-Client an die Benutzeroberfläche

Die [SignalR Codebasis](https://github.com/SignalR/SignalR/archive/master.zip) enthält ein Beispiel für Windows Phone, die Ausgabe der Ablaufverfolgung an schreibt eine [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) mithilfe einer benutzerdefinierten [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) Implementierung mit dem Namen `TextBlockWriter`. Diese Klasse finden Sie in der **samples/Microsoft.AspNet.SignalR.Client.WP8.Samples** Projekt. Beim Erstellen einer Instanz von `TextBlockWriter`, übergeben Sie in der aktuellen [SynchronizationContext](https://msdn.microsoft.com/library/system.threading.synchronizationcontext(v=vs.110).aspx), und ein [StackPanel](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.stackpanel.aspx) , in dem es erstellt eine [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) für Ablaufverfolgung Ausgabe:

[!code-csharp[Main](enabling-signalr-tracing/samples/sample7.cs)]

Die Ausgabe der Ablaufverfolgung wird dann in ein neues geschrieben [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) erstellt, der [StackPanel](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.stackpanel.aspx) im übergeben:

![](enabling-signalr-tracing/_static/image2.png)

<a id="phone_debug"></a>
### <a name="logging-windows-phone-client-events-to-the-debug-console"></a>Windows Phone-Clientereignisse Anmelden die Debugging-Konsole

Um die Ausgabe an die Benutzeroberfläche, anstatt die Debugging-Konsole zu senden, erstellen Sie eine Implementierung von [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) , die im Debugfenster ausgegeben, und weisen Sie es an der Verbindungs des [TraceWriter](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.connection.tracewriter(v=vs.118).aspx) Eigenschaft:

[!code-csharp[Main](enabling-signalr-tracing/samples/sample8.cs)]

Ablaufverfolgungsinformationen werden dann im Debugfenster in Visual Studio geschrieben:

![](enabling-signalr-tracing/_static/image3.png)

<a id="javascript"></a>
## <a name="enabling-tracing-in-the-javascript-client"></a>Aktivieren der Ablaufverfolgung in der JavaScript-client

Legen Sie zum Aktivieren der clientseitigen Protokollierung für eine Verbindung der `logging` Eigenschaft für das Verbindungsobjekt vor dem Aufruf der `start` Methode zum Herstellen der Verbindung.

**Client-JavaScript-Code für das Aktivieren der Ablaufverfolgung in der Browserkonsole "(mit den generierten Proxy)**

[!code-javascript[Main](enabling-signalr-tracing/samples/sample9.js?highlight=1)]

**Client-JavaScript-Code für das Aktivieren der Ablaufverfolgung in der Browserkonsole "(ohne den generierten Proxy)**

[!code-javascript[Main](enabling-signalr-tracing/samples/sample10.js?highlight=2)]

Wenn Ablaufverfolgung aktiviert ist, protokolliert der JavaScript-Client Ereignisse in der Browserkonsole. Um die Browserkonsole zugreifen zu können, finden Sie unter [Überwachung Transporte](../getting-started/introduction-to-signalr.md#MonitoringTransports).

Der folgende Screenshot zeigt einen SignalR-JavaScript-Client mit aktivierter Ablaufverfolgung. Sie zeigt Verbindung und Hub-Aufruf-Ereignisse in der Browserkonsole angezeigt:

![SignalR-Ablaufverfolgungsereignisse in der Browserkonsole.](enabling-signalr-tracing/_static/image4.png)

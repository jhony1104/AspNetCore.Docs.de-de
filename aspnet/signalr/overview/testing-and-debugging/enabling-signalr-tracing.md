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
<a name="enabling-signalr-tracing"></a><span data-ttu-id="95410-104">Aktivieren der Ablaufverfolgung für SignalR</span><span class="sxs-lookup"><span data-stu-id="95410-104">Enabling SignalR Tracing</span></span>
====================
<span data-ttu-id="95410-105">durch [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="95410-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="95410-106">Dieses Dokument beschreibt, wie Sie aktivieren und Konfigurieren der Ablaufverfolgung für SignalR-Server und Clients.</span><span class="sxs-lookup"><span data-stu-id="95410-106">This document describes how to enable and configure tracing for SignalR servers and clients.</span></span> <span data-ttu-id="95410-107">Ablaufverfolgung können Sie Diagnoseinformationen über Ereignisse in der SignalR-Anwendung anzeigen.</span><span class="sxs-lookup"><span data-stu-id="95410-107">Tracing enables you to view diagnostic information about events in your SignalR application.</span></span>
>
> <span data-ttu-id="95410-108">In diesem Thema wurde ursprünglich von Patrick Fletcher geschrieben.</span><span class="sxs-lookup"><span data-stu-id="95410-108">This topic was originally written by Patrick Fletcher.</span></span>
>
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="95410-109">Softwareversionen, die in diesem Tutorial verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="95410-109">Software versions used in the tutorial</span></span>
>
>
> - [<span data-ttu-id="95410-110">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="95410-110">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="95410-111">.NET Framework 4.5</span><span class="sxs-lookup"><span data-stu-id="95410-111">.NET Framework 4.5</span></span>
> - <span data-ttu-id="95410-112">SignalR-Version 2</span><span class="sxs-lookup"><span data-stu-id="95410-112">SignalR version 2</span></span>
>
>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="95410-113">Fragen und Kommentare</span><span class="sxs-lookup"><span data-stu-id="95410-113">Questions and comments</span></span>
>
> <span data-ttu-id="95410-114">Lassen Sie Feedback, auf wie Ihnen in diesem Tutorial gefallen hat und was wir in den Kommentaren am unteren Rand der Seite verbessern können.</span><span class="sxs-lookup"><span data-stu-id="95410-114">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="95410-115">Wenn Sie Fragen, die nicht direkt mit dem Tutorial verknüpft sind haben, können Sie sie veröffentlichen das [ASP.NET SignalR-Forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) oder [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="95410-115">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


<span data-ttu-id="95410-116">Wenn die Ablaufverfolgung aktiviert ist, erstellt eine SignalR-Anwendung Protokolleinträge für Ereignisse.</span><span class="sxs-lookup"><span data-stu-id="95410-116">When tracing is enabled, a SignalR application creates log entries for events.</span></span> <span data-ttu-id="95410-117">Sie können Ereignisse aus dem Client und dem Server protokollieren.</span><span class="sxs-lookup"><span data-stu-id="95410-117">You can log events from both the client and the server.</span></span> <span data-ttu-id="95410-118">Ablaufverfolgung für die Serververbindung für Protokolle, mit horizontaler Skalierung-Anbieter und Message Bus-Ereignisse.</span><span class="sxs-lookup"><span data-stu-id="95410-118">Tracing on the server logs connection, scaleout provider, and message bus events.</span></span> <span data-ttu-id="95410-119">Ablaufverfolgung für die Client-Protokolle-Verbindungsereignisse.</span><span class="sxs-lookup"><span data-stu-id="95410-119">Tracing on the client logs connection events.</span></span> <span data-ttu-id="95410-120">In SignalR 2.1 und höher, protokolliert der Ablaufverfolgung auf dem Client den vollständigen Inhalt des Hub-Aufruf-Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="95410-120">In SignalR 2.1 and later, tracing on the client logs the full content of hub invocation messages.</span></span>

## <a name="contents"></a><span data-ttu-id="95410-121">Inhalt</span><span class="sxs-lookup"><span data-stu-id="95410-121">Contents</span></span>

- [<span data-ttu-id="95410-122">Aktivieren der Ablaufverfolgung auf dem server</span><span class="sxs-lookup"><span data-stu-id="95410-122">Enabling tracing on the server</span></span>](#server)

    - [<span data-ttu-id="95410-123">Protokollierung von Server-Ereignisse in Textdateien</span><span class="sxs-lookup"><span data-stu-id="95410-123">Logging server events to text files</span></span>](#server_text)
    - [<span data-ttu-id="95410-124">Server-Ereignisse protokollieren im Ereignisprotokoll</span><span class="sxs-lookup"><span data-stu-id="95410-124">Logging server events to the event log</span></span>](#server_eventlog)
- [<span data-ttu-id="95410-125">Aktivieren der Ablaufverfolgung in der .NET Client (Windows-Desktop-apps)</span><span class="sxs-lookup"><span data-stu-id="95410-125">Enabling tracing in the .NET client (Windows Desktop apps)</span></span>](#net_client)

    - [<span data-ttu-id="95410-126">Protokollieren von Ereignissen an die Konsole Desktop-client</span><span class="sxs-lookup"><span data-stu-id="95410-126">Logging Desktop client events to the console</span></span>](#desktop_console)
    - [<span data-ttu-id="95410-127">Protokollieren von Ereignissen in eine Textdatei-Desktopclient</span><span class="sxs-lookup"><span data-stu-id="95410-127">Logging Desktop client events to a text file</span></span>](#desktop_text)
- [<span data-ttu-id="95410-128">Aktivieren der Ablaufverfolgung in Windows Phone 8-clients</span><span class="sxs-lookup"><span data-stu-id="95410-128">Enabling tracing in Windows Phone 8 clients</span></span>](#phone)

    - [<span data-ttu-id="95410-129">Protokollierung von Ereignissen für Windows Phone-Client an die Benutzeroberfläche</span><span class="sxs-lookup"><span data-stu-id="95410-129">Logging Windows Phone client events to the UI</span></span>](#phone_ui)
    - [<span data-ttu-id="95410-130">Windows Phone-Clientereignisse Anmelden die Debugging-Konsole</span><span class="sxs-lookup"><span data-stu-id="95410-130">Logging Windows Phone client events to the debug console</span></span>](#phone_debug)
- [<span data-ttu-id="95410-131">Aktivieren der Ablaufverfolgung in der JavaScript-client</span><span class="sxs-lookup"><span data-stu-id="95410-131">Enabling tracing in the JavaScript client</span></span>](#javascript)

<a id="server"></a>
## <a name="enabling-tracing-on-the-server"></a><span data-ttu-id="95410-132">Aktivieren der Ablaufverfolgung auf dem server</span><span class="sxs-lookup"><span data-stu-id="95410-132">Enabling tracing on the server</span></span>

<span data-ttu-id="95410-133">Sie Aktivieren der Ablaufverfolgung auf dem Server in der Anwendungskonfigurationsdatei ("App.config" oder "Web.config", je nach Art des Projekts.) Sie angeben, welche Kategorien von Ereignissen, die Sie protokollieren möchten.</span><span class="sxs-lookup"><span data-stu-id="95410-133">You enable tracing on the server within the application's configuration file (either App.config or Web.config depending on the type of project.) You specify which categories of events you want to log.</span></span> <span data-ttu-id="95410-134">In der Konfigurationsdatei, Sie auch angeben, ob die Ereignisse zu protokollieren, um eine Textdatei, die Windows-Ereignisprotokoll oder ein benutzerdefiniertes Protokoll, das mit einer Implementierung des [TraceListener](https://msdn.microsoft.com/library/system.diagnostics.tracelistener(v=vs.110).aspx).</span><span class="sxs-lookup"><span data-stu-id="95410-134">In the configuration file, you also specify whether to log the events to a text file, the Windows event log, or a custom log using an implementation of [TraceListener](https://msdn.microsoft.com/library/system.diagnostics.tracelistener(v=vs.110).aspx).</span></span>

<span data-ttu-id="95410-135">Die Server-Ereigniskategorien umfassen die folgenden Arten von Nachrichten:</span><span class="sxs-lookup"><span data-stu-id="95410-135">The server event categories include the following sorts of messages:</span></span>

| <span data-ttu-id="95410-136">Source</span><span class="sxs-lookup"><span data-stu-id="95410-136">Source</span></span> | <span data-ttu-id="95410-137">Mitteilungen</span><span class="sxs-lookup"><span data-stu-id="95410-137">Messages</span></span> |
| --- | --- |
| <span data-ttu-id="95410-138">SignalR.SqlMessageBus</span><span class="sxs-lookup"><span data-stu-id="95410-138">SignalR.SqlMessageBus</span></span> | <span data-ttu-id="95410-139">Anbieter-Setup für SQL-Nachrichtenbus mit horizontaler Skalierung, Database-Vorgang, Fehler und Timeout-Ereignisse</span><span class="sxs-lookup"><span data-stu-id="95410-139">SQL Message Bus scaleout provider setup, database operation, error, and timeout events</span></span> |
| <span data-ttu-id="95410-140">SignalR.ServiceBusMessageBus</span><span class="sxs-lookup"><span data-stu-id="95410-140">SignalR.ServiceBusMessageBus</span></span> | <span data-ttu-id="95410-141">Service Bus mit horizontaler Skalierung-Anbieter-Topic erstellen und Abonnements, Fehler und messagingereignisse</span><span class="sxs-lookup"><span data-stu-id="95410-141">Service bus scaleout provider topic creation and subscription, error, and messaging events</span></span> |
| <span data-ttu-id="95410-142">SignalR.RedisMessageBus</span><span class="sxs-lookup"><span data-stu-id="95410-142">SignalR.RedisMessageBus</span></span> | <span data-ttu-id="95410-143">Redis-Verbindung trennen und Fehler Anbieterereignisse des mit horizontaler Skalierung</span><span class="sxs-lookup"><span data-stu-id="95410-143">Redis scaleout provider connection, disconnection, and error events</span></span> |
| <span data-ttu-id="95410-144">SignalR.ScaleoutMessageBus</span><span class="sxs-lookup"><span data-stu-id="95410-144">SignalR.ScaleoutMessageBus</span></span> | <span data-ttu-id="95410-145">Messagingereignisse mit horizontaler Skalierung</span><span class="sxs-lookup"><span data-stu-id="95410-145">Scaleout messaging events</span></span> |
| <span data-ttu-id="95410-146">SignalR.Transports.WebSocketTransport</span><span class="sxs-lookup"><span data-stu-id="95410-146">SignalR.Transports.WebSocketTransport</span></span> | <span data-ttu-id="95410-147">WebSocket-Transport-Verbindung, die Verbindungstrennung, messaging und Fehler-Ereignisse</span><span class="sxs-lookup"><span data-stu-id="95410-147">WebSocket transport connection, disconnection, messaging, and error events</span></span> |
| <span data-ttu-id="95410-148">SignalR.Transports.ServerSentEventsTransport</span><span class="sxs-lookup"><span data-stu-id="95410-148">SignalR.Transports.ServerSentEventsTransport</span></span> | <span data-ttu-id="95410-149">ServerSentEvents transport Verbindung, die Verbindungstrennung, messaging und Fehler Ereignisse</span><span class="sxs-lookup"><span data-stu-id="95410-149">ServerSentEvents transport connection, disconnection, messaging, and error events</span></span> |
| <span data-ttu-id="95410-150">SignalR.Transports.ForeverFrameTransport</span><span class="sxs-lookup"><span data-stu-id="95410-150">SignalR.Transports.ForeverFrameTransport</span></span> | <span data-ttu-id="95410-151">Verbindung trennen, messaging und Fehler Transportereignisse des ForeverFrame</span><span class="sxs-lookup"><span data-stu-id="95410-151">ForeverFrame transport connection, disconnection, messaging, and error events</span></span> |
| <span data-ttu-id="95410-152">SignalR.Transports.LongPollingTransport</span><span class="sxs-lookup"><span data-stu-id="95410-152">SignalR.Transports.LongPollingTransport</span></span> | <span data-ttu-id="95410-153">Verbindung trennen, messaging und Fehler Transportereignisse des LongPolling</span><span class="sxs-lookup"><span data-stu-id="95410-153">LongPolling transport connection, disconnection, messaging, and error events</span></span> |
| <span data-ttu-id="95410-154">SignalR.Transports.TransportHeartBeat</span><span class="sxs-lookup"><span data-stu-id="95410-154">SignalR.Transports.TransportHeartBeat</span></span> | <span data-ttu-id="95410-155">Übertragen Sie die Verbindung trennen und Keepalive-Ereignisse</span><span class="sxs-lookup"><span data-stu-id="95410-155">Transport connection, disconnection, and keepalive events</span></span> |
| <span data-ttu-id="95410-156">SignalR.ReflectedHubDescriptorProvider</span><span class="sxs-lookup"><span data-stu-id="95410-156">SignalR.ReflectedHubDescriptorProvider</span></span> | <span data-ttu-id="95410-157">Hub-Ermittlungsereignisse</span><span class="sxs-lookup"><span data-stu-id="95410-157">Hub discovery events</span></span> |

<a id="server_text"></a>
### <a name="logging-server-events-to-text-files"></a><span data-ttu-id="95410-158">Protokollierung von Server-Ereignisse in Textdateien</span><span class="sxs-lookup"><span data-stu-id="95410-158">Logging server events to text files</span></span>

<span data-ttu-id="95410-159">Der folgende Code zeigt, wie die Ablaufverfolgung für jede Kategorie des Ereignisses aktiviert wird.</span><span class="sxs-lookup"><span data-stu-id="95410-159">The following code shows how to enable tracing for each category of event.</span></span> <span data-ttu-id="95410-160">In diesem Beispiel konfiguriert die Anwendung zum Protokollieren von Ereignissen in Textdateien.</span><span class="sxs-lookup"><span data-stu-id="95410-160">This sample configures the application to log events to text files.</span></span>

<span data-ttu-id="95410-161">**XML-Server-Code zum Aktivieren der Ablaufverfolgung**</span><span class="sxs-lookup"><span data-stu-id="95410-161">**XML server code for enabling tracing**</span></span>

[!code-html[Main](enabling-signalr-tracing/samples/sample1.html)]

<span data-ttu-id="95410-162">Im obigen Code die `SignalRSwitch` Eintrag gibt an, die [TraceLevel](https://msdn.microsoft.com/library/system.diagnostics.tracelevel(v=vs.110).aspx) für Ereignisse, die gesendet, um das angegebene Protokoll verwendet.</span><span class="sxs-lookup"><span data-stu-id="95410-162">In the code above, the `SignalRSwitch` entry specifies the [TraceLevel](https://msdn.microsoft.com/library/system.diagnostics.tracelevel(v=vs.110).aspx) used for events sent to the specified log.</span></span> <span data-ttu-id="95410-163">In diesem Fall es festgelegt ist `Verbose` d.h. alle Debug- und Ablaufverfolgungsmeldungen werden protokolliert.</span><span class="sxs-lookup"><span data-stu-id="95410-163">In this case, it is set to `Verbose` which means all debugging and tracing messages are logged.</span></span>

<span data-ttu-id="95410-164">Die folgende Ausgabe zeigt die Einträge aus der `transports.log.txt` Datei für eine Anwendung mithilfe der oben genannten Konfigurationsdatei.</span><span class="sxs-lookup"><span data-stu-id="95410-164">The following output shows entries from the `transports.log.txt` file for an application using the above configuration file.</span></span> <span data-ttu-id="95410-165">Es wird eine neue Verbindung, eine Verbindung entfernt und Transport Heartbeat-Ereignisse.</span><span class="sxs-lookup"><span data-stu-id="95410-165">It shows a new connection, a removed connection, and transport heartbeat events.</span></span>

[!code-console[Main](enabling-signalr-tracing/samples/sample2.cmd)]

<a id="server_eventlog"></a>
### <a name="logging-server-events-to-the-event-log"></a><span data-ttu-id="95410-166">Server-Ereignisse protokollieren im Ereignisprotokoll</span><span class="sxs-lookup"><span data-stu-id="95410-166">Logging server events to the event log</span></span>

<span data-ttu-id="95410-167">Ändern Sie zum Protokollieren von Ereignissen in das Ereignisprotokoll, anstatt eine Textdatei, die Werte für die Einträge in der `sharedListeners` Knoten.</span><span class="sxs-lookup"><span data-stu-id="95410-167">To log events to the event log rather than a text file, change the values for the entries in the `sharedListeners` node.</span></span> <span data-ttu-id="95410-168">Der folgende Code zeigt, wie Serverereignisse im Ereignisprotokoll protokolliert:</span><span class="sxs-lookup"><span data-stu-id="95410-168">The following code shows how to log server events to the event log:</span></span>

<span data-ttu-id="95410-169">**XML-Server-Code zum Protokollieren von Ereignissen im Ereignisprotokoll**</span><span class="sxs-lookup"><span data-stu-id="95410-169">**XML server code for logging events to the event log**</span></span>

[!code-xml[Main](enabling-signalr-tracing/samples/sample3.xml)]

<span data-ttu-id="95410-170">Ereignisse werden im Anwendungsprotokoll protokolliert und stehen über die Ereignisanzeige, wie unten dargestellt:</span><span class="sxs-lookup"><span data-stu-id="95410-170">The events are logged in the Application log, and are available through the Event Viewer, as shown below:</span></span>

![Ereignisanzeige mit SignalR-Protokollen](enabling-signalr-tracing/_static/image1.png)

> [!NOTE]
> <span data-ttu-id="95410-172">Wenn Sie das Ereignisprotokoll verwenden zu können, legen Sie die **TraceLevel** zu **Fehler** auf die Anzahl der Nachrichten überschaubar zu halten.</span><span class="sxs-lookup"><span data-stu-id="95410-172">When using the event log, set the **TraceLevel** to **Error** to keep the number of messages manageable.</span></span>

<a id="net_client"></a>
## <a name="enabling-tracing-in-the-net-client-windows-desktop-apps"></a><span data-ttu-id="95410-173">Aktivieren der Ablaufverfolgung in der .NET Client (Windows-Desktop-apps)</span><span class="sxs-lookup"><span data-stu-id="95410-173">Enabling tracing in the .NET client (Windows Desktop apps)</span></span>

<span data-ttu-id="95410-174">Der .NET Client kann Ereignisse protokollieren, auf der Konsole, einer Textdatei oder in ein benutzerdefiniertes Protokoll, das mit einer Implementierung des [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx).</span><span class="sxs-lookup"><span data-stu-id="95410-174">The .NET client can log events to the console, a text file, or to a custom log using an implementation of [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx).</span></span>

<span data-ttu-id="95410-175">Legen Sie zum Aktivieren der Protokollierung in der .NET Client, der Verbindungs `TraceLevel` Eigenschaft, um eine [TraceLevels](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.tracelevels(v=vs.118).aspx) Wert und die `TraceWriter` -Eigenschaft auf einen gültigen [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) Instanz.</span><span class="sxs-lookup"><span data-stu-id="95410-175">To enable logging in the .NET client, set the connection's `TraceLevel` property to a [TraceLevels](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.tracelevels(v=vs.118).aspx) value, and the `TraceWriter` property to a valid [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) instance.</span></span>

<a id="desktop_console"></a>
### <a name="logging-desktop-client-events-to-the-console"></a><span data-ttu-id="95410-176">Protokollieren von Ereignissen an die Konsole Desktop-client</span><span class="sxs-lookup"><span data-stu-id="95410-176">Logging Desktop client events to the console</span></span>

<span data-ttu-id="95410-177">Der folgende C#-Code veranschaulicht das Protokollieren von Ereignissen in der .NET Client an die Konsole:</span><span class="sxs-lookup"><span data-stu-id="95410-177">The following C# code shows how to log events in the .NET client to the console:</span></span>

[!code-csharp[Main](enabling-signalr-tracing/samples/sample4.cs?highlight=2-3)]

<a id="desktop_text"></a>
### <a name="logging-desktop-client-events-to-a-text-file"></a><span data-ttu-id="95410-178">Protokollieren von Ereignissen in eine Textdatei-Desktopclient</span><span class="sxs-lookup"><span data-stu-id="95410-178">Logging Desktop client events to a text file</span></span>

<span data-ttu-id="95410-179">Der folgende C#-Code zeigt, wie für das Protokollieren von Ereignissen in der .NET Client in einer Textdatei gespeichert wird:</span><span class="sxs-lookup"><span data-stu-id="95410-179">The following C# code shows how to log events in the .NET client to a text file:</span></span>

[!code-csharp[Main](enabling-signalr-tracing/samples/sample5.cs?highlight=4-5)]

<span data-ttu-id="95410-180">Die folgende Ausgabe zeigt die Einträge aus der `ClientLog.txt` Datei für eine Anwendung mithilfe der oben genannten Konfigurationsdatei.</span><span class="sxs-lookup"><span data-stu-id="95410-180">The following output shows entries from the `ClientLog.txt` file for an application using the above configuration file.</span></span> <span data-ttu-id="95410-181">Es zeigt, dass des Clients eine Verbindung mit dem Server, und der Hub, die das Aufrufen einer Clientmethode wird aufgerufen, `addMessage`:</span><span class="sxs-lookup"><span data-stu-id="95410-181">It shows the client connecting to the server, and the hub invoking a client method called `addMessage`:</span></span>

[!code-console[Main](enabling-signalr-tracing/samples/sample6.cmd)]

<a id="phone"></a>
## <a name="enabling-tracing-in-windows-phone-8-clients"></a><span data-ttu-id="95410-182">Aktivieren der Ablaufverfolgung in Windows Phone 8-clients</span><span class="sxs-lookup"><span data-stu-id="95410-182">Enabling tracing in Windows Phone 8 clients</span></span>

<span data-ttu-id="95410-183">SignalR-Anwendungen für Windows Phone-apps verwenden die gleichen .NET Client als desktop-apps, aber [Console.Out](https://msdn.microsoft.com/library/system.console.out(v=vs.110).aspx) der und Schreiben in eine Datei mit [StreamWriter](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx) sind nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="95410-183">SignalR applications for Windows Phone apps use the same .NET client as desktop apps, but [Console.Out](https://msdn.microsoft.com/library/system.console.out(v=vs.110).aspx) and writing to a file with [StreamWriter](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx) are not available.</span></span> <span data-ttu-id="95410-184">Stattdessen müssen Sie zum Erstellen einer benutzerdefinierten Implementierung des [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) für die Ablaufverfolgung.</span><span class="sxs-lookup"><span data-stu-id="95410-184">Instead, you need to create a custom implementation of [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) for tracing.</span></span>

<a id="phone_ui"></a>
### <a name="logging-windows-phone-client-events-to-the-ui"></a><span data-ttu-id="95410-185">Protokollierung von Ereignissen für Windows Phone-Client an die Benutzeroberfläche</span><span class="sxs-lookup"><span data-stu-id="95410-185">Logging Windows Phone client events to the UI</span></span>

<span data-ttu-id="95410-186">Die [SignalR Codebasis](https://github.com/SignalR/SignalR/archive/master.zip) enthält ein Beispiel für Windows Phone, die Ausgabe der Ablaufverfolgung an schreibt eine [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) mithilfe einer benutzerdefinierten [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) Implementierung mit dem Namen `TextBlockWriter`.</span><span class="sxs-lookup"><span data-stu-id="95410-186">The [SignalR codebase](https://github.com/SignalR/SignalR/archive/master.zip) includes a Windows Phone sample that writes trace output to a [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) using a custom [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) implementation called `TextBlockWriter`.</span></span> <span data-ttu-id="95410-187">Diese Klasse finden Sie in der **samples/Microsoft.AspNet.SignalR.Client.WP8.Samples** Projekt.</span><span class="sxs-lookup"><span data-stu-id="95410-187">This class can be found in the **samples/Microsoft.AspNet.SignalR.Client.WP8.Samples** project.</span></span> <span data-ttu-id="95410-188">Beim Erstellen einer Instanz von `TextBlockWriter`, übergeben Sie in der aktuellen [SynchronizationContext](https://msdn.microsoft.com/library/system.threading.synchronizationcontext(v=vs.110).aspx), und ein [StackPanel](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.stackpanel.aspx) , in dem es erstellt eine [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) für Ablaufverfolgung Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="95410-188">When creating an instance of `TextBlockWriter`, pass in the current [SynchronizationContext](https://msdn.microsoft.com/library/system.threading.synchronizationcontext(v=vs.110).aspx), and a [StackPanel](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.stackpanel.aspx) where it will create a [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) to use for trace output:</span></span>

[!code-csharp[Main](enabling-signalr-tracing/samples/sample7.cs)]

<span data-ttu-id="95410-189">Die Ausgabe der Ablaufverfolgung wird dann in ein neues geschrieben [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) erstellt, der [StackPanel](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.stackpanel.aspx) im übergeben:</span><span class="sxs-lookup"><span data-stu-id="95410-189">The trace output will then be written to a new [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) created in the [StackPanel](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.stackpanel.aspx) you passed in:</span></span>

![](enabling-signalr-tracing/_static/image2.png)

<a id="phone_debug"></a>
### <a name="logging-windows-phone-client-events-to-the-debug-console"></a><span data-ttu-id="95410-190">Windows Phone-Clientereignisse Anmelden die Debugging-Konsole</span><span class="sxs-lookup"><span data-stu-id="95410-190">Logging Windows Phone client events to the debug console</span></span>

<span data-ttu-id="95410-191">Um die Ausgabe an die Benutzeroberfläche, anstatt die Debugging-Konsole zu senden, erstellen Sie eine Implementierung von [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) , die im Debugfenster ausgegeben, und weisen Sie es an der Verbindungs des [TraceWriter](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.connection.tracewriter(v=vs.118).aspx) Eigenschaft:</span><span class="sxs-lookup"><span data-stu-id="95410-191">To send output to the debug console rather than the UI, create an implementation of [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) that writes to the debug window, and assign it to your connection's [TraceWriter](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.connection.tracewriter(v=vs.118).aspx) property:</span></span>

[!code-csharp[Main](enabling-signalr-tracing/samples/sample8.cs)]

<span data-ttu-id="95410-192">Ablaufverfolgungsinformationen werden dann im Debugfenster in Visual Studio geschrieben:</span><span class="sxs-lookup"><span data-stu-id="95410-192">Trace information will then be written to the debug window in Visual Studio:</span></span>

![](enabling-signalr-tracing/_static/image3.png)

<a id="javascript"></a>
## <a name="enabling-tracing-in-the-javascript-client"></a><span data-ttu-id="95410-193">Aktivieren der Ablaufverfolgung in der JavaScript-client</span><span class="sxs-lookup"><span data-stu-id="95410-193">Enabling tracing in the JavaScript client</span></span>

<span data-ttu-id="95410-194">Legen Sie zum Aktivieren der clientseitigen Protokollierung für eine Verbindung der `logging` Eigenschaft für das Verbindungsobjekt vor dem Aufruf der `start` Methode zum Herstellen der Verbindung.</span><span class="sxs-lookup"><span data-stu-id="95410-194">To enable client-side logging on a connection, set the `logging` property on the connection object before you call the `start` method to establish the connection.</span></span>

<span data-ttu-id="95410-195">**Client-JavaScript-Code für das Aktivieren der Ablaufverfolgung in der Browserkonsole "(mit den generierten Proxy)**</span><span class="sxs-lookup"><span data-stu-id="95410-195">**Client JavaScript code for enabling tracing to the browser console (with the generated proxy)**</span></span>

[!code-javascript[Main](enabling-signalr-tracing/samples/sample9.js?highlight=1)]

<span data-ttu-id="95410-196">**Client-JavaScript-Code für das Aktivieren der Ablaufverfolgung in der Browserkonsole "(ohne den generierten Proxy)**</span><span class="sxs-lookup"><span data-stu-id="95410-196">**Client JavaScript code for enabling tracing to the browser console (without the generated proxy)**</span></span>

[!code-javascript[Main](enabling-signalr-tracing/samples/sample10.js?highlight=2)]

<span data-ttu-id="95410-197">Wenn Ablaufverfolgung aktiviert ist, protokolliert der JavaScript-Client Ereignisse in der Browserkonsole.</span><span class="sxs-lookup"><span data-stu-id="95410-197">When tracing is enabled, the JavaScript client logs events to the browser console.</span></span> <span data-ttu-id="95410-198">Um die Browserkonsole zugreifen zu können, finden Sie unter [Überwachung Transporte](../getting-started/introduction-to-signalr.md#MonitoringTransports).</span><span class="sxs-lookup"><span data-stu-id="95410-198">To access the browser console, see [Monitoring Transports](../getting-started/introduction-to-signalr.md#MonitoringTransports).</span></span>

<span data-ttu-id="95410-199">Der folgende Screenshot zeigt einen SignalR-JavaScript-Client mit aktivierter Ablaufverfolgung.</span><span class="sxs-lookup"><span data-stu-id="95410-199">The following screenshot shows a SignalR JavaScript client with tracing enabled.</span></span> <span data-ttu-id="95410-200">Sie zeigt Verbindung und Hub-Aufruf-Ereignisse in der Browserkonsole angezeigt:</span><span class="sxs-lookup"><span data-stu-id="95410-200">It shows connection and hub invocation events in the browser console:</span></span>

![SignalR-Ablaufverfolgungsereignisse in der Browserkonsole.](enabling-signalr-tracing/_static/image4.png)

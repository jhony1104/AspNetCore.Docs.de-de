---
title: WebSockets-Unterstützung in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie mit WebSockets in ASP.NET beginnen.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: fundamentals/websockets
ms.openlocfilehash: fc07d572116f8eea2b30ea6cf80324e5c66f994c
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963172"
---
# <a name="websockets-support-in-aspnet-core"></a><span data-ttu-id="2fb13-103">WebSockets-Unterstützung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2fb13-103">WebSockets support in ASP.NET Core</span></span>

<span data-ttu-id="2fb13-104">Von [Tom Dykstra](https://github.com/tdykstra) und [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="2fb13-104">By [Tom Dykstra](https://github.com/tdykstra) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="2fb13-105">In diesem Artikel erfahren Sie, wie Sie mit WebSockets in ASP.NET beginnen.</span><span class="sxs-lookup"><span data-stu-id="2fb13-105">This article explains how to get started with WebSockets in ASP.NET Core.</span></span> <span data-ttu-id="2fb13-106">Bei [WebSockets](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) handelt es sich um ein Protokoll, das bidirektionale persistente Kommunikationskanäle über TCP-Verbindungen ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="2fb13-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) is a protocol that enables two-way persistent communication channels over TCP connections.</span></span> <span data-ttu-id="2fb13-107">Es wird in Apps verwendet, die von schneller Kommunikation in Echtzeit profitieren, z.B. Chats, Dashboards und Spiele-Apps.</span><span class="sxs-lookup"><span data-stu-id="2fb13-107">It's used in apps that benefit from fast, real-time communication, such as chat, dashboard, and game apps.</span></span>

<span data-ttu-id="2fb13-108">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="2fb13-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="2fb13-109">[Laufvergleich](#sample-app).</span><span class="sxs-lookup"><span data-stu-id="2fb13-109">[How to run](#sample-app).</span></span>

## SignalR

<span data-ttu-id="2fb13-110">[ASP.NET Core SignalR](xref:signalr/introduction) ist eine Bibliothek, die das Hinzufügen von Echtzeit-Webfunktionalität zu Apps erleichtert.</span><span class="sxs-lookup"><span data-stu-id="2fb13-110">[ASP.NET Core SignalR](xref:signalr/introduction) is a library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="2fb13-111">Sie verwendet wenn möglich immer WebSockets.</span><span class="sxs-lookup"><span data-stu-id="2fb13-111">It uses WebSockets whenever possible.</span></span>

<span data-ttu-id="2fb13-112">Für die meisten Anwendungen empfehlen wir SignalR über RAW-WebSockets.</span><span class="sxs-lookup"><span data-stu-id="2fb13-112">For most applications, we recommend SignalR over raw WebSockets.</span></span> SignalR<span data-ttu-id="2fb13-113"> stellt ein Transportfallback für Umgebungen bereit, in denen WebSockets nicht verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="2fb13-113"> provides transport fallback for environments where WebSockets is not available.</span></span> <span data-ttu-id="2fb13-114">Darüber hinaus bietet es ein einfaches App-Modell für Remoteprozeduraufrufe.</span><span class="sxs-lookup"><span data-stu-id="2fb13-114">It also provides a simple remote procedure call app model.</span></span> <span data-ttu-id="2fb13-115">In den meisten Szenarien hat SignalR außerdem keinen signifikanten Leistungsnachteil gegenüber der Verwendung von RAW-WebSockets.</span><span class="sxs-lookup"><span data-stu-id="2fb13-115">And in most scenarios, SignalR has no significant performance disadvantage compared to using raw WebSockets.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2fb13-116">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="2fb13-116">Prerequisites</span></span>

* <span data-ttu-id="2fb13-117">ASP.NET Core 1.1 oder höher</span><span class="sxs-lookup"><span data-stu-id="2fb13-117">ASP.NET Core 1.1 or later</span></span>
* <span data-ttu-id="2fb13-118">Jedes Betriebssystem, das ASP.NET Core unterstützt:</span><span class="sxs-lookup"><span data-stu-id="2fb13-118">Any OS that supports ASP.NET Core:</span></span>
  
  * <span data-ttu-id="2fb13-119">Windows 7/Windows Server 2008 und höher</span><span class="sxs-lookup"><span data-stu-id="2fb13-119">Windows 7 / Windows Server 2008 or later</span></span>
  * <span data-ttu-id="2fb13-120">Linux</span><span class="sxs-lookup"><span data-stu-id="2fb13-120">Linux</span></span>
  * <span data-ttu-id="2fb13-121">macOS</span><span class="sxs-lookup"><span data-stu-id="2fb13-121">macOS</span></span>
  
* <span data-ttu-id="2fb13-122">Wenn die App unter Windows mit IIS ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="2fb13-122">If the app runs on Windows with IIS:</span></span>

  * <span data-ttu-id="2fb13-123">Windows 8/Windows Server 2012 und höher</span><span class="sxs-lookup"><span data-stu-id="2fb13-123">Windows 8 / Windows Server 2012 or later</span></span>
  * <span data-ttu-id="2fb13-124">IIS 8/IIS 8 Express</span><span class="sxs-lookup"><span data-stu-id="2fb13-124">IIS 8 / IIS 8 Express</span></span>
  * <span data-ttu-id="2fb13-125">WebSockets müssen in IIS aktiviert sein (weitere Informationen finden Sie im Abschnitt [Unterstützung für IIS/IIS Express](#iisiis-express-support)).</span><span class="sxs-lookup"><span data-stu-id="2fb13-125">WebSockets must be enabled (See the [IIS/IIS Express support](#iisiis-express-support) section.).</span></span>
  
* <span data-ttu-id="2fb13-126">Wenn die App unter [HTTP.sys](xref:fundamentals/servers/httpsys) ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="2fb13-126">If the app runs on [HTTP.sys](xref:fundamentals/servers/httpsys):</span></span>

  * <span data-ttu-id="2fb13-127">Windows 8/Windows Server 2012 und höher</span><span class="sxs-lookup"><span data-stu-id="2fb13-127">Windows 8 / Windows Server 2012 or later</span></span>

* <span data-ttu-id="2fb13-128">Weitere Informationen zu unterstützten Browsern finden Sie unter https://caniuse.com/#feat=websockets.</span><span class="sxs-lookup"><span data-stu-id="2fb13-128">For supported browsers, see https://caniuse.com/#feat=websockets.</span></span>

::: moniker range="< aspnetcore-2.1"

## <a name="nuget-package"></a><span data-ttu-id="2fb13-129">NuGet-Paket</span><span class="sxs-lookup"><span data-stu-id="2fb13-129">NuGet package</span></span>

<span data-ttu-id="2fb13-130">Installieren Sie das Paket [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/).</span><span class="sxs-lookup"><span data-stu-id="2fb13-130">Install the [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) package.</span></span>

::: moniker-end

## <a name="configure-the-middleware"></a><span data-ttu-id="2fb13-131">Konfigurieren der Middleware</span><span class="sxs-lookup"><span data-stu-id="2fb13-131">Configure the middleware</span></span>


<span data-ttu-id="2fb13-132">Fügen Sie die WebSockets-Middleware zur `Configure`-Methode der `Startup`-Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="2fb13-132">Add the WebSockets middleware in the `Configure` method of the `Startup` class:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="2fb13-133">Sie können die folgenden Einstellungen konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="2fb13-133">The following settings can be configured:</span></span>

* <span data-ttu-id="2fb13-134">`KeepAliveInterval`: Legt fest, wie oft Ping-Frames an den Client gesendet werden, um sicherzustellen, dass Proxys die Verbindung aufrechterhalten</span><span class="sxs-lookup"><span data-stu-id="2fb13-134">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="2fb13-135">Der Standardwert beträgt zwei Minuten.</span><span class="sxs-lookup"><span data-stu-id="2fb13-135">The default is two minutes.</span></span>
* <span data-ttu-id="2fb13-136">`ReceiveBufferSize`: Legt die Größe des Puffers fest, der zum Empfang von Daten verwendet wird</span><span class="sxs-lookup"><span data-stu-id="2fb13-136">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="2fb13-137">Fortgeschrittene Benutzer müssen diese Angaben möglicherweise ändern, um die Leistung auf Grundlage der Größe ihrer Daten anzupassen.</span><span class="sxs-lookup"><span data-stu-id="2fb13-137">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="2fb13-138">Der Standardwert ist 4 KB.</span><span class="sxs-lookup"><span data-stu-id="2fb13-138">The default is 4 KB.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="2fb13-139">Sie können die folgenden Einstellungen konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="2fb13-139">The following settings can be configured:</span></span>

* <span data-ttu-id="2fb13-140">`KeepAliveInterval`: Legt fest, wie oft Ping-Frames an den Client gesendet werden, um sicherzustellen, dass Proxys die Verbindung aufrechterhalten</span><span class="sxs-lookup"><span data-stu-id="2fb13-140">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="2fb13-141">Der Standardwert beträgt zwei Minuten.</span><span class="sxs-lookup"><span data-stu-id="2fb13-141">The default is two minutes.</span></span>
* <span data-ttu-id="2fb13-142"><xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize>: Legt die Größe des Puffers fest, der zum Empfang von Daten verwendet wird</span><span class="sxs-lookup"><span data-stu-id="2fb13-142"><xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize> - The size of the buffer used to receive data.</span></span> <span data-ttu-id="2fb13-143">Fortgeschrittene Benutzer müssen diese Angaben möglicherweise ändern, um die Leistung auf Grundlage der Größe ihrer Daten anzupassen.</span><span class="sxs-lookup"><span data-stu-id="2fb13-143">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="2fb13-144">Der Standardwert ist 4 KB.</span><span class="sxs-lookup"><span data-stu-id="2fb13-144">The default is 4 KB.</span></span>
* <span data-ttu-id="2fb13-145">`AllowedOrigins` – Eine Liste der zulässigen Origin-Headerwerte für WebSocket-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="2fb13-145">`AllowedOrigins` - A list of allowed Origin header values for WebSocket requests.</span></span> <span data-ttu-id="2fb13-146">Alle Ursprünge sind standardmäßig zulässig.</span><span class="sxs-lookup"><span data-stu-id="2fb13-146">By default, all origins are allowed.</span></span> <span data-ttu-id="2fb13-147">Weitere Informationen finden Sie unter „Beschränkung von WebSocket-Ursprüngen“ weiter unten.</span><span class="sxs-lookup"><span data-stu-id="2fb13-147">See "WebSocket origin restriction" below for details.</span></span>

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a><span data-ttu-id="2fb13-148">Akzeptieren der Anforderungen von WebSocket</span><span class="sxs-lookup"><span data-stu-id="2fb13-148">Accept WebSocket requests</span></span>

<span data-ttu-id="2fb13-149">Prüfen Sie zu einem späteren Zeitpunkt im Lebenszyklus einer Anforderung (z.B. später in der `Configure`-Methode oder in einer Aktionsmethode), ob es sich um eine WebSocket-Anforderung handelt, und akzeptieren Sie diese.</span><span class="sxs-lookup"><span data-stu-id="2fb13-149">Somewhere later in the request life cycle (later in the `Configure` method or in an action method, for example) check if it's a WebSocket request and accept the WebSocket request.</span></span>

<span data-ttu-id="2fb13-150">Das folgende Beispiel ist ein späterer Auszug der `Configure`-Methode.</span><span class="sxs-lookup"><span data-stu-id="2fb13-150">The following example is from later in the `Configure` method:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

<span data-ttu-id="2fb13-151">WebSocket-Anforderungen können bei jeder URL eingehen. Dieser Beispielcode akzeptiert jedoch nur Anforderungen für `/ws`.</span><span class="sxs-lookup"><span data-stu-id="2fb13-151">A WebSocket request could come in on any URL, but this sample code only accepts requests for `/ws`.</span></span>

<span data-ttu-id="2fb13-152">Bei Verwendung eines WebSockets **muss** die Middlewarepipeline während der gesamten Dauer der Verbindung ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="2fb13-152">When using a WebSocket, you **must** keep the middleware pipeline running for the duration of the connection.</span></span> <span data-ttu-id="2fb13-153">Wenn Sie versuchen, eine WebSocket-Nachricht zu senden oder zu empfangen, nachdem die Middlewarepipeline beendet wurde, erhalten Sie möglicherweise eine Ausnahme wie die folgende:</span><span class="sxs-lookup"><span data-stu-id="2fb13-153">If you attempt to send or receive a WebSocket message after the middleware pipeline ends, you may get an exception like the following:</span></span>

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

<span data-ttu-id="2fb13-154">Wenn Sie einen Hintergrunddienst zum Schreiben von Daten an ein WebSocket verwenden, stellen Sie sicher, dass die Middlewarepipeline dauerhaft ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="2fb13-154">If you're using a background service to write data to a WebSocket, make sure you keep the middleware pipeline running.</span></span> <span data-ttu-id="2fb13-155">Verwenden Sie dafür ein <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span><span class="sxs-lookup"><span data-stu-id="2fb13-155">Do this by using a <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span></span> <span data-ttu-id="2fb13-156">Übergeben Sie die `TaskCompletionSource` an Ihren Hintergrunddienst, und lassen Sie sie <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> aufrufen, wenn Sie das WebSocket beenden.</span><span class="sxs-lookup"><span data-stu-id="2fb13-156">Pass the `TaskCompletionSource` to your background service and have it call <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> when you finish with the WebSocket.</span></span> <span data-ttu-id="2fb13-157">Verwenden Sie dann `await` für die <xref:System.Threading.Tasks.TaskCompletionSource%601.Task>-Eigenschaft während der Anforderung, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="2fb13-157">Then `await` the <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> property during the request, as shown in the following example:</span></span>

```csharp
app.Use(async (context, next) => {
    var socket = await context.WebSockets.AcceptWebSocketAsync();
    var socketFinishedTcs = new TaskCompletionSource<object>();

    BackgroundSocketProcessor.AddSocket(socket, socketFinishedTcs); 

    await socketFinishedTcs.Task;
});
```
<span data-ttu-id="2fb13-158">Die „closed“-Ausnahme von WebSocket kann auch auftreten, wenn Sie zu früh von einer Aktionsmethode zurückkehren.</span><span class="sxs-lookup"><span data-stu-id="2fb13-158">The WebSocket closed exception can also happen if you return too soon from an action method.</span></span> <span data-ttu-id="2fb13-159">Wenn Sie einen Socket in einer Aktionsmethode akzeptieren, warten Sie vor dem Zurückkehren von der Aktionsmethode, bis der Code, der den Socket verwendet, abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="2fb13-159">If you accept a socket in an action method, wait for the code that uses the socket to complete before returning from the action method.</span></span>

<span data-ttu-id="2fb13-160">Verwenden Sie nie `Task.Wait()`, `Task.Result` oder ähnliche Blockierungsaufrufe, um auf den Abschluss des Sockets zu warten, da dies zu schwerwiegenden Threadingproblemen führen kann.</span><span class="sxs-lookup"><span data-stu-id="2fb13-160">Never use `Task.Wait()`, `Task.Result`, or similar blocking calls to wait for the socket to complete, as that can cause serious threading issues.</span></span> <span data-ttu-id="2fb13-161">Verwenden Sie immer `await`.</span><span class="sxs-lookup"><span data-stu-id="2fb13-161">Always use `await`.</span></span>

## <a name="send-and-receive-messages"></a><span data-ttu-id="2fb13-162">Senden und Empfangen von Nachrichten</span><span class="sxs-lookup"><span data-stu-id="2fb13-162">Send and receive messages</span></span>

<span data-ttu-id="2fb13-163">Die `AcceptWebSocketAsync`-Methode ändert die TCP-Verbindung in eine WebSocket-Verbindung und stellt ein [WebSocket](/dotnet/core/api/system.net.websockets.websocket)-Objekt bereit.</span><span class="sxs-lookup"><span data-stu-id="2fb13-163">The `AcceptWebSocketAsync` method upgrades the TCP connection to a WebSocket connection and provides a [WebSocket](/dotnet/core/api/system.net.websockets.websocket) object.</span></span> <span data-ttu-id="2fb13-164">Verwenden Sie das `WebSocket`-Objekt, um Nachrichten zu senden und zu empfangen.</span><span class="sxs-lookup"><span data-stu-id="2fb13-164">Use the `WebSocket` object to send and receive messages.</span></span>

<span data-ttu-id="2fb13-165">Der weiter oben gezeigte Code, der WebSocket-Anforderungen akzeptiert, übergibt das `WebSocket`-Objekt an eine `Echo`-Methode.</span><span class="sxs-lookup"><span data-stu-id="2fb13-165">The code shown earlier that accepts the WebSocket request passes the `WebSocket` object to an `Echo` method.</span></span> <span data-ttu-id="2fb13-166">Der Code empfängt eine Nachricht und sendet diese umgehend wieder zurück.</span><span class="sxs-lookup"><span data-stu-id="2fb13-166">The code receives a message and immediately sends back the same message.</span></span> <span data-ttu-id="2fb13-167">Nachrichten werden in einer Schleife gesendet und empfangen, bis der Client die Verbindung schließt:</span><span class="sxs-lookup"><span data-stu-id="2fb13-167">Messages are sent and received in a loop until the client closes the connection:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

<span data-ttu-id="2fb13-168">Wenn Sie die WebSocket-Verbindung vor Beginn der Schleife akzeptieren, endet die Middlewarepipeline.</span><span class="sxs-lookup"><span data-stu-id="2fb13-168">When accepting the WebSocket connection before beginning the loop, the middleware pipeline ends.</span></span> <span data-ttu-id="2fb13-169">Wenn Sie das Socket schließen, wird die Pipeline entladen.</span><span class="sxs-lookup"><span data-stu-id="2fb13-169">Upon closing the socket, the pipeline unwinds.</span></span> <span data-ttu-id="2fb13-170">Das bedeutet, dass sich die Anforderung in der Pipeline nicht mehr weiter bewegt, wenn der WebSocket akzeptiert wird.</span><span class="sxs-lookup"><span data-stu-id="2fb13-170">That is, the request stops moving forward in the pipeline when the WebSocket is accepted.</span></span> <span data-ttu-id="2fb13-171">Wenn die Schleife beendet und der Socket geschlossen ist, wird die Anforderung in der Pipeline weiter verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="2fb13-171">When the loop is finished and the socket is closed, the request proceeds back up the pipeline.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a><span data-ttu-id="2fb13-172">Behandeln der Trennung der Verbindung mit dem Client</span><span class="sxs-lookup"><span data-stu-id="2fb13-172">Handle client disconnects</span></span>

<span data-ttu-id="2fb13-173">Der Server wird nicht automatisch informiert, wenn die Verbindung mit dem Client wegen Konnektivitätsverlust getrennt wird.</span><span class="sxs-lookup"><span data-stu-id="2fb13-173">The server is not automatically informed when the client disconnects due to loss of connectivity.</span></span> <span data-ttu-id="2fb13-174">Der Server empfängt nur dann eine Trennungsnachricht, wenn der Client sie sendet, was bei Verlust der Verbindung nicht möglich ist.</span><span class="sxs-lookup"><span data-stu-id="2fb13-174">The server receives a disconnect message only if the client sends it, which can't be done if the internet connection is lost.</span></span> <span data-ttu-id="2fb13-175">Wenn Sie Maßnahmen ergreifen möchten, wenn dies der Fall ist, legen Sie ein Timeout fest für den Fall, dass innerhalb eines bestimmten Zeitfensters keine Eingabe vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="2fb13-175">If you want to take some action when that happens, set a timeout after nothing is received from the client within a certain time window.</span></span>

<span data-ttu-id="2fb13-176">Wenn der Client nicht immer Nachrichten sendet und Sie kein Timeout festlegen möchten, nur weil die Verbindung in den Leerlauf übergeht, lassen Sie den Client einen Timer verwenden, um alle X Sekunden eine Ping-Nachricht zu senden.</span><span class="sxs-lookup"><span data-stu-id="2fb13-176">If the client isn't always sending messages and you don't want to timeout just because the connection goes idle, have the client use a timer to send a ping message every X seconds.</span></span> <span data-ttu-id="2fb13-177">Wenn auf dem Server nicht innerhalb von 2\*X Sekunden nach einer Nachricht die nächste eingeht, beenden Sie die Verbindung, und melden Sie, dass der Client die Verbindung getrennt hat.</span><span class="sxs-lookup"><span data-stu-id="2fb13-177">On the server, if a message hasn't arrived within 2\*X seconds after the previous one, terminate the connection and report that the client disconnected.</span></span> <span data-ttu-id="2fb13-178">Warten Sie für den doppelten Zeitraum des erwarteten Zeitintervalls, um zusätzliche Zeit für Netzwerkverzögerungen einzuräumen, die die Ping-Nachricht aufhalten könnten.</span><span class="sxs-lookup"><span data-stu-id="2fb13-178">Wait for twice the expected time interval to leave extra time for network delays that might hold up the ping message.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="2fb13-179">Beschränkung von WebSocket-Ursprüngen</span><span class="sxs-lookup"><span data-stu-id="2fb13-179">WebSocket origin restriction</span></span>

<span data-ttu-id="2fb13-180">Der von CORS erzeugte Schutz gilt nicht für WebSockets.</span><span class="sxs-lookup"><span data-stu-id="2fb13-180">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="2fb13-181">Für Browser gilt Folgendes **nicht**:</span><span class="sxs-lookup"><span data-stu-id="2fb13-181">Browsers do **not**:</span></span>

* <span data-ttu-id="2fb13-182">Ausführen von CORS-Preflightanforderungen</span><span class="sxs-lookup"><span data-stu-id="2fb13-182">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="2fb13-183">Berücksichtigen der Einschränkungen, die in den `Access-Control`-Headern bei der Erstellung von WebSocket-Anforderungen angegeben sind</span><span class="sxs-lookup"><span data-stu-id="2fb13-183">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="2fb13-184">Allerdings senden Browser den `Origin`-Header, wenn die WebSocket-Anforderungen ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="2fb13-184">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="2fb13-185">Anwendungen sollten zur Überprüfung dieser Header konfiguriert werden, um sicherzustellen, dass nur WebSockets von den erwarteten Ursprüngen zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="2fb13-185">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="2fb13-186">Wenn Sie Ihren Server unter „https://server.com“ und Ihren Client unter „https://client.com“ hosten, fügen Sie „https://client.com“ zur Liste `AllowedOrigins` hinzu, damit sie von WebSockets überprüft wird.</span><span class="sxs-lookup"><span data-stu-id="2fb13-186">If you're hosting your server on "https://server.com" and hosting your client on "https://client.com", add "https://client.com" to the `AllowedOrigins` list for WebSockets to verify.</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> <span data-ttu-id="2fb13-187">Der `Origin`-Header wird vom Client gesteuert und kann wie der `Referer`-Header überlistet werden.</span><span class="sxs-lookup"><span data-stu-id="2fb13-187">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="2fb13-188">Verwenden Sie diese Header **nicht** als Authentifizierungsmechanismus.</span><span class="sxs-lookup"><span data-stu-id="2fb13-188">Do **not** use these headers as an authentication mechanism.</span></span>

::: moniker-end

## <a name="iisiis-express-support"></a><span data-ttu-id="2fb13-189">Unterstützung für IIS und IIS Express</span><span class="sxs-lookup"><span data-stu-id="2fb13-189">IIS/IIS Express support</span></span>

<span data-ttu-id="2fb13-190">In Windows Server 2012 oder höher und in Windows 8 oder höher mit IIS 8 oder IIS Express 8 oder höher ist die Unterstützung für das WebSocket-Protokoll enthalten.</span><span class="sxs-lookup"><span data-stu-id="2fb13-190">Windows Server 2012 or later and Windows 8 or later with IIS/IIS Express 8 or later has support for the WebSocket protocol.</span></span>

> [!NOTE]
> <span data-ttu-id="2fb13-191">WebSockets sind immer aktiviert, wenn Sie IIS Express verwenden.</span><span class="sxs-lookup"><span data-stu-id="2fb13-191">WebSockets are always enabled when using IIS Express.</span></span>

### <a name="enabling-websockets-on-iis"></a><span data-ttu-id="2fb13-192">Aktivieren von WebSockets in IIS</span><span class="sxs-lookup"><span data-stu-id="2fb13-192">Enabling WebSockets on IIS</span></span>

<span data-ttu-id="2fb13-193">So aktivieren Sie die Unterstützung für das WebSocket-Protokoll unter Windows Server 2012 oder höher:</span><span class="sxs-lookup"><span data-stu-id="2fb13-193">To enable support for the WebSocket protocol on Windows Server 2012 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="2fb13-194">Diese Schritte sind nicht erforderlich, wenn Sie IIS Express verwenden.</span><span class="sxs-lookup"><span data-stu-id="2fb13-194">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="2fb13-195">Verwenden Sie den Assistenten **Rollen und Features hinzufügen** im Menü **Verwalten** oder den Link in **Server-Manager**.</span><span class="sxs-lookup"><span data-stu-id="2fb13-195">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span>
1. <span data-ttu-id="2fb13-196">Klicken Sie auf **Rollenbasierte oder featurebasierte Installation**.</span><span class="sxs-lookup"><span data-stu-id="2fb13-196">Select **Role-based or Feature-based Installation**.</span></span> <span data-ttu-id="2fb13-197">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="2fb13-197">Select **Next**.</span></span>
1. <span data-ttu-id="2fb13-198">Wählen Sie den entsprechenden Server aus (standardmäßig ist der lokale Server ausgewählt).</span><span class="sxs-lookup"><span data-stu-id="2fb13-198">Select the appropriate server (the local server is selected by default).</span></span> <span data-ttu-id="2fb13-199">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="2fb13-199">Select **Next**.</span></span>
1. <span data-ttu-id="2fb13-200">Erweitern Sie **Webserver (IIS)** in der Struktur **Rollen**, und erweitern Sie **Webserver** und anschließend **Anwendungsentwicklung**.</span><span class="sxs-lookup"><span data-stu-id="2fb13-200">Expand **Web Server (IIS)** in the **Roles** tree, expand **Web Server**, and then expand **Application Development**.</span></span>
1. <span data-ttu-id="2fb13-201">Wählen Sie **WebSocket-Protokoll** aus.</span><span class="sxs-lookup"><span data-stu-id="2fb13-201">Select **WebSocket Protocol**.</span></span> <span data-ttu-id="2fb13-202">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="2fb13-202">Select **Next**.</span></span>
1. <span data-ttu-id="2fb13-203">Wenn keine zusätzlichen Features erforderlich sind, klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="2fb13-203">If additional features aren't needed, select **Next**.</span></span>
1. <span data-ttu-id="2fb13-204">Klicken Sie auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="2fb13-204">Select **Install**.</span></span>
1. <span data-ttu-id="2fb13-205">Wenn die Installation abgeschlossen ist, klicken Sie auf **Schließen**, um den Assistenten zu beenden.</span><span class="sxs-lookup"><span data-stu-id="2fb13-205">When the installation completes, select **Close** to exit the wizard.</span></span>

<span data-ttu-id="2fb13-206">So aktivieren Sie die Unterstützung für das WebSocket-Protokoll unter Windows 8 oder höher:</span><span class="sxs-lookup"><span data-stu-id="2fb13-206">To enable support for the WebSocket protocol on Windows 8 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="2fb13-207">Diese Schritte sind nicht erforderlich, wenn Sie IIS Express verwenden.</span><span class="sxs-lookup"><span data-stu-id="2fb13-207">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="2fb13-208">Navigieren Sie zu **Systemsteuerung** > **Programme** > **Programme und Features** > **Windows-Features aktivieren oder deaktivieren** (links auf dem Bildschirm).</span><span class="sxs-lookup"><span data-stu-id="2fb13-208">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="2fb13-209">Öffnen Sie die folgenden Knoten: **Internetinformationsdienste** > **WWW-Dienste** > **Anwendungsentwicklungsfeatures**.</span><span class="sxs-lookup"><span data-stu-id="2fb13-209">Open the following nodes: **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="2fb13-210">Wählen Sie das Feature **WebSocket-Protokoll** aus.</span><span class="sxs-lookup"><span data-stu-id="2fb13-210">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="2fb13-211">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="2fb13-211">Select **OK**.</span></span>

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a><span data-ttu-id="2fb13-212">Deaktivieren von WebSocket bei Verwendung von „socket.io“ in „Node.js“</span><span class="sxs-lookup"><span data-stu-id="2fb13-212">Disable WebSocket when using socket.io on Node.js</span></span>

<span data-ttu-id="2fb13-213">Wenn Sie die WebSocket-Unterstützung in [socket.io](https://socket.io/) in [Node.js](https://nodejs.org/) verwenden, deaktivieren Sie das IIS-WebSocket-Standardmodul mithilfe des `webSocket`-Elements in *web.config* oder *applicationHost.config*. Wenn dieser Schritt nicht durchgeführt wird, versucht das IIS-WebSocket-Modul, die WebSocket-Kommunikation statt Node.js und der App zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="2fb13-213">If using the WebSocket support in [socket.io](https://socket.io/) on [Node.js](https://nodejs.org/), disable the default IIS WebSocket module using the `webSocket` element in *web.config* or *applicationHost.config*. If this step isn't performed, the IIS WebSocket module attempts to handle the WebSocket communication rather than Node.js and the app.</span></span>

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a><span data-ttu-id="2fb13-214">Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="2fb13-214">Sample app</span></span>

<span data-ttu-id="2fb13-215">Die [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples), die in diesem Artikel verwendet wird, ist eine Echo-App.</span><span class="sxs-lookup"><span data-stu-id="2fb13-215">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) that accompanies this article is an echo app.</span></span> <span data-ttu-id="2fb13-216">Sie verfügt über eine Webseite, die WebSocket-Verbindungen herstellt. Der Server schickt alle empfangenen Nachrichten zurück an den Client.</span><span class="sxs-lookup"><span data-stu-id="2fb13-216">It has a web page that makes WebSocket connections, and the server resends any messages it receives back to the client.</span></span> <span data-ttu-id="2fb13-217">Führen Sie die App über eine Eingabeaufforderung aus (sie ist nicht darauf ausgelegt, von Visual Studio mit IIS Express ausgeführt zu werden), und navigieren Sie zu http://localhost:5000.</span><span class="sxs-lookup"><span data-stu-id="2fb13-217">Run the app from a command prompt (it's not set up to run from Visual Studio with IIS Express) and navigate to http://localhost:5000.</span></span> <span data-ttu-id="2fb13-218">Die Webseite zeigt den Verbindungsstatus in der oberen linken Ecke an:</span><span class="sxs-lookup"><span data-stu-id="2fb13-218">The web page shows the connection status in the upper left:</span></span>

![Erster Zustand der Webseite](websockets/_static/start.png)

<span data-ttu-id="2fb13-220">Klicken Sie auf **Connect** (Verbinden), um eine WebSocket-Anforderung an die gezeigte URL zu senden.</span><span class="sxs-lookup"><span data-stu-id="2fb13-220">Select **Connect** to send a WebSocket request to the URL shown.</span></span> <span data-ttu-id="2fb13-221">Geben Sie einen Testtext ein, und klicken Sie auf **Send** (Senden).</span><span class="sxs-lookup"><span data-stu-id="2fb13-221">Enter a test message and select **Send**.</span></span> <span data-ttu-id="2fb13-222">Wenn dies abgeschlossen ist, klicken Sie auf **Close Socket** (Socket schließen).</span><span class="sxs-lookup"><span data-stu-id="2fb13-222">When done, select **Close Socket**.</span></span> <span data-ttu-id="2fb13-223">Der Abschnitt **Kommunikationsprotokoll** meldet jede open-, send- und close-Aktion, wenn diese durchgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="2fb13-223">The **Communication Log** section reports each open, send, and close action as it happens.</span></span>

![Erster Zustand der Webseite](websockets/_static/end.png)


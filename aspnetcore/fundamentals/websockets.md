---
title: WebSockets-Unterstützung in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie mit WebSockets in ASP.NET beginnen.
monikerRange: '>= aspnetcore-1.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 01/17/2019
uid: fundamentals/websockets
ms.openlocfilehash: 76acb9c96ed5e8bbbaf39eeb6cb23307bb44fb8d
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836857"
---
# <a name="websockets-support-in-aspnet-core"></a><span data-ttu-id="ed6b0-103">WebSockets-Unterstützung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ed6b0-103">WebSockets support in ASP.NET Core</span></span>

<span data-ttu-id="ed6b0-104">Von [Tom Dykstra](https://github.com/tdykstra) und [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="ed6b0-104">By [Tom Dykstra](https://github.com/tdykstra) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="ed6b0-105">In diesem Artikel erfahren Sie, wie Sie mit WebSockets in ASP.NET beginnen.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-105">This article explains how to get started with WebSockets in ASP.NET Core.</span></span> <span data-ttu-id="ed6b0-106">Bei [WebSockets](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) handelt es sich um ein Protokoll, das bidirektionale persistente Kommunikationskanäle über TCP-Verbindungen ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) is a protocol that enables two-way persistent communication channels over TCP connections.</span></span> <span data-ttu-id="ed6b0-107">Es wird in Apps verwendet, die von schneller Kommunikation in Echtzeit profitieren, z.B. Chats, Dashboards und Spiele-Apps.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-107">It's used in apps that benefit from fast, real-time communication, such as chat, dashboard, and game apps.</span></span>

<span data-ttu-id="ed6b0-108">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ed6b0-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="ed6b0-109">Weitere Informationen finden Sie im Abschnitt [Nächste Schritte](#next-steps).</span><span class="sxs-lookup"><span data-stu-id="ed6b0-109">See the [Next steps](#next-steps) section for more information.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ed6b0-110">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="ed6b0-110">Prerequisites</span></span>

* <span data-ttu-id="ed6b0-111">ASP.NET Core 1.1 oder höher</span><span class="sxs-lookup"><span data-stu-id="ed6b0-111">ASP.NET Core 1.1 or later</span></span>
* <span data-ttu-id="ed6b0-112">Jedes Betriebssystem, das ASP.NET Core unterstützt:</span><span class="sxs-lookup"><span data-stu-id="ed6b0-112">Any OS that supports ASP.NET Core:</span></span>
  
  * <span data-ttu-id="ed6b0-113">Windows 7/Windows Server 2008 und höher</span><span class="sxs-lookup"><span data-stu-id="ed6b0-113">Windows 7 / Windows Server 2008 or later</span></span>
  * <span data-ttu-id="ed6b0-114">Linux</span><span class="sxs-lookup"><span data-stu-id="ed6b0-114">Linux</span></span>
  * <span data-ttu-id="ed6b0-115">macOS</span><span class="sxs-lookup"><span data-stu-id="ed6b0-115">macOS</span></span>
  
* <span data-ttu-id="ed6b0-116">Wenn die App unter Windows mit IIS ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="ed6b0-116">If the app runs on Windows with IIS:</span></span>

  * <span data-ttu-id="ed6b0-117">Windows 8/Windows Server 2012 und höher</span><span class="sxs-lookup"><span data-stu-id="ed6b0-117">Windows 8 / Windows Server 2012 or later</span></span>
  * <span data-ttu-id="ed6b0-118">IIS 8/IIS 8 Express</span><span class="sxs-lookup"><span data-stu-id="ed6b0-118">IIS 8 / IIS 8 Express</span></span>
  * <span data-ttu-id="ed6b0-119">WebSockets müssen in IIS aktiviert sein (weitere Informationen finden Sie im Abschnitt [Unterstützung für IIS/IIS Express](#iisiis-express-support)).</span><span class="sxs-lookup"><span data-stu-id="ed6b0-119">WebSockets must be enabled (See the [IIS/IIS Express support](#iisiis-express-support) section.).</span></span>
  
* <span data-ttu-id="ed6b0-120">Wenn die App unter [HTTP.sys](xref:fundamentals/servers/httpsys) ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="ed6b0-120">If the app runs on [HTTP.sys](xref:fundamentals/servers/httpsys):</span></span>

  * <span data-ttu-id="ed6b0-121">Windows 8/Windows Server 2012 und höher</span><span class="sxs-lookup"><span data-stu-id="ed6b0-121">Windows 8 / Windows Server 2012 or later</span></span>

* <span data-ttu-id="ed6b0-122">Weitere Informationen zu unterstützten Browsern finden Sie unter https://caniuse.com/#feat=websockets.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-122">For supported browsers, see https://caniuse.com/#feat=websockets.</span></span>

## <a name="when-to-use-websockets"></a><span data-ttu-id="ed6b0-123">Verwendungszweck von WebSockets</span><span class="sxs-lookup"><span data-stu-id="ed6b0-123">When to use WebSockets</span></span>

<span data-ttu-id="ed6b0-124">Verwenden Sie WebSockets, um direkt mit einer Socketverbindung zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-124">Use WebSockets to work directly with a socket connection.</span></span> <span data-ttu-id="ed6b0-125">Verwenden Sie WebSockets beispielsweise für die bestmögliche Leistung bei einem Echtzeitspiel.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-125">For example, use WebSockets for the best possible performance with a real-time game.</span></span>

<span data-ttu-id="ed6b0-126">[ASP.NET Core SignalR](xref:signalr/introduction) ist eine Bibliothek, die das Hinzufügen von Echtzeitwebfunktionalität zu Apps erleichtert.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-126">[ASP.NET Core SignalR](xref:signalr/introduction) is a library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="ed6b0-127">Sie verwendet wenn möglich immer WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-127">It uses WebSockets whenever possible.</span></span>

## <a name="how-to-use-websockets"></a><span data-ttu-id="ed6b0-128">So verwenden Sie WebSockets</span><span class="sxs-lookup"><span data-stu-id="ed6b0-128">How to use WebSockets</span></span>

* <span data-ttu-id="ed6b0-129">Installieren Sie das Paket [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/).</span><span class="sxs-lookup"><span data-stu-id="ed6b0-129">Install the [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) package.</span></span>
* <span data-ttu-id="ed6b0-130">Konfigurieren Sie die Middleware.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-130">Configure the middleware.</span></span>
* <span data-ttu-id="ed6b0-131">Akzeptieren Sie Anforderungen von WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-131">Accept WebSocket requests.</span></span>
* <span data-ttu-id="ed6b0-132">Senden Sie Nachrichten, und empfangen Sie diese.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-132">Send and receive messages.</span></span>

### <a name="configure-the-middleware"></a><span data-ttu-id="ed6b0-133">Konfigurieren der Middleware</span><span class="sxs-lookup"><span data-stu-id="ed6b0-133">Configure the middleware</span></span>

<span data-ttu-id="ed6b0-134">Fügen Sie die WebSockets-Middleware zur `Configure`-Methode der `Startup`-Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-134">Add the WebSockets middleware in the `Configure` method of the `Startup` class:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](websockets/samples/1.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="ed6b0-135">Sie können die folgenden Einstellungen konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="ed6b0-135">The following settings can be configured:</span></span>

* <span data-ttu-id="ed6b0-136">`KeepAliveInterval`: Legt fest, wie oft Ping-Frames an den Client gesendet werden, um sicherzustellen, dass Proxys die Verbindung aufrechterhalten</span><span class="sxs-lookup"><span data-stu-id="ed6b0-136">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="ed6b0-137">Der Standardwert beträgt zwei Minuten.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-137">The default is two minutes.</span></span>
* <span data-ttu-id="ed6b0-138">`ReceiveBufferSize`: Legt die Größe des Puffers fest, der zum Empfang von Daten verwendet wird</span><span class="sxs-lookup"><span data-stu-id="ed6b0-138">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="ed6b0-139">Fortgeschrittene Benutzer müssen diese Angaben möglicherweise ändern, um die Leistung auf Grundlage der Größe ihrer Daten anzupassen.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-139">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="ed6b0-140">Der Standardwert ist 4 KB.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-140">The default is 4 KB.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="ed6b0-141">Sie können die folgenden Einstellungen konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="ed6b0-141">The following settings can be configured:</span></span>

* <span data-ttu-id="ed6b0-142">`KeepAliveInterval`: Legt fest, wie oft Ping-Frames an den Client gesendet werden, um sicherzustellen, dass Proxys die Verbindung aufrechterhalten</span><span class="sxs-lookup"><span data-stu-id="ed6b0-142">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="ed6b0-143">Der Standardwert beträgt zwei Minuten.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-143">The default is two minutes.</span></span>
* <span data-ttu-id="ed6b0-144">`ReceiveBufferSize`: Legt die Größe des Puffers fest, der zum Empfang von Daten verwendet wird</span><span class="sxs-lookup"><span data-stu-id="ed6b0-144">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="ed6b0-145">Fortgeschrittene Benutzer müssen diese Angaben möglicherweise ändern, um die Leistung auf Grundlage der Größe ihrer Daten anzupassen.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-145">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="ed6b0-146">Der Standardwert ist 4 KB.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-146">The default is 4 KB.</span></span>
* <span data-ttu-id="ed6b0-147">`AllowedOrigins` – Eine Liste der zulässigen Origin-Headerwerte für WebSocket-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-147">`AllowedOrigins` - A list of allowed Origin header values for WebSocket requests.</span></span> <span data-ttu-id="ed6b0-148">Alle Ursprünge sind standardmäßig zulässig.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-148">By default, all origins are allowed.</span></span> <span data-ttu-id="ed6b0-149">Weitere Informationen finden Sie unter „Beschränkung von WebSocket-Ursprüngen“ weiter unten.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-149">See "WebSocket origin restriction" below for details.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](websockets/samples/1.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

::: moniker-end

### <a name="accept-websocket-requests"></a><span data-ttu-id="ed6b0-150">Akzeptieren der Anforderungen von WebSocket</span><span class="sxs-lookup"><span data-stu-id="ed6b0-150">Accept WebSocket requests</span></span>

<span data-ttu-id="ed6b0-151">Prüfen Sie zu einem späteren Zeitpunkt im Lebenszyklus einer Anforderung (z.B. später in der `Configure`-Methode oder in einer MVC-Aktion), ob es sich um eine WebSocket-Anforderung handelt, und akzeptieren Sie diese.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-151">Somewhere later in the request life cycle (later in the `Configure` method or in an MVC action, for example) check if it's a WebSocket request and accept the WebSocket request.</span></span>

<span data-ttu-id="ed6b0-152">Das folgende Beispiel ist ein späterer Auszug der `Configure`-Methode.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-152">The following example is from later in the `Configure` method:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](websockets/samples/1.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

::: moniker-end

<span data-ttu-id="ed6b0-153">WebSocket-Anforderungen können bei jeder URL eingehen. Dieser Beispielcode akzeptiert jedoch nur Anforderungen für `/ws`.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-153">A WebSocket request could come in on any URL, but this sample code only accepts requests for `/ws`.</span></span>

### <a name="send-and-receive-messages"></a><span data-ttu-id="ed6b0-154">Senden und Empfangen von Nachrichten</span><span class="sxs-lookup"><span data-stu-id="ed6b0-154">Send and receive messages</span></span>

<span data-ttu-id="ed6b0-155">Die `AcceptWebSocketAsync`-Methode ändert die TCP-Verbindung in eine WebSocket-Verbindung und stellt ein [WebSocket](/dotnet/core/api/system.net.websockets.websocket)-Objekt bereit.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-155">The `AcceptWebSocketAsync` method upgrades the TCP connection to a WebSocket connection and provides a [WebSocket](/dotnet/core/api/system.net.websockets.websocket) object.</span></span> <span data-ttu-id="ed6b0-156">Verwenden Sie das `WebSocket`-Objekt, um Nachrichten zu senden und zu empfangen.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-156">Use the `WebSocket` object to send and receive messages.</span></span>

<span data-ttu-id="ed6b0-157">Der weiter oben gezeigte Code, der WebSocket-Anforderungen akzeptiert, übergibt das `WebSocket`-Objekt an eine `Echo`-Methode.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-157">The code shown earlier that accepts the WebSocket request passes the `WebSocket` object to an `Echo` method.</span></span> <span data-ttu-id="ed6b0-158">Der Code empfängt eine Nachricht und sendet diese umgehend wieder zurück.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-158">The code receives a message and immediately sends back the same message.</span></span> <span data-ttu-id="ed6b0-159">Nachrichten werden in einer Schleife gesendet und empfangen, bis der Client die Verbindung schließt:</span><span class="sxs-lookup"><span data-stu-id="ed6b0-159">Messages are sent and received in a loop until the client closes the connection:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](websockets/samples/1.x/WebSocketsSample/Startup.cs?name=Echo)]

::: moniker-end

<span data-ttu-id="ed6b0-160">Wenn Sie die WebSocket-Verbindung vor Beginn der Schleife akzeptieren, endet die Middlewarepipeline.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-160">When accepting the WebSocket connection before beginning the loop, the middleware pipeline ends.</span></span> <span data-ttu-id="ed6b0-161">Wenn Sie das Socket schließen, wird die Pipeline entladen.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-161">Upon closing the socket, the pipeline unwinds.</span></span> <span data-ttu-id="ed6b0-162">Das bedeutet, dass sich die Anforderung in der Pipeline nicht mehr weiter bewegt, wenn der WebSocket akzeptiert wird.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-162">That is, the request stops moving forward in the pipeline when the WebSocket is accepted.</span></span> <span data-ttu-id="ed6b0-163">Wenn die Schleife beendet und der Socket geschlossen ist, wird die Anforderung in der Pipeline weiter verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-163">When the loop is finished and the socket is closed, the request proceeds back up the pipeline.</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="handle-client-disconnects"></a><span data-ttu-id="ed6b0-164">Behandeln der Trennung der Verbindung mit dem Client</span><span class="sxs-lookup"><span data-stu-id="ed6b0-164">Handle client disconnects</span></span>

<span data-ttu-id="ed6b0-165">Der Server wird nicht automatisch informiert, wenn die Verbindung mit dem Client wegen Konnektivitätsverlust getrennt wird.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-165">The server is not automatically informed when the client disconnects due to loss of connectivity.</span></span> <span data-ttu-id="ed6b0-166">Der Server empfängt nur dann eine Trennungsnachricht, wenn der Client sie sendet, was bei Verlust der Verbindung nicht möglich ist.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-166">The server receives a disconnect message only if the client sends it, which can't be done if the internet connection is lost.</span></span> <span data-ttu-id="ed6b0-167">Wenn Sie Maßnahmen ergreifen möchten, wenn dies der Fall ist, legen Sie ein Timeout fest für den Fall, dass innerhalb eines bestimmten Zeitfensters keine Eingabe vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-167">If you want to take some action when that happens, set a timeout after nothing is received from the client within a certain time window.</span></span>

<span data-ttu-id="ed6b0-168">Wenn der Client nicht immer Nachrichten sendet und Sie kein Timeout festlegen möchten, nur weil die Verbindung in den Leerlauf übergeht, lassen Sie den Client einen Timer verwenden, um alle X Sekunden eine Ping-Nachricht zu senden.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-168">If the client isn't always sending messages and you don't want to timeout just because the connection goes idle, have the client use a timer to send a ping message every X seconds.</span></span> <span data-ttu-id="ed6b0-169">Wenn auf dem Server nicht innerhalb von 2\*X Sekunden nach einer Nachricht die nächste eingeht, beenden Sie die Verbindung, und melden Sie, dass der Client die Verbindung getrennt hat.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-169">On the server, if a message hasn't arrived within 2\*X seconds after the previous one, terminate the connection and report that the client disconnected.</span></span> <span data-ttu-id="ed6b0-170">Warten Sie für den doppelten Zeitraum des erwarteten Zeitintervalls, um zusätzliche Zeit für Netzwerkverzögerungen einzuräumen, die die Ping-Nachricht aufhalten könnten.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-170">Wait for twice the expected time interval to leave extra time for network delays that might hold up the ping message.</span></span>

### <a name="websocket-origin-restriction"></a><span data-ttu-id="ed6b0-171">Beschränkung von WebSocket-Ursprüngen</span><span class="sxs-lookup"><span data-stu-id="ed6b0-171">WebSocket origin restriction</span></span>

<span data-ttu-id="ed6b0-172">Der von CORS erzeugte Schutz gilt nicht für WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-172">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="ed6b0-173">Für Browser gilt Folgendes **nicht**:</span><span class="sxs-lookup"><span data-stu-id="ed6b0-173">Browsers do **not**:</span></span>

* <span data-ttu-id="ed6b0-174">Ausführen von CORS-Preflightanforderungen</span><span class="sxs-lookup"><span data-stu-id="ed6b0-174">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="ed6b0-175">Berücksichtigen der Einschränkungen, die in den `Access-Control`-Headern bei der Erstellung von WebSocket-Anforderungen angegeben sind</span><span class="sxs-lookup"><span data-stu-id="ed6b0-175">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="ed6b0-176">Allerdings senden Browser den `Origin`-Header, wenn die WebSocket-Anforderungen ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-176">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="ed6b0-177">Anwendungen sollten zur Überprüfung dieser Header konfiguriert werden, um sicherzustellen, dass nur WebSockets von den erwarteten Ursprüngen zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-177">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="ed6b0-178">Wenn Sie Ihren Server unter „https://server.com“ und Ihren Client unter „https://client.com“ hosten, fügen Sie „https://client.com“ zur Liste `AllowedOrigins` hinzu, damit sie von WebSockets überprüft wird.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-178">If you're hosting your server on "https://server.com" and hosting your client on "https://client.com", add "https://client.com" to the `AllowedOrigins` list for WebSockets to verify.</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> <span data-ttu-id="ed6b0-179">Der `Origin`-Header wird vom Client gesteuert und kann wie der `Referer`-Header überlistet werden.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-179">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="ed6b0-180">Verwenden Sie diese Header **nicht** als Authentifizierungsmechanismus.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-180">Do **not** use these headers as an authentication mechanism.</span></span>

::: moniker-end

## <a name="iisiis-express-support"></a><span data-ttu-id="ed6b0-181">Unterstützung für IIS und IIS Express</span><span class="sxs-lookup"><span data-stu-id="ed6b0-181">IIS/IIS Express support</span></span>

<span data-ttu-id="ed6b0-182">In Windows Server 2012 oder höher und in Windows 8 oder höher mit IIS 8 oder IIS Express 8 oder höher ist die Unterstützung für das WebSocket-Protokoll enthalten.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-182">Windows Server 2012 or later and Windows 8 or later with IIS/IIS Express 8 or later has support for the WebSocket protocol.</span></span>

> [!NOTE]
> <span data-ttu-id="ed6b0-183">WebSockets sind immer aktiviert, wenn Sie IIS Express verwenden.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-183">WebSockets are always enabled when using IIS Express.</span></span>

### <a name="enabling-websockets-on-iis"></a><span data-ttu-id="ed6b0-184">Aktivieren von WebSockets in IIS</span><span class="sxs-lookup"><span data-stu-id="ed6b0-184">Enabling WebSockets on IIS</span></span>

<span data-ttu-id="ed6b0-185">So aktivieren Sie die Unterstützung für das WebSocket-Protokoll unter Windows Server 2012 oder höher:</span><span class="sxs-lookup"><span data-stu-id="ed6b0-185">To enable support for the WebSocket protocol on Windows Server 2012 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="ed6b0-186">Diese Schritte sind nicht erforderlich, wenn Sie IIS Express verwenden.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-186">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="ed6b0-187">Verwenden Sie den Assistenten **Rollen und Features hinzufügen** im Menü **Verwalten** oder den Link in **Server-Manager**.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-187">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span>
1. <span data-ttu-id="ed6b0-188">Klicken Sie auf **Rollenbasierte oder featurebasierte Installation**.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-188">Select **Role-based or Feature-based Installation**.</span></span> <span data-ttu-id="ed6b0-189">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-189">Select **Next**.</span></span>
1. <span data-ttu-id="ed6b0-190">Wählen Sie den entsprechenden Server aus (standardmäßig ist der lokale Server ausgewählt).</span><span class="sxs-lookup"><span data-stu-id="ed6b0-190">Select the appropriate server (the local server is selected by default).</span></span> <span data-ttu-id="ed6b0-191">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-191">Select **Next**.</span></span>
1. <span data-ttu-id="ed6b0-192">Erweitern Sie **Webserver (IIS)** in der Struktur **Rollen**, und erweitern Sie **Webserver** und anschließend **Anwendungsentwicklung**.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-192">Expand **Web Server (IIS)** in the **Roles** tree, expand **Web Server**, and then expand **Application Development**.</span></span>
1. <span data-ttu-id="ed6b0-193">Wählen Sie **WebSocket-Protokoll** aus.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-193">Select **WebSocket Protocol**.</span></span> <span data-ttu-id="ed6b0-194">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-194">Select **Next**.</span></span>
1. <span data-ttu-id="ed6b0-195">Wenn keine zusätzlichen Features erforderlich sind, klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-195">If additional features aren't needed, select **Next**.</span></span>
1. <span data-ttu-id="ed6b0-196">Klicken Sie auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-196">Select **Install**.</span></span>
1. <span data-ttu-id="ed6b0-197">Wenn die Installation abgeschlossen ist, klicken Sie auf **Schließen**, um den Assistenten zu beenden.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-197">When the installation completes, select **Close** to exit the wizard.</span></span>

<span data-ttu-id="ed6b0-198">So aktivieren Sie die Unterstützung für das WebSocket-Protokoll unter Windows 8 oder höher:</span><span class="sxs-lookup"><span data-stu-id="ed6b0-198">To enable support for the WebSocket protocol on Windows 8 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="ed6b0-199">Diese Schritte sind nicht erforderlich, wenn Sie IIS Express verwenden.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-199">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="ed6b0-200">Navigieren Sie zu **Systemsteuerung** > **Programme** > **Programme und Features** > **Windows-Features aktivieren oder deaktivieren** (links auf dem Bildschirm).</span><span class="sxs-lookup"><span data-stu-id="ed6b0-200">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="ed6b0-201">Öffnen Sie die folgenden Knoten: **Internetinformationsdienste** > **WWW-Dienste** > **Anwendungsentwicklungsfeatures**.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-201">Open the following nodes: **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="ed6b0-202">Wählen Sie das Feature **WebSocket-Protokoll** aus.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-202">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="ed6b0-203">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-203">Select **OK**.</span></span>

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a><span data-ttu-id="ed6b0-204">Deaktivieren von WebSocket bei Verwendung von „socket.io“ in „Node.js“</span><span class="sxs-lookup"><span data-stu-id="ed6b0-204">Disable WebSocket when using socket.io on Node.js</span></span>

<span data-ttu-id="ed6b0-205">Wenn Sie die WebSocket-Unterstützung in [socket.io](https://socket.io/) in [Node.js](https://nodejs.org/) verwenden, deaktivieren Sie das IIS-WebSocket-Standardmodul mithilfe des `webSocket`-Elements in *web.config* oder *applicationHost.config*. Wenn dieser Schritt nicht durchgeführt wird, versucht das IIS-WebSocket-Modul, die WebSocket-Kommunikation statt Node.js und der App zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-205">If using the WebSocket support in [socket.io](https://socket.io/) on [Node.js](https://nodejs.org/), disable the default IIS WebSocket module using the `webSocket` element in *web.config* or *applicationHost.config*. If this step isn't performed, the IIS WebSocket module attempts to handle the WebSocket communication rather than Node.js and the app.</span></span>

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="next-steps"></a><span data-ttu-id="ed6b0-206">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="ed6b0-206">Next steps</span></span>

<span data-ttu-id="ed6b0-207">Die [Beispiel-App](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/websockets/samples), die in diesem Artikel verwendet wird, ist eine Echo-App.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-207">The [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/websockets/samples) that accompanies this article is an echo app.</span></span> <span data-ttu-id="ed6b0-208">Sie verfügt über eine Webseite, die WebSocket-Verbindungen herstellt. Der Server schickt alle empfangenen Nachrichten zurück an den Client.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-208">It has a web page that makes WebSocket connections, and the server resends any messages it receives back to the client.</span></span> <span data-ttu-id="ed6b0-209">Führen Sie die App über eine Eingabeaufforderung aus (sie ist nicht darauf ausgelegt, von Visual Studio mit IIS Express ausgeführt zu werden), und navigieren Sie zu http://localhost:5000.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-209">Run the app from a command prompt (it's not set up to run from Visual Studio with IIS Express) and navigate to http://localhost:5000.</span></span> <span data-ttu-id="ed6b0-210">Die Webseite zeigt den Verbindungsstatus in der oberen linken Ecke an:</span><span class="sxs-lookup"><span data-stu-id="ed6b0-210">The web page shows the connection status in the upper left:</span></span>

![Erster Zustand der Webseite](websockets/_static/start.png)

<span data-ttu-id="ed6b0-212">Klicken Sie auf **Connect** (Verbinden), um eine WebSocket-Anforderung an die gezeigte URL zu senden.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-212">Select **Connect** to send a WebSocket request to the URL shown.</span></span> <span data-ttu-id="ed6b0-213">Geben Sie einen Testtext ein, und klicken Sie auf **Send** (Senden).</span><span class="sxs-lookup"><span data-stu-id="ed6b0-213">Enter a test message and select **Send**.</span></span> <span data-ttu-id="ed6b0-214">Wenn dies abgeschlossen ist, klicken Sie auf **Close Socket** (Socket schließen).</span><span class="sxs-lookup"><span data-stu-id="ed6b0-214">When done, select **Close Socket**.</span></span> <span data-ttu-id="ed6b0-215">Der Abschnitt **Kommunikationsprotokoll** meldet jede open-, send- und close-Aktion, wenn diese durchgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="ed6b0-215">The **Communication Log** section reports each open, send, and close action as it happens.</span></span>

![Erster Zustand der Webseite](websockets/_static/end.png)

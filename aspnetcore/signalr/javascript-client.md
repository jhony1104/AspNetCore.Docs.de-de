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
# <a name="aspnet-core-signalr-javascript-client"></a><span data-ttu-id="94871-103">ASP.NET Core SignalR-JavaScript-client</span><span class="sxs-lookup"><span data-stu-id="94871-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="94871-104">Von [Rachel Appel](http://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="94871-104">By [Rachel Appel](http://twitter.com/rachelappel)</span></span>

<span data-ttu-id="94871-105">Die ASP.NET Core SignalR JavaScript-Clientbibliothek ermöglicht Entwicklern von serverseitigen hubcode aufrufen.</span><span class="sxs-lookup"><span data-stu-id="94871-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="94871-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="94871-106">[View or download sample code](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-client-package"></a><span data-ttu-id="94871-107">Installieren Sie das Paket der SignalR-client</span><span class="sxs-lookup"><span data-stu-id="94871-107">Install the SignalR client package</span></span>

<span data-ttu-id="94871-108">Die SignalR-JavaScript-Clientbibliothek wird bereitgestellt, als ein [Npm](https://www.npmjs.com/) Paket.</span><span class="sxs-lookup"><span data-stu-id="94871-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="94871-109">Wenn Sie Visual Studio verwenden, führen Sie `npm install` aus der **-Paket-Manager-Konsole** während Sie sich in den Stammordner.</span><span class="sxs-lookup"><span data-stu-id="94871-109">If you're using Visual Studio, run `npm install` from the **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="94871-110">Führen Sie für Visual Studio Code, den Befehl in der **integriertes Terminal**.</span><span class="sxs-lookup"><span data-stu-id="94871-110">For Visual Studio Code, run the command from the **Integrated Terminal**.</span></span>

  ```console
  npm init -y
  npm install @aspnet/signalr
  ```

<span data-ttu-id="94871-111">Npm installiert den Inhalt des Pakets in der *"node_modules"\\@aspnet\signalr\dist\browser* Ordner.</span><span class="sxs-lookup"><span data-stu-id="94871-111">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="94871-112">Erstellen Sie einen neuen Ordner namens *Signalr* unter der *"Wwwroot"\\Lib* Ordner.</span><span class="sxs-lookup"><span data-stu-id="94871-112">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="94871-113">Kopieren der *signalr.js* -Datei in die *Wwwroot\lib\signalr* Ordner.</span><span class="sxs-lookup"><span data-stu-id="94871-113">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

## <a name="use-the-signalr-javascript-client"></a><span data-ttu-id="94871-114">Verwenden Sie den SignalR JavaScript-client</span><span class="sxs-lookup"><span data-stu-id="94871-114">Use the SignalR JavaScript client</span></span>

<span data-ttu-id="94871-115">Verweisen auf den SignalR JavaScript-Client in der `<script>` Element.</span><span class="sxs-lookup"><span data-stu-id="94871-115">Reference the SignalR JavaScript client in the `<script>` element.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

## <a name="connect-to-a-hub"></a><span data-ttu-id="94871-116">Verbinden mit einem hub</span><span class="sxs-lookup"><span data-stu-id="94871-116">Connect to a hub</span></span>

<span data-ttu-id="94871-117">Der folgende Code erstellt und startet eine Verbindung.</span><span class="sxs-lookup"><span data-stu-id="94871-117">The following code creates and starts a connection.</span></span> <span data-ttu-id="94871-118">Die Hub-Name ist Groß-/Kleinschreibung.</span><span class="sxs-lookup"><span data-stu-id="94871-118">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a><span data-ttu-id="94871-119">Cross-Origin-Verbindungen</span><span class="sxs-lookup"><span data-stu-id="94871-119">Cross-origin connections</span></span>

<span data-ttu-id="94871-120">Browser werden in der Regel Verbindungen aus der gleichen Domäne wie die angeforderte Seite laden.</span><span class="sxs-lookup"><span data-stu-id="94871-120">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="94871-121">Es gibt jedoch Situationen, wenn eine Verbindung mit einer anderen Domäne erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="94871-121">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="94871-122">Um zu verhindern, dass eine schädliche Website sensible Daten von einem anderen Standort lesen [Cross-Origin-Verbindungen](xref:security/cors) sind standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="94871-122">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="94871-123">Um eine cors-Anforderung zu ermöglichen, aktivieren Sie ihn in das `Startup` Klasse.</span><span class="sxs-lookup"><span data-stu-id="94871-123">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="94871-124">Aufrufen von Hub-Methoden von client</span><span class="sxs-lookup"><span data-stu-id="94871-124">Call hub methods from client</span></span>

<span data-ttu-id="94871-125">JavaScript-Clients rufen Sie öffentliche Methoden für Hubs über die [Aufrufen](/javascript/api/%40aspnet/signalr/hubconnection#invoke) Methode der [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="94871-125">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="94871-126">Die `invoke` -Methode akzeptiert zwei Argumente:</span><span class="sxs-lookup"><span data-stu-id="94871-126">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="94871-127">Der Name der hubmethode.</span><span class="sxs-lookup"><span data-stu-id="94871-127">The name of the hub method.</span></span> <span data-ttu-id="94871-128">Im folgenden Beispiel wird der Name der Methode auf dem Hub `SendMessage`.</span><span class="sxs-lookup"><span data-stu-id="94871-128">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="94871-129">In der hubmethode definierten Argumente.</span><span class="sxs-lookup"><span data-stu-id="94871-129">Any arguments defined in the hub method.</span></span> <span data-ttu-id="94871-130">Im folgenden Beispiel wird der Name des Arguments `message`.</span><span class="sxs-lookup"><span data-stu-id="94871-130">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="94871-131">Der Beispielcode verwendet die pfeilfunktionssyntax, die in aktuellen Versionen von allen wichtigen Browsern mit Ausnahme von Internet Explorer unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="94871-131">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="94871-132">Bei Verwendung von Azure SignalR Service in *serverlose Modus*, Sie können nicht von einem Client hubmethoden aufrufen.</span><span class="sxs-lookup"><span data-stu-id="94871-132">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="94871-133">Weitere Informationen finden Sie unter den [SignalR Service-Dokumentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="94871-133">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="94871-134">Rufen Sie Client-Methoden von Hub-Instanz</span><span class="sxs-lookup"><span data-stu-id="94871-134">Call client methods from hub</span></span>

<span data-ttu-id="94871-135">Zum Empfangen von Nachrichten aus dem Hub Definieren einer Methode mit dem [auf](/javascript/api/%40aspnet/signalr/hubconnection#on) Methode der `HubConnection`.</span><span class="sxs-lookup"><span data-stu-id="94871-135">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="94871-136">Der Name des Client-JavaScript-Methode.</span><span class="sxs-lookup"><span data-stu-id="94871-136">The name of the JavaScript client method.</span></span> <span data-ttu-id="94871-137">Im folgenden Beispiel wird der Name der Methode `ReceiveMessage`.</span><span class="sxs-lookup"><span data-stu-id="94871-137">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="94871-138">Argumente übergibt der Hub, an die Methode.</span><span class="sxs-lookup"><span data-stu-id="94871-138">Arguments the hub passes to the method.</span></span> <span data-ttu-id="94871-139">Im folgenden Beispiel wird den Wert des Arguments `message`.</span><span class="sxs-lookup"><span data-stu-id="94871-139">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="94871-140">Der vorangehende Code in `connection.on` ausgeführt wird, wenn Sie serverseitigen Code aufruft, mit der [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) Methode.</span><span class="sxs-lookup"><span data-stu-id="94871-140">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="94871-141">SignalR bestimmt, welche Clientmethode aufrufen, die nach übereinstimmenden Namen der Methode und Argumente, die in definierten `SendAsync` und `connection.on`.</span><span class="sxs-lookup"><span data-stu-id="94871-141">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="94871-142">Rufen Sie als bewährte Methode, die [starten](/javascript/api/%40aspnet/signalr/hubconnection#start) Methode für die `HubConnection` nach `on`.</span><span class="sxs-lookup"><span data-stu-id="94871-142">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="94871-143">Dadurch wird sichergestellt, dass die Handler registriert sind, bevor alle Nachrichten empfangen werden.</span><span class="sxs-lookup"><span data-stu-id="94871-143">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="94871-144">Fehlerbehandlung und Protokollierung</span><span class="sxs-lookup"><span data-stu-id="94871-144">Error handling and logging</span></span>

<span data-ttu-id="94871-145">Kette eine `catch` Methode am Ende der `start` Methode zum Behandeln von clientseitigen Fehlern.</span><span class="sxs-lookup"><span data-stu-id="94871-145">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="94871-146">Verwendung `console.error` zu Ausgabefehler in der Browserkonsole.</span><span class="sxs-lookup"><span data-stu-id="94871-146">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

<span data-ttu-id="94871-147">Einrichten von clientseitigen Protokollierung, Ablaufverfolgung durch Übergeben einer Protokollierung und der Typ des Ereignisses zu protokollieren, wenn die Verbindung hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="94871-147">Setup client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="94871-148">Nachrichten werden mit der angegebenen Ebene oder höher protokolliert.</span><span class="sxs-lookup"><span data-stu-id="94871-148">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="94871-149">Verfügbaren Protokollebenen lauten wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="94871-149">Available log levels are as follows:</span></span>

* <span data-ttu-id="94871-150">`signalR.LogLevel.Error` &ndash; Fehlermeldungen.</span><span class="sxs-lookup"><span data-stu-id="94871-150">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="94871-151">Protokolle `Error` nur Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="94871-151">Logs `Error` messages only.</span></span>
* <span data-ttu-id="94871-152">`signalR.LogLevel.Warning` &ndash; Warnmeldungen zu potenziellen Fehlern.</span><span class="sxs-lookup"><span data-stu-id="94871-152">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="94871-153">Protokolle `Warning`, und `Error` Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="94871-153">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="94871-154">`signalR.LogLevel.Information` &ndash; Statusmeldungen, die ohne Fehler.</span><span class="sxs-lookup"><span data-stu-id="94871-154">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="94871-155">Protokolle `Information`, `Warning`, und `Error` Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="94871-155">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="94871-156">`signalR.LogLevel.Trace` &ndash; Ablaufverfolgungsmeldungen Sie aus.</span><span class="sxs-lookup"><span data-stu-id="94871-156">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="94871-157">Protokolliert alle Elemente einschließlich Daten, die zwischen Hub und dem Client übertragen.</span><span class="sxs-lookup"><span data-stu-id="94871-157">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="94871-158">Verwenden der ["configurelogging"](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) Methode [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) so konfigurieren Sie die Protokollebene.</span><span class="sxs-lookup"><span data-stu-id="94871-158">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="94871-159">Nachrichten werden in der Browserkonsole protokolliert.</span><span class="sxs-lookup"><span data-stu-id="94871-159">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="94871-160">Verbinden von clients</span><span class="sxs-lookup"><span data-stu-id="94871-160">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="94871-161">Automatisch erneut eine Verbindung herstellen</span><span class="sxs-lookup"><span data-stu-id="94871-161">Automatically reconnect</span></span>

<span data-ttu-id="94871-162">Für SignalR JavaScript-Client konfiguriert werden kann automatisch verbunden werden, mithilfe der `withAutomaticReconnect` Methode [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="94871-162">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="94871-163">Es wird nicht standardmäßig automatisch wiederherzustellen.</span><span class="sxs-lookup"><span data-stu-id="94871-163">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="94871-164">Ohne Parameter `withAutomaticReconnect()` konfiguriert den Client, um zu warten, 0, 2, 10 und 30 Sekunden bzw. jedem Versuch eine erneute Verbindung beenden nach vier fehlgeschlagenen versuchen.</span><span class="sxs-lookup"><span data-stu-id="94871-164">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="94871-165">Vor jedem erneuten Verbindungsversuche, die `HubConnection` wird der Übergang zu der `HubConnectionState.Reconnecting` Status und ausgelöst werden die `onreconnecting` Rückrufe anstelle der Übergang in die `Disconnected` Zustand und Auslösen von seine `onclose` Rückrufe wie eine `HubConnection`ohne automatische verbindungswiederherstellung konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="94871-165">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="94871-166">Dies bietet die Möglichkeit, Benutzer zu warnen, dass die Verbindung unterbrochen wurde und UI-Elemente zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="94871-166">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting((error) => {
  console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

  document.getElementById("messageInput").disabled = true;

  const li = document.createElement("li");
  li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
  document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="94871-167">Wenn der Client erfolgreich in die ersten vier Versuche, erneut eine Verbindung herstellt der `HubConnection` erfolgt ein Wechsel zurück in die `Connected` Status und ausgelöst werden die `onreconnected` Rückrufe.</span><span class="sxs-lookup"><span data-stu-id="94871-167">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="94871-168">Dies bietet die Möglichkeit, Benutzer zu informieren, die die Verbindung wiederhergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="94871-168">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="94871-169">Da die Verbindung vollständig neu auf dem Server, sieht eine neue `connectionId` bereitgestellt der `onreconnected` Rückruf.</span><span class="sxs-lookup"><span data-stu-id="94871-169">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="94871-170">Die `onreconnected` des Rückrufs `connectionId` Parameter wird nicht definiert sein, wenn die `HubConnection` wurde so konfiguriert, dass [überspringen Aushandlung](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="94871-170">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected((connectionId) => {
  console.assert(connection.state === signalR.HubConnectionState.Connected);

  document.getElementById("messageInput").disabled = false;

  const li = document.createElement("li");
  li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
  document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="94871-171">`withAutomaticReconnect()` Konfigurieren Sie wird nicht die `HubConnection` zum Wiederholen von Fehlern der ersten Start, sodass Fehler beim Starten von manuell verarbeitet werden müssen:</span><span class="sxs-lookup"><span data-stu-id="94871-171">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="94871-172">Wenn der Client erfolgreich in die ersten vier Versuche, erneut eine Verbindung herstellen nicht die `HubConnection` wird der Übergang in die `Disconnected` Status und ausgelöst werden die [Onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) Rückrufe.</span><span class="sxs-lookup"><span data-stu-id="94871-172">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="94871-173">Dies bietet die Möglichkeit zum Informieren der Benutzer die Verbindung wurde dauerhaft verloren und wird empfohlen, die Seite zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="94871-173">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose((error) => {
  console.assert(connection.state === signalR.HubConnectionState.Disconnected);

  document.getElementById("messageInput").disabled = true;

  const li = document.createElement("li");
  li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
  document.getElementById("messagesList").appendChild(li);
})
```

<span data-ttu-id="94871-174">Um eine benutzerdefinierte Anzahl von verbindungswiederherstellungsversuchen vor dem Trennen der Verbindung zu konfigurieren oder ändern die zeitplanung eine erneute Verbindung `withAutomaticReconnect` akzeptiert ein Array von Zahlen, die, die die Verzögerung in Millisekunden darstellt, die Wartezeit vor dem Starten von jedem Versuch eine erneute Verbindung.</span><span class="sxs-lookup"><span data-stu-id="94871-174">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="94871-175">Im vorhergehende Beispiel konfiguriert die `HubConnection` zu versuchen, Verbindungen, unmittelbar nachdem die Verbindung unterbrochen wird.</span><span class="sxs-lookup"><span data-stu-id="94871-175">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="94871-176">Dies gilt auch für die Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="94871-176">This is also true for the default configuration.</span></span>

<span data-ttu-id="94871-177">Wenn eine erneute Verbindung beim erste Versuch fehlschlägt, wird die zweite Versuch für eine erneute Verbindung auch sofort starten, anstatt abzuwarten, 2 Sekunden, wie in der Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="94871-177">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="94871-178">Wenn eine erneute Verbindung beim zweite Versuch ein Fehler auftritt, startet der dritte Versuch für eine erneute Verbindung innerhalb von 10 Sekunden, die wiederum wie die Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="94871-178">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="94871-179">Das benutzerdefinierte Verhalten dann weicht erneut das Standardverhalten durch Beenden dritte Wiederherstellen der Verbindung versucht haben Fehler statt zu versuchen, eine weitere Verbindung versucht, die in einer anderen 30 Sekunden, wie in der Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="94871-179">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="94871-180">Wenn Sie möchten noch mehr Kontrolle über Zeitpunkt und Anzahl automatisch erneut eine Verbindung herzustellen versucht, `withAutomaticReconnect` akzeptiert ein Objekt, das die `IReconnectPolicy` -Schnittstelle, die eine einzige namens Methode `nextRetryDelayInMilliseconds`.</span><span class="sxs-lookup"><span data-stu-id="94871-180">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IReconnectPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="94871-181">`nextRetryDelayInMilliseconds` akzeptiert zwei Argumente `previousRetryCount` und `elapsedMilliseconds`, welche der beiden Zahlen sind.</span><span class="sxs-lookup"><span data-stu-id="94871-181">`nextRetryDelayInMilliseconds` takes two arguments, `previousRetryCount` and `elapsedMilliseconds`, which are both numbers.</span></span> <span data-ttu-id="94871-182">Vor dem ersten Versuch eine erneute Verbindung sowohl `previousRetryCount` und `elapsedMilliseconds` wird 0 (null) sein.</span><span class="sxs-lookup"><span data-stu-id="94871-182">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero.</span></span> <span data-ttu-id="94871-183">Nach den fehlerhaften Wiederholungsversuchen beim Senden `previousRetryCount` um eins erhöht und `elapsedMilliseconds` die viel Zeit aufgewendet, die bisher in Millisekunden Verbindung entsprechend aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="94871-183">After each failed retry attempt, `previousRetryCount` will be incremented by one and `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds.</span></span>

<span data-ttu-id="94871-184">`nextRetryDelayInMilliseconds` muss entweder eine Anzahl, der die Anzahl von Millisekunden wartet, bevor der nächste Versuch für eine erneute Verbindung zurückgeben oder `null` Wenn die `HubConnection` beendet werden sollte erneut eine Verbindung herzustellen.</span><span class="sxs-lookup"><span data-stu-id="94871-184">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="94871-185">Alternativ können Sie schreiben Code, der den Client manuell, wie in wiederhergestellt [manuell erneut verbinden](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="94871-185">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="94871-186">Manuell erneut verbinden</span><span class="sxs-lookup"><span data-stu-id="94871-186">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="94871-187">Vor 3.0 nicht für SignalR JavaScript-Client automatisch erneut eine Verbindung herzustellen.</span><span class="sxs-lookup"><span data-stu-id="94871-187">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="94871-188">Sie müssen Code schreiben, die den Client manuell neu verbunden wird.</span><span class="sxs-lookup"><span data-stu-id="94871-188">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="94871-189">Der folgende Code veranschaulicht einen typischen manuellen erneuten Herstellen einer Verbindung Ansatz:</span><span class="sxs-lookup"><span data-stu-id="94871-189">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="94871-190">Eine Funktion (in diesem Fall die `start` Funktion) wird erstellt, um die Verbindung zu starten.</span><span class="sxs-lookup"><span data-stu-id="94871-190">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="94871-191">Rufen Sie die `start` -Funktion in der Verbindungs `onclose` -Ereignishandler.</span><span class="sxs-lookup"><span data-stu-id="94871-191">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="94871-192">Eine Implementierung unter realen Bedingungen würde ein Exponentielles Backoff verwendet, oder Wiederholen Sie eine angegebene Anzahl von Malen, bevor aufgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="94871-192">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span> 

## <a name="additional-resources"></a><span data-ttu-id="94871-193">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="94871-193">Additional resources</span></span>

* [<span data-ttu-id="94871-194">JavaScript-API-Referenz</span><span class="sxs-lookup"><span data-stu-id="94871-194">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="94871-195">JavaScript-tutorial</span><span class="sxs-lookup"><span data-stu-id="94871-195">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="94871-196">Webpack- und TypeScript-tutorial</span><span class="sxs-lookup"><span data-stu-id="94871-196">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="94871-197">Hubs</span><span class="sxs-lookup"><span data-stu-id="94871-197">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="94871-198">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="94871-198">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="94871-199">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="94871-199">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="94871-200">Cross-Origin Requests (CORS)</span><span class="sxs-lookup"><span data-stu-id="94871-200">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="94871-201">Serverlose Azure SignalR Service-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="94871-201">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

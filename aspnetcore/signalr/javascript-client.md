---
title: ASP.net Core SignalR JavaScript-Client
author: bradygaster
description: Übersicht über ASP.net Core SignalR JavaScript-Client.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: eaf737642cdbd7ab2b1b5c16538b47a70cddd332
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354695"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a><span data-ttu-id="2288d-103">ASP.net Core SignalR JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="2288d-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="2288d-104">Von [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="2288d-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="2288d-105">Die ASP.net Core SignalR JavaScript-Client Bibliothek ermöglicht es Entwicklern, serverseitigen Hub-Code aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="2288d-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="2288d-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2288d-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-opno-locsignalr-client-package"></a><span data-ttu-id="2288d-107">Installieren des SignalR-Client Pakets</span><span class="sxs-lookup"><span data-stu-id="2288d-107">Install the SignalR client package</span></span>

<span data-ttu-id="2288d-108">Die SignalR JavaScript-Client Bibliothek wird als [NPM](https://www.npmjs.com/) -Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="2288d-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="2288d-109">Wenn Sie Visual Studio verwenden, führen Sie `npm install` aus der **-Paket-Manager-Konsole** während Sie sich in den Stammordner.</span><span class="sxs-lookup"><span data-stu-id="2288d-109">If you're using Visual Studio, run `npm install` from the **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="2288d-110">Führen Sie für Visual Studio Code, den Befehl in der **integriertes Terminal**.</span><span class="sxs-lookup"><span data-stu-id="2288d-110">For Visual Studio Code, run the command from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

  ```console
  npm init -y
  npm install @microsoft/signalr
  ```

<span data-ttu-id="2288d-111">Npm installiert den Inhalt des Pakets in der *"node_modules"\\@microsoft\signalr\dist\browser* Ordner.</span><span class="sxs-lookup"><span data-stu-id="2288d-111">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="2288d-112">Erstellen Sie einen neuen Ordner namens *Signalr* unter der *"Wwwroot"\\Lib* Ordner.</span><span class="sxs-lookup"><span data-stu-id="2288d-112">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="2288d-113">Kopieren der *signalr.js* -Datei in die *Wwwroot\lib\signalr* Ordner.</span><span class="sxs-lookup"><span data-stu-id="2288d-113">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

  ```console
  npm init -y
  npm install @aspnet/signalr
  ```

<span data-ttu-id="2288d-114">Npm installiert den Inhalt des Pakets in der *"node_modules"\\@aspnet\signalr\dist\browser* Ordner.</span><span class="sxs-lookup"><span data-stu-id="2288d-114">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="2288d-115">Erstellen Sie einen neuen Ordner namens *Signalr* unter der *"Wwwroot"\\Lib* Ordner.</span><span class="sxs-lookup"><span data-stu-id="2288d-115">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="2288d-116">Kopieren der *signalr.js* -Datei in die *Wwwroot\lib\signalr* Ordner.</span><span class="sxs-lookup"><span data-stu-id="2288d-116">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

## <a name="use-the-opno-locsignalr-javascript-client"></a><span data-ttu-id="2288d-117">Verwenden des SignalR JavaScript-Clients</span><span class="sxs-lookup"><span data-stu-id="2288d-117">Use the SignalR JavaScript client</span></span>

<span data-ttu-id="2288d-118">Verweisen Sie im `<script>`-Element auf den SignalR JavaScript-Client.</span><span class="sxs-lookup"><span data-stu-id="2288d-118">Reference the SignalR JavaScript client in the `<script>` element.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

## <a name="connect-to-a-hub"></a><span data-ttu-id="2288d-119">Verbinden mit einem hub</span><span class="sxs-lookup"><span data-stu-id="2288d-119">Connect to a hub</span></span>

<span data-ttu-id="2288d-120">Der folgende Code erstellt und startet eine Verbindung.</span><span class="sxs-lookup"><span data-stu-id="2288d-120">The following code creates and starts a connection.</span></span> <span data-ttu-id="2288d-121">Die Hub-Name ist Groß-/Kleinschreibung.</span><span class="sxs-lookup"><span data-stu-id="2288d-121">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a><span data-ttu-id="2288d-122">Cross-Origin-Verbindungen</span><span class="sxs-lookup"><span data-stu-id="2288d-122">Cross-origin connections</span></span>

<span data-ttu-id="2288d-123">Browser werden in der Regel Verbindungen aus der gleichen Domäne wie die angeforderte Seite laden.</span><span class="sxs-lookup"><span data-stu-id="2288d-123">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="2288d-124">Es gibt jedoch Situationen, wenn eine Verbindung mit einer anderen Domäne erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="2288d-124">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="2288d-125">Um zu verhindern, dass eine schädliche Website sensible Daten von einem anderen Standort lesen [Cross-Origin-Verbindungen](xref:security/cors) sind standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="2288d-125">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="2288d-126">Um eine cors-Anforderung zu ermöglichen, aktivieren Sie ihn in das `Startup` Klasse.</span><span class="sxs-lookup"><span data-stu-id="2288d-126">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="2288d-127">Aufrufen von Hub-Methoden von client</span><span class="sxs-lookup"><span data-stu-id="2288d-127">Call hub methods from client</span></span>

<span data-ttu-id="2288d-128">JavaScript-Clients rufen Sie öffentliche Methoden für Hubs über die [Aufrufen](/javascript/api/%40aspnet/signalr/hubconnection#invoke) Methode der [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="2288d-128">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="2288d-129">Die `invoke` -Methode akzeptiert zwei Argumente:</span><span class="sxs-lookup"><span data-stu-id="2288d-129">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="2288d-130">Der Name der hubmethode.</span><span class="sxs-lookup"><span data-stu-id="2288d-130">The name of the hub method.</span></span> <span data-ttu-id="2288d-131">Im folgenden Beispiel wird der Name der Methode auf dem Hub `SendMessage`.</span><span class="sxs-lookup"><span data-stu-id="2288d-131">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="2288d-132">In der hubmethode definierten Argumente.</span><span class="sxs-lookup"><span data-stu-id="2288d-132">Any arguments defined in the hub method.</span></span> <span data-ttu-id="2288d-133">Im folgenden Beispiel wird der Name des Arguments `message`.</span><span class="sxs-lookup"><span data-stu-id="2288d-133">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="2288d-134">Im Beispielcode wird eine Pfeil Funktions Syntax verwendet, die in den aktuellen Versionen aller wichtigen Browser mit Ausnahme von Internet Explorer unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="2288d-134">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="2288d-135">Wenn Sie Azure SignalR Service im *Server losen Modus*verwenden, können Sie keine hubmethoden von einem Client aus abrufen.</span><span class="sxs-lookup"><span data-stu-id="2288d-135">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="2288d-136">Weitere Informationen finden Sie in der [Dokumentation zumSignalR-Dienst](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="2288d-136">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="2288d-137">Die `invoke`-Methode gibt eine JavaScript- [Zusage](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)zurück.</span><span class="sxs-lookup"><span data-stu-id="2288d-137">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="2288d-138">Die `Promise` wird mit dem Rückgabewert (sofern vorhanden) aufgelöst, wenn die-Methode auf dem Server zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="2288d-138">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="2288d-139">Wenn die-Methode auf dem Server einen Fehler auslöst, wird der `Promise` mit der Fehlermeldung abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="2288d-139">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="2288d-140">Verwenden Sie die Methoden `then` und `catch` für die `Promise` selbst, um diese Fälle (oder `await` Syntax) zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="2288d-140">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="2288d-141">Die `send`-Methode gibt einen JavaScript-`Promise`zurück.</span><span class="sxs-lookup"><span data-stu-id="2288d-141">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="2288d-142">Die `Promise` wird aufgelöst, wenn die Nachricht an den Server gesendet wurde.</span><span class="sxs-lookup"><span data-stu-id="2288d-142">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="2288d-143">Wenn beim Senden der Nachricht ein Fehler auftritt, wird der `Promise` mit der Fehlermeldung abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="2288d-143">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="2288d-144">Verwenden Sie die Methoden `then` und `catch` für die `Promise` selbst, um diese Fälle (oder `await` Syntax) zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="2288d-144">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="2288d-145">Die Verwendung von `send` wartet nicht, bis der Server die Nachricht empfangen hat.</span><span class="sxs-lookup"><span data-stu-id="2288d-145">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="2288d-146">Folglich ist es nicht möglich, Daten oder Fehler vom Server zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="2288d-146">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="2288d-147">Rufen Sie Client-Methoden von Hub-Instanz</span><span class="sxs-lookup"><span data-stu-id="2288d-147">Call client methods from hub</span></span>

<span data-ttu-id="2288d-148">Zum Empfangen von Nachrichten aus dem Hub Definieren einer Methode mit dem [auf](/javascript/api/%40aspnet/signalr/hubconnection#on) Methode der `HubConnection`.</span><span class="sxs-lookup"><span data-stu-id="2288d-148">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="2288d-149">Der Name des Client-JavaScript-Methode.</span><span class="sxs-lookup"><span data-stu-id="2288d-149">The name of the JavaScript client method.</span></span> <span data-ttu-id="2288d-150">Im folgenden Beispiel wird der Name der Methode `ReceiveMessage`.</span><span class="sxs-lookup"><span data-stu-id="2288d-150">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="2288d-151">Argumente übergibt der Hub, an die Methode.</span><span class="sxs-lookup"><span data-stu-id="2288d-151">Arguments the hub passes to the method.</span></span> <span data-ttu-id="2288d-152">Im folgenden Beispiel wird den Wert des Arguments `message`.</span><span class="sxs-lookup"><span data-stu-id="2288d-152">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="2288d-153">Der vorangehende Code in `connection.on` ausgeführt wird, wenn Sie serverseitigen Code aufruft, mit der [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) Methode.</span><span class="sxs-lookup"><span data-stu-id="2288d-153">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR<span data-ttu-id="2288d-154"> bestimmt, welche Client Methode aufgerufen werden soll, indem der Methodenname und die in `SendAsync` und `connection.on`definierten Argumente abgeglichen werden.</span><span class="sxs-lookup"><span data-stu-id="2288d-154"> determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="2288d-155">Rufen Sie als bewährte Methode, die [starten](/javascript/api/%40aspnet/signalr/hubconnection#start) Methode für die `HubConnection` nach `on`.</span><span class="sxs-lookup"><span data-stu-id="2288d-155">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="2288d-156">Dadurch wird sichergestellt, dass die Handler registriert sind, bevor alle Nachrichten empfangen werden.</span><span class="sxs-lookup"><span data-stu-id="2288d-156">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="2288d-157">Fehlerbehandlung und Protokollierung</span><span class="sxs-lookup"><span data-stu-id="2288d-157">Error handling and logging</span></span>

<span data-ttu-id="2288d-158">Kette eine `catch` Methode am Ende der `start` Methode zum Behandeln von clientseitigen Fehlern.</span><span class="sxs-lookup"><span data-stu-id="2288d-158">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="2288d-159">Verwendung `console.error` zu Ausgabefehler in der Browserkonsole.</span><span class="sxs-lookup"><span data-stu-id="2288d-159">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

<span data-ttu-id="2288d-160">Einrichten von clientseitigen Protokollierung, Ablaufverfolgung durch Übergeben einer Protokollierung und der Typ des Ereignisses zu protokollieren, wenn die Verbindung hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="2288d-160">Setup client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="2288d-161">Nachrichten werden mit der angegebenen Ebene oder höher protokolliert.</span><span class="sxs-lookup"><span data-stu-id="2288d-161">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="2288d-162">Verfügbaren Protokollebenen lauten wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="2288d-162">Available log levels are as follows:</span></span>

* <span data-ttu-id="2288d-163">`signalR.LogLevel.Error` &ndash; Fehlermeldungen.</span><span class="sxs-lookup"><span data-stu-id="2288d-163">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="2288d-164">Protokolle `Error` nur Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="2288d-164">Logs `Error` messages only.</span></span>
* <span data-ttu-id="2288d-165">`signalR.LogLevel.Warning` &ndash; Warnmeldungen zu potenziellen Fehlern.</span><span class="sxs-lookup"><span data-stu-id="2288d-165">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="2288d-166">Protokolle `Warning`, und `Error` Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="2288d-166">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="2288d-167">`signalR.LogLevel.Information` &ndash; Statusmeldungen, die ohne Fehler.</span><span class="sxs-lookup"><span data-stu-id="2288d-167">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="2288d-168">Protokolle `Information`, `Warning`, und `Error` Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="2288d-168">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="2288d-169">`signalR.LogLevel.Trace` &ndash; Ablaufverfolgungsmeldungen Sie aus.</span><span class="sxs-lookup"><span data-stu-id="2288d-169">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="2288d-170">Protokolliert alle Elemente einschließlich Daten, die zwischen Hub und dem Client übertragen.</span><span class="sxs-lookup"><span data-stu-id="2288d-170">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="2288d-171">Verwenden der ["configurelogging"](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) Methode [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) so konfigurieren Sie die Protokollebene.</span><span class="sxs-lookup"><span data-stu-id="2288d-171">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="2288d-172">Nachrichten werden in der Browserkonsole protokolliert.</span><span class="sxs-lookup"><span data-stu-id="2288d-172">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="2288d-173">Verbinden von clients</span><span class="sxs-lookup"><span data-stu-id="2288d-173">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="2288d-174">Automatisch wiederherstellen der Verbindung</span><span class="sxs-lookup"><span data-stu-id="2288d-174">Automatically reconnect</span></span>

<span data-ttu-id="2288d-175">Der JavaScript-Client für SignalR kann so konfiguriert werden, dass er mit der `withAutomaticReconnect`-Methode auf [hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)automatisch erneut eine Verbindung herstellt.</span><span class="sxs-lookup"><span data-stu-id="2288d-175">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="2288d-176">Standardmäßig wird die Verbindung nicht automatisch wieder hergestellt.</span><span class="sxs-lookup"><span data-stu-id="2288d-176">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="2288d-177">Ohne Parameter konfiguriert `withAutomaticReconnect()` den Client so, dass 0, 2, 10 und 30 Sekunden gewartet wird, bevor versucht wird, jeden erneuten Verbindungsversuch durchzusetzen, wobei nach vier fehlgeschlagenen Versuchen angehalten wird.</span><span class="sxs-lookup"><span data-stu-id="2288d-177">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="2288d-178">Vor dem Starten eines erneuten Verbindungsversuchs wechselt der `HubConnection` in den `HubConnectionState.Reconnecting`-Zustand und löst seine `onreconnecting` Rückrufe aus, anstatt in den `Disconnected` Zustand zu wechseln und seine `onclose` Rückrufe wie eine `HubConnection` auszulösen, ohne dass eine automatische erneute Verbindung konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="2288d-178">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="2288d-179">Dies bietet die Möglichkeit, die Benutzer zu warnen, dass die Verbindung unterbrochen wurde, und Benutzeroberflächen Elemente zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="2288d-179">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="2288d-180">Wenn der Client innerhalb der ersten vier Versuche erfolgreich erneut eine Verbindung herstellt, wechselt der `HubConnection` zurück in den `Connected` Zustand und löst seine `onreconnected` Rückrufe aus.</span><span class="sxs-lookup"><span data-stu-id="2288d-180">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="2288d-181">Dies bietet die Möglichkeit, den Benutzern mitzuteilen, dass die Verbindung wieder hergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="2288d-181">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="2288d-182">Da die Verbindung mit dem Server völlig neu ist, wird eine neue `connectionId` für den `onreconnected` Rückruf bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="2288d-182">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="2288d-183">Der `connectionId` Parameter des `onreconnected` Rückrufs ist nicht definiert, wenn die `HubConnection` zum über [springen der Aushandlung](xref:signalr/configuration#configure-client-options)konfiguriert wurde.</span><span class="sxs-lookup"><span data-stu-id="2288d-183">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="2288d-184">`withAutomaticReconnect()` die `HubConnection` nicht so konfigurieren, dass anfängliche Start Fehler erneut auftreten, müssen Start Fehler manuell behandelt werden:</span><span class="sxs-lookup"><span data-stu-id="2288d-184">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="2288d-185">Wenn der Client nicht innerhalb der ersten vier Versuche erneut eine Verbindung herstellt, wechselt der `HubConnection` in den `Disconnected` Zustand und auslöst seine [OnClose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) -Rückrufe.</span><span class="sxs-lookup"><span data-stu-id="2288d-185">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="2288d-186">Dies bietet die Möglichkeit, den Benutzern mitzuteilen, dass die Verbindung dauerhaft verloren gegangen ist, und es wird empfohlen, die Seite zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="2288d-186">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="2288d-187">Um eine benutzerdefinierte Anzahl von Wiederholungs versuchen zu konfigurieren, bevor die Verbindung getrennt wird, oder um die Zeit für die Wiederherstellung der Verbindung zu ändern, nimmt `withAutomaticReconnect` ein Array von Zahlen an, das die Wartezeit in Millisekunden angibt, die vor dem Start der einzelnen Wiederholungs Versuche</span><span class="sxs-lookup"><span data-stu-id="2288d-187">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="2288d-188">Im vorherigen Beispiel wird der `HubConnection` so konfiguriert, dass nach dem Verlust der Verbindung erneut versucht wird, die Verbindung herzustellen.</span><span class="sxs-lookup"><span data-stu-id="2288d-188">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="2288d-189">Dies gilt auch für die Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="2288d-189">This is also true for the default configuration.</span></span>

<span data-ttu-id="2288d-190">Wenn der erste Wiederholungsversuch fehlschlägt, wird der zweite Wiederholungsversuch auch sofort gestartet, anstatt zwei Sekunden zu warten, wie in der Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="2288d-190">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="2288d-191">Wenn der zweite Wiederholungsversuch fehlschlägt, wird der dritte Versuch der erneuten Verbindungs Herstellung in 10 Sekunden gestartet. Dies entspricht der Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="2288d-191">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="2288d-192">Das benutzerdefinierte Verhalten weicht dann erneut vom Standardverhalten ab, indem nach dem dritten Wiederholungsversuch beendet wird, anstatt einen weiteren Wiederholungsversuch in weiteren 30 Sekunden auszuführen, wie in der Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="2288d-192">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="2288d-193">Wenn Sie noch mehr Kontrolle über die zeitliche Steuerung und die Anzahl der automatischen Wiederholungs Versuche benötigen, akzeptiert `withAutomaticReconnect` ein Objekt, das die `IRetryPolicy`-Schnittstelle implementiert, die über eine einzige Methode mit dem Namen `nextRetryDelayInMilliseconds`verfügt.</span><span class="sxs-lookup"><span data-stu-id="2288d-193">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="2288d-194">`nextRetryDelayInMilliseconds` nimmt ein einzelnes Argument mit dem Typ `RetryContext`.</span><span class="sxs-lookup"><span data-stu-id="2288d-194">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="2288d-195">Der `RetryContext` verfügt über drei Eigenschaften: `previousRetryCount`, `elapsedMilliseconds` und `retryReason` die eine `number`, eine `number` bzw. eine `Error` sind.</span><span class="sxs-lookup"><span data-stu-id="2288d-195">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="2288d-196">Vor dem ersten Versuch, die Verbindung wiederherzustellen, sind sowohl `previousRetryCount` als auch `elapsedMilliseconds` 0 (null), und die `retryReason` ist der Fehler, der bewirkt hat, dass die Verbindung unterbrochen wurde.</span><span class="sxs-lookup"><span data-stu-id="2288d-196">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="2288d-197">Nach jedem fehlgeschlagenen Wiederholungsversuch werden `previousRetryCount` um eins inkrementiert, `elapsedMilliseconds` entsprechend der Zeitspanne in Millisekunden, die zum erneuten Herstellen der Verbindung aufgewendet wurde, aktualisiert, und die `retryReason` ist der Fehler, durch den der letzte Wiederholungsversuch für die Verbindung verursacht wurde.</span><span class="sxs-lookup"><span data-stu-id="2288d-197">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="2288d-198">`nextRetryDelayInMilliseconds` muss entweder eine Zahl zurückgeben, die die Anzahl der Millisekunden angibt, die vor dem nächsten erneuten Verbindungsversuch gewartet werden soll, oder `null`, wenn die `HubConnection` die erneute Verbindungs Herstellung abbrechen soll.</span><span class="sxs-lookup"><span data-stu-id="2288d-198">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
            if (retryContext.elapsedMilliseconds < 60000) {
                // If we've been reconnecting for less than 60 seconds so far,
                // wait between 0 and 10 seconds before the next reconnect attempt.
                return Math.random() * 10000;
            } else {
                // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
                return null;
            }
        }
    })
    .build();
```

<span data-ttu-id="2288d-199">Alternativ dazu können Sie auch Code schreiben, mit dem der Client manuell erneut eine Verbindung herstellt, wie in [Manuelles Wiederherstellen der Verbindung](#manually-reconnect)gezeigt.</span><span class="sxs-lookup"><span data-stu-id="2288d-199">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="2288d-200">Verbindung manuell wiederherstellen</span><span class="sxs-lookup"><span data-stu-id="2288d-200">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="2288d-201">Vor 3,0 wird die Verbindung des JavaScript-Clients für SignalR nicht automatisch wieder hergestellt.</span><span class="sxs-lookup"><span data-stu-id="2288d-201">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="2288d-202">Sie müssen Code schreiben, die den Client manuell neu verbunden wird.</span><span class="sxs-lookup"><span data-stu-id="2288d-202">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="2288d-203">Der folgende Code veranschaulicht einen typischen manuellen Verbindungs Ansatz:</span><span class="sxs-lookup"><span data-stu-id="2288d-203">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="2288d-204">Eine Funktion (in diesem Fall die `start` Funktion) wird erstellt, um die Verbindung zu starten.</span><span class="sxs-lookup"><span data-stu-id="2288d-204">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="2288d-205">Rufen Sie die `start` -Funktion in der Verbindungs `onclose` -Ereignishandler.</span><span class="sxs-lookup"><span data-stu-id="2288d-205">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="2288d-206">Eine Implementierung unter realen Bedingungen würde ein Exponentielles Backoff verwendet, oder Wiederholen Sie eine angegebene Anzahl von Malen, bevor aufgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="2288d-206">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2288d-207">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="2288d-207">Additional resources</span></span>

* [<span data-ttu-id="2288d-208">JavaScript-API-Referenz</span><span class="sxs-lookup"><span data-stu-id="2288d-208">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="2288d-209">JavaScript-tutorial</span><span class="sxs-lookup"><span data-stu-id="2288d-209">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="2288d-210">Webpack- und TypeScript-tutorial</span><span class="sxs-lookup"><span data-stu-id="2288d-210">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="2288d-211">Hubs</span><span class="sxs-lookup"><span data-stu-id="2288d-211">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="2288d-212">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="2288d-212">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="2288d-213">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="2288d-213">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="2288d-214">Cross-Origin Requests (CORS)</span><span class="sxs-lookup"><span data-stu-id="2288d-214">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* <span data-ttu-id="2288d-215">[Server lose Dokumentation zu Azure SignalR Service](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="2288d-215">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>

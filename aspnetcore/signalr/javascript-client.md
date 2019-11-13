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
ms.openlocfilehash: 926160a41c82853d83890f0d52b14d7d5561a990
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963774"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a><span data-ttu-id="3acd3-103">ASP.net Core SignalR JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="3acd3-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="3acd3-104">Von [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="3acd3-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="3acd3-105">Die ASP.net Core SignalR JavaScript-Client Bibliothek ermöglicht es Entwicklern, serverseitigen Hub-Code aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="3acd3-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="3acd3-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3acd3-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-opno-locsignalr-client-package"></a><span data-ttu-id="3acd3-107">Installieren des SignalR-Client Pakets</span><span class="sxs-lookup"><span data-stu-id="3acd3-107">Install the SignalR client package</span></span>

<span data-ttu-id="3acd3-108">Die SignalR JavaScript-Client Bibliothek wird als [NPM](https://www.npmjs.com/) -Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="3acd3-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="3acd3-109">Wenn Sie Visual Studio verwenden, führen Sie `npm install` in der **Paket-Manager-Konsole** aus, während Sie sich im Stamm Ordner befinden.</span><span class="sxs-lookup"><span data-stu-id="3acd3-109">If you're using Visual Studio, run `npm install` from the **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="3acd3-110">Führen Sie für Visual Studio Code den Befehl über das **integrierte Terminal**aus.</span><span class="sxs-lookup"><span data-stu-id="3acd3-110">For Visual Studio Code, run the command from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

  ```console
  npm init -y
  npm install @microsoft/signalr
  ```

<span data-ttu-id="3acd3-111">NPM installiert den Paket Inhalt im Ordner *node_modules\\@microsoft\signalr\dist\browser* .</span><span class="sxs-lookup"><span data-stu-id="3acd3-111">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="3acd3-112">Erstellen Sie einen neuen Ordner mit dem Namen *signalr* unter dem Ordner *wwwroot\\lib* .</span><span class="sxs-lookup"><span data-stu-id="3acd3-112">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="3acd3-113">Kopieren Sie die Datei " *signalr. js* " in den Ordner " *wwwroot\lib\signalr* ".</span><span class="sxs-lookup"><span data-stu-id="3acd3-113">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

  ```console
  npm init -y
  npm install @aspnet/signalr
  ```

<span data-ttu-id="3acd3-114">NPM installiert den Paket Inhalt im Ordner *node_modules\\@aspnet\signalr\dist\browser* .</span><span class="sxs-lookup"><span data-stu-id="3acd3-114">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="3acd3-115">Erstellen Sie einen neuen Ordner mit dem Namen *signalr* unter dem Ordner *wwwroot\\lib* .</span><span class="sxs-lookup"><span data-stu-id="3acd3-115">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="3acd3-116">Kopieren Sie die Datei " *signalr. js* " in den Ordner " *wwwroot\lib\signalr* ".</span><span class="sxs-lookup"><span data-stu-id="3acd3-116">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

## <a name="use-the-opno-locsignalr-javascript-client"></a><span data-ttu-id="3acd3-117">Verwenden des SignalR JavaScript-Clients</span><span class="sxs-lookup"><span data-stu-id="3acd3-117">Use the SignalR JavaScript client</span></span>

<span data-ttu-id="3acd3-118">Verweisen Sie im `<script>`-Element auf den SignalR JavaScript-Client.</span><span class="sxs-lookup"><span data-stu-id="3acd3-118">Reference the SignalR JavaScript client in the `<script>` element.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

## <a name="connect-to-a-hub"></a><span data-ttu-id="3acd3-119">Herstellen einer Verbindung mit einem Hub</span><span class="sxs-lookup"><span data-stu-id="3acd3-119">Connect to a hub</span></span>

<span data-ttu-id="3acd3-120">Mit dem folgenden Code wird eine Verbindung erstellt und gestartet.</span><span class="sxs-lookup"><span data-stu-id="3acd3-120">The following code creates and starts a connection.</span></span> <span data-ttu-id="3acd3-121">Der Name des Hubs wird nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="3acd3-121">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a><span data-ttu-id="3acd3-122">Ursprungs übergreifende Verbindungen</span><span class="sxs-lookup"><span data-stu-id="3acd3-122">Cross-origin connections</span></span>

<span data-ttu-id="3acd3-123">Normalerweise laden Browser Verbindungen aus derselben Domäne wie die angeforderte Seite.</span><span class="sxs-lookup"><span data-stu-id="3acd3-123">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="3acd3-124">Es gibt jedoch Situationen, in denen eine Verbindung mit einer anderen Domäne erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="3acd3-124">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="3acd3-125">Um zu verhindern, dass ein böswilliger Standort vertrauliche Daten von einem anderen Standort liest, sind [Ursprungs übergreifende Verbindungen](xref:security/cors) standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="3acd3-125">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="3acd3-126">Um eine Ursprungs übergreifende Anforderung zuzulassen, aktivieren Sie Sie in der `Startup`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="3acd3-126">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="3acd3-127">Hub-Methoden vom Client abrufen</span><span class="sxs-lookup"><span data-stu-id="3acd3-127">Call hub methods from client</span></span>

<span data-ttu-id="3acd3-128">JavaScript-Clients rufen öffentliche Methoden für Hubs über die [Aufruf Methode](/javascript/api/%40aspnet/signalr/hubconnection#invoke) von [hubconnection](/javascript/api/%40aspnet/signalr/hubconnection)auf.</span><span class="sxs-lookup"><span data-stu-id="3acd3-128">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="3acd3-129">Die `invoke`-Methode akzeptiert zwei Argumente:</span><span class="sxs-lookup"><span data-stu-id="3acd3-129">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="3acd3-130">Der Name der Hub-Methode.</span><span class="sxs-lookup"><span data-stu-id="3acd3-130">The name of the hub method.</span></span> <span data-ttu-id="3acd3-131">Im folgenden Beispiel wird der Methodenname auf dem Hub `SendMessage`.</span><span class="sxs-lookup"><span data-stu-id="3acd3-131">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="3acd3-132">Alle Argumente, die in der Hub-Methode definiert sind.</span><span class="sxs-lookup"><span data-stu-id="3acd3-132">Any arguments defined in the hub method.</span></span> <span data-ttu-id="3acd3-133">Im folgenden Beispiel ist der Argument Name `message`.</span><span class="sxs-lookup"><span data-stu-id="3acd3-133">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="3acd3-134">Im Beispielcode wird eine Pfeil Funktions Syntax verwendet, die in den aktuellen Versionen aller wichtigen Browser mit Ausnahme von Internet Explorer unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="3acd3-134">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="3acd3-135">Wenn Sie Azure SignalR Service im *Server losen Modus*verwenden, können Sie keine hubmethoden von einem Client aus abrufen.</span><span class="sxs-lookup"><span data-stu-id="3acd3-135">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="3acd3-136">Weitere Informationen finden Sie in der [Dokumentation zumSignalR-Dienst](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="3acd3-136">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="3acd3-137">Die `invoke`-Methode gibt eine JavaScript- [Zusage](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)zurück.</span><span class="sxs-lookup"><span data-stu-id="3acd3-137">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="3acd3-138">Die `Promise` wird mit dem Rückgabewert (sofern vorhanden) aufgelöst, wenn die-Methode auf dem Server zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="3acd3-138">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="3acd3-139">Wenn die-Methode auf dem Server einen Fehler auslöst, wird der `Promise` mit der Fehlermeldung abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="3acd3-139">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="3acd3-140">Verwenden Sie die Methoden `then` und `catch` für die `Promise` selbst, um diese Fälle (oder `await` Syntax) zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="3acd3-140">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="3acd3-141">Die `send`-Methode gibt einen JavaScript-`Promise`zurück.</span><span class="sxs-lookup"><span data-stu-id="3acd3-141">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="3acd3-142">Die `Promise` wird aufgelöst, wenn die Nachricht an den Server gesendet wurde.</span><span class="sxs-lookup"><span data-stu-id="3acd3-142">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="3acd3-143">Wenn beim Senden der Nachricht ein Fehler auftritt, wird der `Promise` mit der Fehlermeldung abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="3acd3-143">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="3acd3-144">Verwenden Sie die Methoden `then` und `catch` für die `Promise` selbst, um diese Fälle (oder `await` Syntax) zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="3acd3-144">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="3acd3-145">Die Verwendung von `send` wartet nicht, bis der Server die Nachricht empfangen hat.</span><span class="sxs-lookup"><span data-stu-id="3acd3-145">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="3acd3-146">Folglich ist es nicht möglich, Daten oder Fehler vom Server zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="3acd3-146">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="3acd3-147">Client Methoden aus Hub abrufen</span><span class="sxs-lookup"><span data-stu-id="3acd3-147">Call client methods from hub</span></span>

<span data-ttu-id="3acd3-148">Um Nachrichten vom Hub zu empfangen, definieren Sie mithilfe der [on](/javascript/api/%40aspnet/signalr/hubconnection#on) -Methode der `HubConnection`eine Methode.</span><span class="sxs-lookup"><span data-stu-id="3acd3-148">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="3acd3-149">Der Name der JavaScript-Client Methode.</span><span class="sxs-lookup"><span data-stu-id="3acd3-149">The name of the JavaScript client method.</span></span> <span data-ttu-id="3acd3-150">Im folgenden Beispiel wird der Methodenname `ReceiveMessage`.</span><span class="sxs-lookup"><span data-stu-id="3acd3-150">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="3acd3-151">Argumente, die der Hub an die Methode übergibt.</span><span class="sxs-lookup"><span data-stu-id="3acd3-151">Arguments the hub passes to the method.</span></span> <span data-ttu-id="3acd3-152">Im folgenden Beispiel ist der Argument Wert `message`.</span><span class="sxs-lookup"><span data-stu-id="3acd3-152">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="3acd3-153">Der vorangehende Code in `connection.on` wird ausgeführt, wenn der serverseitige Code ihn mithilfe der [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) -Methode aufruft.</span><span class="sxs-lookup"><span data-stu-id="3acd3-153">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR<span data-ttu-id="3acd3-154"> bestimmt, welche Client Methode aufgerufen werden soll, indem der Methodenname und die in `SendAsync` und `connection.on`definierten Argumente abgeglichen werden.</span><span class="sxs-lookup"><span data-stu-id="3acd3-154"> determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="3acd3-155">Es wird empfohlen, die [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) -Methode nach `on`auf dem `HubConnection` aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="3acd3-155">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="3acd3-156">Dadurch wird sichergestellt, dass ihre Handler registriert werden, bevor Nachrichten empfangen werden.</span><span class="sxs-lookup"><span data-stu-id="3acd3-156">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="3acd3-157">Fehlerbehandlung und Protokollierung</span><span class="sxs-lookup"><span data-stu-id="3acd3-157">Error handling and logging</span></span>

<span data-ttu-id="3acd3-158">Verketten Sie eine `catch` Methode an das Ende der `start` Methode, um Client seitige Fehler zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="3acd3-158">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="3acd3-159">Verwenden Sie `console.error`, um Fehler an die Konsole des Browsers auszugeben.</span><span class="sxs-lookup"><span data-stu-id="3acd3-159">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

<span data-ttu-id="3acd3-160">Richten Sie die Client seitige Protokoll Ablauf Verfolgung ein, indem Sie eine Protokollierung und einen Ereignistyp an die Protokollierung übergeben.</span><span class="sxs-lookup"><span data-stu-id="3acd3-160">Setup client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="3acd3-161">Nachrichten werden mit der angegebenen Protokollebene und höher protokolliert.</span><span class="sxs-lookup"><span data-stu-id="3acd3-161">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="3acd3-162">Folgende Protokoll Ebenen sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="3acd3-162">Available log levels are as follows:</span></span>

* <span data-ttu-id="3acd3-163">`signalR.LogLevel.Error` &ndash; Fehlermeldungen.</span><span class="sxs-lookup"><span data-stu-id="3acd3-163">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="3acd3-164">Protokolliert nur `Error` Meldungen.</span><span class="sxs-lookup"><span data-stu-id="3acd3-164">Logs `Error` messages only.</span></span>
* <span data-ttu-id="3acd3-165">`signalR.LogLevel.Warning` &ndash; Warnmeldungen zu möglichen Fehlern.</span><span class="sxs-lookup"><span data-stu-id="3acd3-165">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="3acd3-166">Protokolliert `Warning`und `Error` Meldungen.</span><span class="sxs-lookup"><span data-stu-id="3acd3-166">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="3acd3-167">`signalR.LogLevel.Information` &ndash; Status Meldungen ohne Fehler.</span><span class="sxs-lookup"><span data-stu-id="3acd3-167">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="3acd3-168">Protokolliert `Information`-, `Warning`-und `Error`-Meldungen.</span><span class="sxs-lookup"><span data-stu-id="3acd3-168">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="3acd3-169">`signalR.LogLevel.Trace` &ndash; Ablauf Verfolgungs Meldungen.</span><span class="sxs-lookup"><span data-stu-id="3acd3-169">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="3acd3-170">Protokolliert alles, einschließlich Daten, die zwischen dem Hub und dem Client transportiert werden.</span><span class="sxs-lookup"><span data-stu-id="3acd3-170">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="3acd3-171">Verwenden Sie die Methode configure [relogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) für [hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) , um die Protokollebene zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="3acd3-171">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="3acd3-172">Nachrichten werden in der Browser Konsole protokolliert.</span><span class="sxs-lookup"><span data-stu-id="3acd3-172">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="3acd3-173">Clients erneut verbinden</span><span class="sxs-lookup"><span data-stu-id="3acd3-173">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="3acd3-174">Automatisch wiederherstellen der Verbindung</span><span class="sxs-lookup"><span data-stu-id="3acd3-174">Automatically reconnect</span></span>

<span data-ttu-id="3acd3-175">Der JavaScript-Client für SignalR kann so konfiguriert werden, dass er mit der `withAutomaticReconnect`-Methode auf [hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)automatisch erneut eine Verbindung herstellt.</span><span class="sxs-lookup"><span data-stu-id="3acd3-175">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="3acd3-176">Standardmäßig wird die Verbindung nicht automatisch wieder hergestellt.</span><span class="sxs-lookup"><span data-stu-id="3acd3-176">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="3acd3-177">Ohne Parameter konfiguriert `withAutomaticReconnect()` den Client so, dass 0, 2, 10 und 30 Sekunden gewartet wird, bevor versucht wird, jeden erneuten Verbindungsversuch durchzusetzen, wobei nach vier fehlgeschlagenen Versuchen angehalten wird.</span><span class="sxs-lookup"><span data-stu-id="3acd3-177">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="3acd3-178">Vor dem Starten eines erneuten Verbindungsversuchs wechselt der `HubConnection` in den `HubConnectionState.Reconnecting`-Zustand und löst seine `onreconnecting` Rückrufe aus, anstatt in den `Disconnected` Zustand zu wechseln und seine `onclose` Rückrufe wie eine `HubConnection` auszulösen, ohne dass eine automatische erneute Verbindung konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="3acd3-178">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="3acd3-179">Dies bietet die Möglichkeit, die Benutzer zu warnen, dass die Verbindung unterbrochen wurde, und Benutzeroberflächen Elemente zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="3acd3-179">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="3acd3-180">Wenn der Client innerhalb der ersten vier Versuche erfolgreich erneut eine Verbindung herstellt, wechselt der `HubConnection` zurück in den `Connected` Zustand und löst seine `onreconnected` Rückrufe aus.</span><span class="sxs-lookup"><span data-stu-id="3acd3-180">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="3acd3-181">Dies bietet die Möglichkeit, den Benutzern mitzuteilen, dass die Verbindung wieder hergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="3acd3-181">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="3acd3-182">Da die Verbindung mit dem Server völlig neu ist, wird eine neue `connectionId` für den `onreconnected` Rückruf bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="3acd3-182">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="3acd3-183">Der `connectionId` Parameter des `onreconnected` Rückrufs ist nicht definiert, wenn die `HubConnection` zum über [springen der Aushandlung](xref:signalr/configuration#configure-client-options)konfiguriert wurde.</span><span class="sxs-lookup"><span data-stu-id="3acd3-183">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="3acd3-184">`withAutomaticReconnect()` die `HubConnection` nicht so konfigurieren, dass anfängliche Start Fehler erneut auftreten, müssen Start Fehler manuell behandelt werden:</span><span class="sxs-lookup"><span data-stu-id="3acd3-184">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="3acd3-185">Wenn der Client nicht innerhalb der ersten vier Versuche erneut eine Verbindung herstellt, wechselt der `HubConnection` in den `Disconnected` Zustand und auslöst seine [OnClose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) -Rückrufe.</span><span class="sxs-lookup"><span data-stu-id="3acd3-185">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="3acd3-186">Dies bietet die Möglichkeit, den Benutzern mitzuteilen, dass die Verbindung dauerhaft verloren gegangen ist, und es wird empfohlen, die Seite zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="3acd3-186">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="3acd3-187">Um eine benutzerdefinierte Anzahl von Wiederholungs versuchen zu konfigurieren, bevor die Verbindung getrennt wird, oder um die Zeit für die Wiederherstellung der Verbindung zu ändern, nimmt `withAutomaticReconnect` ein Array von Zahlen an, das die Wartezeit in Millisekunden angibt, die vor dem Start der einzelnen Wiederholungs Versuche</span><span class="sxs-lookup"><span data-stu-id="3acd3-187">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="3acd3-188">Im vorherigen Beispiel wird der `HubConnection` so konfiguriert, dass nach dem Verlust der Verbindung erneut versucht wird, die Verbindung herzustellen.</span><span class="sxs-lookup"><span data-stu-id="3acd3-188">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="3acd3-189">Dies gilt auch für die Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="3acd3-189">This is also true for the default configuration.</span></span>

<span data-ttu-id="3acd3-190">Wenn der erste Wiederholungsversuch fehlschlägt, wird der zweite Wiederholungsversuch auch sofort gestartet, anstatt zwei Sekunden zu warten, wie in der Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="3acd3-190">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="3acd3-191">Wenn der zweite Wiederholungsversuch fehlschlägt, wird der dritte Versuch der erneuten Verbindungs Herstellung in 10 Sekunden gestartet. Dies entspricht der Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="3acd3-191">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="3acd3-192">Das benutzerdefinierte Verhalten weicht dann erneut vom Standardverhalten ab, indem nach dem dritten Wiederholungsversuch beendet wird, anstatt einen weiteren Wiederholungsversuch in weiteren 30 Sekunden auszuführen, wie in der Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="3acd3-192">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="3acd3-193">Wenn Sie noch mehr Kontrolle über die zeitliche Steuerung und die Anzahl der automatischen Wiederholungs Versuche benötigen, akzeptiert `withAutomaticReconnect` ein Objekt, das die `IRetryPolicy`-Schnittstelle implementiert, die über eine einzige Methode mit dem Namen `nextRetryDelayInMilliseconds`verfügt.</span><span class="sxs-lookup"><span data-stu-id="3acd3-193">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="3acd3-194">`nextRetryDelayInMilliseconds` nimmt ein einzelnes Argument mit dem Typ `RetryContext`.</span><span class="sxs-lookup"><span data-stu-id="3acd3-194">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="3acd3-195">Der `RetryContext` verfügt über drei Eigenschaften: `previousRetryCount`, `elapsedMilliseconds` und `retryReason` die eine `number`, eine `number` bzw. eine `Error` sind.</span><span class="sxs-lookup"><span data-stu-id="3acd3-195">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="3acd3-196">Vor dem ersten Versuch, die Verbindung wiederherzustellen, sind sowohl `previousRetryCount` als auch `elapsedMilliseconds` 0 (null), und die `retryReason` ist der Fehler, der bewirkt hat, dass die Verbindung unterbrochen wurde.</span><span class="sxs-lookup"><span data-stu-id="3acd3-196">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="3acd3-197">Nach jedem fehlgeschlagenen Wiederholungsversuch werden `previousRetryCount` um eins inkrementiert, `elapsedMilliseconds` entsprechend der Zeitspanne in Millisekunden, die zum erneuten Herstellen der Verbindung aufgewendet wurde, aktualisiert, und die `retryReason` ist der Fehler, durch den der letzte Wiederholungsversuch für die Verbindung verursacht wurde.</span><span class="sxs-lookup"><span data-stu-id="3acd3-197">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="3acd3-198">`nextRetryDelayInMilliseconds` muss entweder eine Zahl zurückgeben, die die Anzahl der Millisekunden angibt, die vor dem nächsten erneuten Verbindungsversuch gewartet werden soll, oder `null`, wenn die `HubConnection` die erneute Verbindungs Herstellung abbrechen soll.</span><span class="sxs-lookup"><span data-stu-id="3acd3-198">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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
        })
    .build();
```

<span data-ttu-id="3acd3-199">Alternativ dazu können Sie auch Code schreiben, mit dem der Client manuell erneut eine Verbindung herstellt, wie in [Manuelles Wiederherstellen der Verbindung](#manually-reconnect)gezeigt.</span><span class="sxs-lookup"><span data-stu-id="3acd3-199">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="3acd3-200">Verbindung manuell wiederherstellen</span><span class="sxs-lookup"><span data-stu-id="3acd3-200">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="3acd3-201">Vor 3,0 wird die Verbindung des JavaScript-Clients für SignalR nicht automatisch wieder hergestellt.</span><span class="sxs-lookup"><span data-stu-id="3acd3-201">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="3acd3-202">Sie müssen Code schreiben, mit dem der Client manuell erneut eine Verbindung herstellt.</span><span class="sxs-lookup"><span data-stu-id="3acd3-202">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="3acd3-203">Der folgende Code veranschaulicht einen typischen manuellen Verbindungs Ansatz:</span><span class="sxs-lookup"><span data-stu-id="3acd3-203">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="3acd3-204">Eine Funktion (in diesem Fall die `start`-Funktion) wird erstellt, um die Verbindung zu starten.</span><span class="sxs-lookup"><span data-stu-id="3acd3-204">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="3acd3-205">Ruft die `start`-Funktion im `onclose`-Ereignishandler der Verbindung auf.</span><span class="sxs-lookup"><span data-stu-id="3acd3-205">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="3acd3-206">Eine reale Implementierung würde einen exponentiellen Backoff verwenden oder eine angegebene Anzahl von Wiederholungen wiederholen, bevor Sie zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="3acd3-206">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3acd3-207">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="3acd3-207">Additional resources</span></span>

* [<span data-ttu-id="3acd3-208">JavaScript-API-Referenz</span><span class="sxs-lookup"><span data-stu-id="3acd3-208">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="3acd3-209">JavaScript-Tutorial</span><span class="sxs-lookup"><span data-stu-id="3acd3-209">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="3acd3-210">WebPack und typescript-Tutorial</span><span class="sxs-lookup"><span data-stu-id="3acd3-210">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="3acd3-211">Hubs</span><span class="sxs-lookup"><span data-stu-id="3acd3-211">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="3acd3-212">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="3acd3-212">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="3acd3-213">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="3acd3-213">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="3acd3-214">Cross-Origin-Anforderungen (cors)</span><span class="sxs-lookup"><span data-stu-id="3acd3-214">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* <span data-ttu-id="3acd3-215">[Server lose Dokumentation zu Azure SignalR Service](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="3acd3-215">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>

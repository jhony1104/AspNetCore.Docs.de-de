---
title: ASP.net Core SignalR JavaScript-Client
author: bradygaster
description: Übersicht über ASP.net Core SignalR JavaScript-Client.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: cb2b3ddc3eba2d6e1ea91c1e7f6715ffa9ad1b08
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2020
ms.locfileid: "84756014"
---
# <a name="aspnet-core-signalr-javascript-client"></a><span data-ttu-id="01f30-103">ASP.net Core SignalR JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="01f30-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="01f30-104">Von [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="01f30-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="01f30-105">Die ASP.net Core SignalR JavaScript-Client Bibliothek ermöglicht es Entwicklern, serverseitigen Hub-Code aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="01f30-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="01f30-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="01f30-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-client-package"></a><span data-ttu-id="01f30-107">Installieren des SignalR Client Pakets</span><span class="sxs-lookup"><span data-stu-id="01f30-107">Install the SignalR client package</span></span>

<span data-ttu-id="01f30-108">Die SignalR JavaScript-Client Bibliothek wird als [NPM](https://www.npmjs.com/) -Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="01f30-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="01f30-109">In den folgenden Abschnitten werden verschiedene Möglichkeiten zum Installieren der-Client Bibliothek erläutert.</span><span class="sxs-lookup"><span data-stu-id="01f30-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="01f30-110">Installieren mit NPM</span><span class="sxs-lookup"><span data-stu-id="01f30-110">Install with npm</span></span>

<span data-ttu-id="01f30-111">Wenn Sie Visual Studio verwenden, führen Sie die folgenden Befehle über die **Paket-Manager-Konsole** im Stamm Ordner aus.</span><span class="sxs-lookup"><span data-stu-id="01f30-111">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="01f30-112">Führen Sie für Visual Studio Code die folgenden Befehle über das **integrierte Terminal**aus.</span><span class="sxs-lookup"><span data-stu-id="01f30-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="01f30-113">NPM installiert den Paket Inhalt im Ordner *" \\ @microsoft\signalr\dist\browser node_modules* ".</span><span class="sxs-lookup"><span data-stu-id="01f30-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="01f30-114">Erstellen Sie einen neuen Ordner mit dem Namen *signalr* unter dem Ordner *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="01f30-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="01f30-115">Kopieren Sie die *signalr.js* Datei in den Ordner " *wwwroot\lib\signalr* ".</span><span class="sxs-lookup"><span data-stu-id="01f30-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="01f30-116">NPM installiert den Paket Inhalt im Ordner *" \\ @aspnet\signalr\dist\browser node_modules* ".</span><span class="sxs-lookup"><span data-stu-id="01f30-116">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="01f30-117">Erstellen Sie einen neuen Ordner mit dem Namen *signalr* unter dem Ordner *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="01f30-117">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="01f30-118">Kopieren Sie die *signalr.js* Datei in den Ordner " *wwwroot\lib\signalr* ".</span><span class="sxs-lookup"><span data-stu-id="01f30-118">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

<span data-ttu-id="01f30-119">Verweisen Sie SignalR auf den JavaScript-Client im- `<script>` Element.</span><span class="sxs-lookup"><span data-stu-id="01f30-119">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="01f30-120">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="01f30-120">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="01f30-121">Verwenden eines Content Delivery Network (CDN)</span><span class="sxs-lookup"><span data-stu-id="01f30-121">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="01f30-122">Wenn Sie die Client Bibliothek ohne die erforderliche Komponente für NPM verwenden möchten, verweisen Sie auf eine CDN-gehostete Kopie der Client Bibliothek.</span><span class="sxs-lookup"><span data-stu-id="01f30-122">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="01f30-123">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="01f30-123">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="01f30-124">Die Client Bibliothek ist unter den folgenden CDNs verfügbar:</span><span class="sxs-lookup"><span data-stu-id="01f30-124">The client library is available on the following CDNs:</span></span>

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="01f30-125">cdnjs verfügbar</span><span class="sxs-lookup"><span data-stu-id="01f30-125">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="01f30-126">jsdelivr</span><span class="sxs-lookup"><span data-stu-id="01f30-126">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="01f30-127">unpkg</span><span class="sxs-lookup"><span data-stu-id="01f30-127">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="01f30-128">cdnjs verfügbar</span><span class="sxs-lookup"><span data-stu-id="01f30-128">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="01f30-129">jsdelivr</span><span class="sxs-lookup"><span data-stu-id="01f30-129">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="01f30-130">unpkg</span><span class="sxs-lookup"><span data-stu-id="01f30-130">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a><span data-ttu-id="01f30-131">Installieren mit Libman</span><span class="sxs-lookup"><span data-stu-id="01f30-131">Install with LibMan</span></span>

<span data-ttu-id="01f30-132">[Libman](xref:client-side/libman/index) kann verwendet werden, um bestimmte Client Bibliotheksdateien aus der CDN-gehosteten Client Bibliothek zu installieren.</span><span class="sxs-lookup"><span data-stu-id="01f30-132">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="01f30-133">Fügen Sie z. b. nur die minierte JavaScript-Datei zum Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="01f30-133">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="01f30-134">Weitere Informationen zu diesem Ansatz finden [Sie unter Hinzufügen der SignalR Client Bibliothek](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="01f30-134">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="01f30-135">Herstellen einer Verbindung mit einem Hub</span><span class="sxs-lookup"><span data-stu-id="01f30-135">Connect to a hub</span></span>

<span data-ttu-id="01f30-136">Mit dem folgenden Code wird eine Verbindung erstellt und gestartet.</span><span class="sxs-lookup"><span data-stu-id="01f30-136">The following code creates and starts a connection.</span></span> <span data-ttu-id="01f30-137">Der Name des Hubs wird nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="01f30-137">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="01f30-138">Ursprungs übergreifende Verbindungen</span><span class="sxs-lookup"><span data-stu-id="01f30-138">Cross-origin connections</span></span>

<span data-ttu-id="01f30-139">Normalerweise laden Browser Verbindungen aus derselben Domäne wie die angeforderte Seite.</span><span class="sxs-lookup"><span data-stu-id="01f30-139">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="01f30-140">Es gibt jedoch Situationen, in denen eine Verbindung mit einer anderen Domäne erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="01f30-140">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="01f30-141">Um zu verhindern, dass ein böswilliger Standort vertrauliche Daten von einem anderen Standort liest, sind [Ursprungs übergreifende Verbindungen](xref:security/cors) standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="01f30-141">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="01f30-142">Um eine Ursprungs übergreifende Anforderung zuzulassen, aktivieren Sie Sie in der- `Startup` Klasse.</span><span class="sxs-lookup"><span data-stu-id="01f30-142">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="01f30-143">Hub-Methoden vom Client abrufen</span><span class="sxs-lookup"><span data-stu-id="01f30-143">Call hub methods from client</span></span>

<span data-ttu-id="01f30-144">JavaScript-Clients rufen öffentliche Methoden für Hubs über die [Aufruf Methode](/javascript/api/%40aspnet/signalr/hubconnection#invoke) von [hubconnection](/javascript/api/%40aspnet/signalr/hubconnection)auf.</span><span class="sxs-lookup"><span data-stu-id="01f30-144">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="01f30-145">Die- `invoke` Methode akzeptiert zwei Argumente:</span><span class="sxs-lookup"><span data-stu-id="01f30-145">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="01f30-146">Der Name der Hub-Methode.</span><span class="sxs-lookup"><span data-stu-id="01f30-146">The name of the hub method.</span></span> <span data-ttu-id="01f30-147">Im folgenden Beispiel ist der Methodenname auf dem Hub `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="01f30-147">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="01f30-148">Alle Argumente, die in der Hub-Methode definiert sind.</span><span class="sxs-lookup"><span data-stu-id="01f30-148">Any arguments defined in the hub method.</span></span> <span data-ttu-id="01f30-149">Im folgenden Beispiel lautet der Argument Name `message` .</span><span class="sxs-lookup"><span data-stu-id="01f30-149">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="01f30-150">Im Beispielcode wird eine Pfeil Funktions Syntax verwendet, die in den aktuellen Versionen aller wichtigen Browser mit Ausnahme von Internet Explorer unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="01f30-150">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="01f30-151">Wenn Sie den Azure- SignalR Dienst im *Server losen Modus*verwenden, können Sie keine hubmethoden von einem Client aus abrufen.</span><span class="sxs-lookup"><span data-stu-id="01f30-151">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="01f30-152">Weitere Informationen finden Sie in der [ SignalR Dokumentation zum Dienst](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="01f30-152">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="01f30-153">Die- `invoke` Methode gibt eine JavaScript- [Zusage](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)zurück.</span><span class="sxs-lookup"><span data-stu-id="01f30-153">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="01f30-154">Die `Promise` wird mit dem Rückgabewert (sofern vorhanden) aufgelöst, wenn die-Methode auf dem Server zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="01f30-154">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="01f30-155">Wenn die-Methode auf dem Server einen Fehler auslöst, `Promise` wird mit der Fehlermeldung zurückgewiesen.</span><span class="sxs-lookup"><span data-stu-id="01f30-155">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="01f30-156">Verwenden `then` Sie die- `catch` Methode und die-Methode für den `Promise` selbst, um diese Fälle (oder `await` Syntax) zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="01f30-156">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="01f30-157">Die- `send` Methode gibt ein JavaScript zurück `Promise` .</span><span class="sxs-lookup"><span data-stu-id="01f30-157">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="01f30-158">Der `Promise` wird aufgelöst, wenn die Nachricht an den Server gesendet wurde.</span><span class="sxs-lookup"><span data-stu-id="01f30-158">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="01f30-159">Wenn beim Senden der Nachricht ein Fehler auftritt, `Promise` wird mit der Fehlermeldung zurückgewiesen.</span><span class="sxs-lookup"><span data-stu-id="01f30-159">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="01f30-160">Verwenden `then` Sie die- `catch` Methode und die-Methode für den `Promise` selbst, um diese Fälle (oder `await` Syntax) zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="01f30-160">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="01f30-161">Die Verwendung von `send` wartet nicht, bis der Server die Nachricht empfangen hat.</span><span class="sxs-lookup"><span data-stu-id="01f30-161">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="01f30-162">Folglich ist es nicht möglich, Daten oder Fehler vom Server zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="01f30-162">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="01f30-163">Client Methoden aus Hub abrufen</span><span class="sxs-lookup"><span data-stu-id="01f30-163">Call client methods from hub</span></span>

<span data-ttu-id="01f30-164">Um Nachrichten vom Hub zu empfangen, definieren Sie mithilfe der [on](/javascript/api/%40aspnet/signalr/hubconnection#on) -Methode von eine Methode `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="01f30-164">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="01f30-165">Der Name der JavaScript-Client Methode.</span><span class="sxs-lookup"><span data-stu-id="01f30-165">The name of the JavaScript client method.</span></span> <span data-ttu-id="01f30-166">Im folgenden Beispiel lautet der Methodenname `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="01f30-166">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="01f30-167">Argumente, die der Hub an die Methode übergibt.</span><span class="sxs-lookup"><span data-stu-id="01f30-167">Arguments the hub passes to the method.</span></span> <span data-ttu-id="01f30-168">Im folgenden Beispiel ist der Argument Wert `message` .</span><span class="sxs-lookup"><span data-stu-id="01f30-168">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="01f30-169">Der vorangehende Code in wird ausgeführt, `connection.on` Wenn der serverseitige Code ihn mithilfe der [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) -Methode aufruft.</span><span class="sxs-lookup"><span data-stu-id="01f30-169">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR<span data-ttu-id="01f30-170">bestimmt, welche Client Methode aufgerufen werden soll, indem der Methodenname und die in und definierten Argumente abgeglichen werden `SendAsync` `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="01f30-170"> determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="01f30-171">Es wird empfohlen, die [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) -Methode für den After-Befehl aufzurufen `HubConnection` `on` .</span><span class="sxs-lookup"><span data-stu-id="01f30-171">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="01f30-172">Dadurch wird sichergestellt, dass ihre Handler registriert werden, bevor Nachrichten empfangen werden.</span><span class="sxs-lookup"><span data-stu-id="01f30-172">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="01f30-173">Fehlerbehandlung und Protokollierung</span><span class="sxs-lookup"><span data-stu-id="01f30-173">Error handling and logging</span></span>

<span data-ttu-id="01f30-174">Verketten Sie eine `catch` Methode an das Ende der `start` Methode, um Client seitige Fehler zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="01f30-174">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="01f30-175">Verwenden `console.error` Sie, um Fehler an die Konsole des Browsers auszugeben.</span><span class="sxs-lookup"><span data-stu-id="01f30-175">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="01f30-176">Richten Sie die Client seitige Protokoll Ablauf Verfolgung ein, indem Sie eine Protokollierung und einen Ereignistyp an die Protokollierung übergeben, wenn die Verbindung hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="01f30-176">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="01f30-177">Nachrichten werden mit der angegebenen Protokollebene und höher protokolliert.</span><span class="sxs-lookup"><span data-stu-id="01f30-177">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="01f30-178">Folgende Protokoll Ebenen sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="01f30-178">Available log levels are as follows:</span></span>

* <span data-ttu-id="01f30-179">`signalR.LogLevel.Error`: Fehlermeldungen.</span><span class="sxs-lookup"><span data-stu-id="01f30-179">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="01f30-180">Protokolliert `Error` nur Meldungen.</span><span class="sxs-lookup"><span data-stu-id="01f30-180">Logs `Error` messages only.</span></span>
* <span data-ttu-id="01f30-181">`signalR.LogLevel.Warning`: Warnmeldungen zu möglichen Fehlern.</span><span class="sxs-lookup"><span data-stu-id="01f30-181">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="01f30-182">Protokolle `Warning` und `Error` Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="01f30-182">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="01f30-183">`signalR.LogLevel.Information`: Status Meldungen ohne Fehler.</span><span class="sxs-lookup"><span data-stu-id="01f30-183">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="01f30-184">Protokolliert `Information` die `Warning` Meldungen, und `Error` .</span><span class="sxs-lookup"><span data-stu-id="01f30-184">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="01f30-185">`signalR.LogLevel.Trace`: Ablauf Verfolgungs Meldungen.</span><span class="sxs-lookup"><span data-stu-id="01f30-185">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="01f30-186">Protokolliert alles, einschließlich Daten, die zwischen dem Hub und dem Client transportiert werden.</span><span class="sxs-lookup"><span data-stu-id="01f30-186">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="01f30-187">Verwenden Sie die Methode configure [relogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) für [hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) , um die Protokollebene zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="01f30-187">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="01f30-188">Nachrichten werden in der Browser Konsole protokolliert.</span><span class="sxs-lookup"><span data-stu-id="01f30-188">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="01f30-189">Clients erneut verbinden</span><span class="sxs-lookup"><span data-stu-id="01f30-189">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="01f30-190">Automatisch wiederherstellen der Verbindung</span><span class="sxs-lookup"><span data-stu-id="01f30-190">Automatically reconnect</span></span>

<span data-ttu-id="01f30-191">Der JavaScript-Client für SignalR kann so konfiguriert werden, dass er mithilfe der- `withAutomaticReconnect` Methode auf [hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)automatisch erneut eine Verbindung herstellt.</span><span class="sxs-lookup"><span data-stu-id="01f30-191">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="01f30-192">Standardmäßig wird die Verbindung nicht automatisch wieder hergestellt.</span><span class="sxs-lookup"><span data-stu-id="01f30-192">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="01f30-193">Ohne Parameter `withAutomaticReconnect()` konfiguriert den Client so, dass 0, 2, 10 und 30 Sekunden gewartet wird, bevor versucht wird, jeden erneuten Verbindungsversuch durchzusetzen, der nach vier fehlgeschlagenen Versuchen angehalten wird.</span><span class="sxs-lookup"><span data-stu-id="01f30-193">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="01f30-194">Vor dem Starten von Wiederholungs versuchen wechselt der `HubConnection` in den `HubConnectionState.Reconnecting` -Zustand und löst seine Rückrufe `onreconnecting` aus, anstatt in den `Disconnected` -Zustand zu wechseln und seine Rückrufe wie eine auszulösen, `onclose` ohne dass die `HubConnection` Automatische Verbindungs Wiederherstellung konfiguriert wurde.</span><span class="sxs-lookup"><span data-stu-id="01f30-194">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="01f30-195">Dies bietet die Möglichkeit, die Benutzer zu warnen, dass die Verbindung unterbrochen wurde, und Benutzeroberflächen Elemente zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="01f30-195">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="01f30-196">Wenn der Client innerhalb der ersten vier Versuche erfolgreich erneut eine Verbindung herstellt, wechselt `HubConnection` zurück in den `Connected` -Zustand und löst seine `onreconnected` Rückrufe aus.</span><span class="sxs-lookup"><span data-stu-id="01f30-196">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="01f30-197">Dies bietet die Möglichkeit, den Benutzern mitzuteilen, dass die Verbindung wieder hergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="01f30-197">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="01f30-198">Da die Verbindung mit dem Server vollständig neu ist, `connectionId` wird dem Rückruf ein neuer bereitgestellt `onreconnected` .</span><span class="sxs-lookup"><span data-stu-id="01f30-198">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="01f30-199">Der `onreconnected` -Parameter des Rückrufs ist nicht `connectionId` definiert `HubConnection` , wenn der zum über [springen der Aushandlung](xref:signalr/configuration#configure-client-options)konfiguriert wurde.</span><span class="sxs-lookup"><span data-stu-id="01f30-199">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="01f30-200">`withAutomaticReconnect()`konfiguriert nicht `HubConnection` , um anfängliche Start Fehler zu wiederholen, sodass Start Fehler manuell behandelt werden müssen:</span><span class="sxs-lookup"><span data-stu-id="01f30-200">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="01f30-201">Wenn der Client nicht innerhalb der ersten vier Versuche erneut eine Verbindung herstellt, wechselt `HubConnection` in den `Disconnected` -Zustand und auslöst seine [OnClose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) -Rückrufe.</span><span class="sxs-lookup"><span data-stu-id="01f30-201">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="01f30-202">Dies bietet die Möglichkeit, den Benutzern mitzuteilen, dass die Verbindung dauerhaft verloren gegangen ist, und es wird empfohlen, die Seite zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="01f30-202">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="01f30-203">Um eine benutzerdefinierte Anzahl von Wiederholungs versuchen zu konfigurieren, bevor die Verbindung getrennt wird, oder um die zeitliche Verbindung wiederherzustellen, `withAutomaticReconnect` akzeptiert ein Array von Zahlen, das die Verzögerung in Millisekunden angibt, die gewartet werden soll, bevor die einzelnen Wiederholungs Versuche gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="01f30-203">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="01f30-204">Im vorangehenden Beispiel wird der so konfiguriert `HubConnection` , dass nach dem Verlust der Verbindung erneut versucht wird, die Verbindung herzustellen.</span><span class="sxs-lookup"><span data-stu-id="01f30-204">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="01f30-205">Dies gilt auch für die Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="01f30-205">This is also true for the default configuration.</span></span>

<span data-ttu-id="01f30-206">Wenn der erste Wiederholungsversuch fehlschlägt, wird der zweite Wiederholungsversuch auch sofort gestartet, anstatt zwei Sekunden zu warten, wie in der Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="01f30-206">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="01f30-207">Wenn der zweite Wiederholungsversuch fehlschlägt, wird der dritte Versuch der erneuten Verbindungs Herstellung in 10 Sekunden gestartet. Dies entspricht der Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="01f30-207">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="01f30-208">Das benutzerdefinierte Verhalten weicht dann erneut vom Standardverhalten ab, indem nach dem dritten Wiederholungsversuch beendet wird, anstatt einen weiteren Wiederholungsversuch in weiteren 30 Sekunden auszuführen, wie in der Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="01f30-208">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="01f30-209">Wenn Sie eine noch größere Kontrolle über die zeitliche Steuerung und die Anzahl der automatischen Wiederholungs Versuche wünschen, `withAutomaticReconnect` akzeptiert ein Objekt, das die- `IRetryPolicy` Schnittstelle implementiert, die über eine einzige Methode mit dem Namen verfügt `nextRetryDelayInMilliseconds` .</span><span class="sxs-lookup"><span data-stu-id="01f30-209">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="01f30-210">`nextRetryDelayInMilliseconds`nimmt ein einzelnes Argument mit dem Typ an `RetryContext` .</span><span class="sxs-lookup"><span data-stu-id="01f30-210">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="01f30-211">`RetryContext`Verfügt über drei Eigenschaften: `previousRetryCount` , `elapsedMilliseconds` und `retryReason` die sind `number` , und `number` `Error` .</span><span class="sxs-lookup"><span data-stu-id="01f30-211">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="01f30-212">Vor dem ersten Versuch, die Verbindung wiederherzustellen, sind sowohl `previousRetryCount` als auch `elapsedMilliseconds` NULL, und der Fehler, der bewirkt hat, `retryReason` dass die Verbindung unterbrochen wurde.</span><span class="sxs-lookup"><span data-stu-id="01f30-212">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="01f30-213">Nach jedem fehlgeschlagenen Wiederholungsversuch `previousRetryCount` wird der Wert um eins erhöht `elapsedMilliseconds` . der Wert wird aktualisiert, um die Zeitspanne in Millisekunden wiederherzustellen, die bis zum letzten Verbindungsversuch aufgewendet wurde. Dies ist der Fehler, der `retryReason` zum Fehlschlagen des letzten Wiederholungs Versuchs geführt hat.</span><span class="sxs-lookup"><span data-stu-id="01f30-213">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="01f30-214">`nextRetryDelayInMilliseconds`muss entweder eine Zahl zurückgeben, die die Anzahl der Millisekunden angibt, die vor dem nächsten erneuten Verbindungsversuch gewartet werden soll, oder, `null` Wenn die `HubConnection` Verbindung nicht mehr herstellen soll.</span><span class="sxs-lookup"><span data-stu-id="01f30-214">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
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

<span data-ttu-id="01f30-215">Alternativ dazu können Sie auch Code schreiben, mit dem der Client manuell erneut eine Verbindung herstellt, wie in [Manuelles Wiederherstellen der Verbindung](#manually-reconnect)gezeigt.</span><span class="sxs-lookup"><span data-stu-id="01f30-215">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="01f30-216">Verbindung manuell wiederherstellen</span><span class="sxs-lookup"><span data-stu-id="01f30-216">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="01f30-217">Vor 3,0 wird vom JavaScript-Client für SignalR nicht automatisch eine erneute Verbindung hergestellt.</span><span class="sxs-lookup"><span data-stu-id="01f30-217">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="01f30-218">Sie müssen Code schreiben, mit dem der Client manuell erneut eine Verbindung herstellt.</span><span class="sxs-lookup"><span data-stu-id="01f30-218">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="01f30-219">Der folgende Code veranschaulicht einen typischen manuellen Verbindungs Ansatz:</span><span class="sxs-lookup"><span data-stu-id="01f30-219">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="01f30-220">Eine Funktion (in diesem Fall die `start` Funktion) wird erstellt, um die Verbindung zu starten.</span><span class="sxs-lookup"><span data-stu-id="01f30-220">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="01f30-221">Die `start` -Funktion wird im-Ereignishandler der Verbindung aufgerufen `onclose` .</span><span class="sxs-lookup"><span data-stu-id="01f30-221">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="01f30-222">Eine reale Implementierung würde einen exponentiellen Backoff verwenden oder eine angegebene Anzahl von Wiederholungen wiederholen, bevor Sie zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="01f30-222">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="01f30-223">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="01f30-223">Additional resources</span></span>

* [<span data-ttu-id="01f30-224">JavaScript-API-Referenz</span><span class="sxs-lookup"><span data-stu-id="01f30-224">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="01f30-225">JavaScript-Tutorial</span><span class="sxs-lookup"><span data-stu-id="01f30-225">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="01f30-226">WebPack und typescript-Tutorial</span><span class="sxs-lookup"><span data-stu-id="01f30-226">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="01f30-227">Hubs</span><span class="sxs-lookup"><span data-stu-id="01f30-227">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="01f30-228">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="01f30-228">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="01f30-229">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="01f30-229">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="01f30-230">Cross-Origin-Anforderungen (cors)</span><span class="sxs-lookup"><span data-stu-id="01f30-230">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* <span data-ttu-id="01f30-231">[Azure- SignalR Dienst Server lose Dokumentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="01f30-231">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>

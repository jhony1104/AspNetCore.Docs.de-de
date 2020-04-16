---
title: ASP.NET SignalR Core JavaScript-Client
author: bradygaster
description: Überblick über SignalR ASP.NET Core JavaScript-Client.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: 43b2cacf9f415ec422a00b28246f30c8ad74de29
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440856"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a><span data-ttu-id="1f0d5-103">ASP.NET SignalR Core JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="1f0d5-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="1f0d5-104">Von [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="1f0d5-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="1f0d5-105">Die ASP.NET SignalR Core JavaScript-Clientbibliothek ermöglicht Entwicklern den Aufruf von serverseitigem Hubcode.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="1f0d5-106">[Beispielcode anzeigen oder herunterladen](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([downloaden](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1f0d5-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-opno-locsignalr-client-package"></a><span data-ttu-id="1f0d5-107">Installieren SignalR des Clientpakets</span><span class="sxs-lookup"><span data-stu-id="1f0d5-107">Install the SignalR client package</span></span>

<span data-ttu-id="1f0d5-108">Die SignalR JavaScript-Clientbibliothek wird als [npm-Paket](https://www.npmjs.com/) bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="1f0d5-109">In den folgenden Abschnitten werden verschiedene Möglichkeiten zum Installieren der Clientbibliothek beschrieben.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="1f0d5-110">Installieren mit npm</span><span class="sxs-lookup"><span data-stu-id="1f0d5-110">Install with npm</span></span>

<span data-ttu-id="1f0d5-111">Wenn Sie Visual Studio verwenden, führen Sie die folgenden Befehle aus der **Package Manager-Konsole** aus, während Sie sich im Stammordner befinden.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-111">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="1f0d5-112">Führen Sie für Visual Studio Code die folgenden Befehle vom **integrierten Terminal**aus aus.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="1f0d5-113">npm installiert den Paketinhalt im \*Ordner node_modules.\\ \*</span><span class="sxs-lookup"><span data-stu-id="1f0d5-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="1f0d5-114">Erstellen Sie einen neuen Ordner mit dem Namen *Signalr* unter dem Ordner *wwwroot\\lib.*</span><span class="sxs-lookup"><span data-stu-id="1f0d5-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="1f0d5-115">Kopieren Sie die Datei *signalr.js* in den Ordner *wwwroot-lib-signalr.*</span><span class="sxs-lookup"><span data-stu-id="1f0d5-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="1f0d5-116">npm installiert den Paketinhalt im \*Ordner node_modules.\\ \*</span><span class="sxs-lookup"><span data-stu-id="1f0d5-116">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="1f0d5-117">Erstellen Sie einen neuen Ordner mit dem Namen *Signalr* unter dem Ordner *wwwroot\\lib.*</span><span class="sxs-lookup"><span data-stu-id="1f0d5-117">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="1f0d5-118">Kopieren Sie die Datei *signalr.js* in den Ordner *wwwroot-lib-signalr.*</span><span class="sxs-lookup"><span data-stu-id="1f0d5-118">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

<span data-ttu-id="1f0d5-119">Verweisen SignalR Sie auf den `<script>` JavaScript-Client im Element.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-119">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="1f0d5-120">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="1f0d5-120">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="1f0d5-121">Verwenden eines Content Delivery Network (CDN)</span><span class="sxs-lookup"><span data-stu-id="1f0d5-121">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="1f0d5-122">Um die Clientbibliothek ohne die npm-Voraussetzung zu verwenden, verweisen Sie auf eine von CDN gehostete Kopie der Clientbibliothek.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-122">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="1f0d5-123">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="1f0d5-123">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="1f0d5-124">Die Clientbibliothek ist auf den folgenden CDNs verfügbar:</span><span class="sxs-lookup"><span data-stu-id="1f0d5-124">The client library is available on the following CDNs:</span></span>

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="1f0d5-125">cdnjs</span><span class="sxs-lookup"><span data-stu-id="1f0d5-125">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="1f0d5-126">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="1f0d5-126">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="1f0d5-127">unpkg</span><span class="sxs-lookup"><span data-stu-id="1f0d5-127">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="1f0d5-128">cdnjs</span><span class="sxs-lookup"><span data-stu-id="1f0d5-128">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="1f0d5-129">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="1f0d5-129">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="1f0d5-130">unpkg</span><span class="sxs-lookup"><span data-stu-id="1f0d5-130">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a><span data-ttu-id="1f0d5-131">Installieren mit LibMan</span><span class="sxs-lookup"><span data-stu-id="1f0d5-131">Install with LibMan</span></span>

<span data-ttu-id="1f0d5-132">[LibMan](xref:client-side/libman/index) kann verwendet werden, um bestimmte Clientbibliotheksdateien aus der cdN-gehosteten Clientbibliothek zu installieren.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-132">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="1f0d5-133">Fügen Sie dem Projekt beispielsweise nur die vermählte JavaScript-Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-133">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="1f0d5-134">Weitere Informationen zu diesem Ansatz finden Sie unter [Hinzufügen SignalR der Clientbibliothek](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="1f0d5-134">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="1f0d5-135">Herstellen einer Verbindung mit einem Hub</span><span class="sxs-lookup"><span data-stu-id="1f0d5-135">Connect to a hub</span></span>

<span data-ttu-id="1f0d5-136">Der folgende Code erstellt und startet eine Verbindung.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-136">The following code creates and starts a connection.</span></span> <span data-ttu-id="1f0d5-137">Der Name des Hubs ist nicht berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-137">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a><span data-ttu-id="1f0d5-138">Ursprungsübergreifende Verbindungen</span><span class="sxs-lookup"><span data-stu-id="1f0d5-138">Cross-origin connections</span></span>

<span data-ttu-id="1f0d5-139">In der Regel laden Browser Verbindungen aus derselben Domäne wie die angeforderte Seite.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-139">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="1f0d5-140">Es gibt jedoch Fälle, in denen eine Verbindung zu einer anderen Domäne erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-140">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="1f0d5-141">Um zu verhindern, dass eine schädliche Site vertrauliche Daten von einer anderen Site liest, sind [ursprungsübergreifende Verbindungen](xref:security/cors) standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-141">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="1f0d5-142">Um eine ursprungsübergreifende Anforderung zuzulassen, `Startup` aktivieren Sie sie in der Klasse.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-142">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="1f0d5-143">Call Hub-Methoden vom Client</span><span class="sxs-lookup"><span data-stu-id="1f0d5-143">Call hub methods from client</span></span>

<span data-ttu-id="1f0d5-144">JavaScript-Clients rufen öffentliche Methoden auf Hubs über die [Aufrufmethode](/javascript/api/%40aspnet/signalr/hubconnection#invoke) der [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection)auf.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-144">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="1f0d5-145">Die `invoke` Methode akzeptiert zwei Argumente:</span><span class="sxs-lookup"><span data-stu-id="1f0d5-145">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="1f0d5-146">Der Name der Hub-Methode.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-146">The name of the hub method.</span></span> <span data-ttu-id="1f0d5-147">Im folgenden Beispiel lautet `SendMessage`der Methodenname auf dem Hub .</span><span class="sxs-lookup"><span data-stu-id="1f0d5-147">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="1f0d5-148">Alle in der Hub-Methode definierten Argumente.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-148">Any arguments defined in the hub method.</span></span> <span data-ttu-id="1f0d5-149">Im folgenden Beispiel lautet `message`der Argumentname .</span><span class="sxs-lookup"><span data-stu-id="1f0d5-149">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="1f0d5-150">Der Beispielcode verwendet pfeilfunktionssyntax, die in aktuellen Versionen aller gängigen Browser außer Internet Explorer unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-150">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="1f0d5-151">Wenn Sie Azure SignalR Service im *serverlosen Modus*verwenden, können Sie Hubmethoden nicht von einem Client aufrufen.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-151">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="1f0d5-152">Weitere Informationen finden Sie in der [ SignalR Servicedokumentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="1f0d5-152">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="1f0d5-153">Die `invoke` Methode gibt ein JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)zurück.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-153">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="1f0d5-154">Der `Promise` wird mit dem Rückgabewert (falls vorhanden) aufgelöst, wenn die Methode auf dem Server zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-154">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="1f0d5-155">Wenn die Methode auf dem Server `Promise` einen Fehler auslöst, wird die mit der Fehlermeldung zurückgewiesen.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-155">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="1f0d5-156">Verwenden `then` Sie `catch` die `Promise` und Methoden auf der `await` selbst, um diese Fälle (oder Syntax) zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-156">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="1f0d5-157">Die `send` Methode gibt `Promise`ein JavaScript zurück.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-157">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="1f0d5-158">Der `Promise` wird aufgelöst, wenn die Nachricht an den Server gesendet wurde.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-158">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="1f0d5-159">Wenn beim Senden der Nachricht `Promise` ein Fehler auftritt, wird der mit der Fehlermeldung zurückgewiesen.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-159">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="1f0d5-160">Verwenden `then` Sie `catch` die `Promise` und Methoden auf der `await` selbst, um diese Fälle (oder Syntax) zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-160">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="1f0d5-161">Die `send` Verwendung wartet nicht, bis der Server die Nachricht empfangen hat.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-161">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="1f0d5-162">Daher ist es nicht möglich, Daten oder Fehler vom Server zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-162">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="1f0d5-163">Aufrufen von Clientmethoden vom Hub</span><span class="sxs-lookup"><span data-stu-id="1f0d5-163">Call client methods from hub</span></span>

<span data-ttu-id="1f0d5-164">Um Nachrichten vom Hub zu empfangen, definieren Sie `HubConnection`eine Methode mit der [on-Methode](/javascript/api/%40aspnet/signalr/hubconnection#on) der .</span><span class="sxs-lookup"><span data-stu-id="1f0d5-164">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="1f0d5-165">Der Name der JavaScript-Clientmethode.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-165">The name of the JavaScript client method.</span></span> <span data-ttu-id="1f0d5-166">Im folgenden Beispiel lautet `ReceiveMessage`der Methodenname .</span><span class="sxs-lookup"><span data-stu-id="1f0d5-166">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="1f0d5-167">Argumente, die der Hub an die Methode übergibt.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-167">Arguments the hub passes to the method.</span></span> <span data-ttu-id="1f0d5-168">Im folgenden Beispiel lautet `message`der Argumentwert .</span><span class="sxs-lookup"><span data-stu-id="1f0d5-168">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="1f0d5-169">Der vorherige `connection.on` Code in wird ausgeführt, wenn serverseitiger Code ihn mithilfe der [SendAsync-Methode](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) aufruft.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-169">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR<span data-ttu-id="1f0d5-170">bestimmt, welche Clientmethode aufruft werden soll, `SendAsync` `connection.on`indem der in und definierte Methodenname und die in definierten Argumente übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-170"> determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="1f0d5-171">Als bewährte Methode rufen Sie die `HubConnection` `on` [Startmethode](/javascript/api/%40aspnet/signalr/hubconnection#start) für die after auf.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-171">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="1f0d5-172">Dadurch wird sichergestellt, dass Ihre Handler registriert sind, bevor Nachrichten empfangen werden.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-172">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="1f0d5-173">Fehlerbehandlung und Protokollierung</span><span class="sxs-lookup"><span data-stu-id="1f0d5-173">Error handling and logging</span></span>

<span data-ttu-id="1f0d5-174">Verketten `catch` Sie eine Methode `start` an das Ende der Methode, um clientseitige Fehler zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-174">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="1f0d5-175">Verwenden `console.error` Sie diese Datei, um Fehler an der Konsole des Browsers auszugeben.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-175">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="1f0d5-176">Richten Sie die clientseitige Protokollablaufverfolgung ein, indem Sie eine Protokollierung und einen Ereignistyp übergeben, um zu protokollieren, wenn die Verbindung hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-176">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="1f0d5-177">Nachrichten werden mit der angegebenen Protokollebene und höher protokolliert.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-177">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="1f0d5-178">Die verfügbaren Protokollebenen sind wie folgt:</span><span class="sxs-lookup"><span data-stu-id="1f0d5-178">Available log levels are as follows:</span></span>

* <span data-ttu-id="1f0d5-179">`signalR.LogLevel.Error`&ndash; Fehlermeldungen.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-179">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="1f0d5-180">Protokolliert `Error` nur Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-180">Logs `Error` messages only.</span></span>
* <span data-ttu-id="1f0d5-181">`signalR.LogLevel.Warning`&ndash; Warnmeldungen zu möglichen Fehlern.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-181">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="1f0d5-182">Protokolle `Warning`und `Error` Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-182">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="1f0d5-183">`signalR.LogLevel.Information`&ndash; Statusmeldungen ohne Fehler.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-183">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="1f0d5-184">Protokolle `Information` `Warning`, `Error` und Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-184">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="1f0d5-185">`signalR.LogLevel.Trace`&ndash; Verfolgen Sie Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-185">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="1f0d5-186">Protokolliert alles, einschließlich Daten, die zwischen Hub und Client transportiert werden.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-186">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="1f0d5-187">Verwenden Sie die [configureLogging-Methode](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) auf [HubConnectionBuilder,](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) um die Protokollebene zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-187">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="1f0d5-188">Nachrichten werden in der Browserkonsole protokolliert.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-188">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="1f0d5-189">Wiederherstellen von Clients</span><span class="sxs-lookup"><span data-stu-id="1f0d5-189">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="1f0d5-190">Automatische Wiederverbindung</span><span class="sxs-lookup"><span data-stu-id="1f0d5-190">Automatically reconnect</span></span>

<span data-ttu-id="1f0d5-191">Der JavaScript-Client für SignalR kann so konfiguriert `withAutomaticReconnect` werden, dass die Verbindung mit der Methode auf [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)automatisch wiederhergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-191">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="1f0d5-192">Die Verbindung wird nicht standardmäßig wiederhergestellt.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-192">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="1f0d5-193">Konfiguriert den `withAutomaticReconnect()` Client ohne Parameter so, dass er 0, 2, 10 und 30 Sekunden wartet, bevor jeder erneute Verbindungsversuch versucht wird, und wird nach vier fehlgeschlagenen Versuchen angehalten.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-193">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="1f0d5-194">Vor dem Starten von `HubConnection` Wiederverbindungsversuchen `HubConnectionState.Reconnecting` wird der `onreconnecting` in den Zustand übergehen `Disconnected` und seine `onclose` Rückrufe auslösen, `HubConnection` anstatt in den Zustand zu wechseln und seine Rückrufe wie eine ohne automatische Wiederverbindung zu auslösen.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-194">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="1f0d5-195">Dies bietet die Möglichkeit, Benutzer vor dem Verlust der Verbindung zu warnen und UI-Elemente zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-195">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="1f0d5-196">Wenn der Client innerhalb der ersten vier `HubConnection` Versuche erfolgreich `Connected` eine Verbindung `onreconnected` herstellt, wechselt der in den Zustand zurück und feuert seine Rückrufe ab.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-196">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="1f0d5-197">Dies bietet die Möglichkeit, die Benutzer über die Wiederherzustellen der Verbindung zu informieren.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-197">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="1f0d5-198">Da die Verbindung für den Server `connectionId` völlig neu aussieht, wird eine neue für den `onreconnected` Rückruf bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-198">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="1f0d5-199">Der `onreconnected` Parameter des `connectionId` Rückrufs ist nicht `HubConnection` definiert, wenn der zum Überspringen der [Aushandlung](xref:signalr/configuration#configure-client-options)konfiguriert wurde.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-199">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="1f0d5-200">`withAutomaticReconnect()`konfiguriert die `HubConnection` nicht, um anfängliche Startfehler erneut zu versuchen, daher müssen Startfehler manuell behandelt werden:</span><span class="sxs-lookup"><span data-stu-id="1f0d5-200">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="1f0d5-201">Wenn der Client innerhalb der ersten vier Versuche `HubConnection` keine erneute `Disconnected` Verbindung herstellt, wechselt der in den Zustand und feuert seine [Onclose-Rückrufe](/javascript/api/%40aspnet/signalr/hubconnection#onclose) ab.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-201">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="1f0d5-202">Dies bietet die Möglichkeit, Benutzer darüber zu informieren, dass die Verbindung dauerhaft unterbrochen wurde, und empfehlen, die Seite zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="1f0d5-202">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="1f0d5-203">Um eine benutzerdefinierte Anzahl von Wiederverbindungsversuchen zu konfigurieren, bevor `withAutomaticReconnect` das Zeitzeichen für die erneute Verbindung getrennt oder geändert wird, wird ein Array von Zahlen akzeptiert, die die Verzögerung in Millisekunden darstellen, um zu warten, bevor jeder erneute Verbindungsversuch gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-203">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="1f0d5-204">Im vorherigen Beispiel `HubConnection` wird konfiguriert, dass der Versuch sofort nach Verbindungsverlust erneut hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-204">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="1f0d5-205">Dies gilt auch für die Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-205">This is also true for the default configuration.</span></span>

<span data-ttu-id="1f0d5-206">Wenn der erste Wiederherstellungsversuch fehlschlägt, wird der zweite Wiederherstellungsversuch ebenfalls sofort gestartet, anstatt 2 Sekunden wie in der Standardkonfiguration zu warten.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-206">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="1f0d5-207">Wenn der zweite Wiederherstellungsversuch fehlschlägt, beginnt der dritte Wiederherstellungsversuch in 10 Sekunden, was wiederum der Standardkonfiguration entspricht.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-207">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="1f0d5-208">Das benutzerdefinierte Verhalten weicht dann erneut vom Standardverhalten ab, indem es nach dem dritten Erneutverbindungsversuchsfehler beendet wird, anstatt einen weiteren erneuten Verbindungsversuch in weiteren 30 Sekunden wie in der Standardkonfiguration zu versuchen.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-208">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="1f0d5-209">Wenn Sie noch mehr Kontrolle über das Timing und `withAutomaticReconnect` die Anzahl `IRetryPolicy` der automatischen Wiederverbindungsversuche `nextRetryDelayInMilliseconds`wünschen, akzeptiert ein Objekt, das die Schnittstelle implementiert, die über eine einzelne Methode mit dem Namen verfügt.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-209">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="1f0d5-210">`nextRetryDelayInMilliseconds`nimmt ein einzelnes `RetryContext`Argument mit dem Typ .</span><span class="sxs-lookup"><span data-stu-id="1f0d5-210">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="1f0d5-211">Der `RetryContext` hat drei `previousRetryCount` `elapsedMilliseconds` Eigenschaften: `retryReason` , `number`und `number` die `Error` sind a , a bzw. ein.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-211">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="1f0d5-212">Vor dem ersten erneuten `previousRetryCount` `elapsedMilliseconds` Verbindungsversuch sind beide `retryReason` und Null, und der wird der Fehler sein, der den Verlust der Verbindung verursacht hat.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-212">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="1f0d5-213">Nach jedem fehlgeschlagenen `previousRetryCount` Wiederholungsversuch wird um `elapsedMilliseconds` eins erhöht, wird aktualisiert, um die Zeit widerzuspiegeln, die bisher in Millisekunden für die erneute Verbindung aufgewendet wurde, und es `retryReason` wird der Fehler sein, der den letzten erneuten Verbindungsversuch verursacht hat.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-213">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="1f0d5-214">`nextRetryDelayInMilliseconds`muss entweder eine Zahl zurückgeben, die die Anzahl der Millisekunden darstellt, die vor dem nächsten erneuten Verbindungsversuch gewartet werden soll, oder `null` wenn die `HubConnection` erneute Verbindung beendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-214">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="1f0d5-215">Alternativ können Sie Code schreiben, der den Client manuell wieder verbindet, wie unter [Manuelle Wiederherstellung der Verbindung](#manually-reconnect)gezeigt.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-215">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="1f0d5-216">Manuelle Verbindung wieder herstellen</span><span class="sxs-lookup"><span data-stu-id="1f0d5-216">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="1f0d5-217">Vor 3.0 stellt der JavaScript-Client für SignalR nicht automatisch eine erneute Verbindung her.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-217">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="1f0d5-218">Sie müssen Code schreiben, der den Client manuell wieder verbindet.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-218">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="1f0d5-219">Der folgende Code veranschaulicht einen typischen manuellen Wiederverbindungsansatz:</span><span class="sxs-lookup"><span data-stu-id="1f0d5-219">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="1f0d5-220">Eine Funktion (in diesem `start` Fall die Funktion) wird erstellt, um die Verbindung zu starten.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-220">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="1f0d5-221">Rufen `start` Sie die Funktion `onclose` im Ereignishandler der Verbindung auf.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-221">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="1f0d5-222">Eine reale Implementierung würde einen exponentiellen Back-off verwenden oder eine bestimmte Anzahl von Wiederholungen wiederholen, bevor sie aufgibt.</span><span class="sxs-lookup"><span data-stu-id="1f0d5-222">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1f0d5-223">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="1f0d5-223">Additional resources</span></span>

* [<span data-ttu-id="1f0d5-224">JavaScript-API-Referenz</span><span class="sxs-lookup"><span data-stu-id="1f0d5-224">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="1f0d5-225">JavaScript-Tutorial</span><span class="sxs-lookup"><span data-stu-id="1f0d5-225">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="1f0d5-226">WebPack- und TypeScript-Tutorial</span><span class="sxs-lookup"><span data-stu-id="1f0d5-226">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="1f0d5-227">Hubs</span><span class="sxs-lookup"><span data-stu-id="1f0d5-227">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="1f0d5-228">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="1f0d5-228">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="1f0d5-229">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="1f0d5-229">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="1f0d5-230">Ursprungsübergreifende Anforderungen (CORS)</span><span class="sxs-lookup"><span data-stu-id="1f0d5-230">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* <span data-ttu-id="1f0d5-231">[Serverlose SignalR Azure Service-Dokumentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="1f0d5-231">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>

---
uid: signalr/overview/guide-to-the-api/hubs-api-guide-javascript-client
title: ASP.NET SignalR-Hubs-API-Leitfaden – JavaScript-Client | Microsoft-Dokumentation
author: pfletcher
description: Dieses Dokument enthält eine Einführung zur Verwendung von den Hubs-API für SignalR-Version 2 in JavaScript-Clients wie Browsern und Windows Store (WinJS) Application...
ms.author: riande
ms.date: 01/15/2019
ms.assetid: a9fd4dc0-1b96-4443-82ca-932a5b4a8ea4
msc.legacyurl: /signalr/overview/guide-to-the-api/hubs-api-guide-javascript-client
msc.type: authoredcontent
ms.openlocfilehash: 12d675b6a2f2f6acdd8c3a5d0d27b5ad2fb1efc4
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396310"
---
<a name="aspnet-signalr-hubs-api-guide---javascript-client"></a><span data-ttu-id="effd0-103">ASP.NET SignalR-Hubs-API-Leitfaden – JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="effd0-103">ASP.NET SignalR Hubs API Guide - JavaScript Client</span></span>
====================

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="effd0-104">Dieses Dokument enthält eine Einführung zur Verwendung von den Hubs-API für SignalR-Version 2 in JavaScript-Clients wie Browsern und Anwendungen für Windows Store (WinJS).</span><span class="sxs-lookup"><span data-stu-id="effd0-104">This document provides an introduction to using the Hubs API for SignalR version 2 in JavaScript clients, such as browsers and Windows Store (WinJS) applications.</span></span>
>
> <span data-ttu-id="effd0-105">Der SignalR-Hubs-API können Sie die Remoteprozeduraufrufe (RPCs) von einem Server verbundene Clients und von den Clients an den Server vornehmen.</span><span class="sxs-lookup"><span data-stu-id="effd0-105">The SignalR Hubs API enables you to make remote procedure calls (RPCs) from a server to connected clients and from clients to the server.</span></span> <span data-ttu-id="effd0-106">Im Server-Code Sie Methoden definieren, die von Clients aufgerufen werden können, und rufen Sie Methoden, die auf dem Client ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="effd0-106">In server code, you define methods that can be called by clients, and you call methods that run on the client.</span></span> <span data-ttu-id="effd0-107">Im Clientcode Sie Methoden definieren, die vom Server aufgerufen werden können, und rufen Sie Methoden, die auf dem Server ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="effd0-107">In client code, you define methods that can be called from the server, and you call methods that run on the server.</span></span> <span data-ttu-id="effd0-108">SignalR ist für alle Client-zu-Server sich für Sie übernimmt.</span><span class="sxs-lookup"><span data-stu-id="effd0-108">SignalR takes care of all of the client-to-server plumbing for you.</span></span>
>
> <span data-ttu-id="effd0-109">SignalR bietet außerdem eine Low-Level-API wird aufgerufen, dauerhafte Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="effd0-109">SignalR also offers a lower-level API called Persistent Connections.</span></span> <span data-ttu-id="effd0-110">Eine Einführung in SignalR-Hubs und dauerhafte Verbindungen, finden Sie unter [Einführung zu SignalR](../getting-started/introduction-to-signalr.md).</span><span class="sxs-lookup"><span data-stu-id="effd0-110">For an introduction to SignalR, Hubs, and Persistent Connections, see [Introduction to SignalR](../getting-started/introduction-to-signalr.md).</span></span>
>
> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="effd0-111">In diesem Thema verwendeten Softwareversionen</span><span class="sxs-lookup"><span data-stu-id="effd0-111">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="effd0-112">Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="effd0-112">Visual Studio 2017</span></span>](https://visualstudio.microsoft.com/downloads/)
> - <span data-ttu-id="effd0-113">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="effd0-113">.NET 4.5</span></span>
> - <span data-ttu-id="effd0-114">SignalR-Version 2</span><span class="sxs-lookup"><span data-stu-id="effd0-114">SignalR version 2</span></span>
>
>
>
> ## <a name="previous-versions-of-this-topic"></a><span data-ttu-id="effd0-115">Vorherige Versionen dieses Themas</span><span class="sxs-lookup"><span data-stu-id="effd0-115">Previous versions of this topic</span></span>
>
> <span data-ttu-id="effd0-116">Weitere Informationen zu früheren Versionen von SignalR, finden Sie unter [ältere Versionen von SignalR](../older-versions/index.md).</span><span class="sxs-lookup"><span data-stu-id="effd0-116">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="effd0-117">Fragen und Kommentare</span><span class="sxs-lookup"><span data-stu-id="effd0-117">Questions and comments</span></span>
>
> <span data-ttu-id="effd0-118">Lassen Sie Feedback, auf wie Ihnen in diesem Tutorial gefallen hat und was wir in den Kommentaren am unteren Rand der Seite verbessern können.</span><span class="sxs-lookup"><span data-stu-id="effd0-118">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="effd0-119">Wenn Sie Fragen, die nicht direkt mit dem Tutorial verknüpft sind haben, können Sie sie veröffentlichen das [ASP.NET SignalR-Forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) oder [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="effd0-119">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>

## <a name="overview"></a><span data-ttu-id="effd0-120">Übersicht</span><span class="sxs-lookup"><span data-stu-id="effd0-120">Overview</span></span>

<span data-ttu-id="effd0-121">Dieses Dokument enthält folgende Abschnitte:</span><span class="sxs-lookup"><span data-stu-id="effd0-121">This document contains the following sections:</span></span>

- [<span data-ttu-id="effd0-122">Den generierten Proxy und was dies für Sie übernimmt.</span><span class="sxs-lookup"><span data-stu-id="effd0-122">The generated proxy and what it does for you</span></span>](#genproxy)

    - [<span data-ttu-id="effd0-123">Verwenden Sie den generierten proxy</span><span class="sxs-lookup"><span data-stu-id="effd0-123">When to use the generated proxy</span></span>](#cantusegenproxy)
- [<span data-ttu-id="effd0-124">Client-Setup</span><span class="sxs-lookup"><span data-stu-id="effd0-124">Client Setup</span></span>](#clientsetup)

    - [<span data-ttu-id="effd0-125">Wie Sie auf den dynamisch generierten Proxy verweisen.</span><span class="sxs-lookup"><span data-stu-id="effd0-125">How to reference the dynamically generated proxy</span></span>](#dynamicproxy)
    - [<span data-ttu-id="effd0-126">Vorgehensweise: erstellen eine physische Datei für SignalR generierter proxy</span><span class="sxs-lookup"><span data-stu-id="effd0-126">How to create a physical file for the SignalR generated proxy</span></span>](#manualproxy)
- [<span data-ttu-id="effd0-127">Gewusst wie: Herstellen einer Verbindung</span><span class="sxs-lookup"><span data-stu-id="effd0-127">How to establish a connection</span></span>](#establishconnection)

    - [<span data-ttu-id="effd0-128">$. connection.hub ist das gleiche Objekt erstellt, $.hubConnection()</span><span class="sxs-lookup"><span data-stu-id="effd0-128">$.connection.hub is the same object that $.hubConnection() creates</span></span>](#connequivalence)
    - [<span data-ttu-id="effd0-129">Asynchrone Ausführung der Start-Methode</span><span class="sxs-lookup"><span data-stu-id="effd0-129">Asynchronous execution of the start method</span></span>](#asyncstart)
- [<span data-ttu-id="effd0-130">Gewusst wie: Herstellen einer Verbindung domänenübergreifende</span><span class="sxs-lookup"><span data-stu-id="effd0-130">How to establish a cross-domain connection</span></span>](#crossdomain)
- [<span data-ttu-id="effd0-131">Gewusst wie: Konfigurieren der Verbindung</span><span class="sxs-lookup"><span data-stu-id="effd0-131">How to configure the connection</span></span>](#configureconnection)

    - [<span data-ttu-id="effd0-132">Gewusst wie: Angeben von Abfragezeichenfolgen-Parameter</span><span class="sxs-lookup"><span data-stu-id="effd0-132">How to specify query string parameters</span></span>](#querystring)
    - [<span data-ttu-id="effd0-133">Das Angeben der Transportmethode</span><span class="sxs-lookup"><span data-stu-id="effd0-133">How to specify the transport method</span></span>](#transport)
- [<span data-ttu-id="effd0-134">Wie Sie einen Proxy für einen Hub-Klasse abrufen</span><span class="sxs-lookup"><span data-stu-id="effd0-134">How to get a proxy for a Hub class</span></span>](#getproxy)
- [<span data-ttu-id="effd0-135">Wie Sie Methoden auf dem Client zu definieren, die der Server aufgerufen werden können</span><span class="sxs-lookup"><span data-stu-id="effd0-135">How to define methods on the client that the server can call</span></span>](#callclient)
- [<span data-ttu-id="effd0-136">Gewusst wie: Servermethoden vom Client aufrufen.</span><span class="sxs-lookup"><span data-stu-id="effd0-136">How to call server methods from the client</span></span>](#callserver)
- [<span data-ttu-id="effd0-137">Gewusst wie: Behandeln der Objektlebensdauer-Ereignisse der Verbindung</span><span class="sxs-lookup"><span data-stu-id="effd0-137">How to handle connection lifetime events</span></span>](#connectionlifetime)
- [<span data-ttu-id="effd0-138">Gewusst wie: Behandeln von Fehlern</span><span class="sxs-lookup"><span data-stu-id="effd0-138">How to handle errors</span></span>](#handleerrors)
- [<span data-ttu-id="effd0-139">Gewusst wie: Aktivieren der clientseitigen Protokollierung</span><span class="sxs-lookup"><span data-stu-id="effd0-139">How to enable client-side logging</span></span>](#logging)

<span data-ttu-id="effd0-140">Dokumentation zum Erstellen des Servers oder Clients mit .NET zu programmieren finden Sie unter den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="effd0-140">For documentation on how to program the server or .NET clients, see the following resources:</span></span>

- [<span data-ttu-id="effd0-141">SignalR-Hubs-API-Guide - Server</span><span class="sxs-lookup"><span data-stu-id="effd0-141">SignalR Hubs API Guide - Server</span></span>](hubs-api-guide-server.md)
- [<span data-ttu-id="effd0-142">Leitfaden für SignalR Hubs-API – .NET-Client</span><span class="sxs-lookup"><span data-stu-id="effd0-142">SignalR Hubs API Guide - .NET Client</span></span>](hubs-api-guide-net-client.md)

<span data-ttu-id="effd0-143">Die SignalR-2-Serverkomponente ist nur verfügbar unter .NET 4.5, (obwohl es ein .NET Client für SignalR 2 auf .NET 4.0 gibt).</span><span class="sxs-lookup"><span data-stu-id="effd0-143">The SignalR 2 server component is only available on .NET 4.5 (though there is a .NET client for SignalR 2 on .NET 4.0).</span></span>

<a id="genproxy"></a>

## <a name="the-generated-proxy-and-what-it-does-for-you"></a><span data-ttu-id="effd0-144">Den generierten Proxy und was dies für Sie übernimmt.</span><span class="sxs-lookup"><span data-stu-id="effd0-144">The generated proxy and what it does for you</span></span>

<span data-ttu-id="effd0-145">Sie können einen JavaScript-Client für die Kommunikation mit einem SignalR-Dienst, mit oder ohne einen Proxy, den für Sie SignalR generiert programmieren.</span><span class="sxs-lookup"><span data-stu-id="effd0-145">You can program a JavaScript client to communicate with a SignalR service with or without a proxy that SignalR generates for you.</span></span> <span data-ttu-id="effd0-146">Funktionsweise der Proxy für Sie ist die Syntax des Codes vereinfachen, die Sie die Verbindung verwenden, Write-Methoden, die der Server aufruft, und rufen Methoden auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="effd0-146">What the proxy does for you is simplify the syntax of the code you use to connect, write methods that the server calls, and call methods on the server.</span></span>

<span data-ttu-id="effd0-147">Beim Schreiben von Code zum Aufrufen von Servermethoden, der generierte Proxy können Sie Syntax verwenden, die aussieht, als ob Sie eine lokale Funktion ausgeführt haben: Sie können schreiben `serverMethod(arg1, arg2)` anstelle von `invoke('serverMethod', arg1, arg2)`.</span><span class="sxs-lookup"><span data-stu-id="effd0-147">When you write code to call server methods, the generated proxy enables you to use syntax that looks as though you were executing a local function: you can write `serverMethod(arg1, arg2)` instead of `invoke('serverMethod', arg1, arg2)`.</span></span> <span data-ttu-id="effd0-148">Die Syntax des generierten Proxys kann auch einen sofortigen und entpackte clientseitige Fehler, wenn Sie einen Server-Methodennamen richtig eingegeben.</span><span class="sxs-lookup"><span data-stu-id="effd0-148">The generated proxy syntax also enables an immediate and intelligible client-side error if you mistype a server method name.</span></span> <span data-ttu-id="effd0-149">Und wenn Sie die Datei, die definiert, die Proxys manuell erstellen, erhalten Sie auch IntelliSense-Unterstützung für das Schreiben von Code, der Servermethoden aufruft.</span><span class="sxs-lookup"><span data-stu-id="effd0-149">And if you manually create the file that defines the proxies, you can also get IntelliSense support for writing code that calls server methods.</span></span>

<span data-ttu-id="effd0-150">Nehmen wir beispielsweise an, dass Sie die folgende hubklasse auf dem Server verfügen:</span><span class="sxs-lookup"><span data-stu-id="effd0-150">For example, suppose you have the following Hub class on the server:</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample1.cs?highlight=1,3,5)]

<span data-ttu-id="effd0-151">Die folgenden Codebeispiele zeigen, wie sieht der JavaScript-Code zum Aufrufen der `NewContosoChatMessage` Methode auf dem Server und Empfangen von Aufrufen von der `addContosoChatMessageToPage` Methode auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="effd0-151">The following code examples show what JavaScript code looks like for invoking the `NewContosoChatMessage` method on the server and receiving invocations of the `addContosoChatMessageToPage` method from the server.</span></span>

<span data-ttu-id="effd0-152">**Mit den generierten proxy**</span><span class="sxs-lookup"><span data-stu-id="effd0-152">**With the generated proxy**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample2.js?highlight=1-2,8)]

<span data-ttu-id="effd0-153">**Ohne den generierten proxy**</span><span class="sxs-lookup"><span data-stu-id="effd0-153">**Without the generated proxy**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample3.js?highlight=2-3,9)]

<a id="cantusegenproxy"></a>

### <a name="when-to-use-the-generated-proxy"></a><span data-ttu-id="effd0-154">Verwenden Sie den generierten proxy</span><span class="sxs-lookup"><span data-stu-id="effd0-154">When to use the generated proxy</span></span>

<span data-ttu-id="effd0-155">Wenn Sie mehrere Ereignishandler für eine Clientmethode zu registrieren, die der Server ruft möchten, können nicht Sie den generierten Proxy verwenden.</span><span class="sxs-lookup"><span data-stu-id="effd0-155">If you want to register multiple event handlers for a client method that the server calls, you can't use the generated proxy.</span></span> <span data-ttu-id="effd0-156">Andernfalls, Sie können auch den generierten Proxy verwenden oder nicht basierend auf Ihrer Codierung Einstellung.</span><span class="sxs-lookup"><span data-stu-id="effd0-156">Otherwise, you can choose to use the generated proxy or not based on your coding preference.</span></span> <span data-ttu-id="effd0-157">Wenn Sie nicht ihn verwenden, Sie müssen keine verweisen die URL "Signalr/Hubs" in eine `script` Element im Clientcode.</span><span class="sxs-lookup"><span data-stu-id="effd0-157">If you choose not to use it, you don't have to reference the "signalr/hubs" URL in a `script` element in your client code.</span></span>

<a id="clientsetup"></a>

## <a name="client-setup"></a><span data-ttu-id="effd0-158">Client-setup</span><span class="sxs-lookup"><span data-stu-id="effd0-158">Client setup</span></span>

<span data-ttu-id="effd0-159">Verweise auf jQuery und die SignalR Core JavaScript-Datei wird von ein JavaScript-Client benötigt.</span><span class="sxs-lookup"><span data-stu-id="effd0-159">A JavaScript client requires references to jQuery and the SignalR core JavaScript file.</span></span> <span data-ttu-id="effd0-160">Die jQuery-Version muss es sich um 1.6.4 oder höher Hauptversionen, z. B. 1.7.2, 1.8.2 oder 1.9.1 sein.</span><span class="sxs-lookup"><span data-stu-id="effd0-160">The jQuery version must be 1.6.4 or major later versions, such as 1.7.2, 1.8.2, or 1.9.1.</span></span> <span data-ttu-id="effd0-161">Wenn Sie den generierten Proxy verwenden möchten, benötigen Sie auch einen Verweis auf den Proxy generiert, SignalR JavaScript-Datei.</span><span class="sxs-lookup"><span data-stu-id="effd0-161">If you decide to use the generated proxy, you also need a reference to the SignalR generated proxy JavaScript file.</span></span> <span data-ttu-id="effd0-162">Das folgende Beispiel zeigt, wie die Verweise in einer HTML-Seite aussehen könnte, die den generierten Proxy verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="effd0-162">The following example shows what the references might look like in an HTML page that uses the generated proxy.</span></span>

[!code-html[Main](hubs-api-guide-javascript-client/samples/sample4.html)]

<span data-ttu-id="effd0-163">Diese Verweise in folgender Reihenfolge enthalten sein müssen: jQuery Vorname, Nachname von SignalR Core danach und SignalR-Proxys.</span><span class="sxs-lookup"><span data-stu-id="effd0-163">These references must be included in this order: jQuery first, SignalR core after that, and SignalR proxies last.</span></span>

<a id="dynamicproxy"></a>

### <a name="how-to-reference-the-dynamically-generated-proxy"></a><span data-ttu-id="effd0-164">Wie Sie auf den dynamisch generierten Proxy verweisen.</span><span class="sxs-lookup"><span data-stu-id="effd0-164">How to reference the dynamically generated proxy</span></span>

<span data-ttu-id="effd0-165">Im vorherigen Beispiel wird der Verweis auf den generierten SignalR-Proxy für dynamisch generierte JavaScript-Code, nicht zu einer physischen Datei.</span><span class="sxs-lookup"><span data-stu-id="effd0-165">In the preceding example, the reference to the SignalR generated proxy is to dynamically generated JavaScript code, not to a physical file.</span></span> <span data-ttu-id="effd0-166">SignalR JavaScript-Code für den Proxy dynamisch erstellt und zeigt es an den Client als Antwort auf die URL "/ Signalr/Hubs".</span><span class="sxs-lookup"><span data-stu-id="effd0-166">SignalR creates the JavaScript code for the proxy on the fly and serves it to the client in response to the "/signalr/hubs" URL.</span></span> <span data-ttu-id="effd0-167">Wenn Sie zuvor angegeben haben, eine andere base-URL für SignalR-Verbindungen auf dem Server in Ihrer `MapSignalR` -Methode, die URL für die dynamisch generierte Proxydatei lautet die benutzerdefinierte URL mit "/ Hubs" angefügt.</span><span class="sxs-lookup"><span data-stu-id="effd0-167">If you specified a different base URL for SignalR connections on the server in your `MapSignalR` method, the URL for the dynamically generated proxy file is your custom URL with "/hubs" appended to it.</span></span>

> [!NOTE]
> <span data-ttu-id="effd0-168">Verwenden Sie für Windows 8 (Windows Store) JavaScript-Clients die physischen Proxydatei statt der dynamisch generierten aus.</span><span class="sxs-lookup"><span data-stu-id="effd0-168">For Windows 8 (Windows Store) JavaScript clients, use the physical proxy file instead of the dynamically generated one.</span></span> <span data-ttu-id="effd0-169">Weitere Informationen finden Sie unter [Vorgehensweise: erstellen eine physische Datei für SignalR generierter Proxy](#manualproxy) weiter unten in diesem Thema.</span><span class="sxs-lookup"><span data-stu-id="effd0-169">For more information, see [How to create a physical file for the SignalR generated proxy](#manualproxy) later in this topic.</span></span>


<span data-ttu-id="effd0-170">Verwenden Sie in ASP.NET MVC 4 oder 5 Razor-Ansicht die Tilde zum Verweisen auf das Stammverzeichnis der Anwendung in Ihrem Proxy Dateiverweis aus:</span><span class="sxs-lookup"><span data-stu-id="effd0-170">In an ASP.NET MVC 4 or 5 Razor view, use the tilde to refer to the application root in your proxy file reference:</span></span>

[!code-html[Main](hubs-api-guide-javascript-client/samples/sample5.html)]

<span data-ttu-id="effd0-171">Weitere Informationen zur Verwendung von SignalR in MVC 5 finden Sie unter [erste Schritte mit SignalR und MVC 5](../getting-started/tutorial-getting-started-with-signalr-and-mvc.md).</span><span class="sxs-lookup"><span data-stu-id="effd0-171">For more information about using SignalR in MVC 5, see [Getting Started with SignalR and MVC 5](../getting-started/tutorial-getting-started-with-signalr-and-mvc.md).</span></span>

<span data-ttu-id="effd0-172">Verwenden Sie in einer ASP.NET MVC 3 Razor-Ansicht `Url.Content` Proxy-Datei als Referenz:</span><span class="sxs-lookup"><span data-stu-id="effd0-172">In an ASP.NET MVC 3 Razor view, use `Url.Content` for your proxy file reference:</span></span>

[!code-cshtml[Main](hubs-api-guide-javascript-client/samples/sample6.cshtml)]

<span data-ttu-id="effd0-173">Verwenden Sie in einer ASP.NET Web Forms-Anwendung `ResolveClientUrl` von Proxys für Datei Verweis, oder registrieren Sie ihn über das ScriptManager, die mit einem relativen Pfad Stamm app (beginnend mit einer Tilde):</span><span class="sxs-lookup"><span data-stu-id="effd0-173">In an ASP.NET Web Forms application, use `ResolveClientUrl` for your proxies file reference or register it via the ScriptManager using an app root relative path (starting with a tilde):</span></span>

[!code-aspx[Main](hubs-api-guide-javascript-client/samples/sample7.aspx)]

<span data-ttu-id="effd0-174">Verwenden Sie als allgemeine Regel die gleiche Methode zum Angeben der URL "/ Signalr/Hubs", die Sie für CSS oder JavaScript-Dateien verwenden.</span><span class="sxs-lookup"><span data-stu-id="effd0-174">As a general rule, use the same method for specifying the "/signalr/hubs" URL that you use for CSS or JavaScript files.</span></span> <span data-ttu-id="effd0-175">Wenn Sie eine URL angeben, ohne mit einer Tilde, wird in einigen Szenarien Ihrer Anwendung fehlerfrei, wenn Sie in Visual Studio mit IIS Express testen jedoch mit einen 404-Fehler fehl schlägt, wenn Sie in der vollständigen Variante von IIS bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="effd0-175">If you specify a URL without using a tilde, in some scenarios your application will work correctly when you test in Visual Studio using IIS Express but will fail with a 404 error when you deploy to full IIS.</span></span> <span data-ttu-id="effd0-176">Weitere Informationen finden Sie unter **Auflösen von Verweisen auf Ressourcen auf Stammebene** in [Webserver in Visual Studio für ASP.NET-Webprojekte](https://msdn.microsoft.com/library/58wxa9w5.aspx) auf der MSDN-Website.</span><span class="sxs-lookup"><span data-stu-id="effd0-176">For more information, see **Resolving References to Root-Level Resources** in [Web Servers in Visual Studio for ASP.NET Web Projects](https://msdn.microsoft.com/library/58wxa9w5.aspx) on the MSDN site.</span></span>

<span data-ttu-id="effd0-177">Wenn Sie ein Webprojekt in Visual Studio 2017 im Debugmodus ausgeführt, und wenn Sie Internet Explorer als Standardbrowser verwenden, Sie die Proxydatei im sehen **Projektmappen-Explorer** unter **Skripts**.</span><span class="sxs-lookup"><span data-stu-id="effd0-177">When you run a web project in Visual Studio 2017 in debug mode, and if you use Internet Explorer as your browser, you can see the proxy file in **Solution Explorer** under **Scripts**.</span></span>

<span data-ttu-id="effd0-178">Um den Inhalt der Datei anzuzeigen, doppelklicken Sie auf **Hubs**.</span><span class="sxs-lookup"><span data-stu-id="effd0-178">To see the contents of the file, double-click **hubs**.</span></span> <span data-ttu-id="effd0-179">Wenn Sie nicht Visual Studio 2012 oder 2013 und Internet Explorer verwenden oder wenn Sie nicht im Debugmodus sind, können Sie auch den Inhalt der Datei abrufen, indem Sie zu der URL "/ SignalR/Hubs".</span><span class="sxs-lookup"><span data-stu-id="effd0-179">If you are not using Visual Studio 2012 or 2013 and Internet Explorer, or if you are not in debug mode, you can also get the contents of the file by browsing to the "/signalR/hubs" URL.</span></span> <span data-ttu-id="effd0-180">Wenn an Ihrem Standort ausgeführt wird z. B. `http://localhost:56699`, wechseln Sie zu `http://localhost:56699/SignalR/hubs` in Ihrem Browser.</span><span class="sxs-lookup"><span data-stu-id="effd0-180">For example, if your site is running at `http://localhost:56699`, go to `http://localhost:56699/SignalR/hubs` in your browser.</span></span>

<a id="manualproxy"></a>

### <a name="how-to-create-a-physical-file-for-the-signalr-generated-proxy"></a><span data-ttu-id="effd0-181">Vorgehensweise: erstellen eine physische Datei für SignalR generierter proxy</span><span class="sxs-lookup"><span data-stu-id="effd0-181">How to create a physical file for the SignalR generated proxy</span></span>

<span data-ttu-id="effd0-182">Als Alternative zu den dynamisch generierten Proxy können Sie eine physikalische Datei mit den Proxycode zu erstellen und auf diese Datei verweisen.</span><span class="sxs-lookup"><span data-stu-id="effd0-182">As an alternative to the dynamically generated proxy, you can create a physical file that has the proxy code and reference that file.</span></span> <span data-ttu-id="effd0-183">Sie sollten dies für die Kontrolle über die Zwischenspeicherung oder die Bündelung Verhalten oder IntelliSense zu erhalten, wenn Sie Aufrufe von Servermethoden codieren.</span><span class="sxs-lookup"><span data-stu-id="effd0-183">You might want to do that for control over caching or bundling behavior, or to get IntelliSense when you are coding calls to server methods.</span></span>

<span data-ttu-id="effd0-184">Um eine Proxydatei zu erstellen, führen Sie die folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="effd0-184">To create a proxy file, perform the following steps:</span></span>

1. <span data-ttu-id="effd0-185">Installieren Sie die [Microsoft.AspNet.SignalR.Utils](https://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/) NuGet-Paket.</span><span class="sxs-lookup"><span data-stu-id="effd0-185">Install the [Microsoft.AspNet.SignalR.Utils](https://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/) NuGet package.</span></span>
2. <span data-ttu-id="effd0-186">Öffnen Sie eine Eingabeaufforderung, und navigieren Sie zu der *Tools* Ordner, der die SignalR.exe-Datei enthält.</span><span class="sxs-lookup"><span data-stu-id="effd0-186">Open a command prompt and browse to the *tools* folder that contains the SignalR.exe file.</span></span> <span data-ttu-id="effd0-187">Der Ordner "Tools" ist an folgendem Speicherort:</span><span class="sxs-lookup"><span data-stu-id="effd0-187">The tools folder is at the following location:</span></span>

    `[your solution folder]\packages\Microsoft.AspNet.SignalR.Utils.2.1.0\tools`
3. <span data-ttu-id="effd0-188">Geben Sie folgenden Befehl ein:</span><span class="sxs-lookup"><span data-stu-id="effd0-188">Enter the following command:</span></span>

    `signalr ghp /path:[path to the .dll that contains your Hub class]`

    <span data-ttu-id="effd0-189">Der Pfad zu Ihrer *DLL* ist in der Regel die *Bin* Ordner in Ihrem Projektordner.</span><span class="sxs-lookup"><span data-stu-id="effd0-189">The path to your *.dll* is typically the *bin* folder in your project folder.</span></span>

    <span data-ttu-id="effd0-190">Dieser Befehl erstellt eine Datei namens *"Server.js"* im gleichen Ordner wie *signalr.exe*.</span><span class="sxs-lookup"><span data-stu-id="effd0-190">This command creates a file named *server.js* in the same folder as *signalr.exe*.</span></span>
4. <span data-ttu-id="effd0-191">Platzieren der *"Server.js"* Datei in einem geeigneten Ordner in Ihrem Projekt, benennen Sie sie nach Bedarf für Ihre Anwendung und einen Verweis darauf anstelle der "Signalr/Hubs" Verweis hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="effd0-191">Put the *server.js* file in an appropriate folder in your project, rename it as appropriate for your application, and add a reference to it in place of the "signalr/hubs" reference.</span></span>

<a id="establishconnection"></a>

## <a name="how-to-establish-a-connection"></a><span data-ttu-id="effd0-192">Gewusst wie: Herstellen einer Verbindung</span><span class="sxs-lookup"><span data-stu-id="effd0-192">How to establish a connection</span></span>

<span data-ttu-id="effd0-193">Bevor Sie eine Verbindung herstellen können, müssen Sie ein Verbindungsobjekt zu erstellen, erstellen Sie einen Proxy und Registrierung von Ereignishandlern für Methoden, die vom Server aufgerufen werden können.</span><span class="sxs-lookup"><span data-stu-id="effd0-193">Before you can establish a connection, you have to create a connection object, create a proxy, and register event handlers for methods that can be called from the server.</span></span> <span data-ttu-id="effd0-194">Wenn der Proxy und die Ereignishandler eingerichtet sind, Herstellung einer Verbindung durch Aufrufen der `start` Methode.</span><span class="sxs-lookup"><span data-stu-id="effd0-194">When the proxy and event handlers are set up, establish the connection by calling the `start` method.</span></span>

<span data-ttu-id="effd0-195">Wenn Sie den generierten Proxy verwenden, müssen Sie das Verbindungsobjekt in Ihrem eigenen Code zu erstellen, da der generierte Proxycode für Sie übernimmt.</span><span class="sxs-lookup"><span data-stu-id="effd0-195">If you are using the generated proxy, you don't have to create the connection object in your own code because the generated proxy code does it for you.</span></span>

<a id="nogenconnection"></a>

<span data-ttu-id="effd0-196">**Herstellen einer Verbindung (mit den generierten Proxy)**</span><span class="sxs-lookup"><span data-stu-id="effd0-196">**Establish a connection (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample8.js?highlight=5)]

<span data-ttu-id="effd0-197">**Herstellen einer Verbindung (ohne den generierten Proxy)**</span><span class="sxs-lookup"><span data-stu-id="effd0-197">**Establish a connection (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample9.js?highlight=1,6)]

<span data-ttu-id="effd0-198">Der Beispielcode verwendet die Standardeinstellung "/ Signalr" die URL für die Verbindung mit Ihrem SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="effd0-198">The sample code uses the default "/signalr" URL to connect to your SignalR service.</span></span> <span data-ttu-id="effd0-199">WPF-Clientcode für die Methode wird vom Server ohne Parameter aufgerufen. [ASP.NET SignalR Hubs API Guide - Server - The /signalr URL](hubs-api-guide-server.md#signalrurl).</span><span class="sxs-lookup"><span data-stu-id="effd0-199">For information about how to specify a different base URL, see [ASP.NET SignalR Hubs API Guide - Server - The /signalr URL](hubs-api-guide-server.md#signalrurl).</span></span>

<span data-ttu-id="effd0-200">Standardmäßig ist der Hub-Speicherort dem aktuellen Server; Wenn Sie auf einen anderen Server herstellen, geben Sie die URL vor dem Aufruf der `start` Methode, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="effd0-200">By default, the hub location is the current server; if you are connecting to a different server, specify the URL before calling the `start` method, as shown in the following example:</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample10.js)]

> [!NOTE]
> <span data-ttu-id="effd0-201">Normalerweise der Registrierung von Ereignishandlern vor dem Aufruf der `start` Methode zum Herstellen der Verbindung.</span><span class="sxs-lookup"><span data-stu-id="effd0-201">Normally you register event handlers before calling the `start` method to establish the connection.</span></span> <span data-ttu-id="effd0-202">Wenn einige Ereignishandler zu registrieren, nachdem die Verbindung hergestellt werden soll, können Sie dies, aber Sie müssen mindestens eines Ihrer einen oder mehrere Ereignishandler vor dem Aufruf Registrieren der `start` Methode.</span><span class="sxs-lookup"><span data-stu-id="effd0-202">If you want to register some event handlers after establishing the connection, you can do that, but you must register at least one of your event handler(s) before calling the `start` method.</span></span> <span data-ttu-id="effd0-203">Ein Grund hierfür ist, dass in einer Anwendung kann viele Hubs vorhanden sein, aber nicht empfehlenswert Auslösen der `OnConnected` Ereignis für jede Hub-Instanz, wenn Sie nur einen der Verträge verwenden möchten.</span><span class="sxs-lookup"><span data-stu-id="effd0-203">One reason for this is that there can be many Hubs in an application, but you wouldn't want to trigger the `OnConnected` event on every Hub if you are only going to use to one of them.</span></span> <span data-ttu-id="effd0-204">Wenn die Verbindung hergestellt ist, das Vorhandensein einer Clientmethode auf einem Hub-Proxy ist, was SignalR auslösen soll die `OnConnected` Ereignis.</span><span class="sxs-lookup"><span data-stu-id="effd0-204">When the connection is established, the presence of a client method on a Hub's proxy is what tells SignalR to trigger the `OnConnected` event.</span></span> <span data-ttu-id="effd0-205">Wenn Sie sich nicht die Ereignishandler vor dem Aufruf Registrieren der `start` -Methode, Sie werden zum Aufrufen von Methoden in den Hub, aber der Hub `OnConnected` Methode nicht aufgerufen und keine Clientmethoden werden vom Server aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="effd0-205">If you don't register any event handlers before calling the `start` method, you will be able to invoke methods on the Hub, but the Hub's `OnConnected` method won't be called and no client methods will be invoked from the server.</span></span>


<a id="connequivalence"></a>

### <a name="connectionhub-is-the-same-object-that-hubconnection-creates"></a><span data-ttu-id="effd0-206">$. connection.hub ist das gleiche Objekt erstellt, $.hubConnection()</span><span class="sxs-lookup"><span data-stu-id="effd0-206">$.connection.hub is the same object that $.hubConnection() creates</span></span>

<span data-ttu-id="effd0-207">Wie Sie aus den Beispielen sehen können, wenn Sie den generierten Proxy verwenden `$.connection.hub` bezieht sich auf das Verbindungsobjekt.</span><span class="sxs-lookup"><span data-stu-id="effd0-207">As you can see from the examples, when you use the generated proxy, `$.connection.hub` refers to the connection object.</span></span> <span data-ttu-id="effd0-208">Dies ist das gleiche Objekt, das Sie durch den Aufruf erhalten `$.hubConnection()` kein bei den generierten Proxy verwenden.</span><span class="sxs-lookup"><span data-stu-id="effd0-208">This is the same object that you get by calling `$.hubConnection()` when you aren't using the generated proxy.</span></span> <span data-ttu-id="effd0-209">Der generierte Proxycode wird die Verbindung für Sie erstellt, durch die folgende Anweisung ausführen:</span><span class="sxs-lookup"><span data-stu-id="effd0-209">The generated proxy code creates the connection for you by executing the following statement:</span></span>

![Erstellen eine Verbindung in die generierte Proxydatei](hubs-api-guide-javascript-client/_static/image3.png)

<span data-ttu-id="effd0-211">Wenn Sie den generierten Proxy verwenden, möchten Sie etwas `$.connection.hub` , Sie mit einem Verbindungsobjekt durchführen können, wenn Sie den generierten Proxy nicht verwenden.</span><span class="sxs-lookup"><span data-stu-id="effd0-211">When you're using the generated proxy, you can do anything with `$.connection.hub` that you can do with a connection object when you're not using the generated proxy.</span></span>

<a id="asyncstart"></a>

### <a name="asynchronous-execution-of-the-start-method"></a><span data-ttu-id="effd0-212">Asynchrone Ausführung der Start-Methode</span><span class="sxs-lookup"><span data-stu-id="effd0-212">Asynchronous execution of the start method</span></span>

<span data-ttu-id="effd0-213">Wenn die `start` Methode asynchron ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="effd0-213">The `start` method executes asynchronously.</span></span> <span data-ttu-id="effd0-214">Gibt eine [jQuery verzögerten Objekt](http://api.jquery.com/category/deferred-object/), was bedeutet, dass Sie die Rückruffunktionen hinzufügen können, durch Aufrufen von Methoden wie z. B. `pipe`, `done`, und `fail`.</span><span class="sxs-lookup"><span data-stu-id="effd0-214">It returns a [jQuery Deferred object](http://api.jquery.com/category/deferred-object/), which means that you can add callback functions by calling methods such as `pipe`, `done`, and `fail`.</span></span> <span data-ttu-id="effd0-215">Wenn Sie über Code, die verfügen, die nach dem Herstellen der Verbindung ausgeführt werden sollen, z. B. einen Aufruf an eine Servermethode, fügen Sie diesen Code in eine Callback-Funktion, oder aus einer Rückruffunktion aufgerufen Sie wird.</span><span class="sxs-lookup"><span data-stu-id="effd0-215">If you have code that you want to execute after the connection is established, such as a call to a server method, put that code in a callback function or call it from a callback function.</span></span> <span data-ttu-id="effd0-216">Die `.done` Callback-Methode wird immer dann ausgeführt, nachdem die Verbindung hergestellt wurde, und nachdem alle code Sie in haben Ihrem `OnConnected` Ereignishandlermethode auf dem Server die Ausführung abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="effd0-216">The `.done` callback method is executed after the connection has been established, and after any code that you have in your `OnConnected` event handler method on the server finishes executing.</span></span>

<span data-ttu-id="effd0-217">Wenn Sie die Anweisung "Jetzt verbunden" aus dem vorherigen Beispiel als die nächste Codezeile nach dem Einfügen der `start` Methodenaufruf (nicht in eine `.done` Rückruf), wird die `console.log` Zeile ausgeführt, bevor die Verbindung hergestellt wird, wie im folgenden dargestellt Beispiel:</span><span class="sxs-lookup"><span data-stu-id="effd0-217">If you put the "Now connected" statement from the preceding example as the next line of code after the `start` method call (not in a `.done` callback), the `console.log` line will execute before the connection is established, as shown in the following example:</span></span>

![Falsche Methode zum Schreiben von Code, der ausgeführt wird, nachdem die Verbindung hergestellt wird](hubs-api-guide-javascript-client/_static/image5.png)

<a id="crossdomain"></a>

## <a name="how-to-establish-a-cross-domain-connection"></a><span data-ttu-id="effd0-219">Gewusst wie: Herstellen einer Verbindung domänenübergreifende</span><span class="sxs-lookup"><span data-stu-id="effd0-219">How to establish a cross-domain connection</span></span>

<span data-ttu-id="effd0-220">In der Regel, wenn der Browser eine Seite lädt `http://contoso.com`, die SignalR-Verbindung ist auf der gleichen Domäne `http://contoso.com/signalr`.</span><span class="sxs-lookup"><span data-stu-id="effd0-220">Typically if the browser loads a page from `http://contoso.com`, the SignalR connection is in the same domain, at `http://contoso.com/signalr`.</span></span> <span data-ttu-id="effd0-221">Wenn die Seite `http://contoso.com` stellt eine Verbindung mit `http://fabrikam.com/signalr`, d. h. eine domänenübergreifende-Verbindung.</span><span class="sxs-lookup"><span data-stu-id="effd0-221">If the page from `http://contoso.com` makes a connection to `http://fabrikam.com/signalr`, that is a cross-domain connection.</span></span> <span data-ttu-id="effd0-222">Aus Sicherheitsgründen sind die domänenübergreifende Verbindungen standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="effd0-222">For security reasons, cross-domain connections are disabled by default.</span></span>

<span data-ttu-id="effd0-223">In SignalR 1.x, domänenübergreifende Anforderungen wurden gesteuert, indem einem einzigen EnableCrossDomain-Flag.</span><span class="sxs-lookup"><span data-stu-id="effd0-223">In SignalR 1.x, cross domain requests were controlled by a single EnableCrossDomain flag.</span></span> <span data-ttu-id="effd0-224">Dieses Flag gesteuert, sowohl JSONP und CORS-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="effd0-224">This flag controlled both JSONP and CORS requests.</span></span> <span data-ttu-id="effd0-225">Zur Erhöhung der Flexibilität-alle CORS-Unterstützung wurde aus dem die Server-Komponente von SignalR entfernt (JavaScript-Clients weiterhin verwenden CORS normalerweise, wenn es erkannt wird, dass der Browser unterstützt), und neue OWIN-Middleware zur Unterstützung dieser Szenarios verfügbar gemacht wurde.</span><span class="sxs-lookup"><span data-stu-id="effd0-225">For greater flexibility, all CORS support has been removed from the server component of SignalR (JavaScript clients still use CORS normally if it is detected that the browser supports it), and new OWIN middleware has been made available to support these scenarios.</span></span>

<span data-ttu-id="effd0-226">Wenn JSONP auf dem Client (domänenübergreifende Anforderungen in älteren Browsern unterstützen) erforderlich ist, müssen sie explizit aktiviert werden, durch Festlegen von `EnableJSONP` auf die `HubConfiguration` -Objekt `true`, wie unten dargestellt.</span><span class="sxs-lookup"><span data-stu-id="effd0-226">If JSONP is required on the client (to support cross-domain requests in older browsers), it will need to be enabled explicitly by setting `EnableJSONP` on the `HubConfiguration` object to `true`, as shown below.</span></span> <span data-ttu-id="effd0-227">JSONP ist standardmäßig deaktiviert, da er als weniger sicher als CORS ist.</span><span class="sxs-lookup"><span data-stu-id="effd0-227">JSONP is disabled by default, as it is less secure than CORS.</span></span>

<span data-ttu-id="effd0-228">**Microsoft.Owin.Cors und dem Projekt hinzugefügt:** Um diese Bibliothek zu installieren, führen Sie den folgenden Befehl in der Paket-Manager-Konsole ein:</span><span class="sxs-lookup"><span data-stu-id="effd0-228">**Adding Microsoft.Owin.Cors to your project:** To install this library, run the following command in the Package Manager Console:</span></span>

`Install-Package Microsoft.Owin.Cors`

<span data-ttu-id="effd0-229">Dieser Befehl fügt die 2.1.0 Version des Pakets zu Ihrem Projekt.</span><span class="sxs-lookup"><span data-stu-id="effd0-229">This command will add the 2.1.0 version of the package to your project.</span></span>

### <a name="calling-usecors"></a><span data-ttu-id="effd0-230">Aufrufen von "usecors"</span><span class="sxs-lookup"><span data-stu-id="effd0-230">Calling UseCors</span></span>

 <span data-ttu-id="effd0-231">Der folgende Codeausschnitt veranschaulicht, wie domänenübergreifende Verbindungen in SignalR 2. implementiert wird.</span><span class="sxs-lookup"><span data-stu-id="effd0-231">The following code snippet demonstrates how to implement cross-domain connections in SignalR 2.</span></span>

<span data-ttu-id="effd0-232">**Implementieren von domänenübergreifende Anforderungen in SignalR 2**</span><span class="sxs-lookup"><span data-stu-id="effd0-232">**Implementing cross-domain requests in SignalR 2**</span></span>

<span data-ttu-id="effd0-233">Der folgende Code veranschaulicht, wie CORS oder JSONP in einem SignalR-2-Projekt zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="effd0-233">The following code demonstrates how to enable CORS or JSONP in a SignalR 2 project.</span></span> <span data-ttu-id="effd0-234">Dieses Codebeispiel verwendet `Map` und `RunSignalR` anstelle von `MapSignalR`, sodass nur für die SignalR-Anforderungen, die CORS-Unterstützung erfordern, die CORS-Middleware ausgeführt wird (und nicht für den gesamten Datenverkehr an den im angegebenen Pfad `MapSignalR`.) Map kann auch für andere Middleware, die verwendet werden, die für eine bestimmte URL-Präfix, nicht aber für die gesamte Anwendung ausgeführt werden muss.</span><span class="sxs-lookup"><span data-stu-id="effd0-234">This code sample uses `Map` and `RunSignalR` instead of `MapSignalR`, so that the CORS middleware runs only for the SignalR requests that require CORS support (rather than for all traffic at the path specified in `MapSignalR`.) Map can also be used for any other middleware that needs to run for a specific URL prefix, rather than for the entire application.</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample11.cs)]

> [!NOTE]
>
> - <span data-ttu-id="effd0-235">Legen Sie nicht `jQuery.support.cors` auf "true" in Ihrem Code.</span><span class="sxs-lookup"><span data-stu-id="effd0-235">Don't set `jQuery.support.cors` to true in your code.</span></span>
>
>     ![Legen Sie jQuery.support.cors nicht auf "true"](hubs-api-guide-javascript-client/_static/image7.png)
>
>     <span data-ttu-id="effd0-237">SignalR behandelt die Verwendung von CORS.</span><span class="sxs-lookup"><span data-stu-id="effd0-237">SignalR handles the use of CORS.</span></span> <span data-ttu-id="effd0-238">Festlegen von `jQuery.support.cors` auf "true" JSONP deaktiviert, da SignalR davon aus, der Browser das CORS unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="effd0-238">Setting `jQuery.support.cors` to true disables JSONP because it causes SignalR to assume the browser supports CORS.</span></span>
> - <span data-ttu-id="effd0-239">Wenn Sie eine Verbindung zu einem Localhost-URL herstellen, wird nicht Internet Explorer 10 eine Verbindung domänenübergreifende beachtet werden, damit die Anwendung lokal mit Internet Explorer 10 funktionieren, auch wenn Sie die domänenübergreifende Verbindungen auf dem Server aktiviert haben.</span><span class="sxs-lookup"><span data-stu-id="effd0-239">When you're connecting to a localhost URL, Internet Explorer 10 won't consider it a cross-domain connection, so the application will work locally with IE 10 even if you haven't enabled cross-domain connections on the server.</span></span>
> - <span data-ttu-id="effd0-240">Weitere Informationen zur Verwendung von domänenübergreifende Verbindungen mit Internet Explorer 9, finden Sie unter [dieser Stack Overflow-Thread](http://stackoverflow.com/questions/13573397/siganlr-ie9-cross-domain-request-dont-work).</span><span class="sxs-lookup"><span data-stu-id="effd0-240">For information about using cross-domain connections with Internet Explorer 9, see [this StackOverflow thread](http://stackoverflow.com/questions/13573397/siganlr-ie9-cross-domain-request-dont-work).</span></span>
> - <span data-ttu-id="effd0-241">Weitere Informationen zur Verwendung von domänenübergreifende Verbindungen mit Chrome finden Sie unter [dieser Stack Overflow-Thread](http://stackoverflow.com/questions/15467373/signalr-1-0-1-cross-domain-request-cors-with-chrome).</span><span class="sxs-lookup"><span data-stu-id="effd0-241">For information about using cross-domain connections with Chrome, see [this StackOverflow thread](http://stackoverflow.com/questions/15467373/signalr-1-0-1-cross-domain-request-cors-with-chrome).</span></span>
> - <span data-ttu-id="effd0-242">Der Beispielcode verwendet die Standardeinstellung "/ Signalr" die URL für die Verbindung mit Ihrem SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="effd0-242">The sample code uses the default "/signalr" URL to connect to your SignalR service.</span></span> <span data-ttu-id="effd0-243">WPF-Clientcode für die Methode wird vom Server ohne Parameter aufgerufen. [ASP.NET SignalR Hubs API Guide - Server - The /signalr URL](hubs-api-guide-server.md#signalrurl).</span><span class="sxs-lookup"><span data-stu-id="effd0-243">For information about how to specify a different base URL, see [ASP.NET SignalR Hubs API Guide - Server - The /signalr URL](hubs-api-guide-server.md#signalrurl).</span></span>


<a id="configureconnection"></a>

## <a name="how-to-configure-the-connection"></a><span data-ttu-id="effd0-244">Gewusst wie: Konfigurieren der Verbindung</span><span class="sxs-lookup"><span data-stu-id="effd0-244">How to configure the connection</span></span>

<span data-ttu-id="effd0-245">Bevor Sie eine Verbindung herstellen, können Sie Abfragezeichenfolgen-Parameter angeben oder geben Sie die Transportmethode.</span><span class="sxs-lookup"><span data-stu-id="effd0-245">Before you establish a connection, you can specify query string parameters or specify the transport method.</span></span>

<a id="querystring"></a>

### <a name="how-to-specify-query-string-parameters"></a><span data-ttu-id="effd0-246">Gewusst wie: Angeben von Abfragezeichenfolgen-Parameter</span><span class="sxs-lookup"><span data-stu-id="effd0-246">How to specify query string parameters</span></span>

<span data-ttu-id="effd0-247">Wenn Sie möchten die Daten an den Server gesendet, wenn der Client eine Verbindung herstellt, können Sie Abfragezeichenfolgen-Parameter an das Verbindungsobjekt hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="effd0-247">If you want to send data to the server when the client connects, you can add query string parameters to the connection object.</span></span> <span data-ttu-id="effd0-248">Die folgenden Beispiele zeigen, wie Sie einen Abfragezeichenfolgen-Parameter in Client-Code festlegen.</span><span class="sxs-lookup"><span data-stu-id="effd0-248">The following examples show how to set a query string parameter in client code.</span></span>

<span data-ttu-id="effd0-249">**Legen Sie einen Zeichenfolgenwert für die Abfrage vor dem Aufrufen der Start-Methode (mit den generierten Proxy)**</span><span class="sxs-lookup"><span data-stu-id="effd0-249">**Set a query string value before calling the start method (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample12.js?highlight=1)]

<span data-ttu-id="effd0-250">**Legen Sie einen Zeichenfolgenwert für die Abfrage vor dem Aufrufen der Start-Methode (ohne den generierten Proxy)**</span><span class="sxs-lookup"><span data-stu-id="effd0-250">**Set a query string value before calling the start method (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample13.js?highlight=2)]

<span data-ttu-id="effd0-251">Das folgende Beispiel zeigt, wie Sie einen Abfragezeichenfolgen-Parameter im Servercode zu lesen.</span><span class="sxs-lookup"><span data-stu-id="effd0-251">The following example shows how to read a query string parameter in server code.</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample14.cs?highlight=5)]

<a id="transport"></a>

### <a name="how-to-specify-the-transport-method"></a><span data-ttu-id="effd0-252">Das Angeben der Transportmethode</span><span class="sxs-lookup"><span data-stu-id="effd0-252">How to specify the transport method</span></span>

<span data-ttu-id="effd0-253">Als Teil der Prozess der verbindungsherstellung verhandelt ein SignalR-Client normalerweise mit dem Server um den besten Transport zu bestimmen, der unterstützt wird, indem sowohl Server-als auch.</span><span class="sxs-lookup"><span data-stu-id="effd0-253">As part of the process of connecting, a SignalR client normally negotiates with the server to determine the best transport that is supported by both server and client.</span></span> <span data-ttu-id="effd0-254">Wenn Sie bereits wissen, dass Transport Sie verwenden möchten, können Sie dieser Aushandlungsprozess umgehen, indem Sie die Transportmethode angeben, beim Aufrufen der `start` Methode.</span><span class="sxs-lookup"><span data-stu-id="effd0-254">If you already know which transport you want to use, you can bypass this negotiation process by specifying the transport method when you call the `start` method.</span></span>

<span data-ttu-id="effd0-255">**Clientcode, der angibt, die Transportmethode (mit den generierten Proxy)**</span><span class="sxs-lookup"><span data-stu-id="effd0-255">**Client code that specifies the transport method (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample15.js?highlight=1)]

<span data-ttu-id="effd0-256">**Clientcode, der angibt, die Transportmethode (ohne den generierten Proxy)**</span><span class="sxs-lookup"><span data-stu-id="effd0-256">**Client code that specifies the transport method (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample16.js?highlight=2)]

<span data-ttu-id="effd0-257">Als Alternative können Sie mehrere Transportmethoden in der Reihenfolge angeben, in denen Sie SignalR, wenn sie Sie testen möchten:</span><span class="sxs-lookup"><span data-stu-id="effd0-257">As an alternative, you can specify multiple transport methods in the order in which you want SignalR to try them:</span></span>

<span data-ttu-id="effd0-258">**Clientcode, der angibt, ein benutzerdefinierter Transport-fallback-Schema (mit den generierten Proxy)**</span><span class="sxs-lookup"><span data-stu-id="effd0-258">**Client code that specifies a custom transport fallback scheme (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample17.js?highlight=1)]

<span data-ttu-id="effd0-259">**Clientcode, der angibt, ein benutzerdefinierter Transport-fallback-Schema (ohne den generierten Proxy)**</span><span class="sxs-lookup"><span data-stu-id="effd0-259">**Client code that specifies a custom transport fallback scheme (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample18.js?highlight=2)]

<span data-ttu-id="effd0-260">Sie können die folgenden Werte verwenden, für die Angabe der Transportmethode die:</span><span class="sxs-lookup"><span data-stu-id="effd0-260">You can use the following values for specifying the transport method:</span></span>

- <span data-ttu-id="effd0-261">"webSockets"</span><span class="sxs-lookup"><span data-stu-id="effd0-261">"webSockets"</span></span>
- <span data-ttu-id="effd0-262">"foreverFrame"</span><span class="sxs-lookup"><span data-stu-id="effd0-262">"foreverFrame"</span></span>
- <span data-ttu-id="effd0-263">"serverSentEvents"</span><span class="sxs-lookup"><span data-stu-id="effd0-263">"serverSentEvents"</span></span>
- <span data-ttu-id="effd0-264">"LongPolling"</span><span class="sxs-lookup"><span data-stu-id="effd0-264">"longPolling"</span></span>

<span data-ttu-id="effd0-265">Die folgenden Beispiele zeigen, wie Sie herausfinden, welcher Transportmethode von einer Verbindung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="effd0-265">The following examples show how to find out which transport method is being used by a connection.</span></span>

<span data-ttu-id="effd0-266">**Clientcode, der die Transportmethode ein, die eine Verbindung (mit den generierten Proxy) wird angezeigt.**</span><span class="sxs-lookup"><span data-stu-id="effd0-266">**Client code that displays the transport method used by a connection (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample19.js?highlight=2)]

<span data-ttu-id="effd0-267">**Clientcode, der die Transportmethode, die von einer Verbindung (ohne den generierten Proxy) verwendete zeigt**</span><span class="sxs-lookup"><span data-stu-id="effd0-267">**Client code that displays the transport method used by a connection (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample20.js?highlight=3)]

<span data-ttu-id="effd0-268">Informationen zum Überprüfen der Transportmethode in Server-Code finden Sie unter [für die ASP.NET SignalR-Hubs-API - Server – zum Abrufen von Informationen über den Client aus der Kontexteigenschaft](hubs-api-guide-server.md#contextproperty).</span><span class="sxs-lookup"><span data-stu-id="effd0-268">For information about how to check the transport method in server code, see [ASP.NET SignalR Hubs API Guide - Server - How to get information about the client from the Context property](hubs-api-guide-server.md#contextproperty).</span></span> <span data-ttu-id="effd0-269">Weitere Informationen zu Transporten und Fallbacks, finden Sie unter [Einführung zu SignalR - Transporte und Fallbacks](../getting-started/introduction-to-signalr.md#transports).</span><span class="sxs-lookup"><span data-stu-id="effd0-269">For more information about transports and fallbacks, see [Introduction to SignalR - Transports and Fallbacks](../getting-started/introduction-to-signalr.md#transports).</span></span>

<a id="getproxy"></a>

## <a name="how-to-get-a-proxy-for-a-hub-class"></a><span data-ttu-id="effd0-270">Wie Sie einen Proxy für einen Hub-Klasse abrufen</span><span class="sxs-lookup"><span data-stu-id="effd0-270">How to get a proxy for a Hub class</span></span>

<span data-ttu-id="effd0-271">Jede Verbindungsobjekt, das Sie erstellen kapselt Informationen über eine Verbindung mit einem SignalR-Dienst, der eine oder mehrere Klassen von Hub enthält.</span><span class="sxs-lookup"><span data-stu-id="effd0-271">Each connection object that you create encapsulates information about a connection to a SignalR service that contains one or more Hub classes.</span></span> <span data-ttu-id="effd0-272">Mit einer hubklasse kommunizieren kann, verwenden Sie ein Proxyobjekt, das Sie erstellen selbst (sofern Sie nicht über den generierten Proxy verwenden) oder die für Sie generiert wird.</span><span class="sxs-lookup"><span data-stu-id="effd0-272">To communicate with a Hub class, you use a proxy object which you create yourself (if you're not using the generated proxy) or which is generated for you.</span></span>

<span data-ttu-id="effd0-273">Auf dem Client ist der Proxyname einer Version in Kamel-Schreibweise des Klassennamens Hub.</span><span class="sxs-lookup"><span data-stu-id="effd0-273">On the client the proxy name is a camel-cased version of the Hub class name.</span></span> <span data-ttu-id="effd0-274">SignalR erstellt automatisch diese Änderung, damit JavaScript-Code JavaScript-Konventionen entsprechen kann.</span><span class="sxs-lookup"><span data-stu-id="effd0-274">SignalR automatically makes this change so that JavaScript code can conform to JavaScript conventions.</span></span>

<span data-ttu-id="effd0-275">**Hub-Klasse auf server**</span><span class="sxs-lookup"><span data-stu-id="effd0-275">**Hub class on server**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample21.cs?highlight=1)]

<span data-ttu-id="effd0-276">**Abrufen eines Verweises auf den generierten Client-Proxy für den Hub**</span><span class="sxs-lookup"><span data-stu-id="effd0-276">**Get a reference to the generated client proxy for the Hub**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample22.js?highlight=1)]

<span data-ttu-id="effd0-277">**Erstellen von Clientproxy für die hubklasse (ohne generierten Proxy)**</span><span class="sxs-lookup"><span data-stu-id="effd0-277">**Create client proxy for the Hub class (without generated proxy)**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample23.cs?highlight=1)]

<span data-ttu-id="effd0-278">Wenn Sie ergänzen, um Ihre hubklasse mit einem `HubName` -Attributs festzulegen, verwenden Sie den genauen Namen ohne die Groß-/Kleinschreibung ändern.</span><span class="sxs-lookup"><span data-stu-id="effd0-278">If you decorate your Hub class with a `HubName` attribute, use the exact name without changing case.</span></span>

<span data-ttu-id="effd0-279">**Hub-Klasse, auf dem Server mit HubName-Attribut**</span><span class="sxs-lookup"><span data-stu-id="effd0-279">**Hub class on server with HubName attribute**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample24.cs?highlight=1)]

<span data-ttu-id="effd0-280">**Abrufen eines Verweises auf den generierten Client-Proxy für den Hub**</span><span class="sxs-lookup"><span data-stu-id="effd0-280">**Get a reference to the generated client proxy for the Hub**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample25.js?highlight=1)]

<span data-ttu-id="effd0-281">**Erstellen von Clientproxy für die hubklasse (ohne generierten Proxy)**</span><span class="sxs-lookup"><span data-stu-id="effd0-281">**Create client proxy for the Hub class (without generated proxy)**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample26.cs?highlight=1)]

<a id="callclient"></a>

## <a name="how-to-define-methods-on-the-client-that-the-server-can-call"></a><span data-ttu-id="effd0-282">Wie Sie Methoden auf dem Client zu definieren, die der Server aufgerufen werden können</span><span class="sxs-lookup"><span data-stu-id="effd0-282">How to define methods on the client that the server can call</span></span>

<span data-ttu-id="effd0-283">Um eine Methode definieren, die der Server über einen Hub aufrufen können, fügen Sie einen Ereignishandler auf die Hubproxy-Klasse unter Verwendung der `client` Eigenschaft des generierten Proxys oder der Aufruf der `on` Methode, wenn Sie den generierten Proxy nicht verwenden.</span><span class="sxs-lookup"><span data-stu-id="effd0-283">To define a method that the server can call from a Hub, add an event handler to the Hub proxy by using the `client` property of the generated proxy, or call the `on` method if you aren't using the generated proxy.</span></span> <span data-ttu-id="effd0-284">Die Parameter können komplexe Objekte sein.</span><span class="sxs-lookup"><span data-stu-id="effd0-284">The parameters can be complex objects.</span></span>

<span data-ttu-id="effd0-285">Fügen Sie den Ereignishandler vor dem Aufruf der `start` Methode zum Herstellen der Verbindung.</span><span class="sxs-lookup"><span data-stu-id="effd0-285">Add the event handler before you call the `start` method to establish the connection.</span></span> <span data-ttu-id="effd0-286">(Wenn Sie nach dem Aufruf Ereignishandler hinzufügen möchten die `start` -Methode finden Sie unter den Hinweis im [Gewusst wie: Herstellen einer Verbindung](#establishconnection) weiter oben in diesem Dokument, und verwenden Sie die Syntax für die Definition einer Methode ohne Verwendung des generierten Proxys angezeigt.)</span><span class="sxs-lookup"><span data-stu-id="effd0-286">(If you want to add event handlers after calling the `start` method, see the note in [How to establish a connection](#establishconnection) earlier in this document, and use the syntax shown for defining a method without using the generated proxy.)</span></span>

<span data-ttu-id="effd0-287">Methode-namenszuordnung wird Groß-/Kleinschreibung.</span><span class="sxs-lookup"><span data-stu-id="effd0-287">Method name matching is case-insensitive.</span></span> <span data-ttu-id="effd0-288">Z. B. `Clients.All.addContosoChatMessageToPage` auf dem Server führt `AddContosoChatMessageToPage`, `addContosoChatMessageToPage`, oder `addcontosochatmessagetopage` auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="effd0-288">For example, `Clients.All.addContosoChatMessageToPage` on the server will execute `AddContosoChatMessageToPage`, `addContosoChatMessageToPage`, or `addcontosochatmessagetopage` on the client.</span></span>

<span data-ttu-id="effd0-289">**Definieren Sie die Methode auf dem Client (mit den generierten Proxy)**</span><span class="sxs-lookup"><span data-stu-id="effd0-289">**Define method on client (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample27.js?highlight=2)]

<span data-ttu-id="effd0-290">**Alternative Möglichkeit zum Definieren einer Methode auf dem Client (mit den generierten Proxy)**</span><span class="sxs-lookup"><span data-stu-id="effd0-290">**Alternate way to define method on client (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample28.js?highlight=1-2)]

<span data-ttu-id="effd0-291">**Definieren Sie die Methode auf dem Client (ohne den generierten Proxy, oder wenn Sie nach dem Aufrufen der Start-Methode hinzufügen)**</span><span class="sxs-lookup"><span data-stu-id="effd0-291">**Define method on client (without the generated proxy, or when adding after calling the start method)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample29.js?highlight=3)]

<span data-ttu-id="effd0-292">**Server-Code, der die Methode aufruft.**</span><span class="sxs-lookup"><span data-stu-id="effd0-292">**Server code that calls the client method**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample30.cs?highlight=5)]

<span data-ttu-id="effd0-293">Die folgenden Beispiele enthalten ein komplexes Objekt als Methodenparameter angegeben.</span><span class="sxs-lookup"><span data-stu-id="effd0-293">The following examples include a complex object as a method parameter.</span></span>

<span data-ttu-id="effd0-294">**Definieren Sie die Methode auf Client, der ein komplexes Objekt (mit den generierten Proxy) akzeptiert.**</span><span class="sxs-lookup"><span data-stu-id="effd0-294">**Define method on client that takes a complex object (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample31.js?highlight=2-3)]

<span data-ttu-id="effd0-295">**Definieren Sie die Methode auf Client, der ein komplexes Objekt (ohne den generierten Proxy) akzeptiert.**</span><span class="sxs-lookup"><span data-stu-id="effd0-295">**Define method on client that takes a complex object (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample32.js?highlight=3-4)]

<span data-ttu-id="effd0-296">**Server-Code, die das komplexe Objekt definiert.**</span><span class="sxs-lookup"><span data-stu-id="effd0-296">**Server code that defines the complex object**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample33.cs?highlight=1)]

<span data-ttu-id="effd0-297">**Servercode, der die Clientmethode, die mit der ein komplexes Objekt aufruft**</span><span class="sxs-lookup"><span data-stu-id="effd0-297">**Server code that calls the client method using a complex object**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample34.cs?highlight=3)]

<a id="callserver"></a>

## <a name="how-to-call-server-methods-from-the-client"></a><span data-ttu-id="effd0-298">Gewusst wie: Servermethoden vom Client aufrufen.</span><span class="sxs-lookup"><span data-stu-id="effd0-298">How to call server methods from the client</span></span>

<span data-ttu-id="effd0-299">Um eine Servermethode auf dem Client aufzurufen, verwenden die `server` Eigenschaft des generierten Proxys oder `invoke` Methode für die Hubproxy-Klasse, wenn Sie den generierten Proxy nicht verwenden.</span><span class="sxs-lookup"><span data-stu-id="effd0-299">To call a server method from the client, use the `server` property of the generated proxy or the `invoke` method on the Hub proxy if you aren't using the generated proxy.</span></span> <span data-ttu-id="effd0-300">Der Rückgabewert oder Parameter können komplexe Objekte sein.</span><span class="sxs-lookup"><span data-stu-id="effd0-300">The return value or parameters can be complex objects.</span></span>

<span data-ttu-id="effd0-301">Eine Camel-Case-Version des Methodennamens für den Hub übergeben.</span><span class="sxs-lookup"><span data-stu-id="effd0-301">Pass in a camel-case version of the method name on the Hub.</span></span> <span data-ttu-id="effd0-302">SignalR erstellt automatisch diese Änderung, damit JavaScript-Code JavaScript-Konventionen entsprechen kann.</span><span class="sxs-lookup"><span data-stu-id="effd0-302">SignalR automatically makes this change so that JavaScript code can conform to JavaScript conventions.</span></span>

<span data-ttu-id="effd0-303">Die folgenden Beispiele zeigen, wie Sie eine Servermethode aufrufen, die nicht über einen Rückgabewert verfügt und wie Sie eine Servermethode aufrufen, die einen Rückgabewert verfügt.</span><span class="sxs-lookup"><span data-stu-id="effd0-303">The following examples show how to call a server method that doesn't have a return value and how to call a server method that does have a return value.</span></span>

<span data-ttu-id="effd0-304">**Server-Methode ohne HubMethodName-Attribut**</span><span class="sxs-lookup"><span data-stu-id="effd0-304">**Server method with no HubMethodName attribute**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample35.cs?highlight=3)]

<span data-ttu-id="effd0-305">**Servercode, der definiert, das komplexe Objekt an den Parameter übergeben**</span><span class="sxs-lookup"><span data-stu-id="effd0-305">**Server code that defines the complex object passed in a parameter**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample36.cs)]

<span data-ttu-id="effd0-306">**Clientcode, der die Server-Methode (mit den generierten Proxy) aufruft.**</span><span class="sxs-lookup"><span data-stu-id="effd0-306">**Client code that invokes the server method (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample37.js?highlight=1)]

<span data-ttu-id="effd0-307">**Clientcode, der die Server-Methode (ohne den generierten Proxy) aufruft.**</span><span class="sxs-lookup"><span data-stu-id="effd0-307">**Client code that invokes the server method (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample38.js?highlight=1)]

<span data-ttu-id="effd0-308">Wenn Sie die Hub-Methode mit ergänzt einen `HubMethodName` -Attributs festzulegen, verwenden Sie diesen Namen ohne die Groß-/Kleinschreibung ändern.</span><span class="sxs-lookup"><span data-stu-id="effd0-308">If you decorated the Hub method with a `HubMethodName` attribute, use that name without changing case.</span></span>

<span data-ttu-id="effd0-309">**Server-Methode** mit einem HubMethodName-Attribut</span><span class="sxs-lookup"><span data-stu-id="effd0-309">**Server method** with a HubMethodName attribute</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample39.cs?highlight=3)]

<span data-ttu-id="effd0-310">**Clientcode, der die Server-Methode (mit den generierten Proxy) aufruft.**</span><span class="sxs-lookup"><span data-stu-id="effd0-310">**Client code that invokes the server method (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample40.js?highlight=1)]

<span data-ttu-id="effd0-311">**Clientcode, der die Server-Methode (ohne den generierten Proxy) aufruft.**</span><span class="sxs-lookup"><span data-stu-id="effd0-311">**Client code that invokes the server method (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample41.js?highlight=1)]

<span data-ttu-id="effd0-312">Im vorherigen Beispiel veranschaulicht eine Servermethode aufrufen, die keinen zurückgibt Wert.</span><span class="sxs-lookup"><span data-stu-id="effd0-312">The preceding examples show how to call a server method that has no return value.</span></span> <span data-ttu-id="effd0-313">Die folgenden Beispiele zeigen, wie Sie eine Servermethode aufrufen, die über einen Rückgabewert verfügt.</span><span class="sxs-lookup"><span data-stu-id="effd0-313">The following examples show how to call a server method that has a return value.</span></span>

<span data-ttu-id="effd0-314">**Server-Code für eine Methode, die über einen Rückgabewert verfügt.**</span><span class="sxs-lookup"><span data-stu-id="effd0-314">**Server code for a method that has a return value**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample42.cs?highlight=3)]

<span data-ttu-id="effd0-315">**Die Stock-Klasse für den** Rückgabewert</span><span class="sxs-lookup"><span data-stu-id="effd0-315">**The Stock class used for the** return value</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample43.cs?highlight=1)]

<span data-ttu-id="effd0-316">**Clientcode, der die Server-Methode (mit den generierten Proxy) aufruft.**</span><span class="sxs-lookup"><span data-stu-id="effd0-316">**Client code that invokes the server method (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample44.js?highlight=2,4-5)]

<span data-ttu-id="effd0-317">**Clientcode, der die Server-Methode (ohne den generierten Proxy) aufruft.**</span><span class="sxs-lookup"><span data-stu-id="effd0-317">**Client code that invokes the server method (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample45.js?highlight=2,4-5)]

<a id="connectionlifetime"></a>

## <a name="how-to-handle-connection-lifetime-events"></a><span data-ttu-id="effd0-318">Gewusst wie: Behandeln der Objektlebensdauer-Ereignisse der Verbindung</span><span class="sxs-lookup"><span data-stu-id="effd0-318">How to handle connection lifetime events</span></span>

<span data-ttu-id="effd0-319">SignalR bietet die folgenden Verbindung, die Objektlebensdauer-Ereignisse, die Sie behandeln können:</span><span class="sxs-lookup"><span data-stu-id="effd0-319">SignalR provides the following connection lifetime events that you can handle:</span></span>

- <span data-ttu-id="effd0-320">`starting`: Wird ausgelöst, bevor alle Daten über die Verbindung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="effd0-320">`starting`: Raised before any data is sent over the connection.</span></span>
- <span data-ttu-id="effd0-321">`received`: Wird ausgelöst, wenn alle Daten für die Verbindung empfangen werden.</span><span class="sxs-lookup"><span data-stu-id="effd0-321">`received`: Raised when any data is received on the connection.</span></span> <span data-ttu-id="effd0-322">Stellt die empfangenen Daten bereit.</span><span class="sxs-lookup"><span data-stu-id="effd0-322">Provides the received data.</span></span>
- <span data-ttu-id="effd0-323">`connectionSlow`: Wird ausgelöst, wenn der Client eine Verbindung langsame ist oder häufig löschen erkennt.</span><span class="sxs-lookup"><span data-stu-id="effd0-323">`connectionSlow`: Raised when the client detects a slow or frequently dropping connection.</span></span>
- <span data-ttu-id="effd0-324">`reconnecting`: Wird ausgelöst, wenn des zugrunde liegenden Transports, erneut eine Verbindung herzustellen beginnt.</span><span class="sxs-lookup"><span data-stu-id="effd0-324">`reconnecting`: Raised when the underlying transport begins reconnecting.</span></span>
- <span data-ttu-id="effd0-325">`reconnected`: Wird ausgelöst, wenn der zugrunde liegenden Transport die Verbindung wiederhergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="effd0-325">`reconnected`: Raised when the underlying transport has reconnected.</span></span>
- <span data-ttu-id="effd0-326">`stateChanged`: Wird ausgelöst, wenn der Verbindungsstatus ändert.</span><span class="sxs-lookup"><span data-stu-id="effd0-326">`stateChanged`: Raised when the connection state changes.</span></span> <span data-ttu-id="effd0-327">Enthält den alten Status und den neuen Zustand (Herstellen einer Verbindung, verbunden, Verbindung oder Disconnected).</span><span class="sxs-lookup"><span data-stu-id="effd0-327">Provides the old state and the new state (Connecting, Connected, Reconnecting, or Disconnected).</span></span>
- <span data-ttu-id="effd0-328">`disconnected`: Wird ausgelöst, wenn die Verbindung getrennt wurde.</span><span class="sxs-lookup"><span data-stu-id="effd0-328">`disconnected`: Raised when the connection has disconnected.</span></span>

<span data-ttu-id="effd0-329">Beispielsweise sollten Sie Warnmeldungen angezeigt, wenn Probleme mit der Verbindung, die nennenswerte Verzögerungen verursachen können, behandelt der `connectionSlow` Ereignis.</span><span class="sxs-lookup"><span data-stu-id="effd0-329">For example, if you want to display warning messages when there are connection problems that might cause noticeable delays, handle the `connectionSlow` event.</span></span>

<span data-ttu-id="effd0-330">**Behandeln Sie das ConnectionSlow-Ereignis (mit den generierten Proxy)**</span><span class="sxs-lookup"><span data-stu-id="effd0-330">**Handle the connectionSlow event (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample46.js?highlight=1)]

<span data-ttu-id="effd0-331">**Behandeln des Ereignisses ConnectionSlow (ohne den generierten Proxy)**</span><span class="sxs-lookup"><span data-stu-id="effd0-331">**Handle the connectionSlow event (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample47.js?highlight=2)]

<span data-ttu-id="effd0-332">Weitere Informationen finden Sie unter [verstehen und Behandeln von Ereignissen für Lebensdauer in SignalR](handling-connection-lifetime-events.md).</span><span class="sxs-lookup"><span data-stu-id="effd0-332">For more information, see [Understanding and Handling Connection Lifetime Events in SignalR](handling-connection-lifetime-events.md).</span></span>

<a id="handleerrors"></a>

## <a name="how-to-handle-errors"></a><span data-ttu-id="effd0-333">Gewusst wie: Behandeln von Fehlern</span><span class="sxs-lookup"><span data-stu-id="effd0-333">How to handle errors</span></span>

<span data-ttu-id="effd0-334">Der SignalR-JavaScript-Client enthält eine `error` -Ereignis, das Sie einen Handler für hinzufügen können.</span><span class="sxs-lookup"><span data-stu-id="effd0-334">The SignalR JavaScript client provides an `error` event that you can add a handler for.</span></span> <span data-ttu-id="effd0-335">Sie können auch die Fail-Methode verwenden, um einen Handler für Fehler hinzuzufügen, die aus einem Methodenaufruf für den Server.</span><span class="sxs-lookup"><span data-stu-id="effd0-335">You can also use the fail method to add a handler for errors that result from a server method invocation.</span></span>

<span data-ttu-id="effd0-336">Wenn Sie detaillierte Fehlermeldungen auf dem Server nicht explizit aktivieren, enthält das Ausnahmeobjekt an, dem SignalR nach einem Fehler zurückgibt minimale Informationen über den Fehler an.</span><span class="sxs-lookup"><span data-stu-id="effd0-336">If you don't explicitly enable detailed error messages on the server, the exception object that SignalR returns after an error contains minimal information about the error.</span></span> <span data-ttu-id="effd0-337">Beispielsweise wenn ein Aufruf von `newContosoChatMessage` ein Fehler auftritt, der die Fehlermeldung in das Fehlerobjekt enthält "`There was an error invoking Hub method 'contosoChatHub.newContosoChatMessage'.`" Senden detaillierte Fehlermeldungen für Clients in einer produktionsumgebung wird nicht empfohlen aus Sicherheitsgründen aber wenn Sie möchten detaillierte Fehlermeldungen für aktivieren Problembehandlung, verwenden Sie den folgenden Code auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="effd0-337">For example, if a call to `newContosoChatMessage` fails, the error message in the error object contains "`There was an error invoking Hub method 'contosoChatHub.newContosoChatMessage'.`" Sending detailed error messages to clients in production is not recommended for security reasons, but if you want to enable detailed error messages for troubleshooting purposes, use the following code on the server.</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample48.cs?highlight=2)]

<span data-ttu-id="effd0-338">Das folgende Beispiel zeigt, wie Sie einen Ereignishandler für das Fehlerereignis hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="effd0-338">The following example shows how to add a handler for the error event.</span></span>

<span data-ttu-id="effd0-339">**Fügen Sie einen Fehlerhandler (mit den generierten Proxy)**</span><span class="sxs-lookup"><span data-stu-id="effd0-339">**Add an error handler (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample49.js?highlight=1)]

<span data-ttu-id="effd0-340">**Hinzufügen einer Fehlerbehandlungsroutine (ohne den generierten Proxy)**</span><span class="sxs-lookup"><span data-stu-id="effd0-340">**Add an error handler (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample50.js?highlight=2)]

<span data-ttu-id="effd0-341">Das folgende Beispiel zeigt, wie Sie einen Fehler aus einem Methodenaufruf zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="effd0-341">The following example shows how to handle an error from a method invocation.</span></span>

<span data-ttu-id="effd0-342">**Behandeln Sie Fehler über den Aufruf einer Methode (mit den generierten Proxy)**</span><span class="sxs-lookup"><span data-stu-id="effd0-342">**Handle an error from a method invocation (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample51.js?highlight=2)]

<span data-ttu-id="effd0-343">**Behandeln Sie einen Fehler eines Methodenaufrufs (ohne den generierten Proxy)**</span><span class="sxs-lookup"><span data-stu-id="effd0-343">**Handle an error from a method invocation (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample52.js?highlight=2)]

<span data-ttu-id="effd0-344">Wenn der Aufruf einer Methode ein Fehler auftritt, die `error` Ereignis wird auch ausgelöst, sodass Ihren Code in die `error` Methodenhandler und klicken Sie in der `.fail` Methode-Rückruf ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="effd0-344">If a method invocation fails, the `error` event is also raised, so your code in the `error` method handler and in the `.fail` method callback would execute.</span></span>

<a id="logging"></a>

## <a name="how-to-enable-client-side-logging"></a><span data-ttu-id="effd0-345">Gewusst wie: Aktivieren der clientseitigen Protokollierung</span><span class="sxs-lookup"><span data-stu-id="effd0-345">How to enable client-side logging</span></span>

<span data-ttu-id="effd0-346">Legen Sie zum Aktivieren der clientseitigen Protokollierung für eine Verbindung der `logging` Eigenschaft für das Verbindungsobjekt vor dem Aufruf der `start` Methode zum Herstellen der Verbindung.</span><span class="sxs-lookup"><span data-stu-id="effd0-346">To enable client-side logging on a connection, set the `logging` property on the connection object before you call the `start` method to establish the connection.</span></span>

<span data-ttu-id="effd0-347">**Aktivieren der Protokollierung (mit den generierten Proxy)**</span><span class="sxs-lookup"><span data-stu-id="effd0-347">**Enable logging (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample53.js?highlight=1)]

<span data-ttu-id="effd0-348">**Aktivieren der Protokollierung (ohne den generierten Proxy)**</span><span class="sxs-lookup"><span data-stu-id="effd0-348">**Enable logging (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample54.js?highlight=2)]

<span data-ttu-id="effd0-349">Um die Protokolle anzuzeigen, öffnen Sie Entwicklertools Ihres Browsers, und wechseln Sie zur Registerkarte "Konsole" aus. Ein Tutorial mit schrittweise Anweisungen und Bildschirm Bildschirmfotos, die zeigen, wie Sie möchten, können, finden Sie [Serverübertragung mit ASP.NET Signalr - Aktivieren der Protokollierung](../getting-started/tutorial-server-broadcast-with-signalr.md#enable-logging).</span><span class="sxs-lookup"><span data-stu-id="effd0-349">To see the logs, open your browser's developer tools and go to the Console tab. For a tutorial that shows step-by-step instructions and screen shots that show how to do this, see [Server Broadcast with ASP.NET Signalr - Enable Logging](../getting-started/tutorial-server-broadcast-with-signalr.md#enable-logging).</span></span>

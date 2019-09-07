---
title: ASP.net Core blazor-Hostingmodelle
author: guardrex
description: Grundlegendes zu Client seitigen und serverseitigen edzor-Hostingmodellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: blazor/hosting-models
ms.openlocfilehash: f7a16d64e1f874a4f6b3c8db5217810b13c7c6ff
ms.sourcegitcommit: 43c6335b5859282f64d66a7696c5935a2bcdf966
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2019
ms.locfileid: "70800426"
---
# <a name="aspnet-core-blazor-hosting-models"></a><span data-ttu-id="4d41c-103">ASP.net Core blazor-Hostingmodelle</span><span class="sxs-lookup"><span data-stu-id="4d41c-103">ASP.NET Core Blazor hosting models</span></span>

<span data-ttu-id="4d41c-104">Von [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="4d41c-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="4d41c-105">Blazor ist ein Webframework, das für die clientseitige Ausführung im Browser auf einer [WebAssembly](https://webassembly.org/)-basierten .NET-Laufzeit (*Blazor, clientseitig*) oder serverseitigen Ausführung in ASP.NET Core (*Blazor, serverseitig*) konzipiert ist.</span><span class="sxs-lookup"><span data-stu-id="4d41c-105">Blazor is a web framework designed to run client-side in the browser on a [WebAssembly](https://webassembly.org/)-based .NET runtime (*Blazor client-side*) or server-side in ASP.NET Core (*Blazor server-side*).</span></span> <span data-ttu-id="4d41c-106">Unabhängig vom Hostingmodell sind die APP-und Komponentenmodelle *identisch*.</span><span class="sxs-lookup"><span data-stu-id="4d41c-106">Regardless of the hosting model, the app and component models *are the same*.</span></span>

<span data-ttu-id="4d41c-107">Informationen zum Erstellen eines Projekts für die in diesem Artikel beschriebenen Hostingmodelle <xref:blazor/get-started>finden Sie unter.</span><span class="sxs-lookup"><span data-stu-id="4d41c-107">To create a project for the hosting models described in this article, see <xref:blazor/get-started>.</span></span>

## <a name="client-side"></a><span data-ttu-id="4d41c-108">Clientseitig</span><span class="sxs-lookup"><span data-stu-id="4d41c-108">Client-side</span></span>

<span data-ttu-id="4d41c-109">Das Prinzipal-Hostingmodell für blazor führt die Client seitige Ausführung im Browser in Webassembly aus.</span><span class="sxs-lookup"><span data-stu-id="4d41c-109">The principal hosting model for Blazor is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="4d41c-110">Die Blazor-App, die jeweiligen Abhängigkeiten und die .NET-Runtime werden im Browser herunterladen.</span><span class="sxs-lookup"><span data-stu-id="4d41c-110">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="4d41c-111">Die App wird direkt im UI-Thread des Browsers ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="4d41c-111">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="4d41c-112">UI-Updates und Ereignis Behandlung treten innerhalb desselben Prozesses auf.</span><span class="sxs-lookup"><span data-stu-id="4d41c-112">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="4d41c-113">Die Assets der App werden als statische Dateien auf einem Webserver oder Dienst bereitgestellt, in dem statische Inhalte für Clients bereitgestellt werden können.</span><span class="sxs-lookup"><span data-stu-id="4d41c-113">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![Client seitige blazor-Komponente: Die blazor-APP wird in einem UI-Thread im Browser ausgeführt.](hosting-models/_static/client-side.png)

<span data-ttu-id="4d41c-115">Um eine blazor-App mithilfe des Client seitigen Hostingmodells zu erstellen, verwenden Sie die Vorlage " **blazor Webassembly-App** " ([dotnet New blazorwasm](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="4d41c-115">To create a Blazor app using the client-side hosting model, use the **Blazor WebAssembly App** template ([dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)).</span></span>

<span data-ttu-id="4d41c-116">Nachdem Sie die APP-Vorlage für die **blazor-Webassembly** ausgewählt haben, können Sie die APP für die Verwendung eines ASP.net Core-Back-Ends konfigurieren, indem Sie das Kontrollkästchen **ASP.net Core gehostet** aktivieren ([dotnet New blazorwasm--Hosted](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="4d41c-116">After selecting the **Blazor WebAssembly App** template, you have the option of configuring the app to use an ASP.NET Core backend by selecting the **ASP.NET Core hosted** check box ([dotnet new blazorwasm --hosted](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="4d41c-117">Die ASP.net Core-App bedient die blazor-APP an Clients.</span><span class="sxs-lookup"><span data-stu-id="4d41c-117">The ASP.NET Core app serves the Blazor app to clients.</span></span> <span data-ttu-id="4d41c-118">Die Client seitige blazor-App kann über das Netzwerk mithilfe von Web-API-aufrufen oder [signalr](xref:signalr/introduction)mit dem Server interagieren.</span><span class="sxs-lookup"><span data-stu-id="4d41c-118">The Blazor client-side app can interact with the server over the network using web API calls or [SignalR](xref:signalr/introduction).</span></span>

<span data-ttu-id="4d41c-119">Die Vorlagen enthalten das Skript " *blazor. Webassembly. js* ", das Folgendes behandelt:</span><span class="sxs-lookup"><span data-stu-id="4d41c-119">The templates include the *blazor.webassembly.js* script that handles:</span></span>

* <span data-ttu-id="4d41c-120">Herunterladen der .NET-Laufzeit, der APP und der APP-Abhängigkeiten.</span><span class="sxs-lookup"><span data-stu-id="4d41c-120">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="4d41c-121">Initialisierung der Laufzeit zum Ausführen der app.</span><span class="sxs-lookup"><span data-stu-id="4d41c-121">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="4d41c-122">Das Client seitige Hostingmodell bietet mehrere Vorteile:</span><span class="sxs-lookup"><span data-stu-id="4d41c-122">The client-side hosting model offers several benefits:</span></span>

* <span data-ttu-id="4d41c-123">Es gibt keine serverseitige .net-Abhängigkeit.</span><span class="sxs-lookup"><span data-stu-id="4d41c-123">There's no .NET server-side dependency.</span></span> <span data-ttu-id="4d41c-124">Die APP funktioniert nach dem Herunterladen auf den Client vollständig.</span><span class="sxs-lookup"><span data-stu-id="4d41c-124">The app is fully functioning after downloaded to the client.</span></span>
* <span data-ttu-id="4d41c-125">Client Ressourcen und-Funktionen sind vollständig ausgelastet.</span><span class="sxs-lookup"><span data-stu-id="4d41c-125">Client resources and capabilities are fully leveraged.</span></span>
* <span data-ttu-id="4d41c-126">Die Arbeit wird vom Server an den Client verlagert.</span><span class="sxs-lookup"><span data-stu-id="4d41c-126">Work is offloaded from the server to the client.</span></span>
* <span data-ttu-id="4d41c-127">Ein ASP.net Core Webserver ist nicht erforderlich, um die APP zu hosten.</span><span class="sxs-lookup"><span data-stu-id="4d41c-127">An ASP.NET Core web server isn't required to host the app.</span></span> <span data-ttu-id="4d41c-128">Server lose Bereitstellungs Szenarien sind möglich (z. b. das Bereitstellen der APP aus einem CDN).</span><span class="sxs-lookup"><span data-stu-id="4d41c-128">Serverless deployment scenarios are possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="4d41c-129">Es gibt Nachteile zum Client seitigen Hosting:</span><span class="sxs-lookup"><span data-stu-id="4d41c-129">There are downsides to client-side hosting:</span></span>

* <span data-ttu-id="4d41c-130">Die APP ist auf die Funktionen des Browsers beschränkt.</span><span class="sxs-lookup"><span data-stu-id="4d41c-130">The app is restricted to the capabilities of the browser.</span></span>
* <span data-ttu-id="4d41c-131">Kompatible Client Hardware und Software (z. b. webassemblyunterstützung) ist erforderlich.</span><span class="sxs-lookup"><span data-stu-id="4d41c-131">Capable client hardware and software (for example, WebAssembly support) is required.</span></span>
* <span data-ttu-id="4d41c-132">Die Download Größe ist größer, und die Auslastung von apps dauert länger.</span><span class="sxs-lookup"><span data-stu-id="4d41c-132">Download size is larger, and apps take longer to load.</span></span>
* <span data-ttu-id="4d41c-133">Die Unterstützung von .NET-Laufzeit und-Tools ist weniger ausgereift.</span><span class="sxs-lookup"><span data-stu-id="4d41c-133">.NET runtime and tooling support is less mature.</span></span> <span data-ttu-id="4d41c-134">Beispielsweise gibt es Einschränkungen in [.NET Standard](/dotnet/standard/net-standard) Unterstützung und Debuggen.</span><span class="sxs-lookup"><span data-stu-id="4d41c-134">For example, limitations exist in [.NET Standard](/dotnet/standard/net-standard) support and debugging.</span></span>

## <a name="server-side"></a><span data-ttu-id="4d41c-135">Serverseitig</span><span class="sxs-lookup"><span data-stu-id="4d41c-135">Server-side</span></span>

<span data-ttu-id="4d41c-136">Mit dem serverseitigen Hostingmodell wird die APP auf dem Server innerhalb einer ASP.net Core-app ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="4d41c-136">With the server-side hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="4d41c-137">Benutzeroberflächenupdates, Ereignisbehandlung und JavaScript-Aufrufe werden über eine [SignalR](xref:signalr/introduction)-Verbindung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="4d41c-137">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![Der Browser interagiert mit der APP (gehostet innerhalb einer ASP.net Core-APP) auf dem Server über eine signalr-Verbindung.](hosting-models/_static/server-side.png)

<span data-ttu-id="4d41c-139">Um eine blazor-App mit dem serverseitigen Hostingmodell zu erstellen, verwenden Sie die ASP.net Core **blazor-Server-App** -Vorlage ([dotnet New blazorserver](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="4d41c-139">To create a Blazor app using the server-side hosting model, use the ASP.NET Core **Blazor Server App** template ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="4d41c-140">Die ASP.net Core-App hostet die serverseitige APP und erstellt den signalr-Endpunkt, mit dem Clients eine Verbindung herstellen.</span><span class="sxs-lookup"><span data-stu-id="4d41c-140">The ASP.NET Core app hosts the server-side app and creates the SignalR endpoint where clients connect.</span></span>

<span data-ttu-id="4d41c-141">Die ASP.net Core App verweist auf die `Startup` Klasse der APP, die hinzugefügt werden soll:</span><span class="sxs-lookup"><span data-stu-id="4d41c-141">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="4d41c-142">Server seitige Dienste.</span><span class="sxs-lookup"><span data-stu-id="4d41c-142">Server-side services.</span></span>
* <span data-ttu-id="4d41c-143">Die APP für die Pipeline für die Anforderungs Verarbeitung.</span><span class="sxs-lookup"><span data-stu-id="4d41c-143">The app to the request handling pipeline.</span></span>

<span data-ttu-id="4d41c-144">Das Skript&dagger; " *blazor. Server. js* " stellt die Client Verbindung her.</span><span class="sxs-lookup"><span data-stu-id="4d41c-144">The *blazor.server.js* script&dagger; establishes the client connection.</span></span> <span data-ttu-id="4d41c-145">Es liegt in der Verantwortung der APP, den App-Zustand nach Bedarf beizubehalten und wiederherzustellen (z. b. im Fall einer verlorenen Netzwerkverbindung).</span><span class="sxs-lookup"><span data-stu-id="4d41c-145">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="4d41c-146">Das serverseitige Hostingmodell bietet mehrere Vorteile:</span><span class="sxs-lookup"><span data-stu-id="4d41c-146">The server-side hosting model offers several benefits:</span></span>

* <span data-ttu-id="4d41c-147">Die Download Größe ist erheblich kleiner als eine Client seitige APP, und die APP lädt viel schneller.</span><span class="sxs-lookup"><span data-stu-id="4d41c-147">Download size is significantly smaller than a client-side app, and the app loads much faster.</span></span>
* <span data-ttu-id="4d41c-148">Die App nutzt vollständige Vorteile der Serverfunktionen, einschließlich der Verwendung von .net Core-kompatiblen APIs.</span><span class="sxs-lookup"><span data-stu-id="4d41c-148">The app takes full advantage of server capabilities, including use of any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="4d41c-149">.Net Core auf dem Server wird verwendet, um die APP auszuführen, sodass vorhandene .NET-Tools, wie z. b. das Debuggen, erwartungsgemäß funktionieren.</span><span class="sxs-lookup"><span data-stu-id="4d41c-149">.NET Core on the server is used to run the app, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="4d41c-150">Thin Clients werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="4d41c-150">Thin clients are supported.</span></span> <span data-ttu-id="4d41c-151">Serverseitige apps arbeiten z. b. mit Browsern, die Webassembly nicht unterstützen, und auf Geräten mit Ressourcen Beschränkung.</span><span class="sxs-lookup"><span data-stu-id="4d41c-151">For example, server-side apps work with browsers that don't support WebAssembly and on resource-constrained devices.</span></span>
* <span data-ttu-id="4d41c-152">Die .net-C# /Codebasis der APP, einschließlich des Komponenten Codes der APP, wird nicht für Clients bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="4d41c-152">The app's .NET/C# code base, including the app's component code, isn't served to clients.</span></span>

<span data-ttu-id="4d41c-153">Es gibt Nachteile zum serverseitigen Hosting:</span><span class="sxs-lookup"><span data-stu-id="4d41c-153">There are downsides to server-side hosting:</span></span>

* <span data-ttu-id="4d41c-154">Eine höhere Latenz ist normalerweise vorhanden.</span><span class="sxs-lookup"><span data-stu-id="4d41c-154">Higher latency usually exists.</span></span> <span data-ttu-id="4d41c-155">Jede Benutzerinteraktion umfasst einen Netzwerkhop.</span><span class="sxs-lookup"><span data-stu-id="4d41c-155">Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="4d41c-156">Es gibt keine Offline Unterstützung.</span><span class="sxs-lookup"><span data-stu-id="4d41c-156">There's no offline support.</span></span> <span data-ttu-id="4d41c-157">Wenn die Client Verbindung fehlschlägt, funktioniert die APP nicht mehr.</span><span class="sxs-lookup"><span data-stu-id="4d41c-157">If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="4d41c-158">Skalierbarkeit ist eine Herausforderung für apps mit vielen Benutzern.</span><span class="sxs-lookup"><span data-stu-id="4d41c-158">Scalability is challenging for apps with many users.</span></span> <span data-ttu-id="4d41c-159">Der Server muss mehrere Clientverbindungen verwalten und den Client Status verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="4d41c-159">The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="4d41c-160">Ein ASP.net Core Server ist erforderlich, um der APP zu dienen.</span><span class="sxs-lookup"><span data-stu-id="4d41c-160">An ASP.NET Core server is required to serve the app.</span></span> <span data-ttu-id="4d41c-161">Server lose Bereitstellungs Szenarien sind nicht möglich (z. b. für die APP aus einem CDN).</span><span class="sxs-lookup"><span data-stu-id="4d41c-161">Serverless deployment scenarios aren't possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="4d41c-162">&dagger;Das Skript " *blazor. Server. js* " wird von einer eingebetteten Ressource in der ASP.net Core freigegebenen Framework bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="4d41c-162">&dagger;The *blazor.server.js* script is served from an embedded resource in the ASP.NET Core shared framework.</span></span>

### <a name="reconnection-to-the-same-server"></a><span data-ttu-id="4d41c-163">Erneute Verbindung mit demselben Server</span><span class="sxs-lookup"><span data-stu-id="4d41c-163">Reconnection to the same server</span></span>

<span data-ttu-id="4d41c-164">Serverseitige blazor-apps erfordern eine aktive signalr-Verbindung mit dem Server.</span><span class="sxs-lookup"><span data-stu-id="4d41c-164">Blazor server-side apps require an active SignalR connection to the server.</span></span> <span data-ttu-id="4d41c-165">Wenn die Verbindung unterbrochen wird, versucht die APP, erneut eine Verbindung mit dem Server herzustellen.</span><span class="sxs-lookup"><span data-stu-id="4d41c-165">If the connection is lost, the app attempts to reconnect to the server.</span></span> <span data-ttu-id="4d41c-166">Solange sich der Status des Clients weiterhin im Arbeitsspeicher befindet, wird die Client Sitzung fortgesetzt, ohne den Zustand zu verlieren.</span><span class="sxs-lookup"><span data-stu-id="4d41c-166">As long as the client's state is still in memory, the client session resumes without losing state.</span></span>
 
<span data-ttu-id="4d41c-167">Wenn der Client erkennt, dass die Verbindung unterbrochen wurde, wird dem Benutzer eine Standardbenutzer Oberfläche angezeigt, während der Client versucht, die Verbindung wiederherzustellen.</span><span class="sxs-lookup"><span data-stu-id="4d41c-167">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="4d41c-168">Wenn bei der erneuten Verbindungs Herstellung ein Fehler auftritt, wird dem Benutzer die Option zum erneuten versuchen bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="4d41c-168">If reconnection fails, the user is provided the option to retry.</span></span> <span data-ttu-id="4d41c-169">Definieren Sie zum Anpassen der Benutzeroberfläche ein Element `components-reconnect-modal` mit als `id` dessen auf der Razor Page *_Host. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="4d41c-169">To customize the UI, define an element with `components-reconnect-modal` as its `id` in the *_Host.cshtml* Razor page.</span></span> <span data-ttu-id="4d41c-170">Der Client aktualisiert dieses Element mit einer der folgenden CSS-Klassen basierend auf dem Status der Verbindung:</span><span class="sxs-lookup"><span data-stu-id="4d41c-170">The client updates this element with one of the following CSS classes based on the state of the connection:</span></span>
 
* <span data-ttu-id="4d41c-171">`components-reconnect-show`&ndash; Zeigen Sie die Benutzeroberfläche an, um anzugeben, dass die Verbindung unterbrochen wurde und der Client versucht, die Verbindung wiederherzustellen.</span><span class="sxs-lookup"><span data-stu-id="4d41c-171">`components-reconnect-show` &ndash; Show the UI to indicate the connection was lost and the client is attempting to reconnect.</span></span>
* <span data-ttu-id="4d41c-172">`components-reconnect-hide`&ndash; Der Client verfügt über eine aktive Verbindung, die die Benutzeroberfläche ausblenden.</span><span class="sxs-lookup"><span data-stu-id="4d41c-172">`components-reconnect-hide` &ndash; The client has an active connection, hide the UI.</span></span>
* <span data-ttu-id="4d41c-173">`components-reconnect-failed`&ndash; Fehler beim erneuten Herstellen der Verbindung.</span><span class="sxs-lookup"><span data-stu-id="4d41c-173">`components-reconnect-failed` &ndash; Reconnection failed.</span></span> <span data-ttu-id="4d41c-174">Um erneut eine erneute Verbindung herzustellen, `window.Blazor.reconnect()`wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="4d41c-174">To attempt reconnection again, call `window.Blazor.reconnect()`.</span></span>

### <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="4d41c-175">Zustands behaftete erneute Verbindung nach der vorab Ausführung</span><span class="sxs-lookup"><span data-stu-id="4d41c-175">Stateful reconnection after prerendering</span></span>
 
<span data-ttu-id="4d41c-176">Serverseitige blazor-apps werden standardmäßig so eingerichtet, dass Sie die Benutzeroberfläche auf dem Server vorab ausführen, bevor die Client Verbindung mit dem Server hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="4d41c-176">Blazor server-side apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="4d41c-177">Dies wird auf der Razor page " *_Host. cshtml* " eingerichtet:</span><span class="sxs-lookup"><span data-stu-id="4d41c-177">This is set up in the *_Host.cshtml* Razor page:</span></span>
 
```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>(RenderMode.ServerPrerendered))</app>
 
    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="4d41c-178">`RenderMode`konfiguriert, ob die Komponente Folgendes hat:</span><span class="sxs-lookup"><span data-stu-id="4d41c-178">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="4d41c-179">Wird in die Seite vorab übernommen.</span><span class="sxs-lookup"><span data-stu-id="4d41c-179">Is prerendered into the page.</span></span>
* <span data-ttu-id="4d41c-180">Wird als statischer HTML-Code auf der Seite gerendert, oder wenn er die erforderlichen Informationen zum Bootstrapping einer blazor-APP vom Benutzer-Agent enthält.</span><span class="sxs-lookup"><span data-stu-id="4d41c-180">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="4d41c-181">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="4d41c-181">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="4d41c-182">Rendert die Komponente in statischem HTML-Format und enthält einen Marker für eine serverseitige blazor-app.</span><span class="sxs-lookup"><span data-stu-id="4d41c-182">Renders the component into static HTML and includes a marker for a Blazor server-side app.</span></span> <span data-ttu-id="4d41c-183">Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="4d41c-183">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="4d41c-184">Parameter werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="4d41c-184">Parameters aren't supported.</span></span> |
| `Server`            | <span data-ttu-id="4d41c-185">Rendert einen Marker für eine serverseitige blazor-app.</span><span class="sxs-lookup"><span data-stu-id="4d41c-185">Renders a marker for a Blazor server-side app.</span></span> <span data-ttu-id="4d41c-186">Die Ausgabe der Komponente ist nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="4d41c-186">Output from the component isn't included.</span></span> <span data-ttu-id="4d41c-187">Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="4d41c-187">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="4d41c-188">Parameter werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="4d41c-188">Parameters aren't supported.</span></span> |
| `Static`            | <span data-ttu-id="4d41c-189">Rendert die Komponente in statischem HTML-Format.</span><span class="sxs-lookup"><span data-stu-id="4d41c-189">Renders the component into static HTML.</span></span> <span data-ttu-id="4d41c-190">Parameter werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="4d41c-190">Parameters are supported.</span></span> |

<span data-ttu-id="4d41c-191">Das Rendering von Serverkomponenten von einer statischen HTML-Seite wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="4d41c-191">Rendering server components from a static HTML page isn't supported.</span></span>
 
<span data-ttu-id="4d41c-192">Der Client stellt erneut eine Verbindung mit dem Server mit dem gleichen Zustand her, der für die Vorabversion der APP verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="4d41c-192">The client reconnects to the server with the same state that was used to prerender the app.</span></span> <span data-ttu-id="4d41c-193">Wenn sich der Status der APP weiterhin im Arbeitsspeicher befindet, wird der Komponenten Status nach dem Herstellen der signalr-Verbindung nicht erneut umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="4d41c-193">If the app's state is still in memory, the component state isn't rerendered after the SignalR connection is established.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="4d41c-194">Zustands behaftete interaktive Komponenten von Razor Pages und Ansichten</span><span class="sxs-lookup"><span data-stu-id="4d41c-194">Render stateful interactive components from Razor pages and views</span></span>
 
<span data-ttu-id="4d41c-195">Zustands behaftete interaktive Komponenten können einer Razor Page oder Ansicht hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="4d41c-195">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="4d41c-196">Wenn die Seite oder Ansicht gerendert wird:</span><span class="sxs-lookup"><span data-stu-id="4d41c-196">When the page or view renders:</span></span>

* <span data-ttu-id="4d41c-197">Die Komponente wird mit der Seite oder der Ansicht vorab überstehen.</span><span class="sxs-lookup"><span data-stu-id="4d41c-197">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="4d41c-198">Der anfängliche Komponenten Zustand, der für die vorab Generierung verwendet wird, geht verloren.</span><span class="sxs-lookup"><span data-stu-id="4d41c-198">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="4d41c-199">Der neue Komponenten Status wird erstellt, wenn die signalr-Verbindung hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="4d41c-199">New component state is created when the SignalR connection is established.</span></span>
 
<span data-ttu-id="4d41c-200">Die folgende Razor Page rendert `Counter` eine Komponente:</span><span class="sxs-lookup"><span data-stu-id="4d41c-200">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>
 
@(await Html.RenderComponentAsync<Counter>(RenderMode.ServerPrerendered))
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="4d41c-201">Nicht interaktive Komponenten von Razor Pages und Ansichten</span><span class="sxs-lookup"><span data-stu-id="4d41c-201">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="4d41c-202">Auf der folgenden Razor Page wird die `MyComponent` Komponente statisch mit einem Anfangswert gerendert, der mit einem-Format angegeben wird:</span><span class="sxs-lookup"><span data-stu-id="4d41c-202">In the following Razor page, the `MyComponent` component is statically rendered with an initial value that's specified using a form:</span></span>
 
```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>
 
@(await Html.RenderComponentAsync<MyComponent>(RenderMode.Static, 
    new { InitialValue = InitialValue }))
 
@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="4d41c-203">Da `MyComponent` statisch gerendert wird, kann die Komponente nicht interaktiv sein.</span><span class="sxs-lookup"><span data-stu-id="4d41c-203">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="4d41c-204">Erkennen, wenn die APP vorab durchgeführt wird</span><span class="sxs-lookup"><span data-stu-id="4d41c-204">Detect when the app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-signalr-client-for-blazor-server-side-apps"></a><span data-ttu-id="4d41c-205">Konfigurieren des signalr-Clients für serverseitige blazor-apps</span><span class="sxs-lookup"><span data-stu-id="4d41c-205">Configure the SignalR client for Blazor server-side apps</span></span>
 
<span data-ttu-id="4d41c-206">Manchmal müssen Sie den signalr-Client konfigurieren, der von serverseitigen blazor-Apps verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="4d41c-206">Sometimes, you need to configure the SignalR client used by Blazor server-side apps.</span></span> <span data-ttu-id="4d41c-207">Beispielsweise können Sie die Protokollierung auf dem signalr-Client konfigurieren, um ein Verbindungsproblem zu diagnostizieren.</span><span class="sxs-lookup"><span data-stu-id="4d41c-207">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>
 
<span data-ttu-id="4d41c-208">So konfigurieren Sie den signalr-Client in der Datei *pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="4d41c-208">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="4d41c-209">Fügen Sie `autostart="false"` dem `<script>` -Tag für das Skript " *blazor. Server. js* " ein Attribut hinzu.</span><span class="sxs-lookup"><span data-stu-id="4d41c-209">Add an `autostart="false"` attribute to the `<script>` tag for the *blazor.server.js* script.</span></span>
* <span data-ttu-id="4d41c-210">Ruft `Blazor.start` auf und übergibt ein Konfigurationsobjekt, das den signalr-Generator angibt.</span><span class="sxs-lookup"><span data-stu-id="4d41c-210">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>
 
```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging(2); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a><span data-ttu-id="4d41c-211">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="4d41c-211">Additional resources</span></span>

* <xref:blazor/get-started>
* <xref:signalr/introduction>

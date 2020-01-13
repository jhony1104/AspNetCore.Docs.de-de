---
title: ASP.net Core Blazor Hostingmodellen
author: guardrex
description: Verstehen Blazor Webassembly-und Blazor Server-Hostingmodellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-models
ms.openlocfilehash: 7676d16bddf146ea38619ed35c5e32c5bce731de
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74943762"
---
# <a name="aspnet-core-opno-locblazor-hosting-models"></a><span data-ttu-id="bfcd3-103">ASP.net Core Blazor Hostingmodellen</span><span class="sxs-lookup"><span data-stu-id="bfcd3-103">ASP.NET Core Blazor hosting models</span></span>

<span data-ttu-id="bfcd3-104">Von [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="bfcd3-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="bfcd3-105"> ist ein Webframework, das für die Client seitige Ausführung im Browser auf einer [webassemblybasierten](https://webassembly.org/) .NET-Runtime ( *Blazor Webassembly*) oder Serverseite in ASP.net Core ( *Blazor Server*) konzipiert ist.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-105"> is a web framework designed to run client-side in the browser on a [WebAssembly](https://webassembly.org/)-based .NET runtime (*Blazor WebAssembly*) or server-side in ASP.NET Core (*Blazor Server*).</span></span> <span data-ttu-id="bfcd3-106">Unabhängig vom Hostingmodell sind die APP-und Komponentenmodelle *identisch*.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-106">Regardless of the hosting model, the app and component models *are the same*.</span></span>

<span data-ttu-id="bfcd3-107">Informationen zum Erstellen eines Projekts für die in diesem Artikel beschriebenen Hostingmodelle finden Sie unter <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-107">To create a project for the hosting models described in this article, see <xref:blazor/get-started>.</span></span>

## <a name="opno-locblazor-webassembly"></a>Blazor<span data-ttu-id="bfcd3-108"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="bfcd3-108"> WebAssembly</span></span>

<span data-ttu-id="bfcd3-109">Das Prinzipal-Hostingmodell für Blazor wird Client seitig im Browser in Webassembly ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-109">The principal hosting model for Blazor is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="bfcd3-110">Die Blazor-App, die jeweiligen Abhängigkeiten und die .NET-Runtime werden im Browser heruntergeladen.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-110">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="bfcd3-111">Die App wird direkt im UI-Thread des Browsers ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-111">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="bfcd3-112">UI-Updates und Ereignis Behandlung treten innerhalb desselben Prozesses auf.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-112">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="bfcd3-113">Die Assets der App werden als statische Dateien auf einem Webserver oder Dienst bereitgestellt, in dem statische Inhalte für Clients bereitgestellt werden können.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-113">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![[! Schel. No-Loc (blazor)]<span data-ttu-id="bfcd3-114"> Webassembly: [! Schel. Die APP NO-LOC (blazor)] wird in einem UI-Thread im Browser ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-114"> WebAssembly: The Blazor app runs on a UI thread inside the browser.</span></span>](hosting-models/_static/blazor-webassembly.png)

<span data-ttu-id="bfcd3-115">Verwenden Sie zum Erstellen einer Blazor-App mithilfe des Client seitigen Hostingmodells die **Blazor Webassembly-App** -Vorlage ([dotnet New blazorwasm](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="bfcd3-115">To create a Blazor app using the client-side hosting model, use the **Blazor WebAssembly App** template ([dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)).</span></span>

<span data-ttu-id="bfcd3-116">Nachdem Sie die Vorlage **Blazor Webassembly-App** ausgewählt haben, haben Sie die Möglichkeit, die APP für die Verwendung eines ASP.net Core-Back-Ends zu konfigurieren, indem Sie das Kontrollkästchen **ASP.net Core gehostet** aktivieren ([dotnet New blazorwasm--Hosted](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="bfcd3-116">After selecting the **Blazor WebAssembly App** template, you have the option of configuring the app to use an ASP.NET Core backend by selecting the **ASP.NET Core hosted** check box ([dotnet new blazorwasm --hosted](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="bfcd3-117">Die ASP.net Core-App dient der Blazor-App für Clients.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-117">The ASP.NET Core app serves the Blazor app to clients.</span></span> <span data-ttu-id="bfcd3-118">Die Blazor Webassembly-App kann mithilfe von Web-API-aufrufen oder [SignalR](xref:signalr/introduction)mit dem Server über das Netzwerk interagieren.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-118">The Blazor WebAssembly app can interact with the server over the network using web API calls or [SignalR](xref:signalr/introduction).</span></span>

<span data-ttu-id="bfcd3-119">Die Vorlagen enthalten das Skript " *blazor. Webassembly. js* ", das Folgendes behandelt:</span><span class="sxs-lookup"><span data-stu-id="bfcd3-119">The templates include the *blazor.webassembly.js* script that handles:</span></span>

* <span data-ttu-id="bfcd3-120">Herunterladen der .NET-Laufzeit, der APP und der APP-Abhängigkeiten.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-120">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="bfcd3-121">Initialisierung der Laufzeit zum Ausführen der app.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-121">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="bfcd3-122">Das Blazor Webassembly-Hostingmodell bietet mehrere Vorteile:</span><span class="sxs-lookup"><span data-stu-id="bfcd3-122">The Blazor WebAssembly hosting model offers several benefits:</span></span>

* <span data-ttu-id="bfcd3-123">Es gibt keine serverseitige .net-Abhängigkeit.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-123">There's no .NET server-side dependency.</span></span> <span data-ttu-id="bfcd3-124">Die APP funktioniert nach dem Herunterladen auf den Client vollständig.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-124">The app is fully functioning after downloaded to the client.</span></span>
* <span data-ttu-id="bfcd3-125">Client Ressourcen und-Funktionen sind vollständig ausgelastet.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-125">Client resources and capabilities are fully leveraged.</span></span>
* <span data-ttu-id="bfcd3-126">Die Arbeit wird vom Server an den Client verlagert.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-126">Work is offloaded from the server to the client.</span></span>
* <span data-ttu-id="bfcd3-127">Ein ASP.net Core Webserver ist nicht erforderlich, um die APP zu hosten.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-127">An ASP.NET Core web server isn't required to host the app.</span></span> <span data-ttu-id="bfcd3-128">Server lose Bereitstellungs Szenarien sind möglich (z. b. das Bereitstellen der APP aus einem CDN).</span><span class="sxs-lookup"><span data-stu-id="bfcd3-128">Serverless deployment scenarios are possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="bfcd3-129">Es gibt Nachteile für Blazor Webassembly-Hosting:</span><span class="sxs-lookup"><span data-stu-id="bfcd3-129">There are downsides to Blazor WebAssembly hosting:</span></span>

* <span data-ttu-id="bfcd3-130">Die APP ist auf die Funktionen des Browsers beschränkt.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-130">The app is restricted to the capabilities of the browser.</span></span>
* <span data-ttu-id="bfcd3-131">Kompatible Client Hardware und Software (z. b. webassemblyunterstützung) ist erforderlich.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-131">Capable client hardware and software (for example, WebAssembly support) is required.</span></span>
* <span data-ttu-id="bfcd3-132">Die Download Größe ist größer, und die Auslastung von apps dauert länger.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-132">Download size is larger, and apps take longer to load.</span></span>
* <span data-ttu-id="bfcd3-133">Die Unterstützung von .NET-Laufzeit und-Tools ist weniger ausgereift.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-133">.NET runtime and tooling support is less mature.</span></span> <span data-ttu-id="bfcd3-134">Beispielsweise gibt es Einschränkungen in [.NET Standard](/dotnet/standard/net-standard) Unterstützung und Debuggen.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-134">For example, limitations exist in [.NET Standard](/dotnet/standard/net-standard) support and debugging.</span></span>

## <a name="opno-locblazor-server"></a>Blazor<span data-ttu-id="bfcd3-135"> Server</span><span class="sxs-lookup"><span data-stu-id="bfcd3-135"> Server</span></span>

<span data-ttu-id="bfcd3-136">Mit dem Blazor Server-Hostingmodell wird die APP auf dem Server innerhalb einer ASP.net Core-app ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-136">With the Blazor Server hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="bfcd3-137">Benutzeroberflächenupdates, Ereignisbehandlung und JavaScript-Aufrufe werden über eine [SignalR](xref:signalr/introduction)-Verbindung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-137">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![Der Browser interagiert mit der APP (gehostet innerhalb einer ASP.net Core-APP) auf dem Server über einen [! Schel. No-Loc (signalr)]-Verbindung.](hosting-models/_static/blazor-server.png)

<span data-ttu-id="bfcd3-139">Verwenden Sie zum Erstellen einer Blazor-App mithilfe des Blazor Server-Hostingmodells die ASP.net Core **Blazor Server-App** -Vorlage ([dotnet New blazorserver](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="bfcd3-139">To create a Blazor app using the Blazor Server hosting model, use the ASP.NET Core **Blazor Server App** template ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="bfcd3-140">Die ASP.net Core-App hostet die Blazor Server-APP und erstellt den SignalR Endpunkt, auf dem Clients eine Verbindung herstellen.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-140">The ASP.NET Core app hosts the Blazor Server app and creates the SignalR endpoint where clients connect.</span></span>

<span data-ttu-id="bfcd3-141">Die ASP.net Core-App verweist auf die `Startup` Klasse der APP, um Folgendes hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="bfcd3-141">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="bfcd3-142">Server seitige Dienste.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-142">Server-side services.</span></span>
* <span data-ttu-id="bfcd3-143">Die APP für die Pipeline für die Anforderungs Verarbeitung.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-143">The app to the request handling pipeline.</span></span>

<span data-ttu-id="bfcd3-144">Das Skript " *blazor. Server. js* "&dagger; die Client Verbindung herstellt.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-144">The *blazor.server.js* script&dagger; establishes the client connection.</span></span> <span data-ttu-id="bfcd3-145">Es liegt in der Verantwortung der APP, den App-Zustand nach Bedarf beizubehalten und wiederherzustellen (z. b. im Fall einer verlorenen Netzwerkverbindung).</span><span class="sxs-lookup"><span data-stu-id="bfcd3-145">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="bfcd3-146">Das Blazor Server-Hostingmodell bietet mehrere Vorteile:</span><span class="sxs-lookup"><span data-stu-id="bfcd3-146">The Blazor Server hosting model offers several benefits:</span></span>

* <span data-ttu-id="bfcd3-147">Die Download Größe ist erheblich kleiner als eine Blazor Webassembly-APP, und die APP lädt viel schneller.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-147">Download size is significantly smaller than a Blazor WebAssembly app, and the app loads much faster.</span></span>
* <span data-ttu-id="bfcd3-148">Die App nutzt vollständige Vorteile der Serverfunktionen, einschließlich der Verwendung von .net Core-kompatiblen APIs.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-148">The app takes full advantage of server capabilities, including use of any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="bfcd3-149">.Net Core auf dem Server wird verwendet, um die APP auszuführen, sodass vorhandene .NET-Tools, wie z. b. das Debuggen, erwartungsgemäß funktionieren.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-149">.NET Core on the server is used to run the app, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="bfcd3-150">Thin Clients werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-150">Thin clients are supported.</span></span> <span data-ttu-id="bfcd3-151">Blazor Server-apps funktionieren z. b. mit Browsern, die Webassembly nicht unterstützen, und auf Geräten, die auf Ressourcen beschränkt sind.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-151">For example, Blazor Server apps work with browsers that don't support WebAssembly and on resource-constrained devices.</span></span>
* <span data-ttu-id="bfcd3-152">Die .net-C# /Codebasis der APP, einschließlich des Komponenten Codes der APP, wird nicht für Clients bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-152">The app's .NET/C# code base, including the app's component code, isn't served to clients.</span></span>

<span data-ttu-id="bfcd3-153">Es gibt Nachteile für Blazor Server Hosting:</span><span class="sxs-lookup"><span data-stu-id="bfcd3-153">There are downsides to Blazor Server hosting:</span></span>

* <span data-ttu-id="bfcd3-154">Eine höhere Latenz ist normalerweise vorhanden.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-154">Higher latency usually exists.</span></span> <span data-ttu-id="bfcd3-155">Jede Benutzerinteraktion umfasst einen Netzwerkhop.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-155">Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="bfcd3-156">Es gibt keine Offline Unterstützung.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-156">There's no offline support.</span></span> <span data-ttu-id="bfcd3-157">Wenn die Client Verbindung fehlschlägt, funktioniert die APP nicht mehr.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-157">If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="bfcd3-158">Skalierbarkeit ist eine Herausforderung für apps mit vielen Benutzern.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-158">Scalability is challenging for apps with many users.</span></span> <span data-ttu-id="bfcd3-159">Der Server muss mehrere Clientverbindungen verwalten und den Client Status verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-159">The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="bfcd3-160">Ein ASP.net Core Server ist erforderlich, um der APP zu dienen.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-160">An ASP.NET Core server is required to serve the app.</span></span> <span data-ttu-id="bfcd3-161">Server lose Bereitstellungs Szenarien sind nicht möglich (z. b. für die APP aus einem CDN).</span><span class="sxs-lookup"><span data-stu-id="bfcd3-161">Serverless deployment scenarios aren't possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="bfcd3-162">&dagger;das Skript " *blazor. Server. js* " über eine eingebettete Ressource in ASP.net Core Shared Framework bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-162">&dagger;The *blazor.server.js* script is served from an embedded resource in the ASP.NET Core shared framework.</span></span>

### <a name="comparison-to-server-rendered-ui"></a><span data-ttu-id="bfcd3-163">Vergleich mit der Server gerenderten Benutzeroberfläche</span><span class="sxs-lookup"><span data-stu-id="bfcd3-163">Comparison to server-rendered UI</span></span>

<span data-ttu-id="bfcd3-164">Eine Möglichkeit, Blazor Server-apps zu verstehen, besteht darin, zu verstehen, wie Sie sich von herkömmlichen Modellen zum Rendern von Benutzeroberflächen in ASP.net Core Apps mithilfe von Razor-Ansichten oder Razor Pages</span><span class="sxs-lookup"><span data-stu-id="bfcd3-164">One way to understand Blazor Server apps is to understand how it differs from traditional models for rendering UI in ASP.NET Core apps using Razor views or Razor Pages.</span></span> <span data-ttu-id="bfcd3-165">Beide Modelle verwenden die Razor-Sprache, um HTML-Inhalte zu beschreiben. Sie unterscheiden sich jedoch deutlich von der Darstellung von Markup.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-165">Both models use the Razor language to describe HTML content, but they significantly differ in how markup is rendered.</span></span>

<span data-ttu-id="bfcd3-166">Wenn eine Razor Page oder eine Ansicht gerendert wird, gibt jede Zeile von Razor-Code HTML-Code in Textform aus.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-166">When a Razor Page or view is rendered, every line of Razor code emits HTML in text form.</span></span> <span data-ttu-id="bfcd3-167">Nach dem Rendering verwirft der Server die Seiten-oder Sicht Instanz, einschließlich eines beliebigen Zustands, der erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-167">After rendering, the server disposes of the page or view instance, including any state that was produced.</span></span> <span data-ttu-id="bfcd3-168">Wenn eine andere Anforderung für die Seite auftritt, z. b. wenn die Server Validierung fehlschlägt und die Validierungs Zusammenfassung angezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="bfcd3-168">When another request for the page occurs, for instance when server validation fails and the validation summary is displayed:</span></span>

* <span data-ttu-id="bfcd3-169">Die gesamte Seite wird erneut in HTML-Text umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-169">The entire page is rerendered to HTML text again.</span></span>
* <span data-ttu-id="bfcd3-170">Die Seite wird an den Client gesendet.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-170">The page is sent to the client.</span></span>

<span data-ttu-id="bfcd3-171">Eine Blazor-App besteht aus wiederverwendbaren Elementen der UI, die als *Komponenten*bezeichnet werden.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-171">A Blazor app is composed of reusable elements of UI called *components*.</span></span> <span data-ttu-id="bfcd3-172">Eine Komponente enthält C# Code, Markup und andere Komponenten.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-172">A component contains C# code, markup, and other components.</span></span> <span data-ttu-id="bfcd3-173">Wenn eine Komponente gerendert wird, erstellt Blazor ein Diagramm der enthaltenen Komponenten, ähnlich wie HTML-oder XML-Dokumentobjektmodell (DOM).</span><span class="sxs-lookup"><span data-stu-id="bfcd3-173">When a component is rendered, Blazor produces a graph of the included components similar to an HTML or XML Document Object Model (DOM).</span></span> <span data-ttu-id="bfcd3-174">Dieses Diagramm enthält den Komponenten Zustand in Eigenschaften und Feldern.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-174">This graph includes component state held in properties and fields.</span></span> Blazor<span data-ttu-id="bfcd3-175"> wertet das Komponenten Diagramm aus, sodass eine binäre Darstellung des Markups erzeugt wird.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-175"> evaluates the component graph to produce a binary representation of the markup.</span></span> <span data-ttu-id="bfcd3-176">Das binäre Format kann wie folgt lauten:</span><span class="sxs-lookup"><span data-stu-id="bfcd3-176">The binary format can be:</span></span>

* <span data-ttu-id="bfcd3-177">Wird in HTML-Text (während der vorab Generierung) umgewandelt.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-177">Turned into HTML text (during prerendering).</span></span>
* <span data-ttu-id="bfcd3-178">Wird verwendet, um das Markup beim regulären Rendering effizient zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-178">Used to efficiently update the markup during regular rendering.</span></span>

<span data-ttu-id="bfcd3-179">Ein Benutzeroberflächen Update in Blazor wird durch Folgendes ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="bfcd3-179">A UI update in Blazor is triggered by:</span></span>

* <span data-ttu-id="bfcd3-180">Benutzerinteraktion, z. b. das Auswählen einer Schaltfläche.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-180">User interaction, such as selecting a button.</span></span>
* <span data-ttu-id="bfcd3-181">App-Trigger, z. b. ein Timer.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-181">App triggers, such as a timer.</span></span>

<span data-ttu-id="bfcd3-182">Das Diagramm wird erneut ausgeführt, und es wird ein UI- *diff* (Differenz) berechnet.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-182">The graph is rerendered, and a UI *diff* (difference) is calculated.</span></span> <span data-ttu-id="bfcd3-183">Dieser Unterschied ist der kleinste Satz an DOM-Änderungen, die erforderlich sind, um die Benutzeroberfläche auf dem Client zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-183">This diff is the smallest set of DOM edits required to update the UI on the client.</span></span> <span data-ttu-id="bfcd3-184">Der diff wird in einem binären Format an den Client gesendet und vom Browser angewendet.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-184">The diff is sent to the client in a binary format and applied by the browser.</span></span>

<span data-ttu-id="bfcd3-185">Eine Komponente wird verworfen, nachdem der Benutzer auf dem Client dorthin navigiert ist.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-185">A component is disposed after the user navigates away from it on the client.</span></span> <span data-ttu-id="bfcd3-186">Während ein Benutzer mit einer Komponente interagiert, muss der Zustand der Komponente (Dienste, Ressourcen) im Arbeitsspeicher des Servers gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-186">While a user is interacting with a component, the component's state (services, resources) must be held in the server's memory.</span></span> <span data-ttu-id="bfcd3-187">Da der Status vieler Komponenten möglicherweise gleichzeitig vom Server verwaltet wird, ist die Arbeitsspeicher Erschöpfung ein Problem, das behoben werden muss.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-187">Because the state of many components might be maintained by the server concurrently, memory exhaustion is a concern that must be addressed.</span></span> <span data-ttu-id="bfcd3-188">Anleitungen zum Erstellen einer Blazor Server-APP, um die optimale Verwendung des Server Arbeitsspeichers sicherzustellen, finden Sie unter <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-188">For guidance on how to author a Blazor Server app to ensure the best use of server memory, see <xref:security/blazor/server>.</span></span>

### <a name="circuits"></a><span data-ttu-id="bfcd3-189">Fen</span><span class="sxs-lookup"><span data-stu-id="bfcd3-189">Circuits</span></span>

<span data-ttu-id="bfcd3-190">Eine Blazor Server-App basiert auf [ASP.net Core SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="bfcd3-190">A Blazor Server app is built on top of [ASP.NET Core SignalR](xref:signalr/introduction).</span></span> <span data-ttu-id="bfcd3-191">Jeder Client kommuniziert über *mindestens eine SignalR* Verbindung, die als Verbindung bezeichnet wird, mit dem Server.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-191">Each client communicates to the server over one or more SignalR connections called a *circuit*.</span></span> <span data-ttu-id="bfcd3-192">Eine Verbindung ist BlazorAbstraktion über SignalR Verbindungen, die temporäre Netzwerkunterbrechungen tolerieren können.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-192">A circuit is Blazor's abstraction over SignalR connections that can tolerate temporary network interruptions.</span></span> <span data-ttu-id="bfcd3-193">Wenn ein Blazor Client feststellt, dass die Verbindung mit dem SignalR getrennt ist, versucht er, eine Verbindung mit dem Server herzustellen, indem eine neue SignalR Verbindung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-193">When a Blazor client sees that the SignalR connection is disconnected, it attempts to reconnect to the server using a new SignalR connection.</span></span>

<span data-ttu-id="bfcd3-194">Jeder Browser Bildschirm (Browser Registerkarte oder IFRAME), der mit einer Blazor Server-App verbunden ist, verwendet eine SignalR Verbindung.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-194">Each browser screen (browser tab or iframe) that is connected to a Blazor Server app uses a SignalR connection.</span></span> <span data-ttu-id="bfcd3-195">Dies ist jedoch ein weiterer wichtiger Unterschied im Vergleich zu typischen, von Servern gerenderten apps.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-195">This is yet another important distinction compared to typical server-rendered apps.</span></span> <span data-ttu-id="bfcd3-196">In einer Server gerenderten APP wird das Öffnen derselben app in mehreren Browser Bildschirmen in der Regel nicht zu zusätzlichen Ressourcenanforderungen auf dem Server übertragen.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-196">In a server-rendered app, opening the same app in multiple browser screens typically doesn't translate into additional resource demands on the server.</span></span> <span data-ttu-id="bfcd3-197">In einer Blazor Server-App erfordert jeder Browser Bildschirm eine separate Verbindung und separate Instanzen des Komponenten Zustands, die vom Server verwaltet werden.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-197">In a Blazor Server app, each browser screen requires a separate circuit and separate instances of component state to be managed by the server.</span></span>

Blazor<span data-ttu-id="bfcd3-198"> das Schließen einer Browser Registerkarte oder navigieren zu einer externen URL zu einer Ordnungs *gemäßen Beendigung.*</span><span class="sxs-lookup"><span data-stu-id="bfcd3-198"> considers closing a browser tab or navigating to an external URL a *graceful* termination.</span></span> <span data-ttu-id="bfcd3-199">Im Fall einer ordnungsgemäßen Beendigung werden die Verbindung und zugehörige Ressourcen sofort freigegeben.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-199">In the event of a graceful termination, the circuit and associated resources are immediately released.</span></span> <span data-ttu-id="bfcd3-200">Ein Client kann möglicherweise auch nicht ordnungsgemäß getrennt werden, beispielsweise aufgrund einer Netzwerk Unterbrechung.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-200">A client may also disconnect non-gracefully, for instance due to a network interruption.</span></span> Blazor<span data-ttu-id="bfcd3-201"> Server speichert getrennte Verbindungen für ein konfigurierbares Intervall, damit der Client die Verbindung wiederherstellen kann.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-201"> Server stores disconnected circuits for a configurable interval to allow the client to reconnect.</span></span> <span data-ttu-id="bfcd3-202">Weitere Informationen finden Sie im Abschnitt [Wiederherstellen der Verbindung mit dem gleichen Server](#reconnection-to-the-same-server) .</span><span class="sxs-lookup"><span data-stu-id="bfcd3-202">For more information, see the [Reconnection to the same server](#reconnection-to-the-same-server) section.</span></span>

### <a name="ui-latency"></a><span data-ttu-id="bfcd3-203">UI-Latenz</span><span class="sxs-lookup"><span data-stu-id="bfcd3-203">UI Latency</span></span>

<span data-ttu-id="bfcd3-204">Die Benutzeroberflächen Latenz ist die Zeit, die Sie von einer initiierten Aktion bis zum Zeitpunkt der Aktualisierung der Benutzeroberfläche benötigt.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-204">UI latency is the time it takes from an initiated action to the time the UI is updated.</span></span> <span data-ttu-id="bfcd3-205">Kleinere Werte für die Benutzeroberflächen Latenz sind zwingend erforderlich, damit eine APP für einen Benutzer reaktionsfähig ist.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-205">Smaller values for UI latency are imperative for an app to feel responsive to a user.</span></span> <span data-ttu-id="bfcd3-206">In einer Blazor Server-APP wird jede Aktion an den Server gesendet, verarbeitet und ein UI-diff zurückgesendet.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-206">In a Blazor Server app, each action is sent to the server, processed, and a UI diff is sent back.</span></span> <span data-ttu-id="bfcd3-207">Folglich ist die Benutzeroberflächen Latenz die Summe der Netzwerk Latenz und der Server Latenz bei der Verarbeitung der Aktion.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-207">Consequently, UI latency is the sum of network latency and the server latency in processing the action.</span></span>

<span data-ttu-id="bfcd3-208">Für eine Branchen-APP, die auf ein privates Unternehmensnetzwerk beschränkt ist, sind die Auswirkungen auf die Benutzer Wahrnehmung von Latenzzeit aufgrund der Netzwerk Latenz in der Regel nicht wahrnehmbar.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-208">For a line of business app that's limited to a private corporate network, the effect on user perceptions of latency due to network latency are usually imperceptible.</span></span> <span data-ttu-id="bfcd3-209">Für eine APP, die über das Internet bereitgestellt wird, kann die Latenz für Benutzer bemerkbar werden, insbesondere, wenn die Benutzer weitgehend geografisch verteilt sind.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-209">For an app deployed over the Internet, latency may become noticeable to users, particularly if users are widely distributed geographically.</span></span>

<span data-ttu-id="bfcd3-210">Die Speicherauslastung kann auch zur APP-Latenz beitragen.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-210">Memory usage can also contribute to app latency.</span></span> <span data-ttu-id="bfcd3-211">Eine erhöhte Arbeitsspeicher Auslastung führt zu häufigen Garbage Collection-oder Paging-Speicher auf den Datenträger, die beide die Leistung der APP beeinträchtigen und somit die Benutzeroberflächen Latenz erhöhen.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-211">Increased memory usage results in frequent garbage collection or paging memory to disk, both of which degrade app performance and consequently increase UI latency.</span></span> <span data-ttu-id="bfcd3-212">Weitere Informationen finden Sie unter <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-212">For more information, see <xref:security/blazor/server>.</span></span>

Blazor<span data-ttu-id="bfcd3-213"> Server-apps sollten optimiert werden, um die Benutzeroberflächen Latenz zu minimieren, indem Netzwerk Latenz und Speicherauslastung reduziert werden</span><span class="sxs-lookup"><span data-stu-id="bfcd3-213"> Server apps should be optimized to minimize UI latency by reducing network latency and memory usage.</span></span> <span data-ttu-id="bfcd3-214">Einen Ansatz zum Messen der Netzwerk Latenz finden Sie unter <xref:host-and-deploy/blazor/server#measure-network-latency>.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-214">For an approach to measuring network latency, see <xref:host-and-deploy/blazor/server#measure-network-latency>.</span></span> <span data-ttu-id="bfcd3-215">Weitere Informationen zu SignalR und Blazorfinden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="bfcd3-215">For more information on SignalR and Blazor, see:</span></span>

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="connection-to-the-server"></a><span data-ttu-id="bfcd3-216">Verbindung mit dem Server</span><span class="sxs-lookup"><span data-stu-id="bfcd3-216">Connection to the server</span></span>

Blazor<span data-ttu-id="bfcd3-217"> Server-apps benötigen eine aktive SignalR Verbindung mit dem Server.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-217"> Server apps require an active SignalR connection to the server.</span></span> <span data-ttu-id="bfcd3-218">Wenn die Verbindung unterbrochen wird, versucht die APP, erneut eine Verbindung mit dem Server herzustellen.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-218">If the connection is lost, the app attempts to reconnect to the server.</span></span> <span data-ttu-id="bfcd3-219">Solange sich der Status des Clients weiterhin im Arbeitsspeicher befindet, wird die Client Sitzung fortgesetzt, ohne den Zustand zu verlieren.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-219">As long as the client's state is still in memory, the client session resumes without losing state.</span></span>

#### <a name="reconnection-to-the-same-server"></a><span data-ttu-id="bfcd3-220">Erneute Verbindung mit demselben Server</span><span class="sxs-lookup"><span data-stu-id="bfcd3-220">Reconnection to the same server</span></span>

<span data-ttu-id="bfcd3-221">Eine Blazor Server-App als Antwort auf die erste Client Anforderung, die den UI-Status auf dem Server festlegt.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-221">A Blazor Server app prerenders in response to the first client request, which sets up the UI state on the server.</span></span> <span data-ttu-id="bfcd3-222">Wenn der Client versucht, eine SignalR Verbindung zu erstellen, muss der Client erneut eine Verbindung mit dem gleichen Server herstellen.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-222">When the client attempts to create a SignalR connection, the client must reconnect to the same server.</span></span> Blazor<span data-ttu-id="bfcd3-223"> Server-apps, die mehr als einen Back-End-Server verwenden, sollten persistente *Sitzungen* für SignalR Verbindungen implementieren.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-223"> Server apps that use more than one backend server should implement *sticky sessions* for SignalR connections.</span></span>

<span data-ttu-id="bfcd3-224">Sie sollten [Azure SignalR Service](/azure/azure-signalr) für Blazor Server-Apps verwenden.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-224">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="bfcd3-225">Der Dienst ermöglicht das zentrale Hochskalieren einer Blazor Server-App auf eine große Anzahl gleichzeitiger SignalR-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-225">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="bfcd3-226">Persistente Sitzungen werden für den Azure SignalR-Dienst aktiviert, indem die `ServerStickyMode` Option oder der Konfigurations Wert für den Dienst auf `Required`festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-226">Sticky sessions are enabled for the Azure SignalR Service by setting the service's `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="bfcd3-227">Weitere Informationen finden Sie unter <xref:host-and-deploy/blazor/server#signalr-configuration>.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-227">For more information, see <xref:host-and-deploy/blazor/server#signalr-configuration>.</span></span>

<span data-ttu-id="bfcd3-228">Wenn Sie IIS verwenden, werden persistente Sitzungen mit dem Routing von Anwendungsanforderungen aktiviert.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-228">When using IIS, sticky sessions are enabled with Application Request Routing.</span></span> <span data-ttu-id="bfcd3-229">Weitere Informationen finden Sie unter [http-Lastenausgleich mithilfe des Routing von Anwendungsanforderungen](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="bfcd3-229">For more information, see [HTTP Load Balancing using Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="bfcd3-230">Reflektieren des Verbindungs Zustands in der Benutzeroberfläche</span><span class="sxs-lookup"><span data-stu-id="bfcd3-230">Reflect the connection state in the UI</span></span>

<span data-ttu-id="bfcd3-231">Wenn der Client erkennt, dass die Verbindung unterbrochen wurde, wird dem Benutzer eine Standardbenutzer Oberfläche angezeigt, während der Client versucht, die Verbindung wiederherzustellen.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-231">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="bfcd3-232">Wenn bei der erneuten Verbindungs Herstellung ein Fehler auftritt, wird dem Benutzer die Option zum erneuten versuchen bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-232">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="bfcd3-233">Zum Anpassen der Benutzeroberfläche definieren Sie ein Element mit einem `id` `components-reconnect-modal` im `<body>` der Razor Page *_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="bfcd3-233">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="bfcd3-234">In der folgenden Tabelle werden die CSS-Klassen beschrieben, die auf das `components-reconnect-modal` Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-234">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="bfcd3-235">CSS-Klasse</span><span class="sxs-lookup"><span data-stu-id="bfcd3-235">CSS class</span></span>                       | <span data-ttu-id="bfcd3-236">Gibt&hellip;</span><span class="sxs-lookup"><span data-stu-id="bfcd3-236">Indicates&hellip;</span></span> |
| ------------------------------- | ------------------------- |
| `components-reconnect-show`     | <span data-ttu-id="bfcd3-237">Eine verlorene Verbindung.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-237">A lost connection.</span></span> <span data-ttu-id="bfcd3-238">Der Client versucht, die Verbindung wiederherzustellen.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-238">The client is attempting to reconnect.</span></span> <span data-ttu-id="bfcd3-239">Zeigen Sie die modale an.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-239">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="bfcd3-240">Eine aktive Verbindung mit dem Server wird wieder hergestellt.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-240">An active connection is re-established to the server.</span></span> <span data-ttu-id="bfcd3-241">Blenden Sie den modalen aus.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-241">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="bfcd3-242">Fehler beim erneuten Herstellen der Verbindung, wahrscheinlich aufgrund eines Netzwerk Fehlers.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-242">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="bfcd3-243">Um erneut eine Verbindung herzustellen, wenden Sie `window.Blazor.reconnect()`an.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-243">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="bfcd3-244">Erneute Verbindung abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-244">Reconnection rejected.</span></span> <span data-ttu-id="bfcd3-245">Der Server wurde erreicht, hat jedoch die Verbindung abgelehnt, und der Benutzer Zustand auf dem Server geht verloren.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-245">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="bfcd3-246">Um die APP erneut zu laden, wenden Sie `location.reload()`an.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-246">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="bfcd3-247">Der Verbindungsstatus kann folgende Ergebnisse haben:</span><span class="sxs-lookup"><span data-stu-id="bfcd3-247">This connection state may result when:</span></span><ul><li><span data-ttu-id="bfcd3-248">Ein Absturz in der serverseitigen Verbindung tritt auf.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-248">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="bfcd3-249">Der Client ist so lange getrennt, dass der Server den Zustand des Benutzers löscht.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-249">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="bfcd3-250">Instanzen der Komponenten, mit denen der Benutzer interagiert, werden verworfen.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-250">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="bfcd3-251">Der Server wird neu gestartet, oder der Arbeitsprozess der APP wird wieder verwendet.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-251">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="bfcd3-252">Zustands behaftete erneute Verbindung nach der vorab Ausführung</span><span class="sxs-lookup"><span data-stu-id="bfcd3-252">Stateful reconnection after prerendering</span></span>

Blazor<span data-ttu-id="bfcd3-253"> Server-apps werden standardmäßig so eingerichtet, dass Sie die Benutzeroberfläche auf dem Server vorab ausführen, bevor die Client Verbindung mit dem Server hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-253"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="bfcd3-254">Dies wird auf der Razor Page *_Host. cshtml* eingerichtet:</span><span class="sxs-lookup"><span data-stu-id="bfcd3-254">This is set up in the *_Host.cshtml* Razor page:</span></span>

::: moniker range=">= aspnetcore-3.1"

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

::: moniker-end

::: moniker range="< aspnetcore-3.1"

```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>(RenderMode.ServerPrerendered))</app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

::: moniker-end

<span data-ttu-id="bfcd3-255">`RenderMode` konfiguriert, ob die Komponente Folgendes hat:</span><span class="sxs-lookup"><span data-stu-id="bfcd3-255">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="bfcd3-256">Wird in die Seite vorab übernommen.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-256">Is prerendered into the page.</span></span>
* <span data-ttu-id="bfcd3-257">Wird auf der Seite als statischer HTML-Code gerendert, oder, wenn er die erforderlichen Informationen zum Bootstrapping einer Blazor-APP vom Benutzer-Agent enthält.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-257">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

::: moniker range=">= aspnetcore-3.1"

| `RenderMode`        | <span data-ttu-id="bfcd3-258">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="bfcd3-258">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="bfcd3-259">Rendert die Komponente in statischem HTML-Format und enthält einen Marker für eine Blazor Server-app.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-259">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="bfcd3-260">Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-260">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="bfcd3-261">Rendert einen Marker für eine Blazor Server-app.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-261">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="bfcd3-262">Die Ausgabe der Komponente ist nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-262">Output from the component isn't included.</span></span> <span data-ttu-id="bfcd3-263">Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-263">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="bfcd3-264">Rendert die Komponente in statischem HTML-Format.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-264">Renders the component into static HTML.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.1"

| `RenderMode`        | <span data-ttu-id="bfcd3-265">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="bfcd3-265">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="bfcd3-266">Rendert die Komponente in statischem HTML-Format und enthält einen Marker für eine Blazor Server-app.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-266">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="bfcd3-267">Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-267">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="bfcd3-268">Parameter werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-268">Parameters aren't supported.</span></span> |
| `Server`            | <span data-ttu-id="bfcd3-269">Rendert einen Marker für eine Blazor Server-app.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-269">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="bfcd3-270">Die Ausgabe der Komponente ist nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-270">Output from the component isn't included.</span></span> <span data-ttu-id="bfcd3-271">Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-271">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="bfcd3-272">Parameter werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-272">Parameters aren't supported.</span></span> |
| `Static`            | <span data-ttu-id="bfcd3-273">Rendert die Komponente in statischem HTML-Format.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-273">Renders the component into static HTML.</span></span> <span data-ttu-id="bfcd3-274">Parameter werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-274">Parameters are supported.</span></span> |

::: moniker-end

<span data-ttu-id="bfcd3-275">Das Rendering von Serverkomponenten von einer statischen HTML-Seite wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-275">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="bfcd3-276">Wenn `RenderMode` `ServerPrerendered`ist, wird die Komponente anfänglich statisch als Teil der Seite gerendert.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-276">When `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="bfcd3-277">Sobald der Browser eine Verbindung mit dem Server herstellt, wird die Komponente *wieder*gerendert, und die Komponente ist nun interaktiv.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-277">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="bfcd3-278">Wenn die [OnInitialized {Async}](xref:blazor/lifecycle#component-initialization-methods) -Lebenszyklus Methode zum Initialisieren der Komponente vorhanden ist, wird die-Methode *zweimal*ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="bfcd3-278">If the [OnInitialized{Async}](xref:blazor/lifecycle#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="bfcd3-279">Bei statischer vorab Erstellung der Komponente.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-279">When the component is prerendered statically.</span></span>
* <span data-ttu-id="bfcd3-280">Nachdem die Server Verbindung hergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-280">After the server connection has been established.</span></span>

<span data-ttu-id="bfcd3-281">Dies kann zu einer merklichen Änderung in den Daten führen, die in der Benutzeroberfläche angezeigt werden, wenn die Komponente schließlich gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-281">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="bfcd3-282">So vermeiden Sie das doppelte renderingszenario in einer Blazor Server-App:</span><span class="sxs-lookup"><span data-stu-id="bfcd3-282">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="bfcd3-283">Übergeben Sie einen Bezeichner, der verwendet werden kann, um den Zustand während der vorab Ausführung zwischenzuspeichern und den Zustand nach dem Neustart der APP abzurufen.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-283">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="bfcd3-284">Verwenden Sie den Bezeichner während der vorab Einschreibung, um den Komponenten Zustand zu speichern.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-284">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="bfcd3-285">Verwenden Sie nach der vorab Ausführung den Bezeichner, um den zwischengespeicherten Zustand abzurufen.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-285">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="bfcd3-286">Der folgende Code veranschaulicht eine aktualisierte `WeatherForecastService` in einer Vorlagen basierten Blazor Server-APP, die das doppelte Rendering vermeidet:</span><span class="sxs-lookup"><span data-stu-id="bfcd3-286">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] Summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = Summaries[rng.Next(Summaries.Length)]
            }).ToArray();
        });
    }
}
```

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="bfcd3-287">Zustands behaftete interaktive Komponenten von Razor Pages und Ansichten</span><span class="sxs-lookup"><span data-stu-id="bfcd3-287">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="bfcd3-288">Zustands behaftete interaktive Komponenten können einer Razor Page oder Ansicht hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-288">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="bfcd3-289">Wenn die Seite oder Ansicht gerendert wird:</span><span class="sxs-lookup"><span data-stu-id="bfcd3-289">When the page or view renders:</span></span>

* <span data-ttu-id="bfcd3-290">Die Komponente wird mit der Seite oder der Ansicht vorab überstehen.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-290">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="bfcd3-291">Der anfängliche Komponenten Zustand, der für die vorab Generierung verwendet wird, geht verloren.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-291">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="bfcd3-292">Der neue Komponenten Status wird erstellt, wenn die SignalR Verbindung hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-292">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="bfcd3-293">Die folgende Razor Page rendert eine `Counter` Komponente:</span><span class="sxs-lookup"><span data-stu-id="bfcd3-293">The following Razor page renders a `Counter` component:</span></span>

::: moniker range=">= aspnetcore-3.1"

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.1"

```cshtml
<h1>My Razor Page</h1>

@(await Html.RenderComponentAsync<Counter>(RenderMode.ServerPrerendered))

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

::: moniker-end

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="bfcd3-294">Nicht interaktive Komponenten von Razor Pages und Ansichten</span><span class="sxs-lookup"><span data-stu-id="bfcd3-294">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="bfcd3-295">Auf der folgenden Razor-Seite wird die `Counter` Komponente statisch mit einem Anfangswert gerendert, der mit einem Formular angegeben wird:</span><span class="sxs-lookup"><span data-stu-id="bfcd3-295">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

::: moniker range=">= aspnetcore-3.1"

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.1"

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

@(await Html.RenderComponentAsync<Counter>(RenderMode.Static, 
    new { InitialValue = InitialValue }))

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

::: moniker-end

<span data-ttu-id="bfcd3-296">Da `MyComponent` statisch gerendert wird, kann die Komponente nicht interaktiv sein.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-296">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="bfcd3-297">Erkennen, wenn die APP vorab durchgeführt wird</span><span class="sxs-lookup"><span data-stu-id="bfcd3-297">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="bfcd3-298">Konfigurieren des SignalR Clients für Blazor Server-apps</span><span class="sxs-lookup"><span data-stu-id="bfcd3-298">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="bfcd3-299">Manchmal müssen Sie den SignalR Client konfigurieren, der von Blazor Server-Apps verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-299">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="bfcd3-300">Beispielsweise können Sie die Protokollierung für den SignalR Client konfigurieren, um ein Verbindungsproblem zu diagnostizieren.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-300">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="bfcd3-301">So konfigurieren Sie den SignalR-Client in der Datei *pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="bfcd3-301">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="bfcd3-302">Fügen Sie dem `<script>`-Tag für das Skript " *blazor. Server. js* " ein `autostart="false"` Attribut hinzu.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-302">Add an `autostart="false"` attribute to the `<script>` tag for the *blazor.server.js* script.</span></span>
* <span data-ttu-id="bfcd3-303">Ruft `Blazor.start` auf und übergibt ein Konfigurationsobjekt, das den SignalR Generator angibt.</span><span class="sxs-lookup"><span data-stu-id="bfcd3-303">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a><span data-ttu-id="bfcd3-304">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="bfcd3-304">Additional resources</span></span>

* <xref:blazor/get-started>
* <xref:signalr/introduction>

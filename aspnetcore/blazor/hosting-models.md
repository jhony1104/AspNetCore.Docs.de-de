---
title: ASP.net Core Blazor Hostingmodellen
author: guardrex
description: Verstehen Blazor Webassembly-und Blazor Server-Hostingmodellen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
- blazor.webassembly.js
uid: blazor/hosting-models
ms.openlocfilehash: 2c66bede9c1e31b22fd1612fead556176d6f192b
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76726863"
---
# <a name="aspnet-core-opno-locblazor-hosting-models"></a><span data-ttu-id="a31fa-103">ASP.net Core Blazor Hostingmodellen</span><span class="sxs-lookup"><span data-stu-id="a31fa-103">ASP.NET Core Blazor hosting models</span></span>

<span data-ttu-id="a31fa-104">Von [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="a31fa-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="a31fa-105"> ist ein Webframework, das für die Client seitige Ausführung im Browser auf einer [webassemblybasierten](https://webassembly.org/) .NET-Runtime ( *Blazor Webassembly*) oder Serverseite in ASP.net Core ( *Blazor Server*) konzipiert ist.</span><span class="sxs-lookup"><span data-stu-id="a31fa-105"> is a web framework designed to run client-side in the browser on a [WebAssembly](https://webassembly.org/)-based .NET runtime (*Blazor WebAssembly*) or server-side in ASP.NET Core (*Blazor Server*).</span></span> <span data-ttu-id="a31fa-106">Unabhängig vom Hostingmodell sind die APP-und Komponentenmodelle *identisch*.</span><span class="sxs-lookup"><span data-stu-id="a31fa-106">Regardless of the hosting model, the app and component models *are the same*.</span></span>

<span data-ttu-id="a31fa-107">Informationen zum Erstellen eines Projekts für die in diesem Artikel beschriebenen Hostingmodelle finden Sie unter <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="a31fa-107">To create a project for the hosting models described in this article, see <xref:blazor/get-started>.</span></span>

## <a name="opno-locblazor-webassembly"></a>Blazor<span data-ttu-id="a31fa-108"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="a31fa-108"> WebAssembly</span></span>

<span data-ttu-id="a31fa-109">Das Prinzipal-Hostingmodell für Blazor wird Client seitig im Browser in Webassembly ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="a31fa-109">The principal hosting model for Blazor is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="a31fa-110">Die Blazor-App, die jeweiligen Abhängigkeiten und die .NET-Runtime werden im Browser heruntergeladen.</span><span class="sxs-lookup"><span data-stu-id="a31fa-110">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="a31fa-111">Die App wird direkt im UI-Thread des Browsers ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="a31fa-111">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="a31fa-112">UI-Updates und Ereignis Behandlung treten innerhalb desselben Prozesses auf.</span><span class="sxs-lookup"><span data-stu-id="a31fa-112">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="a31fa-113">Die Assets der App werden als statische Dateien auf einem Webserver oder Dienst bereitgestellt, in dem statische Inhalte für Clients bereitgestellt werden können.</span><span class="sxs-lookup"><span data-stu-id="a31fa-113">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![[! Schel. No-Loc (blazor)]<span data-ttu-id="a31fa-114"> Webassembly: [! Schel. Die APP NO-LOC (blazor)] wird in einem UI-Thread im Browser ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="a31fa-114"> WebAssembly: The Blazor app runs on a UI thread inside the browser.</span></span>](hosting-models/_static/blazor-webassembly.png)

<span data-ttu-id="a31fa-115">Verwenden Sie zum Erstellen einer Blazor-App mithilfe des Client seitigen Hostingmodells die **Blazor Webassembly-App** -Vorlage ([dotnet New blazorwasm](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="a31fa-115">To create a Blazor app using the client-side hosting model, use the **Blazor WebAssembly App** template ([dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)).</span></span>

<span data-ttu-id="a31fa-116">Nachdem Sie die Vorlage **Blazor Webassembly-App** ausgewählt haben, haben Sie die Möglichkeit, die APP für die Verwendung eines ASP.net Core-Back-Ends zu konfigurieren, indem Sie das Kontrollkästchen **ASP.net Core gehostet** aktivieren ([dotnet New blazorwasm--Hosted](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="a31fa-116">After selecting the **Blazor WebAssembly App** template, you have the option of configuring the app to use an ASP.NET Core backend by selecting the **ASP.NET Core hosted** check box ([dotnet new blazorwasm --hosted](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="a31fa-117">Die ASP.net Core-App dient der Blazor-App für Clients.</span><span class="sxs-lookup"><span data-stu-id="a31fa-117">The ASP.NET Core app serves the Blazor app to clients.</span></span> <span data-ttu-id="a31fa-118">Die Blazor Webassembly-App kann mithilfe von Web-API-aufrufen oder [SignalR](xref:signalr/introduction)mit dem Server über das Netzwerk interagieren.</span><span class="sxs-lookup"><span data-stu-id="a31fa-118">The Blazor WebAssembly app can interact with the server over the network using web API calls or [SignalR](xref:signalr/introduction).</span></span>

<span data-ttu-id="a31fa-119">Die Vorlagen enthalten das `blazor.webassembly.js` Skript, das Folgendes behandelt:</span><span class="sxs-lookup"><span data-stu-id="a31fa-119">The templates include the `blazor.webassembly.js` script that handles:</span></span>

* <span data-ttu-id="a31fa-120">Herunterladen der .NET-Laufzeit, der APP und der APP-Abhängigkeiten.</span><span class="sxs-lookup"><span data-stu-id="a31fa-120">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="a31fa-121">Initialisierung der Laufzeit zum Ausführen der app.</span><span class="sxs-lookup"><span data-stu-id="a31fa-121">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="a31fa-122">Das Blazor Webassembly-Hostingmodell bietet mehrere Vorteile:</span><span class="sxs-lookup"><span data-stu-id="a31fa-122">The Blazor WebAssembly hosting model offers several benefits:</span></span>

* <span data-ttu-id="a31fa-123">Es gibt keine serverseitige .net-Abhängigkeit.</span><span class="sxs-lookup"><span data-stu-id="a31fa-123">There's no .NET server-side dependency.</span></span> <span data-ttu-id="a31fa-124">Die APP funktioniert nach dem Herunterladen auf den Client vollständig.</span><span class="sxs-lookup"><span data-stu-id="a31fa-124">The app is fully functioning after downloaded to the client.</span></span>
* <span data-ttu-id="a31fa-125">Client Ressourcen und-Funktionen sind vollständig ausgelastet.</span><span class="sxs-lookup"><span data-stu-id="a31fa-125">Client resources and capabilities are fully leveraged.</span></span>
* <span data-ttu-id="a31fa-126">Die Arbeit wird vom Server an den Client verlagert.</span><span class="sxs-lookup"><span data-stu-id="a31fa-126">Work is offloaded from the server to the client.</span></span>
* <span data-ttu-id="a31fa-127">Ein ASP.net Core Webserver ist nicht erforderlich, um die APP zu hosten.</span><span class="sxs-lookup"><span data-stu-id="a31fa-127">An ASP.NET Core web server isn't required to host the app.</span></span> <span data-ttu-id="a31fa-128">Server lose Bereitstellungs Szenarien sind möglich (z. b. das Bereitstellen der APP aus einem CDN).</span><span class="sxs-lookup"><span data-stu-id="a31fa-128">Serverless deployment scenarios are possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="a31fa-129">Es gibt Nachteile für Blazor Webassembly-Hosting:</span><span class="sxs-lookup"><span data-stu-id="a31fa-129">There are downsides to Blazor WebAssembly hosting:</span></span>

* <span data-ttu-id="a31fa-130">Die APP ist auf die Funktionen des Browsers beschränkt.</span><span class="sxs-lookup"><span data-stu-id="a31fa-130">The app is restricted to the capabilities of the browser.</span></span>
* <span data-ttu-id="a31fa-131">Kompatible Client Hardware und Software (z. b. webassemblyunterstützung) ist erforderlich.</span><span class="sxs-lookup"><span data-stu-id="a31fa-131">Capable client hardware and software (for example, WebAssembly support) is required.</span></span>
* <span data-ttu-id="a31fa-132">Die Download Größe ist größer, und die Auslastung von apps dauert länger.</span><span class="sxs-lookup"><span data-stu-id="a31fa-132">Download size is larger, and apps take longer to load.</span></span>
* <span data-ttu-id="a31fa-133">Die Unterstützung von .NET-Laufzeit und-Tools ist weniger ausgereift.</span><span class="sxs-lookup"><span data-stu-id="a31fa-133">.NET runtime and tooling support is less mature.</span></span> <span data-ttu-id="a31fa-134">Beispielsweise gibt es Einschränkungen in [.NET Standard](/dotnet/standard/net-standard) Unterstützung und Debuggen.</span><span class="sxs-lookup"><span data-stu-id="a31fa-134">For example, limitations exist in [.NET Standard](/dotnet/standard/net-standard) support and debugging.</span></span>

## <a name="opno-locblazor-server"></a>Blazor<span data-ttu-id="a31fa-135"> Server</span><span class="sxs-lookup"><span data-stu-id="a31fa-135"> Server</span></span>

<span data-ttu-id="a31fa-136">Mit dem Blazor Server-Hostingmodell wird die APP auf dem Server innerhalb einer ASP.net Core-app ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="a31fa-136">With the Blazor Server hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="a31fa-137">Benutzeroberflächenupdates, Ereignisbehandlung und JavaScript-Aufrufe werden über eine [SignalR](xref:signalr/introduction)-Verbindung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="a31fa-137">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![Der Browser interagiert mit der APP (gehostet innerhalb einer ASP.net Core-APP) auf dem Server über einen [! Schel. No-Loc (signalr)]-Verbindung.](hosting-models/_static/blazor-server.png)

<span data-ttu-id="a31fa-139">Verwenden Sie zum Erstellen einer Blazor-App mithilfe des Blazor Server-Hostingmodells die ASP.net Core **Blazor Server-App** -Vorlage ([dotnet New blazorserver](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="a31fa-139">To create a Blazor app using the Blazor Server hosting model, use the ASP.NET Core **Blazor Server App** template ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="a31fa-140">Die ASP.net Core-App hostet die Blazor Server-APP und erstellt den SignalR Endpunkt, auf dem Clients eine Verbindung herstellen.</span><span class="sxs-lookup"><span data-stu-id="a31fa-140">The ASP.NET Core app hosts the Blazor Server app and creates the SignalR endpoint where clients connect.</span></span>

<span data-ttu-id="a31fa-141">Die ASP.net Core-App verweist auf die `Startup` Klasse der APP, um Folgendes hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="a31fa-141">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="a31fa-142">Server seitige Dienste.</span><span class="sxs-lookup"><span data-stu-id="a31fa-142">Server-side services.</span></span>
* <span data-ttu-id="a31fa-143">Die APP für die Pipeline für die Anforderungs Verarbeitung.</span><span class="sxs-lookup"><span data-stu-id="a31fa-143">The app to the request handling pipeline.</span></span>

<span data-ttu-id="a31fa-144">Mit dem `blazor.server.js` Skript&dagger; wird die Client Verbindung hergestellt.</span><span class="sxs-lookup"><span data-stu-id="a31fa-144">The `blazor.server.js` script&dagger; establishes the client connection.</span></span> <span data-ttu-id="a31fa-145">Es liegt in der Verantwortung der APP, den App-Zustand nach Bedarf beizubehalten und wiederherzustellen (z. b. im Fall einer verlorenen Netzwerkverbindung).</span><span class="sxs-lookup"><span data-stu-id="a31fa-145">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="a31fa-146">Das Blazor Server-Hostingmodell bietet mehrere Vorteile:</span><span class="sxs-lookup"><span data-stu-id="a31fa-146">The Blazor Server hosting model offers several benefits:</span></span>

* <span data-ttu-id="a31fa-147">Die Download Größe ist erheblich kleiner als eine Blazor Webassembly-APP, und die APP lädt viel schneller.</span><span class="sxs-lookup"><span data-stu-id="a31fa-147">Download size is significantly smaller than a Blazor WebAssembly app, and the app loads much faster.</span></span>
* <span data-ttu-id="a31fa-148">Die App nutzt vollständige Vorteile der Serverfunktionen, einschließlich der Verwendung von .net Core-kompatiblen APIs.</span><span class="sxs-lookup"><span data-stu-id="a31fa-148">The app takes full advantage of server capabilities, including use of any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="a31fa-149">.Net Core auf dem Server wird verwendet, um die APP auszuführen, sodass vorhandene .NET-Tools, wie z. b. das Debuggen, erwartungsgemäß funktionieren.</span><span class="sxs-lookup"><span data-stu-id="a31fa-149">.NET Core on the server is used to run the app, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="a31fa-150">Thin Clients werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="a31fa-150">Thin clients are supported.</span></span> <span data-ttu-id="a31fa-151">Blazor Server-apps funktionieren z. b. mit Browsern, die Webassembly nicht unterstützen, und auf Geräten, die auf Ressourcen beschränkt sind.</span><span class="sxs-lookup"><span data-stu-id="a31fa-151">For example, Blazor Server apps work with browsers that don't support WebAssembly and on resource-constrained devices.</span></span>
* <span data-ttu-id="a31fa-152">Die .net-C# /Codebasis der APP, einschließlich des Komponenten Codes der APP, wird nicht für Clients bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="a31fa-152">The app's .NET/C# code base, including the app's component code, isn't served to clients.</span></span>

<span data-ttu-id="a31fa-153">Es gibt Nachteile für Blazor Server Hosting:</span><span class="sxs-lookup"><span data-stu-id="a31fa-153">There are downsides to Blazor Server hosting:</span></span>

* <span data-ttu-id="a31fa-154">Eine höhere Latenz ist normalerweise vorhanden.</span><span class="sxs-lookup"><span data-stu-id="a31fa-154">Higher latency usually exists.</span></span> <span data-ttu-id="a31fa-155">Jede Benutzerinteraktion umfasst einen Netzwerkhop.</span><span class="sxs-lookup"><span data-stu-id="a31fa-155">Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="a31fa-156">Es gibt keine Offline Unterstützung.</span><span class="sxs-lookup"><span data-stu-id="a31fa-156">There's no offline support.</span></span> <span data-ttu-id="a31fa-157">Wenn die Client Verbindung fehlschlägt, funktioniert die APP nicht mehr.</span><span class="sxs-lookup"><span data-stu-id="a31fa-157">If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="a31fa-158">Skalierbarkeit ist eine Herausforderung für apps mit vielen Benutzern.</span><span class="sxs-lookup"><span data-stu-id="a31fa-158">Scalability is challenging for apps with many users.</span></span> <span data-ttu-id="a31fa-159">Der Server muss mehrere Clientverbindungen verwalten und den Client Status verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="a31fa-159">The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="a31fa-160">Ein ASP.net Core Server ist erforderlich, um der APP zu dienen.</span><span class="sxs-lookup"><span data-stu-id="a31fa-160">An ASP.NET Core server is required to serve the app.</span></span> <span data-ttu-id="a31fa-161">Server lose Bereitstellungs Szenarien sind nicht möglich (z. b. für die APP aus einem CDN).</span><span class="sxs-lookup"><span data-stu-id="a31fa-161">Serverless deployment scenarios aren't possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="a31fa-162">&dagger;das `blazor.server.js` Skript von einer eingebetteten Ressource im ASP.net Core freigegebenen Framework bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="a31fa-162">&dagger;The `blazor.server.js` script is served from an embedded resource in the ASP.NET Core shared framework.</span></span>

### <a name="comparison-to-server-rendered-ui"></a><span data-ttu-id="a31fa-163">Vergleich mit der Server gerenderten Benutzeroberfläche</span><span class="sxs-lookup"><span data-stu-id="a31fa-163">Comparison to server-rendered UI</span></span>

<span data-ttu-id="a31fa-164">Eine Möglichkeit, Blazor Server-apps zu verstehen, besteht darin, zu verstehen, wie Sie sich von herkömmlichen Modellen zum Rendern von Benutzeroberflächen in ASP.net Core Apps mithilfe von Razor-Ansichten oder Razor Pages</span><span class="sxs-lookup"><span data-stu-id="a31fa-164">One way to understand Blazor Server apps is to understand how it differs from traditional models for rendering UI in ASP.NET Core apps using Razor views or Razor Pages.</span></span> <span data-ttu-id="a31fa-165">Beide Modelle verwenden die Razor-Sprache, um HTML-Inhalte zu beschreiben. Sie unterscheiden sich jedoch deutlich von der Darstellung von Markup.</span><span class="sxs-lookup"><span data-stu-id="a31fa-165">Both models use the Razor language to describe HTML content, but they significantly differ in how markup is rendered.</span></span>

<span data-ttu-id="a31fa-166">Wenn eine Razor Page oder eine Ansicht gerendert wird, gibt jede Zeile von Razor-Code HTML-Code in Textform aus.</span><span class="sxs-lookup"><span data-stu-id="a31fa-166">When a Razor Page or view is rendered, every line of Razor code emits HTML in text form.</span></span> <span data-ttu-id="a31fa-167">Nach dem Rendering verwirft der Server die Seiten-oder Sicht Instanz, einschließlich eines beliebigen Zustands, der erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="a31fa-167">After rendering, the server disposes of the page or view instance, including any state that was produced.</span></span> <span data-ttu-id="a31fa-168">Wenn eine andere Anforderung für die Seite auftritt, z. b. wenn die Server Validierung fehlschlägt und die Validierungs Zusammenfassung angezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="a31fa-168">When another request for the page occurs, for instance when server validation fails and the validation summary is displayed:</span></span>

* <span data-ttu-id="a31fa-169">Die gesamte Seite wird erneut in HTML-Text umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="a31fa-169">The entire page is rerendered to HTML text again.</span></span>
* <span data-ttu-id="a31fa-170">Die Seite wird an den Client gesendet.</span><span class="sxs-lookup"><span data-stu-id="a31fa-170">The page is sent to the client.</span></span>

<span data-ttu-id="a31fa-171">Eine Blazor-App besteht aus wiederverwendbaren Elementen der UI, die als *Komponenten*bezeichnet werden.</span><span class="sxs-lookup"><span data-stu-id="a31fa-171">A Blazor app is composed of reusable elements of UI called *components*.</span></span> <span data-ttu-id="a31fa-172">Eine Komponente enthält C# Code, Markup und andere Komponenten.</span><span class="sxs-lookup"><span data-stu-id="a31fa-172">A component contains C# code, markup, and other components.</span></span> <span data-ttu-id="a31fa-173">Wenn eine Komponente gerendert wird, erstellt Blazor ein Diagramm der enthaltenen Komponenten, ähnlich wie HTML-oder XML-Dokumentobjektmodell (DOM).</span><span class="sxs-lookup"><span data-stu-id="a31fa-173">When a component is rendered, Blazor produces a graph of the included components similar to an HTML or XML Document Object Model (DOM).</span></span> <span data-ttu-id="a31fa-174">Dieses Diagramm enthält den Komponenten Zustand in Eigenschaften und Feldern.</span><span class="sxs-lookup"><span data-stu-id="a31fa-174">This graph includes component state held in properties and fields.</span></span> Blazor<span data-ttu-id="a31fa-175"> wertet das Komponenten Diagramm aus, sodass eine binäre Darstellung des Markups erzeugt wird.</span><span class="sxs-lookup"><span data-stu-id="a31fa-175"> evaluates the component graph to produce a binary representation of the markup.</span></span> <span data-ttu-id="a31fa-176">Das binäre Format kann wie folgt lauten:</span><span class="sxs-lookup"><span data-stu-id="a31fa-176">The binary format can be:</span></span>

* <span data-ttu-id="a31fa-177">Wird in HTML-Text (während der vorab Generierung) umgewandelt.</span><span class="sxs-lookup"><span data-stu-id="a31fa-177">Turned into HTML text (during prerendering).</span></span>
* <span data-ttu-id="a31fa-178">Wird verwendet, um das Markup beim regulären Rendering effizient zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="a31fa-178">Used to efficiently update the markup during regular rendering.</span></span>

<span data-ttu-id="a31fa-179">Ein Benutzeroberflächen Update in Blazor wird durch Folgendes ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="a31fa-179">A UI update in Blazor is triggered by:</span></span>

* <span data-ttu-id="a31fa-180">Benutzerinteraktion, z. b. das Auswählen einer Schaltfläche.</span><span class="sxs-lookup"><span data-stu-id="a31fa-180">User interaction, such as selecting a button.</span></span>
* <span data-ttu-id="a31fa-181">App-Trigger, z. b. ein Timer.</span><span class="sxs-lookup"><span data-stu-id="a31fa-181">App triggers, such as a timer.</span></span>

<span data-ttu-id="a31fa-182">Das Diagramm wird erneut ausgeführt, und es wird ein UI- *diff* (Differenz) berechnet.</span><span class="sxs-lookup"><span data-stu-id="a31fa-182">The graph is rerendered, and a UI *diff* (difference) is calculated.</span></span> <span data-ttu-id="a31fa-183">Dieser Unterschied ist der kleinste Satz an DOM-Änderungen, die erforderlich sind, um die Benutzeroberfläche auf dem Client zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="a31fa-183">This diff is the smallest set of DOM edits required to update the UI on the client.</span></span> <span data-ttu-id="a31fa-184">Der diff wird in einem binären Format an den Client gesendet und vom Browser angewendet.</span><span class="sxs-lookup"><span data-stu-id="a31fa-184">The diff is sent to the client in a binary format and applied by the browser.</span></span>

<span data-ttu-id="a31fa-185">Eine Komponente wird verworfen, nachdem der Benutzer auf dem Client dorthin navigiert ist.</span><span class="sxs-lookup"><span data-stu-id="a31fa-185">A component is disposed after the user navigates away from it on the client.</span></span> <span data-ttu-id="a31fa-186">Während ein Benutzer mit einer Komponente interagiert, muss der Zustand der Komponente (Dienste, Ressourcen) im Arbeitsspeicher des Servers gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="a31fa-186">While a user is interacting with a component, the component's state (services, resources) must be held in the server's memory.</span></span> <span data-ttu-id="a31fa-187">Da der Status vieler Komponenten möglicherweise gleichzeitig vom Server verwaltet wird, ist die Arbeitsspeicher Erschöpfung ein Problem, das behoben werden muss.</span><span class="sxs-lookup"><span data-stu-id="a31fa-187">Because the state of many components might be maintained by the server concurrently, memory exhaustion is a concern that must be addressed.</span></span> <span data-ttu-id="a31fa-188">Anleitungen zum Erstellen einer Blazor Server-APP, um die optimale Verwendung des Server Arbeitsspeichers sicherzustellen, finden Sie unter <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="a31fa-188">For guidance on how to author a Blazor Server app to ensure the best use of server memory, see <xref:security/blazor/server>.</span></span>

### <a name="integrate-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="a31fa-189">Integrieren von Razor-Komponenten in Razor Pages und MVC-apps</span><span class="sxs-lookup"><span data-stu-id="a31fa-189">Integrate Razor components into Razor Pages and MVC apps</span></span>

#### <a name="use-components-in-pages-and-views"></a><span data-ttu-id="a31fa-190">Verwenden von Komponenten in Seiten und Ansichten</span><span class="sxs-lookup"><span data-stu-id="a31fa-190">Use components in pages and views</span></span>

<span data-ttu-id="a31fa-191">Eine vorhandene Razor Pages-oder MVC-App kann Razor-Komponenten in Seiten und Ansichten integrieren:</span><span class="sxs-lookup"><span data-stu-id="a31fa-191">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="a31fa-192">In der Layoutdatei der APP ( *_Layout. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="a31fa-192">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="a31fa-193">Fügen Sie dem `<head>`-Element das folgende `<base>`-Tag hinzu:</span><span class="sxs-lookup"><span data-stu-id="a31fa-193">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="a31fa-194">Beim `href` Wert (dem *App-Basispfad*) im vorhergehenden Beispiel wird davon ausgegangen, dass sich die APP im Stamm-URL-Pfad (`/`) befindet.</span><span class="sxs-lookup"><span data-stu-id="a31fa-194">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="a31fa-195">Wenn es sich bei der App um eine untergeordnete Anwendung handelt, befolgen Sie die Anweisungen im Abschnitt *App-Basispfad* des <xref:host-and-deploy/blazor/index#app-base-path> Artikels.</span><span class="sxs-lookup"><span data-stu-id="a31fa-195">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="a31fa-196">Die Datei " *_Layout. cshtml* " befindet sich im Ordner " *pages/Shared* " in einer Razor Pages-APP oder in *Sichten/freigegebenen* Ordnern in einer MVC-app.</span><span class="sxs-lookup"><span data-stu-id="a31fa-196">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="a31fa-197">Fügen Sie ein `<script>`-Tag für das Skript " *blazor. Server. js* " innerhalb des schließenden `</body>`-Tags hinzu:</span><span class="sxs-lookup"><span data-stu-id="a31fa-197">Add a `<script>` tag for the *blazor.server.js* script inside of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="a31fa-198">Das-Framework fügt der APP das Skript " *blazor. Server. js* " hinzu.</span><span class="sxs-lookup"><span data-stu-id="a31fa-198">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="a31fa-199">Es ist nicht erforderlich, das Skript manuell der APP hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="a31fa-199">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="a31fa-200">Fügen Sie dem Stamm Ordner des Projekts eine *_Imports. Razor* -Datei mit folgendem Inhalt hinzu (ändern Sie den letzten Namespace, `MyAppNamespace`, in den Namespace der APP):</span><span class="sxs-lookup"><span data-stu-id="a31fa-200">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

   ```csharp
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. <span data-ttu-id="a31fa-201">Fügen Sie in `Startup.ConfigureServices`den Blazor Server-Dienst hinzu:</span><span class="sxs-lookup"><span data-stu-id="a31fa-201">In `Startup.ConfigureServices`, add the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="a31fa-202">Fügen Sie in `Startup.Configure`den Blazor Hub-Endpunkt `app.UseEndpoints`hinzu:</span><span class="sxs-lookup"><span data-stu-id="a31fa-202">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="a31fa-203">Integrieren von Komponenten in beliebige Seiten oder Ansichten.</span><span class="sxs-lookup"><span data-stu-id="a31fa-203">Integrate components into any page or view.</span></span> <span data-ttu-id="a31fa-204">Weitere Informationen finden Sie im Abschnitt *integrieren von Komponenten in Razor Pages und MVC-apps* des <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps> Artikels.</span><span class="sxs-lookup"><span data-stu-id="a31fa-204">For more information, see the *Integrate components into Razor Pages and MVC apps* section of the <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps> article.</span></span>

#### <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="a31fa-205">Verwenden von Routing fähigen Komponenten in einer Razor Pages-App</span><span class="sxs-lookup"><span data-stu-id="a31fa-205">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="a31fa-206">So unterstützen Sie Routing fähige Razor-Komponenten in Razor Pages-apps:</span><span class="sxs-lookup"><span data-stu-id="a31fa-206">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="a31fa-207">Befolgen Sie die Anweisungen im Abschnitt [Verwenden von Komponenten in Seiten und Ansichten](#use-components-in-pages-and-views) .</span><span class="sxs-lookup"><span data-stu-id="a31fa-207">Follow the guidance in the [Use components in pages and views](#use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="a31fa-208">Fügen Sie die Datei " *app. Razor* " dem Stamm des Projekts mit folgendem Inhalt hinzu:</span><span class="sxs-lookup"><span data-stu-id="a31fa-208">Add an *App.razor* file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="a31fa-209">Fügen Sie dem Ordner *pages* eine *_Host. cshtml* -Datei mit folgendem Inhalt hinzu:</span><span class="sxs-lookup"><span data-stu-id="a31fa-209">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="a31fa-210">Komponenten verwenden die freigegebene *_Layout. cshtml* -Datei für das Layout.</span><span class="sxs-lookup"><span data-stu-id="a31fa-210">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="a31fa-211">Fügen Sie eine Route mit niedriger Priorität für die *_Host. cshtml* -Seite zur Endpunkt Konfiguration in `Startup.Configure`hinzu:</span><span class="sxs-lookup"><span data-stu-id="a31fa-211">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="a31fa-212">Fügen Sie der APP Routing fähige Komponenten hinzu.</span><span class="sxs-lookup"><span data-stu-id="a31fa-212">Add routable components to the app.</span></span> <span data-ttu-id="a31fa-213">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a31fa-213">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="a31fa-214">Wenn Sie einen benutzerdefinierten Ordner zum Speichern der APP-Komponenten verwenden, fügen Sie den Namespace, der den Ordner darstellt, der Datei *pages/_ViewImports. cshtml* hinzu.</span><span class="sxs-lookup"><span data-stu-id="a31fa-214">When using a custom folder to hold the app's components, add the namespace representing the folder to the *Pages/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="a31fa-215">Weitere Informationen finden Sie unter <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span><span class="sxs-lookup"><span data-stu-id="a31fa-215">For more information, see <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span></span>

#### <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="a31fa-216">Verwenden von Routing fähigen Komponenten in einer MVC-App</span><span class="sxs-lookup"><span data-stu-id="a31fa-216">Use routable components in an MVC app</span></span>

<span data-ttu-id="a31fa-217">So unterstützen Sie Routing fähige Razor-Komponenten in MVC-apps:</span><span class="sxs-lookup"><span data-stu-id="a31fa-217">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="a31fa-218">Befolgen Sie die Anweisungen im Abschnitt [Verwenden von Komponenten in Seiten und Ansichten](#use-components-in-pages-and-views) .</span><span class="sxs-lookup"><span data-stu-id="a31fa-218">Follow the guidance in the [Use components in pages and views](#use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="a31fa-219">Fügen Sie die Datei " *app. Razor* " dem Stamm des Projekts mit folgendem Inhalt hinzu:</span><span class="sxs-lookup"><span data-stu-id="a31fa-219">Add an *App.razor* file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="a31fa-220">Fügen Sie dem Ordner " *views/Home* " eine *_Host. cshtml* -Datei mit folgendem Inhalt hinzu:</span><span class="sxs-lookup"><span data-stu-id="a31fa-220">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="a31fa-221">Komponenten verwenden die freigegebene *_Layout. cshtml* -Datei für das Layout.</span><span class="sxs-lookup"><span data-stu-id="a31fa-221">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="a31fa-222">Fügen Sie dem Home-Controller eine Aktion hinzu:</span><span class="sxs-lookup"><span data-stu-id="a31fa-222">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="a31fa-223">Fügen Sie eine Route mit niedriger Priorität für die Controller Aktion hinzu, die die Ansicht *_Host. cshtml* an die Endpunkt Konfiguration in `Startup.Configure`zurückgibt:</span><span class="sxs-lookup"><span data-stu-id="a31fa-223">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="a31fa-224">Erstellen Sie einen Ordner *pages* , und fügen Sie der APP Routing fähige Komponenten hinzu.</span><span class="sxs-lookup"><span data-stu-id="a31fa-224">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="a31fa-225">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a31fa-225">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="a31fa-226">Wenn Sie einen benutzerdefinierten Ordner zum Speichern der APP-Komponenten verwenden, fügen Sie den Namespace, der den Ordner darstellt, der *views/_ViewImports. cshtml-* Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="a31fa-226">When using a custom folder to hold the app's components, add the namespace representing the folder to the *Views/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="a31fa-227">Weitere Informationen finden Sie unter <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span><span class="sxs-lookup"><span data-stu-id="a31fa-227">For more information, see <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span></span>

### <a name="circuits"></a><span data-ttu-id="a31fa-228">Fen</span><span class="sxs-lookup"><span data-stu-id="a31fa-228">Circuits</span></span>

<span data-ttu-id="a31fa-229">Eine Blazor Server-App basiert auf [ASP.net Core SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="a31fa-229">A Blazor Server app is built on top of [ASP.NET Core SignalR](xref:signalr/introduction).</span></span> <span data-ttu-id="a31fa-230">Jeder Client kommuniziert über *mindestens eine SignalR* Verbindung, die als Verbindung bezeichnet wird, mit dem Server.</span><span class="sxs-lookup"><span data-stu-id="a31fa-230">Each client communicates to the server over one or more SignalR connections called a *circuit*.</span></span> <span data-ttu-id="a31fa-231">Eine Verbindung ist BlazorAbstraktion über SignalR Verbindungen, die temporäre Netzwerkunterbrechungen tolerieren können.</span><span class="sxs-lookup"><span data-stu-id="a31fa-231">A circuit is Blazor's abstraction over SignalR connections that can tolerate temporary network interruptions.</span></span> <span data-ttu-id="a31fa-232">Wenn ein Blazor Client feststellt, dass die Verbindung mit dem SignalR getrennt ist, versucht er, eine Verbindung mit dem Server herzustellen, indem eine neue SignalR Verbindung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="a31fa-232">When a Blazor client sees that the SignalR connection is disconnected, it attempts to reconnect to the server using a new SignalR connection.</span></span>

<span data-ttu-id="a31fa-233">Jeder Browser Bildschirm (Browser Registerkarte oder IFRAME), der mit einer Blazor Server-App verbunden ist, verwendet eine SignalR Verbindung.</span><span class="sxs-lookup"><span data-stu-id="a31fa-233">Each browser screen (browser tab or iframe) that is connected to a Blazor Server app uses a SignalR connection.</span></span> <span data-ttu-id="a31fa-234">Dies ist jedoch ein weiterer wichtiger Unterschied im Vergleich zu typischen, von Servern gerenderten apps.</span><span class="sxs-lookup"><span data-stu-id="a31fa-234">This is yet another important distinction compared to typical server-rendered apps.</span></span> <span data-ttu-id="a31fa-235">In einer Server gerenderten APP wird das Öffnen derselben app in mehreren Browser Bildschirmen in der Regel nicht zu zusätzlichen Ressourcenanforderungen auf dem Server übertragen.</span><span class="sxs-lookup"><span data-stu-id="a31fa-235">In a server-rendered app, opening the same app in multiple browser screens typically doesn't translate into additional resource demands on the server.</span></span> <span data-ttu-id="a31fa-236">In einer Blazor Server-App erfordert jeder Browser Bildschirm eine separate Verbindung und separate Instanzen des Komponenten Zustands, die vom Server verwaltet werden.</span><span class="sxs-lookup"><span data-stu-id="a31fa-236">In a Blazor Server app, each browser screen requires a separate circuit and separate instances of component state to be managed by the server.</span></span>

Blazor<span data-ttu-id="a31fa-237"> das Schließen einer Browser Registerkarte oder navigieren zu einer externen URL zu einer Ordnungs *gemäßen Beendigung.*</span><span class="sxs-lookup"><span data-stu-id="a31fa-237"> considers closing a browser tab or navigating to an external URL a *graceful* termination.</span></span> <span data-ttu-id="a31fa-238">Im Fall einer ordnungsgemäßen Beendigung werden die Verbindung und zugehörige Ressourcen sofort freigegeben.</span><span class="sxs-lookup"><span data-stu-id="a31fa-238">In the event of a graceful termination, the circuit and associated resources are immediately released.</span></span> <span data-ttu-id="a31fa-239">Ein Client kann möglicherweise auch nicht ordnungsgemäß getrennt werden, beispielsweise aufgrund einer Netzwerk Unterbrechung.</span><span class="sxs-lookup"><span data-stu-id="a31fa-239">A client may also disconnect non-gracefully, for instance due to a network interruption.</span></span> Blazor<span data-ttu-id="a31fa-240"> Server speichert getrennte Verbindungen für ein konfigurierbares Intervall, damit der Client die Verbindung wiederherstellen kann.</span><span class="sxs-lookup"><span data-stu-id="a31fa-240"> Server stores disconnected circuits for a configurable interval to allow the client to reconnect.</span></span> <span data-ttu-id="a31fa-241">Weitere Informationen finden Sie im Abschnitt [Wiederherstellen der Verbindung mit dem gleichen Server](#reconnection-to-the-same-server) .</span><span class="sxs-lookup"><span data-stu-id="a31fa-241">For more information, see the [Reconnection to the same server](#reconnection-to-the-same-server) section.</span></span>

### <a name="ui-latency"></a><span data-ttu-id="a31fa-242">UI-Latenz</span><span class="sxs-lookup"><span data-stu-id="a31fa-242">UI Latency</span></span>

<span data-ttu-id="a31fa-243">Die Benutzeroberflächen Latenz ist die Zeit, die Sie von einer initiierten Aktion bis zum Zeitpunkt der Aktualisierung der Benutzeroberfläche benötigt.</span><span class="sxs-lookup"><span data-stu-id="a31fa-243">UI latency is the time it takes from an initiated action to the time the UI is updated.</span></span> <span data-ttu-id="a31fa-244">Kleinere Werte für die Benutzeroberflächen Latenz sind zwingend erforderlich, damit eine APP für einen Benutzer reaktionsfähig ist.</span><span class="sxs-lookup"><span data-stu-id="a31fa-244">Smaller values for UI latency are imperative for an app to feel responsive to a user.</span></span> <span data-ttu-id="a31fa-245">In einer Blazor Server-APP wird jede Aktion an den Server gesendet, verarbeitet und ein UI-diff zurückgesendet.</span><span class="sxs-lookup"><span data-stu-id="a31fa-245">In a Blazor Server app, each action is sent to the server, processed, and a UI diff is sent back.</span></span> <span data-ttu-id="a31fa-246">Folglich ist die Benutzeroberflächen Latenz die Summe der Netzwerk Latenz und der Server Latenz bei der Verarbeitung der Aktion.</span><span class="sxs-lookup"><span data-stu-id="a31fa-246">Consequently, UI latency is the sum of network latency and the server latency in processing the action.</span></span>

<span data-ttu-id="a31fa-247">Für eine Branchen-APP, die auf ein privates Unternehmensnetzwerk beschränkt ist, sind die Auswirkungen auf die Benutzer Wahrnehmung von Latenzzeit aufgrund der Netzwerk Latenz in der Regel nicht wahrnehmbar.</span><span class="sxs-lookup"><span data-stu-id="a31fa-247">For a line of business app that's limited to a private corporate network, the effect on user perceptions of latency due to network latency are usually imperceptible.</span></span> <span data-ttu-id="a31fa-248">Für eine APP, die über das Internet bereitgestellt wird, kann die Latenz für Benutzer bemerkbar werden, insbesondere, wenn die Benutzer weitgehend geografisch verteilt sind.</span><span class="sxs-lookup"><span data-stu-id="a31fa-248">For an app deployed over the Internet, latency may become noticeable to users, particularly if users are widely distributed geographically.</span></span>

<span data-ttu-id="a31fa-249">Die Speicherauslastung kann auch zur APP-Latenz beitragen.</span><span class="sxs-lookup"><span data-stu-id="a31fa-249">Memory usage can also contribute to app latency.</span></span> <span data-ttu-id="a31fa-250">Eine erhöhte Arbeitsspeicher Auslastung führt zu häufigen Garbage Collection-oder Paging-Speicher auf den Datenträger, die beide die Leistung der APP beeinträchtigen und somit die Benutzeroberflächen Latenz erhöhen.</span><span class="sxs-lookup"><span data-stu-id="a31fa-250">Increased memory usage results in frequent garbage collection or paging memory to disk, both of which degrade app performance and consequently increase UI latency.</span></span> <span data-ttu-id="a31fa-251">Weitere Informationen finden Sie unter <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="a31fa-251">For more information, see <xref:security/blazor/server>.</span></span>

Blazor<span data-ttu-id="a31fa-252"> Server-apps sollten optimiert werden, um die Benutzeroberflächen Latenz zu minimieren, indem Netzwerk Latenz und Speicherauslastung reduziert werden</span><span class="sxs-lookup"><span data-stu-id="a31fa-252"> Server apps should be optimized to minimize UI latency by reducing network latency and memory usage.</span></span> <span data-ttu-id="a31fa-253">Einen Ansatz zum Messen der Netzwerk Latenz finden Sie unter <xref:host-and-deploy/blazor/server#measure-network-latency>.</span><span class="sxs-lookup"><span data-stu-id="a31fa-253">For an approach to measuring network latency, see <xref:host-and-deploy/blazor/server#measure-network-latency>.</span></span> <span data-ttu-id="a31fa-254">Weitere Informationen zu SignalR und Blazorfinden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="a31fa-254">For more information on SignalR and Blazor, see:</span></span>

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="connection-to-the-server"></a><span data-ttu-id="a31fa-255">Verbindung mit dem Server</span><span class="sxs-lookup"><span data-stu-id="a31fa-255">Connection to the server</span></span>

Blazor<span data-ttu-id="a31fa-256"> Server-apps benötigen eine aktive SignalR Verbindung mit dem Server.</span><span class="sxs-lookup"><span data-stu-id="a31fa-256"> Server apps require an active SignalR connection to the server.</span></span> <span data-ttu-id="a31fa-257">Wenn die Verbindung unterbrochen wird, versucht die APP, erneut eine Verbindung mit dem Server herzustellen.</span><span class="sxs-lookup"><span data-stu-id="a31fa-257">If the connection is lost, the app attempts to reconnect to the server.</span></span> <span data-ttu-id="a31fa-258">Solange sich der Status des Clients weiterhin im Arbeitsspeicher befindet, wird die Client Sitzung fortgesetzt, ohne den Zustand zu verlieren.</span><span class="sxs-lookup"><span data-stu-id="a31fa-258">As long as the client's state is still in memory, the client session resumes without losing state.</span></span>

#### <a name="reconnection-to-the-same-server"></a><span data-ttu-id="a31fa-259">Erneute Verbindung mit demselben Server</span><span class="sxs-lookup"><span data-stu-id="a31fa-259">Reconnection to the same server</span></span>

<span data-ttu-id="a31fa-260">Eine Blazor Server-App als Antwort auf die erste Client Anforderung, die den UI-Status auf dem Server festlegt.</span><span class="sxs-lookup"><span data-stu-id="a31fa-260">A Blazor Server app prerenders in response to the first client request, which sets up the UI state on the server.</span></span> <span data-ttu-id="a31fa-261">Wenn der Client versucht, eine SignalR Verbindung zu erstellen, muss der Client erneut eine Verbindung mit dem gleichen Server herstellen.</span><span class="sxs-lookup"><span data-stu-id="a31fa-261">When the client attempts to create a SignalR connection, the client must reconnect to the same server.</span></span> Blazor<span data-ttu-id="a31fa-262"> Server-apps, die mehr als einen Back-End-Server verwenden, sollten persistente *Sitzungen* für SignalR Verbindungen implementieren.</span><span class="sxs-lookup"><span data-stu-id="a31fa-262"> Server apps that use more than one backend server should implement *sticky sessions* for SignalR connections.</span></span>

<span data-ttu-id="a31fa-263">Sie sollten [Azure SignalR Service](/azure/azure-signalr) für Blazor Server-Apps verwenden.</span><span class="sxs-lookup"><span data-stu-id="a31fa-263">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="a31fa-264">Der Dienst ermöglicht das zentrale Hochskalieren einer Blazor Server-App auf eine große Anzahl gleichzeitiger SignalR-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="a31fa-264">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="a31fa-265">Persistente Sitzungen werden für den Azure SignalR-Dienst aktiviert, indem die `ServerStickyMode` Option oder der Konfigurations Wert für den Dienst auf `Required`festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="a31fa-265">Sticky sessions are enabled for the Azure SignalR Service by setting the service's `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="a31fa-266">Weitere Informationen finden Sie unter <xref:host-and-deploy/blazor/server#signalr-configuration>.</span><span class="sxs-lookup"><span data-stu-id="a31fa-266">For more information, see <xref:host-and-deploy/blazor/server#signalr-configuration>.</span></span>

<span data-ttu-id="a31fa-267">Bei der Verwendung von IIS sind persistente Sitzungen mit Routing von Anwendungsanforderungen aktiviert.</span><span class="sxs-lookup"><span data-stu-id="a31fa-267">When using IIS, sticky sessions are enabled with Application Request Routing.</span></span> <span data-ttu-id="a31fa-268">Weitere Informationen finden Sie unter [HTTP-Lastenausgleiche mithilfe von Routing von Anwendungsanforderungen](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="a31fa-268">For more information, see [HTTP Load Balancing using Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="a31fa-269">Reflektieren des Verbindungs Zustands in der Benutzeroberfläche</span><span class="sxs-lookup"><span data-stu-id="a31fa-269">Reflect the connection state in the UI</span></span>

<span data-ttu-id="a31fa-270">Wenn der Client erkennt, dass die Verbindung unterbrochen wurde, wird dem Benutzer eine Standardbenutzer Oberfläche angezeigt, während der Client versucht, die Verbindung wiederherzustellen.</span><span class="sxs-lookup"><span data-stu-id="a31fa-270">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="a31fa-271">Wenn bei der erneuten Verbindungs Herstellung ein Fehler auftritt, wird dem Benutzer die Option zum erneuten versuchen bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="a31fa-271">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="a31fa-272">Zum Anpassen der Benutzeroberfläche definieren Sie ein Element mit einem `id` `components-reconnect-modal` im `<body>` der Razor Page *_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a31fa-272">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="a31fa-273">In der folgenden Tabelle werden die CSS-Klassen beschrieben, die auf das `components-reconnect-modal` Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="a31fa-273">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="a31fa-274">CSS-Klasse</span><span class="sxs-lookup"><span data-stu-id="a31fa-274">CSS class</span></span>                       | <span data-ttu-id="a31fa-275">Gibt&hellip;</span><span class="sxs-lookup"><span data-stu-id="a31fa-275">Indicates&hellip;</span></span> |
| ------------------------------- | ------------------------- |
| `components-reconnect-show`     | <span data-ttu-id="a31fa-276">Eine verlorene Verbindung.</span><span class="sxs-lookup"><span data-stu-id="a31fa-276">A lost connection.</span></span> <span data-ttu-id="a31fa-277">Der Client versucht, die Verbindung wiederherzustellen.</span><span class="sxs-lookup"><span data-stu-id="a31fa-277">The client is attempting to reconnect.</span></span> <span data-ttu-id="a31fa-278">Zeigen Sie die modale an.</span><span class="sxs-lookup"><span data-stu-id="a31fa-278">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="a31fa-279">Eine aktive Verbindung mit dem Server wird wieder hergestellt.</span><span class="sxs-lookup"><span data-stu-id="a31fa-279">An active connection is re-established to the server.</span></span> <span data-ttu-id="a31fa-280">Blenden Sie den modalen aus.</span><span class="sxs-lookup"><span data-stu-id="a31fa-280">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="a31fa-281">Fehler beim erneuten Herstellen der Verbindung, wahrscheinlich aufgrund eines Netzwerk Fehlers.</span><span class="sxs-lookup"><span data-stu-id="a31fa-281">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="a31fa-282">Um erneut eine Verbindung herzustellen, wenden Sie `window.Blazor.reconnect()`an.</span><span class="sxs-lookup"><span data-stu-id="a31fa-282">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="a31fa-283">Erneute Verbindung abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="a31fa-283">Reconnection rejected.</span></span> <span data-ttu-id="a31fa-284">Der Server wurde erreicht, hat jedoch die Verbindung abgelehnt, und der Benutzer Zustand auf dem Server geht verloren.</span><span class="sxs-lookup"><span data-stu-id="a31fa-284">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="a31fa-285">Um die APP erneut zu laden, wenden Sie `location.reload()`an.</span><span class="sxs-lookup"><span data-stu-id="a31fa-285">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="a31fa-286">Der Verbindungsstatus kann folgende Ergebnisse haben:</span><span class="sxs-lookup"><span data-stu-id="a31fa-286">This connection state may result when:</span></span><ul><li><span data-ttu-id="a31fa-287">Ein Absturz in der serverseitigen Verbindung tritt auf.</span><span class="sxs-lookup"><span data-stu-id="a31fa-287">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="a31fa-288">Der Client ist so lange getrennt, dass der Server den Zustand des Benutzers löscht.</span><span class="sxs-lookup"><span data-stu-id="a31fa-288">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="a31fa-289">Instanzen der Komponenten, mit denen der Benutzer interagiert, werden verworfen.</span><span class="sxs-lookup"><span data-stu-id="a31fa-289">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="a31fa-290">Der Server wird neu gestartet, oder der Arbeitsprozess der APP wird wieder verwendet.</span><span class="sxs-lookup"><span data-stu-id="a31fa-290">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="a31fa-291">Zustands behaftete erneute Verbindung nach der vorab Ausführung</span><span class="sxs-lookup"><span data-stu-id="a31fa-291">Stateful reconnection after prerendering</span></span>

Blazor<span data-ttu-id="a31fa-292"> Server-apps werden standardmäßig so eingerichtet, dass Sie die Benutzeroberfläche auf dem Server vorab ausführen, bevor die Client Verbindung mit dem Server hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="a31fa-292"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="a31fa-293">Dies wird auf der Razor Page *_Host. cshtml* eingerichtet:</span><span class="sxs-lookup"><span data-stu-id="a31fa-293">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="a31fa-294">`RenderMode` konfiguriert, ob die Komponente Folgendes hat:</span><span class="sxs-lookup"><span data-stu-id="a31fa-294">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="a31fa-295">Wird in die Seite vorab übernommen.</span><span class="sxs-lookup"><span data-stu-id="a31fa-295">Is prerendered into the page.</span></span>
* <span data-ttu-id="a31fa-296">Wird auf der Seite als statischer HTML-Code gerendert, oder, wenn er die erforderlichen Informationen zum Bootstrapping einer Blazor-APP vom Benutzer-Agent enthält.</span><span class="sxs-lookup"><span data-stu-id="a31fa-296">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="a31fa-297">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="a31fa-297">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="a31fa-298">Rendert die Komponente in statischem HTML-Format und enthält einen Marker für eine Blazor Server-app.</span><span class="sxs-lookup"><span data-stu-id="a31fa-298">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="a31fa-299">Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="a31fa-299">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="a31fa-300">Rendert einen Marker für eine Blazor Server-app.</span><span class="sxs-lookup"><span data-stu-id="a31fa-300">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="a31fa-301">Die Ausgabe der Komponente ist nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="a31fa-301">Output from the component isn't included.</span></span> <span data-ttu-id="a31fa-302">Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="a31fa-302">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="a31fa-303">Rendert die Komponente in statischem HTML-Format.</span><span class="sxs-lookup"><span data-stu-id="a31fa-303">Renders the component into static HTML.</span></span> |

<span data-ttu-id="a31fa-304">Das Rendering von Serverkomponenten von einer statischen HTML-Seite wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="a31fa-304">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="a31fa-305">Wenn `RenderMode` `ServerPrerendered`ist, wird die Komponente anfänglich statisch als Teil der Seite gerendert.</span><span class="sxs-lookup"><span data-stu-id="a31fa-305">When `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="a31fa-306">Sobald der Browser eine Verbindung mit dem Server herstellt, wird die Komponente *wieder*gerendert, und die Komponente ist nun interaktiv.</span><span class="sxs-lookup"><span data-stu-id="a31fa-306">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="a31fa-307">Wenn die [OnInitialized {Async}](xref:blazor/lifecycle#component-initialization-methods) -Lebenszyklus Methode zum Initialisieren der Komponente vorhanden ist, wird die-Methode *zweimal*ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="a31fa-307">If the [OnInitialized{Async}](xref:blazor/lifecycle#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="a31fa-308">Bei statischer vorab Erstellung der Komponente.</span><span class="sxs-lookup"><span data-stu-id="a31fa-308">When the component is prerendered statically.</span></span>
* <span data-ttu-id="a31fa-309">Nachdem die Server Verbindung hergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="a31fa-309">After the server connection has been established.</span></span>

<span data-ttu-id="a31fa-310">Dies kann zu einer merklichen Änderung in den Daten führen, die in der Benutzeroberfläche angezeigt werden, wenn die Komponente schließlich gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="a31fa-310">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="a31fa-311">So vermeiden Sie das doppelte renderingszenario in einer Blazor Server-App:</span><span class="sxs-lookup"><span data-stu-id="a31fa-311">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="a31fa-312">Übergeben Sie einen Bezeichner, der verwendet werden kann, um den Zustand während der vorab Ausführung zwischenzuspeichern und den Zustand nach dem Neustart der APP abzurufen.</span><span class="sxs-lookup"><span data-stu-id="a31fa-312">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="a31fa-313">Verwenden Sie den Bezeichner während der vorab Einschreibung, um den Komponenten Zustand zu speichern.</span><span class="sxs-lookup"><span data-stu-id="a31fa-313">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="a31fa-314">Verwenden Sie nach der vorab Ausführung den Bezeichner, um den zwischengespeicherten Zustand abzurufen.</span><span class="sxs-lookup"><span data-stu-id="a31fa-314">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="a31fa-315">Der folgende Code veranschaulicht eine aktualisierte `WeatherForecastService` in einer Vorlagen basierten Blazor Server-APP, die das doppelte Rendering vermeidet:</span><span class="sxs-lookup"><span data-stu-id="a31fa-315">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] _summaries = new[]
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
                Summary = _summaries[rng.Next(_summaries.Length)]
            }).ToArray();
        });
    }
}
```

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="a31fa-316">Zustands behaftete interaktive Komponenten von Razor Pages und Ansichten</span><span class="sxs-lookup"><span data-stu-id="a31fa-316">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="a31fa-317">Zustands behaftete interaktive Komponenten können einer Razor Page oder Ansicht hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="a31fa-317">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="a31fa-318">Wenn die Seite oder Ansicht gerendert wird:</span><span class="sxs-lookup"><span data-stu-id="a31fa-318">When the page or view renders:</span></span>

* <span data-ttu-id="a31fa-319">Die Komponente wird mit der Seite oder der Ansicht vorab überstehen.</span><span class="sxs-lookup"><span data-stu-id="a31fa-319">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="a31fa-320">Der anfängliche Komponenten Zustand, der für die vorab Generierung verwendet wird, geht verloren.</span><span class="sxs-lookup"><span data-stu-id="a31fa-320">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="a31fa-321">Der neue Komponenten Status wird erstellt, wenn die SignalR Verbindung hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="a31fa-321">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="a31fa-322">Die folgende Razor Page rendert eine `Counter` Komponente:</span><span class="sxs-lookup"><span data-stu-id="a31fa-322">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="a31fa-323">Nicht interaktive Komponenten von Razor Pages und Ansichten</span><span class="sxs-lookup"><span data-stu-id="a31fa-323">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="a31fa-324">Auf der folgenden Razor-Seite wird die `Counter` Komponente statisch mit einem Anfangswert gerendert, der mit einem Formular angegeben wird:</span><span class="sxs-lookup"><span data-stu-id="a31fa-324">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

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

<span data-ttu-id="a31fa-325">Da `MyComponent` statisch gerendert wird, kann die Komponente nicht interaktiv sein.</span><span class="sxs-lookup"><span data-stu-id="a31fa-325">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="a31fa-326">Erkennen, wenn die APP vorab durchgeführt wird</span><span class="sxs-lookup"><span data-stu-id="a31fa-326">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="a31fa-327">Konfigurieren des SignalR Clients für Blazor Server-apps</span><span class="sxs-lookup"><span data-stu-id="a31fa-327">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="a31fa-328">Manchmal müssen Sie den SignalR Client konfigurieren, der von Blazor Server-Apps verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="a31fa-328">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="a31fa-329">Beispielsweise können Sie die Protokollierung für den SignalR Client konfigurieren, um ein Verbindungsproblem zu diagnostizieren.</span><span class="sxs-lookup"><span data-stu-id="a31fa-329">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="a31fa-330">So konfigurieren Sie den SignalR-Client in der Datei *pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a31fa-330">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="a31fa-331">Fügen Sie dem `<script>`-Tag für das `blazor.server.js` Skript ein `autostart="false"` Attribut hinzu.</span><span class="sxs-lookup"><span data-stu-id="a31fa-331">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="a31fa-332">Ruft `Blazor.start` auf und übergibt ein Konfigurationsobjekt, das den SignalR Generator angibt.</span><span class="sxs-lookup"><span data-stu-id="a31fa-332">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="a31fa-333">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a31fa-333">Additional resources</span></span>

* <xref:blazor/get-started>
* <xref:signalr/introduction>

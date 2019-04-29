---
title: Einführung in Blazor in ASP.NET Core
author: guardrex
description: Lernen Sie ASP.NET Core Blazor kennen, eine Möglichkeit, interaktive clientseitige Webbenutzeroberflächen mit .NET in einer ASP.NET Core-App zu erstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: seoapril2019
ms.date: 04/18/2019
uid: blazor/index
ms.openlocfilehash: 74eeb003c249fc9ff8267ac855455f875806ccd9
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2019
ms.locfileid: "59982996"
---
# <a name="introduction-to-blazor"></a><span data-ttu-id="1d0e8-103">Einführung in Blazor</span><span class="sxs-lookup"><span data-stu-id="1d0e8-103">Introduction to Blazor</span></span>

<span data-ttu-id="1d0e8-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1d0e8-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1d0e8-105">Willkommen bei Blazor!</span><span class="sxs-lookup"><span data-stu-id="1d0e8-105">Welcome to Blazor!</span></span>

<span data-ttu-id="1d0e8-106">Erstellen einer interaktiven clientseitigen Webbenutzeroberfläche mit .NET:</span><span class="sxs-lookup"><span data-stu-id="1d0e8-106">Build interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="1d0e8-107">Erstellen von umfassenden interaktiven Benutzeroberflächen mit C# anstatt mit JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-107">Build rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="1d0e8-108">Gemeinsames Verwenden von server- und clientseitiger App-Logik, die mit .NET geschrieben wurde.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-108">Share server-side and client-side app logic written with .NET.</span></span>
* <span data-ttu-id="1d0e8-109">Rendern der Benutzeroberfläche als HTML und CSS für umfassende Browserunterstützung (einschließlich mobiler Browser).</span><span class="sxs-lookup"><span data-stu-id="1d0e8-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="1d0e8-110">Blazor unterstützt Kernszenarios, die für die meisten Apps erforderlich sind:</span><span class="sxs-lookup"><span data-stu-id="1d0e8-110">Blazor supports core scenarios required by most apps:</span></span>

* <span data-ttu-id="1d0e8-111">Parameter</span><span class="sxs-lookup"><span data-stu-id="1d0e8-111">Parameters</span></span>
* <span data-ttu-id="1d0e8-112">Ereignisbehandlung</span><span class="sxs-lookup"><span data-stu-id="1d0e8-112">Event handling</span></span>
* <span data-ttu-id="1d0e8-113">Datenbindung</span><span class="sxs-lookup"><span data-stu-id="1d0e8-113">Data binding</span></span>
* <span data-ttu-id="1d0e8-114">Routing</span><span class="sxs-lookup"><span data-stu-id="1d0e8-114">Routing</span></span>
* <span data-ttu-id="1d0e8-115">Dependency Injection</span><span class="sxs-lookup"><span data-stu-id="1d0e8-115">Dependency injection</span></span>
* <span data-ttu-id="1d0e8-116">Layouts</span><span class="sxs-lookup"><span data-stu-id="1d0e8-116">Layouts</span></span>
* <span data-ttu-id="1d0e8-117">Vorlagen</span><span class="sxs-lookup"><span data-stu-id="1d0e8-117">Templates</span></span>
* <span data-ttu-id="1d0e8-118">Kaskadierende Werte</span><span class="sxs-lookup"><span data-stu-id="1d0e8-118">Cascading values</span></span>

## <a name="components"></a><span data-ttu-id="1d0e8-119">Komponenten</span><span class="sxs-lookup"><span data-stu-id="1d0e8-119">Components</span></span>

<span data-ttu-id="1d0e8-120">Eine *Komponente* in Blazor ist ein Element der Benutzeroberfläche, beispielsweise eine Seite, ein Dialogfeld oder ein Formular für die Dateneingabe.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-120">A *component* in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span> <span data-ttu-id="1d0e8-121">Komponenten behandeln Benutzerereignisse und definieren flexible Renderinglogik für die Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-121">Components handle user events and define flexible UI rendering logic.</span></span> <span data-ttu-id="1d0e8-122">Komponenten können geschachtelt sein und wiederverwendet werden.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-122">Components can be nested and reused.</span></span>

<span data-ttu-id="1d0e8-123">Komponenten sind in .NET-Assemblys integrierte .NET-Klassen, die gemeinsam genutzt und als NuGet-Pakete verteilt werden können.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-123">Components are .NET classes built into .NET assemblies that can be shared and distributed as NuGet packages.</span></span> <span data-ttu-id="1d0e8-124">Die Komponentenklasse wird in der Regel in Form einer Razor-Markupseite mit der Dateierweiterung *.razor* geschrieben.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-124">The component class is usually written in the form of a Razor markup page with a *.razor* file extension.</span></span> <span data-ttu-id="1d0e8-125">Komponenten in Blazor werden manchmal auch als Razor-Komponenten bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-125">Components in Blazor are sometimes referred to as Razor components.</span></span>

<span data-ttu-id="1d0e8-126">[Razor](xref:mvc/views/razor) ist eine Syntax, mit der HTML-Markup mit C#-Code kombiniert werden kann.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-126">[Razor](xref:mvc/views/razor) is a syntax for combining HTML markup with C# code.</span></span> <span data-ttu-id="1d0e8-127">Razor wurde entwickelt, um die Produktivität von Entwicklern zu optimieren, da diese damit und unter Verwendung der [IntelliSense](/visualstudio/ide/using-intellisense)-Unterstützung innerhalb einer Datei zwischen Markup und C# wechseln können.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-127">Razor is designed for developer productivity, allowing the developer to switch between markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="1d0e8-128">Razor Pages und MVC-Ansichten verwenden ebenfalls Razor.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-128">Razor Pages and MVC views also use Razor.</span></span> <span data-ttu-id="1d0e8-129">Im Gegensatz zu Razor Pages und MVC-Ansichten, die auf einem Anforderung/Antwort-Modell aufbauen, werden Komponenten speziell für die Verarbeitung der Benutzeroberflächengestaltung verwendet.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-129">Unlike Razor Pages and MVC views, which are built around a request/response model, components are used specifically for handling UI composition.</span></span> <span data-ttu-id="1d0e8-130">Razor-Komponenten können insbesondere für die clientseitige Benutzeroberflächenlogik und -gestaltung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-130">Razor components can be used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="1d0e8-131">Das folgende Markup stellt ein Beispiel einer benutzerdefinierten Dialogfeldkomponente dar:</span><span class="sxs-lookup"><span data-stu-id="1d0e8-131">The following markup is an example of a custom dialog component:</span></span>

```cshtml
<div>
    <h2>@Title</h2>
    @BodyContent
    <button onclick="@OnOK">OK</button>
</div>

@functions {
    public string Title { get; set; }
    public RenderFragment BodyContent { get; set; }
    public Action OnOK { get; set; }
}
```

<span data-ttu-id="1d0e8-132">Wenn diese Komponente an einer anderen Stelle in der App verwendet wird, beschleunigt IntelliSense in [Visual Studio](https://visualstudio.microsoft.com/vs/) die Entwicklung mithilfe von Syntax- und Parametervervollständigung.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-132">When this component is used elsewhere in the app, IntelliSense in [Visual Studio](https://visualstudio.microsoft.com/vs/) speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="1d0e8-133">Die Komponenten werden in einer In-Memory-Darstellung des Browser-DOMs gerendert, die als *Renderbaum* bezeichnet wird und anschließend verwendet werden kann, um die Benutzeroberfläche auf flexible und effiziente Weise zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-133">Components render into an in-memory representation of the browser DOM called a *render tree* that can then be used to update the UI in a flexible and efficient way.</span></span>

## <a name="blazor-server-side"></a><span data-ttu-id="1d0e8-134">Serverseitiges Blazor</span><span class="sxs-lookup"><span data-stu-id="1d0e8-134">Blazor server-side</span></span>

<span data-ttu-id="1d0e8-135">Blazor entkoppelt die Komponentenrenderinglogik von Aktualisierungen der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-135">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="1d0e8-136">Serverseitiges Blazor bietet Unterstützung zum Hosten von Razor-Komponenten in einer ASP.NET Core-App auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-136">Blazor server-side provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="1d0e8-137">Aktualisierungen der Benutzeroberfläche werden über eine SignalR-Verbindung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-137">UI updates are handled over a SignalR connection.</span></span>

<span data-ttu-id="1d0e8-138">Die Runtime übernimmt die folgenden Aufgaben:</span><span class="sxs-lookup"><span data-stu-id="1d0e8-138">The runtime:</span></span>

* <span data-ttu-id="1d0e8-139">Senden von Benutzeroberflächenereignissen aus dem Browser an den Server.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-139">Handles sending UI events from the browser to the server.</span></span>
* <span data-ttu-id="1d0e8-140">Anwenden von vom Server gesendeten Benutzeroberflächenupdates auf den Browser nach dem Ausführen der Komponenten.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-140">Applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="1d0e8-141">Die Verbindung, die von serverseitigem Blazor für die Kommunikation mit dem Browser verwendet wird, wird auch für die Verarbeitung von JavaScript-Interopaufrufen verwendet.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-141">The connection used by Blazor server-side to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![Serverseitiges Blazor führt .NET-Code auf dem Server aus und interagiert über eine SignalR-Verbindung mit dem Dokumentobjektmodell.](index/_static/blazor-server-side.png)

<span data-ttu-id="1d0e8-143">Weitere Informationen finden Sie unter <xref:blazor/hosting-models#server-side>.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-143">For more information, see <xref:blazor/hosting-models#server-side>.</span></span>

## <a name="blazor-client-side"></a><span data-ttu-id="1d0e8-144">Clientseitiges Blazor</span><span class="sxs-lookup"><span data-stu-id="1d0e8-144">Blazor client-side</span></span>

<span data-ttu-id="1d0e8-145">Clientseitiges Blazor ist ein Single-Page-App-Framework zum Erstellen von interaktiven clientseitigen Web-Apps mit .NET.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-145">Blazor client-side is a single-page app framework for building interactive client-side Web apps with .NET.</span></span> <span data-ttu-id="1d0e8-146">Clientseitiges Blazor verwendet offene Webstandards ohne Plug-Ins oder Codetranspilation.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-146">Blazor client-side uses open web standards without plugins or code transpilation.</span></span> <span data-ttu-id="1d0e8-147">Clientseitiges Blazor funktioniert in allen modernen Webbrowsern, einschließlich mobiler Browser.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-147">Blazor client-side works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="1d0e8-148">Die Verwendung von .NET im Browser für die clientseitige Webentwicklung bietet viele Vorteile:</span><span class="sxs-lookup"><span data-stu-id="1d0e8-148">Using .NET in the browser for client-side web development offers many advantages:</span></span>

* <span data-ttu-id="1d0e8-149">**C#-Sprache**: Schreiben Sie Code in C# anstatt in JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-149">**C# language**: Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="1d0e8-150">**.NET-Ökosystem**: Nutzen Sie das vorhandene Ökosystem von .NET-Bibliotheken.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-150">**.NET Ecosystem**: Leverage the existing ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="1d0e8-151">**Full-Stack-Entwicklung**: Nutzen Sie server- und clientseitige Logik gemeinsam.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-151">**Full-stack development**: Share server and client-side logic.</span></span>
* <span data-ttu-id="1d0e8-152">**Geschwindigkeit und Skalierbarkeit:** NET wurde für Leistung, Zuverlässigkeit und Sicherheit konzipiert.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-152">**Speed and scalability**: .NET was built for performance, reliability, and security.</span></span>
* <span data-ttu-id="1d0e8-153">**Branchenführende Tools:** Bleiben Sie mit Visual Studio unter Windows, Linux und macOS produktiv.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-153">**Industry-leading tools**: Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="1d0e8-154">**Stabilität und Konsistenz**:  Setzen Sie auf gebräuchliche Sprachen, Frameworks und Tools, die stabil, funktionsreich und einfach zu verwenden sind.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-154">**Stability and consistency**:  Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

<span data-ttu-id="1d0e8-155">Das Ausführen von .NET-Code in einem Webbrowser wird durch [WebAssembly](http://webassembly.org) (Kurzform: *wasm*) ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-155">Running .NET code inside web browsers is made possible by [WebAssembly](http://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="1d0e8-156">WebAssembly ist ein offener Webstandard, der in Webbrowsern ohne Plug-Ins unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-156">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span> <span data-ttu-id="1d0e8-157">Es handelt sich um ein Bytecodeformat, das für schnelles Downloaden und die maximale Ausführungsgeschwindigkeit optimiert ist.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-157">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span>

<span data-ttu-id="1d0e8-158">WebAssembly-Code kann über JavaScript-Interoperabilität auf alle Funktionen des Browsers zugreifen.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-158">WebAssembly code can access the full functionality of the browser via JavaScript interop.</span></span> <span data-ttu-id="1d0e8-159">Gleichzeitig wird der über WebAssembly ausgeführte .NET-Code in derselben vertrauenswürdigen Sandbox wie JavaScript ausgeführt, um böswillige Aktionen auf dem Clientcomputer zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-159">At the same time, .NET code executed via WebAssembly runs in the same trusted sandbox as JavaScript to prevent malicious actions on the client machine.</span></span>

![Clientseitiges Blazor führt .NET-Code unter Verwendung von WebAssembly im Browser aus.](index/_static/blazor-client-side.png)

<span data-ttu-id="1d0e8-161">Folgendes geschieht, wenn eine clientseitige Blazor-App in einem Browser erstellt und ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="1d0e8-161">When a Blazor client-side app is built and run in a browser:</span></span>

* <span data-ttu-id="1d0e8-162">C#-Codedateien und Razor-Dateien werden in .NET-Assemblys kompiliert.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-162">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="1d0e8-163">Die Assemblys und die .NET-Runtime werden im Browser heruntergeladen.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-163">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="1d0e8-164">Clientseitiges Blazor startet die .NET-Runtime und konfiguriert die Runtime zum Laden der Assemblys für die App.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-164">Blazor client-side bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="1d0e8-165">Die Bearbeitung von Dokumentobjektmodellen (DOM) und API-Aufrufen im Browser wird von der clientseitigen Blazor-Runtime über die JavaScript-Interop verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-165">Document Object Model (DOM) manipulation and browser API calls are handled by the Blazor client-side runtime via JavaScript interop.</span></span>

<span data-ttu-id="1d0e8-166">Um die Größe der heruntergeladenen App zu verringern, wird nicht verwendeter Code aus der App entfernt, wenn sie vom [IL-Linker (Intermediate Language)](xref:host-and-deploy/blazor/configure-linker) veröffentlicht wird.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-166">To reduce the size of the downloaded app, unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:host-and-deploy/blazor/configure-linker).</span></span>

<span data-ttu-id="1d0e8-167">Die Blazor-Clientseite ist ein clientseitiges Hostingmodell.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-167">Blazor client-side is a client-side hosting model.</span></span> <span data-ttu-id="1d0e8-168">Da Blazor die Renderinglogik einer Komponente von der Art und Weise entkoppelt, wie Benutzeroberflächenupdates angewendet werden, besteht Flexibilität hinsichtlich des Hostings von Blazor.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-168">Because Blazor decouples a component's rendering logic from how UI updates are applied, there's flexibility in how Blazor can be hosted.</span></span> <span data-ttu-id="1d0e8-169">Verwenden Sie [serverseitiges Blazor](#blazor-server-side), um Blazor auf dem Server in einer ASP.NET Core-App zu hosten, in der Benutzeroberflächenupdates über eine SignalR-Verbindung verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-169">Use [Blazor server-side](#blazor-server-side) to host Blazor on the server in an ASP.NET Core app where UI updates are handled over a SignalR connection.</span></span> <span data-ttu-id="1d0e8-170">Weitere Informationen finden Sie unter <xref:blazor/hosting-models#server-side>.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-170">For more information, see <xref:blazor/hosting-models#server-side>.</span></span> 

<span data-ttu-id="1d0e8-171">Die Größe der Nutzlast ist ein wichtiger Leistungsfaktor für die Nutzbarkeit einer App.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-171">Payload size is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="1d0e8-172">Clientseitiges Blazor optimiert die Nutzlastgröße, um die Downloadzeiten zu verkürzen:</span><span class="sxs-lookup"><span data-stu-id="1d0e8-172">Blazor client-side optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="1d0e8-173">Nicht verwendete Teile von .NET-Assemblys werden während des Buildvorgangs entfernt.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-173">Unused parts of .NET assemblies are removed during the build process.</span></span>
* <span data-ttu-id="1d0e8-174">HTTP-Antworten werden komprimiert.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-174">HTTP responses are compressed.</span></span>
* <span data-ttu-id="1d0e8-175">Die .NET-Runtime und die Assemblys werden im Browser zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-175">The .NET runtime and assemblies are cached in the browser.</span></span>

<span data-ttu-id="1d0e8-176">[Serverseitiges Blazor](#blazor-server-side) bietet mithilfe der Verwaltung von .NET-Assemblys, der App-Assembly und der serverseitigen Runtime eine kleinere Nutzlast als clientseitiges Blazor.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-176">[Blazor server-side](#blazor-server-side) provides a smaller payload size than Blazor client-side by maintaining .NET assemblies, the app's assembly, and the runtime server-side.</span></span> <span data-ttu-id="1d0e8-177">Serverseitige Blazor-Apps stellen den Clients nur Markupdateien und statische Ressourcen zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-177">Blazor server-side apps only serve markup files and static assets to clients.</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="1d0e8-178">JavaScript-Interoperabilität</span><span class="sxs-lookup"><span data-stu-id="1d0e8-178">JavaScript interop</span></span>

<span data-ttu-id="1d0e8-179">Für Apps, die JavaScript-Bibliotheken und Browser-APIs von Drittanbietern erfordern, sind die Komponenten für die Interoperabilität mit JavaScript konzipiert.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-179">For apps that require third-party JavaScript libraries and browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="1d0e8-180">Komponenten können jede Bibliothek oder API verwenden, die auch von JavaScript verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-180">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="1d0e8-181">C#-Code kann JavaScript-Code abfragen, und umgekehrt.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-181">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="1d0e8-182">Weitere Informationen finden Sie unter [JavaScript interop (JavaScript-Interoperabilität)](xref:blazor/javascript-interop).</span><span class="sxs-lookup"><span data-stu-id="1d0e8-182">For more information, see [JavaScript interop](xref:blazor/javascript-interop).</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="1d0e8-183">Codefreigabe und .NET Standard</span><span class="sxs-lookup"><span data-stu-id="1d0e8-183">Code sharing and .NET Standard</span></span>

<span data-ttu-id="1d0e8-184">Apps können auf bereits vorhandene [.NET Standard](/dotnet/standard/net-standard)-Bibliotheken verweisen und diese verwenden.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-184">Apps can reference and use existing [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="1d0e8-185">.NET Standard ist eine formale Spezifikation von .NET-APIs, die häufig für .NET-Implementierungen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-185">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="1d0e8-186">Blazor implementiert .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-186">Blazor implements .NET Standard 2.0.</span></span> <span data-ttu-id="1d0e8-187">APIs, die nicht in einem Webbrowser angewendet werden können (z.B. zum Zugreifen auf ein Dateisystem, zum Öffnen eines Sockets, für das Threading und für andere Features), lösen die Ausnahme <xref:System.PlatformNotSupportedException> aus.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-187">APIs that aren't applicable inside a web browser (for example, accessing the file system, opening a socket, threading, and other features) throw <xref:System.PlatformNotSupportedException>.</span></span> <span data-ttu-id="1d0e8-188">.NET-Standardklassenbibliotheken können auf verschiedenen .NET-Plattformen wie Blazor, .NET Framework, .NET Core, Xamarin, Mono und Unity freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="1d0e8-188">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1d0e8-189">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="1d0e8-189">Additional resources</span></span>

* [<span data-ttu-id="1d0e8-190">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="1d0e8-190">WebAssembly</span></span>](http://webassembly.org/)
* [<span data-ttu-id="1d0e8-191">Leitfaden für C#</span><span class="sxs-lookup"><span data-stu-id="1d0e8-191">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="1d0e8-192">HTML</span><span class="sxs-lookup"><span data-stu-id="1d0e8-192">HTML</span></span>](https://www.w3.org/html/)

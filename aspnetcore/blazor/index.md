---
title: Einführung in Blazor in ASP.NET Core
author: guardrex
description: Lernen Sie ASP.NET Core Blazor kennen, eine Möglichkeit, interaktive clientseitige Webbenutzeroberflächen mit .NET in einer ASP.NET Core-App zu erstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: seoapril2019
ms.date: 04/15/2019
uid: blazor/index
ms.openlocfilehash: a5b12a5c5c10a74ab192d0d67a2ba9a5617232c7
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614573"
---
# <a name="introduction-to-blazor"></a><span data-ttu-id="b820e-103">Einführung in Blazor</span><span class="sxs-lookup"><span data-stu-id="b820e-103">Introduction to Blazor</span></span>

<span data-ttu-id="b820e-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b820e-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="razor-components"></a><span data-ttu-id="b820e-105">Razor Components</span><span class="sxs-lookup"><span data-stu-id="b820e-105">Razor Components</span></span>

<span data-ttu-id="b820e-106">Das serverseitige Hostingmodell von Blazor, *Razor Components*, bietet eine Möglichkeit zum Erstellen einer interaktiven clientseitigen Webbenutzeroberfläche mit .NET:</span><span class="sxs-lookup"><span data-stu-id="b820e-106">The server-side hosting model of Blazor, *Razor Components*, is a way to build interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="b820e-107">Erstellen von umfassenden interaktiven Benutzeroberflächen mit C# anstatt mit JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b820e-107">Build rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="b820e-108">Gemeinsames Verwenden von serverseitiger und clientseitiger App-Logik, die ausnahmslos mit .NET geschrieben wurde.</span><span class="sxs-lookup"><span data-stu-id="b820e-108">Share server-side and client-side app logic all written with .NET.</span></span>
* <span data-ttu-id="b820e-109">Rendern der Benutzeroberfläche als HTML und CSS für umfassende Browserunterstützung (einschließlich mobiler Browser).</span><span class="sxs-lookup"><span data-stu-id="b820e-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="b820e-110">Razor Components unterstützt Kernfunktionen, die für die meisten Apps erforderlich sind, beispielsweise:</span><span class="sxs-lookup"><span data-stu-id="b820e-110">Razor Components supports core facilities required by most apps, including:</span></span>

* <span data-ttu-id="b820e-111">Parameter</span><span class="sxs-lookup"><span data-stu-id="b820e-111">Parameters</span></span>
* <span data-ttu-id="b820e-112">Ereignisbehandlung</span><span class="sxs-lookup"><span data-stu-id="b820e-112">Event handling</span></span>
* <span data-ttu-id="b820e-113">Datenbindung</span><span class="sxs-lookup"><span data-stu-id="b820e-113">Data binding</span></span>
* <span data-ttu-id="b820e-114">Routing</span><span class="sxs-lookup"><span data-stu-id="b820e-114">Routing</span></span>
* <span data-ttu-id="b820e-115">Dependency Injection</span><span class="sxs-lookup"><span data-stu-id="b820e-115">Dependency injection</span></span>
* <span data-ttu-id="b820e-116">Layouts</span><span class="sxs-lookup"><span data-stu-id="b820e-116">Layouts</span></span>
* <span data-ttu-id="b820e-117">Vorlagen</span><span class="sxs-lookup"><span data-stu-id="b820e-117">Templating</span></span>
* <span data-ttu-id="b820e-118">Kaskadierende Werte</span><span class="sxs-lookup"><span data-stu-id="b820e-118">Cascading values</span></span>

<span data-ttu-id="b820e-119">Razor Components entkoppelt die Komponentenrenderinglogik von Aktualisierungen der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="b820e-119">Razor Components decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="b820e-120">Mit ASP.NET Core Razor Components wird in .NET Core 3.0 Unterstützung zum Hosten von Razor Components in einer ASP.NET Core-App auf dem Server hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b820e-120">ASP.NET Core Razor Components in .NET Core 3.0 adds support for hosting Razor Components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="b820e-121">Aktualisierungen der Benutzeroberfläche werden über eine SignalR-Verbindung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="b820e-121">UI updates are handled over a SignalR connection.</span></span>

<span data-ttu-id="b820e-122">Die Runtime übernimmt die folgenden Aufgaben:</span><span class="sxs-lookup"><span data-stu-id="b820e-122">The runtime:</span></span>

* <span data-ttu-id="b820e-123">Senden von Benutzeroberflächenereignissen aus dem Browser an den Server.</span><span class="sxs-lookup"><span data-stu-id="b820e-123">Handles sending UI events from the browser to the server.</span></span>
* <span data-ttu-id="b820e-124">Anwenden von vom Server gesendeten Benutzeroberflächenupdates auf den Browser nach dem Ausführen der Komponenten.</span><span class="sxs-lookup"><span data-stu-id="b820e-124">Applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="b820e-125">Die Verbindung, die von Razor Components für die Kommunikation mit dem Browser verwendet wird, wird auch für die Verarbeitung von JavaScript-Interopaufrufen verwendet.</span><span class="sxs-lookup"><span data-stu-id="b820e-125">The connection used by Razor Components to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![Razor Components führt .NET-Code auf dem Server aus und interagiert über eine SignalR-Verbindung mit dem Dokumentobjektmodell.](index/_static/aspnet-core-razor-components.png)

<span data-ttu-id="b820e-127">Weitere Informationen finden Sie unter <xref:blazor/hosting-models#server-side-hosting-model>.</span><span class="sxs-lookup"><span data-stu-id="b820e-127">For more information, see <xref:blazor/hosting-models#server-side-hosting-model>.</span></span>

## <a name="components"></a><span data-ttu-id="b820e-128">Komponenten</span><span class="sxs-lookup"><span data-stu-id="b820e-128">Components</span></span>

<span data-ttu-id="b820e-129">Eine *Razor Component* ist Bestandteil der Benutzeroberfläche (z.B. Seiten, Dialogfelder oder Formulare für die Dateneingabe).</span><span class="sxs-lookup"><span data-stu-id="b820e-129">A *Razor Component* is a piece of UI, such as a page, dialog, or data entry form.</span></span> <span data-ttu-id="b820e-130">Komponenten behandeln Benutzerereignisse und definieren flexible Renderinglogik für die Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="b820e-130">Components handle user events and define flexible UI rendering logic.</span></span> <span data-ttu-id="b820e-131">Komponenten können geschachtelt sein und wiederverwendet werden.</span><span class="sxs-lookup"><span data-stu-id="b820e-131">Components can be nested and reused.</span></span>

<span data-ttu-id="b820e-132">Komponenten sind in .NET-Assemblys integrierte .NET-Klassen, die gemeinsam genutzt und als NuGet-Pakete verteilt werden können.</span><span class="sxs-lookup"><span data-stu-id="b820e-132">Components are .NET classes built into .NET assemblies that can be shared and distributed as NuGet packages.</span></span> <span data-ttu-id="b820e-133">Die Klasse wird in der Regel in Form einer Razor-Markupseite mit der Dateierweiterung *.razor* (Razor Components) oder einer Razor-Markupseite mit der Dateierweiterung *.cshtml* (Blazor) geschrieben.</span><span class="sxs-lookup"><span data-stu-id="b820e-133">The class is normally written in the form of a Razor markup page with a *.razor* file extension (Razor Components) or Razor markup page with a *.cshtml* file extension (Blazor).</span></span>

<span data-ttu-id="b820e-134">[Razor](xref:mvc/views/razor) ist eine Syntax, mit der HTML-Markup mit C#-Code kombiniert werden kann.</span><span class="sxs-lookup"><span data-stu-id="b820e-134">[Razor](xref:mvc/views/razor) is a syntax for combining HTML markup with C# code.</span></span> <span data-ttu-id="b820e-135">Razor wurde entwickelt, um die Produktivität von Entwicklern zu optimieren, da diese damit und unter Verwendung der [IntelliSense](/visualstudio/ide/using-intellisense)-Unterstützung innerhalb einer Datei zwischen Markup und C# wechseln können.</span><span class="sxs-lookup"><span data-stu-id="b820e-135">Razor is designed for developer productivity, allowing the developer to switch between markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="b820e-136">Razor Pages und MVC-Ansichten verwenden ebenfalls Razor.</span><span class="sxs-lookup"><span data-stu-id="b820e-136">Razor Pages and MVC views also use Razor.</span></span> <span data-ttu-id="b820e-137">Im Gegensatz zu Razor Pages und MVC-Ansichten, die auf einem Anforderung/Antwort-Modell aufbauen, werden Komponenten speziell für die Verarbeitung der Benutzeroberflächengestaltung verwendet.</span><span class="sxs-lookup"><span data-stu-id="b820e-137">Unlike Razor Pages and MVC views, which are built around a request/response model, components are used specifically for handling UI composition.</span></span> <span data-ttu-id="b820e-138">Razor Components können insbesondere für die clientseitige Benutzeroberflächenlogik und -gestaltung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="b820e-138">Razor Components can be used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="b820e-139">Das folgende Markup stellt ein Beispiel einer benutzerdefinierten Dialogfeldkomponente dar:</span><span class="sxs-lookup"><span data-stu-id="b820e-139">The following markup is an example of a custom dialog component:</span></span>

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

<span data-ttu-id="b820e-140">Wenn diese Komponente an einer anderen Stelle in der App verwendet wird, beschleunigt IntelliSense die Entwicklung mithilfe von Syntax- und Parametervervollständigung.</span><span class="sxs-lookup"><span data-stu-id="b820e-140">When this component is used elsewhere in the app, IntelliSense speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="b820e-141">Die Komponenten werden in einer In-Memory-Darstellung des Browser-DOMs gerendert, die als *Renderbaum* bezeichnet wird und anschließend verwendet werden kann, um die Benutzeroberfläche auf flexible und effiziente Weise zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="b820e-141">Components render into an in-memory representation of the browser DOM called a *render tree* that can then be used to update the UI in a flexible and efficient way.</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="b820e-142">JavaScript-Interoperabilität</span><span class="sxs-lookup"><span data-stu-id="b820e-142">JavaScript interop</span></span>

<span data-ttu-id="b820e-143">Für Apps, die JavaScript-Bibliotheken und Browser-APIs von Drittanbietern erfordern, sind die Komponenten für die Interoperabilität mit JavaScript konzipiert.</span><span class="sxs-lookup"><span data-stu-id="b820e-143">For apps that require third-party JavaScript libraries and browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="b820e-144">Komponenten können jede Bibliothek oder API verwenden, die auch von JavaScript verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="b820e-144">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="b820e-145">C#-Code kann JavaScript-Code abfragen, und umgekehrt.</span><span class="sxs-lookup"><span data-stu-id="b820e-145">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="b820e-146">Weitere Informationen finden Sie unter [JavaScript interop (JavaScript-Interoperabilität)](xref:blazor/javascript-interop).</span><span class="sxs-lookup"><span data-stu-id="b820e-146">For more information, see [JavaScript interop](xref:blazor/javascript-interop).</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="b820e-147">Codefreigabe und .NET Standard</span><span class="sxs-lookup"><span data-stu-id="b820e-147">Code sharing and .NET Standard</span></span>

<span data-ttu-id="b820e-148">Apps können auf bereits vorhandene [.NET Standard](/dotnet/standard/net-standard)-Bibliotheken verweisen und diese verwenden.</span><span class="sxs-lookup"><span data-stu-id="b820e-148">Apps can reference and use existing [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="b820e-149">.NET Standard ist eine formale Spezifikation von .NET-APIs, die häufig für .NET-Implementierungen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="b820e-149">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="b820e-150">Blazor implementiert .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="b820e-150">Blazor implements .NET Standard 2.0.</span></span> <span data-ttu-id="b820e-151">APIs, die nicht in einem Webbrowser angewendet werden können (z.B. zum Zugreifen auf ein Dateisystem, zum Öffnen eines Sockets, für das Threading und für andere Features), lösen die Ausnahme <xref:System.PlatformNotSupportedException> aus.</span><span class="sxs-lookup"><span data-stu-id="b820e-151">APIs that aren't applicable inside a web browser (for example, accessing the file system, opening a socket, threading, and other features) throw <xref:System.PlatformNotSupportedException>.</span></span> <span data-ttu-id="b820e-152">.NET-Standardklassenbibliotheken können auf verschiedenen .NET-Plattformen wie Blazor, .NET Framework, .NET Core, Xamarin, Mono und Unity freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="b820e-152">.NET Standard class libraries can be shared across different .NET platforms, like Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

## <a name="blazor"></a><span data-ttu-id="b820e-153">Blazor</span><span class="sxs-lookup"><span data-stu-id="b820e-153">Blazor</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="b820e-154">Blazor ist ein Single-Page-App-Framework zum Erstellen von interaktiven clientseitigen Web-Apps mit .NET.</span><span class="sxs-lookup"><span data-stu-id="b820e-154">Blazor is a single-page app framework for building interactive client-side Web apps with .NET.</span></span> <span data-ttu-id="b820e-155">Blazor verwendet offene Webstandards ohne Plug-Ins oder Codetranspilation.</span><span class="sxs-lookup"><span data-stu-id="b820e-155">Blazor uses open web standards without plugins or code transpilation.</span></span> <span data-ttu-id="b820e-156">Dieses Framework funktioniert in allen modernen Webbrowsern, einschließlich mobiler Browser.</span><span class="sxs-lookup"><span data-stu-id="b820e-156">Blazor works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="b820e-157">Die Verwendung von .NET im Browser für die clientseitige Webentwicklung bietet viele Vorteile:</span><span class="sxs-lookup"><span data-stu-id="b820e-157">Using .NET in the browser for client-side web development offers many advantages:</span></span>

* <span data-ttu-id="b820e-158">**C#-Sprache**: Schreiben Sie Code in C# anstatt in JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b820e-158">**C# language**: Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="b820e-159">**.NET-Ökosystem**: Nutzen Sie das vorhandene Ökosystem von .NET-Bibliotheken.</span><span class="sxs-lookup"><span data-stu-id="b820e-159">**.NET Ecosystem**: Leverage the existing ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="b820e-160">**Full-Stack-Entwicklung**: Nutzen Sie server- und clientseitige Logik gemeinsam.</span><span class="sxs-lookup"><span data-stu-id="b820e-160">**Full-stack development**: Share server and client-side logic.</span></span>
* <span data-ttu-id="b820e-161">**Geschwindigkeit und Skalierbarkeit:** NET wurde für Leistung, Zuverlässigkeit und Sicherheit konzipiert.</span><span class="sxs-lookup"><span data-stu-id="b820e-161">**Speed and scalability**: .NET was built for performance, reliability, and security.</span></span>
* <span data-ttu-id="b820e-162">**Branchenführende Tools:** Bleiben Sie mit Visual Studio unter Windows, Linux und macOS produktiv.</span><span class="sxs-lookup"><span data-stu-id="b820e-162">**Industry-leading tools**: Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="b820e-163">**Stabilität und Konsistenz**:  Setzen Sie auf eine Sammlung von Sprachen, Frameworks und Tools, die stabil, funktionsreich und einfach zu verwenden sind.</span><span class="sxs-lookup"><span data-stu-id="b820e-163">**Stability and consistency**:  Build on a commonset of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

<span data-ttu-id="b820e-164">Das Ausführen von .NET-Code in einem Webbrowser wird durch [WebAssembly](http://webassembly.org) (Kurzform: *wasm*) ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="b820e-164">Running .NET code inside web browsers is made possible by [WebAssembly](http://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="b820e-165">WebAssembly ist ein offener Webstandard, der in Webbrowsern ohne Plug-Ins unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="b820e-165">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span> <span data-ttu-id="b820e-166">Es handelt sich um ein Bytecodeformat, das für schnelles Downloaden und die maximale Ausführungsgeschwindigkeit optimiert ist.</span><span class="sxs-lookup"><span data-stu-id="b820e-166">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span>

<span data-ttu-id="b820e-167">WebAssembly-Code kann über JavaScript-Interoperabilität auf alle Funktionen des Browsers zugreifen.</span><span class="sxs-lookup"><span data-stu-id="b820e-167">WebAssembly code can access the full functionality of the browser via JavaScript interop.</span></span> <span data-ttu-id="b820e-168">Gleichzeitig wird der über WebAssembly ausgeführte .NET-Code in derselben vertrauenswürdigen Sandbox wie JavaScript ausgeführt, um böswillige Aktionen auf dem Clientcomputer zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="b820e-168">At the same time, .NET code executed via WebAssembly runs in the same trusted sandbox as JavaScript to prevent malicious actions on the client machine.</span></span>

![Blazor führt .NET-Code unter Verwendung von WebAssembly im Browser aus.](index/_static/blazor.png)

<span data-ttu-id="b820e-170">Folgendes geschieht, wenn eine Blazor-App in einem Browser erstellt und ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="b820e-170">When a Blazor app is built and run in a browser:</span></span>

* <span data-ttu-id="b820e-171">C#-Codedateien und Razor-Dateien werden in .NET-Assemblys kompiliert.</span><span class="sxs-lookup"><span data-stu-id="b820e-171">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="b820e-172">Die Assemblys und die .NET-Runtime werden im Browser heruntergeladen.</span><span class="sxs-lookup"><span data-stu-id="b820e-172">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="b820e-173">Blazor startet die .NET-Runtime und konfiguriert die Runtime zum Laden der Assemblys für die App.</span><span class="sxs-lookup"><span data-stu-id="b820e-173">Blazor bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="b820e-174">Die Bearbeitung von Dokumentobjektmodellen (DOM) und API-Aufrufen im Browser wird von der Blazor-Runtime über die JavaScript-Interop verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="b820e-174">Document Object Model (DOM) manipulation and browser API calls are handled by the Blazor runtime via JavaScript interop.</span></span>

<span data-ttu-id="b820e-175">Blazor unterstützt Kernfunktionen, die für die meisten Apps erforderlich sind, beispielsweise:</span><span class="sxs-lookup"><span data-stu-id="b820e-175">Blazor supports core facilities required by most apps, including:</span></span>

* <span data-ttu-id="b820e-176">Parameter</span><span class="sxs-lookup"><span data-stu-id="b820e-176">Parameters</span></span>
* <span data-ttu-id="b820e-177">Ereignisbehandlung</span><span class="sxs-lookup"><span data-stu-id="b820e-177">Event handling</span></span>
* <span data-ttu-id="b820e-178">Datenbindung</span><span class="sxs-lookup"><span data-stu-id="b820e-178">Data binding</span></span>
* <span data-ttu-id="b820e-179">Routing</span><span class="sxs-lookup"><span data-stu-id="b820e-179">Routing</span></span>
* <span data-ttu-id="b820e-180">Dependency Injection</span><span class="sxs-lookup"><span data-stu-id="b820e-180">Dependency injection</span></span>
* <span data-ttu-id="b820e-181">Layouts</span><span class="sxs-lookup"><span data-stu-id="b820e-181">Layouts</span></span>
* <span data-ttu-id="b820e-182">Vorlagen</span><span class="sxs-lookup"><span data-stu-id="b820e-182">Templating</span></span>
* <span data-ttu-id="b820e-183">Kaskadierende Werte</span><span class="sxs-lookup"><span data-stu-id="b820e-183">Cascading values</span></span>

<span data-ttu-id="b820e-184">Um die Größe der heruntergeladenen App zu verringern, wird nicht verwendeter Code aus der App entfernt, wenn sie vom [IL-Linker (Intermediate Language)](xref:host-and-deploy/blazor/configure-linker) veröffentlicht wird.</span><span class="sxs-lookup"><span data-stu-id="b820e-184">To reduce the size of the downloaded app, unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:host-and-deploy/blazor/configure-linker).</span></span>

<span data-ttu-id="b820e-185">Die Blazor-Clientseite ist ein clientseitiges Hostingmodell.</span><span class="sxs-lookup"><span data-stu-id="b820e-185">Blazor client-side is a client-side hosting model.</span></span> <span data-ttu-id="b820e-186">Da Blazor die Renderinglogik einer Komponente von der Art und Weise entkoppelt, wie Benutzeroberflächenupdates angewendet werden, besteht Flexibilität hinsichtlich des Hostings von Blazor.</span><span class="sxs-lookup"><span data-stu-id="b820e-186">Because Blazor decouples a component's rendering logic from how UI updates are applied, there's flexibility in how Blazor can be hosted.</span></span> <span data-ttu-id="b820e-187">Verwenden Sie ASP.NET Core [Razor Components](#razor-components), um Razor Components auf dem Server in einer ASP.NET Core-App zu hosten, in der Benutzeroberflächenupdates über eine SignalR-Verbindung verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="b820e-187">Use ASP.NET Core [Razor Components](#razor-components) to host Razor Components on the server in an ASP.NET Core app where UI updates are handled over a SignalR connection.</span></span> <span data-ttu-id="b820e-188">Weitere Informationen finden Sie unter <xref:blazor/hosting-models#server-side-hosting-model>.</span><span class="sxs-lookup"><span data-stu-id="b820e-188">For more information, see <xref:blazor/hosting-models#server-side-hosting-model>.</span></span> 

<span data-ttu-id="b820e-189">Die Größe der Nutzlast ist ein wichtiger Leistungsfaktor für die Nutzbarkeit einer App.</span><span class="sxs-lookup"><span data-stu-id="b820e-189">Payload size is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="b820e-190">Blazor optimiert die Nutzlastgröße, um die Downloadzeiten zu verringern:</span><span class="sxs-lookup"><span data-stu-id="b820e-190">Blazor optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="b820e-191">Nicht verwendete Teile von .NET-Assemblys werden während des Buildvorgangs entfernt.</span><span class="sxs-lookup"><span data-stu-id="b820e-191">Unused parts of .NET assemblies are removed during the build process.</span></span>
* <span data-ttu-id="b820e-192">HTTP-Antworten werden komprimiert.</span><span class="sxs-lookup"><span data-stu-id="b820e-192">HTTP responses are compressed.</span></span>
* <span data-ttu-id="b820e-193">Die .NET-Runtime und die Assemblys werden im Browser zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="b820e-193">The .NET runtime and assemblies are cached in the browser.</span></span>

<span data-ttu-id="b820e-194">[Razor Components](#razor-components) bietet mithilfe der Verwaltung von .NET-Assemblys, der App-Assembly und der serverseitigen Runtime eine kleinere Nutzlast als Blazor.</span><span class="sxs-lookup"><span data-stu-id="b820e-194">[Razor Components](#razor-components) provides a smaller payload size than Blazor by maintaining .NET assemblies, the app's assembly, and the runtime server-side.</span></span> <span data-ttu-id="b820e-195">Razor Components-Apps stellen den Clients nur Markupdateien und statische Ressourcen zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="b820e-195">Razor Components apps only serve markup files and static assets to clients.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b820e-196">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b820e-196">Additional resources</span></span>

* [<span data-ttu-id="b820e-197">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="b820e-197">WebAssembly</span></span>](http://webassembly.org/)
* [<span data-ttu-id="b820e-198">Leitfaden für C#</span><span class="sxs-lookup"><span data-stu-id="b820e-198">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="b820e-199">HTML</span><span class="sxs-lookup"><span data-stu-id="b820e-199">HTML</span></span>](https://www.w3.org/html/)

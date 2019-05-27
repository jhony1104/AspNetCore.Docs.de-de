---
title: Einführung in Blazor in ASP.NET Core
author: guardrex
description: Lernen Sie ASP.NET Core Blazor kennen, eine Möglichkeit, interaktive clientseitige Webbenutzeroberflächen mit .NET in einer ASP.NET Core-App zu erstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 05/01/2019
uid: blazor/index
ms.openlocfilehash: bd7d2d3e6702844627f19dfbbbad5c52389a52e5
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65085783"
---
# <a name="introduction-to-blazor"></a><span data-ttu-id="cdab2-103">Einführung in Blazor</span><span class="sxs-lookup"><span data-stu-id="cdab2-103">Introduction to Blazor</span></span>

<span data-ttu-id="cdab2-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cdab2-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="cdab2-105">*Willkommen bei Blazor!*</span><span class="sxs-lookup"><span data-stu-id="cdab2-105">*Welcome to Blazor!*</span></span>

<span data-ttu-id="cdab2-106">Blazor ist ein Framework zum Erstellen von interaktiver clientseitiger Webbenutzeroberflächen mit .NET:</span><span class="sxs-lookup"><span data-stu-id="cdab2-106">Blazor is a framework for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="cdab2-107">Erstellen Sie umfassende interaktive Benutzeroberflächen mit C# anstatt mit JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cdab2-107">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="cdab2-108">Gemeinsames Verwenden von server- und clientseitiger App-Logik, die mit .NET geschrieben wurde.</span><span class="sxs-lookup"><span data-stu-id="cdab2-108">Share server-side and client-side app logic written with .NET.</span></span>
* <span data-ttu-id="cdab2-109">Rendern der Benutzeroberfläche als HTML und CSS für umfassende Browserunterstützung (einschließlich mobiler Browser).</span><span class="sxs-lookup"><span data-stu-id="cdab2-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="cdab2-110">Die Verwendung von .NET im für die clientseitige Webentwicklung bietet die folgenden Vorteile:</span><span class="sxs-lookup"><span data-stu-id="cdab2-110">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="cdab2-111">Schreiben Sie Code in C# anstatt in JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cdab2-111">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="cdab2-112">Nutzen Sie das vorhandene .NET-Ökosystem von .NET-Bibliotheken.</span><span class="sxs-lookup"><span data-stu-id="cdab2-112">Leverage the existing .NET ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="cdab2-113">Geben Sie die App-Logik über Server und Client hinweg frei.</span><span class="sxs-lookup"><span data-stu-id="cdab2-113">Share app logic across the server and client.</span></span>
* <span data-ttu-id="cdab2-114">Profitieren Sie von Leistung, Zuverlässigkeit und Sicherheit von .NET.</span><span class="sxs-lookup"><span data-stu-id="cdab2-114">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="cdab2-115">Bleiben Sie mit Visual Studio unter Windows, Linux und macOS produktiv.</span><span class="sxs-lookup"><span data-stu-id="cdab2-115">Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="cdab2-116">Setzen Sie auf gebräuchliche Sprachen, Frameworks und Tools, die stabil, funktionsreich und einfach zu verwenden sind.</span><span class="sxs-lookup"><span data-stu-id="cdab2-116">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="cdab2-117">Komponenten</span><span class="sxs-lookup"><span data-stu-id="cdab2-117">Components</span></span>

<span data-ttu-id="cdab2-118">Blazor-Apps basieren auf *Komponenten*.</span><span class="sxs-lookup"><span data-stu-id="cdab2-118">Blazor apps are based on *components*.</span></span> <span data-ttu-id="cdab2-119">Eine Komponente in Blazor ist ein Element der Benutzeroberfläche, beispielsweise eine Seite, ein Dialogfeld oder ein Formular für die Dateneingabe.</span><span class="sxs-lookup"><span data-stu-id="cdab2-119">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span> <span data-ttu-id="cdab2-120">Komponenten behandeln Benutzerereignisse und definieren flexible Renderinglogik für die Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="cdab2-120">Components handle user events and define flexible UI rendering logic.</span></span> <span data-ttu-id="cdab2-121">Komponenten können geschachtelt sein und wiederverwendet werden.</span><span class="sxs-lookup"><span data-stu-id="cdab2-121">Components can be nested and reused.</span></span>

<span data-ttu-id="cdab2-122">Komponenten sind in .NET-Assemblys integrierte .NET-Klassen, die gemeinsam genutzt und als [NuGet-Pakete](/nuget/what-is-nuget) verteilt werden können.</span><span class="sxs-lookup"><span data-stu-id="cdab2-122">Components are .NET classes built into .NET assemblies that can be shared and distributed as [NuGet packages](/nuget/what-is-nuget).</span></span> <span data-ttu-id="cdab2-123">Die Komponentenklasse wird in der Regel in Form einer Razor-Markupseite mit der Dateierweiterung *.razor* geschrieben.</span><span class="sxs-lookup"><span data-stu-id="cdab2-123">The component class is usually written in the form of a Razor markup page with a *.razor* file extension.</span></span>

<span data-ttu-id="cdab2-124">Komponenten in Blazor werden manchmal auch als *Razor-Komponenten* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="cdab2-124">Components in Blazor are sometimes referred to as *Razor components*.</span></span> <span data-ttu-id="cdab2-125">[Razor](xref:mvc/views/razor) ist eine Syntax, mit der HTML-Markup mit C#-Code kombiniert werden kann, ausgerichtet auf die Produktivität der Entwickler.</span><span class="sxs-lookup"><span data-stu-id="cdab2-125">[Razor](xref:mvc/views/razor) is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> <span data-ttu-id="cdab2-126">Razor ermöglicht es Ihnen, mit [IntelliSense](/visualstudio/ide/using-intellisense)-Unterstützung zwischen HTML-Markup und C# in derselben Datei zu wechseln.</span><span class="sxs-lookup"><span data-stu-id="cdab2-126">Razor allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="cdab2-127">Razor Pages und MVC verwenden ebenfalls Razor.</span><span class="sxs-lookup"><span data-stu-id="cdab2-127">Razor Pages and MVC also use Razor.</span></span> <span data-ttu-id="cdab2-128">Im Gegensatz zu Razor Pages und MVC, die auf einem Anforderung/Antwort-Modell aufbauen, werden Komponenten speziell für die clientseitige Benutzeroberflächenlogik und -gestaltung verwendet.</span><span class="sxs-lookup"><span data-stu-id="cdab2-128">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="cdab2-129">Das folgende Razor-Markup veranschaulicht eine Komponente (*Dialog.razor*), die in eine andere Komponente verschachtelt werden kann:</span><span class="sxs-lookup"><span data-stu-id="cdab2-129">The following Razor markup demonstrates a component (*Dialog.razor*), which can be nested within another component:</span></span>

```cshtml
<div>
    <h1>@Title</h1>

    @ChildContent

    <button onclick="@OnYes">Yes!</button>
</div>

@functions {
    [Parameter]
    private string Title { get; set; }

    [Parameter]
    private RenderFragment ChildContent { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#!");
    }
}
```

<span data-ttu-id="cdab2-130">Der Textinhalt (`ChildContent`) und der Titel (`Title`) des Dialogfelds werden von der Komponente bereitgestellt, die diese Komponente in ihrer Benutzeroberfläche verwendet.</span><span class="sxs-lookup"><span data-stu-id="cdab2-130">The dialog's body content (`ChildContent`) and title (`Title`) are provided by the component that uses this component in its UI.</span></span> <span data-ttu-id="cdab2-131">`OnYes` ist eine C#-Methode, die vom Schaltflächenereignis `onclick` ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="cdab2-131">`OnYes` is a C# method triggered by the button's `onclick` event.</span></span>

<span data-ttu-id="cdab2-132">Blazor verwendet die natürlichen HTML-Tags für die Benutzeroberflächengestaltung.</span><span class="sxs-lookup"><span data-stu-id="cdab2-132">Blazor uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="cdab2-133">HTML-Elemente legen Komponenten fest, und die Attribute eines Tags übergeben Werte an die Eigenschaften einer Komponente.</span><span class="sxs-lookup"><span data-stu-id="cdab2-133">HTML elements specify components, and a tag's attributes pass values to a component's properties.</span></span> <span data-ttu-id="cdab2-134">`ChildContent` und `Title` werden von der Komponente festgelegt, die die Dialogfeldkomponente (*Index.razor*) verwendet:</span><span class="sxs-lookup"><span data-stu-id="cdab2-134">`ChildContent` and `Title` are set by the component that uses the Dialog component (*Index.razor*):</span></span>

```cshtml
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="cdab2-135">Das Dialogfeld wird gerendert, wenn die übergeordnete Komponente (*Index.razor*) in einem Browser aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="cdab2-135">The dialog is rendered when the parent (*Index.razor*) is accessed in a browser:</span></span>

![Im Browser gerenderte Dialogfeldkomponente](index/_static/dialog.png)

<span data-ttu-id="cdab2-137">Wenn diese Komponente in der App verwendet wird, beschleunigt IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) und [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) die Entwicklung mithilfe von Syntax- und Parametervervollständigung.</span><span class="sxs-lookup"><span data-stu-id="cdab2-137">When this component is used in the app, IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) and [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="cdab2-138">Die Komponenten werden in einer In-Memory-Darstellung des Browser-DOM gerendert, die als *Renderbaum* bezeichnet und verwendet wird, um die Benutzeroberfläche auf flexible und effiziente Weise zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="cdab2-138">Components render into an in-memory representation of the browser DOM called a *render tree* that's used to update the UI in a flexible and efficient way.</span></span>

## <a name="blazor-client-side"></a><span data-ttu-id="cdab2-139">Clientseitiges Blazor</span><span class="sxs-lookup"><span data-stu-id="cdab2-139">Blazor client-side</span></span>

<span data-ttu-id="cdab2-140">Clientseitiges Blazor ist ein Single-Page-App-Framework zum Erstellen von interaktiven clientseitigen Web-Apps mit .NET.</span><span class="sxs-lookup"><span data-stu-id="cdab2-140">Blazor client-side is a single-page app framework for building interactive client-side web apps with .NET.</span></span> <span data-ttu-id="cdab2-141">Blazor verwendet offene Webstandards ohne Plug-Ins oder Codetranspilation und funktioniert in allen modernen Webbrowsern, einschließlich mobiler Browser.</span><span class="sxs-lookup"><span data-stu-id="cdab2-141">Blazor client-side uses open web standards without plugins or code transpilation and works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="cdab2-142">Das Ausführen von .NET-Code in einem Webbrowser wird durch [WebAssembly](http://webassembly.org) (Kurzform: *wasm*) ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="cdab2-142">Running .NET code inside web browsers is made possible by [WebAssembly](http://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="cdab2-143">WebAssembly ist ein offener Webstandard, der in Webbrowsern ohne Plug-Ins unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="cdab2-143">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span> <span data-ttu-id="cdab2-144">Es handelt sich um ein Bytecodeformat, das für schnelles Downloaden und die maximale Ausführungsgeschwindigkeit optimiert ist.</span><span class="sxs-lookup"><span data-stu-id="cdab2-144">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span>

<span data-ttu-id="cdab2-145">WebAssembly-Code kann auf alle Funktionen des Browsers über JavaScript, auch als *JavaScript-Interoperabilität* (oder *JavaScript-Interop*) bezeichnet, zugreifen.</span><span class="sxs-lookup"><span data-stu-id="cdab2-145">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability* (or *JavaScript interop*).</span></span> <span data-ttu-id="cdab2-146">Über WebAssembly im Browser ausgeführter .NET-Code wird in derselben vertrauenswürdigen Sandbox wie JavaScript ausgeführt, was praktisch verhindert, dass eine App böswillige Aktionen auf dem Clientcomputer ausführen kann.</span><span class="sxs-lookup"><span data-stu-id="cdab2-146">.NET code executed via WebAssembly in the browser runs in the same trusted sandbox as JavaScript, which virtually eliminates the opportunity for an app to perform malicious actions on the client machine.</span></span>

![Clientseitiges Blazor führt .NET-Code unter Verwendung von WebAssembly im Browser aus.](index/_static/blazor-client-side.png)

<span data-ttu-id="cdab2-148">Folgendes geschieht, wenn eine clientseitige Blazor-App in einem Browser erstellt und ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="cdab2-148">When a Blazor client-side app is built and run in a browser:</span></span>

* <span data-ttu-id="cdab2-149">C#-Codedateien und Razor-Dateien werden in .NET-Assemblys kompiliert.</span><span class="sxs-lookup"><span data-stu-id="cdab2-149">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="cdab2-150">Die Assemblys und die .NET-Runtime werden im Browser heruntergeladen.</span><span class="sxs-lookup"><span data-stu-id="cdab2-150">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="cdab2-151">Clientseitiges Blazor startet die .NET-Runtime und konfiguriert die Runtime zum Laden der Assemblys für die App.</span><span class="sxs-lookup"><span data-stu-id="cdab2-151">Blazor client-side bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="cdab2-152">Die Bearbeitung von Dokumentobjektmodellen (DOM) und API-Aufrufen im Browser wird von der clientseitigen Blazor-Runtime über die JavaScript-Interop verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="cdab2-152">The Blazor client-side runtime uses JavaScript interop to handle Document Object Model (DOM) manipulation and browser API calls.</span></span>

<span data-ttu-id="cdab2-153">Die Größe der veröffentlichten App, ihre *Nutzlast*, ist ein wichtiger Leistungsfaktor für die Nutzbarkeit einer App.</span><span class="sxs-lookup"><span data-stu-id="cdab2-153">The size of the published app, its *payload size*, is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="cdab2-154">Das Herunterladen einer großen App in einen Browser dauert relativ lange, was der Benutzerfreundlichkeit schadet.</span><span class="sxs-lookup"><span data-stu-id="cdab2-154">A large app takes a relatively long time to download to a browser, which hurts the user experience.</span></span> <span data-ttu-id="cdab2-155">Clientseitiges Blazor optimiert die Nutzlastgröße, um die Downloadzeiten zu verkürzen:</span><span class="sxs-lookup"><span data-stu-id="cdab2-155">Blazor client-side optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="cdab2-156">Nicht verwendeter Code wird aus der App entfernt, wenn sie vom [IL-Linker (Intermediate Language)](xref:host-and-deploy/blazor/configure-linker) veröffentlicht wird.</span><span class="sxs-lookup"><span data-stu-id="cdab2-156">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:host-and-deploy/blazor/configure-linker).</span></span>
* <span data-ttu-id="cdab2-157">HTTP-Antworten werden komprimiert.</span><span class="sxs-lookup"><span data-stu-id="cdab2-157">HTTP responses are compressed.</span></span>
* <span data-ttu-id="cdab2-158">Die .NET-Runtime und die Assemblys werden im Browser zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="cdab2-158">The .NET runtime and assemblies are cached in the browser.</span></span>

<span data-ttu-id="cdab2-159">Weitere Informationen und Anleitungen zur Auswahl eines Hostingmodells finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="cdab2-159">For more information and guidance on choosing a hosting model, see <xref:blazor/hosting-models>.</span></span>

## <a name="blazor-server-side"></a><span data-ttu-id="cdab2-160">Serverseitiges Blazor</span><span class="sxs-lookup"><span data-stu-id="cdab2-160">Blazor server-side</span></span>

<span data-ttu-id="cdab2-161">Blazor entkoppelt die Komponentenrenderinglogik von Aktualisierungen der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="cdab2-161">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="cdab2-162">Serverseitiges Blazor bietet Unterstützung zum Hosten von Razor-Komponenten in einer ASP.NET Core-App auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="cdab2-162">Blazor server-side provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="cdab2-163">Aktualisierungen der Benutzeroberfläche werden über eine [SignalR](xref:signalr/introduction)-Verbindung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="cdab2-163">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="cdab2-164">Die Runtime handhabt das Senden von Benutzeroberflächenereignissen vom Browser an den Server und wendet Benutzeroberflächenupdates an, die nach dem Ausführen der Komponenten vom Server zurück an den Browser gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="cdab2-164">The runtime handles sending UI events from the browser to the server and applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="cdab2-165">Die Verbindung, die von serverseitigem Blazor für die Kommunikation mit dem Browser verwendet wird, wird auch für die Verarbeitung von JavaScript-Interopaufrufen verwendet.</span><span class="sxs-lookup"><span data-stu-id="cdab2-165">The connection used by Blazor server-side to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![Serverseitiges Blazor führt .NET-Code auf dem Server aus und interagiert über eine SignalR-Verbindung mit dem Dokumentobjektmodell.](index/_static/blazor-server-side.png)

<span data-ttu-id="cdab2-167">Weitere Informationen und Anleitungen zur Auswahl eines Hostingmodells finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="cdab2-167">For more information and guidance on choosing a hosting model, see <xref:blazor/hosting-models>.</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="cdab2-168">JavaScript-Interoperabilität</span><span class="sxs-lookup"><span data-stu-id="cdab2-168">JavaScript interop</span></span>

<span data-ttu-id="cdab2-169">Für Apps, die JavaScript-Bibliotheken und Browser-APIs von Drittanbietern erfordern, sind die Komponenten für die Interoperabilität mit JavaScript konzipiert.</span><span class="sxs-lookup"><span data-stu-id="cdab2-169">For apps that require third-party JavaScript libraries and browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="cdab2-170">Komponenten können jede Bibliothek oder API verwenden, die auch von JavaScript verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="cdab2-170">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="cdab2-171">C#-Code kann JavaScript-Code abfragen, und umgekehrt.</span><span class="sxs-lookup"><span data-stu-id="cdab2-171">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="cdab2-172">Weitere Informationen finden Sie unter <xref:blazor/javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="cdab2-172">For more information, see <xref:blazor/javascript-interop>.</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="cdab2-173">Codefreigabe und .NET Standard</span><span class="sxs-lookup"><span data-stu-id="cdab2-173">Code sharing and .NET Standard</span></span>

<span data-ttu-id="cdab2-174">Blazor implementiert [.NET Standard 2.0](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="cdab2-174">Blazor implements [.NET Standard 2.0](/dotnet/standard/net-standard).</span></span> <span data-ttu-id="cdab2-175">.NET Standard ist eine formale Spezifikation von .NET-APIs, die häufig für .NET-Implementierungen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="cdab2-175">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="cdab2-176">.NET-Standardklassenbibliotheken können auf verschiedenen .NET-Plattformen wie Blazor, .NET Framework, .NET Core, Xamarin, Mono und Unity freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="cdab2-176">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="cdab2-177">APIs, die nicht in einem Webbrowser angewendet werden können (z.B. zum Zugreifen auf ein Dateisystem, zum Öffnen eines Sockets und für das Threading), lösen die Ausnahme <xref:System.PlatformNotSupportedException> aus.</span><span class="sxs-lookup"><span data-stu-id="cdab2-177">APIs that aren't applicable inside a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cdab2-178">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="cdab2-178">Additional resources</span></span>

* [<span data-ttu-id="cdab2-179">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="cdab2-179">WebAssembly</span></span>](http://webassembly.org/)
* [<span data-ttu-id="cdab2-180">Leitfaden für C#</span><span class="sxs-lookup"><span data-stu-id="cdab2-180">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="cdab2-181">HTML</span><span class="sxs-lookup"><span data-stu-id="cdab2-181">HTML</span></span>](https://www.w3.org/html/)

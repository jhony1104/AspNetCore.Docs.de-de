---
title: Einführung in ASP.NET Core Blazor
author: guardrex
description: Lernen Sie ASP.NET Core Blazor kennen, eine Möglichkeit, interaktive clientseitige Webbenutzeroberflächen mit .NET in einer ASP.NET Core-App zu erstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 12/05/2019
no-loc:
- Blazor
- SignalR
uid: blazor/index
ms.openlocfilehash: 4ab6af649afe66d92fb2ea07d3abed057f321bce
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74943718"
---
# <a name="introduction-to-aspnet-core-opno-locblazor"></a><span data-ttu-id="339cc-103">Einführung in ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="339cc-103">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="339cc-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="339cc-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="339cc-105">*Willkommen bei Blazor!*</span><span class="sxs-lookup"><span data-stu-id="339cc-105">*Welcome to Blazor!*</span></span>

Blazor<span data-ttu-id="339cc-106"> ist ein Framework zum Erstellen einer interaktiven clientseitigen Webbenutzeroberfläche mit .NET:</span><span class="sxs-lookup"><span data-stu-id="339cc-106"> is a framework for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="339cc-107">Erstellen Sie umfassende interaktive Benutzeroberflächen mit C# anstatt mit JavaScript.</span><span class="sxs-lookup"><span data-stu-id="339cc-107">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="339cc-108">Gemeinsames Verwenden von server- und clientseitiger App-Logik, die in .NET geschrieben wurde.</span><span class="sxs-lookup"><span data-stu-id="339cc-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="339cc-109">Rendern der Benutzeroberfläche als HTML und CSS für umfassende Browserunterstützung (einschließlich mobiler Browser).</span><span class="sxs-lookup"><span data-stu-id="339cc-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="339cc-110">Die Verwendung von .NET im für die clientseitige Webentwicklung bietet die folgenden Vorteile:</span><span class="sxs-lookup"><span data-stu-id="339cc-110">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="339cc-111">Schreiben Sie Code in C# anstatt in JavaScript.</span><span class="sxs-lookup"><span data-stu-id="339cc-111">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="339cc-112">Nutzen Sie das vorhandene .NET-Ökosystem von .NET-Bibliotheken.</span><span class="sxs-lookup"><span data-stu-id="339cc-112">Leverage the existing .NET ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="339cc-113">Geben Sie die App-Logik server- und clientübergreifend frei.</span><span class="sxs-lookup"><span data-stu-id="339cc-113">Share app logic across server and client.</span></span>
* <span data-ttu-id="339cc-114">Profitieren Sie von Leistung, Zuverlässigkeit und Sicherheit von .NET.</span><span class="sxs-lookup"><span data-stu-id="339cc-114">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="339cc-115">Bleiben Sie mit Visual Studio unter Windows, Linux und macOS produktiv.</span><span class="sxs-lookup"><span data-stu-id="339cc-115">Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="339cc-116">Setzen Sie auf gebräuchliche Sprachen, Frameworks und Tools, die stabil, funktionsreich und einfach zu verwenden sind.</span><span class="sxs-lookup"><span data-stu-id="339cc-116">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="339cc-117">Komponenten</span><span class="sxs-lookup"><span data-stu-id="339cc-117">Components</span></span>

Blazor<span data-ttu-id="339cc-118">-Apps basieren auf *Komponenten*.</span><span class="sxs-lookup"><span data-stu-id="339cc-118"> apps are based on *components*.</span></span> <span data-ttu-id="339cc-119">Eine Komponente in Blazor ist ein Element der Benutzeroberfläche, beispielsweise eine Seite, ein Dialogfeld oder ein Formular für die Dateneingabe.</span><span class="sxs-lookup"><span data-stu-id="339cc-119">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="339cc-120">Komponenten sind in .NET-Assemblys integrierte .NET-Klassen, auf die Folgendes zutrifft:</span><span class="sxs-lookup"><span data-stu-id="339cc-120">Components are .NET classes built into .NET assemblies that:</span></span>

* <span data-ttu-id="339cc-121">Sie definieren die Logik für ein flexibles Rendering der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="339cc-121">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="339cc-122">Sie behandeln Benutzerereignisse.</span><span class="sxs-lookup"><span data-stu-id="339cc-122">Handle user events.</span></span>
* <span data-ttu-id="339cc-123">Sie können geschachtelt und wiederverwendet werden.</span><span class="sxs-lookup"><span data-stu-id="339cc-123">Can be nested and reused.</span></span>
* <span data-ttu-id="339cc-124">Sie können als [Razor-Klassenbibliotheken](xref:razor-pages/ui-class) oder [NuGet-Pakete](/nuget/what-is-nuget) freigegeben und verteilt werden.</span><span class="sxs-lookup"><span data-stu-id="339cc-124">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="339cc-125">Die Komponentenklasse wird in der Regel in Form einer [Razor](xref:mvc/views/razor)-Markupseite mit der Dateierweiterung *.razor* geschrieben.</span><span class="sxs-lookup"><span data-stu-id="339cc-125">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a *.razor* file extension.</span></span> <span data-ttu-id="339cc-126">Komponenten in Blazor werden formal als *Razor-Komponenten* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="339cc-126">Components in Blazor are formally referred to as *Razor components*.</span></span> <span data-ttu-id="339cc-127">Razor ist eine Syntax, mit der HTML-Markup mit C#-Code kombiniert werden kann, ausgerichtet auf die Produktivität der Entwickler.</span><span class="sxs-lookup"><span data-stu-id="339cc-127">Razor is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> <span data-ttu-id="339cc-128">Razor ermöglicht es Ihnen, mit [IntelliSense](/visualstudio/ide/using-intellisense)-Unterstützung zwischen HTML-Markup und C# in derselben Datei zu wechseln.</span><span class="sxs-lookup"><span data-stu-id="339cc-128">Razor allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="339cc-129">Razor Pages und MVC verwenden ebenfalls Razor.</span><span class="sxs-lookup"><span data-stu-id="339cc-129">Razor Pages and MVC also use Razor.</span></span> <span data-ttu-id="339cc-130">Im Gegensatz zu Razor Pages und MVC, die auf einem Anforderung/Antwort-Modell aufbauen, werden Komponenten speziell für die clientseitige Benutzeroberflächenlogik und -gestaltung verwendet.</span><span class="sxs-lookup"><span data-stu-id="339cc-130">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="339cc-131">Das folgende Razor-Markup veranschaulicht eine Komponente (*Dialog.razor*), die in eine andere Komponente verschachtelt werden kann:</span><span class="sxs-lookup"><span data-stu-id="339cc-131">The following Razor markup demonstrates a component (*Dialog.razor*), which can be nested within another component:</span></span>

```razor
<div>
    <h1>@Title</h1>

    @ChildContent

    <button @onclick="OnYes">Yes!</button>
</div>

@code {
    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button was selected.");
    }
}
```

<span data-ttu-id="339cc-132">Der Textinhalt (`ChildContent`) und der Titel (`Title`) des Dialogfelds werden von der Komponente bereitgestellt, die diese Komponente in ihrer Benutzeroberfläche verwendet.</span><span class="sxs-lookup"><span data-stu-id="339cc-132">The dialog's body content (`ChildContent`) and title (`Title`) are provided by the component that uses this component in its UI.</span></span> <span data-ttu-id="339cc-133">`OnYes` ist eine C#-Methode, die vom Schaltflächenereignis `onclick` ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="339cc-133">`OnYes` is a C# method triggered by the button's `onclick` event.</span></span>

Blazor<span data-ttu-id="339cc-134"> verwendet natürliche HTML-Tags für die Benutzeroberflächengestaltung.</span><span class="sxs-lookup"><span data-stu-id="339cc-134"> uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="339cc-135">HTML-Elemente legen Komponenten fest, und die Attribute eines Tags übergeben Werte an die Eigenschaften einer Komponente.</span><span class="sxs-lookup"><span data-stu-id="339cc-135">HTML elements specify components, and a tag's attributes pass values to a component's properties.</span></span>

<span data-ttu-id="339cc-136">Im folgenden Beispiel verwendet die `Index`-Komponente die `Dialog`-Komponente.</span><span class="sxs-lookup"><span data-stu-id="339cc-136">In the following example, the `Index` component uses the `Dialog` component.</span></span> <span data-ttu-id="339cc-137">`ChildContent` und `Title` werden durch die Attribute und den Inhalt des `<Dialog>`-Elements festgelegt.</span><span class="sxs-lookup"><span data-stu-id="339cc-137">`ChildContent` and `Title` are set by the attributes and content of the `<Dialog>` element.</span></span>

<span data-ttu-id="339cc-138">*Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="339cc-138">*Index.razor*:</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="339cc-139">Das Dialogfeld wird gerendert, wenn die übergeordnete Komponente (*Index.razor*) in einem Browser aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="339cc-139">The dialog is rendered when the parent (*Index.razor*) is accessed in a browser:</span></span>

![Im Browser gerenderte Dialogfeldkomponente](index/_static/dialog.png)

<span data-ttu-id="339cc-141">Wenn diese Komponente in der App verwendet wird, beschleunigt IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) und [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) die Entwicklung mithilfe von Syntax- und Parametervervollständigung.</span><span class="sxs-lookup"><span data-stu-id="339cc-141">When this component is used in the app, IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) and [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="339cc-142">Die Komponenten werden in einer In-Memory-Darstellung des Browser-DOM (Document Object Model) gerendert, die als *Renderbaum* bezeichnet und verwendet wird, um die Benutzeroberfläche auf flexible und effiziente Weise zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="339cc-142">Components render into an in-memory representation of the browser's Document Object Model (DOM) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## <a name="opno-locblazor-webassembly"></a>Blazor<span data-ttu-id="339cc-143"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="339cc-143"> WebAssembly</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="339cc-144"> WebAssembly ist ein Single-Page-App-Framework zum Erstellen interaktiver clientseitiger Web-Apps mit .NET.</span><span class="sxs-lookup"><span data-stu-id="339cc-144"> WebAssembly is a single-page app framework for building interactive client-side web apps with .NET.</span></span> Blazor<span data-ttu-id="339cc-145"> WebAssembly verwendet offene Webstandards ohne Plug-Ins oder Codetranspilation und funktioniert in allen modernen Webbrowsern, einschließlich mobiler Browser.</span><span class="sxs-lookup"><span data-stu-id="339cc-145"> WebAssembly uses open web standards without plugins or code transpilation and works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="339cc-146">Das Ausführen von .NET-Code in einem Webbrowser wird durch [WebAssembly](https://webassembly.org) (Kurzform: *wasm*) ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="339cc-146">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="339cc-147">Es handelt sich um ein Bytecodeformat, das für schnelles Downloaden und die maximale Ausführungsgeschwindigkeit optimiert ist.</span><span class="sxs-lookup"><span data-stu-id="339cc-147">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="339cc-148">WebAssembly ist ein offener Webstandard, der in Webbrowsern ohne Plug-Ins unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="339cc-148">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="339cc-149">WebAssembly-Code kann auf alle Funktionen des Browsers über JavaScript, auch als *JavaScript-Interoperabilität* (oder *JavaScript-Interop*) bezeichnet, zugreifen.</span><span class="sxs-lookup"><span data-stu-id="339cc-149">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability* (or *JavaScript interop*).</span></span> <span data-ttu-id="339cc-150">Über WebAssembly im Browser ausgeführter .NET-Code wird in der JavaScript-Sandbox des Browsers ausgeführt. Hierbei greifen die Schutzmaßnahmen der Sandbox gegen schädliche Aktionen auf dem Clientcomputer.</span><span class="sxs-lookup"><span data-stu-id="339cc-150">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

![Blazor<span data-ttu-id="339cc-151"> WebAssembly führt .NET-Code unter Verwendung von WebAssembly im Browser aus.</span><span class="sxs-lookup"><span data-stu-id="339cc-151"> WebAssembly runs .NET code in the browser with WebAssembly.</span></span>](index/_static/blazor-webassembly.png)

<span data-ttu-id="339cc-152">Folgendes geschieht, wenn eine Blazor WebAssembly-App in einem Browser erstellt und ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="339cc-152">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="339cc-153">C#-Codedateien und Razor-Dateien werden in .NET-Assemblys kompiliert.</span><span class="sxs-lookup"><span data-stu-id="339cc-153">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="339cc-154">Die Assemblys und die .NET-Runtime werden im Browser heruntergeladen.</span><span class="sxs-lookup"><span data-stu-id="339cc-154">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* Blazor<span data-ttu-id="339cc-155"> WebAssembly führt einen Bootstrap für die .NET-Runtime aus und konfiguriert die Runtime zum Laden der Assemblys für die App.</span><span class="sxs-lookup"><span data-stu-id="339cc-155"> WebAssembly bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="339cc-156">DOM-Änderungen und API-Aufrufe im Browser werden von der Blazor WebAssembly-Runtime über die JavaScript-Interop verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="339cc-156">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="339cc-157">Die Größe der veröffentlichten App, ihre *Nutzlast*, ist ein wichtiger Leistungsfaktor für die Nutzbarkeit einer App.</span><span class="sxs-lookup"><span data-stu-id="339cc-157">The size of the published app, its *payload size*, is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="339cc-158">Das Herunterladen einer großen App in einen Browser dauert relativ lange, was die Benutzerfreundlichkeit beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="339cc-158">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> Blazor<span data-ttu-id="339cc-159"> WebAssembly optimiert die Nutzlastgröße, um die Downloadzeiten zu verkürzen:</span><span class="sxs-lookup"><span data-stu-id="339cc-159"> WebAssembly optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="339cc-160">Nicht verwendeter Code wird aus der App entfernt, wenn sie vom [IL-Linker (Intermediate Language)](xref:host-and-deploy/blazor/configure-linker) veröffentlicht wird.</span><span class="sxs-lookup"><span data-stu-id="339cc-160">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:host-and-deploy/blazor/configure-linker).</span></span>
* <span data-ttu-id="339cc-161">HTTP-Antworten werden komprimiert.</span><span class="sxs-lookup"><span data-stu-id="339cc-161">HTTP responses are compressed.</span></span>
* <span data-ttu-id="339cc-162">Die .NET-Runtime und die Assemblys werden im Browser zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="339cc-162">The .NET runtime and assemblies are cached in the browser.</span></span>

## <a name="opno-locblazor-server"></a>Blazor<span data-ttu-id="339cc-163"> Server</span><span class="sxs-lookup"><span data-stu-id="339cc-163"> Server</span></span>

Blazor<span data-ttu-id="339cc-164"> entkoppelt die Komponentenrenderinglogik von Aktualisierungen der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="339cc-164"> decouples component rendering logic from how UI updates are applied.</span></span> Blazor<span data-ttu-id="339cc-165"> Server bietet Unterstützung zum Hosten von Razor-Komponenten in einer ASP.NET Core-App auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="339cc-165"> Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="339cc-166">Aktualisierungen der Benutzeroberfläche werden über eine [SignalR](xref:signalr/introduction)-Verbindung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="339cc-166">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="339cc-167">Die Runtime handhabt das Senden von Benutzeroberflächenereignissen vom Browser an den Server und wendet Benutzeroberflächenupdates an, die nach dem Ausführen der Komponenten vom Server zurück an den Browser gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="339cc-167">The runtime handles sending UI events from the browser to the server and applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="339cc-168">Die Verbindung, die von Blazor Server für die Kommunikation mit dem Browser verwendet wird, wird auch für die Verarbeitung von JavaScript-Interopaufrufen verwendet.</span><span class="sxs-lookup"><span data-stu-id="339cc-168">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![Blazor<span data-ttu-id="339cc-169"> Server führt .NET-Code auf dem Server aus und interagiert über eine SignalR-Verbindung mit dem Dokumentobjektmodell.</span><span class="sxs-lookup"><span data-stu-id="339cc-169"> Server runs .NET code on the server and interacts with the Document Object Model on the client over a SignalR connection</span></span>](index/_static/blazor-server.png)

## <a name="javascript-interop"></a><span data-ttu-id="339cc-170">JavaScript-Interoperabilität</span><span class="sxs-lookup"><span data-stu-id="339cc-170">JavaScript interop</span></span>

<span data-ttu-id="339cc-171">Für Apps, die JavaScript-Bibliotheken und Zugriff auf Browser-APIs von Drittanbietern benötigen, sind die Komponenten für die Interoperabilität mit JavaScript konzipiert.</span><span class="sxs-lookup"><span data-stu-id="339cc-171">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="339cc-172">Komponenten können jede Bibliothek oder API verwenden, die auch von JavaScript verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="339cc-172">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="339cc-173">C#-Code kann JavaScript-Code abfragen, und umgekehrt.</span><span class="sxs-lookup"><span data-stu-id="339cc-173">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="339cc-174">Weitere Informationen finden Sie unter <xref:blazor/javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="339cc-174">For more information, see <xref:blazor/javascript-interop>.</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="339cc-175">Codefreigabe und .NET Standard</span><span class="sxs-lookup"><span data-stu-id="339cc-175">Code sharing and .NET Standard</span></span>

Blazor<span data-ttu-id="339cc-176"> implementiert [.NET Standard 2.0](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="339cc-176"> implements [.NET Standard 2.0](/dotnet/standard/net-standard).</span></span> <span data-ttu-id="339cc-177">.NET Standard ist eine formale Spezifikation von .NET-APIs, die häufig für .NET-Implementierungen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="339cc-177">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="339cc-178">.NET-Standardklassenbibliotheken können auf verschiedenen .NET-Plattformen wie Blazor, .NET Framework, .NET Core, Xamarin, Mono und Unity freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="339cc-178">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="339cc-179">APIs, die nicht in einem Webbrowser angewendet werden können (z.B. zum Zugreifen auf ein Dateisystem, zum Öffnen eines Sockets und für das Threading), lösen die Ausnahme <xref:System.PlatformNotSupportedException> aus.</span><span class="sxs-lookup"><span data-stu-id="339cc-179">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="339cc-180">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="339cc-180">Additional resources</span></span>

* [<span data-ttu-id="339cc-181">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="339cc-181">WebAssembly</span></span>](https://webassembly.org/)
* <xref:blazor/hosting-models>
* [<span data-ttu-id="339cc-182">Leitfaden für C#</span><span class="sxs-lookup"><span data-stu-id="339cc-182">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="339cc-183">HTML</span><span class="sxs-lookup"><span data-stu-id="339cc-183">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="339cc-184">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) Community-Links</span><span class="sxs-lookup"><span data-stu-id="339cc-184">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>

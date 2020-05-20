---
<span data-ttu-id="d0551-101">title: „Einführung in ASP.NET Core Blazor“ author: description: „Lernen Sie ASP.NET Core Blazor kennen, eine Möglichkeit, interaktive clientseitige Webbenutzeroberflächen mit .NET in einer ASP.NET Core-App zu erstellen.“</span><span class="sxs-lookup"><span data-stu-id="d0551-101">title: 'Introduction to ASP.NET Core Blazor' author: description: 'Explore ASP.NET Core Blazor, a way to build interactive client-side web UI with .NET in an ASP.NET Core app.'</span></span>
<span data-ttu-id="d0551-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0551-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0551-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0551-103">'Blazor'</span></span>
- <span data-ttu-id="d0551-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0551-104">'Identity'</span></span>
- <span data-ttu-id="d0551-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0551-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0551-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0551-106">'Razor'</span></span>
- <span data-ttu-id="d0551-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d0551-107">'SignalR' uid:</span></span> 

---
# <a name="introduction-to-aspnet-core-blazor"></a><span data-ttu-id="d0551-108">Einführung in ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="d0551-108">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="d0551-109">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d0551-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d0551-110">*Willkommen bei Blazor!*</span><span class="sxs-lookup"><span data-stu-id="d0551-110">*Welcome to Blazor!*</span></span>

Blazor<span data-ttu-id="d0551-111"> ist ein Framework zum Erstellen einer interaktiven clientseitigen Webbenutzeroberfläche mit .NET:</span><span class="sxs-lookup"><span data-stu-id="d0551-111"> is a framework for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="d0551-112">Erstellen Sie umfassende interaktive Benutzeroberflächen mit C# anstatt mit JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d0551-112">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="d0551-113">Gemeinsames Verwenden von server- und clientseitiger App-Logik, die in .NET geschrieben wurde.</span><span class="sxs-lookup"><span data-stu-id="d0551-113">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="d0551-114">Rendern der Benutzeroberfläche als HTML und CSS für umfassende Browserunterstützung (einschließlich mobiler Browser).</span><span class="sxs-lookup"><span data-stu-id="d0551-114">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="d0551-115">Integrieren mit modernen Hostingplattformen, z. B. [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index)</span><span class="sxs-lookup"><span data-stu-id="d0551-115">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="d0551-116">Die Verwendung von .NET im für die clientseitige Webentwicklung bietet die folgenden Vorteile:</span><span class="sxs-lookup"><span data-stu-id="d0551-116">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="d0551-117">Schreiben Sie Code in C# anstatt in JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d0551-117">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="d0551-118">Nutzen Sie das vorhandene .NET-Ökosystem von .NET-Bibliotheken.</span><span class="sxs-lookup"><span data-stu-id="d0551-118">Leverage the existing .NET ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="d0551-119">Geben Sie die App-Logik server- und clientübergreifend frei.</span><span class="sxs-lookup"><span data-stu-id="d0551-119">Share app logic across server and client.</span></span>
* <span data-ttu-id="d0551-120">Profitieren Sie von Leistung, Zuverlässigkeit und Sicherheit von .NET.</span><span class="sxs-lookup"><span data-stu-id="d0551-120">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="d0551-121">Bleiben Sie mit Visual Studio unter Windows, Linux und macOS produktiv.</span><span class="sxs-lookup"><span data-stu-id="d0551-121">Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="d0551-122">Setzen Sie auf gebräuchliche Sprachen, Frameworks und Tools, die stabil, funktionsreich und einfach zu verwenden sind.</span><span class="sxs-lookup"><span data-stu-id="d0551-122">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="d0551-123">Komponenten</span><span class="sxs-lookup"><span data-stu-id="d0551-123">Components</span></span>

Blazor<span data-ttu-id="d0551-124">-Apps basieren auf *Komponenten*.</span><span class="sxs-lookup"><span data-stu-id="d0551-124"> apps are based on *components*.</span></span> <span data-ttu-id="d0551-125">Eine Komponente in Blazor ist ein Element der Benutzeroberfläche, beispielsweise eine Seite, ein Dialogfeld oder ein Formular für die Dateneingabe.</span><span class="sxs-lookup"><span data-stu-id="d0551-125">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="d0551-126">Komponenten sind in .NET-Assemblys integrierte .NET-Klassen, auf die Folgendes zutrifft:</span><span class="sxs-lookup"><span data-stu-id="d0551-126">Components are .NET classes built into .NET assemblies that:</span></span>

* <span data-ttu-id="d0551-127">Sie definieren die Logik für ein flexibles Rendering der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="d0551-127">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="d0551-128">Sie behandeln Benutzerereignisse.</span><span class="sxs-lookup"><span data-stu-id="d0551-128">Handle user events.</span></span>
* <span data-ttu-id="d0551-129">Sie können geschachtelt und wiederverwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d0551-129">Can be nested and reused.</span></span>
* <span data-ttu-id="d0551-130">Sie können als [Razor-Klassenbibliotheken](xref:razor-pages/ui-class) oder [NuGet-Pakete](/nuget/what-is-nuget) freigegeben und verteilt werden.</span><span class="sxs-lookup"><span data-stu-id="d0551-130">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="d0551-131">Die Komponentenklasse wird in der Regel in Form einer [Razor](xref:mvc/views/razor)-Markupseite mit der Dateierweiterung *.razor* geschrieben.</span><span class="sxs-lookup"><span data-stu-id="d0551-131">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a *.razor* file extension.</span></span> <span data-ttu-id="d0551-132">Komponenten in Blazor werden formal als *Razor-Komponenten* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="d0551-132">Components in Blazor are formally referred to as *Razor components*.</span></span> Razor<span data-ttu-id="d0551-133"> ist eine Syntax, mit der HTML-Markup mit C#-Code kombiniert werden kann, ausgerichtet auf die Produktivität der Entwickler.</span><span class="sxs-lookup"><span data-stu-id="d0551-133"> is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> Razor<span data-ttu-id="d0551-134"> ermöglicht es Ihnen, mit [IntelliSense](/visualstudio/ide/using-intellisense)-Unterstützung zwischen HTML-Markup und C# in derselben Datei zu wechseln.</span><span class="sxs-lookup"><span data-stu-id="d0551-134"> allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> Razor<span data-ttu-id="d0551-135"> Pages und MVC verwenden ebenfalls Razor.</span><span class="sxs-lookup"><span data-stu-id="d0551-135"> Pages and MVC also use Razor.</span></span> <span data-ttu-id="d0551-136">Im Gegensatz zu Razor Pages und MVC, die auf einem Anforderung/Antwort-Modell aufbauen, werden Komponenten speziell für die clientseitige Benutzeroberflächenlogik und -gestaltung verwendet.</span><span class="sxs-lookup"><span data-stu-id="d0551-136">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="d0551-137">Das folgende Razor-Markup veranschaulicht eine Komponente (*Dialog.razor*), die in eine andere Komponente verschachtelt werden kann:</span><span class="sxs-lookup"><span data-stu-id="d0551-137">The following Razor markup demonstrates a component (*Dialog.razor*), which can be nested within another component:</span></span>

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

<span data-ttu-id="d0551-138">Der Textinhalt (`ChildContent`) und der Titel (`Title`) des Dialogfelds werden von der Komponente bereitgestellt, die diese Komponente in ihrer Benutzeroberfläche verwendet.</span><span class="sxs-lookup"><span data-stu-id="d0551-138">The dialog's body content (`ChildContent`) and title (`Title`) are provided by the component that uses this component in its UI.</span></span> <span data-ttu-id="d0551-139">`OnYes` ist eine C#-Methode, die vom Schaltflächenereignis `onclick` ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="d0551-139">`OnYes` is a C# method triggered by the button's `onclick` event.</span></span>

Blazor<span data-ttu-id="d0551-140"> verwendet natürliche HTML-Tags für die Benutzeroberflächengestaltung.</span><span class="sxs-lookup"><span data-stu-id="d0551-140"> uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="d0551-141">HTML-Elemente legen Komponenten fest, und die Attribute eines Tags übergeben Werte an die Eigenschaften einer Komponente.</span><span class="sxs-lookup"><span data-stu-id="d0551-141">HTML elements specify components, and a tag's attributes pass values to a component's properties.</span></span>

<span data-ttu-id="d0551-142">Im folgenden Beispiel verwendet die `Index`-Komponente die `Dialog`-Komponente.</span><span class="sxs-lookup"><span data-stu-id="d0551-142">In the following example, the `Index` component uses the `Dialog` component.</span></span> <span data-ttu-id="d0551-143">`ChildContent` und `Title` werden durch die Attribute und den Inhalt des `<Dialog>`-Elements festgelegt.</span><span class="sxs-lookup"><span data-stu-id="d0551-143">`ChildContent` and `Title` are set by the attributes and content of the `<Dialog>` element.</span></span>

<span data-ttu-id="d0551-144">*Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="d0551-144">*Index.razor*:</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="d0551-145">Das Dialogfeld wird gerendert, wenn die übergeordnete Komponente (*Index.razor*) in einem Browser aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="d0551-145">The dialog is rendered when the parent (*Index.razor*) is accessed in a browser:</span></span>

![Im Browser gerenderte Dialogfeldkomponente](index/_static/dialog.png)

<span data-ttu-id="d0551-147">Wenn diese Komponente in der App verwendet wird, beschleunigt IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) und [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) die Entwicklung mithilfe von Syntax- und Parametervervollständigung.</span><span class="sxs-lookup"><span data-stu-id="d0551-147">When this component is used in the app, IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) and [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="d0551-148">Die Komponenten werden in einer In-Memory-Darstellung des Browser-DOM (Document Object Model) gerendert, die als *Renderbaum* bezeichnet und verwendet wird, um die Benutzeroberfläche auf flexible und effiziente Weise zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="d0551-148">Components render into an in-memory representation of the browser's Document Object Model (DOM) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="d0551-149"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="d0551-149"> WebAssembly</span></span>

Blazor<span data-ttu-id="d0551-150"> WebAssembly ist ein Single-Page-App-Framework zum Erstellen interaktiver clientseitiger Web-Apps mit .NET.</span><span class="sxs-lookup"><span data-stu-id="d0551-150"> WebAssembly is a single-page app framework for building interactive client-side web apps with .NET.</span></span> Blazor<span data-ttu-id="d0551-151"> WebAssembly verwendet offene Webstandards ohne Plug-Ins oder Codetranspilation und funktioniert in allen modernen Webbrowsern, einschließlich mobiler Browser.</span><span class="sxs-lookup"><span data-stu-id="d0551-151"> WebAssembly uses open web standards without plugins or code transpilation and works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="d0551-152">Das Ausführen von .NET-Code in einem Webbrowser wird durch [WebAssembly](https://webassembly.org) (Kurzform: *wasm*) ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="d0551-152">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="d0551-153">Es handelt sich um ein Bytecodeformat, das für schnelles Downloaden und die maximale Ausführungsgeschwindigkeit optimiert ist.</span><span class="sxs-lookup"><span data-stu-id="d0551-153">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="d0551-154">WebAssembly ist ein offener Webstandard, der in Webbrowsern ohne Plug-Ins unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="d0551-154">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="d0551-155">WebAssembly-Code kann auf alle Funktionen des Browsers über JavaScript, auch als *JavaScript-Interoperabilität* (oder *JavaScript-Interop*) bezeichnet, zugreifen.</span><span class="sxs-lookup"><span data-stu-id="d0551-155">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability* (or *JavaScript interop*).</span></span> <span data-ttu-id="d0551-156">Über WebAssembly im Browser ausgeführter .NET-Code wird in der JavaScript-Sandbox des Browsers ausgeführt. Hierbei greifen die Schutzmaßnahmen der Sandbox gegen schädliche Aktionen auf dem Clientcomputer.</span><span class="sxs-lookup"><span data-stu-id="d0551-156">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

<span data-ttu-id="d0551-157">![Blazor WebAssembly führt .NET-Code unter Verwendung von WebAssembly im Browser aus.](index/_static/blazor-webassembly.png)</span><span class="sxs-lookup"><span data-stu-id="d0551-157">![Blazor WebAssembly runs .NET code in the browser with WebAssembly.](index/_static/blazor-webassembly.png)</span></span>

<span data-ttu-id="d0551-158">Folgendes geschieht, wenn eine Blazor WebAssembly-App in einem Browser erstellt und ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="d0551-158">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="d0551-159">C#-Codedateien und Razor-Dateien werden in .NET-Assemblys kompiliert.</span><span class="sxs-lookup"><span data-stu-id="d0551-159">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="d0551-160">Die Assemblys und die .NET-Runtime werden im Browser heruntergeladen.</span><span class="sxs-lookup"><span data-stu-id="d0551-160">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* Blazor<span data-ttu-id="d0551-161"> WebAssembly führt einen Bootstrap für die .NET-Runtime aus und konfiguriert die Runtime zum Laden der Assemblys für die App.</span><span class="sxs-lookup"><span data-stu-id="d0551-161"> WebAssembly bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="d0551-162">DOM-Änderungen und API-Aufrufe im Browser werden von der Blazor WebAssembly-Runtime über die JavaScript-Interop verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="d0551-162">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="d0551-163">Die Größe der veröffentlichten App, ihre *Nutzlast*, ist ein wichtiger Leistungsfaktor für die Nutzbarkeit einer App.</span><span class="sxs-lookup"><span data-stu-id="d0551-163">The size of the published app, its *payload size*, is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="d0551-164">Das Herunterladen einer großen App in einen Browser dauert relativ lange, was die Benutzerfreundlichkeit beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="d0551-164">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> Blazor<span data-ttu-id="d0551-165"> WebAssembly optimiert die Nutzlastgröße, um die Downloadzeiten zu verkürzen:</span><span class="sxs-lookup"><span data-stu-id="d0551-165"> WebAssembly optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="d0551-166">Nicht verwendeter Code wird aus der App entfernt, wenn sie vom [IL-Linker (Intermediate Language)](xref:host-and-deploy/blazor/configure-linker) veröffentlicht wird.</span><span class="sxs-lookup"><span data-stu-id="d0551-166">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:host-and-deploy/blazor/configure-linker).</span></span>
* <span data-ttu-id="d0551-167">HTTP-Antworten werden komprimiert.</span><span class="sxs-lookup"><span data-stu-id="d0551-167">HTTP responses are compressed.</span></span>
* <span data-ttu-id="d0551-168">Die .NET-Runtime und die Assemblys werden im Browser zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="d0551-168">The .NET runtime and assemblies are cached in the browser.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="d0551-169"> Server</span><span class="sxs-lookup"><span data-stu-id="d0551-169"> Server</span></span>

Blazor<span data-ttu-id="d0551-170"> entkoppelt die Komponentenrenderinglogik von Aktualisierungen der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="d0551-170"> decouples component rendering logic from how UI updates are applied.</span></span> Blazor<span data-ttu-id="d0551-171"> Server bietet Unterstützung zum Hosten von Razor-Komponenten in einer ASP.NET Core-App auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="d0551-171"> Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="d0551-172">Aktualisierungen der Benutzeroberfläche werden über eine [SignalR](xref:signalr/introduction)-Verbindung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="d0551-172">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="d0551-173">Die Runtime handhabt das Senden von Benutzeroberflächenereignissen vom Browser an den Server und wendet Benutzeroberflächenupdates an, die nach dem Ausführen der Komponenten vom Server zurück an den Browser gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="d0551-173">The runtime handles sending UI events from the browser to the server and applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="d0551-174">Die Verbindung, die von Blazor Server für die Kommunikation mit dem Browser verwendet wird, wird auch für die Verarbeitung von JavaScript-Interopaufrufen verwendet.</span><span class="sxs-lookup"><span data-stu-id="d0551-174">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

<span data-ttu-id="d0551-175">![Blazor Server führt .NET-Code auf dem Server aus und interagiert über eine SignalR-Verbindung](index/_static/blazor-server.png) mit dem Dokumentobjektmodell.</span><span class="sxs-lookup"><span data-stu-id="d0551-175">![Blazor Server runs .NET code on the server and interacts with the Document Object Model on the client over a SignalR connection](index/_static/blazor-server.png)</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="d0551-176">JavaScript-Interoperabilität</span><span class="sxs-lookup"><span data-stu-id="d0551-176">JavaScript interop</span></span>

<span data-ttu-id="d0551-177">Für Apps, die JavaScript-Bibliotheken und Zugriff auf Browser-APIs von Drittanbietern benötigen, sind die Komponenten für die Interoperabilität mit JavaScript konzipiert.</span><span class="sxs-lookup"><span data-stu-id="d0551-177">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="d0551-178">Komponenten können jede Bibliothek oder API verwenden, die auch von JavaScript verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="d0551-178">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="d0551-179">C#-Code kann JavaScript-Code abfragen, und umgekehrt.</span><span class="sxs-lookup"><span data-stu-id="d0551-179">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="d0551-180">Weitere Informationen finden Sie in den folgenden Artikeln:</span><span class="sxs-lookup"><span data-stu-id="d0551-180">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="d0551-181">Codefreigabe und .NET Standard</span><span class="sxs-lookup"><span data-stu-id="d0551-181">Code sharing and .NET Standard</span></span>

Blazor<span data-ttu-id="d0551-182"> implementiert [.NET Standard 2.0](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="d0551-182"> implements [.NET Standard 2.0](/dotnet/standard/net-standard).</span></span> <span data-ttu-id="d0551-183">.NET Standard ist eine formale Spezifikation von .NET-APIs, die häufig für .NET-Implementierungen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d0551-183">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="d0551-184">.NET-Standardklassenbibliotheken können auf verschiedenen .NET-Plattformen wie Blazor, .NET Framework, .NET Core, Xamarin, Mono und Unity freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="d0551-184">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="d0551-185">APIs, die nicht in einem Webbrowser angewendet werden können (z.B. zum Zugreifen auf ein Dateisystem, zum Öffnen eines Sockets und für das Threading), lösen die Ausnahme <xref:System.PlatformNotSupportedException> aus.</span><span class="sxs-lookup"><span data-stu-id="d0551-185">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d0551-186">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d0551-186">Additional resources</span></span>

* [<span data-ttu-id="d0551-187">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="d0551-187">WebAssembly</span></span>](https://webassembly.org/)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* [<span data-ttu-id="d0551-188">Leitfaden für C#</span><span class="sxs-lookup"><span data-stu-id="d0551-188">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="d0551-189">HTML</span><span class="sxs-lookup"><span data-stu-id="d0551-189">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="d0551-190">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) Community-Links</span><span class="sxs-lookup"><span data-stu-id="d0551-190">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>

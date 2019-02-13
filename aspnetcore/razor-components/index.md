---
title: Einführung in Razor Components
author: guardrex
description: Lernen Sie ASP.NET Core Razor Components kennen, eine Möglichkeit, interaktive clientseitige Webbenutzeroberflächen mit .NET in einer ASP.NET Core-App zu erstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2019
uid: razor-components/index
ms.openlocfilehash: 04a73d33cee0deedaf3dc97395836a936b580fbd
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56159525"
---
# <a name="introduction-to-razor-components"></a><span data-ttu-id="ec1b3-103">Einführung in Razor Components</span><span class="sxs-lookup"><span data-stu-id="ec1b3-103">Introduction to Razor Components</span></span>

<span data-ttu-id="ec1b3-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ec1b3-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ec1b3-105">*Razor Components* bieten eine Möglichkeit zum Erstellen einer interaktiven clientseitigen Webbenutzeroberfläche mit .NET:</span><span class="sxs-lookup"><span data-stu-id="ec1b3-105">*Razor Components* is a way to build interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="ec1b3-106">Erstellen von umfassenden interaktiven Benutzeroberflächen mit C# anstatt mit JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ec1b3-106">Build rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="ec1b3-107">Gemeinsames Verwenden von serverseitiger und clientseitiger App-Logik, die ausnahmslos mit .NET geschrieben wurde.</span><span class="sxs-lookup"><span data-stu-id="ec1b3-107">Share server-side and client-side app logic all written with .NET.</span></span>
* <span data-ttu-id="ec1b3-108">Rendern der Benutzeroberfläche als HTML und CSS für umfassende Browserunterstützung (einschließlich mobiler Browser).</span><span class="sxs-lookup"><span data-stu-id="ec1b3-108">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="ec1b3-109">Razor Components unterstützt Kernfunktionen, die für die meisten Apps erforderlich sind, beispielsweise:</span><span class="sxs-lookup"><span data-stu-id="ec1b3-109">Razor Components supports core facilities required by most apps, including:</span></span>

* <span data-ttu-id="ec1b3-110">Parameter</span><span class="sxs-lookup"><span data-stu-id="ec1b3-110">Parameters</span></span>
* <span data-ttu-id="ec1b3-111">Ereignisbehandlung</span><span class="sxs-lookup"><span data-stu-id="ec1b3-111">Event handling</span></span>
* <span data-ttu-id="ec1b3-112">Datenbindung</span><span class="sxs-lookup"><span data-stu-id="ec1b3-112">Data binding</span></span>
* <span data-ttu-id="ec1b3-113">Routing</span><span class="sxs-lookup"><span data-stu-id="ec1b3-113">Routing</span></span>
* <span data-ttu-id="ec1b3-114">Dependency Injection</span><span class="sxs-lookup"><span data-stu-id="ec1b3-114">Dependency injection</span></span>
* <span data-ttu-id="ec1b3-115">Layouts</span><span class="sxs-lookup"><span data-stu-id="ec1b3-115">Layouts</span></span>
* <span data-ttu-id="ec1b3-116">Vorlagen</span><span class="sxs-lookup"><span data-stu-id="ec1b3-116">Templating</span></span>
* <span data-ttu-id="ec1b3-117">Kaskadierende Werte</span><span class="sxs-lookup"><span data-stu-id="ec1b3-117">Cascading values</span></span>

<span data-ttu-id="ec1b3-118">Razor Components entkoppelt die Komponentenrenderinglogik von Aktualisierungen der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="ec1b3-118">Razor Components decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="ec1b3-119">Mit ASP.NET Core Razor Components wird in .NET Core 3.0 Unterstützung zum Hosten von Razor Components in einer ASP.NET Core-App auf dem Server hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="ec1b3-119">ASP.NET Core Razor Components in .NET Core 3.0 adds support for hosting Razor Components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="ec1b3-120">Aktualisierungen der Benutzeroberfläche werden über eine SignalR-Verbindung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="ec1b3-120">UI updates are handled over a SignalR connection.</span></span>

<span data-ttu-id="ec1b3-121">Die Runtime übernimmt die folgenden Aufgaben:</span><span class="sxs-lookup"><span data-stu-id="ec1b3-121">The runtime:</span></span>

* <span data-ttu-id="ec1b3-122">Senden von Benutzeroberflächenereignissen aus dem Browser an den Server.</span><span class="sxs-lookup"><span data-stu-id="ec1b3-122">Handles sending UI events from the browser to the server.</span></span>
* <span data-ttu-id="ec1b3-123">Anwenden von vom Server gesendeten Benutzeroberflächenupdates auf den Browser nach dem Ausführen der Komponenten.</span><span class="sxs-lookup"><span data-stu-id="ec1b3-123">Applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="ec1b3-124">Die Verbindung, die von Razor Components für die Kommunikation mit dem Browser verwendet wird, wird auch für die Verarbeitung von JavaScript-Interopaufrufen verwendet.</span><span class="sxs-lookup"><span data-stu-id="ec1b3-124">The connection used by Razor Components to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![Razor Components führt .NET-Code auf dem Server aus und interagiert über eine SignalR-Verbindung mit dem Dokumentobjektmodell.](index/_static/aspnet-core-razor-components.png)

<span data-ttu-id="ec1b3-126">Weitere Informationen finden Sie unter <xref:razor-components/hosting-models#server-side-hosting-model>.</span><span class="sxs-lookup"><span data-stu-id="ec1b3-126">For more information, see <xref:razor-components/hosting-models#server-side-hosting-model>.</span></span>

<span data-ttu-id="ec1b3-127">*Blazor* ist das experimentelle clientseitige Hostingmodell von Razor Components.</span><span class="sxs-lookup"><span data-stu-id="ec1b3-127">*Blazor* is the experimental client-side hosting model of Razor Components.</span></span> <span data-ttu-id="ec1b3-128">Blazor wird unter .NET unter Verwendung offener Webstandards ohne Plug-Ins und Codetranspilierung im Browser ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="ec1b3-128">Blazor runs on .NET in the browser using open web standards without plugins or code transpilation.</span></span> <span data-ttu-id="ec1b3-129">Weitere Informationen finden Sie unter <xref:razor-components/hosting-models#client-side-hosting-model>.</span><span class="sxs-lookup"><span data-stu-id="ec1b3-129">For more information, see <xref:razor-components/hosting-models#client-side-hosting-model>.</span></span>

## <a name="components"></a><span data-ttu-id="ec1b3-130">Komponenten</span><span class="sxs-lookup"><span data-stu-id="ec1b3-130">Components</span></span>

<span data-ttu-id="ec1b3-131">Eine *Razor Component* ist Bestandteil der Benutzeroberfläche (z.B. Seiten, Dialogfelder oder Formulare für die Dateneingabe).</span><span class="sxs-lookup"><span data-stu-id="ec1b3-131">A *Razor Component* is a piece of UI, such as a page, dialog, or data entry form.</span></span> <span data-ttu-id="ec1b3-132">Komponenten behandeln Benutzerereignisse und definieren flexible Renderinglogik für die Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="ec1b3-132">Components handle user events and define flexible UI rendering logic.</span></span> <span data-ttu-id="ec1b3-133">Komponenten können geschachtelt sein und wiederverwendet werden.</span><span class="sxs-lookup"><span data-stu-id="ec1b3-133">Components can be nested and reused.</span></span>

<span data-ttu-id="ec1b3-134">Komponenten sind in .NET-Assemblys integrierte .NET-Klassen, die gemeinsam genutzt und als NuGet-Pakete verteilt werden können.</span><span class="sxs-lookup"><span data-stu-id="ec1b3-134">Components are .NET classes built into .NET assemblies that can be shared and distributed as NuGet packages.</span></span> <span data-ttu-id="ec1b3-135">Die Klasse kann entweder in Form einer Razor-Markupseite (*CSHTML-Datei*) oder als C#-Klasse (*CS-Datei*) geschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="ec1b3-135">The class can either be written in the form of a Razor markup page (*.cshtml*) or as a C# class (*.cs*).</span></span>

<span data-ttu-id="ec1b3-136">[Razor](xref:mvc/views/razor) ist eine Syntax, mit der HTML-Markup mit C#-Code kombiniert werden kann.</span><span class="sxs-lookup"><span data-stu-id="ec1b3-136">[Razor](xref:mvc/views/razor) is a syntax for combining HTML markup with C# code.</span></span> <span data-ttu-id="ec1b3-137">Razor wurde entwickelt, um die Produktivität von Entwicklern zu optimieren, da diese damit und unter Verwendung der [IntelliSense](/visualstudio/ide/using-intellisense)-Unterstützung innerhalb einer Datei zwischen Markup und C# wechseln können.</span><span class="sxs-lookup"><span data-stu-id="ec1b3-137">Razor is designed for developer productivity, allowing the developer to switch between markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="ec1b3-138">Razor Pages und MVC-Ansichten verwenden ebenfalls Razor.</span><span class="sxs-lookup"><span data-stu-id="ec1b3-138">Razor Pages and MVC views also use Razor.</span></span> <span data-ttu-id="ec1b3-139">Im Gegensatz zu Razor Pages und MVC-Ansichten, die auf einem Anforderung/Antwort-Modell aufbauen, werden Komponenten speziell für die Verarbeitung der Benutzeroberflächengestaltung verwendet.</span><span class="sxs-lookup"><span data-stu-id="ec1b3-139">Unlike Razor Pages and MVC views, which are built around a request/response model, components are used specifically for handling UI composition.</span></span> <span data-ttu-id="ec1b3-140">Razor Components können insbesondere für die clientseitige Benutzeroberflächenlogik und -gestaltung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="ec1b3-140">Razor Components can be used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="ec1b3-141">Das folgende Markup stellt ein Beispiel einer benutzerdefinierten Dialogfeldkomponente in einer Razor-Datei (*DialogComponent.cshtml*) dar:</span><span class="sxs-lookup"><span data-stu-id="ec1b3-141">The following markup is an example of a custom dialog component in a Razor file (*DialogComponent.cshtml*):</span></span>

```cshtml
<div>
    <h2>@Title</h2>
    @BodyContent
    <button onclick=@OnOK>OK</button>
</div>

@functions {
    public string Title { get; set; }
    public RenderFragment BodyContent { get; set; }
    public Action OnOK { get; set; }
}
```

<span data-ttu-id="ec1b3-142">Wenn diese Komponente an einer anderen Stelle in der App verwendet wird, beschleunigt IntelliSense die Entwicklung mithilfe von Syntax- und Parametervervollständigung.</span><span class="sxs-lookup"><span data-stu-id="ec1b3-142">When this component is used elsewhere in the app, IntelliSense speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="ec1b3-143">Die Komponenten werden in einer In-Memory-Darstellung des Browser-DOMs gerendert, die als *Renderbaum* bezeichnet wird und anschließend verwendet werden kann, um die Benutzeroberfläche auf flexible und effiziente Weise zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="ec1b3-143">Components render into an in-memory representation of the browser DOM called a *render tree* that can then be used to update the UI in a flexible and efficient way.</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="ec1b3-144">JavaScript-Interoperabilität</span><span class="sxs-lookup"><span data-stu-id="ec1b3-144">JavaScript interop</span></span>

<span data-ttu-id="ec1b3-145">Für Apps, die JavaScript-Bibliotheken und Browser-APIs von Drittanbietern erfordern, sind die Komponenten für die Interoperabilität mit JavaScript konzipiert.</span><span class="sxs-lookup"><span data-stu-id="ec1b3-145">For apps that require third-party JavaScript libraries and browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="ec1b3-146">Komponenten können jede Bibliothek oder API verwenden, die auch von JavaScript verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="ec1b3-146">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="ec1b3-147">C#-Code kann JavaScript-Code abfragen, und umgekehrt.</span><span class="sxs-lookup"><span data-stu-id="ec1b3-147">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="ec1b3-148">Weitere Informationen finden Sie unter [JavaScript interop (JavaScript-Interoperabilität)](xref:razor-components/javascript-interop).</span><span class="sxs-lookup"><span data-stu-id="ec1b3-148">For more information, see [JavaScript interop](xref:razor-components/javascript-interop).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ec1b3-149">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="ec1b3-149">Additional resources</span></span>

* <xref:spa/blazor/index>
* [<span data-ttu-id="ec1b3-150">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="ec1b3-150">WebAssembly</span></span>](http://webassembly.org/)
* [<span data-ttu-id="ec1b3-151">Leitfaden für C#</span><span class="sxs-lookup"><span data-stu-id="ec1b3-151">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="ec1b3-152">HTML</span><span class="sxs-lookup"><span data-stu-id="ec1b3-152">HTML</span></span>](https://www.w3.org/html/)

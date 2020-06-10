---
title: "title: 'Fortgeschrittene ASP.NET Core-Blazor-Szenarios' author: description: 'Informieren Sie sich über erweiterte Szenarios in Blazor einschließlich der Integration manueller RenderTreeBuilder-Logik in eine App.'"
author: guardrex
description: 'monikerRange: ms.author: ms.custom: ms.date: no-loc:'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 3345f545e230ada78e6c66fc9eb049060d5794d6
ms.sourcegitcommit: 04eeffe9f2f07aaa9e04720adb2e37245f5ce17d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/26/2020
ms.locfileid: "83851158"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a><span data-ttu-id="8620f-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8620f-103">'Blazor'</span></span>

<span data-ttu-id="8620f-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8620f-104">'Identity'</span></span>

## <a name="blazor-server-circuit-handler"></a><span data-ttu-id="8620f-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8620f-105">'Let's Encrypt'</span></span>

<span data-ttu-id="8620f-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8620f-106">'Razor'</span></span> <span data-ttu-id="8620f-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8620f-107">'SignalR' uid:</span></span> <span data-ttu-id="8620f-108">Erweiterte ASP.NET Core-Blazor-Szenarios</span><span class="sxs-lookup"><span data-stu-id="8620f-108">ASP.NET Core Blazor advanced scenarios</span></span>

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => circuits.Count;
}
```

<span data-ttu-id="8620f-109">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="8620f-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span> Blazor<span data-ttu-id="8620f-110"> Server-Verbindungshandler</span><span class="sxs-lookup"><span data-stu-id="8620f-110"> Server circuit handler</span></span> <span data-ttu-id="8620f-111">In Blazor Server kann mithilfe von Code ein *Verbindungshandler* definiert werden, mit dem Code für Zustandsänderungen einer Benutzerverbindung ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="8620f-111">Blazor Server allows code to define a *circuit handler*, which allows running code on changes to the state of a user's circuit.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="8620f-112">Ein Verbindungshandler wird durch das Ableiten von `CircuitHandler` und Registrieren der Klasse im Dienstcontainer der App implementiert.</span><span class="sxs-lookup"><span data-stu-id="8620f-112">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="8620f-113">Im folgenden Beispiel eines Verbindungshandlers werden geöffnete SignalR-Verbindungen nachverfolgt:</span><span class="sxs-lookup"><span data-stu-id="8620f-113">The following example of a circuit handler tracks open SignalR connections:</span></span>

<span data-ttu-id="8620f-114">Verbindungshandler werden mithilfe von DI registriert.</span><span class="sxs-lookup"><span data-stu-id="8620f-114">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="8620f-115">Bereichsbezogene Instanzen werden pro Verbindungsinstanz erstellt.</span><span class="sxs-lookup"><span data-stu-id="8620f-115">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="8620f-116">Mithilfe von `TrackingCircuitHandler` aus dem vorherigen Beispiel wird ein Singletondienst erstellt, da der Zustand aller Verbindungen nachverfolgt werden muss:</span><span class="sxs-lookup"><span data-stu-id="8620f-116">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="8620f-117">Wenn die Methoden eines benutzerdefinierten Verbindungshandlers einen Ausnahmefehler auslöst, ist dieser Ausnahmefehler für die Blazor Server-Verbindung schwerwiegend.</span><span class="sxs-lookup"><span data-stu-id="8620f-117">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the Blazor Server circuit.</span></span>

<span data-ttu-id="8620f-118">Umschließen Sie den Code in einer oder mehreren [try-catch](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisungen mit Fehlerbehandlung und Protokollierung, um Ausnahmen im Code oder in aufgerufenen Methoden eines Handlers zu tolerieren.</span><span class="sxs-lookup"><span data-stu-id="8620f-118">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

> [!NOTE]
> <span data-ttu-id="8620f-119">Wenn eine Verbindung beendet wird, weil ein Benutzer die Verbindung getrennt hat und das Framework den Verbindungsstatus bereinigt, gibt das Framework den DI-Bereich der Verbindung frei.</span><span class="sxs-lookup"><span data-stu-id="8620f-119">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="8620f-120">Wenn der Bereich freigegeben wird, werden alle Dienste im Verbindungsbereich verworfen, die <xref:System.IDisposable?displayProperty=fullName> implementieren.</span><span class="sxs-lookup"><span data-stu-id="8620f-120">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span>

<span data-ttu-id="8620f-121">Wenn ein DI-Dienst während der Freigabe einen Ausnahmefehler auslöst, protokolliert das Framework die Ausnahme.</span><span class="sxs-lookup"><span data-stu-id="8620f-121">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="8620f-122">Manuelle RenderTreeBuilder-Logik</span><span class="sxs-lookup"><span data-stu-id="8620f-122">Manual RenderTreeBuilder logic</span></span> <span data-ttu-id="8620f-123"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> stellt Methoden zum Bearbeiten von Komponenten und Elementen bereit. Dazu gehört auch das manuelle Erstellen von Komponenten in C#-Code.</span><span class="sxs-lookup"><span data-stu-id="8620f-123"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> provides methods for manipulating components and elements, including building components manually in C# code.</span></span> <span data-ttu-id="8620f-124">Die Verwendung von <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> zum Erstellen von Komponenten ist ein erweitertes Szenario.</span><span class="sxs-lookup"><span data-stu-id="8620f-124">Use of <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to create components is an advanced scenario.</span></span> <span data-ttu-id="8620f-125">Eine falsch formatierte Komponente (z. B. ein nicht geschlossenes Markuptag) kann zu undefiniertem Verhalten führen.</span><span class="sxs-lookup"><span data-stu-id="8620f-125">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span> <span data-ttu-id="8620f-126">Beachten Sie die folgende `PetDetails`-Komponente, die manuell in eine andere Komponente integriert werden kann.</span><span class="sxs-lookup"><span data-stu-id="8620f-126">Consider the following `PetDetails` component, which can be manually built into another component:</span></span> <span data-ttu-id="8620f-127">Im folgenden Beispiel generiert die Schleife in der `CreateComponent`-Methode drei `PetDetails`-Komponenten.</span><span class="sxs-lookup"><span data-stu-id="8620f-127">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span>

<span data-ttu-id="8620f-128">Wenn Sie <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>-Methoden aufrufen, um die Komponenten (`OpenComponent` und `AddAttribute`) zu erstellen, sind die Sequenznummern Zeilennummern des Quellcodes.</span><span class="sxs-lookup"><span data-stu-id="8620f-128">When calling <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span>

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> <span data-ttu-id="8620f-129">Der diff-Algorithmus von Blazor basiert auf den Sequenznummern, die unterschiedlichen Codezeilen und nicht unterschiedlichen Aufrufen entsprechen.</span><span class="sxs-lookup"><span data-stu-id="8620f-129">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="8620f-130">Hartcodieren Sie die Argumente für Sequenznummern, wenn Sie eine Komponente mit <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>-Methoden erstellen.</span><span class="sxs-lookup"><span data-stu-id="8620f-130">When creating a component with <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="8620f-131">**Die Verwendung einer Berechnung oder eines Leistungszählers zum Generieren der Sequenznummer kann zu schlechter Leistung führen.**</span><span class="sxs-lookup"><span data-stu-id="8620f-131">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="8620f-132">Weitere Informationen finden Sie im Abschnitt [Auf Codezeilennummern und nicht auf die Ausführungsreihenfolge bezogene Sequenznummern](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order).</span><span class="sxs-lookup"><span data-stu-id="8620f-132">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="8620f-133">`BuiltContent`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="8620f-133">`BuiltContent` component:</span></span> <span data-ttu-id="8620f-134">Die Typen in <xref:Microsoft.AspNetCore.Components.RenderTree> ermöglichen die Verarbeitung der *Ergebnisse* von Renderingvorgängen.</span><span class="sxs-lookup"><span data-stu-id="8620f-134">The types in <xref:Microsoft.AspNetCore.Components.RenderTree> allow processing of the *results* of rendering operations.</span></span>

<span data-ttu-id="8620f-135">Hierbei handelt es sich um interne Details der Blazor-Frameworkimplementierung.</span><span class="sxs-lookup"><span data-stu-id="8620f-135">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="8620f-136">Diese Typen sollten als *instabil* betrachtet werden und in zukünftigen Versionen geändert werden.</span><span class="sxs-lookup"><span data-stu-id="8620f-136">These types should be considered *unstable* and subject to change in future releases.</span></span> <span data-ttu-id="8620f-137">Auf Codezeilennummern und nicht auf die Ausführungsreihenfolge bezogene Sequenznummern</span><span class="sxs-lookup"><span data-stu-id="8620f-137">Sequence numbers relate to code line numbers and not execution order</span></span>

Razor<span data-ttu-id="8620f-138">-Komponentendateiel ( *.razor*) werden immer kompiliert.</span><span class="sxs-lookup"><span data-stu-id="8620f-138"> component files (*.razor*) are always compiled.</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="8620f-139">Die Kompilierung stellt gegenüber der Interpretation von Code einen Vorteil dar, da der Kompilierungsschritt zum Einfügen von Informationen verwendet werden kann, die die App-Leistung zur Laufzeit erhöhen können.</span><span class="sxs-lookup"><span data-stu-id="8620f-139">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="8620f-140">Ein wichtiges Beispiel für diese Verbesserungen sind *Sequenznummern*.</span><span class="sxs-lookup"><span data-stu-id="8620f-140">A key example of these improvements involves *sequence numbers*.</span></span>

| <span data-ttu-id="8620f-141">Sequenznummern geben der Laufzeit an, welche Ausgaben aus den unterschiedlichen und geordneten Codezeilen stammen.</span><span class="sxs-lookup"><span data-stu-id="8620f-141">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> | <span data-ttu-id="8620f-142">Diese Informationen werden von der Laufzeit verwendet, um effiziente und zeitlich lineare Strukturunterschiede zu generieren. Diese Methode ist viel schneller als es für einen allgemeinen diff-Strukturalgorithmus normalerweise möglich ist.</span><span class="sxs-lookup"><span data-stu-id="8620f-142">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>      | <span data-ttu-id="8620f-143">Sehen Sie sich die folgende Razor-Komponentendatei ( *.razor*) an:</span><span class="sxs-lookup"><span data-stu-id="8620f-143">Consider the following Razor component (*.razor*) file:</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="8620f-144">Der vorangehende Code wird in etwa wie folgt kompiliert:</span><span class="sxs-lookup"><span data-stu-id="8620f-144">The preceding code compiles to something like the following:</span></span>        | <span data-ttu-id="8620f-145">Wenn der Code zum ersten Mal ausgeführt wird, erhält der Generator Folgendes, wenn `someFlag` `true` ist:</span><span class="sxs-lookup"><span data-stu-id="8620f-145">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span> | <span data-ttu-id="8620f-146">Sequenz</span><span class="sxs-lookup"><span data-stu-id="8620f-146">Sequence</span></span>  |
| <span data-ttu-id="8620f-147">Typ</span><span class="sxs-lookup"><span data-stu-id="8620f-147">Type</span></span>        | <span data-ttu-id="8620f-148">Daten</span><span class="sxs-lookup"><span data-stu-id="8620f-148">Data</span></span> | <span data-ttu-id="8620f-149">title: 'Fortgeschrittene ASP.NET Core-Blazor-Szenarios' author: description: 'Informieren Sie sich über erweiterte Szenarios in Blazor einschließlich der Integration manueller RenderTreeBuilder-Logik in eine App.'</span><span class="sxs-lookup"><span data-stu-id="8620f-149">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span> |

<span data-ttu-id="8620f-150">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8620f-150">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="8620f-151">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8620f-151">'Blazor'</span></span>

| <span data-ttu-id="8620f-152">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8620f-152">'Identity'</span></span> | <span data-ttu-id="8620f-153">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8620f-153">'Let's Encrypt'</span></span>       | <span data-ttu-id="8620f-154">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8620f-154">'Razor'</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="8620f-155">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8620f-155">'SignalR' uid:</span></span>        | <span data-ttu-id="8620f-156">---: | --- title: 'Fortgeschrittene ASP.NET Core-Blazor-Szenarios' author: description: 'Informieren Sie sich über erweiterte Szenarios in Blazor einschließlich der Integration manueller RenderTreeBuilder-Logik in eine App.'</span><span class="sxs-lookup"><span data-stu-id="8620f-156">---: | --- title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>  | <span data-ttu-id="8620f-157">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8620f-157">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> |

<span data-ttu-id="8620f-158">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8620f-158">'Blazor'</span></span>

* <span data-ttu-id="8620f-159">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8620f-159">'Identity'</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="8620f-160">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8620f-160">'Let's Encrypt'</span></span>

<span data-ttu-id="8620f-161">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8620f-161">'Razor'</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="8620f-162">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8620f-162">'SignalR' uid:</span></span>

| <span data-ttu-id="8620f-163">title: 'Fortgeschrittene ASP.NET Core-Blazor-Szenarios' author: description: 'Informieren Sie sich über erweiterte Szenarios in Blazor einschließlich der Integration manueller RenderTreeBuilder-Logik in eine App.'</span><span class="sxs-lookup"><span data-stu-id="8620f-163">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span> | <span data-ttu-id="8620f-164">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8620f-164">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>      | <span data-ttu-id="8620f-165">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8620f-165">'Blazor'</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="8620f-166">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8620f-166">'Identity'</span></span>        | <span data-ttu-id="8620f-167">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8620f-167">'Let's Encrypt'</span></span> | <span data-ttu-id="8620f-168">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8620f-168">'Razor'</span></span>  |
| <span data-ttu-id="8620f-169">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8620f-169">'SignalR' uid:</span></span>        | <span data-ttu-id="8620f-170">----- | :----: | | 0        | Textknoten | Erster  | | 1        | Textknoten | Zweiter |</span><span class="sxs-lookup"><span data-stu-id="8620f-170">----- | :----: | | 0        | Text node | First  | | 1        | Text node | Second |</span></span> | <span data-ttu-id="8620f-171">Stellen Sie sich vor, dass `someFlag` `false` wird und das Markup wieder gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="8620f-171">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> |

<span data-ttu-id="8620f-172">Dieses Mal empfängt der Generator Folgendes:</span><span class="sxs-lookup"><span data-stu-id="8620f-172">This time, the builder receives:</span></span> <span data-ttu-id="8620f-173">Sequenz</span><span class="sxs-lookup"><span data-stu-id="8620f-173">Sequence</span></span>

| <span data-ttu-id="8620f-174">Typ</span><span class="sxs-lookup"><span data-stu-id="8620f-174">Type</span></span> | <span data-ttu-id="8620f-175">Daten</span><span class="sxs-lookup"><span data-stu-id="8620f-175">Data</span></span>      | <span data-ttu-id="8620f-176">title: 'Fortgeschrittene ASP.NET Core-Blazor-Szenarios' author: description: 'Informieren Sie sich über erweiterte Szenarios in Blazor einschließlich der Integration manueller RenderTreeBuilder-Logik in eine App.'</span><span class="sxs-lookup"><span data-stu-id="8620f-176">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="8620f-177">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8620f-177">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>        | <span data-ttu-id="8620f-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8620f-178">'Blazor'</span></span> | <span data-ttu-id="8620f-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8620f-179">'Identity'</span></span> |

<span data-ttu-id="8620f-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8620f-180">'Let's Encrypt'</span></span>

* <span data-ttu-id="8620f-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8620f-181">'Razor'</span></span>
* <span data-ttu-id="8620f-182">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8620f-182">'SignalR' uid:</span></span>

<span data-ttu-id="8620f-183">---: | --- title: 'Fortgeschrittene ASP.NET Core-Blazor-Szenarios' author: description: 'Informieren Sie sich über erweiterte Szenarios in Blazor einschließlich der Integration manueller RenderTreeBuilder-Logik in eine App.'</span><span class="sxs-lookup"><span data-stu-id="8620f-183">---: | --- title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span> <span data-ttu-id="8620f-184">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8620f-184">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="8620f-185">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8620f-185">'Blazor'</span></span> <span data-ttu-id="8620f-186">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8620f-186">'Identity'</span></span> <span data-ttu-id="8620f-187">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8620f-187">'Let's Encrypt'</span></span> <span data-ttu-id="8620f-188">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8620f-188">'Razor'</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="8620f-189">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8620f-189">'SignalR' uid:</span></span>

* <span data-ttu-id="8620f-190">title: 'Fortgeschrittene ASP.NET Core-Blazor-Szenarios' author: description: 'Informieren Sie sich über erweiterte Szenarios in Blazor einschließlich der Integration manueller RenderTreeBuilder-Logik in eine App.'</span><span class="sxs-lookup"><span data-stu-id="8620f-190">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
* <span data-ttu-id="8620f-191">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8620f-191">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="8620f-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8620f-192">'Blazor'</span></span> <span data-ttu-id="8620f-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8620f-193">'Identity'</span></span> <span data-ttu-id="8620f-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8620f-194">'Let's Encrypt'</span></span> <span data-ttu-id="8620f-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8620f-195">'Razor'</span></span>
* <span data-ttu-id="8620f-196">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8620f-196">'SignalR' uid:</span></span> <span data-ttu-id="8620f-197">title: 'Fortgeschrittene ASP.NET Core-Blazor-Szenarios' author: description: 'Informieren Sie sich über erweiterte Szenarios in Blazor einschließlich der Integration manueller RenderTreeBuilder-Logik in eine App.'</span><span class="sxs-lookup"><span data-stu-id="8620f-197">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span> <span data-ttu-id="8620f-198">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8620f-198">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> 
* <span data-ttu-id="8620f-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8620f-199">'Blazor'</span></span> <span data-ttu-id="8620f-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8620f-200">'Identity'</span></span>

## <a name="perform-large-data-transfers-in-blazor-server-apps"></a><span data-ttu-id="8620f-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8620f-201">'Let's Encrypt'</span></span>

<span data-ttu-id="8620f-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8620f-202">'Razor'</span></span> <span data-ttu-id="8620f-203">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8620f-203">'SignalR' uid:</span></span>

* <span data-ttu-id="8620f-204">----- | :----: | | 1        | Textknoten  | Zweiter |</span><span class="sxs-lookup"><span data-stu-id="8620f-204">----- | :----: | | 1        | Text node  | Second |</span></span>
* <span data-ttu-id="8620f-205">Wenn die Laufzeit einen diff-Algorithmus ausführt, wird angezeigt, dass das Element bei Sequenz `0` entfernt wurde. Daraufhin wird das folgende triviale *Bearbeitungsskript* generiert:</span><span class="sxs-lookup"><span data-stu-id="8620f-205">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

<span data-ttu-id="8620f-206">Entfernen Sie den ersten Textknoten.</span><span class="sxs-lookup"><span data-stu-id="8620f-206">Remove the first text node.</span></span>

<span data-ttu-id="8620f-207">Problem beim programmgesteuerten Generieren von Sequenznummern</span><span class="sxs-lookup"><span data-stu-id="8620f-207">The problem with generating sequence numbers programmatically</span></span>

* <span data-ttu-id="8620f-208">Angenommen, Sie haben stattdessen die folgende Buildlogik für die Renderstruktur geschrieben:</span><span class="sxs-lookup"><span data-stu-id="8620f-208">Imagine instead that you wrote the following render tree builder logic:</span></span>
* <span data-ttu-id="8620f-209">Die erste Ausgabe lautet nun wie folgt:</span><span class="sxs-lookup"><span data-stu-id="8620f-209">Now, the first output is:</span></span>
* <span data-ttu-id="8620f-210">Sequenz</span><span class="sxs-lookup"><span data-stu-id="8620f-210">Sequence</span></span>
* <span data-ttu-id="8620f-211">Typ</span><span class="sxs-lookup"><span data-stu-id="8620f-211">Type</span></span>
* <span data-ttu-id="8620f-212">Daten</span><span class="sxs-lookup"><span data-stu-id="8620f-212">Data</span></span>
  * <span data-ttu-id="8620f-213">title: 'Fortgeschrittene ASP.NET Core-Blazor-Szenarios' author: description: 'Informieren Sie sich über erweiterte Szenarios in Blazor einschließlich der Integration manueller RenderTreeBuilder-Logik in eine App.'</span><span class="sxs-lookup"><span data-stu-id="8620f-213">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
  * <span data-ttu-id="8620f-214">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8620f-214">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="8620f-215">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8620f-215">'Blazor'</span></span>
  * <span data-ttu-id="8620f-216">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8620f-216">'Identity'</span></span>
  * <span data-ttu-id="8620f-217">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8620f-217">'Let's Encrypt'</span></span> <span data-ttu-id="8620f-218">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8620f-218">'Razor'</span></span>

<span data-ttu-id="8620f-219">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8620f-219">'SignalR' uid:</span></span> <span data-ttu-id="8620f-220">---: | --- title: 'Fortgeschrittene ASP.NET Core-Blazor-Szenarios' author: description: 'Informieren Sie sich über erweiterte Szenarios in Blazor einschließlich der Integration manueller RenderTreeBuilder-Logik in eine App.'</span><span class="sxs-lookup"><span data-stu-id="8620f-220">---: | --- title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>

* <span data-ttu-id="8620f-221">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8620f-221">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="8620f-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8620f-222">'Blazor'</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime jsRuntime;
    private readonly int segmentSize = 6144;
    private readonly int maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> thisReference;
    private List<IMemoryOwner<byte>> uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        this.jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          uploadedSegments.Add(current);
        }

        var segments = uploadedSegments;
        uploadedSegments = null;

        return new SegmentedStream(segments, segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (uploadedSegments != null)
        {
            foreach (var segment in uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

<span data-ttu-id="8620f-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8620f-223">'Identity'</span></span>

* <span data-ttu-id="8620f-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8620f-224">'Let's Encrypt'</span></span>
* <span data-ttu-id="8620f-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8620f-225">'Razor'</span></span>
* <span data-ttu-id="8620f-226">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8620f-226">'SignalR' uid:</span></span>
  * <span data-ttu-id="8620f-227">title: 'Fortgeschrittene ASP.NET Core-Blazor-Szenarios' author: description: 'Informieren Sie sich über erweiterte Szenarios in Blazor einschließlich der Integration manueller RenderTreeBuilder-Logik in eine App.'</span><span class="sxs-lookup"><span data-stu-id="8620f-227">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
  * <span data-ttu-id="8620f-228">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8620f-228">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
  * <span data-ttu-id="8620f-229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8620f-229">'Blazor'</span></span> <span data-ttu-id="8620f-230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8620f-230">'Identity'</span></span> <span data-ttu-id="8620f-231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8620f-231">'Let's Encrypt'</span></span>
  * <span data-ttu-id="8620f-232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8620f-232">'Razor'</span></span>
  * <span data-ttu-id="8620f-233">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8620f-233">'SignalR' uid:</span></span>

<span data-ttu-id="8620f-234">----- | :----: | | 0        | Textknoten | Erster  | | 1        | Textknoten | Zweiter |</span><span class="sxs-lookup"><span data-stu-id="8620f-234">----- | :----: | | 0        | Text node | First  | | 1        | Text node | Second |</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> sequence;
    private long currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            sequence = new ReadOnlySequence<byte>(
                segments[0].Memory.Slice(0, lastSegmentSize));
            return;
        }

        var sequenceSegment = new BufferSegment<byte>(
            segments[0].Memory.Slice(0, segmentSize));
        var lastSegment = sequenceSegment;

        for (int i = 1; i < segments.Count; i++)
        {
            var isLastSegment = i + 1 == segments.Count;
            lastSegment = lastSegment.Append(segments[i].Memory.Slice(
                0, isLastSegment ? lastSegmentSize : segmentSize));
        }

        sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(currentPosition + count < sequence.Length ? 
            count : sequence.Length - currentPosition);
        var data = sequence.Slice(currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        currentPosition += bytesToWrite;

        return bytesToWrite;
    }

    private class BufferSegment<T> : ReadOnlySequenceSegment<T>
    {
        public BufferSegment(ReadOnlyMemory<T> memory)
        {
            Memory = memory;
        }

        public BufferSegment<T> Append(ReadOnlyMemory<T> memory)
        {
            var segment = new BufferSegment<T>(memory)
            {
                RunningIndex = RunningIndex + Memory.Length
            };

            Next = segment;

            return segment;
        }
    }

    public override bool CanRead => true;

    public override bool CanSeek => false;

    public override bool CanWrite => false;

    public override long Length => throw new NotImplementedException();

    public override void Flush() => throw new NotImplementedException();

    public override long Seek(long offset, SeekOrigin origin) => 
        throw new NotImplementedException();

    public override void SetLength(long value) => 
        throw new NotImplementedException();

    public override void Write(byte[] buffer, int offset, int count) => 
        throw new NotImplementedException();
}
```

<span data-ttu-id="8620f-235">Dieses Ergebnis ist mit dem des vorherigen Falls identisch, sodass keine negativen Probleme aufgetreten sind.</span><span class="sxs-lookup"><span data-stu-id="8620f-235">This outcome is identical to the prior case, so no negative issues exist.</span></span>

```javascript
function getFileSize(selector) {
  const file = getFile(selector);
  return file.size;
}

async function receiveSegment(segmentNumber, selector) {
  const file = getFile(selector);
  var segments = getFileSegments(file);
  var index = segmentNumber * 6144;
  return await getNextChunk(file, index);
}

function getFile(selector) {
  const element = document.querySelector(selector);
  if (!element) {
    throw new Error('Invalid selector');
  }
  const files = element.files;
  if (!files || files.length === 0) {
    throw new Error(`Element ${elementId} doesn't contain any files.`);
  }
  const file = files[0];
  return file;
}

function getFileSegments(file) {
  const segments = Math.floor(size % 6144 === 0 ? size / 6144 : 1 + size / 6144);
  return segments;
}

async function getNextChunk(file, index) {
  const length = file.size - index <= 6144 ? file.size - index : 6144;
  const chunk = file.slice(index, index + length);
  index += length;
  const base64Chunk = await this.base64EncodeAsync(chunk);
  return { base64Chunk, index };
}

async function base64EncodeAsync(chunk) {
  const reader = new FileReader();
  const result = new Promise((resolve, reject) => {
    reader.addEventListener('load',
      () => {
        const base64Chunk = reader.result;
        const cleanChunk = 
          base64Chunk.replace('data:application/octet-stream;base64,', '');
        resolve(cleanChunk);
      },
      false);
    reader.addEventListener('error', reject);
  });
  reader.readAsDataURL(chunk);
  return result;
}
```

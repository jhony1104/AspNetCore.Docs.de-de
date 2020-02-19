---
title: ASP.net Core Blazor Erweiterte Szenarien
author: guardrex
description: Erfahren Sie mehr über erweiterte Szenarien in Blazor, einschließlich der Integration von manueller rendertreebuilder-Logik in eine APP.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 5e0618faa7b1b5e4cc15e30d9c16afaf7ccabaf0
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453181"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a><span data-ttu-id="50863-103">Erweiterte ASP.net Core blazor-Szenarios</span><span class="sxs-lookup"><span data-stu-id="50863-103">ASP.NET Core Blazor advanced scenarios</span></span>

<span data-ttu-id="50863-104">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="50863-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="50863-105">Manuelle rendertreebuilder-Logik</span><span class="sxs-lookup"><span data-stu-id="50863-105">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="50863-106">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` stellt Methoden zum Bearbeiten von Komponenten und Elementen bereit, einschließlich des manuellen C# erbauens von Komponenten im Code.</span><span class="sxs-lookup"><span data-stu-id="50863-106">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="50863-107">Die Verwendung von `RenderTreeBuilder` zum Erstellen von Komponenten ist ein erweitertes Szenario.</span><span class="sxs-lookup"><span data-stu-id="50863-107">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="50863-108">Eine falsch formatierte Komponente (z. b. ein nicht geschlossenes Markup Kennzeichen) kann zu undefiniertem Verhalten führen.</span><span class="sxs-lookup"><span data-stu-id="50863-108">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="50863-109">Beachten Sie die folgende `PetDetails`-Komponente, die manuell in eine andere-Komponente integriert werden kann:</span><span class="sxs-lookup"><span data-stu-id="50863-109">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="50863-110">Im folgenden Beispiel generiert die-Schleife in der `CreateComponent`-Methode drei `PetDetails`-Komponenten.</span><span class="sxs-lookup"><span data-stu-id="50863-110">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="50863-111">Wenn Sie `RenderTreeBuilder` Methoden aufrufen, um die Komponenten (`OpenComponent` und `AddAttribute`) zu erstellen, sind die Sequenznummern Quell Codezeilen Nummern.</span><span class="sxs-lookup"><span data-stu-id="50863-111">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="50863-112">Der blazor Difference-Algorithmus basiert auf den Sequenznummern, die unterschiedlichen Codezeilen entsprechen, nicht unterschiedlichen Aufruf aufrufen.</span><span class="sxs-lookup"><span data-stu-id="50863-112">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="50863-113">Wenn Sie eine Komponente mit `RenderTreeBuilder`-Methoden erstellen, hart codieren Sie die Argumente für Sequenznummern.</span><span class="sxs-lookup"><span data-stu-id="50863-113">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="50863-114">**Die Verwendung einer Berechnung oder eines Leistungs Zählers, um die Sequenznummer zu generieren, kann zu schlechter Leistung führen.**</span><span class="sxs-lookup"><span data-stu-id="50863-114">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="50863-115">Weitere Informationen finden Sie im Abschnitt [Sequenznummern im Zusammenhang mit Codezeilen Nummern und nicht in der Ausführungsreihenfolge](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .</span><span class="sxs-lookup"><span data-stu-id="50863-115">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="50863-116">`BuiltContent` Komponente:</span><span class="sxs-lookup"><span data-stu-id="50863-116">`BuiltContent` component:</span></span>

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
> <span data-ttu-id="50863-117">Die Typen in `Microsoft.AspNetCore.Components.RenderTree` die Verarbeitung der *Ergebnisse* von Renderingvorgängen zulassen.</span><span class="sxs-lookup"><span data-stu-id="50863-117">The types in `Microsoft.AspNetCore.Components.RenderTree` allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="50863-118">Hierbei handelt es sich um interne Details der blazor-Framework-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="50863-118">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="50863-119">Diese Typen sollten als *instabil* eingestuft werden und in zukünftigen Versionen geändert werden können.</span><span class="sxs-lookup"><span data-stu-id="50863-119">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="50863-120">Sequenznummern beziehen sich auf Codezeilen Nummern und keine Ausführungsreihenfolge.</span><span class="sxs-lookup"><span data-stu-id="50863-120">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="50863-121">Razor-Komponenten Dateien *(Razor*) werden immer kompiliert.</span><span class="sxs-lookup"><span data-stu-id="50863-121">Razor component files (*.razor*) are always compiled.</span></span> <span data-ttu-id="50863-122">Die Kompilierung ist ein potenzieller Vorteil gegenüber der Interpretation von Code, da der Kompilierungs Schritt zum Einfügen von Informationen verwendet werden kann, die die APP-Leistung zur Laufzeit erhöhen</span><span class="sxs-lookup"><span data-stu-id="50863-122">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="50863-123">Ein wichtiges Beispiel für diese Verbesserungen sind *Sequenznummern*.</span><span class="sxs-lookup"><span data-stu-id="50863-123">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="50863-124">Sequenznummern geben der Laufzeit an, welche Ausgaben aus den unterschiedlichen und geordneten Codezeilen stammen.</span><span class="sxs-lookup"><span data-stu-id="50863-124">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="50863-125">Diese Informationen werden von der Common Language Runtime verwendet, um effiziente Strukturunterschiede in linearer Zeit zu generieren, die wesentlich schneller ist als bei einem allgemeinen Struktur Vergleichsalgorithmus.</span><span class="sxs-lookup"><span data-stu-id="50863-125">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="50863-126">Sehen Sie sich die folgende Razor-Komponenten Datei ( *. Razor*) an:</span><span class="sxs-lookup"><span data-stu-id="50863-126">Consider the following Razor component (*.razor*) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="50863-127">Der vorangehende Code wird in etwa wie folgt kompiliert:</span><span class="sxs-lookup"><span data-stu-id="50863-127">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="50863-128">Wenn der Code zum ersten Mal ausgeführt wird, erhält der Generator Folgendes, wenn `someFlag` `true`ist:</span><span class="sxs-lookup"><span data-stu-id="50863-128">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="50863-129">Sequenz</span><span class="sxs-lookup"><span data-stu-id="50863-129">Sequence</span></span> | <span data-ttu-id="50863-130">Typ</span><span class="sxs-lookup"><span data-stu-id="50863-130">Type</span></span>      | <span data-ttu-id="50863-131">Daten</span><span class="sxs-lookup"><span data-stu-id="50863-131">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="50863-132">0</span><span class="sxs-lookup"><span data-stu-id="50863-132">0</span></span>        | <span data-ttu-id="50863-133">Textknoten</span><span class="sxs-lookup"><span data-stu-id="50863-133">Text node</span></span> | <span data-ttu-id="50863-134">First</span><span class="sxs-lookup"><span data-stu-id="50863-134">First</span></span>  |
| <span data-ttu-id="50863-135">1</span><span class="sxs-lookup"><span data-stu-id="50863-135">1</span></span>        | <span data-ttu-id="50863-136">Textknoten</span><span class="sxs-lookup"><span data-stu-id="50863-136">Text node</span></span> | <span data-ttu-id="50863-137">Sekunde</span><span class="sxs-lookup"><span data-stu-id="50863-137">Second</span></span> |

<span data-ttu-id="50863-138">Stellen Sie sich vor, dass `someFlag` `false`wird und das Markup wieder gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="50863-138">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="50863-139">Dieses Mal empfängt der Generator Folgendes:</span><span class="sxs-lookup"><span data-stu-id="50863-139">This time, the builder receives:</span></span>

| <span data-ttu-id="50863-140">Sequenz</span><span class="sxs-lookup"><span data-stu-id="50863-140">Sequence</span></span> | <span data-ttu-id="50863-141">Typ</span><span class="sxs-lookup"><span data-stu-id="50863-141">Type</span></span>       | <span data-ttu-id="50863-142">Daten</span><span class="sxs-lookup"><span data-stu-id="50863-142">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="50863-143">1</span><span class="sxs-lookup"><span data-stu-id="50863-143">1</span></span>        | <span data-ttu-id="50863-144">Textknoten</span><span class="sxs-lookup"><span data-stu-id="50863-144">Text node</span></span>  | <span data-ttu-id="50863-145">Sekunde</span><span class="sxs-lookup"><span data-stu-id="50863-145">Second</span></span> |

<span data-ttu-id="50863-146">Wenn die Laufzeit einen diff ausführt, wird angezeigt, dass das Element bei Sequenz `0` entfernt wurde, sodass das folgende triviale *Bearbeitungs Skript*generiert wird:</span><span class="sxs-lookup"><span data-stu-id="50863-146">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="50863-147">Entfernen Sie den ersten Textknoten.</span><span class="sxs-lookup"><span data-stu-id="50863-147">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="50863-148">Das Problem beim programmgesteuerten Erstellen von Sequenznummern</span><span class="sxs-lookup"><span data-stu-id="50863-148">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="50863-149">Angenommen, Sie haben die folgende Logik des Renderstruktur-Generators geschrieben:</span><span class="sxs-lookup"><span data-stu-id="50863-149">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="50863-150">Nun lautet die erste Ausgabe wie folgt:</span><span class="sxs-lookup"><span data-stu-id="50863-150">Now, the first output is:</span></span>

| <span data-ttu-id="50863-151">Sequenz</span><span class="sxs-lookup"><span data-stu-id="50863-151">Sequence</span></span> | <span data-ttu-id="50863-152">Typ</span><span class="sxs-lookup"><span data-stu-id="50863-152">Type</span></span>      | <span data-ttu-id="50863-153">Daten</span><span class="sxs-lookup"><span data-stu-id="50863-153">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="50863-154">0</span><span class="sxs-lookup"><span data-stu-id="50863-154">0</span></span>        | <span data-ttu-id="50863-155">Textknoten</span><span class="sxs-lookup"><span data-stu-id="50863-155">Text node</span></span> | <span data-ttu-id="50863-156">First</span><span class="sxs-lookup"><span data-stu-id="50863-156">First</span></span>  |
| <span data-ttu-id="50863-157">1</span><span class="sxs-lookup"><span data-stu-id="50863-157">1</span></span>        | <span data-ttu-id="50863-158">Textknoten</span><span class="sxs-lookup"><span data-stu-id="50863-158">Text node</span></span> | <span data-ttu-id="50863-159">Sekunde</span><span class="sxs-lookup"><span data-stu-id="50863-159">Second</span></span> |

<span data-ttu-id="50863-160">Dieses Ergebnis ist mit dem vorherigen Fall identisch, sodass keine negativen Probleme aufgetreten sind.</span><span class="sxs-lookup"><span data-stu-id="50863-160">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="50863-161">`someFlag` wird im zweiten Rendering `false`, und die Ausgabe lautet:</span><span class="sxs-lookup"><span data-stu-id="50863-161">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="50863-162">Sequenz</span><span class="sxs-lookup"><span data-stu-id="50863-162">Sequence</span></span> | <span data-ttu-id="50863-163">Typ</span><span class="sxs-lookup"><span data-stu-id="50863-163">Type</span></span>      | <span data-ttu-id="50863-164">Daten</span><span class="sxs-lookup"><span data-stu-id="50863-164">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="50863-165">0</span><span class="sxs-lookup"><span data-stu-id="50863-165">0</span></span>        | <span data-ttu-id="50863-166">Textknoten</span><span class="sxs-lookup"><span data-stu-id="50863-166">Text node</span></span> | <span data-ttu-id="50863-167">Sekunde</span><span class="sxs-lookup"><span data-stu-id="50863-167">Second</span></span> |

<span data-ttu-id="50863-168">Dieses Mal sieht der Vergleichsalgorithmus, dass *zwei* Änderungen aufgetreten sind, und der Algorithmus generiert das folgende Bearbeitungs Skript:</span><span class="sxs-lookup"><span data-stu-id="50863-168">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="50863-169">Ändern Sie den Wert für den ersten Textknoten in `Second`.</span><span class="sxs-lookup"><span data-stu-id="50863-169">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="50863-170">Entfernen Sie den zweiten Textknoten.</span><span class="sxs-lookup"><span data-stu-id="50863-170">Remove the second text node.</span></span>

<span data-ttu-id="50863-171">Das Erzeugen der Sequenznummern hat alle nützlichen Informationen dazu verloren, wo die `if/else` branches und Schleifen im ursprünglichen Code vorhanden waren.</span><span class="sxs-lookup"><span data-stu-id="50863-171">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="50863-172">Dies führt **doppelt so lange** wie zuvor zu einem diff.</span><span class="sxs-lookup"><span data-stu-id="50863-172">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="50863-173">Dies ist ein sehr einfaches Beispiel.</span><span class="sxs-lookup"><span data-stu-id="50863-173">This is a trivial example.</span></span> <span data-ttu-id="50863-174">In realistischeren Fällen mit komplexen und tief geschachtelten Strukturen und vor allem mit Schleifen sind die Leistungskosten in der Regel höher.</span><span class="sxs-lookup"><span data-stu-id="50863-174">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="50863-175">Anstatt sofort festzustellen, welche Schleifen Blöcke oder Verzweigungen eingefügt oder entfernt wurden, muss der diff-Algorithmus tief in die renderstrukturen rekurdieren.</span><span class="sxs-lookup"><span data-stu-id="50863-175">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="50863-176">Dies führt in der Regel dazu, dass längere Bearbeitungs Skripts erstellt werden müssen, da der diff-Algorithmus falsch formatiert ist, wie sich die alten und neuen Strukturen voneinander unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="50863-176">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="50863-177">Anleitungen und Schlussfolgerungen</span><span class="sxs-lookup"><span data-stu-id="50863-177">Guidance and conclusions</span></span>

* <span data-ttu-id="50863-178">Die APP-Leistung leidet, wenn Sequenznummern dynamisch generiert werden.</span><span class="sxs-lookup"><span data-stu-id="50863-178">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="50863-179">Das Framework kann zur Laufzeit automatisch keine eigenen Sequenznummern erstellen, da die erforderlichen Informationen nicht vorhanden sind, es sei denn, Sie werden zur Kompilierzeit aufgezeichnet.</span><span class="sxs-lookup"><span data-stu-id="50863-179">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="50863-180">Schreiben Sie keine langen Blöcke der manuell implementierten `RenderTreeBuilder` Logik.</span><span class="sxs-lookup"><span data-stu-id="50863-180">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="50863-181">Bevorzugen Sie *Razor* -Dateien, und ermöglichen Sie es dem Compiler, sich mit den Sequenznummern zu beschäftigen.</span><span class="sxs-lookup"><span data-stu-id="50863-181">Prefer *.razor* files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="50863-182">Wenn Sie manuelle `RenderTreeBuilder` Logik nicht vermeiden können, teilen Sie lange Code Blöcke in kleinere Teile auf, die `OpenRegion`/`CloseRegion` aufrufen umschließen.</span><span class="sxs-lookup"><span data-stu-id="50863-182">If you're unable to avoid manual `RenderTreeBuilder` logic, split long blocks of code into smaller pieces wrapped in `OpenRegion`/`CloseRegion` calls.</span></span> <span data-ttu-id="50863-183">Jede Region verfügt über einen eigenen separaten Bereich von Sequenznummern, sodass Sie in jeder Region von NULL (oder einer beliebigen anderen beliebigen Zahl) neu starten können.</span><span class="sxs-lookup"><span data-stu-id="50863-183">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="50863-184">Wenn Sequenznummern hart codiert sind, erfordert der Vergleichsalgorithmus nur, dass die Sequenznummern den Wert erhöhen.</span><span class="sxs-lookup"><span data-stu-id="50863-184">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="50863-185">Die Anfangswerte und Lücken sind irrelevant.</span><span class="sxs-lookup"><span data-stu-id="50863-185">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="50863-186">Eine berechtigte Option besteht darin, die Codezeilen Nummer als Sequenznummer zu verwenden oder von NULL zu beginnen und um Werte oder Hunderte (oder ein beliebiges bevorzugtes Intervall) zu erhöhen.</span><span class="sxs-lookup"><span data-stu-id="50863-186">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* Blazor<span data-ttu-id="50863-187"> verwendet Sequenznummern, während andere Benutzeroberflächen-Frameworks, die Sie nicht verwenden, diese verwenden.</span><span class="sxs-lookup"><span data-stu-id="50863-187"> uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="50863-188">Das diffalling ist weitaus schneller, wenn Sequenznummern verwendet werden, und Blazor hat den Vorteil eines Kompilierungs Schritts, der automatisch Sequenznummern für Entwickler erstellt, die *Razor* -Dateien erstellen.</span><span class="sxs-lookup"><span data-stu-id="50863-188">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring *.razor* files.</span></span>

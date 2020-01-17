---
title: ASP.net Core Blazor Lebenszyklus
author: guardrex
description: Erfahren Sie, wie Sie den Lebenszyklus von Razor-Komponenten in ASP.net Core Blazor-Apps verwenden.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: df5bb676df59b538179a69978040521c4ee78ed1
ms.sourcegitcommit: cbd30479f42cbb3385000ef834d9c7d021fd218d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76146367"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a><span data-ttu-id="65de8-103">ASP.net Core Blazor Lebenszyklus</span><span class="sxs-lookup"><span data-stu-id="65de8-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="65de8-104">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="65de8-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="65de8-105">Das Blazor Framework umfasst synchrone und asynchrone Lebenszyklus Methoden.</span><span class="sxs-lookup"><span data-stu-id="65de8-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="65de8-106">Überschreiben Sie Lebenszyklus Methoden, um während der Initialisierung und dem Rendering von Komponenten zusätzliche Vorgänge für Komponenten auszuführen.</span><span class="sxs-lookup"><span data-stu-id="65de8-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="65de8-107">Lebenszyklusmethoden</span><span class="sxs-lookup"><span data-stu-id="65de8-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="65de8-108">Initialisierungs Methoden für Komponenten</span><span class="sxs-lookup"><span data-stu-id="65de8-108">Component initialization methods</span></span>

<span data-ttu-id="65de8-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> werden aufgerufen, wenn die Komponente initialisiert wird, nachdem ihre ursprünglichen Parameter von der übergeordneten Komponente empfangen wurden.</span><span class="sxs-lookup"><span data-stu-id="65de8-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="65de8-110">Verwenden Sie `OnInitializedAsync`, wenn die Komponente einen asynchronen Vorgang ausführt und nach Abschluss des Vorgangs aktualisiert werden soll.</span><span class="sxs-lookup"><span data-stu-id="65de8-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span> <span data-ttu-id="65de8-111">Diese Methoden werden nur einmal aufgerufen, wenn die Komponente zum ersten Mal instanziiert wird.</span><span class="sxs-lookup"><span data-stu-id="65de8-111">These methods are only called one time when the component is first instantiated.</span></span>

<span data-ttu-id="65de8-112">Überschreiben Sie für einen synchronen Vorgang `OnInitialized`:</span><span class="sxs-lookup"><span data-stu-id="65de8-112">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="65de8-113">Um einen asynchronen Vorgang auszuführen, überschreiben Sie `OnInitializedAsync`, und verwenden Sie das Schlüsselwort `await` für den Vorgang:</span><span class="sxs-lookup"><span data-stu-id="65de8-113">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

### <a name="before-parameters-are-set"></a><span data-ttu-id="65de8-114">Vor dem Festlegen von Parametern</span><span class="sxs-lookup"><span data-stu-id="65de8-114">Before parameters are set</span></span>

<span data-ttu-id="65de8-115"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> legt Parameter fest, die vom übergeordneten Element in der Rendering-Struktur bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="65de8-115"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="65de8-116"><xref:Microsoft.AspNetCore.Components.ParameterView> enthält den gesamten Satz von Parameterwerten jedes Mal, wenn `SetParametersAsync` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="65de8-116"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="65de8-117">Die Standard Implementierung von `SetParametersAsync` legt den Wert jeder Eigenschaft mit dem `[Parameter]` oder `[CascadingParameter]` Attribut fest, das über einen entsprechenden Wert in der `ParameterView`verfügt.</span><span class="sxs-lookup"><span data-stu-id="65de8-117">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="65de8-118">Parameter, die keinen entsprechenden Wert in `ParameterView` haben, bleiben unverändert.</span><span class="sxs-lookup"><span data-stu-id="65de8-118">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="65de8-119">Wenn `base.SetParametersAync` nicht aufgerufen wird, kann der benutzerdefinierte Code den Wert eingehender Parameter in beliebiger Weise interpretieren.</span><span class="sxs-lookup"><span data-stu-id="65de8-119">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="65de8-120">Beispielsweise ist es nicht erforderlich, den Eigenschaften der-Klasse die eingehenden Parameter zuzuweisen.</span><span class="sxs-lookup"><span data-stu-id="65de8-120">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="65de8-121">Nachdem Parameter festgelegt wurden</span><span class="sxs-lookup"><span data-stu-id="65de8-121">After parameters are set</span></span>

<span data-ttu-id="65de8-122"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> werden aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="65de8-122"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> are called:</span></span>

* <span data-ttu-id="65de8-123">Wenn die Komponente initialisiert wird und ihren ersten Satz von Parametern von der übergeordneten Komponente empfangen hat.</span><span class="sxs-lookup"><span data-stu-id="65de8-123">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="65de8-124">Wenn die übergeordnete Komponente erneut gerendert wird und Folgendes bereitstellt:</span><span class="sxs-lookup"><span data-stu-id="65de8-124">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="65de8-125">Nur bekannte primitive unveränderliche Typen, von denen sich mindestens ein Parameter geändert hat.</span><span class="sxs-lookup"><span data-stu-id="65de8-125">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="65de8-126">Alle komplexen typisierten Parameter.</span><span class="sxs-lookup"><span data-stu-id="65de8-126">Any complex-typed parameters.</span></span> <span data-ttu-id="65de8-127">Das Framework weiß nicht, ob die Werte eines komplexen typisierten Parameters intern geändert wurden, sodass der Parametersatz als geändert behandelt wird.</span><span class="sxs-lookup"><span data-stu-id="65de8-127">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="65de8-128">Asynchrone Arbeit beim Anwenden von Parametern und Eigenschafts Werten muss während des `OnParametersSetAsync` Lifecycle-Ereignisses auftreten.</span><span class="sxs-lookup"><span data-stu-id="65de8-128">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

### <a name="after-component-render"></a><span data-ttu-id="65de8-129">Nach dem Rendering der Komponente</span><span class="sxs-lookup"><span data-stu-id="65de8-129">After component render</span></span>

<span data-ttu-id="65de8-130"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> werden aufgerufen, nachdem eine Komponente das Rendering abgeschlossen hat.</span><span class="sxs-lookup"><span data-stu-id="65de8-130"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> are called after a component has finished rendering.</span></span> <span data-ttu-id="65de8-131">Element-und Komponenten Verweise werden an diesem Punkt aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="65de8-131">Element and component references are populated at this point.</span></span> <span data-ttu-id="65de8-132">Verwenden Sie diese Phase, um zusätzliche Initialisierungs Schritte unter Verwendung des gerenderten Inhalts auszuführen, z. b. das Aktivieren von JavaScript-Bibliotheken von Drittanbietern, die auf den gerenderten Dom</span><span class="sxs-lookup"><span data-stu-id="65de8-132">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="65de8-133">Der `firstRender` Parameter für `OnAfterRenderAsync` und `OnAfterRender`:</span><span class="sxs-lookup"><span data-stu-id="65de8-133">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="65de8-134">Wird auf `true` festgelegt, wenn die Komponenteninstanz zum ersten Mal gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="65de8-134">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="65de8-135">Kann verwendet werden, um sicherzustellen, dass die Initialisierungs Arbeit nur einmal ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="65de8-135">Can be used to ensure that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="65de8-136">Asynchrone Arbeit unmittelbar nach dem Rendering muss während des `OnAfterRenderAsync` Lifecycle-Ereignisses stattfinden.</span><span class="sxs-lookup"><span data-stu-id="65de8-136">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="65de8-137">Auch wenn Sie eine <xref:System.Threading.Tasks.Task> von `OnAfterRenderAsync`zurückgeben, plant das Framework keinen weiteren Renderingdurchlauf für die Komponente, sobald diese Aufgabe abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="65de8-137">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="65de8-138">Dadurch wird eine unendliche Renderschleife vermieden.</span><span class="sxs-lookup"><span data-stu-id="65de8-138">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="65de8-139">Dies unterscheidet sich von den anderen Lebenszyklus Methoden, die einen weiteren renderzyklus planen, sobald die zurückgegebene Aufgabe abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="65de8-139">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="65de8-140">`OnAfterRender` und `OnAfterRenderAsync` werden *nicht aufgerufen, wenn die Vorab Anmeldung auf dem Server erfolgt.*</span><span class="sxs-lookup"><span data-stu-id="65de8-140">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="65de8-141">UI-Aktualisierung unterdrücken</span><span class="sxs-lookup"><span data-stu-id="65de8-141">Suppress UI refreshing</span></span>

<span data-ttu-id="65de8-142">Überschreiben Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*>, um die Benutzeroberfläche zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="65de8-142">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> to suppress UI refreshing.</span></span> <span data-ttu-id="65de8-143">Wenn die Implementierung `true`zurückgibt, wird die Benutzeroberfläche aktualisiert:</span><span class="sxs-lookup"><span data-stu-id="65de8-143">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="65de8-144">`ShouldRender` wird jedes Mal aufgerufen, wenn die Komponente gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="65de8-144">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="65de8-145">Auch wenn `ShouldRender` überschrieben wird, wird die Komponente immer anfänglich gerendert.</span><span class="sxs-lookup"><span data-stu-id="65de8-145">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="65de8-146">Statusänderungen</span><span class="sxs-lookup"><span data-stu-id="65de8-146">State changes</span></span>

<span data-ttu-id="65de8-147"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> benachrichtigt die Komponente, dass sich der Status geändert hat.</span><span class="sxs-lookup"><span data-stu-id="65de8-147"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifies the component that its state has changed.</span></span> <span data-ttu-id="65de8-148">Wenn dies zutrifft, bewirkt der Aufruf von `StateHasChanged`, dass die Komponente erneut ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="65de8-148">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="65de8-149">Unvollständige Async-Aktionen bei Rendering behandeln</span><span class="sxs-lookup"><span data-stu-id="65de8-149">Handle incomplete async actions at render</span></span>

<span data-ttu-id="65de8-150">Asynchrone Aktionen, die in Lebenszyklus Ereignissen ausgeführt werden, wurden möglicherweise nicht abgeschlossen, bevor die Komponente gerendert</span><span class="sxs-lookup"><span data-stu-id="65de8-150">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="65de8-151">Objekte können `null` oder nicht vollständig mit Daten aufgefüllt werden, während die Lebenszyklus Methode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="65de8-151">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="65de8-152">Stellen Sie Renderinglogik bereit, um zu bestätigen, dass Objekte initialisiert</span><span class="sxs-lookup"><span data-stu-id="65de8-152">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="65de8-153">Stellen Sie Platzhalter-Benutzeroberflächen Elemente (z. b. eine Lade Nachricht) dar, während Objekte `null`werden.</span><span class="sxs-lookup"><span data-stu-id="65de8-153">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="65de8-154">In der `FetchData` Komponente der Blazor Vorlagen wird `OnInitializedAsync` überschrieben, um Vorhersagedaten (`forecasts`) asynchron zu empfangen.</span><span class="sxs-lookup"><span data-stu-id="65de8-154">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="65de8-155">Wenn `forecasts` `null`ist, wird dem Benutzer eine Meldung zum Laden angezeigt.</span><span class="sxs-lookup"><span data-stu-id="65de8-155">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="65de8-156">Nachdem der von `OnInitializedAsync` zurückgegebene `Task` abgeschlossen ist, wird die Komponente mit dem aktualisierten Zustand erneut ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="65de8-156">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="65de8-157">*Pages/fetchdata. Razor* in der Blazor Server-Vorlage:</span><span class="sxs-lookup"><span data-stu-id="65de8-157">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="65de8-158">Komponenten Beseitigung mit iverwerf</span><span class="sxs-lookup"><span data-stu-id="65de8-158">Component disposal with IDisposable</span></span>

<span data-ttu-id="65de8-159">Wenn eine Komponente <xref:System.IDisposable>implementiert, wird die verwerfen- [Methode](/dotnet/standard/garbage-collection/implementing-dispose) aufgerufen, wenn die Komponente aus der Benutzeroberfläche entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="65de8-159">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="65de8-160">In der folgenden Komponente werden `@implements IDisposable` und die `Dispose`-Methode verwendet:</span><span class="sxs-lookup"><span data-stu-id="65de8-160">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="65de8-161">Das Aufrufen von <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="65de8-161">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` isn't supported.</span></span> <span data-ttu-id="65de8-162">`StateHasChanged` werden möglicherweise als Teil des renderens des Renderers aufgerufen, sodass das Anfordern von Aktualisierungen der Benutzeroberfläche an diesem Punkt nicht unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="65de8-162">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

## <a name="handle-errors"></a><span data-ttu-id="65de8-163">Behandeln von Fehlern</span><span class="sxs-lookup"><span data-stu-id="65de8-163">Handle errors</span></span>

<span data-ttu-id="65de8-164">Informationen zum Behandeln von Fehlern während der Ausführung der Lebenszyklus Methode finden Sie unter <xref:blazor/handle-errors#lifecycle-methods>.</span><span class="sxs-lookup"><span data-stu-id="65de8-164">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

---
title: ASP.net Core Blazor Lebenszyklus
author: guardrex
description: Erfahren Sie, wie Sie den Lebenszyklus von Razor-Komponenten in ASP.net Core Blazor-Apps verwenden.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/26/2019
no-loc:
- Blazor
uid: blazor/lifecycle
ms.openlocfilehash: 1482f6b2147c74b11836e8029401bb8bcb3cdb2d
ms.sourcegitcommit: 0dd224b2b7efca1fda0041b5c3f45080327033f6
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74681374"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a><span data-ttu-id="9786d-103">ASP.net Core Blazor Lebenszyklus</span><span class="sxs-lookup"><span data-stu-id="9786d-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="9786d-104">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="9786d-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="9786d-105">Das Blazor Framework umfasst synchrone und asynchrone Lebenszyklus Methoden.</span><span class="sxs-lookup"><span data-stu-id="9786d-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="9786d-106">Überschreiben Sie Lebenszyklus Methoden, um während der Initialisierung und dem Rendering von Komponenten zusätzliche Vorgänge für Komponenten auszuführen.</span><span class="sxs-lookup"><span data-stu-id="9786d-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="9786d-107">Lebenszyklusmethoden</span><span class="sxs-lookup"><span data-stu-id="9786d-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="9786d-108">Initialisierungs Methoden für Komponenten</span><span class="sxs-lookup"><span data-stu-id="9786d-108">Component initialization methods</span></span>

<span data-ttu-id="9786d-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> Ausführen von Code, der eine Komponente initialisiert.</span><span class="sxs-lookup"><span data-stu-id="9786d-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> execute code that initializes a component.</span></span> <span data-ttu-id="9786d-110">Diese Methoden werden nur einmal aufgerufen, wenn die Komponente zum ersten Mal instanziiert wird.</span><span class="sxs-lookup"><span data-stu-id="9786d-110">These methods are only called one time when the component is first instantiated.</span></span>

<span data-ttu-id="9786d-111">Verwenden Sie zum Ausführen eines asynchronen Vorgangs `OnInitializedAsync` und das `await`-Schlüsselwort für den Vorgang:</span><span class="sxs-lookup"><span data-stu-id="9786d-111">To perform an asynchronous operation, use `OnInitializedAsync` and the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="9786d-112">Asynchrone Arbeit während der Initialisierung der Komponente muss während des `OnInitializedAsync` Lifecycle-Ereignisses stattfinden.</span><span class="sxs-lookup"><span data-stu-id="9786d-112">Asynchronous work during component initialization must occur during the `OnInitializedAsync` lifecycle event.</span></span>

<span data-ttu-id="9786d-113">Verwenden Sie für einen synchronen Vorgang `OnInitialized`:</span><span class="sxs-lookup"><span data-stu-id="9786d-113">For a synchronous operation, use `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

### <a name="before-parameters-are-set"></a><span data-ttu-id="9786d-114">Vor dem Festlegen von Parametern</span><span class="sxs-lookup"><span data-stu-id="9786d-114">Before parameters are set</span></span>

<span data-ttu-id="9786d-115"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> legt Parameter fest, die vom übergeordneten Element in der Rendering-Struktur bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="9786d-115"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="9786d-116"><xref:Microsoft.AspNetCore.Components.ParameterView> enthält den gesamten Satz von Parameterwerten jedes Mal, wenn `SetParametersAsync` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="9786d-116"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="9786d-117">Die Standard Implementierung von `SetParametersAsync` legt den Wert jeder Eigenschaft fest, die mit dem `[Parameter]` oder `[CascadingParameter]` Attribut mit einem entsprechenden Wert in der `ParameterView`versehen ist.</span><span class="sxs-lookup"><span data-stu-id="9786d-117">The default implementation of `SetParametersAsync` sets the value of each property decorated with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="9786d-118">Parameter, die keinen entsprechenden Wert in `ParameterView` haben, bleiben unverändert.</span><span class="sxs-lookup"><span data-stu-id="9786d-118">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="9786d-119">Wenn `base.SetParametersAync` nicht aufgerufen wird, kann der benutzerdefinierte Code den Wert eingehender Parameter in beliebiger Weise interpretieren.</span><span class="sxs-lookup"><span data-stu-id="9786d-119">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="9786d-120">Beispielsweise ist es nicht erforderlich, den Eigenschaften der-Klasse die eingehenden Parameter zuzuweisen.</span><span class="sxs-lookup"><span data-stu-id="9786d-120">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="9786d-121">Nachdem Parameter festgelegt wurden</span><span class="sxs-lookup"><span data-stu-id="9786d-121">After parameters are set</span></span>

<span data-ttu-id="9786d-122"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> werden aufgerufen, wenn eine Komponente Parameter von ihrem übergeordneten Element empfangen hat und die Werte Eigenschaften zugewiesen werden.</span><span class="sxs-lookup"><span data-stu-id="9786d-122"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="9786d-123">Diese Methoden werden nach der Initialisierung der Komponente und jedes Mal, wenn neue Parameterwerte angegeben werden, ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="9786d-123">These methods are executed after component initialization and each time new parameter values are specified:</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="9786d-124">Asynchrone Arbeit beim Anwenden von Parametern und Eigenschafts Werten muss während des `OnParametersSetAsync` Lifecycle-Ereignisses auftreten.</span><span class="sxs-lookup"><span data-stu-id="9786d-124">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

### <a name="after-component-render"></a><span data-ttu-id="9786d-125">Nach dem Rendering der Komponente</span><span class="sxs-lookup"><span data-stu-id="9786d-125">After component render</span></span>

<span data-ttu-id="9786d-126"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> werden aufgerufen, nachdem eine Komponente das Rendering abgeschlossen hat.</span><span class="sxs-lookup"><span data-stu-id="9786d-126"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> are called after a component has finished rendering.</span></span> <span data-ttu-id="9786d-127">Element-und Komponenten Verweise werden an diesem Punkt aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="9786d-127">Element and component references are populated at this point.</span></span> <span data-ttu-id="9786d-128">Verwenden Sie diese Phase, um zusätzliche Initialisierungs Schritte unter Verwendung des gerenderten Inhalts auszuführen, z. b. das Aktivieren von JavaScript-Bibliotheken von Drittanbietern, die auf den gerenderten Dom</span><span class="sxs-lookup"><span data-stu-id="9786d-128">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="9786d-129">Der `firstRender` Parameter für `OnAfterRenderAsync` und `OnAfterRender`:</span><span class="sxs-lookup"><span data-stu-id="9786d-129">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="9786d-130">Wird auf `true` festgelegt, wenn die Komponenteninstanz zum ersten Mal gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="9786d-130">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="9786d-131">Kann verwendet werden, um sicherzustellen, dass die Initialisierungs Arbeit nur einmal ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="9786d-131">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="9786d-132">Asynchrone Arbeit unmittelbar nach dem Rendering muss während des `OnAfterRenderAsync` Lifecycle-Ereignisses stattfinden.</span><span class="sxs-lookup"><span data-stu-id="9786d-132">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="9786d-133">Auch wenn Sie eine <xref:System.Threading.Tasks.Task> von `OnAfterRenderAsync`zurückgeben, plant das Framework keinen weiteren Renderingdurchlauf für die Komponente, sobald diese Aufgabe abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="9786d-133">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="9786d-134">Dadurch wird eine unendliche Renderschleife vermieden.</span><span class="sxs-lookup"><span data-stu-id="9786d-134">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="9786d-135">Dies unterscheidet sich von den anderen Lebenszyklus Methoden, die einen weiteren renderzyklus planen, sobald die zurückgegebene Aufgabe abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="9786d-135">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="9786d-136">`OnAfterRender` und `OnAfterRenderAsync` werden *nicht aufgerufen, wenn die Vorab Anmeldung auf dem Server erfolgt.*</span><span class="sxs-lookup"><span data-stu-id="9786d-136">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="9786d-137">UI-Aktualisierung unterdrücken</span><span class="sxs-lookup"><span data-stu-id="9786d-137">Suppress UI refreshing</span></span>

<span data-ttu-id="9786d-138">Überschreiben Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*>, um die Benutzeroberfläche zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="9786d-138">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> to suppress UI refreshing.</span></span> <span data-ttu-id="9786d-139">Wenn die Implementierung `true`zurückgibt, wird die Benutzeroberfläche aktualisiert:</span><span class="sxs-lookup"><span data-stu-id="9786d-139">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="9786d-140">`ShouldRender` wird jedes Mal aufgerufen, wenn die Komponente gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="9786d-140">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="9786d-141">Auch wenn `ShouldRender` überschrieben wird, wird die Komponente immer anfänglich gerendert.</span><span class="sxs-lookup"><span data-stu-id="9786d-141">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="9786d-142">Statusänderungen</span><span class="sxs-lookup"><span data-stu-id="9786d-142">State changes</span></span>

<span data-ttu-id="9786d-143"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> benachrichtigt die Komponente, dass sich der Status geändert hat.</span><span class="sxs-lookup"><span data-stu-id="9786d-143"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifies the component that its state has changed.</span></span> <span data-ttu-id="9786d-144">Wenn dies zutrifft, bewirkt der Aufruf von `StateHasChanged`, dass die Komponente erneut ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="9786d-144">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="9786d-145">Unvollständige Async-Aktionen bei Rendering behandeln</span><span class="sxs-lookup"><span data-stu-id="9786d-145">Handle incomplete async actions at render</span></span>

<span data-ttu-id="9786d-146">Asynchrone Aktionen, die in Lebenszyklus Ereignissen ausgeführt werden, wurden möglicherweise nicht abgeschlossen, bevor die Komponente gerendert</span><span class="sxs-lookup"><span data-stu-id="9786d-146">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="9786d-147">Objekte können `null` oder nicht vollständig mit Daten aufgefüllt werden, während die Lebenszyklus Methode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="9786d-147">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="9786d-148">Stellen Sie Renderinglogik bereit, um zu bestätigen, dass Objekte initialisiert</span><span class="sxs-lookup"><span data-stu-id="9786d-148">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="9786d-149">Stellen Sie Platzhalter-Benutzeroberflächen Elemente (z. b. eine Lade Nachricht) dar, während Objekte `null`werden.</span><span class="sxs-lookup"><span data-stu-id="9786d-149">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="9786d-150">In der `FetchData` Komponente der Blazor Vorlagen wird `OnInitializedAsync` überschrieben, um Vorhersagedaten (`forecasts`) asynchron zu empfangen.</span><span class="sxs-lookup"><span data-stu-id="9786d-150">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="9786d-151">Wenn `forecasts` `null`ist, wird dem Benutzer eine Meldung zum Laden angezeigt.</span><span class="sxs-lookup"><span data-stu-id="9786d-151">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="9786d-152">Nachdem der von `OnInitializedAsync` zurückgegebene `Task` abgeschlossen ist, wird die Komponente mit dem aktualisierten Zustand erneut ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="9786d-152">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="9786d-153">*Pages/fetchdata. Razor* in der Blazor Server-Vorlage:</span><span class="sxs-lookup"><span data-stu-id="9786d-153">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-cshtml[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="9786d-154">Komponenten Beseitigung mit iverwerf</span><span class="sxs-lookup"><span data-stu-id="9786d-154">Component disposal with IDisposable</span></span>

<span data-ttu-id="9786d-155">Wenn eine Komponente <xref:System.IDisposable>implementiert, wird die verwerfen- [Methode](/dotnet/standard/garbage-collection/implementing-dispose) aufgerufen, wenn die Komponente aus der Benutzeroberfläche entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="9786d-155">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="9786d-156">In der folgenden Komponente werden `@implements IDisposable` und die `Dispose`-Methode verwendet:</span><span class="sxs-lookup"><span data-stu-id="9786d-156">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```csharp
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
> <span data-ttu-id="9786d-157">Das Aufrufen von <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9786d-157">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` isn't supported.</span></span> <span data-ttu-id="9786d-158">`StateHasChanged` werden möglicherweise als Teil des renderens des Renderers aufgerufen, sodass das Anfordern von Aktualisierungen der Benutzeroberfläche an diesem Punkt nicht unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="9786d-158">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

## <a name="handle-errors"></a><span data-ttu-id="9786d-159">Behandeln von Fehlern</span><span class="sxs-lookup"><span data-stu-id="9786d-159">Handle errors</span></span>

<span data-ttu-id="9786d-160">Informationen zum Behandeln von Fehlern während der Ausführung der Lebenszyklus Methode finden Sie unter <xref:blazor/handle-errors#lifecycle-methods>.</span><span class="sxs-lookup"><span data-stu-id="9786d-160">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

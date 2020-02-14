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
ms.openlocfilehash: ecacd0a9728cbefd716e9dc7cd8a8c62f3df6e0d
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77213388"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a><span data-ttu-id="5b0a7-103">ASP.net Core Blazor Lebenszyklus</span><span class="sxs-lookup"><span data-stu-id="5b0a7-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="5b0a7-104">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="5b0a7-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="5b0a7-105">Das Blazor Framework umfasst synchrone und asynchrone Lebenszyklus Methoden.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="5b0a7-106">Überschreiben Sie Lebenszyklus Methoden, um während der Initialisierung und dem Rendering von Komponenten zusätzliche Vorgänge für Komponenten auszuführen.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="5b0a7-107">Lebenszyklusmethoden</span><span class="sxs-lookup"><span data-stu-id="5b0a7-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="5b0a7-108">Initialisierungs Methoden für Komponenten</span><span class="sxs-lookup"><span data-stu-id="5b0a7-108">Component initialization methods</span></span>

<span data-ttu-id="5b0a7-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> werden aufgerufen, wenn die Komponente initialisiert wird, nachdem ihre ursprünglichen Parameter von der übergeordneten Komponente empfangen wurden.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="5b0a7-110">Verwenden Sie `OnInitializedAsync`, wenn die Komponente einen asynchronen Vorgang ausführt und nach Abschluss des Vorgangs aktualisiert werden soll.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="5b0a7-111">Überschreiben Sie für einen synchronen Vorgang `OnInitialized`:</span><span class="sxs-lookup"><span data-stu-id="5b0a7-111">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="5b0a7-112">Um einen asynchronen Vorgang auszuführen, überschreiben Sie `OnInitializedAsync`, und verwenden Sie das Schlüsselwort `await` für den Vorgang:</span><span class="sxs-lookup"><span data-stu-id="5b0a7-112">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor<span data-ttu-id="5b0a7-113"> Server-apps, [deren Inhalts Aufrufe vorab](xref:blazor/hosting-model-configuration#render-mode) `OnInitializedAsync` werden, **_zweimal_** :</span><span class="sxs-lookup"><span data-stu-id="5b0a7-113"> Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call `OnInitializedAsync` **_twice_**:</span></span>

* <span data-ttu-id="5b0a7-114">Einmal, wenn die Komponente anfänglich statisch als Teil der Seite gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-114">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="5b0a7-115">Ein zweites Mal, wenn der Browser eine Verbindung mit dem Server herstellt.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-115">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="5b0a7-116">Um zu verhindern, dass Entwickler Code in `OnInitializedAsync` zweimal ausgeführt wird, finden Sie weitere Informationen im Abschnitt Zustands behaftete [Neuverbindung nach der vorab](#stateful-reconnection-after-prerendering) Version.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-116">To prevent developer code in `OnInitializedAsync` from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="5b0a7-117">Während eine Blazor Server-App vorab erstellt wird, sind bestimmte Aktionen (z. b. das Aufrufen von JavaScript) nicht möglich, da keine Verbindung mit dem Browser hergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-117">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="5b0a7-118">Komponenten müssen bei der vorab Erstellung möglicherweise anders gerzugerdert werden.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-118">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="5b0a7-119">Weitere Informationen finden Sie im Abschnitt [erkennen, wenn die](#detect-when-the-app-is-prerendering) Anwendung eine Vorabversion ist.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-119">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="5b0a7-120">Vor dem Festlegen von Parametern</span><span class="sxs-lookup"><span data-stu-id="5b0a7-120">Before parameters are set</span></span>

<span data-ttu-id="5b0a7-121"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> legt Parameter fest, die vom übergeordneten Element in der Rendering-Struktur bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="5b0a7-121"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="5b0a7-122"><xref:Microsoft.AspNetCore.Components.ParameterView> enthält den gesamten Satz von Parameterwerten jedes Mal, wenn `SetParametersAsync` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-122"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="5b0a7-123">Die Standard Implementierung von `SetParametersAsync` legt den Wert jeder Eigenschaft mit dem `[Parameter]` oder `[CascadingParameter]` Attribut fest, das über einen entsprechenden Wert in der `ParameterView`verfügt.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-123">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="5b0a7-124">Parameter, die keinen entsprechenden Wert in `ParameterView` haben, bleiben unverändert.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-124">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="5b0a7-125">Wenn `base.SetParametersAync` nicht aufgerufen wird, kann der benutzerdefinierte Code den Wert eingehender Parameter in beliebiger Weise interpretieren.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-125">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="5b0a7-126">Beispielsweise ist es nicht erforderlich, den Eigenschaften der-Klasse die eingehenden Parameter zuzuweisen.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-126">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="5b0a7-127">Nachdem Parameter festgelegt wurden</span><span class="sxs-lookup"><span data-stu-id="5b0a7-127">After parameters are set</span></span>

<span data-ttu-id="5b0a7-128"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> werden aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="5b0a7-128"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> are called:</span></span>

* <span data-ttu-id="5b0a7-129">Wenn die Komponente initialisiert wird und ihren ersten Satz von Parametern von der übergeordneten Komponente empfangen hat.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-129">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="5b0a7-130">Wenn die übergeordnete Komponente erneut gerendert wird und Folgendes bereitstellt:</span><span class="sxs-lookup"><span data-stu-id="5b0a7-130">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="5b0a7-131">Nur bekannte primitive unveränderliche Typen, von denen sich mindestens ein Parameter geändert hat.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-131">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="5b0a7-132">Alle komplexen typisierten Parameter.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-132">Any complex-typed parameters.</span></span> <span data-ttu-id="5b0a7-133">Das Framework weiß nicht, ob die Werte eines komplexen typisierten Parameters intern geändert wurden, sodass der Parametersatz als geändert behandelt wird.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-133">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="5b0a7-134">Asynchrone Arbeit beim Anwenden von Parametern und Eigenschafts Werten muss während des `OnParametersSetAsync` Lifecycle-Ereignisses auftreten.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-134">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

### <a name="after-component-render"></a><span data-ttu-id="5b0a7-135">Nach dem Rendering der Komponente</span><span class="sxs-lookup"><span data-stu-id="5b0a7-135">After component render</span></span>

<span data-ttu-id="5b0a7-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> werden aufgerufen, nachdem eine Komponente das Rendering abgeschlossen hat.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> are called after a component has finished rendering.</span></span> <span data-ttu-id="5b0a7-137">Element-und Komponenten Verweise werden an diesem Punkt aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-137">Element and component references are populated at this point.</span></span> <span data-ttu-id="5b0a7-138">Verwenden Sie diese Phase, um zusätzliche Initialisierungs Schritte unter Verwendung des gerenderten Inhalts auszuführen, z. b. das Aktivieren von JavaScript-Bibliotheken von Drittanbietern, die auf den gerenderten Dom</span><span class="sxs-lookup"><span data-stu-id="5b0a7-138">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="5b0a7-139">Der `firstRender` Parameter für `OnAfterRenderAsync` und `OnAfterRender`:</span><span class="sxs-lookup"><span data-stu-id="5b0a7-139">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="5b0a7-140">Wird auf `true` festgelegt, wenn die Komponenteninstanz zum ersten Mal gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-140">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="5b0a7-141">Kann verwendet werden, um sicherzustellen, dass die Initialisierungs Arbeit nur einmal ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-141">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="5b0a7-142">Asynchrone Arbeit unmittelbar nach dem Rendering muss während des `OnAfterRenderAsync` Lifecycle-Ereignisses stattfinden.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-142">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="5b0a7-143">Auch wenn Sie eine <xref:System.Threading.Tasks.Task> von `OnAfterRenderAsync`zurückgeben, plant das Framework keinen weiteren Renderingdurchlauf für die Komponente, sobald diese Aufgabe abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-143">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="5b0a7-144">Dadurch wird eine unendliche Renderschleife vermieden.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-144">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="5b0a7-145">Dies unterscheidet sich von den anderen Lebenszyklus Methoden, die einen weiteren renderzyklus planen, sobald die zurückgegebene Aufgabe abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-145">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="5b0a7-146">`OnAfterRender` und `OnAfterRenderAsync` werden *nicht aufgerufen, wenn die Vorab Anmeldung auf dem Server erfolgt.*</span><span class="sxs-lookup"><span data-stu-id="5b0a7-146">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="5b0a7-147">UI-Aktualisierung unterdrücken</span><span class="sxs-lookup"><span data-stu-id="5b0a7-147">Suppress UI refreshing</span></span>

<span data-ttu-id="5b0a7-148">Überschreiben Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*>, um die Benutzeroberfläche zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-148">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> to suppress UI refreshing.</span></span> <span data-ttu-id="5b0a7-149">Wenn die Implementierung `true`zurückgibt, wird die Benutzeroberfläche aktualisiert:</span><span class="sxs-lookup"><span data-stu-id="5b0a7-149">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="5b0a7-150">`ShouldRender` wird jedes Mal aufgerufen, wenn die Komponente gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-150">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="5b0a7-151">Auch wenn `ShouldRender` überschrieben wird, wird die Komponente immer anfänglich gerendert.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-151">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="5b0a7-152">Zustandsänderungen</span><span class="sxs-lookup"><span data-stu-id="5b0a7-152">State changes</span></span>

<span data-ttu-id="5b0a7-153"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> benachrichtigt die Komponente, dass sich der Status geändert hat.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-153"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifies the component that its state has changed.</span></span> <span data-ttu-id="5b0a7-154">Wenn dies zutrifft, bewirkt der Aufruf von `StateHasChanged`, dass die Komponente erneut ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-154">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="5b0a7-155">Unvollständige Async-Aktionen bei Rendering behandeln</span><span class="sxs-lookup"><span data-stu-id="5b0a7-155">Handle incomplete async actions at render</span></span>

<span data-ttu-id="5b0a7-156">Asynchrone Aktionen, die in Lebenszyklus Ereignissen ausgeführt werden, wurden möglicherweise nicht abgeschlossen, bevor die Komponente gerendert</span><span class="sxs-lookup"><span data-stu-id="5b0a7-156">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="5b0a7-157">Objekte können `null` oder nicht vollständig mit Daten aufgefüllt werden, während die Lebenszyklus Methode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-157">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="5b0a7-158">Stellen Sie Renderinglogik bereit, um zu bestätigen, dass Objekte initialisiert</span><span class="sxs-lookup"><span data-stu-id="5b0a7-158">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="5b0a7-159">Stellen Sie Platzhalter-Benutzeroberflächen Elemente (z. b. eine Lade Nachricht) dar, während Objekte `null`werden.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-159">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="5b0a7-160">In der `FetchData` Komponente der Blazor Vorlagen wird `OnInitializedAsync` überschrieben, um Vorhersagedaten (`forecasts`) asynchron zu empfangen.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-160">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="5b0a7-161">Wenn `forecasts` `null`ist, wird dem Benutzer eine Meldung zum Laden angezeigt.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-161">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="5b0a7-162">Nachdem der von `OnInitializedAsync` zurückgegebene `Task` abgeschlossen ist, wird die Komponente mit dem aktualisierten Zustand erneut ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-162">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="5b0a7-163">*Pages/fetchdata. Razor* in der Blazor Server-Vorlage:</span><span class="sxs-lookup"><span data-stu-id="5b0a7-163">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="5b0a7-164">Komponenten Beseitigung mit iverwerf</span><span class="sxs-lookup"><span data-stu-id="5b0a7-164">Component disposal with IDisposable</span></span>

<span data-ttu-id="5b0a7-165">Wenn eine Komponente <xref:System.IDisposable>implementiert, wird die verwerfen- [Methode](/dotnet/standard/garbage-collection/implementing-dispose) aufgerufen, wenn die Komponente aus der Benutzeroberfläche entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-165">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="5b0a7-166">In der folgenden Komponente werden `@implements IDisposable` und die `Dispose`-Methode verwendet:</span><span class="sxs-lookup"><span data-stu-id="5b0a7-166">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="5b0a7-167">Das Aufrufen von <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-167">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` isn't supported.</span></span> <span data-ttu-id="5b0a7-168">`StateHasChanged` werden möglicherweise als Teil des renderens des Renderers aufgerufen, sodass das Anfordern von Aktualisierungen der Benutzeroberfläche an diesem Punkt nicht unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-168">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

## <a name="handle-errors"></a><span data-ttu-id="5b0a7-169">Fehlerbehandlung</span><span class="sxs-lookup"><span data-stu-id="5b0a7-169">Handle errors</span></span>

<span data-ttu-id="5b0a7-170">Informationen zum Behandeln von Fehlern während der Ausführung der Lebenszyklus Methode finden Sie unter <xref:blazor/handle-errors#lifecycle-methods>.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-170">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="5b0a7-171">Zustands behaftete erneute Verbindung nach der vorab Ausführung</span><span class="sxs-lookup"><span data-stu-id="5b0a7-171">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="5b0a7-172">In einer Blazor Server-APP, wenn `RenderMode` `ServerPrerendered`ist, wird die Komponente anfänglich statisch als Teil der Seite gerendert.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-172">In a Blazor Server app when `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="5b0a7-173">Sobald der Browser eine Verbindung mit dem Server herstellt, wird die Komponente *wieder*gerendert, und die Komponente ist nun interaktiv.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-173">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="5b0a7-174">Wenn die [OnInitialized {Async}](xref:blazor/lifecycle#component-initialization-methods) -Lebenszyklus Methode zum Initialisieren der Komponente vorhanden ist, wird die-Methode *zweimal*ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="5b0a7-174">If the [OnInitialized{Async}](xref:blazor/lifecycle#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="5b0a7-175">Bei statischer vorab Erstellung der Komponente.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-175">When the component is prerendered statically.</span></span>
* <span data-ttu-id="5b0a7-176">Nachdem die Server Verbindung hergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-176">After the server connection has been established.</span></span>

<span data-ttu-id="5b0a7-177">Dies kann zu einer merklichen Änderung in den Daten führen, die in der Benutzeroberfläche angezeigt werden, wenn die Komponente schließlich gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-177">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="5b0a7-178">So vermeiden Sie das doppelte renderingszenario in einer Blazor Server-App:</span><span class="sxs-lookup"><span data-stu-id="5b0a7-178">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="5b0a7-179">Übergeben Sie einen Bezeichner, der verwendet werden kann, um den Zustand während der vorab Ausführung zwischenzuspeichern und den Zustand nach dem Neustart der APP abzurufen.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-179">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="5b0a7-180">Verwenden Sie den Bezeichner während der vorab Einschreibung, um den Komponenten Zustand zu speichern.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-180">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="5b0a7-181">Verwenden Sie nach der vorab Ausführung den Bezeichner, um den zwischengespeicherten Zustand abzurufen.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-181">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="5b0a7-182">Der folgende Code veranschaulicht eine aktualisierte `WeatherForecastService` in einer Vorlagen basierten Blazor Server-APP, die das doppelte Rendering vermeidet:</span><span class="sxs-lookup"><span data-stu-id="5b0a7-182">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] _summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = _summaries[rng.Next(_summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="5b0a7-183">Weitere Informationen zum `RenderMode`finden Sie unter <xref:blazor/hosting-model-configuration#render-mode>.</span><span class="sxs-lookup"><span data-stu-id="5b0a7-183">For more information on the `RenderMode`, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="5b0a7-184">Erkennen, wenn die APP vorab durchgeführt wird</span><span class="sxs-lookup"><span data-stu-id="5b0a7-184">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

---
title: ASP.NET Core Blazor-Lebenszyklus
author: guardrex
description: Erfahren Sie, wie die Lebenszyklusmethoden von Razor-Komponenten in ASP.NET Core Blazor-Apps verwendet werden.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: 831f575afa6ce11d06c016d43ecd1bb59d09eab6
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218907"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a><span data-ttu-id="dd527-103">ASP.NET Core Blazor-Lebenszyklus</span><span class="sxs-lookup"><span data-stu-id="dd527-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="dd527-104">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="dd527-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="dd527-105">Das Blazor-Framework umfasst synchrone und asynchrone Lebenszyklusmethoden.</span><span class="sxs-lookup"><span data-stu-id="dd527-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="dd527-106">Setzen Sie Lebenszyklusmethoden außer Kraft, um während der Komponenteninitialisierung und des Renderings zusätzliche Vorgänge mit Komponenten durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="dd527-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="dd527-107">Lebenszyklusmethoden</span><span class="sxs-lookup"><span data-stu-id="dd527-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="dd527-108">Methoden zur Komponenteninitialisierung</span><span class="sxs-lookup"><span data-stu-id="dd527-108">Component initialization methods</span></span>

<span data-ttu-id="dd527-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> werden aufgerufen, wenn die Komponente initialisiert wird, nachdem sie ihre anfänglichen Parameter von ihrer übergeordneten Komponente erhalten hat.</span><span class="sxs-lookup"><span data-stu-id="dd527-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="dd527-110">Verwenden Sie `OnInitializedAsync`, wenn die Komponente einen asynchronen Vorgang ausführt und nach Abschluss des Vorgangs aktualisiert werden soll.</span><span class="sxs-lookup"><span data-stu-id="dd527-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="dd527-111">Für einen synchronen Betrieb setzen Sie `OnInitialized` außer Kraft:</span><span class="sxs-lookup"><span data-stu-id="dd527-111">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="dd527-112">Um einen asynchronen Vorgang auszuführen, setzen Sie `OnInitializedAsync` außer Kraft und verwenden das Schlüsselwort `await` für den Vorgang:</span><span class="sxs-lookup"><span data-stu-id="dd527-112">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor<span data-ttu-id="dd527-113"> Server-Apps, die [ihren Inhalt vorab rendern](xref:blazor/hosting-model-configuration#render-mode) rufen `OnInitializedAsync` **_zweimal_** auf:</span><span class="sxs-lookup"><span data-stu-id="dd527-113"> Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call `OnInitializedAsync` **_twice_**:</span></span>

* <span data-ttu-id="dd527-114">Einmal, wenn die Komponente anfänglich statisch als Teil der Seite gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="dd527-114">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="dd527-115">Ein zweites Mal, wenn der Browser eine Verbindung mit dem Server herstellt.</span><span class="sxs-lookup"><span data-stu-id="dd527-115">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="dd527-116">Informationen zum Verhindern, dass Entwicklercode in `OnInitializedAsync` zweimal ausgeführt wird, finden Sie im Abschnitt [Zustandsbehaftete erneute Verbindung nach dem Prerendering](#stateful-reconnection-after-prerendering).</span><span class="sxs-lookup"><span data-stu-id="dd527-116">To prevent developer code in `OnInitializedAsync` from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="dd527-117">Während für eine Blazor Server-App ein Prerendering durchgeführt wird, sind bestimmte Aktionen (z. B. Aufrufe in JavaScript) nicht möglich, da noch keine Verbindung mit dem Browser hergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="dd527-117">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="dd527-118">Komponenten müssen wahrscheinlich unterschiedlich rendern, wenn dafür ein Prerendering durchgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="dd527-118">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="dd527-119">Weitere Informationen finden Sie im Abschnitt [Ermitteln des Prerenderings einer App](#detect-when-the-app-is-prerendering).</span><span class="sxs-lookup"><span data-stu-id="dd527-119">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="dd527-120">Wenn Ereignishandler eingerichtet wurden, sollten Sie ihre Einbindung bei der Beseitigung aufheben.</span><span class="sxs-lookup"><span data-stu-id="dd527-120">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="dd527-121">Weitere Informationen finden Sie im Abschnitt [Beseitigung von Komponenten mit IDisposable](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="dd527-121">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="dd527-122">Bevor die Parameter festgelegt werden</span><span class="sxs-lookup"><span data-stu-id="dd527-122">Before parameters are set</span></span>

<span data-ttu-id="dd527-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> legt Parameter fest, die vom übergeordneten Element der Komponente in der Renderstruktur bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="dd527-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="dd527-124"><xref:Microsoft.AspNetCore.Components.ParameterView> enthält bei jedem Aufruf von `SetParametersAsync` den gesamten Satz von Parameterwerten.</span><span class="sxs-lookup"><span data-stu-id="dd527-124"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="dd527-125">Die Standardimplementierung von `SetParametersAsync` legt den Wert der einzelnen Eigenschaften mit dem Attribut `[Parameter]` oder `[CascadingParameter]` fest, die einen entsprechenden Wert in `ParameterView` aufweist.</span><span class="sxs-lookup"><span data-stu-id="dd527-125">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="dd527-126">Parameter, die keinen entsprechenden Wert in `ParameterView` haben, bleiben unverändert.</span><span class="sxs-lookup"><span data-stu-id="dd527-126">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="dd527-127">Wenn `base.SetParametersAync` nicht aufgerufen wird, kann der benutzerdefinierte Code den eingehenden Parameterwert in jeder gewünschten Weise interpretieren.</span><span class="sxs-lookup"><span data-stu-id="dd527-127">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="dd527-128">Beispielsweise ist es nicht erforderlich, die eingehenden Parameter den Eigenschaften der Klasse zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="dd527-128">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="dd527-129">Wenn Ereignishandler eingerichtet wurden, sollten Sie ihre Einbindung bei der Beseitigung aufheben.</span><span class="sxs-lookup"><span data-stu-id="dd527-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="dd527-130">Weitere Informationen finden Sie im Abschnitt [Beseitigung von Komponenten mit IDisposable](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="dd527-130">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="dd527-131">Nachdem die Parameter festgelegt wurden</span><span class="sxs-lookup"><span data-stu-id="dd527-131">After parameters are set</span></span>

<span data-ttu-id="dd527-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> werden aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="dd527-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> are called:</span></span>

* <span data-ttu-id="dd527-133">Wenn die Komponente initialisiert ist und ihren ersten Parametersatz von ihrer übergeordneten Komponente erhalten hat.</span><span class="sxs-lookup"><span data-stu-id="dd527-133">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="dd527-134">Wenn die übergeordnete Komponente neu gerendert wird und Folgendes bereitstellt:</span><span class="sxs-lookup"><span data-stu-id="dd527-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="dd527-135">Nur bekannte einfache, unveränderliche Typen, von denen sich mindestens ein Parameter geändert hat.</span><span class="sxs-lookup"><span data-stu-id="dd527-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="dd527-136">Beliebige Parameter mit komplexem Typ.</span><span class="sxs-lookup"><span data-stu-id="dd527-136">Any complex-typed parameters.</span></span> <span data-ttu-id="dd527-137">Das Framework kann nicht wissen, ob die Werte eines Parameters mit komplexem Typ intern mutiert sind, also behandelt es den Parametersatz als geändert.</span><span class="sxs-lookup"><span data-stu-id="dd527-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="dd527-138">Die asynchrone Arbeit bei der Anwendung von Parametern und Eigenschaftswerten muss während des `OnParametersSetAsync`-Lebenszyklusereignisses erfolgen.</span><span class="sxs-lookup"><span data-stu-id="dd527-138">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="dd527-139">Wenn Ereignishandler eingerichtet wurden, sollten Sie ihre Einbindung bei der Beseitigung aufheben.</span><span class="sxs-lookup"><span data-stu-id="dd527-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="dd527-140">Weitere Informationen finden Sie im Abschnitt [Beseitigung von Komponenten mit IDisposable](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="dd527-140">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="dd527-141">Nach dem Rendern der Komponente</span><span class="sxs-lookup"><span data-stu-id="dd527-141">After component render</span></span>

<span data-ttu-id="dd527-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> werden aufgerufen, nachdem eine Komponente das Rendering beendet hat.</span><span class="sxs-lookup"><span data-stu-id="dd527-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> are called after a component has finished rendering.</span></span> <span data-ttu-id="dd527-143">Element- und Komponentenverweise werden an dieser Stelle aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="dd527-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="dd527-144">In dieser Phase können Sie zusätzliche Initialisierungsschritte unter Verwendung des gerenderten Inhalts durchführen, z. B. die Aktivierung von JavaScript-Bibliotheken von Drittanbietern, die mit den gerenderten DOM-Elementen arbeiten.</span><span class="sxs-lookup"><span data-stu-id="dd527-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="dd527-145">Der Parameter `firstRender` für `OnAfterRenderAsync` und `OnAfterRender`:</span><span class="sxs-lookup"><span data-stu-id="dd527-145">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="dd527-146">Wird auf `true` festgelegt, wenn die Komponenteninstanz zum ersten Mal gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="dd527-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="dd527-147">Kann verwendet werden, um sicherzustellen, dass die Initialisierung nur einmal durchgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="dd527-147">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="dd527-148">Die asynchrone Arbeit unmittelbar nach dem Rendering muss während des `OnAfterRenderAsync`-Lebenszyklusereignisses erfolgen.</span><span class="sxs-lookup"><span data-stu-id="dd527-148">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="dd527-149">Selbst wenn Sie ein <xref:System.Threading.Tasks.Task> von `OnAfterRenderAsync` zurückgeben, plant das Framework keinen weiteren Renderzyklus für Ihre Komponente ein, sobald diese Aufgabe abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="dd527-149">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="dd527-150">Damit soll eine unendliche Renderschleife vermieden werden.</span><span class="sxs-lookup"><span data-stu-id="dd527-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="dd527-151">Dies unterscheidet sich von den anderen Lebenszyklusmethoden, die einen weiteren Renderzyklus planen, sobald die zurückgegebene Aufgabe abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="dd527-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="dd527-152">`OnAfterRender` und `OnAfterRenderAsync` *werden beim Prerendering auf dem Server nicht aufgerufen.*</span><span class="sxs-lookup"><span data-stu-id="dd527-152">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="dd527-153">Wenn Ereignishandler eingerichtet wurden, sollten Sie ihre Einbindung bei der Beseitigung aufheben.</span><span class="sxs-lookup"><span data-stu-id="dd527-153">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="dd527-154">Weitere Informationen finden Sie im Abschnitt [Beseitigung von Komponenten mit IDisposable](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="dd527-154">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="dd527-155">Unterdrücken der UI-Aktualisierung</span><span class="sxs-lookup"><span data-stu-id="dd527-155">Suppress UI refreshing</span></span>

<span data-ttu-id="dd527-156">Setzen Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> außer Kraft, um die Aktualisierung der Benutzeroberfläche zu unterdrücken.</span><span class="sxs-lookup"><span data-stu-id="dd527-156">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> to suppress UI refreshing.</span></span> <span data-ttu-id="dd527-157">Wenn die Implementierung `true` zurückgibt, wird die Benutzeroberfläche aktualisiert:</span><span class="sxs-lookup"><span data-stu-id="dd527-157">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="dd527-158">`ShouldRender` wird jedes Mal aufgerufen, wenn die Komponente gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="dd527-158">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="dd527-159">Selbst wenn `ShouldRender` außer Kraft gesetzt wird, wird die Komponente immer anfänglich gerendert.</span><span class="sxs-lookup"><span data-stu-id="dd527-159">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="dd527-160">Statusänderungen</span><span class="sxs-lookup"><span data-stu-id="dd527-160">State changes</span></span>

<span data-ttu-id="dd527-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> benachrichtigt die Komponente, dass sich ihr Zustand geändert hat.</span><span class="sxs-lookup"><span data-stu-id="dd527-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifies the component that its state has changed.</span></span> <span data-ttu-id="dd527-162">Gegebenenfalls bewirkt der Aufruf von `StateHasChanged` das erneute Rendern der Komponente.</span><span class="sxs-lookup"><span data-stu-id="dd527-162">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="dd527-163">Behandeln unvollständiger asynchroner Aktionen beim Rendern</span><span class="sxs-lookup"><span data-stu-id="dd527-163">Handle incomplete async actions at render</span></span>

<span data-ttu-id="dd527-164">Asynchrone Aktionen, die in Lebenszyklusereignissen ausgeführt werden, sind möglicherweise nicht abgeschlossen, bevor die Komponente gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="dd527-164">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="dd527-165">Objekte können während der Ausführung der Lebenszyklusmethode `null` oder unvollständig mit Daten gefüllt sein.</span><span class="sxs-lookup"><span data-stu-id="dd527-165">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="dd527-166">Stellen Sie eine Renderinglogik bereit, um zu bestätigen, dass die Objekte initialisiert sind.</span><span class="sxs-lookup"><span data-stu-id="dd527-166">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="dd527-167">Rendern Sie UI-Elemente für Platzhalter (z. B. eine Nachricht zum Ladevorgang), während Objekte `null` sind.</span><span class="sxs-lookup"><span data-stu-id="dd527-167">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="dd527-168">In der `FetchData`-Komponente der Blazor-Vorlagen wird `OnInitializedAsync` außer Kraft gesetzt, um Vorhersagedaten asynchron zu erhalten (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="dd527-168">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="dd527-169">Wenn `forecasts` gleich `null` ist, wird dem Benutzer eine Nachricht zum Ladevorgang angezeigt.</span><span class="sxs-lookup"><span data-stu-id="dd527-169">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="dd527-170">Nachdem die von `OnInitializedAsync` zurückgegebene `Task` abgeschlossen ist, wird die Komponente mit dem aktualisierten Zustand neu gerendert.</span><span class="sxs-lookup"><span data-stu-id="dd527-170">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="dd527-171">*Pages/FetchData.razor* in der Blazor-Servervorlage:</span><span class="sxs-lookup"><span data-stu-id="dd527-171">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="dd527-172">Beseitigung von Komponenten mit IDisposable</span><span class="sxs-lookup"><span data-stu-id="dd527-172">Component disposal with IDisposable</span></span>

<span data-ttu-id="dd527-173">Wenn eine Komponente <xref:System.IDisposable> implementiert, wird die [Dispose-Methode](/dotnet/standard/garbage-collection/implementing-dispose) aufgerufen, wenn die Komponente aus der Benutzeroberfläche entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="dd527-173">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="dd527-174">Die folgende Komponente verwendet `@implements IDisposable` und die `Dispose`-Methode:</span><span class="sxs-lookup"><span data-stu-id="dd527-174">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="dd527-175">Der Aufruf von <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="dd527-175">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` isn't supported.</span></span> <span data-ttu-id="dd527-176">`StateHasChanged` könnte im Rahmen des Beendens des Renderers aufgerufen werden, sodass die Anforderung von UI-Updates an diesem Punkt nicht unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="dd527-176">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="dd527-177">Kündigen Sie die .NET-Ereignisabonnements der Ereignishandler.</span><span class="sxs-lookup"><span data-stu-id="dd527-177">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="dd527-178">Die folgenden [Blazor-Formularbeispiele](xref:blazor/forms-validation) veranschaulichen das Aufheben der Einbindung eines Ereignishandlers in der `Dispose`-Methode:</span><span class="sxs-lookup"><span data-stu-id="dd527-178">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="dd527-179">Ansatz mit einem privatem Feld und Lambdaausdruck</span><span class="sxs-lookup"><span data-stu-id="dd527-179">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="dd527-180">Ansatz mit einer privaten Methode</span><span class="sxs-lookup"><span data-stu-id="dd527-180">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><span data-ttu-id="dd527-181">Behandeln von Fehlern</span><span class="sxs-lookup"><span data-stu-id="dd527-181">Handle errors</span></span>

<span data-ttu-id="dd527-182">Informationen zur Behandlung von Fehlern während der Ausführung von Lebenszyklusmethoden finden Sie unter <xref:blazor/handle-errors#lifecycle-methods>.</span><span class="sxs-lookup"><span data-stu-id="dd527-182">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="dd527-183">Zustandsbehaftete erneute Verbindung nach dem Prerendering</span><span class="sxs-lookup"><span data-stu-id="dd527-183">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="dd527-184">Wenn in einer Blazor-Server-App `RenderMode` gleich `ServerPrerendered` ist, wird die Komponente zunächst statisch als Teil der Seite gerendert.</span><span class="sxs-lookup"><span data-stu-id="dd527-184">In a Blazor Server app when `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="dd527-185">Sobald der Browser eine Verbindung mit dem Server herstellt, wird die Komponente *wieder* gerendert, und die Komponente ist nun interaktiv.</span><span class="sxs-lookup"><span data-stu-id="dd527-185">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="dd527-186">Wenn die [OnInitialized{Async}](xref:blazor/lifecycle#component-initialization-methods)-Lebenszyklusmethode zur Initialisierung der Komponente vorhanden ist, wird die Methode *zweimal* ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="dd527-186">If the [OnInitialized{Async}](xref:blazor/lifecycle#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="dd527-187">Wenn die Komponente statisch vorab gerendert ist.</span><span class="sxs-lookup"><span data-stu-id="dd527-187">When the component is prerendered statically.</span></span>
* <span data-ttu-id="dd527-188">Nachdem die Serververbindung hergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="dd527-188">After the server connection has been established.</span></span>

<span data-ttu-id="dd527-189">Dies kann zu einer spürbaren Änderung der auf der Benutzeroberfläche angezeigten Daten führen, wenn die Komponente schließlich gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="dd527-189">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="dd527-190">So vermeiden Sie das Szenario des doppelten Renderings in einer Blazor-Server-App</span><span class="sxs-lookup"><span data-stu-id="dd527-190">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="dd527-191">Übergeben Sie einen Bezeichner, der zum Zwischenspeichern des Status während des Prerenderings und zum Abrufen des Zustands nach dem Neustart der App verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="dd527-191">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="dd527-192">Verwenden Sie den Bezeichner während des Prerenderings, um den Zustand der Komponenten zu speichern.</span><span class="sxs-lookup"><span data-stu-id="dd527-192">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="dd527-193">Verwenden Sie den Bezeichner nach dem Prerendering, um den zwischengespeicherten Zustand abzurufen.</span><span class="sxs-lookup"><span data-stu-id="dd527-193">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="dd527-194">Der folgende Code veranschaulicht eine aktualisierte `WeatherForecastService` in einer vorlagenbasierten Blazor-Server-App, die das doppelte Rendering vermeidet:</span><span class="sxs-lookup"><span data-stu-id="dd527-194">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

<span data-ttu-id="dd527-195">Weitere Informationen zum `RenderMode` finden Sie unter <xref:blazor/hosting-model-configuration#render-mode>.</span><span class="sxs-lookup"><span data-stu-id="dd527-195">For more information on the `RenderMode`, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="dd527-196">Erkennen, wenn für die App ein Prerendering durchgeführt wird</span><span class="sxs-lookup"><span data-stu-id="dd527-196">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

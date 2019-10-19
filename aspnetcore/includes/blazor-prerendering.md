<span data-ttu-id="e8927-101">Während eine blazor-Server-App vorab erstellt wird, sind bestimmte Aktionen (z. b. das Aufrufen von JavaScript) nicht möglich, da keine Verbindung mit dem Browser hergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="e8927-101">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="e8927-102">Komponenten müssen bei der vorab Erstellung möglicherweise anders gerzugerdert werden.</span><span class="sxs-lookup"><span data-stu-id="e8927-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="e8927-103">Um JavaScript-Interop-Aufrufe zu verzögern, bis die Verbindung mit dem Browser hergestellt wurde, können Sie das `OnAfterRenderAsync` Component Lifecycle-Ereignis verwenden.</span><span class="sxs-lookup"><span data-stu-id="e8927-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the `OnAfterRenderAsync` component lifecycle event.</span></span> <span data-ttu-id="e8927-104">Dieses Ereignis wird nur aufgerufen, nachdem die APP vollständig gerendert wurde und die Client Verbindung hergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="e8927-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<input @ref="myInput" value="Value set during render" />

@code {
    private ElementReference myInput;

    protected override void OnAfterRender(bool firstRender)
    {
        if (firstRender)
        {
            JSRuntime.InvokeVoidAsync(
                "setElementValue", myInput, "Value set after render");
        }
    }
}
```

<span data-ttu-id="e8927-105">Die folgende Komponente veranschaulicht, wie JavaScript-Interop als Teil der Initialisierungs Logik einer Komponente auf eine Weise verwendet werden kann, die mit der vorab Generierung kompatibel ist.</span><span class="sxs-lookup"><span data-stu-id="e8927-105">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="e8927-106">Die-Komponente zeigt, dass es möglich ist, in `OnAfterRenderAsync` ein renderingupdate zu initiieren.</span><span class="sxs-lookup"><span data-stu-id="e8927-106">The component shows that it's possible to trigger a rendering update from inside `OnAfterRenderAsync`.</span></span> <span data-ttu-id="e8927-107">Der Entwickler muss in diesem Szenario vermeiden, eine Endlosschleife zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="e8927-107">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="e8927-108">Wenn `JSRuntime.InvokeAsync` aufgerufen wird, wird `ElementRef` nur in `OnAfterRenderAsync` und nicht in einer früheren Lebenszyklus Methode verwendet, da es kein JavaScript-Element gibt, bis die Komponente gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="e8927-108">Where `JSRuntime.InvokeAsync` is called, `ElementRef` is only used in `OnAfterRenderAsync` and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="e8927-109">`StateHasChanged` wird aufgerufen, um die Komponente mit dem neuen Zustand, der vom JavaScript-Interop-Aufruf abgerufen wurde, erneut zu überarbeiten.</span><span class="sxs-lookup"><span data-stu-id="e8927-109">`StateHasChanged` is called to rerender the component with the new state obtained from the JavaScript interop call.</span></span> <span data-ttu-id="e8927-110">Der Code erstellt keine unendliche Schleife, da `StateHasChanged` nur aufgerufen wird, wenn `infoFromJs` `null` ist.</span><span class="sxs-lookup"><span data-stu-id="e8927-110">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<p>
    Get value via JS interop call:
    <strong id="val-get-by-interop">@(infoFromJs ?? "No value yet")</strong>
</p>

<p>
    Set value via JS interop call:
    <input id="val-set-by-interop" @ref="myElem" />
</p>

@code {
    private string infoFromJs;
    private ElementReference myElem;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender && infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementValue", myElem, "Hello from interop call");

            StateHasChanged();
        }
    }
}
```

---
no-loc:
- Blazor
- SignalR
ms.openlocfilehash: 5f3e22e04fe18149ec5a8acb42f42a8ef83a7664
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78647527"
---
<span data-ttu-id="abd00-101">Während für eine Blazor Server-App ein Prerendering durchgeführt wird, sind bestimmte Aktionen (z. B. Aufrufe in JavaScript) nicht möglich, da noch keine Verbindung mit dem Browser hergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="abd00-101">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="abd00-102">Komponenten müssen wahrscheinlich unterschiedlich rendern, wenn dafür ein Prerendering durchgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="abd00-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="abd00-103">Wenn Sie JavaScript-Interop-Aufrufe bis nach Herstellung der Verbindung zum Browser verzögern möchten, können Sie das [Lebenszyklusereignis der OnAfterRenderAsync-Komponente](xref:blazor/lifecycle#after-component-render) verwenden.</span><span class="sxs-lookup"><span data-stu-id="abd00-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the [OnAfterRenderAsync component lifecycle event](xref:blazor/lifecycle#after-component-render).</span></span> <span data-ttu-id="abd00-104">Dieses Ereignis wird nur aufgerufen, nachdem die App vollständig gerendert und die Clientverbindung hergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="abd00-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<div @ref="divElement">Text during render</div>

@code {
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            await JSRuntime.InvokeVoidAsync(
                "setElementText", divElement, "Text after render");
        }
    }
}
```

<span data-ttu-id="abd00-105">Stellen Sie für den vorangehenden Beispielcode eine `setElementText`-JavaScript-Funktion im `<head>`-Element von *wwwroot/index.html* (Blazor WebAssembly) oder *Pages/_Host.cshtml* (Blazor Server) bereit.</span><span class="sxs-lookup"><span data-stu-id="abd00-105">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="abd00-106">Die Funktion wird mit `IJSRuntime.InvokeVoidAsync` aufgerufen und gibt keinen Wert zurück:</span><span class="sxs-lookup"><span data-stu-id="abd00-106">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> <span data-ttu-id="abd00-107">Im vorangehenden Beispiel wird das Dokumentobjektmodell (DOM) direkt zu Demonstrationszwecken geändert.</span><span class="sxs-lookup"><span data-stu-id="abd00-107">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="abd00-108">Das direkte Ändern des DOM mit JavaScript wird in den meisten Szenarios nicht empfohlen, da JavaScript die Änderungsnachverfolgung von Blazor beeinträchtigen kann.</span><span class="sxs-lookup"><span data-stu-id="abd00-108">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>

<span data-ttu-id="abd00-109">Die folgende Komponente veranschaulicht, wie JavaScript-Interop als Teil der Initialisierungslogik einer Komponente auf eine Weise verwendet werden kann, die mit dem Prerendering kompatibel ist.</span><span class="sxs-lookup"><span data-stu-id="abd00-109">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="abd00-110">Die Komponente zeigt, dass es möglich ist, in `OnAfterRenderAsync` ein Renderingupdate zu initiieren.</span><span class="sxs-lookup"><span data-stu-id="abd00-110">The component shows that it's possible to trigger a rendering update from inside `OnAfterRenderAsync`.</span></span> <span data-ttu-id="abd00-111">Der Entwickler muss in diesem Szenario vermeiden, eine Endlosschleife zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="abd00-111">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="abd00-112">Wenn `JSRuntime.InvokeAsync` aufgerufen wird, wird `ElementRef` nur in `OnAfterRenderAsync` und nicht in einer früheren Lebenszyklusmethode verwendet, da es kein JavaScript-Element gibt, bis die Komponente gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="abd00-112">Where `JSRuntime.InvokeAsync` is called, `ElementRef` is only used in `OnAfterRenderAsync` and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="abd00-113">[StateHasChanged](xref:blazor/lifecycle#state-changes) wird aufgerufen, um die Komponente mit dem neuen Zustand, der vom JavaScript-Interop-Aufruf abgerufen wurde, erneut zu überarbeiten.</span><span class="sxs-lookup"><span data-stu-id="abd00-113">[StateHasChanged](xref:blazor/lifecycle#state-changes) is called to rerender the component with the new state obtained from the JavaScript interop call.</span></span> <span data-ttu-id="abd00-114">Der Code erstellt keine Endlosschleife, da `StateHasChanged` nur aufgerufen wird, wenn `infoFromJs` `null` ist.</span><span class="sxs-lookup"><span data-stu-id="abd00-114">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<p>
    Get value via JS interop call:
    <strong id="val-get-by-interop">@(infoFromJs ?? "No value yet")</strong>
</p>

Set value via JS interop call:
<div id="val-set-by-interop" @ref="divElement"></div>

@code {
    private string infoFromJs;
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender && infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementText", divElement, "Hello from interop call!");

            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="abd00-115">Stellen Sie für den vorangehenden Beispielcode eine `setElementText`-JavaScript-Funktion im `<head>`-Element von *wwwroot/index.html* (Blazor WebAssembly) oder *Pages/_Host.cshtml* (Blazor Server) bereit.</span><span class="sxs-lookup"><span data-stu-id="abd00-115">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="abd00-116">Die Funktion wird mit `IJSRuntime.InvokeAsync` aufgerufen und gibt einen Wert zurück:</span><span class="sxs-lookup"><span data-stu-id="abd00-116">The function is called with `IJSRuntime.InvokeAsync` and returns a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> <span data-ttu-id="abd00-117">Im vorangehenden Beispiel wird das Dokumentobjektmodell (DOM) direkt zu Demonstrationszwecken geändert.</span><span class="sxs-lookup"><span data-stu-id="abd00-117">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="abd00-118">Das direkte Ändern des DOM mit JavaScript wird in den meisten Szenarios nicht empfohlen, da JavaScript die Änderungsnachverfolgung von Blazor beeinträchtigen kann.</span><span class="sxs-lookup"><span data-stu-id="abd00-118">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>

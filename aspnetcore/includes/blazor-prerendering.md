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
Während für eine Blazor Server-App ein Prerendering durchgeführt wird, sind bestimmte Aktionen (z. B. Aufrufe in JavaScript) nicht möglich, da noch keine Verbindung mit dem Browser hergestellt wurde. Komponenten müssen wahrscheinlich unterschiedlich rendern, wenn dafür ein Prerendering durchgeführt wurde.

Wenn Sie JavaScript-Interop-Aufrufe bis nach Herstellung der Verbindung zum Browser verzögern möchten, können Sie das [Lebenszyklusereignis der OnAfterRenderAsync-Komponente](xref:blazor/lifecycle#after-component-render) verwenden. Dieses Ereignis wird nur aufgerufen, nachdem die App vollständig gerendert und die Clientverbindung hergestellt wurde.

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

Stellen Sie für den vorangehenden Beispielcode eine `setElementText`-JavaScript-Funktion im `<head>`-Element von *wwwroot/index.html* (Blazor WebAssembly) oder *Pages/_Host.cshtml* (Blazor Server) bereit. Die Funktion wird mit `IJSRuntime.InvokeVoidAsync` aufgerufen und gibt keinen Wert zurück:

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> Im vorangehenden Beispiel wird das Dokumentobjektmodell (DOM) direkt zu Demonstrationszwecken geändert. Das direkte Ändern des DOM mit JavaScript wird in den meisten Szenarios nicht empfohlen, da JavaScript die Änderungsnachverfolgung von Blazor beeinträchtigen kann.

Die folgende Komponente veranschaulicht, wie JavaScript-Interop als Teil der Initialisierungslogik einer Komponente auf eine Weise verwendet werden kann, die mit dem Prerendering kompatibel ist. Die Komponente zeigt, dass es möglich ist, in `OnAfterRenderAsync` ein Renderingupdate zu initiieren. Der Entwickler muss in diesem Szenario vermeiden, eine Endlosschleife zu erstellen.

Wenn `JSRuntime.InvokeAsync` aufgerufen wird, wird `ElementRef` nur in `OnAfterRenderAsync` und nicht in einer früheren Lebenszyklusmethode verwendet, da es kein JavaScript-Element gibt, bis die Komponente gerendert wird.

[StateHasChanged](xref:blazor/lifecycle#state-changes) wird aufgerufen, um die Komponente mit dem neuen Zustand, der vom JavaScript-Interop-Aufruf abgerufen wurde, erneut zu überarbeiten. Der Code erstellt keine Endlosschleife, da `StateHasChanged` nur aufgerufen wird, wenn `infoFromJs` `null` ist.

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

Stellen Sie für den vorangehenden Beispielcode eine `setElementText`-JavaScript-Funktion im `<head>`-Element von *wwwroot/index.html* (Blazor WebAssembly) oder *Pages/_Host.cshtml* (Blazor Server) bereit. Die Funktion wird mit `IJSRuntime.InvokeAsync` aufgerufen und gibt einen Wert zurück:

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> Im vorangehenden Beispiel wird das Dokumentobjektmodell (DOM) direkt zu Demonstrationszwecken geändert. Das direkte Ändern des DOM mit JavaScript wird in den meisten Szenarios nicht empfohlen, da JavaScript die Änderungsnachverfolgung von Blazor beeinträchtigen kann.

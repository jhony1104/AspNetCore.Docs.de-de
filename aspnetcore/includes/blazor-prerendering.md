---
no-loc:
- Blazor
- SignalR
ms.openlocfilehash: 5f3e22e04fe18149ec5a8acb42f42a8ef83a7664
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/17/2020
ms.locfileid: "76159895"
---
Während eine Blazor Server-App vorab erstellt wird, sind bestimmte Aktionen (z. b. das Aufrufen von JavaScript) nicht möglich, da keine Verbindung mit dem Browser hergestellt wurde. Komponenten müssen bei der vorab Erstellung möglicherweise anders gerzugerdert werden.

Um JavaScript-Interop-Aufrufe zu verzögern, bis die Verbindung mit dem Browser hergestellt wurde, können Sie das [onafterrenderasync-Komponenten Lebenszyklus-Ereignis](xref:blazor/lifecycle#after-component-render)verwenden. Dieses Ereignis wird nur aufgerufen, nachdem die APP vollständig gerendert wurde und die Client Verbindung hergestellt wurde.

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

Stellen Sie für den vorangehenden Beispielcode eine `setElementText` JavaScript-Funktion innerhalb des `<head>`-Elements von *wwwroot/Index.html* (Blazor Webassembly) oder *pages/_Host. cshtml* (Blazor Server) bereit. Die-Funktion wird mit `IJSRuntime.InvokeVoidAsync` aufgerufen und gibt keinen Wert zurück:

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> Im vorangehenden Beispiel wird der Dokumentobjektmodell (DOM) direkt zu Demonstrationszwecken geändert. Das direkte Ändern des DOM mit JavaScript wird in den meisten Szenarien nicht empfohlen, da JavaScript die Änderungs Nachverfolgung von Blazorbeeinträchtigen kann.

Die folgende Komponente veranschaulicht, wie JavaScript-Interop als Teil der Initialisierungs Logik einer Komponente auf eine Weise verwendet werden kann, die mit der vorab Generierung kompatibel ist. Die-Komponente zeigt, dass es möglich ist, in `OnAfterRenderAsync`ein renderingupdate zu initiieren. Der Entwickler muss in diesem Szenario vermeiden, eine Endlosschleife zu erstellen.

Wenn `JSRuntime.InvokeAsync` aufgerufen wird, wird `ElementRef` nur in `OnAfterRenderAsync` und nicht in einer früheren Lebenszyklus Methode verwendet, da es kein JavaScript-Element gibt, bis die Komponente gerendert wird.

[Statehaschangiwird](xref:blazor/lifecycle#state-changes) aufgerufen, um die Komponente mit dem neuen Zustand zu überarbeiten, der vom JavaScript-Interop-Aufruf abgerufen wurde. Der Code erstellt keine unendliche Schleife, da `StateHasChanged` nur aufgerufen wird, wenn `infoFromJs` `null`ist.

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

Stellen Sie für den vorangehenden Beispielcode eine `setElementText` JavaScript-Funktion innerhalb des `<head>`-Elements von *wwwroot/Index.html* (Blazor Webassembly) oder *pages/_Host. cshtml* (Blazor Server) bereit. Die-Funktion wird mit `IJSRuntime.InvokeAsync` aufgerufen und gibt einen Wert zurück:

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> Im vorangehenden Beispiel wird der Dokumentobjektmodell (DOM) direkt zu Demonstrationszwecken geändert. Das direkte Ändern des DOM mit JavaScript wird in den meisten Szenarien nicht empfohlen, da JavaScript die Änderungs Nachverfolgung von Blazorbeeinträchtigen kann.

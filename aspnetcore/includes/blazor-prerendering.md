Während eine serverseitige blazor-App vorab erstellt wird, sind bestimmte Aktionen wie das Aufrufen von JavaScript nicht möglich, da keine Verbindung mit dem Browser hergestellt wurde. Komponenten müssen bei der vorab Erstellung möglicherweise anders gerzugerdert werden.

Um JavaScript-Interop-Aufrufe zu verzögern, bis die Verbindung mit dem Browser hergestellt wurde, können `OnAfterRenderAsync` Sie das Komponenten Lebenszyklus-Ereignis verwenden. Dieses Ereignis wird nur aufgerufen, nachdem die APP vollständig gerendert wurde und die Client Verbindung hergestellt wurde.

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
            JSRuntime.InvokeAsync<object>(
                "setElementValue", myInput, "Value set after render");
        }
    }
}
```

Die folgende Komponente veranschaulicht, wie JavaScript-Interop als Teil der Initialisierungs Logik einer Komponente auf eine Weise verwendet werden kann, die mit der vorab Generierung kompatibel ist. Die-Komponente zeigt, dass es möglich ist, ein renderingupdate von innerhalb `OnAfterRenderAsync`von zu initiieren. Der Entwickler muss in diesem Szenario vermeiden, eine Endlosschleife zu erstellen.

Wenn `JSRuntime.InvokeAsync` aufgerufen wird, `ElementRef` wird nur in `OnAfterRenderAsync` und nicht in einer früheren Lebenszyklus Methode verwendet, da es kein JavaScript-Element gibt, bis die Komponente gerendert wird.

`StateHasChanged`wird aufgerufen, um die Komponente mit dem neuen Zustand, der vom JavaScript-Interop-Aufruf abgerufen wurde, erneut zu überarbeiten. Der Code erstellt keine Endlosschleife, da `StateHasChanged` nur aufgerufen wird, `infoFromJs` `null`wenn den Wert hat.

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IComponentContext ComponentContext
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

Verwenden Sie die `IsConnected` -Eigenschaft für den `IComponentContext` Dienst, um unterschiedliche Inhalte bedingt zu erzeugen, je nachdem, ob die APP derzeit Inhalt vorab vorläuft. Wenn serverseitig ausgeführt wird, `IsConnected` gibt nur `true` zurück, wenn eine aktive Verbindung mit dem Client vorhanden ist. Sie wird immer `true` zurückgegeben, wenn die Client seitige Ausführung ausgeführt wird.

```cshtml
@page "/isconnected-example"
@using Microsoft.AspNetCore.Components.Services
@inject IComponentContext ComponentContext

<h1>IsConnected Example</h1>

<p>
    Current state:
    <strong id="connected-state">
        @(ComponentContext.IsConnected ? "connected" : "not connected")
    </strong>
</p>

<p>
    Clicks:
    <strong id="count">@count</strong>
    <button id="increment-count" @onclick="@(() => count++)">Click me</button>
</p>

@code {
    private int count;
}
```

---
title: ASP.net Core Blazor Ereignis Behandlung
author: guardrex
description: Erfahren Sie mehr über BlazorEreignis behandlungsszenarien, einschließlich Ereignis Argument Typen, Ereignis Rückrufe und der Verwaltung von Standardbrowser Ereignissen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: 25844ef39aee849072d16f3d73eda0a1c20ee788
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453163"
---
# <a name="aspnet-core-blazor-event-handling"></a>ASP.net Core blazor-Ereignis Behandlung

Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)

Razor-Komponenten stellen Ereignis Behandlungs Funktionen bereit. Für ein HTML-Element Attribut mit dem Namen `on{EVENT}` (z. b. `onclick` und `onsubmit`) mit einem delegattypisierten Wert behandelt Razor-Komponenten den Wert des Attributs als Ereignishandler. Der Name des Attributs wird immer [`@on{EVENT}`](xref:mvc/views/razor#onevent)formatiert.

Der folgende Code Ruft die `UpdateHeading`-Methode auf, wenn die Schaltfläche in der Benutzeroberfläche ausgewählt wird:

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

Der folgende Code Ruft die `CheckChanged`-Methode auf, wenn das Kontrollkästchen in der Benutzeroberfläche geändert wird:

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

Ereignishandler können auch asynchron sein und eine <xref:System.Threading.Tasks.Task>zurückgeben. [Statehaschte](xref:blazor/lifecycle#state-changes)muss nicht manuell aufgerufen werden. Ausnahmen werden protokolliert, wenn Sie auftreten.

Im folgenden Beispiel wird `UpdateHeading` asynchron aufgerufen, wenn die Schaltfläche ausgewählt wird:

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

## <a name="event-argument-types"></a>Ereignis Argument Typen

Für einige Ereignisse sind Ereignis Argument Typen zulässig. Das Angeben eines Ereignis Typs im Methoden aufzurufen ist nur erforderlich, wenn der Ereignistyp in der-Methode verwendet wird.

Unterstützte `EventArgs` sind in der folgenden Tabelle aufgeführt.

| Ereignis            | Klasse                | DOM-Ereignisse und-Notizen |
| ---------------- | -------------------- | -------------------- |
| Zwischenablage        | `ClipboardEventArgs` | `oncut`, `oncopy`, `onpaste` |
| Hinein             | `DragEventArgs`      | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br>`DataTransfer` und `DataTransferItem` die gezogenen Elementdaten speichern. |
| Error            | `ErrorEventArgs`     | `onerror` |
| Ereignis            | `EventArgs`          | *Allgemein*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*Zwischenablage*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*Input* (Eingabe)<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*Medien*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting` |
| Focus            | `FocusEventArgs`     | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>Beinhaltet keine Unterstützung für `relatedTarget`. |
| Eingabe            | `ChangeEventArgs`    | `onchange`, `oninput` |
| Tastatur         | `KeyboardEventArgs`  | `onkeydown`, `onkeypress`, `onkeyup` |
| Maus            | `MouseEventArgs`     | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| Mauszeiger    | `PointerEventArgs`   | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| Mausrad      | `WheelEventArgs`     | `onwheel`, `onmousewheel` |
| Status         | `ProgressEventArgs`  | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| Toucheingabe            | `TouchEventArgs`     | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br>`TouchPoint` stellt einen einzelnen Kontaktpunkt auf einem Berührungs sensiblen Gerät dar. |

Weitere Informationen finden Sie unter:

* [EventArgs-Klassen in der ASP.net Core Verweis Quelle (dotnet/aspnetcore Release/3.1-Branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).
* [MDN-Webdokumentation: globaleventhandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; enthält Informationen darüber, welche HTML-Elemente die einzelnen DOM-Ereignisse unterstützen.

## <a name="lambda-expressions"></a>Lambdaausdrücke

[Lambda-Ausdrücke](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) können auch verwendet werden:

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Häufig ist es praktisch, zusätzliche Werte zu schließen, z. b. beim Durchlaufen eines Satzes von Elementen. Im folgenden Beispiel werden drei Schaltflächen erstellt, von denen jedes `UpdateHeading` aufgerufen wird, um ein Ereignis Argument (`MouseEventArgs`) und seine Schaltflächen Nummer (`buttonNumber`) zu übergeben, wenn es in der Benutzeroberfläche ausgewählt wird:

```razor
<h2>@_message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string _message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        _message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> Verwenden Sie die Schleifenvariable (`i`) in einer `for`-Schleife **nicht** direkt in einem Lambda-Ausdruck. Andernfalls wird dieselbe Variable von allen Lambda-Ausdrücken verwendet, wodurch `i`Wert in allen Lambdas gleich ist. Erfassen Sie den Wert stets in einer lokalen Variablen (`buttonNumber` im vorangehenden Beispiel), und verwenden Sie Sie dann.

## <a name="eventcallback"></a>EventCallback

Ein häufiges Szenario mit untergeordneten Komponenten ist der Wunsch, die-Methode einer übergeordneten Komponente auszuführen, wenn ein untergeordnetes Komponenten Ereignis auftritt&mdash;z. b. Wenn ein `onclick`-Ereignis im untergeordneten Element auftritt. Um Ereignisse Komponenten übergreifend verfügbar zu machen, verwenden Sie eine `EventCallback`. Eine übergeordnete Komponente kann dem `EventCallback`einer untergeordneten Komponente eine Rückruf Methode zuweisen.

Der `ChildComponent` in der Beispiel-app (*Components/childcomponent. Razor*) veranschaulicht, wie der `onclick` Handler einer Schaltfläche für den Empfang eines `EventCallback` Delegaten aus dem `ParentComponent`des Beispiels eingerichtet ist. Die `EventCallback` wird mit `MouseEventArgs`typisiert, was für ein `onclick` Ereignis von einem Peripheriegerät geeignet ist:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

Der `ParentComponent` legt die `EventCallback<T>` (`OnClickCallback`) des untergeordneten Elements auf die `ShowMessage` Methode fest.

*Pages/para Component. Razor*:

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@_messageText</b></p>

@code {
    private string _messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        _messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

Wenn die Schaltfläche im `ChildComponent`ausgewählt ist:

* Die `ShowMessage`-Methode des `ParentComponent`wird aufgerufen. `_messageText` wird aktualisiert und in der `ParentComponent`angezeigt.
* In der-Methode des Rückrufs (`ShowMessage`) ist kein aufzurufende [statehasch-](xref:blazor/lifecycle#state-changes) Methode erforderlich. `StateHasChanged` wird automatisch aufgerufen, um die `ParentComponent`zu übertragen, ebenso wie untergeordnete Ereignisse die erneute Ausführung von Komponenten in Ereignis Handlern auslöst, die innerhalb des untergeordneten Elements ausgeführt werden.

`EventCallback` und `EventCallback<T>` die asynchrone Delegaten zulassen. `EventCallback<T>` ist stark typisiert und erfordert einen bestimmten Argumenttyp. `EventCallback` ist schwach typisiert und ermöglicht einen beliebigen Argumenttyp.

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

Rufen Sie eine `EventCallback` oder `EventCallback<T>` mit `InvokeAsync` auf, und warten Sie auf die <xref:System.Threading.Tasks.Task>:

```csharp
await callback.InvokeAsync(arg);
```

Verwenden Sie `EventCallback` und `EventCallback<T>` für Ereignisverarbeitungs-und Bindungskomponenten Parameter.

Bevorzugen der stark typisierten `EventCallback<T>` über `EventCallback`. `EventCallback<T>` bietet den Benutzern der Komponente ein besseres Fehler Feedback. Ähnlich wie bei anderen Benutzeroberflächen-Ereignis Handlern ist die Angabe des Ereignis Parameters optional. Verwenden Sie `EventCallback`, wenn kein Wert an den Rückruf übermittelt wird.

## <a name="prevent-default-actions"></a>Standard Aktionen verhindern

Verwenden Sie das [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) direktivenattribut, um die Standardaktion für ein Ereignis zu verhindern.

Wenn ein Schlüssel auf einem Eingabegerät ausgewählt wird und sich der Element Fokus auf einem Textfeld befindet, zeigt ein Browser normalerweise das Schlüsselzeichen im Textfeld an. Im folgenden Beispiel wird das Standardverhalten verhindert, indem das `@onkeypress:preventDefault` direktivenattribut angegeben wird. Der Wert des Leistungs Zählers wird erhöht, und der **+** Schlüssel wird nicht in den Wert des `<input>` Elements aufgezeichnet:

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int _count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            _count++;
        }
    }
}
```

Das Angeben des `@on{EVENT}:preventDefault` Attributs ohne Wert entspricht `@on{EVENT}:preventDefault="true"`.

Der Wert des-Attributs kann auch ein Ausdruck sein. Im folgenden Beispiel ist `_shouldPreventDefault` ein `bool` Feld, das entweder auf `true` oder `false`festgelegt ist:

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

Ein Ereignishandler ist nicht erforderlich, um die Standardaktion zu verhindern. Der Ereignishandler und die Vermeidung von Standard Aktions Szenarien können unabhängig voneinander verwendet werden.

## <a name="stop-event-propagation"></a>Ereignis Weitergabe Abbrechen

Verwenden Sie das Attribut [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) Direktive, um die Ereignis Weitergabe zu verhindern

Im folgenden Beispiel wird durch Aktivieren des Kontrollkästchens verhindert, dass Click-Ereignisse aus dem zweiten untergeordneten `<div>` an die übergeordnete `<div>`weitergegeben werden:

```razor
<label>
    <input @bind="_stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool _stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```

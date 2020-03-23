---
title: ASP.NET Core Blazor-Ereignisbehandlung
author: guardrex
description: Informieren Sie sich über die Features zur Ereignisbehandlung von Blazor, einschließlich Ereignisargumenttypen, Ereignisrückrufe und die Verwaltung von standardmäßigen Browserereignissen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: c144841805e07a136f153c25a78c7f9af7c5801b
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511365"
---
# <a name="aspnet-core-blazor-event-handling"></a>ASP.NET Core Blazor-Ereignisbehandlung

Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)

Razor-Komponenten bieten Features zur Ereignisbehandlung. Bei einem HTML-Elementattribut namens [`@on{EVENT}`](xref:mvc/views/razor#onevent) (z. B. `@onclick`) mit einem Wert vom Typ „Delegat“ behandelt eine Razor-Komponente den Wert des Attributs als einen Ereignishandler.

Der folgende Code ruft die `UpdateHeading`-Methode auf, wenn die Schaltfläche auf der Benutzeroberfläche ausgewählt wird:

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

Der folgende Code ruft die `CheckChanged`-Methode auf, wenn das Kontrollkästchen auf der Benutzeroberfläche geändert wird:

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

Ereignishandler können auch asynchron sein und ein <xref:System.Threading.Tasks.Task> zurückgeben. Es ist nicht erforderlich, [StateHasChanged](xref:blazor/lifecycle#state-changes) manuell aufzurufen. Ausnahmen werden protokolliert, wenn sie auftreten.

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

## <a name="event-argument-types"></a>Ereignisargumenttypen

Für einige Ereignisse sind Ereignisargumenttypen zulässig. Die Angabe eines Ereignistyps im Methodenaufruf ist nur dann erforderlich, wenn der Ereignistyp in der Methode verwendet wird.

Die unterstützten `EventArgs` sind in der folgenden Tabelle aufgeführt.

| event            | Klasse                | DOM-Ereignisse und -Hinweise |
| ---------------- | -------------------- | -------------------- |
| Zwischenablage        | `ClipboardEventArgs` | `oncut`, `oncopy`, `onpaste` |
| Ziehen             | `DragEventArgs`      | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br>`DataTransfer` und `DataTransferItem` speichern gezogene Elementdaten. |
| Fehler            | `ErrorEventArgs`     | `onerror` |
| event            | `EventArgs`          | *Allgemein*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*Zwischenablage*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*Eingabe*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*Medien*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting` |
| Fokus            | `FocusEventArgs`     | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>Umfasst nicht die Unterstützung für `relatedTarget`. |
| Eingabe            | `ChangeEventArgs`    | `onchange`, `oninput` |
| Tastatur         | `KeyboardEventArgs`  | `onkeydown`, `onkeypress`, `onkeyup` |
| Maus            | `MouseEventArgs`     | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| Mauszeiger    | `PointerEventArgs`   | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| Mausrad      | `WheelEventArgs`     | `onwheel`, `onmousewheel` |
| Status         | `ProgressEventArgs`  | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| Toucheingabe            | `TouchEventArgs`     | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br>`TouchPoint` stellt einen einzelnen Kontaktpunkt auf einem Gerät mit Berührungseingabe dar. |

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [EventArgs-Klassen in der ASP.NET Core-Referenzquelle (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).
* [MDN-Webdokumentationen: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Enthält Informationen darüber, welche HTML-Elemente die einzelnen DOM-Ereignisse unterstützen.

## <a name="lambda-expressions"></a>Lambdaausdrücke

[Lambdaausdrücke](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) können ebenfalls verwendet werden:

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Es ist häufig praktisch, über zusätzliche Werte zusammenzuschlagen, z. B. bei der Iteration über eine Reihe von Elementen. Das folgende Beispiel erstellt drei Schaltflächen, die jeweils `UpdateHeading` aufrufen, wobei ein Ereignisargument (`MouseEventArgs`) und seine Schaltflächennummer (`buttonNumber`) übergeben werden, wenn sie auf der Benutzeroberfläche ausgewählt werden:

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
> Verwenden Sie **nicht** die Schleifenvariable (`i`) in einer `for`-Schleife direkt in einem Lambdaausdruck. Ansonsten wird dieselbe Variable von allen Lambdaausdrücken verwendet, sodass der Wert von `i` in allen Lambdas gleich ist. Erfassen Sie ihren Wert immer in einer lokalen Variablen (`buttonNumber` im vorhergehenden Beispiel) und verwenden Sie sie dann.

## <a name="eventcallback"></a>EventCallback

Ein häufiges Szenario mit geschachtelten Komponenten ist der Wunsch, die Methode einer übergeordneten Komponente auszuführen, wenn ein Ereignis einer untergeordneten Komponente eintritt, z. B. wenn ein `onclick`-Ereignis in der untergeordneten Komponente eintritt. Um Ereignisse komponentenübergreifend darzustellen, verwenden Sie ein `EventCallback`. Eine übergeordnete Komponente kann dem `EventCallback` einer untergeordneten Komponente eine Rückrufmethode zuweisen.

Der `ChildComponent` in der Beispiel-App (*Components/ChildComponent.razor*) zeigt, wie der `onclick`-Handler einer Schaltfläche so eingerichtet ist, dass er einen `EventCallback`-Delegaten von der `ParentComponent` des Beispiels empfängt. Der `EventCallback` wird mit `MouseEventArgs` typisiert, was für ein `onclick`-Ereignis von einem Peripheriegerät geeignet ist:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

Die `ParentComponent` legt die `EventCallback<T>` (`OnClickCallback`) des untergeordneten Elements auf seine `ShowMessage`-Methode fest.

*Pages/ParentComponent.razor*:

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

Wenn die Schaltfläche in der `ChildComponent` ausgewählt ist:

* Die `ShowMessage`-Methode von `ParentComponent` wird aufgerufen. `_messageText` wird aktualisiert und in der `ParentComponent` angezeigt.
* Ein Aufruf von [StateHasChanged](xref:blazor/lifecycle#state-changes) ist in der Methode des Rückrufs (`ShowMessage`) nicht erforderlich. `StateHasChanged` wird automatisch aufgerufen, um die `ParentComponent` zu rendern, so wie Ereignisse untergeordneter Elemente das Rendern von Komponenten in Ereignishandlern auslösen, die innerhalb des untergeordneten Elements ausgeführt werden.

`EventCallback` und `EventCallback<T>` gestatten asynchrone Delegate. `EventCallback<T>` ist stark typisiert und erfordert einen bestimmten Argumenttyp. `EventCallback` ist schwach typisiert und gestattet jeden Argumenttyp.

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

Rufen Sie ein `EventCallback` oder `EventCallback<T>` mit `InvokeAsync` auf, und warten Sie auf das <xref:System.Threading.Tasks.Task>:

```csharp
await callback.InvokeAsync(arg);
```

Verwenden Sie `EventCallback` und `EventCallback<T>` für die Ereignisbehandlung und die Bindung von Komponentenparametern.

Bevorzugen Sie das stark typisierte `EventCallback<T>` gegenüber dem `EventCallback`. `EventCallback<T>` bietet den Benutzern der Komponente ein besseres Fehlerfeedback. Ähnlich wie bei anderen UI-Ereignishandlern ist die Angabe des Ereignisparameters optional. Verwenden Sie `EventCallback`, wenn kein Wert an den Rückruf übergeben wurde.

## <a name="prevent-default-actions"></a>Verhindern von Standardaktionen

Verwenden Sie das Direktivenattribut [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault), um die Standardaktion für ein Ereignis zu verhindern.

Wenn ein Schlüssel auf einem Eingabegerät ausgewählt wird und der Elementfokus auf einem Textfeld liegt, zeigt ein Browser normalerweise das Zeichen des Schlüssels in dem Textfeld an. Im folgenden Beispiel wird das Standardverhalten durch die Angabe des Direktivenattributs `@onkeypress:preventDefault` verhindert. Der Zähler wird inkrementiert und der Schlüssel **+** wird nicht im Wert des `<input>`-Elements erfasst:

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

Die Angabe des Attributs `@on{EVENT}:preventDefault` ohne Wert ist gleichbedeutend mit `@on{EVENT}:preventDefault="true"`.

Der Wert des Attributs kann auch ein Ausdruck sein. Im folgenden Beispiel ist `_shouldPreventDefault` ein `bool`-Feld, das entweder auf `true` oder `false` festgelegt ist:

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

Ein Ereignishandler ist nicht erforderlich, um die Standardaktion zu verhindern. Die Szenarien für Ereignishandler und die Verhinderung von Standardaktionen können unabhängig voneinander verwendet werden.

## <a name="stop-event-propagation"></a>Beenden der Ereignisweitergabe

Verwenden Sie das Direktivenattribut [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation), um die Ereignisweitergabe zu beenden.

Im folgenden Beispiel verhindert das Aktivieren des Kontrollkästchens die Weitergabe von Klickereignissen des zweiten untergeordneten `<div>`-Elements an das übergeordnete `<div>`-Element:

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

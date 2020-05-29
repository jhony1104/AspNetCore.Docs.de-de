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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: 610cb9124f59ed07f1fe6193f92052b4513450c8
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424253"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="907ba-103">ASP.NET Core Blazor-Ereignisbehandlung</span><span class="sxs-lookup"><span data-stu-id="907ba-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="907ba-104">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="907ba-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="907ba-105">Razor-Komponenten bieten Features zur Ereignisbehandlung.</span><span class="sxs-lookup"><span data-stu-id="907ba-105">Razor components provide event handling features.</span></span> <span data-ttu-id="907ba-106">Bei einem HTML-Elementattribut namens [`@on{EVENT}`](xref:mvc/views/razor#onevent) (z. B. `@onclick`) mit einem Wert vom Typ „Delegat“ behandelt eine Razor-Komponente den Wert des Attributs als einen Ereignishandler.</span><span class="sxs-lookup"><span data-stu-id="907ba-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="907ba-107">Der folgende Code ruft die `UpdateHeading`-Methode auf, wenn die Schaltfläche auf der Benutzeroberfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="907ba-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="907ba-108">Der folgende Code ruft die `CheckChanged`-Methode auf, wenn das Kontrollkästchen auf der Benutzeroberfläche geändert wird:</span><span class="sxs-lookup"><span data-stu-id="907ba-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="907ba-109">Ereignishandler können auch asynchron sein und ein <xref:System.Threading.Tasks.Task> zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="907ba-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="907ba-110">Es ist nicht erforderlich, [StateHasChanged](xref:blazor/lifecycle#state-changes) manuell aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="907ba-110">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="907ba-111">Ausnahmen werden protokolliert, wenn sie auftreten.</span><span class="sxs-lookup"><span data-stu-id="907ba-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="907ba-112">Im folgenden Beispiel wird `UpdateHeading` asynchron aufgerufen, wenn die Schaltfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="907ba-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="907ba-113">Ereignisargumenttypen</span><span class="sxs-lookup"><span data-stu-id="907ba-113">Event argument types</span></span>

<span data-ttu-id="907ba-114">Für einige Ereignisse sind Ereignisargumenttypen zulässig.</span><span class="sxs-lookup"><span data-stu-id="907ba-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="907ba-115">Die Angabe eines Ereignistyps im Methodenaufruf ist nur dann erforderlich, wenn der Ereignistyp in der Methode verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="907ba-115">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="907ba-116">Die unterstützten `EventArgs` sind in der folgenden Tabelle aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="907ba-116">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="907ba-117">event</span><span class="sxs-lookup"><span data-stu-id="907ba-117">Event</span></span>            | <span data-ttu-id="907ba-118">Klasse</span><span class="sxs-lookup"><span data-stu-id="907ba-118">Class</span></span>                | <span data-ttu-id="907ba-119">DOM-Ereignisse und -Hinweise</span><span class="sxs-lookup"><span data-stu-id="907ba-119">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="907ba-120">Zwischenablage</span><span class="sxs-lookup"><span data-stu-id="907ba-120">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="907ba-121">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="907ba-121">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="907ba-122">Ziehen</span><span class="sxs-lookup"><span data-stu-id="907ba-122">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="907ba-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="907ba-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="907ba-124">`DataTransfer` und `DataTransferItem` speichern gezogene Elementdaten.</span><span class="sxs-lookup"><span data-stu-id="907ba-124">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="907ba-125">Fehler</span><span class="sxs-lookup"><span data-stu-id="907ba-125">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="907ba-126">event</span><span class="sxs-lookup"><span data-stu-id="907ba-126">Event</span></span>            | `EventArgs`          | <span data-ttu-id="907ba-127">*Allgemein*</span><span class="sxs-lookup"><span data-stu-id="907ba-127">*General*</span></span><br><span data-ttu-id="907ba-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="907ba-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="907ba-129">*Zwischenablage*</span><span class="sxs-lookup"><span data-stu-id="907ba-129">*Clipboard*</span></span><br><span data-ttu-id="907ba-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="907ba-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="907ba-131">*Eingabe*</span><span class="sxs-lookup"><span data-stu-id="907ba-131">*Input*</span></span><br><span data-ttu-id="907ba-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="907ba-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="907ba-133">*Medien*</span><span class="sxs-lookup"><span data-stu-id="907ba-133">*Media*</span></span><br><span data-ttu-id="907ba-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="907ba-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="907ba-135">Fokus</span><span class="sxs-lookup"><span data-stu-id="907ba-135">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="907ba-136">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="907ba-136">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="907ba-137">Umfasst nicht die Unterstützung für `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="907ba-137">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="907ba-138">Eingabe</span><span class="sxs-lookup"><span data-stu-id="907ba-138">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="907ba-139">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="907ba-139">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="907ba-140">Tastatur</span><span class="sxs-lookup"><span data-stu-id="907ba-140">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="907ba-141">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="907ba-141">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="907ba-142">Maus</span><span class="sxs-lookup"><span data-stu-id="907ba-142">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="907ba-143">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="907ba-143">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="907ba-144">Mauszeiger</span><span class="sxs-lookup"><span data-stu-id="907ba-144">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="907ba-145">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="907ba-145">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="907ba-146">Mausrad</span><span class="sxs-lookup"><span data-stu-id="907ba-146">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="907ba-147">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="907ba-147">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="907ba-148">Status</span><span class="sxs-lookup"><span data-stu-id="907ba-148">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="907ba-149">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="907ba-149">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="907ba-150">Toucheingabe</span><span class="sxs-lookup"><span data-stu-id="907ba-150">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="907ba-151">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="907ba-151">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="907ba-152">`TouchPoint` stellt einen einzelnen Kontaktpunkt auf einem Gerät mit Berührungseingabe dar.</span><span class="sxs-lookup"><span data-stu-id="907ba-152">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="907ba-153">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="907ba-153">For more information, see the following resources:</span></span>

* <span data-ttu-id="907ba-154">[EventArgs-Klassen in der ASP.NET Core-Referenzquelle (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="907ba-154">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="907ba-155">[MDN-Webdokumentationen: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Enthält Informationen darüber, welche HTML-Elemente die einzelnen DOM-Ereignisse unterstützen.</span><span class="sxs-lookup"><span data-stu-id="907ba-155">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="907ba-156">Lambdaausdrücke</span><span class="sxs-lookup"><span data-stu-id="907ba-156">Lambda expressions</span></span>

<span data-ttu-id="907ba-157">[Lambdaausdrücke](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) können ebenfalls verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="907ba-157">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="907ba-158">Es ist häufig praktisch, über zusätzliche Werte zusammenzuschlagen, z. B. bei der Iteration über eine Reihe von Elementen.</span><span class="sxs-lookup"><span data-stu-id="907ba-158">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="907ba-159">Das folgende Beispiel erstellt drei Schaltflächen, die jeweils `UpdateHeading` aufrufen, wobei ein Ereignisargument (`MouseEventArgs`) und seine Schaltflächennummer (`buttonNumber`) übergeben werden, wenn sie auf der Benutzeroberfläche ausgewählt werden:</span><span class="sxs-lookup"><span data-stu-id="907ba-159">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```razor
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="907ba-160">Verwenden Sie **nicht** die Schleifenvariable (`i`) in einer `for`-Schleife direkt in einem Lambdaausdruck.</span><span class="sxs-lookup"><span data-stu-id="907ba-160">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="907ba-161">Ansonsten wird dieselbe Variable von allen Lambdaausdrücken verwendet, sodass der Wert von `i` in allen Lambdas gleich ist.</span><span class="sxs-lookup"><span data-stu-id="907ba-161">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="907ba-162">Erfassen Sie ihren Wert immer in einer lokalen Variablen (`buttonNumber` im vorhergehenden Beispiel) und verwenden Sie sie dann.</span><span class="sxs-lookup"><span data-stu-id="907ba-162">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="907ba-163">EventCallback</span><span class="sxs-lookup"><span data-stu-id="907ba-163">EventCallback</span></span>

<span data-ttu-id="907ba-164">Ein häufiges Szenario mit geschachtelten Komponenten ist der Wunsch, die Methode einer übergeordneten Komponente auszuführen, wenn ein Ereignis einer untergeordneten Komponente eintritt.</span><span class="sxs-lookup"><span data-stu-id="907ba-164">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="907ba-165">Das ein `onclick`-Ereignis in der untergeordneten Komponente auftritt, ist ein gängiger Anwendungsfall.</span><span class="sxs-lookup"><span data-stu-id="907ba-165">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="907ba-166">Um Ereignisse komponentenübergreifend darzustellen, verwenden Sie ein `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="907ba-166">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="907ba-167">Eine übergeordnete Komponente kann dem `EventCallback` einer untergeordneten Komponente eine Rückrufmethode zuweisen.</span><span class="sxs-lookup"><span data-stu-id="907ba-167">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="907ba-168">Der `ChildComponent` in der Beispiel-App (*Components/ChildComponent.razor*) zeigt, wie der `onclick`-Handler einer Schaltfläche so eingerichtet ist, dass er einen `EventCallback`-Delegaten von der `ParentComponent` des Beispiels empfängt.</span><span class="sxs-lookup"><span data-stu-id="907ba-168">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="907ba-169">Der `EventCallback` wird mit `MouseEventArgs` typisiert, was für ein `onclick`-Ereignis von einem Peripheriegerät geeignet ist:</span><span class="sxs-lookup"><span data-stu-id="907ba-169">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="907ba-170">Die `ParentComponent` legt die `EventCallback<T>` (`OnClickCallback`) des untergeordneten Elements auf seine `ShowMessage`-Methode fest.</span><span class="sxs-lookup"><span data-stu-id="907ba-170">The `ParentComponent` sets the child's `EventCallback<T>` (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="907ba-171">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="907ba-171">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="907ba-172">Wenn die Schaltfläche in der `ChildComponent` ausgewählt ist:</span><span class="sxs-lookup"><span data-stu-id="907ba-172">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="907ba-173">Die `ShowMessage`-Methode von `ParentComponent` wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="907ba-173">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="907ba-174">`messageText` wird aktualisiert und in der `ParentComponent` angezeigt.</span><span class="sxs-lookup"><span data-stu-id="907ba-174">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="907ba-175">Ein Aufruf von [StateHasChanged](xref:blazor/lifecycle#state-changes) ist in der Methode des Rückrufs (`ShowMessage`) nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="907ba-175">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="907ba-176">`StateHasChanged` wird automatisch aufgerufen, um die `ParentComponent` zu rendern, so wie Ereignisse untergeordneter Elemente das Rendern von Komponenten in Ereignishandlern auslösen, die innerhalb des untergeordneten Elements ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="907ba-176">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="907ba-177">`EventCallback` und `EventCallback<T>` gestatten asynchrone Delegate.</span><span class="sxs-lookup"><span data-stu-id="907ba-177">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="907ba-178">`EventCallback<T>` ist stark typisiert und erfordert einen bestimmten Argumenttyp.</span><span class="sxs-lookup"><span data-stu-id="907ba-178">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="907ba-179">`EventCallback` ist schwach typisiert und gestattet jeden Argumenttyp.</span><span class="sxs-lookup"><span data-stu-id="907ba-179">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="907ba-180">Rufen Sie ein `EventCallback` oder `EventCallback<T>` mit `InvokeAsync` auf, und warten Sie auf das <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="907ba-180">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="907ba-181">Verwenden Sie `EventCallback` und `EventCallback<T>` für die Ereignisbehandlung und die Bindung von Komponentenparametern.</span><span class="sxs-lookup"><span data-stu-id="907ba-181">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="907ba-182">Bevorzugen Sie das stark typisierte `EventCallback<T>` gegenüber dem `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="907ba-182">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="907ba-183">`EventCallback<T>` bietet den Benutzern der Komponente ein besseres Fehlerfeedback.</span><span class="sxs-lookup"><span data-stu-id="907ba-183">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="907ba-184">Ähnlich wie bei anderen UI-Ereignishandlern ist die Angabe des Ereignisparameters optional.</span><span class="sxs-lookup"><span data-stu-id="907ba-184">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="907ba-185">Verwenden Sie `EventCallback`, wenn kein Wert an den Rückruf übergeben wurde.</span><span class="sxs-lookup"><span data-stu-id="907ba-185">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="907ba-186">Verhindern von Standardaktionen</span><span class="sxs-lookup"><span data-stu-id="907ba-186">Prevent default actions</span></span>

<span data-ttu-id="907ba-187">Verwenden Sie das Direktivenattribut [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault), um die Standardaktion für ein Ereignis zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="907ba-187">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="907ba-188">Wenn ein Schlüssel auf einem Eingabegerät ausgewählt wird und der Elementfokus auf einem Textfeld liegt, zeigt ein Browser normalerweise das Zeichen des Schlüssels in dem Textfeld an.</span><span class="sxs-lookup"><span data-stu-id="907ba-188">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="907ba-189">Im folgenden Beispiel wird das Standardverhalten durch die Angabe des Direktivenattributs `@onkeypress:preventDefault` verhindert.</span><span class="sxs-lookup"><span data-stu-id="907ba-189">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="907ba-190">Der Zähler wird inkrementiert und der Schlüssel **+** wird nicht im Wert des `<input>`-Elements erfasst:</span><span class="sxs-lookup"><span data-stu-id="907ba-190">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

```razor
<input value="@count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            count++;
        }
    }
}
```

<span data-ttu-id="907ba-191">Die Angabe des Attributs `@on{EVENT}:preventDefault` ohne Wert ist gleichbedeutend mit `@on{EVENT}:preventDefault="true"`.</span><span class="sxs-lookup"><span data-stu-id="907ba-191">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="907ba-192">Der Wert des Attributs kann auch ein Ausdruck sein.</span><span class="sxs-lookup"><span data-stu-id="907ba-192">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="907ba-193">Im folgenden Beispiel ist `shouldPreventDefault` ein `bool`-Feld, das entweder auf `true` oder `false` festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="907ba-193">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

<span data-ttu-id="907ba-194">Ein Ereignishandler ist nicht erforderlich, um die Standardaktion zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="907ba-194">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="907ba-195">Die Szenarien für Ereignishandler und die Verhinderung von Standardaktionen können unabhängig voneinander verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="907ba-195">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="907ba-196">Beenden der Ereignisweitergabe</span><span class="sxs-lookup"><span data-stu-id="907ba-196">Stop event propagation</span></span>

<span data-ttu-id="907ba-197">Verwenden Sie das Direktivenattribut [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation), um die Ereignisweitergabe zu beenden.</span><span class="sxs-lookup"><span data-stu-id="907ba-197">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="907ba-198">Im folgenden Beispiel verhindert das Aktivieren des Kontrollkästchens die Weitergabe von Klickereignissen des zweiten untergeordneten `<div>`-Elements an das übergeordnete `<div>`-Element:</span><span class="sxs-lookup"><span data-stu-id="907ba-198">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<label>
    <input @bind="stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```

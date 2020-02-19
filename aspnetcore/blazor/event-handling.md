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
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="b4eb6-103">ASP.net Core blazor-Ereignis Behandlung</span><span class="sxs-lookup"><span data-stu-id="b4eb6-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="b4eb6-104">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="b4eb6-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="b4eb6-105">Razor-Komponenten stellen Ereignis Behandlungs Funktionen bereit.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-105">Razor components provide event handling features.</span></span> <span data-ttu-id="b4eb6-106">Für ein HTML-Element Attribut mit dem Namen `on{EVENT}` (z. b. `onclick` und `onsubmit`) mit einem delegattypisierten Wert behandelt Razor-Komponenten den Wert des Attributs als Ereignishandler.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-106">For an HTML element attribute named `on{EVENT}` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="b4eb6-107">Der Name des Attributs wird immer [`@on{EVENT}`](xref:mvc/views/razor#onevent)formatiert.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-107">The attribute's name is always formatted [`@on{EVENT}`](xref:mvc/views/razor#onevent).</span></span>

<span data-ttu-id="b4eb6-108">Der folgende Code Ruft die `UpdateHeading`-Methode auf, wenn die Schaltfläche in der Benutzeroberfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="b4eb6-108">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="b4eb6-109">Der folgende Code Ruft die `CheckChanged`-Methode auf, wenn das Kontrollkästchen in der Benutzeroberfläche geändert wird:</span><span class="sxs-lookup"><span data-stu-id="b4eb6-109">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="b4eb6-110">Ereignishandler können auch asynchron sein und eine <xref:System.Threading.Tasks.Task>zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-110">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="b4eb6-111">[Statehaschte](xref:blazor/lifecycle#state-changes)muss nicht manuell aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-111">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="b4eb6-112">Ausnahmen werden protokolliert, wenn Sie auftreten.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-112">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="b4eb6-113">Im folgenden Beispiel wird `UpdateHeading` asynchron aufgerufen, wenn die Schaltfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="b4eb6-113">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="b4eb6-114">Ereignis Argument Typen</span><span class="sxs-lookup"><span data-stu-id="b4eb6-114">Event argument types</span></span>

<span data-ttu-id="b4eb6-115">Für einige Ereignisse sind Ereignis Argument Typen zulässig.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-115">For some events, event argument types are permitted.</span></span> <span data-ttu-id="b4eb6-116">Das Angeben eines Ereignis Typs im Methoden aufzurufen ist nur erforderlich, wenn der Ereignistyp in der-Methode verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-116">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="b4eb6-117">Unterstützte `EventArgs` sind in der folgenden Tabelle aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-117">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="b4eb6-118">Ereignis</span><span class="sxs-lookup"><span data-stu-id="b4eb6-118">Event</span></span>            | <span data-ttu-id="b4eb6-119">Klasse</span><span class="sxs-lookup"><span data-stu-id="b4eb6-119">Class</span></span>                | <span data-ttu-id="b4eb6-120">DOM-Ereignisse und-Notizen</span><span class="sxs-lookup"><span data-stu-id="b4eb6-120">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="b4eb6-121">Zwischenablage</span><span class="sxs-lookup"><span data-stu-id="b4eb6-121">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="b4eb6-122">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="b4eb6-122">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="b4eb6-123">Hinein</span><span class="sxs-lookup"><span data-stu-id="b4eb6-123">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="b4eb6-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="b4eb6-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="b4eb6-125">`DataTransfer` und `DataTransferItem` die gezogenen Elementdaten speichern.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-125">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="b4eb6-126">Error</span><span class="sxs-lookup"><span data-stu-id="b4eb6-126">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="b4eb6-127">Ereignis</span><span class="sxs-lookup"><span data-stu-id="b4eb6-127">Event</span></span>            | `EventArgs`          | <span data-ttu-id="b4eb6-128">*Allgemein*</span><span class="sxs-lookup"><span data-stu-id="b4eb6-128">*General*</span></span><br><span data-ttu-id="b4eb6-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="b4eb6-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="b4eb6-130">*Zwischenablage*</span><span class="sxs-lookup"><span data-stu-id="b4eb6-130">*Clipboard*</span></span><br><span data-ttu-id="b4eb6-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="b4eb6-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="b4eb6-132">*Input* (Eingabe)</span><span class="sxs-lookup"><span data-stu-id="b4eb6-132">*Input*</span></span><br><span data-ttu-id="b4eb6-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="b4eb6-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="b4eb6-134">*Medien*</span><span class="sxs-lookup"><span data-stu-id="b4eb6-134">*Media*</span></span><br><span data-ttu-id="b4eb6-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="b4eb6-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="b4eb6-136">Focus</span><span class="sxs-lookup"><span data-stu-id="b4eb6-136">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="b4eb6-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="b4eb6-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="b4eb6-138">Beinhaltet keine Unterstützung für `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-138">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="b4eb6-139">Eingabe</span><span class="sxs-lookup"><span data-stu-id="b4eb6-139">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="b4eb6-140">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="b4eb6-140">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="b4eb6-141">Tastatur</span><span class="sxs-lookup"><span data-stu-id="b4eb6-141">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="b4eb6-142">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="b4eb6-142">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="b4eb6-143">Maus</span><span class="sxs-lookup"><span data-stu-id="b4eb6-143">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="b4eb6-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="b4eb6-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="b4eb6-145">Mauszeiger</span><span class="sxs-lookup"><span data-stu-id="b4eb6-145">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="b4eb6-146">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="b4eb6-146">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="b4eb6-147">Mausrad</span><span class="sxs-lookup"><span data-stu-id="b4eb6-147">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="b4eb6-148">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="b4eb6-148">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="b4eb6-149">Status</span><span class="sxs-lookup"><span data-stu-id="b4eb6-149">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="b4eb6-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="b4eb6-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="b4eb6-151">Toucheingabe</span><span class="sxs-lookup"><span data-stu-id="b4eb6-151">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="b4eb6-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="b4eb6-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="b4eb6-153">`TouchPoint` stellt einen einzelnen Kontaktpunkt auf einem Berührungs sensiblen Gerät dar.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-153">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="b4eb6-154">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="b4eb6-154">For more information, see the following resources:</span></span>

* <span data-ttu-id="b4eb6-155">[EventArgs-Klassen in der ASP.net Core Verweis Quelle (dotnet/aspnetcore Release/3.1-Branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="b4eb6-155">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="b4eb6-156">[MDN-Webdokumentation: globaleventhandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; enthält Informationen darüber, welche HTML-Elemente die einzelnen DOM-Ereignisse unterstützen.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-156">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="b4eb6-157">Lambdaausdrücke</span><span class="sxs-lookup"><span data-stu-id="b4eb6-157">Lambda expressions</span></span>

<span data-ttu-id="b4eb6-158">[Lambda-Ausdrücke](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) können auch verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="b4eb6-158">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="b4eb6-159">Häufig ist es praktisch, zusätzliche Werte zu schließen, z. b. beim Durchlaufen eines Satzes von Elementen.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-159">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="b4eb6-160">Im folgenden Beispiel werden drei Schaltflächen erstellt, von denen jedes `UpdateHeading` aufgerufen wird, um ein Ereignis Argument (`MouseEventArgs`) und seine Schaltflächen Nummer (`buttonNumber`) zu übergeben, wenn es in der Benutzeroberfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="b4eb6-160">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="b4eb6-161">Verwenden Sie die Schleifenvariable (`i`) in einer `for`-Schleife **nicht** direkt in einem Lambda-Ausdruck.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-161">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="b4eb6-162">Andernfalls wird dieselbe Variable von allen Lambda-Ausdrücken verwendet, wodurch `i`Wert in allen Lambdas gleich ist.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-162">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="b4eb6-163">Erfassen Sie den Wert stets in einer lokalen Variablen (`buttonNumber` im vorangehenden Beispiel), und verwenden Sie Sie dann.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-163">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="b4eb6-164">EventCallback</span><span class="sxs-lookup"><span data-stu-id="b4eb6-164">EventCallback</span></span>

<span data-ttu-id="b4eb6-165">Ein häufiges Szenario mit untergeordneten Komponenten ist der Wunsch, die-Methode einer übergeordneten Komponente auszuführen, wenn ein untergeordnetes Komponenten Ereignis auftritt&mdash;z. b. Wenn ein `onclick`-Ereignis im untergeordneten Element auftritt.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-165">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="b4eb6-166">Um Ereignisse Komponenten übergreifend verfügbar zu machen, verwenden Sie eine `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-166">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="b4eb6-167">Eine übergeordnete Komponente kann dem `EventCallback`einer untergeordneten Komponente eine Rückruf Methode zuweisen.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-167">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="b4eb6-168">Der `ChildComponent` in der Beispiel-app (*Components/childcomponent. Razor*) veranschaulicht, wie der `onclick` Handler einer Schaltfläche für den Empfang eines `EventCallback` Delegaten aus dem `ParentComponent`des Beispiels eingerichtet ist.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-168">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="b4eb6-169">Die `EventCallback` wird mit `MouseEventArgs`typisiert, was für ein `onclick` Ereignis von einem Peripheriegerät geeignet ist:</span><span class="sxs-lookup"><span data-stu-id="b4eb6-169">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="b4eb6-170">Der `ParentComponent` legt die `EventCallback<T>` (`OnClickCallback`) des untergeordneten Elements auf die `ShowMessage` Methode fest.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-170">The `ParentComponent` sets the child's `EventCallback<T>` (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="b4eb6-171">*Pages/para Component. Razor*:</span><span class="sxs-lookup"><span data-stu-id="b4eb6-171">*Pages/ParentComponent.razor*:</span></span>

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

<span data-ttu-id="b4eb6-172">Wenn die Schaltfläche im `ChildComponent`ausgewählt ist:</span><span class="sxs-lookup"><span data-stu-id="b4eb6-172">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="b4eb6-173">Die `ShowMessage`-Methode des `ParentComponent`wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-173">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="b4eb6-174">`_messageText` wird aktualisiert und in der `ParentComponent`angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-174">`_messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="b4eb6-175">In der-Methode des Rückrufs (`ShowMessage`) ist kein aufzurufende [statehasch-](xref:blazor/lifecycle#state-changes) Methode erforderlich.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-175">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="b4eb6-176">`StateHasChanged` wird automatisch aufgerufen, um die `ParentComponent`zu übertragen, ebenso wie untergeordnete Ereignisse die erneute Ausführung von Komponenten in Ereignis Handlern auslöst, die innerhalb des untergeordneten Elements ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-176">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="b4eb6-177">`EventCallback` und `EventCallback<T>` die asynchrone Delegaten zulassen.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-177">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="b4eb6-178">`EventCallback<T>` ist stark typisiert und erfordert einen bestimmten Argumenttyp.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-178">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="b4eb6-179">`EventCallback` ist schwach typisiert und ermöglicht einen beliebigen Argumenttyp.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-179">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

<span data-ttu-id="b4eb6-180">Rufen Sie eine `EventCallback` oder `EventCallback<T>` mit `InvokeAsync` auf, und warten Sie auf die <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="b4eb6-180">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="b4eb6-181">Verwenden Sie `EventCallback` und `EventCallback<T>` für Ereignisverarbeitungs-und Bindungskomponenten Parameter.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-181">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="b4eb6-182">Bevorzugen der stark typisierten `EventCallback<T>` über `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-182">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="b4eb6-183">`EventCallback<T>` bietet den Benutzern der Komponente ein besseres Fehler Feedback.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-183">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="b4eb6-184">Ähnlich wie bei anderen Benutzeroberflächen-Ereignis Handlern ist die Angabe des Ereignis Parameters optional.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-184">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="b4eb6-185">Verwenden Sie `EventCallback`, wenn kein Wert an den Rückruf übermittelt wird.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-185">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="b4eb6-186">Standard Aktionen verhindern</span><span class="sxs-lookup"><span data-stu-id="b4eb6-186">Prevent default actions</span></span>

<span data-ttu-id="b4eb6-187">Verwenden Sie das [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) direktivenattribut, um die Standardaktion für ein Ereignis zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-187">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="b4eb6-188">Wenn ein Schlüssel auf einem Eingabegerät ausgewählt wird und sich der Element Fokus auf einem Textfeld befindet, zeigt ein Browser normalerweise das Schlüsselzeichen im Textfeld an.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-188">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="b4eb6-189">Im folgenden Beispiel wird das Standardverhalten verhindert, indem das `@onkeypress:preventDefault` direktivenattribut angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-189">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="b4eb6-190">Der Wert des Leistungs Zählers wird erhöht, und der **+** Schlüssel wird nicht in den Wert des `<input>` Elements aufgezeichnet:</span><span class="sxs-lookup"><span data-stu-id="b4eb6-190">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="b4eb6-191">Das Angeben des `@on{EVENT}:preventDefault` Attributs ohne Wert entspricht `@on{EVENT}:preventDefault="true"`.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-191">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="b4eb6-192">Der Wert des-Attributs kann auch ein Ausdruck sein.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-192">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="b4eb6-193">Im folgenden Beispiel ist `_shouldPreventDefault` ein `bool` Feld, das entweder auf `true` oder `false`festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="b4eb6-193">In the following example, `_shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

<span data-ttu-id="b4eb6-194">Ein Ereignishandler ist nicht erforderlich, um die Standardaktion zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-194">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="b4eb6-195">Der Ereignishandler und die Vermeidung von Standard Aktions Szenarien können unabhängig voneinander verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="b4eb6-195">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="b4eb6-196">Ereignis Weitergabe Abbrechen</span><span class="sxs-lookup"><span data-stu-id="b4eb6-196">Stop event propagation</span></span>

<span data-ttu-id="b4eb6-197">Verwenden Sie das Attribut [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) Direktive, um die Ereignis Weitergabe zu verhindern</span><span class="sxs-lookup"><span data-stu-id="b4eb6-197">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="b4eb6-198">Im folgenden Beispiel wird durch Aktivieren des Kontrollkästchens verhindert, dass Click-Ereignisse aus dem zweiten untergeordneten `<div>` an die übergeordnete `<div>`weitergegeben werden:</span><span class="sxs-lookup"><span data-stu-id="b4eb6-198">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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

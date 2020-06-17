---
title: ASP.NET Core Blazor-Ereignisbehandlung
author: guardrex
description: Informieren Sie sich über die Features zur Ereignisbehandlung von Blazor, einschließlich Ereignisargumenttypen, Ereignisrückrufe und die Verwaltung von standardmäßigen Browserereignissen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: 2d806413eb66d4c1301df4a0db950e8b919c09a4
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2020
ms.locfileid: "84419748"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="c054f-103">ASP.NET Core Blazor-Ereignisbehandlung</span><span class="sxs-lookup"><span data-stu-id="c054f-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="c054f-104">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="c054f-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="c054f-105">-Komponenten bieten Features zur Ereignisbehandlung.</span><span class="sxs-lookup"><span data-stu-id="c054f-105"> components provide event handling features.</span></span> <span data-ttu-id="c054f-106">Bei einem HTML-Elementattribut namens [`@on{EVENT}`](xref:mvc/views/razor#onevent) (z. B. `@onclick`) mit einem Wert vom Typ „Delegat“ behandelt eine Razor-Komponente den Wert des Attributs als einen Ereignishandler.</span><span class="sxs-lookup"><span data-stu-id="c054f-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="c054f-107">Der folgende Code ruft die `UpdateHeading`-Methode auf, wenn die Schaltfläche auf der Benutzeroberfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="c054f-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="c054f-108">Der folgende Code ruft die `CheckChanged`-Methode auf, wenn das Kontrollkästchen auf der Benutzeroberfläche geändert wird:</span><span class="sxs-lookup"><span data-stu-id="c054f-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="c054f-109">Ereignishandler können auch asynchron sein und ein <xref:System.Threading.Tasks.Task> zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="c054f-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="c054f-110">Es ist nicht erforderlich, [StateHasChanged](xref:blazor/lifecycle#state-changes) manuell aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="c054f-110">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="c054f-111">Ausnahmen werden protokolliert, wenn sie auftreten.</span><span class="sxs-lookup"><span data-stu-id="c054f-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="c054f-112">Im folgenden Beispiel wird `UpdateHeading` asynchron aufgerufen, wenn die Schaltfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="c054f-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="c054f-113">Ereignisargumenttypen</span><span class="sxs-lookup"><span data-stu-id="c054f-113">Event argument types</span></span>

<span data-ttu-id="c054f-114">Für einige Ereignisse sind Ereignisargumenttypen zulässig.</span><span class="sxs-lookup"><span data-stu-id="c054f-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="c054f-115">Die Angabe eines Ereignistyps im Methodenaufruf ist nur dann erforderlich, wenn der Ereignistyp in der Methode verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c054f-115">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="c054f-116">Die unterstützten <xref:System.EventArgs> sind in der folgenden Tabelle aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="c054f-116">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

| <span data-ttu-id="c054f-117">event</span><span class="sxs-lookup"><span data-stu-id="c054f-117">Event</span></span>            | <span data-ttu-id="c054f-118">Klasse</span><span class="sxs-lookup"><span data-stu-id="c054f-118">Class</span></span>                | <span data-ttu-id="c054f-119">DOM-Ereignisse und -Hinweise</span><span class="sxs-lookup"><span data-stu-id="c054f-119">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="c054f-120">Zwischenablage</span><span class="sxs-lookup"><span data-stu-id="c054f-120">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="c054f-121">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="c054f-121">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="c054f-122">Ziehen</span><span class="sxs-lookup"><span data-stu-id="c054f-122">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="c054f-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="c054f-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="c054f-124"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> und <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> speichern gezogene Elementdaten.</span><span class="sxs-lookup"><span data-stu-id="c054f-124"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> |
| <span data-ttu-id="c054f-125">Fehler</span><span class="sxs-lookup"><span data-stu-id="c054f-125">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="c054f-126">event</span><span class="sxs-lookup"><span data-stu-id="c054f-126">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="c054f-127">*Allgemein*</span><span class="sxs-lookup"><span data-stu-id="c054f-127">*General*</span></span><br><span data-ttu-id="c054f-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="c054f-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="c054f-129">*Zwischenablage*</span><span class="sxs-lookup"><span data-stu-id="c054f-129">*Clipboard*</span></span><br><span data-ttu-id="c054f-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="c054f-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="c054f-131">*Eingabe*</span><span class="sxs-lookup"><span data-stu-id="c054f-131">*Input*</span></span><br><span data-ttu-id="c054f-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span><span class="sxs-lookup"><span data-stu-id="c054f-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span></span><br><br><span data-ttu-id="c054f-133">*Medien*</span><span class="sxs-lookup"><span data-stu-id="c054f-133">*Media*</span></span><br><span data-ttu-id="c054f-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="c054f-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="c054f-135"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> enthält Attribute zum Konfigurieren der Zuordnungen zwischen Ereignisnamen und Ereignisargumenttypen.</span><span class="sxs-lookup"><span data-stu-id="c054f-135"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="c054f-136">Fokus</span><span class="sxs-lookup"><span data-stu-id="c054f-136">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="c054f-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="c054f-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="c054f-138">Umfasst nicht die Unterstützung für `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="c054f-138">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="c054f-139">Eingabe</span><span class="sxs-lookup"><span data-stu-id="c054f-139">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="c054f-140">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="c054f-140">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="c054f-141">Tastatur</span><span class="sxs-lookup"><span data-stu-id="c054f-141">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="c054f-142">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="c054f-142">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="c054f-143">Maus</span><span class="sxs-lookup"><span data-stu-id="c054f-143">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="c054f-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="c054f-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="c054f-145">Mauszeiger</span><span class="sxs-lookup"><span data-stu-id="c054f-145">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="c054f-146">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="c054f-146">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="c054f-147">Mausrad</span><span class="sxs-lookup"><span data-stu-id="c054f-147">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="c054f-148">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="c054f-148">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="c054f-149">Status</span><span class="sxs-lookup"><span data-stu-id="c054f-149">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="c054f-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="c054f-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="c054f-151">Toucheingabe</span><span class="sxs-lookup"><span data-stu-id="c054f-151">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="c054f-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="c054f-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="c054f-153"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> stellt einen einzelnen Kontaktpunkt auf einem Gerät mit Berührungseingabe dar.</span><span class="sxs-lookup"><span data-stu-id="c054f-153"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="c054f-154">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="c054f-154">For more information, see the following resources:</span></span>

* <span data-ttu-id="c054f-155">[EventArgs-Klassen in der ASP.NET Core-Referenzquelle (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="c054f-155">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="c054f-156">[MDN-Webdokumentationen: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Enthält Informationen dazu, welche HTML-Elemente die einzelnen DOM-Ereignisse unterstützen.</span><span class="sxs-lookup"><span data-stu-id="c054f-156">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="c054f-157">Lambdaausdrücke</span><span class="sxs-lookup"><span data-stu-id="c054f-157">Lambda expressions</span></span>

<span data-ttu-id="c054f-158">[Lambdaausdrücke](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) können ebenfalls verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="c054f-158">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="c054f-159">Es ist häufig praktisch, über zusätzliche Werte zusammenzuschlagen, z. B. bei der Iteration über eine Reihe von Elementen.</span><span class="sxs-lookup"><span data-stu-id="c054f-159">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="c054f-160">Das folgende Beispiel erstellt drei Schaltflächen, die jeweils `UpdateHeading` aufrufen, wobei ein Ereignisargument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) und seine Schaltflächennummer (`buttonNumber`) übergeben werden, wenn sie auf der Benutzeroberfläche ausgewählt werden:</span><span class="sxs-lookup"><span data-stu-id="c054f-160">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="c054f-161">Verwenden Sie **keine** Schleifenvariable direkt in einem Lambdaausdruck wie `i` im vorangehenden `for`-Schleifenbeispiel oder einer Verweisvariablen in einer `foreach`-Schleife.</span><span class="sxs-lookup"><span data-stu-id="c054f-161">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example or a reference variable in a `foreach` loop.</span></span> <span data-ttu-id="c054f-162">Ansonsten wird dieselbe Variable von allen Lambdaausdrücken verwendet, sodass der gleiche Wert in allen Lambdaausdrücken verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c054f-162">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="c054f-163">Erfassen Sie den Wert der Variable immer in einer lokalen Variable, und verwenden Sie diese anschließend.</span><span class="sxs-lookup"><span data-stu-id="c054f-163">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="c054f-164">Im vorangegangenen Beispiel wird die Schleifenvariable `i` `buttonNumber` zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="c054f-164">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="c054f-165">EventCallback</span><span class="sxs-lookup"><span data-stu-id="c054f-165">EventCallback</span></span>

<span data-ttu-id="c054f-166">Ein häufiges Szenario mit geschachtelten Komponenten ist der Wunsch, die Methode einer übergeordneten Komponente auszuführen, wenn ein Ereignis einer untergeordneten Komponente eintritt.</span><span class="sxs-lookup"><span data-stu-id="c054f-166">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="c054f-167">Das ein `onclick`-Ereignis in der untergeordneten Komponente auftritt, ist ein gängiger Anwendungsfall.</span><span class="sxs-lookup"><span data-stu-id="c054f-167">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="c054f-168">Um Ereignisse komponentenübergreifend darzustellen, verwenden Sie ein <xref:Microsoft.AspNetCore.Components.EventCallback>.</span><span class="sxs-lookup"><span data-stu-id="c054f-168">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="c054f-169">Eine übergeordnete Komponente kann dem <xref:Microsoft.AspNetCore.Components.EventCallback> einer untergeordneten Komponente eine Rückrufmethode zuweisen.</span><span class="sxs-lookup"><span data-stu-id="c054f-169">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="c054f-170">Der `ChildComponent` in der Beispiel-App (*Components/ChildComponent.razor*) zeigt, wie der `onclick`-Handler einer Schaltfläche so eingerichtet ist, dass er einen <xref:Microsoft.AspNetCore.Components.EventCallback>-Delegaten von der `ParentComponent` des Beispiels empfängt.</span><span class="sxs-lookup"><span data-stu-id="c054f-170">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="c054f-171">Der <xref:Microsoft.AspNetCore.Components.EventCallback> wird mit `MouseEventArgs` typisiert, was für ein `onclick`-Ereignis von einem Peripheriegerät geeignet ist:</span><span class="sxs-lookup"><span data-stu-id="c054f-171">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="c054f-172">Die `ParentComponent` legt die <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) des untergeordneten Elements auf seine `ShowMessage`-Methode fest.</span><span class="sxs-lookup"><span data-stu-id="c054f-172">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="c054f-173">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="c054f-173">*Pages/ParentComponent.razor*:</span></span>

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

<span data-ttu-id="c054f-174">Wenn die Schaltfläche in der `ChildComponent` ausgewählt ist:</span><span class="sxs-lookup"><span data-stu-id="c054f-174">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="c054f-175">Die `ShowMessage`-Methode von `ParentComponent` wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="c054f-175">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="c054f-176">`messageText` wird aktualisiert und in der `ParentComponent` angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c054f-176">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="c054f-177">Ein Aufruf von [StateHasChanged](xref:blazor/lifecycle#state-changes) ist in der Methode des Rückrufs (`ShowMessage`) nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="c054f-177">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="c054f-178"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> wird automatisch aufgerufen, um die `ParentComponent` zu rendern, so wie Ereignisse untergeordneter Elemente das Rendern von Komponenten in Ereignishandlern auslösen, die innerhalb des untergeordneten Elements ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="c054f-178"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="c054f-179"><xref:Microsoft.AspNetCore.Components.EventCallback> und <xref:Microsoft.AspNetCore.Components.EventCallback%601> gestatten asynchrone Delegate.</span><span class="sxs-lookup"><span data-stu-id="c054f-179"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="c054f-180"><xref:Microsoft.AspNetCore.Components.EventCallback%601> ist stark typisiert und erfordert einen bestimmten Argumenttyp.</span><span class="sxs-lookup"><span data-stu-id="c054f-180"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="c054f-181"><xref:Microsoft.AspNetCore.Components.EventCallback> ist schwach typisiert und gestattet jeden Argumenttyp.</span><span class="sxs-lookup"><span data-stu-id="c054f-181"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="c054f-182">Rufen Sie ein <xref:Microsoft.AspNetCore.Components.EventCallback> oder <xref:Microsoft.AspNetCore.Components.EventCallback%601> mit <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> auf, und warten Sie auf das <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="c054f-182">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="c054f-183">Verwenden Sie <xref:Microsoft.AspNetCore.Components.EventCallback> und <xref:Microsoft.AspNetCore.Components.EventCallback%601> für die Ereignisbehandlung und die Bindung von Komponentenparametern.</span><span class="sxs-lookup"><span data-stu-id="c054f-183">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="c054f-184">Bevorzugen Sie das stark typisierte <xref:Microsoft.AspNetCore.Components.EventCallback%601> gegenüber dem <xref:Microsoft.AspNetCore.Components.EventCallback>.</span><span class="sxs-lookup"><span data-stu-id="c054f-184">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="c054f-185"><xref:Microsoft.AspNetCore.Components.EventCallback%601> bietet den Benutzern der Komponente ein besseres Fehlerfeedback.</span><span class="sxs-lookup"><span data-stu-id="c054f-185"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="c054f-186">Ähnlich wie bei anderen UI-Ereignishandlern ist die Angabe des Ereignisparameters optional.</span><span class="sxs-lookup"><span data-stu-id="c054f-186">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="c054f-187">Verwenden Sie <xref:Microsoft.AspNetCore.Components.EventCallback>, wenn kein Wert an den Rückruf übergeben wurde.</span><span class="sxs-lookup"><span data-stu-id="c054f-187">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="c054f-188">Verhindern von Standardaktionen</span><span class="sxs-lookup"><span data-stu-id="c054f-188">Prevent default actions</span></span>

<span data-ttu-id="c054f-189">Verwenden Sie das Direktivenattribut [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault), um die Standardaktion für ein Ereignis zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="c054f-189">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="c054f-190">Wenn ein Schlüssel auf einem Eingabegerät ausgewählt wird und der Elementfokus auf einem Textfeld liegt, zeigt ein Browser normalerweise das Zeichen des Schlüssels in dem Textfeld an.</span><span class="sxs-lookup"><span data-stu-id="c054f-190">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="c054f-191">Im folgenden Beispiel wird das Standardverhalten durch die Angabe des Direktivenattributs `@onkeypress:preventDefault` verhindert.</span><span class="sxs-lookup"><span data-stu-id="c054f-191">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="c054f-192">Der Zähler wird inkrementiert und der Schlüssel **+** wird nicht im Wert des `<input>`-Elements erfasst:</span><span class="sxs-lookup"><span data-stu-id="c054f-192">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="c054f-193">Die Angabe des Attributs `@on{EVENT}:preventDefault` ohne Wert ist gleichbedeutend mit `@on{EVENT}:preventDefault="true"`.</span><span class="sxs-lookup"><span data-stu-id="c054f-193">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="c054f-194">Der Wert des Attributs kann auch ein Ausdruck sein.</span><span class="sxs-lookup"><span data-stu-id="c054f-194">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="c054f-195">Im folgenden Beispiel ist `shouldPreventDefault` ein `bool`-Feld, das entweder auf `true` oder `false` festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="c054f-195">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

<span data-ttu-id="c054f-196">Ein Ereignishandler ist nicht erforderlich, um die Standardaktion zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="c054f-196">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="c054f-197">Die Szenarien für Ereignishandler und die Verhinderung von Standardaktionen können unabhängig voneinander verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c054f-197">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="c054f-198">Beenden der Ereignisweitergabe</span><span class="sxs-lookup"><span data-stu-id="c054f-198">Stop event propagation</span></span>

<span data-ttu-id="c054f-199">Verwenden Sie das Direktivenattribut [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation), um die Ereignisweitergabe zu beenden.</span><span class="sxs-lookup"><span data-stu-id="c054f-199">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="c054f-200">Im folgenden Beispiel verhindert das Aktivieren des Kontrollkästchens die Weitergabe von Klickereignissen des zweiten untergeordneten `<div>`-Elements an das übergeordnete `<div>`-Element:</span><span class="sxs-lookup"><span data-stu-id="c054f-200">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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

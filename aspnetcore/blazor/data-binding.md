---
title: "title: 'ASP.NET Core-Datenbindung in Blazor' author: description: 'Erfahren Sie mehr über die Datenbindungsfeatures für Komponenten und DOM-Elemente in Blazor-Apps.'"
author: guardrex
description: 'monikerRange: ms.author: ms.custom: ms.date: no-loc:'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/data-binding
ms.openlocfilehash: 3b419b4738bd3f434cf4a9d8ccdf3b3af86ba1d5
ms.sourcegitcommit: d8a2b036ce7abdac519e6ade4422f9e5956fc4c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "83998496"
---
# <a name="aspnet-core-blazor-data-binding"></a><span data-ttu-id="c897d-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c897d-103">'Blazor'</span></span>

<span data-ttu-id="c897d-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c897d-104">'Identity'</span></span>

<span data-ttu-id="c897d-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c897d-105">'Let's Encrypt'</span></span>

<span data-ttu-id="c897d-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c897d-106">'Razor'</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="c897d-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c897d-107">'SignalR' uid:</span></span>

<span data-ttu-id="c897d-108">ASP.NET Core Blazor-Datenbindung</span><span class="sxs-lookup"><span data-stu-id="c897d-108">ASP.NET Core Blazor data binding</span></span> <span data-ttu-id="c897d-109">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="c897d-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="c897d-110">-Komponenten stellen Features zur Datenbindung über ein HTML-Elementattribut namens [`@bind`](xref:mvc/views/razor#bind) bereit, mit einem Feld, einer Eigenschaft oder einem Razor-Ausdruckswert.</span><span class="sxs-lookup"><span data-stu-id="c897d-110"> components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="c897d-111">Das folgende Beispiel bindet die `CurrentValue`-Eigenschaft an den Wert des Textfelds:</span><span class="sxs-lookup"><span data-stu-id="c897d-111">The following example binds the `CurrentValue` property to the text box's value:</span></span> <span data-ttu-id="c897d-112">Wenn das Textfeld den Fokus verliert, wird der Wert der Eigenschaft aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="c897d-112">When the text box loses focus, the property's value is updated.</span></span> <span data-ttu-id="c897d-113">Das Textfeld wird auf der Benutzeroberfläche nur dann aktualisiert, wenn die Komponente gerendert wird, nicht als Reaktion auf die Änderung des Werts der Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="c897d-113">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="c897d-114">Da sich Komponenten nach der Ausführung von Ereignishandlercode selbst rendern, werden Eigenschaftsaktualisierungen *in der Regel* unmittelbar nach dem Auslösen eines Ereignishandlers auf der Benutzeroberfläche widergespiegelt.</span><span class="sxs-lookup"><span data-stu-id="c897d-114">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="c897d-115">Die Verwendung von [`@bind`](xref:mvc/views/razor#bind) mit der `CurrentValue`-Eigenschaft (`<input @bind="CurrentValue" />`) entspricht im Wesentlichen dem Folgenden:</span><span class="sxs-lookup"><span data-stu-id="c897d-115">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span> <span data-ttu-id="c897d-116">Wenn die Komponente gerendert wird, stammt der `value` des Eingabeelements aus der `CurrentValue`-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="c897d-116">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="c897d-117">Wenn der Benutzer in das Textfeld eingibt und den Elementfokus ändert, wird das Ereignis `onchange` ausgelöst und die Eigenschaft `CurrentValue` auf den geänderten Wert festgelegt.</span><span class="sxs-lookup"><span data-stu-id="c897d-117">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span>

<span data-ttu-id="c897d-118">In Wirklichkeit ist die Codegenerierung komplexer, weil [`@bind`](xref:mvc/views/razor#bind) Fälle behandelt, in denen Typkonvertierungen durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="c897d-118">In reality, the code generation is more complex because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="c897d-119">Im Prinzip assoziiert [`@bind`](xref:mvc/views/razor#bind) den aktuellen Wert eines Ausdrucks mit einem `value`-Attribut und behandelt Änderungen mit dem registrierten Handler.</span><span class="sxs-lookup"><span data-stu-id="c897d-119">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

```razor
@page "/binding-example"

<p>
    <input type="text" @bind="paragraphStyle" />
</p>

<p @bind-style="paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string paragraphStyle = "color:red";
}
```

<span data-ttu-id="c897d-120">Binden Sie eine Eigenschaft oder ein Feld an andere Ereignisse, indem Sie auch ein `@bind:event`-Attribut mit einem `event`-Parameter einbeziehen.</span><span class="sxs-lookup"><span data-stu-id="c897d-120">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span>

* <span data-ttu-id="c897d-121">Das folgende Beispiel bindet die Eigenschaft `CurrentValue` an das Ereignis `oninput`:</span><span class="sxs-lookup"><span data-stu-id="c897d-121">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>
* <span data-ttu-id="c897d-122">Im Gegensatz zu `onchange`, das ausgelöst wird, wenn das Element den Fokus verliert, wird `oninput` ausgelöst, wenn sich der Wert des Textfelds ändert.</span><span class="sxs-lookup"><span data-stu-id="c897d-122">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="c897d-123">Verwenden Sie `@bind-{ATTRIBUTE}` mit der `@bind-{ATTRIBUTE}:event`-Syntax, um andere Elementattribute als `value` zu binden.</span><span class="sxs-lookup"><span data-stu-id="c897d-123">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span>

<span data-ttu-id="c897d-124">Im folgenden Beispiel wird der Stil des Absatzes aktualisiert, wenn sich der Wert `paragraphStyle` ändert:</span><span class="sxs-lookup"><span data-stu-id="c897d-124">In the following example, the paragraph's style is updated when the `paragraphStyle` value changes:</span></span>

<span data-ttu-id="c897d-125">Bei der Attributbindung wird die Groß- und Kleinschreibung berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="c897d-125">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="c897d-126">`@bind` ist gültig.</span><span class="sxs-lookup"><span data-stu-id="c897d-126">`@bind` is valid.</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="c897d-127">`@Bind` und `@BIND` sind ungültig.</span><span class="sxs-lookup"><span data-stu-id="c897d-127">`@Bind` and `@BIND` are invalid.</span></span>

<span data-ttu-id="c897d-128">Nicht analysierbare Werte</span><span class="sxs-lookup"><span data-stu-id="c897d-128">Unparsable values</span></span> <span data-ttu-id="c897d-129">Wenn ein Benutzer einem Element mit Datenbindung einen nicht analysierbaren Wert zur Verfügung stellt, wird der nicht analysierbare Wert automatisch auf seinen vorherigen Wert zurückgesetzt, wenn das Bindungsereignis ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="c897d-129">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="c897d-130">Betrachten Sie das folgende Szenario:</span><span class="sxs-lookup"><span data-stu-id="c897d-130">Consider the following scenario:</span></span> <span data-ttu-id="c897d-131">Ein `<input>`-Element ist an einen `int`-Typ mit einem Anfangswert von `123` gebunden:</span><span class="sxs-lookup"><span data-stu-id="c897d-131">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span> <span data-ttu-id="c897d-132">Der Benutzer aktualisiert den Wert des Elements auf der Seite zu `123.45` und ändert den Elementfokus.</span><span class="sxs-lookup"><span data-stu-id="c897d-132">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span> <span data-ttu-id="c897d-133">Im vorangehenden Szenario wird der Wert des Elements auf `123` zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="c897d-133">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="c897d-134">Wenn der Wert `123.45` zugunsten des ursprünglichen Werts von `123` abgelehnt wird, versteht der Benutzer, dass sein Wert nicht akzeptiert wurde.</span><span class="sxs-lookup"><span data-stu-id="c897d-134">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span> <span data-ttu-id="c897d-135">Standardmäßig gilt die Bindung für das `onchange`-Ereignis des Elements (`@bind="{PROPERTY OR FIELD}"`).</span><span class="sxs-lookup"><span data-stu-id="c897d-135">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="c897d-136">Verwenden Sie `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}`, um die Bindung bei einem anderen Ereignis auszulösen.</span><span class="sxs-lookup"><span data-stu-id="c897d-136">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span>

* <span data-ttu-id="c897d-137">Für das `oninput`-Ereignis (`@bind:event="oninput"`) erfolgt die Umkehrung nach jedem Tastendruck, der einen nicht analysierbaren Wert einführt.</span><span class="sxs-lookup"><span data-stu-id="c897d-137">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="c897d-138">Wenn das `oninput`-Ereignis mit einem `int`-gebundenen Typ angestrebt wird, wird ein Benutzer daran gehindert, ein `.`-Zeichen einzugeben.</span><span class="sxs-lookup"><span data-stu-id="c897d-138">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span>
* <span data-ttu-id="c897d-139">Ein `.`-Zeichen wird sofort entfernt, sodass der Benutzer sofort die Rückmeldung erhält, dass nur ganze Zahlen zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="c897d-139">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span>
* <span data-ttu-id="c897d-140">Es gibt Szenarien, in denen die Umkehrung des Werts auf das `oninput` Ereignis nicht ideal ist, z. B. wenn dem Benutzer erlaubt werden soll, einen nicht analysierbaren `<input>`-Wert zu löschen.</span><span class="sxs-lookup"><span data-stu-id="c897d-140">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="c897d-141">Zu den Alternativen gehören:</span><span class="sxs-lookup"><span data-stu-id="c897d-141">Alternatives include:</span></span> <span data-ttu-id="c897d-142">Verwenden Sie nicht das Ereignis `oninput`.</span><span class="sxs-lookup"><span data-stu-id="c897d-142">Don't use the `oninput` event.</span></span>
  * <span data-ttu-id="c897d-143">Verwenden Sie das Standardereignis `onchange` (geben Sie nur `@bind="{PROPERTY OR FIELD}"` an), bei dem ein ungültiger Wert erst dann rückgängig gemacht wird, wenn das Element den Fokus verliert.</span><span class="sxs-lookup"><span data-stu-id="c897d-143">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
  * <span data-ttu-id="c897d-144">Binden Sie an einen Nullable-Typ, z. B. `int?` oder `string`, und stellen Sie eine benutzerdefinierte Logik zur Behandlung ungültiger Einträge bereit.</span><span class="sxs-lookup"><span data-stu-id="c897d-144">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>

## <a name="format-strings"></a><span data-ttu-id="c897d-145">Verwenden Sie eine [Formularüberprüfungskomponente](xref:blazor/forms-validation) wie <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> oder <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span><span class="sxs-lookup"><span data-stu-id="c897d-145">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span>

<span data-ttu-id="c897d-146">Formularüberprüfungskomponenten verfügen über die integrierte Unterstützung der Verwaltung ungültiger Eingaben.</span><span class="sxs-lookup"><span data-stu-id="c897d-146">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="c897d-147">Formularüberprüfungskomponenten:</span><span class="sxs-lookup"><span data-stu-id="c897d-147">Form validation components:</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="c897d-148">Erlauben Sie dem Benutzer, ungültige Eingaben zu machen und Überprüfungsfehler für das zugehörige <xref:Microsoft.AspNetCore.Components.Forms.EditContext> zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="c897d-148">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="c897d-149">Zeigen Sie Überprüfungsfehler auf der Benutzeroberfläche an, ohne den Benutzer bei der Eingabe zusätzlicher WebForm-Daten zu beeinträchtigen.</span><span class="sxs-lookup"><span data-stu-id="c897d-149">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="c897d-150">Formatzeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="c897d-150">Format strings</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="c897d-151">Die Datenbindung funktioniert mit <xref:System.DateTime>-Formatzeichenfolgen, die `@bind:format` verwenden.</span><span class="sxs-lookup"><span data-stu-id="c897d-151">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span>

<span data-ttu-id="c897d-152">Andere Formatausdrücke wie Währungs- oder Zahlenformate sind zu diesem Zeitpunkt nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="c897d-152">Other format expressions, such as currency or number formats, aren't available at this time.</span></span> <span data-ttu-id="c897d-153">Im vorhergehenden Code ist der Feldtyp des `<input>`-Elements (`type`) standardmäßig auf `text` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="c897d-153">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span>

<span data-ttu-id="c897d-154">`@bind:format` wird für die Bindung der folgenden .NET-Typen unterstützt:</span><span class="sxs-lookup"><span data-stu-id="c897d-154">`@bind:format` is supported for binding the following .NET types:</span></span> <span data-ttu-id="c897d-155"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="c897d-155"><xref:System.DateTime?displayProperty=fullName>?</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="c897d-156"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="c897d-156"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="c897d-157">Das `@bind:format`-Attribut gibt das Datumsformat an, das auf den `value` des `<input>`-Elements angewendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="c897d-157">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="c897d-158">Das Format wird auch verwendet, um den Wert beim Eintreten eines `onchange`-Ereignisses zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="c897d-158">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="c897d-159">Die Angabe eines Formats für den Feldtyp `date` wird nicht empfohlen, da Blazor eine integrierte Unterstützung für die Formatierung von Daten bietet.</span><span class="sxs-lookup"><span data-stu-id="c897d-159">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span>

```razor
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="c897d-160">Verwenden Sie trotz der Empfehlung nur dann das Datumsformat `yyyy-MM-dd` für die Bindung, um ordnungsgemäß zu arbeiten, wenn ein Format mit dem Feldtyp `date` bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="c897d-160">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

<span data-ttu-id="c897d-161">Bindung zwischen übergeordnetem und untergeordnetem Element mit Komponentenparametern</span><span class="sxs-lookup"><span data-stu-id="c897d-161">Parent-to-child binding with component parameters</span></span>

* <span data-ttu-id="c897d-162">Die Bindung erkennt Komponentenparameter, bei denen `@bind-{PROPERTY}` einen Eigenschaftswert von einer übergeordneten Komponente an eine untergeordnete Komponente binden kann.</span><span class="sxs-lookup"><span data-stu-id="c897d-162">Binding recognizes component parameters, where `@bind-{PROPERTY}` can bind a property value from a parent component down to a child component.</span></span>
* <span data-ttu-id="c897d-163">Die Bindung von einem untergeordneten Element an ein übergeordnetes Element wird im Abschnitt [Bindung zwischen untergeordnetem und übergeordnetem Element mit verketteter Bindung](#child-to-parent-binding-with-chained-bind) behandelt.</span><span class="sxs-lookup"><span data-stu-id="c897d-163">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span> <span data-ttu-id="c897d-164">Die folgende untergeordnete Komponente (`ChildComponent`) hat einen `Year`-Komponentenparameter und einen `YearChanged`-Rückruf:</span><span class="sxs-lookup"><span data-stu-id="c897d-164">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    public int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

<span data-ttu-id="c897d-165"><xref:Microsoft.AspNetCore.Components.EventCallback%601> wird in <xref:blazor/event-handling#eventcallback> erläutert.</span><span class="sxs-lookup"><span data-stu-id="c897d-165"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is explained in <xref:blazor/event-handling#eventcallback>.</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="c897d-166">Die folgende übergeordnete Komponente verwendet Folgendes:</span><span class="sxs-lookup"><span data-stu-id="c897d-166">The following parent component uses:</span></span> <span data-ttu-id="c897d-167">`ChildComponent` und bindet den `ParentYear`-Parameter der übergeordneten Komponente an den `Year`-Parameter in der untergeordneten Komponente.</span><span class="sxs-lookup"><span data-stu-id="c897d-167">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="c897d-168">Das Ereignis `onclick` wird zur Auslösung der `ChangeTheYear`-Methode verwendet.</span><span class="sxs-lookup"><span data-stu-id="c897d-168">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span>

<span data-ttu-id="c897d-169">Weitere Informationen finden Sie unter <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="c897d-169">For more information, see <xref:blazor/event-handling>.</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="c897d-170">Das Laden von `ParentComponent` erzeugt das folgende Markup:</span><span class="sxs-lookup"><span data-stu-id="c897d-170">Loading the `ParentComponent` produces the following markup:</span></span> <span data-ttu-id="c897d-171">Wenn der Wert der Eigenschaft `ParentYear` durch Auswahl der Schaltfläche in der `ParentComponent` geändert wird, wird die Eigenschaft `Year` der `ChildComponent` aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="c897d-171">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="c897d-172">Der neue Wert von `Year` wird auf der Benutzeroberfläche gerendert, wenn die `ParentComponent` gerendert wird:</span><span class="sxs-lookup"><span data-stu-id="c897d-172">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

<span data-ttu-id="c897d-173">Der Parameter `Year` ist bindbar, da er ein Begleitereignis `YearChanged` aufweist, das dem Typ des Parameters `Year` entspricht.</span><span class="sxs-lookup"><span data-stu-id="c897d-173">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span> <span data-ttu-id="c897d-174">Gemäß Konvention ist `<ChildComponent @bind-Year="ParentYear" />` im Wesentlichen gleichbedeutend mit dem Schreiben von:</span><span class="sxs-lookup"><span data-stu-id="c897d-174">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

<span data-ttu-id="c897d-175">Im Allgemeinen kann eine Eigenschaft an einen entsprechenden Ereignishandler gebunden werden, indem ein `@bind-{PROPRETY}:event`-Attribut einbezogen wird.</span><span class="sxs-lookup"><span data-stu-id="c897d-175">In general, a property can be bound to a corresponding event handler by including an `@bind-{PROPRETY}:event` attribute.</span></span> <span data-ttu-id="c897d-176">Beispielsweise kann die Eigenschaft `MyProp` mit den folgenden beiden Attributen an `MyEventHandler` gebunden werden:</span><span class="sxs-lookup"><span data-stu-id="c897d-176">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span> <span data-ttu-id="c897d-177">Bindung zwischen untergeordnetem und übergeordnetem Element mit verketteter Bindung</span><span class="sxs-lookup"><span data-stu-id="c897d-177">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="c897d-178">Ein häufiges Szenario ist die Verkettung eines datengebundenen Parameters mit einem Seitenelement in der Ausgabe der Komponente.</span><span class="sxs-lookup"><span data-stu-id="c897d-178">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span>

* <span data-ttu-id="c897d-179">Dieses Szenario wird als *verkettete Bindung* bezeichnet, da mehrere Ebenen der Bindung gleichzeitig auftreten.</span><span class="sxs-lookup"><span data-stu-id="c897d-179">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>
* <span data-ttu-id="c897d-180">Eine verkettete Bindung kann nicht mit der [`@bind`](xref:mvc/views/razor#bind)-Syntax im Element der Seite implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="c897d-180">A chained bind can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the page's element.</span></span>
* <span data-ttu-id="c897d-181">Der Ereignishandler und der Wert müssen separat angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="c897d-181">The event handler and value must be specified separately.</span></span> <span data-ttu-id="c897d-182">Eine übergeordnete Komponente kann jedoch die [`@bind`](xref:mvc/views/razor#bind)-Syntax mit dem Parameter der Komponente verwenden.</span><span class="sxs-lookup"><span data-stu-id="c897d-182">A parent component, however, can use [`@bind`](xref:mvc/views/razor#bind) syntax with the component's parameter.</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="c897d-183">Die folgende `PasswordField`-Komponente (*Passwortfeld.razor*):</span><span class="sxs-lookup"><span data-stu-id="c897d-183">The following `PasswordField` component (*PasswordField.razor*):</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="c897d-184">Legt den Wert eines `<input>`-Elements auf eine `Password`-Eigenschaft fest.</span><span class="sxs-lookup"><span data-stu-id="c897d-184">Sets an `<input>` element's value to a `Password` property.</span></span>

* <span data-ttu-id="c897d-185">Macht Änderungen der `Password`-Eigenschaft einer übergeordneten Komponente mit einem [EventCallback](xref:blazor/event-handling#eventcallback) verfügbar.</span><span class="sxs-lookup"><span data-stu-id="c897d-185">Exposes changes of the `Password` property to a parent component with an [EventCallback](xref:blazor/event-handling#eventcallback).</span></span>
* <span data-ttu-id="c897d-186">Verwendet das `onclick`-Ereignis zum Auslösen der `ToggleShowPassword`-Methode.</span><span class="sxs-lookup"><span data-stu-id="c897d-186">Uses the `onclick` event is used to trigger the `ToggleShowPassword` method.</span></span>

<span data-ttu-id="c897d-187">Weitere Informationen finden Sie unter <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="c897d-187">For more information, see <xref:blazor/event-handling>.</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password
    {
        get { return password ?? string.Empty; }
        set
        {
            if (password != value)
            {
                if (value.Contains(' '))
                {
                    validationMessage = "Spaces not allowed!";
                }
                else
                {
                    password = value;
                    validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="c897d-188">Die `PasswordField`-Komponente wird in einer anderen Komponente verwendet:</span><span class="sxs-lookup"><span data-stu-id="c897d-188">The `PasswordField` component is used in another component:</span></span>

<span data-ttu-id="c897d-189">So führen Sie Prüfungen durch oder fangen Fehler beim Kennwort im vorhergehenden Beispiel ab</span><span class="sxs-lookup"><span data-stu-id="c897d-189">To perform checks or trap errors on the password in the preceding example:</span></span>

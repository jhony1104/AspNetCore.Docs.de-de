---
title: ASP.net Core Blazor Datenbindung
author: guardrex
description: Erfahren Sie mehr über Daten Bindungs Szenarien für Komponenten und DOM-Elemente in Blazor-apps.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/data-binding
ms.openlocfilehash: c38e6095d4e93d3eead10fa8bb0356b2113bb220
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453157"
---
# <a name="aspnet-core-opno-locblazor-data-binding"></a><span data-ttu-id="56dce-103">ASP.net Core Blazor Datenbindung</span><span class="sxs-lookup"><span data-stu-id="56dce-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="56dce-104">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="56dce-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="56dce-105">Die Datenbindung an beide Komponenten und DOM-Elemente erfolgt mit dem [`@bind`](xref:mvc/views/razor#bind) -Attribut.</span><span class="sxs-lookup"><span data-stu-id="56dce-105">Data binding to both components and DOM elements is accomplished with the [`@bind`](xref:mvc/views/razor#bind) attribute.</span></span> <span data-ttu-id="56dce-106">Im folgenden Beispiel wird eine `CurrentValue`-Eigenschaft an den Wert des Textfelds gebunden:</span><span class="sxs-lookup"><span data-stu-id="56dce-106">The following example binds a `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="56dce-107">Wenn das Textfeld den Fokus verliert, wird der Wert der Eigenschaft aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="56dce-107">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="56dce-108">Das Textfeld wird nur dann in der Benutzeroberfläche aktualisiert, wenn die Komponente gerendert wird, nicht als Reaktion auf das Ändern des Eigenschafts Werts.</span><span class="sxs-lookup"><span data-stu-id="56dce-108">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="56dce-109">Da Komponenten nach der Ausführung des Ereignishandlercodes selbst gerenden werden, werden Eigenschafts Aktualisierungen in der Benutzeroberfläche *normalerweise* sofort nach dem Auslösen eines Ereignis Handlers</span><span class="sxs-lookup"><span data-stu-id="56dce-109">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="56dce-110">Die Verwendung von `@bind` mit der `CurrentValue`-Eigenschaft (`<input @bind="CurrentValue" />`) entspricht im wesentlichen folgendem:</span><span class="sxs-lookup"><span data-stu-id="56dce-110">Using `@bind` with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="56dce-111">Wenn die Komponente gerendert wird, stammt der `value` des Eingabe Elements aus der `CurrentValue`-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="56dce-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="56dce-112">Wenn der Benutzer das Textfeld eingibt und den Element Fokus ändert, wird das `onchange`-Ereignis ausgelöst, und die `CurrentValue`-Eigenschaft wird auf den geänderten Wert festgelegt.</span><span class="sxs-lookup"><span data-stu-id="56dce-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="56dce-113">In der Realität ist die Codegenerierung komplexer, da `@bind` Fälle behandelt, in denen Typkonvertierungen durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="56dce-113">In reality, the code generation is more complex because `@bind` handles cases where type conversions are performed.</span></span> <span data-ttu-id="56dce-114">Im Prinzip ordnet `@bind` den aktuellen Wert eines Ausdrucks einem `value`-Attribut zu und behandelt Änderungen mithilfe des registrierten Handlers.</span><span class="sxs-lookup"><span data-stu-id="56dce-114">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="56dce-115">Zusätzlich zur Behandlung von `onchange` Ereignissen mit `@bind` Syntax kann eine Eigenschaft oder ein Feld mit anderen Ereignissen gebunden werden, indem ein [`@bind-value`](xref:mvc/views/razor#bind) Attribut mit einem `event` Parameter ([`@bind-value:event`](xref:mvc/views/razor#bind)) angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="56dce-115">In addition to handling `onchange` events with `@bind` syntax, a property or field can be bound using other events by specifying an [`@bind-value`](xref:mvc/views/razor#bind) attribute with an `event` parameter ([`@bind-value:event`](xref:mvc/views/razor#bind)).</span></span> <span data-ttu-id="56dce-116">Im folgenden Beispiel wird die `CurrentValue`-Eigenschaft für das `oninput`-Ereignis gebunden:</span><span class="sxs-lookup"><span data-stu-id="56dce-116">The following example binds the `CurrentValue` property for the `oninput` event:</span></span>

```razor
<input @bind-value="CurrentValue" @bind-value:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="56dce-117">Im Gegensatz zu `onchange`, das ausgelöst wird, wenn das Element den Fokus verliert, `oninput` ausgelöst, wenn sich der Wert des Textfelds ändert.</span><span class="sxs-lookup"><span data-stu-id="56dce-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="56dce-118">im vorherigen Beispiel wird `@bind-value` gebunden:</span><span class="sxs-lookup"><span data-stu-id="56dce-118">`@bind-value` in the preceding example binds:</span></span>

* <span data-ttu-id="56dce-119">Der angegebene Ausdruck (`CurrentValue`) zum `value` Attribut des Elements.</span><span class="sxs-lookup"><span data-stu-id="56dce-119">The specified expression (`CurrentValue`) to the element's `value` attribute.</span></span>
* <span data-ttu-id="56dce-120">Ein Änderungs Ereignis Delegat für das Ereignis, das durch `@bind-value:event`angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="56dce-120">A change event delegate to the event specified by `@bind-value:event`.</span></span>

### <a name="unparsable-values"></a><span data-ttu-id="56dce-121">Nicht zu erteilbare Werte</span><span class="sxs-lookup"><span data-stu-id="56dce-121">Unparsable values</span></span>

<span data-ttu-id="56dce-122">Wenn ein Benutzer einen nicht zu erteilbaren Wert für ein Daten gebundene Element bereitstellt, wird der nicht teilbare Wert automatisch auf seinen vorherigen Wert zurückgesetzt, wenn das Bindungs Ereignis ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="56dce-122">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="56dce-123">Als Beispiel dient das folgende Szenario:</span><span class="sxs-lookup"><span data-stu-id="56dce-123">Consider the following scenario:</span></span>

* <span data-ttu-id="56dce-124">Ein `<input>`-Element ist an einen `int` Typ mit einem Anfangswert von `123`gebunden:</span><span class="sxs-lookup"><span data-stu-id="56dce-124">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="56dce-125">Der Benutzer aktualisiert den Wert des-Elements auf `123.45` auf der Seite und ändert den Element Fokus.</span><span class="sxs-lookup"><span data-stu-id="56dce-125">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="56dce-126">Im vorangehenden Szenario wird der Wert des Elements auf `123`zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="56dce-126">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="56dce-127">Wenn der Wert `123.45` zugunsten des ursprünglichen Werts `123`abgelehnt wird, erkennt der Benutzer, dass sein Wert nicht akzeptiert wurde.</span><span class="sxs-lookup"><span data-stu-id="56dce-127">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="56dce-128">Standardmäßig gilt die Bindung für das `onchange` Ereignis des Elements (`@bind="{PROPERTY OR FIELD}"`).</span><span class="sxs-lookup"><span data-stu-id="56dce-128">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="56dce-129">Verwenden Sie `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}`, um ein anderes Ereignis festzulegen.</span><span class="sxs-lookup"><span data-stu-id="56dce-129">Use `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` to set a different event.</span></span> <span data-ttu-id="56dce-130">Für das `oninput`-Ereignis (`@bind-value:event="oninput"`) tritt die Neuversion nach jedem Tastatur Strich auf, der einen nicht zu deerbaren Wert einführt.</span><span class="sxs-lookup"><span data-stu-id="56dce-130">For the `oninput` event (`@bind-value:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="56dce-131">Wenn das `oninput` Ereignis mit einem `int`gebundenen Typ als Ziel verwendet wird, wird verhindert, dass ein Benutzer ein `.` Zeichen eingibt.</span><span class="sxs-lookup"><span data-stu-id="56dce-131">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="56dce-132">Ein `.` Zeichen wird sofort entfernt, sodass der Benutzer sofort Feedback erhält, dass nur ganze Zahlen zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="56dce-132">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="56dce-133">Es gibt Szenarios, in denen das Wiederherstellen des Werts für das `oninput` Ereignis nicht ideal ist, z. b. wenn der Benutzer einen nicht zu testbaren `<input>` Wert löschen darf.</span><span class="sxs-lookup"><span data-stu-id="56dce-133">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="56dce-134">Zu Alternativen gehören:</span><span class="sxs-lookup"><span data-stu-id="56dce-134">Alternatives include:</span></span>

* <span data-ttu-id="56dce-135">Verwenden Sie das `oninput`-Ereignis nicht.</span><span class="sxs-lookup"><span data-stu-id="56dce-135">Don't use the `oninput` event.</span></span> <span data-ttu-id="56dce-136">Verwenden Sie das Standard `onchange` Ereignis (`@bind="{PROPERTY OR FIELD}"`), bei dem ein ungültiger Wert nicht wieder hergestellt wird, bis das Element den Fokus verliert.</span><span class="sxs-lookup"><span data-stu-id="56dce-136">Use the default `onchange` event (`@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="56dce-137">Binden Sie an einen Typ, der NULL-Werte zulässt, wie z. b. `int?` oder `string`, und stellen Sie eine benutzerdefinierte Logik bereit,</span><span class="sxs-lookup"><span data-stu-id="56dce-137">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="56dce-138">Verwenden Sie eine [Formular Validierungs Komponente](xref:blazor/forms-validation), z. b. `InputNumber` oder `InputDate`.</span><span class="sxs-lookup"><span data-stu-id="56dce-138">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="56dce-139">Komponenten Validierungs Komponenten verfügen über eine integrierte Unterstützung zum Verwalten Ungültiger Eingaben.</span><span class="sxs-lookup"><span data-stu-id="56dce-139">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="56dce-140">Komponenten Validierungs Komponenten:</span><span class="sxs-lookup"><span data-stu-id="56dce-140">Form validation components:</span></span>
  * <span data-ttu-id="56dce-141">Ermöglicht es dem Benutzer, ungültige Eingaben bereitzustellen und Validierungs Fehler auf dem zugeordneten `EditContext`zu empfangen.</span><span class="sxs-lookup"><span data-stu-id="56dce-141">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="56dce-142">Zeigen Sie Validierungs Fehler in der Benutzeroberfläche an, ohne dass der Benutzer die Eingabe zusätzlicher Webform-Daten beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="56dce-142">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

### <a name="format-strings"></a><span data-ttu-id="56dce-143">Format Zeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="56dce-143">Format strings</span></span>

<span data-ttu-id="56dce-144">Die Datenbindung funktioniert mit <xref:System.DateTime>-Format Zeichenfolgen [`@bind:format`](xref:mvc/views/razor#bind).</span><span class="sxs-lookup"><span data-stu-id="56dce-144">Data binding works with <xref:System.DateTime> format strings using [`@bind:format`](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="56dce-145">Andere Format Ausdrücke, z. b. Währungs-oder Zahlenformate, sind zurzeit nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="56dce-145">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="56dce-146">Im vorangehenden Code ist der Feldtyp (`type`) des `<input>` Elements standardmäßig `text`.</span><span class="sxs-lookup"><span data-stu-id="56dce-146">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="56dce-147">`@bind:format` wird für die Bindung der folgenden .NET-Typen unterstützt:</span><span class="sxs-lookup"><span data-stu-id="56dce-147">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="56dce-148"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="56dce-148"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="56dce-149"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="56dce-149"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="56dce-150">Das `@bind:format`-Attribut gibt das Datumsformat an, das auf die `value` des `<input>` Elements angewendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="56dce-150">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="56dce-151">Das-Format wird auch verwendet, um den Wert zu analysieren, wenn ein `onchange` Ereignis auftritt.</span><span class="sxs-lookup"><span data-stu-id="56dce-151">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="56dce-152">Es wird nicht empfohlen, ein Format für den `date` Feldtyp anzugeben, da Blazor über eine integrierte Unterstützung zum Formatieren von Datumsangaben verfügt.</span><span class="sxs-lookup"><span data-stu-id="56dce-152">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="56dce-153">Verwenden Sie trotz der Empfehlung nur das `yyyy-MM-dd` Datumsformat für die Bindung, um ordnungsgemäß zu funktionieren, wenn ein Format mit dem `date` Feldtyp bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="56dce-153">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

### <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="56dce-154">Über-zu-untergeordnete Bindung mit Komponenten Parametern</span><span class="sxs-lookup"><span data-stu-id="56dce-154">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="56dce-155">Die Bindung erkennt Komponenten Parameter, bei denen `@bind-{property}` einen Eigenschafts Wert von einer übergeordneten Komponente an eine untergeordnete Komponente binden kann.</span><span class="sxs-lookup"><span data-stu-id="56dce-155">Binding recognizes component parameters, where `@bind-{property}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="56dce-156">Die Bindung von einem untergeordneten zu einem übergeordneten Element wird in der Bindung zwischen untergeordneten und über [geordneten Elementen mit verketteter](#child-to-parent-binding-with-chained-bind) Bindung behandelt.</span><span class="sxs-lookup"><span data-stu-id="56dce-156">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="56dce-157">Die folgende untergeordnete Komponente (`ChildComponent`) verfügt über einen `Year` Component-Parameter und `YearChanged`-Rückruf:</span><span class="sxs-lookup"><span data-stu-id="56dce-157">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="56dce-158">`EventCallback<T>` wird in <xref:blazor/event-handling#eventcallback>erläutert.</span><span class="sxs-lookup"><span data-stu-id="56dce-158">`EventCallback<T>` is explained in <xref:blazor/event-handling#eventcallback>.</span></span>

<span data-ttu-id="56dce-159">Die folgende übergeordnete Komponente verwendet:</span><span class="sxs-lookup"><span data-stu-id="56dce-159">The following parent component uses:</span></span>

* <span data-ttu-id="56dce-160">`ChildComponent` und bindet den `ParentYear`-Parameter vom übergeordneten zum `Year`-Parameter in der untergeordneten Komponente.</span><span class="sxs-lookup"><span data-stu-id="56dce-160">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="56dce-161">Das `onclick`-Ereignis wird verwendet, um die `ChangeTheYear`-Methode zu initiieren.</span><span class="sxs-lookup"><span data-stu-id="56dce-161">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="56dce-162">Weitere Informationen finden Sie unter <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="56dce-162">For more information, see <xref:blazor/event-handling>.</span></span>

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

<span data-ttu-id="56dce-163">Beim Laden des `ParentComponent` wird das folgende Markup erzeugt:</span><span class="sxs-lookup"><span data-stu-id="56dce-163">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="56dce-164">Wenn der Wert der `ParentYear`-Eigenschaft durch Auswählen der Schaltfläche im `ParentComponent`geändert wird, wird die Eigenschaft `Year` der `ChildComponent` aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="56dce-164">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="56dce-165">Der neue Wert `Year` wird in der Benutzeroberfläche gerendert, wenn die `ParentComponent` erneut ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="56dce-165">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="56dce-166">Der `Year`-Parameter ist bindbar, da er über ein Begleit `YearChanged` Ereignis verfügt, das mit dem Typ des `Year` Parameters übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="56dce-166">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="56dce-167">Gemäß der Konvention ist `<ChildComponent @bind-Year="ParentYear" />` im wesentlichen Äquivalent zum Schreiben:</span><span class="sxs-lookup"><span data-stu-id="56dce-167">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="56dce-168">Im Allgemeinen kann eine Eigenschaft mithilfe `@bind-property:event` Attributs an einen entsprechenden Ereignishandler gebunden werden.</span><span class="sxs-lookup"><span data-stu-id="56dce-168">In general, a property can be bound to a corresponding event handler using `@bind-property:event` attribute.</span></span> <span data-ttu-id="56dce-169">Beispielsweise kann die-Eigenschaft `MyProp` an `MyEventHandler` mithilfe der folgenden beiden Attribute gebunden werden:</span><span class="sxs-lookup"><span data-stu-id="56dce-169">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

### <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="56dce-170">Bindung zwischen untergeordneten und übergeordneten Elementen mit verketteter Bindung</span><span class="sxs-lookup"><span data-stu-id="56dce-170">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="56dce-171">Ein häufiges Szenario ist die Verkettung eines Daten gebundenen Parameters zu einem Page-Element in der Ausgabe der Komponente.</span><span class="sxs-lookup"><span data-stu-id="56dce-171">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="56dce-172">Dieses Szenario wird als *verkettete Bindung* bezeichnet, da mehrere Bindungs Ebenen gleichzeitig auftreten.</span><span class="sxs-lookup"><span data-stu-id="56dce-172">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="56dce-173">Eine verkettete Bindung kann nicht mit `@bind`-Syntax im-Element der Seite implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="56dce-173">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="56dce-174">Der Ereignishandler und der Wert müssen separat angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="56dce-174">The event handler and value must be specified separately.</span></span> <span data-ttu-id="56dce-175">Eine übergeordnete Komponente kann jedoch `@bind`-Syntax mit dem-Parameter der Komponente verwenden.</span><span class="sxs-lookup"><span data-stu-id="56dce-175">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="56dce-176">Die folgende `PasswordField` Komponente (*PasswordField. Razor*):</span><span class="sxs-lookup"><span data-stu-id="56dce-176">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="56dce-177">Legt den Wert eines `<input>` Elements auf eine `Password` Eigenschaft fest.</span><span class="sxs-lookup"><span data-stu-id="56dce-177">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="56dce-178">Macht Änderungen der `Password`-Eigenschaft für eine übergeordnete Komponente mit einem [EventCallback](xref:blazor/event-handling#eventcallback)verfügbar.</span><span class="sxs-lookup"><span data-stu-id="56dce-178">Exposes changes of the `Password` property to a parent component with an [EventCallback](xref:blazor/event-handling#eventcallback).</span></span>
* <span data-ttu-id="56dce-179">Verwendet das `onclick` Ereignis wird verwendet, um die `ToggleShowPassword`-Methode zu initiieren.</span><span class="sxs-lookup"><span data-stu-id="56dce-179">Uses the `onclick` event is used to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="56dce-180">Weitere Informationen finden Sie unter <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="56dce-180">For more information, see <xref:blazor/event-handling>.</span></span>

```razor
<h1>Child Component</h2>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool _showPassword;

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
        _showPassword = !_showPassword;
    }
}
```

<span data-ttu-id="56dce-181">Die `PasswordField` Komponente wird in einer anderen Komponente verwendet:</span><span class="sxs-lookup"><span data-stu-id="56dce-181">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

<span data-ttu-id="56dce-182">Zum Ausführen von Überprüfungen oder Trap Fehlern für das Kennwort im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="56dce-182">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="56dce-183">Erstellen Sie ein Unterstützungs Feld für `Password` (`_password` im folgenden Beispielcode).</span><span class="sxs-lookup"><span data-stu-id="56dce-183">Create a backing field for `Password` (`_password` in the following example code).</span></span>
* <span data-ttu-id="56dce-184">Führen Sie die Überprüfungen oder Trap Fehler im `Password` Setter aus.</span><span class="sxs-lookup"><span data-stu-id="56dce-184">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="56dce-185">Im folgenden Beispiel wird dem Benutzer sofortiges Feedback bereitgestellt, wenn ein Leerzeichen im Wert des Kennworts verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="56dce-185">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@_validationMessage</span>

@code {
    private bool _showPassword;
    private string _password;
    private string _validationMessage;

    [Parameter]
    public string Password
    {
        get { return _password ?? string.Empty; }
        set
        {
            if (_password != value)
            {
                if (value.Contains(' '))
                {
                    _validationMessage = "Spaces not allowed!";
                }
                else
                {
                    _password = value;
                    _validationMessage = string.Empty;
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
        _showPassword = !_showPassword;
    }
}
```

### <a name="radio-buttons"></a><span data-ttu-id="56dce-186">Optionsfelder</span><span class="sxs-lookup"><span data-stu-id="56dce-186">Radio buttons</span></span>

<span data-ttu-id="56dce-187">Informationen zum Binden an Options Felder in einem Formular finden Sie unter <xref:blazor/forms-validation#work-with-radio-buttons>.</span><span class="sxs-lookup"><span data-stu-id="56dce-187">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>

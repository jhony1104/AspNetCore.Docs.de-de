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
# <a name="aspnet-core-opno-locblazor-data-binding"></a>ASP.net Core Blazor Datenbindung

Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)

Die Datenbindung an beide Komponenten und DOM-Elemente erfolgt mit dem [`@bind`](xref:mvc/views/razor#bind) -Attribut. Im folgenden Beispiel wird eine `CurrentValue`-Eigenschaft an den Wert des Textfelds gebunden:

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

Wenn das Textfeld den Fokus verliert, wird der Wert der Eigenschaft aktualisiert.

Das Textfeld wird nur dann in der Benutzeroberfläche aktualisiert, wenn die Komponente gerendert wird, nicht als Reaktion auf das Ändern des Eigenschafts Werts. Da Komponenten nach der Ausführung des Ereignishandlercodes selbst gerenden werden, werden Eigenschafts Aktualisierungen in der Benutzeroberfläche *normalerweise* sofort nach dem Auslösen eines Ereignis Handlers

Die Verwendung von `@bind` mit der `CurrentValue`-Eigenschaft (`<input @bind="CurrentValue" />`) entspricht im wesentlichen folgendem:

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

Wenn die Komponente gerendert wird, stammt der `value` des Eingabe Elements aus der `CurrentValue`-Eigenschaft. Wenn der Benutzer das Textfeld eingibt und den Element Fokus ändert, wird das `onchange`-Ereignis ausgelöst, und die `CurrentValue`-Eigenschaft wird auf den geänderten Wert festgelegt. In der Realität ist die Codegenerierung komplexer, da `@bind` Fälle behandelt, in denen Typkonvertierungen durchgeführt werden. Im Prinzip ordnet `@bind` den aktuellen Wert eines Ausdrucks einem `value`-Attribut zu und behandelt Änderungen mithilfe des registrierten Handlers.

Zusätzlich zur Behandlung von `onchange` Ereignissen mit `@bind` Syntax kann eine Eigenschaft oder ein Feld mit anderen Ereignissen gebunden werden, indem ein [`@bind-value`](xref:mvc/views/razor#bind) Attribut mit einem `event` Parameter ([`@bind-value:event`](xref:mvc/views/razor#bind)) angegeben wird. Im folgenden Beispiel wird die `CurrentValue`-Eigenschaft für das `oninput`-Ereignis gebunden:

```razor
<input @bind-value="CurrentValue" @bind-value:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

Im Gegensatz zu `onchange`, das ausgelöst wird, wenn das Element den Fokus verliert, `oninput` ausgelöst, wenn sich der Wert des Textfelds ändert.

im vorherigen Beispiel wird `@bind-value` gebunden:

* Der angegebene Ausdruck (`CurrentValue`) zum `value` Attribut des Elements.
* Ein Änderungs Ereignis Delegat für das Ereignis, das durch `@bind-value:event`angegeben wird.

### <a name="unparsable-values"></a>Nicht zu erteilbare Werte

Wenn ein Benutzer einen nicht zu erteilbaren Wert für ein Daten gebundene Element bereitstellt, wird der nicht teilbare Wert automatisch auf seinen vorherigen Wert zurückgesetzt, wenn das Bindungs Ereignis ausgelöst wird.

Als Beispiel dient das folgende Szenario:

* Ein `<input>`-Element ist an einen `int` Typ mit einem Anfangswert von `123`gebunden:

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* Der Benutzer aktualisiert den Wert des-Elements auf `123.45` auf der Seite und ändert den Element Fokus.

Im vorangehenden Szenario wird der Wert des Elements auf `123`zurückgesetzt. Wenn der Wert `123.45` zugunsten des ursprünglichen Werts `123`abgelehnt wird, erkennt der Benutzer, dass sein Wert nicht akzeptiert wurde.

Standardmäßig gilt die Bindung für das `onchange` Ereignis des Elements (`@bind="{PROPERTY OR FIELD}"`). Verwenden Sie `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}`, um ein anderes Ereignis festzulegen. Für das `oninput`-Ereignis (`@bind-value:event="oninput"`) tritt die Neuversion nach jedem Tastatur Strich auf, der einen nicht zu deerbaren Wert einführt. Wenn das `oninput` Ereignis mit einem `int`gebundenen Typ als Ziel verwendet wird, wird verhindert, dass ein Benutzer ein `.` Zeichen eingibt. Ein `.` Zeichen wird sofort entfernt, sodass der Benutzer sofort Feedback erhält, dass nur ganze Zahlen zulässig sind. Es gibt Szenarios, in denen das Wiederherstellen des Werts für das `oninput` Ereignis nicht ideal ist, z. b. wenn der Benutzer einen nicht zu testbaren `<input>` Wert löschen darf. Zu Alternativen gehören:

* Verwenden Sie das `oninput`-Ereignis nicht. Verwenden Sie das Standard `onchange` Ereignis (`@bind="{PROPERTY OR FIELD}"`), bei dem ein ungültiger Wert nicht wieder hergestellt wird, bis das Element den Fokus verliert.
* Binden Sie an einen Typ, der NULL-Werte zulässt, wie z. b. `int?` oder `string`, und stellen Sie eine benutzerdefinierte Logik bereit,
* Verwenden Sie eine [Formular Validierungs Komponente](xref:blazor/forms-validation), z. b. `InputNumber` oder `InputDate`. Komponenten Validierungs Komponenten verfügen über eine integrierte Unterstützung zum Verwalten Ungültiger Eingaben. Komponenten Validierungs Komponenten:
  * Ermöglicht es dem Benutzer, ungültige Eingaben bereitzustellen und Validierungs Fehler auf dem zugeordneten `EditContext`zu empfangen.
  * Zeigen Sie Validierungs Fehler in der Benutzeroberfläche an, ohne dass der Benutzer die Eingabe zusätzlicher Webform-Daten beeinträchtigt.

### <a name="format-strings"></a>Format Zeichenfolgen

Die Datenbindung funktioniert mit <xref:System.DateTime>-Format Zeichenfolgen [`@bind:format`](xref:mvc/views/razor#bind). Andere Format Ausdrücke, z. b. Währungs-oder Zahlenformate, sind zurzeit nicht verfügbar.

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

Im vorangehenden Code ist der Feldtyp (`type`) des `<input>` Elements standardmäßig `text`. `@bind:format` wird für die Bindung der folgenden .NET-Typen unterstützt:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

Das `@bind:format`-Attribut gibt das Datumsformat an, das auf die `value` des `<input>` Elements angewendet werden soll. Das-Format wird auch verwendet, um den Wert zu analysieren, wenn ein `onchange` Ereignis auftritt.

Es wird nicht empfohlen, ein Format für den `date` Feldtyp anzugeben, da Blazor über eine integrierte Unterstützung zum Formatieren von Datumsangaben verfügt. Verwenden Sie trotz der Empfehlung nur das `yyyy-MM-dd` Datumsformat für die Bindung, um ordnungsgemäß zu funktionieren, wenn ein Format mit dem `date` Feldtyp bereitgestellt wird:

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

### <a name="parent-to-child-binding-with-component-parameters"></a>Über-zu-untergeordnete Bindung mit Komponenten Parametern

Die Bindung erkennt Komponenten Parameter, bei denen `@bind-{property}` einen Eigenschafts Wert von einer übergeordneten Komponente an eine untergeordnete Komponente binden kann. Die Bindung von einem untergeordneten zu einem übergeordneten Element wird in der Bindung zwischen untergeordneten und über [geordneten Elementen mit verketteter](#child-to-parent-binding-with-chained-bind) Bindung behandelt.

Die folgende untergeordnete Komponente (`ChildComponent`) verfügt über einen `Year` Component-Parameter und `YearChanged`-Rückruf:

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

`EventCallback<T>` wird in <xref:blazor/event-handling#eventcallback>erläutert.

Die folgende übergeordnete Komponente verwendet:

* `ChildComponent` und bindet den `ParentYear`-Parameter vom übergeordneten zum `Year`-Parameter in der untergeordneten Komponente.
* Das `onclick`-Ereignis wird verwendet, um die `ChangeTheYear`-Methode zu initiieren. Weitere Informationen finden Sie unter <xref:blazor/event-handling>.

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

Beim Laden des `ParentComponent` wird das folgende Markup erzeugt:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

Wenn der Wert der `ParentYear`-Eigenschaft durch Auswählen der Schaltfläche im `ParentComponent`geändert wird, wird die Eigenschaft `Year` der `ChildComponent` aktualisiert. Der neue Wert `Year` wird in der Benutzeroberfläche gerendert, wenn die `ParentComponent` erneut ausgeführt wird:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

Der `Year`-Parameter ist bindbar, da er über ein Begleit `YearChanged` Ereignis verfügt, das mit dem Typ des `Year` Parameters übereinstimmt.

Gemäß der Konvention ist `<ChildComponent @bind-Year="ParentYear" />` im wesentlichen Äquivalent zum Schreiben:

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

Im Allgemeinen kann eine Eigenschaft mithilfe `@bind-property:event` Attributs an einen entsprechenden Ereignishandler gebunden werden. Beispielsweise kann die-Eigenschaft `MyProp` an `MyEventHandler` mithilfe der folgenden beiden Attribute gebunden werden:

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

### <a name="child-to-parent-binding-with-chained-bind"></a>Bindung zwischen untergeordneten und übergeordneten Elementen mit verketteter Bindung

Ein häufiges Szenario ist die Verkettung eines Daten gebundenen Parameters zu einem Page-Element in der Ausgabe der Komponente. Dieses Szenario wird als *verkettete Bindung* bezeichnet, da mehrere Bindungs Ebenen gleichzeitig auftreten.

Eine verkettete Bindung kann nicht mit `@bind`-Syntax im-Element der Seite implementiert werden. Der Ereignishandler und der Wert müssen separat angegeben werden. Eine übergeordnete Komponente kann jedoch `@bind`-Syntax mit dem-Parameter der Komponente verwenden.

Die folgende `PasswordField` Komponente (*PasswordField. Razor*):

* Legt den Wert eines `<input>` Elements auf eine `Password` Eigenschaft fest.
* Macht Änderungen der `Password`-Eigenschaft für eine übergeordnete Komponente mit einem [EventCallback](xref:blazor/event-handling#eventcallback)verfügbar.
* Verwendet das `onclick` Ereignis wird verwendet, um die `ToggleShowPassword`-Methode zu initiieren. Weitere Informationen finden Sie unter <xref:blazor/event-handling>.

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

Die `PasswordField` Komponente wird in einer anderen Komponente verwendet:

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

Zum Ausführen von Überprüfungen oder Trap Fehlern für das Kennwort im vorherigen Beispiel:

* Erstellen Sie ein Unterstützungs Feld für `Password` (`_password` im folgenden Beispielcode).
* Führen Sie die Überprüfungen oder Trap Fehler im `Password` Setter aus.

Im folgenden Beispiel wird dem Benutzer sofortiges Feedback bereitgestellt, wenn ein Leerzeichen im Wert des Kennworts verwendet wird:

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

### <a name="radio-buttons"></a>Optionsfelder

Informationen zum Binden an Options Felder in einem Formular finden Sie unter <xref:blazor/forms-validation#work-with-radio-buttons>.

---
title: ASP.NET Core Blazor-Datenbindung
author: guardrex
description: In diesem Artikel lernen Sie Datenbindungsfeatures für Komponenten und DOM-Elemente in Blazor-Apps kennen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/data-binding
ms.openlocfilehash: a7b3730dad48b5bbb6134dab181051da4e3651b4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80320949"
---
# <a name="aspnet-core-opno-locblazor-data-binding"></a>ASP.NET Core Blazor-Datenbindung

Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)

Razor-Komponenten stellen Features zur Datenbindung über ein HTML-Elementattribut namens [`@bind`](xref:mvc/views/razor#bind) bereit, mit einem Feld, einer Eigenschaft oder einem Razor-Ausdruckswert.

Das folgende Beispiel bindet die `CurrentValue`-Eigenschaft an den Wert des Textfelds:

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

Wenn das Textfeld den Fokus verliert, wird der Wert der Eigenschaft aktualisiert.

Das Textfeld wird auf der Benutzeroberfläche nur dann aktualisiert, wenn die Komponente gerendert wird, nicht als Reaktion auf die Änderung des Werts der Eigenschaft. Da sich Komponenten nach der Ausführung von Ereignishandlercode selbst rendern, werden Eigenschaftsaktualisierungen *in der Regel* unmittelbar nach dem Auslösen eines Ereignishandlers auf der Benutzeroberfläche widergespiegelt.

Die Verwendung von `@bind` mit der `CurrentValue`-Eigenschaft (`<input @bind="CurrentValue" />`) entspricht im Wesentlichen dem Folgenden:

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

Wenn die Komponente gerendert wird, stammt der `value` des Eingabeelements aus der `CurrentValue`-Eigenschaft. Wenn der Benutzer in das Textfeld eingibt und den Elementfokus ändert, wird das Ereignis `onchange` ausgelöst und die Eigenschaft `CurrentValue` auf den geänderten Wert festgelegt. In Wirklichkeit ist die Codegenerierung komplexer, weil `@bind` Fälle behandelt, in denen Typkonvertierungen durchgeführt werden. Im Prinzip assoziiert `@bind` den aktuellen Wert eines Ausdrucks mit einem `value`-Attribut und behandelt Änderungen mit dem registrierten Handler.

Binden Sie eine Eigenschaft oder ein Feld an andere Ereignisse, indem Sie auch ein `@bind:event`-Attribut mit einem `event`-Parameter einbeziehen. Das folgende Beispiel bindet die Eigenschaft `CurrentValue` an das Ereignis `oninput`:

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

Im Gegensatz zu `onchange`, das ausgelöst wird, wenn das Element den Fokus verliert, wird `oninput` ausgelöst, wenn sich der Wert des Textfelds ändert.

Verwenden Sie `@bind-{ATTRIBUTE}` mit der `@bind-{ATTRIBUTE}:event`-Syntax, um andere Elementattribute als `value` zu binden. Im folgenden Beispiel wird der Stil des Absatzes aktualisiert, wenn sich der Wert `_paragraphStyle` ändert:

```razor
@page "/binding-example"

<p>
    <input type="text" @bind="_paragraphStyle" />
</p>

<p @bind-style="_paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string _paragraphStyle = "color:red";
}
```

Bei der Attributbindung wird die Groß- und Kleinschreibung berücksichtigt. Beispielsweise ist `@bind` zulässig, aber `@Bind` ist unzulässig.

## <a name="unparsable-values"></a>Nicht analysierbare Werte

Wenn ein Benutzer einem Element mit Datenbindung einen nicht analysierbaren Wert zur Verfügung stellt, wird der nicht analysierbare Wert automatisch auf seinen vorherigen Wert zurückgesetzt, wenn das Bindungsereignis ausgelöst wird.

Betrachten Sie das folgende Szenario:

* Ein `<input>`-Element ist an einen `int`-Typ mit einem Anfangswert von `123` gebunden:

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* Der Benutzer aktualisiert den Wert des Elements auf der Seite zu `123.45` und ändert den Elementfokus.

Im vorangehenden Szenario wird der Wert des Elements auf `123` zurückgesetzt. Wenn der Wert `123.45` zugunsten des ursprünglichen Werts von `123` abgelehnt wird, versteht der Benutzer, dass sein Wert nicht akzeptiert wurde.

Standardmäßig gilt die Bindung für das `onchange`-Ereignis des Elements (`@bind="{PROPERTY OR FIELD}"`). Verwenden Sie `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}`, um die Bindung bei einem anderen Ereignis auszulösen. Für das `oninput`-Ereignis (`@bind:event="oninput"`) erfolgt die Umkehrung nach jedem Tastendruck, der einen nicht analysierbaren Wert einführt. Wenn das `oninput`-Ereignis mit einem `int`-gebundenen Typ angestrebt wird, wird ein Benutzer daran gehindert, ein `.`-Zeichen einzugeben. Ein `.`-Zeichen wird sofort entfernt, sodass der Benutzer sofort die Rückmeldung erhält, dass nur ganze Zahlen zulässig sind. Es gibt Szenarien, in denen die Umkehrung des Werts auf das `oninput` Ereignis nicht ideal ist, z. B. wenn dem Benutzer erlaubt werden soll, einen nicht analysierbaren `<input>`-Wert zu löschen. Zu den Alternativen gehören:

* Verwenden Sie nicht das Ereignis `oninput`. Verwenden Sie das Standardereignis `onchange` (geben Sie nur `@bind="{PROPERTY OR FIELD}"` an), bei dem ein ungültiger Wert erst dann rückgängig gemacht wird, wenn das Element den Fokus verliert.
* Binden Sie an einen Nullable-Typ, z. B. `int?` oder `string`, und stellen Sie eine benutzerdefinierte Logik zur Behandlung ungültiger Einträge bereit.
* Verwenden Sie eine [Formularüberprüfungskomponente](xref:blazor/forms-validation) wie `InputNumber` oder `InputDate`. Formularüberprüfungskomponenten verfügen über die integrierte Unterstützung der Verwaltung ungültiger Eingaben. Formularüberprüfungskomponenten:
  * Erlauben Sie dem Benutzer, ungültige Eingaben zu machen und Überprüfungsfehler für das zugehörige `EditContext` zu erhalten.
  * Zeigen Sie Überprüfungsfehler auf der Benutzeroberfläche an, ohne den Benutzer bei der Eingabe zusätzlicher WebForm-Daten zu beeinträchtigen.

## <a name="format-strings"></a>Formatzeichenfolgen

Die Datenbindung funktioniert mit <xref:System.DateTime>-Formatzeichenfolgen unter Verwendung von [`@bind:format`](xref:mvc/views/razor#bind). Andere Formatausdrücke wie Währungs- oder Zahlenformate sind zu diesem Zeitpunkt nicht verfügbar.

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

Im vorhergehenden Code ist der Feldtyp des `<input>`-Elements (`type`) standardmäßig auf `text` festgelegt. `@bind:format` wird für die Bindung der folgenden .NET-Typen unterstützt:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

Das `@bind:format`-Attribut gibt das Datumsformat an, das auf den `value` des `<input>`-Elements angewendet werden soll. Das Format wird auch verwendet, um den Wert beim Eintreten eines `onchange`-Ereignisses zu analysieren.

Die Angabe eines Formats für den Feldtyp `date` wird nicht empfohlen, da Blazor eine integrierte Unterstützung für die Formatierung von Daten bietet. Verwenden Sie trotz der Empfehlung nur dann das Datumsformat `yyyy-MM-dd` für die Bindung, um ordnungsgemäß zu arbeiten, wenn ein Format mit dem Feldtyp `date` bereitgestellt wird:

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a>Bindung zwischen übergeordnetem und untergeordnetem Element mit Komponentenparametern

Die Bindung erkennt Komponentenparameter, bei denen `@bind-{PROPERTY}` einen Eigenschaftswert von einer übergeordneten Komponente an eine untergeordnete Komponente binden kann. Die Bindung von einem untergeordneten Element an ein übergeordnetes Element wird im Abschnitt [Bindung zwischen untergeordnetem und übergeordnetem Element mit verketteter Bindung](#child-to-parent-binding-with-chained-bind) behandelt.

Die folgende untergeordnete Komponente (`ChildComponent`) hat einen `Year`-Komponentenparameter und einen `YearChanged`-Rückruf:

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

`EventCallback<T>` wird in <xref:blazor/event-handling#eventcallback> erläutert.

Die folgende übergeordnete Komponente verwendet Folgendes:

* `ChildComponent` und bindet den `ParentYear`-Parameter der übergeordneten Komponente an den `Year`-Parameter in der untergeordneten Komponente.
* Das Ereignis `onclick` wird zur Auslösung der `ChangeTheYear`-Methode verwendet. Weitere Informationen finden Sie unter <xref:blazor/event-handling>.

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

Das Laden von `ParentComponent` erzeugt das folgende Markup:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

Wenn der Wert der Eigenschaft `ParentYear` durch Auswahl der Schaltfläche in der `ParentComponent` geändert wird, wird die Eigenschaft `Year` der `ChildComponent` aktualisiert. Der neue Wert von `Year` wird auf der Benutzeroberfläche gerendert, wenn die `ParentComponent` gerendert wird:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

Der Parameter `Year` ist bindbar, da er ein Begleitereignis `YearChanged` aufweist, das dem Typ des Parameters `Year` entspricht.

Gemäß Konvention ist `<ChildComponent @bind-Year="ParentYear" />` im Wesentlichen gleichbedeutend mit dem Schreiben von:

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

Im Allgemeinen kann eine Eigenschaft an einen entsprechenden Ereignishandler gebunden werden, indem ein `@bind-{PROPRETY}:event`-Attribut einbezogen wird. Beispielsweise kann die Eigenschaft `MyProp` mit den folgenden beiden Attributen an `MyEventHandler` gebunden werden:

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a>Bindung zwischen untergeordnetem und übergeordnetem Element mit verketteter Bindung

Ein häufiges Szenario ist die Verkettung eines datengebundenen Parameters mit einem Seitenelement in der Ausgabe der Komponente. Dieses Szenario wird als *verkettete Bindung* bezeichnet, da mehrere Ebenen der Bindung gleichzeitig auftreten.

Eine verkettete Bindung kann nicht mit der `@bind`-Syntax im Element der Seite implementiert werden. Der Ereignishandler und der Wert müssen separat angegeben werden. Eine übergeordnete Komponente kann jedoch die `@bind`-Syntax mit dem Parameter der Komponente verwenden.

Die folgende `PasswordField`-Komponente (*Passwortfeld.razor*):

* Legt den Wert eines `<input>`-Elements auf eine `Password`-Eigenschaft fest.
* Macht Änderungen der `Password`-Eigenschaft einer übergeordneten Komponente mit einem [EventCallback](xref:blazor/event-handling#eventcallback) verfügbar.
* Verwendet das `onclick`-Ereignis zum Auslösen der `ToggleShowPassword`-Methode. Weitere Informationen finden Sie unter <xref:blazor/event-handling>.

```razor
<h1>Child Component</h1>

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

Die `PasswordField`-Komponente wird in einer anderen Komponente verwendet:

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

So führen Sie Prüfungen durch oder fangen Fehler beim Kennwort im vorhergehenden Beispiel ab

* Erstellen Sie ein Unterstützungsfeld für `Password` (`_password` im folgenden Beispielcode).
* Führen Sie die Prüfungen durch, oder fangen Sie Fehler im `Password`-Setter ab.

Das folgende Beispiel gibt dem Benutzer ein sofortiges Feedback, wenn ein Leerzeichen im Wert des Kennworts verwendet wird:

```razor
<h1>Child Component</h1>

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

## <a name="radio-buttons"></a>Optionsfelder

Informationen zur Bindung an Optionsfelder in einem Formular finden Sie unter <xref:blazor/forms-validation#work-with-radio-buttons>.

---
title: ASP.net Core Blazor Formularen und Validierung
author: guardrex
description: Erfahren Sie, wie Sie Formular-und Feld Validierungs Szenarien in Blazorverwenden.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 6f6fdc13dbb754ecfe06025d496017d3c16951fe
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/17/2020
ms.locfileid: "76159962"
---
# <a name="aspnet-core-opno-locblazor-forms-and-validation"></a>ASP.net Core Blazor Formularen und Validierung

Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

Formulare und Validierung werden in Blazor mithilfe von [Daten Anmerkungen](xref:mvc/models/validation)unterstützt.

Der folgende `ExampleModel` Typ definiert die Validierungs Logik mithilfe von Daten Anmerkungen:

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

Ein Formular wird mithilfe der `EditForm` Komponente definiert. In der folgenden Form werden typische Elemente, Komponenten und Razor-Code veranschaulicht:

```razor
<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

Im vorherigen Beispiel:

* Das Formular überprüft Benutzereingaben im `name` Feld mithilfe der im `ExampleModel`-Typ definierten Validierung. Das Modell wird im `@code` Block der Komponente erstellt und in einem privaten Feld (`exampleModel`) gespeichert. Das-Feld wird dem `Model`-Attribut des `<EditForm>`-Elements zugewiesen.
* Die `@bind-Value` der `InputText` Komponente gebunden:
  * Die Model-Eigenschaft (`exampleModel.Name`) zur `Value`-Eigenschaft der `InputText` Komponente.
  * Ein Änderungs Ereignis Delegat für die `ValueChanged`-Eigenschaft der `InputText` Komponente.
* Die `DataAnnotationsValidator` Komponente fügt die Validierungs Unterstützung mithilfe von Daten Anmerkungen an.
* Die `ValidationSummary` Komponente fasst Validierungs Nachrichten zusammen.
* `HandleValidSubmit` wird ausgelöst, wenn das Formular erfolgreich übermittelt wird (Überprüfung wird bestanden).

Eine Reihe integrierter Eingabe Komponenten ist verfügbar, um Benutzereingaben zu empfangen und zu überprüfen. Eingaben werden überprüft, wenn Sie geändert werden und wenn ein Formular übermittelt wird. In der folgenden Tabelle sind die verfügbaren Eingabe Komponenten aufgeführt.

| Eingabe Komponente | Als&hellip; gerendert       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

Alle Eingabe Komponenten, einschließlich `EditForm`, unterstützen beliebige Attribute. Dem gerenderten HTML-Element wird ein beliebiges Attribut hinzugefügt, das keinem Komponenten Parameter entspricht.

Eingabe Komponenten stellen das Standardverhalten für die Validierung bei der Bearbeitung und das Ändern der CSS-Klasse bereit, um den feldzustand widerzuspiegeln. Einige Komponenten enthalten nützliche Logik für die Verarbeitung. Beispielsweise können `InputDate` und `InputNumber` nicht Parier Bare Werte ordnungsgemäß behandeln, indem Sie Sie als Validierungs Fehler registrieren. Typen, die NULL-Werte akzeptieren können, unterstützen auch die NULL-Zulässigkeit des Zielfelds (z. b. `int?`).

Der folgende `Starship` Typ definiert die Validierungs Logik mit einem größeren Satz von Eigenschaften und Daten Anmerkungen als die früheren `ExampleModel`:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

Im vorherigen Beispiel ist `Description` optional, da keine Daten Anmerkungen vorhanden sind.

Im folgenden Formular werden Benutzereingaben mithilfe der im `Starship` Modell definierten Validierung überprüft:

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

Der `EditForm` erstellt eine `EditContext` als einen [kaskadierenden Wert](xref:blazor/components#cascading-values-and-parameters) , der Metadaten zum Bearbeitungsprozess nachverfolgt, einschließlich der geänderten Felder und der aktuellen Validierungs Meldungen. Der `EditForm` bietet auch praktische Ereignisse für gültige und ungültige übermitteln (`OnValidSubmit`, `OnInvalidSubmit`). Alternativ können Sie `OnSubmit` verwenden, um die Validierungs-und Prüf Feldwerte mit benutzerdefiniertem Validierungscode zu initiieren.

Im folgenden Beispiel:

* Die `HandleSubmit`-Methode wird ausgeführt, wenn die Schaltfläche **senden** ausgewählt ist.
* Das Formular wird mit dem `EditContext`des Formulars überprüft.
* Das Formular wird weiter überprüft, indem der `EditContext` an die `ServerValidate` Methode übergeben wird, die einen Web-API-Endpunkt auf dem Server aufruft (*nicht angezeigt*).
* Zusätzlicher Code wird ausgeführt, abhängig vom Ergebnis der Client-und serverseitigen Validierung durch Überprüfung `isValid`.

```razor
<EditForm EditContext="@editContext" OnSubmit="@HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate() && 
            await ServerValidate(editContext);

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }

    private async Task<bool> ServerValidate(EditContext editContext)
    {
        var serverChecksValid = ...

        return serverChecksValid;
    }
}
```

## <a name="inputtext-based-on-the-input-event"></a>InputText basierend auf dem Eingabe Ereignis

Verwenden Sie die `InputText` Komponente, um eine benutzerdefinierte Komponente zu erstellen, die das Ereignis `input` anstelle des `change` Ereignisses verwendet.

Erstellen Sie eine-Komponente mit dem folgenden Markup, und verwenden Sie die-Komponente, ebenso wie `InputText` verwendet wird:

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a>Arbeiten mit Options Feldern

Beim Arbeiten mit Options Feldern in einem Formular wird die Datenbindung anders behandelt als andere Elemente, da Options Felder als Gruppe ausgewertet werden. Der Wert jeder Options Schaltfläche ist korrigiert, aber der Wert der Optionsfeld Gruppe ist der Wert des ausgewählten Options Felds. Das folgende Beispiel veranschaulicht die Vorgehensweise:

* Verarbeiten der Datenbindung für eine Optionsfeld Gruppe.
* Unterstützen Sie die Validierung mithilfe einer benutzerdefinierten `InputRadio` Komponente.

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

In der folgenden `EditForm` wird die vorangehende `InputRadio` Komponente verwendet, um eine Bewertung vom Benutzer abzurufen und zu überprüfen:

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @model.Rating</p>

@code {
    private Model model = new Model();

    private void HandleValidSubmit()
    {
        Console.WriteLine("valid");
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

## <a name="validation-support"></a>Validierungs Unterstützung

Die `DataAnnotationsValidator` Komponente fügt die Validierungs Unterstützung mithilfe von Daten Anmerkungen an den kaskadierenden `EditContext`an. Das Aktivieren der Unterstützung für die Validierung mithilfe von Daten Anmerkungen erfordert diese explizite Geste. Um ein anderes Validierungssystem als Daten Anmerkungen zu verwenden, ersetzen Sie den `DataAnnotationsValidator` durch eine benutzerdefinierte Implementierung. Die ASP.net Core-Implementierung ist für die Untersuchung in der Verweis Quelle verfügbar: [dataannotationsvalidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[adddataannotationsvalidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).

Blazor führt zwei Validierungs Typen aus:

* Die *Feld Validierung* wird ausgeführt, wenn der Benutzer ein Feld ausstellt. Während der Feld Validierung ordnet die `DataAnnotationsValidator` Komponente alle gemeldeten Überprüfungs Ergebnisse dem Feld zu.
* Die *Modell Validierung* wird ausgeführt, wenn der Benutzer das Formular sendet. Während der Modell Validierung versucht die `DataAnnotationsValidator` Komponente, das Feld basierend auf dem Elementnamen zu ermitteln, den das Validierungs Ergebnis meldet. Validierungs Ergebnisse, die keinem einzelnen Element zugeordnet sind, sind dem Modell und nicht einem Feld zugeordnet.

### <a name="validation-summary-and-validation-message-components"></a>Validierungs Zusammenfassungs-und Validierungs Nachrichten Komponenten

Die `ValidationSummary` Komponente fasst alle Validierungs Nachrichten zusammen, die mit dem taghilfsprogramm für die [Validierungs Zusammenfassung](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)vergleichbar sind:

```razor
<ValidationSummary />
```

Gibt Validierungs Nachrichten für ein bestimmtes Modell mit dem `Model`-Parameter aus:
  
```razor
<ValidationSummary Model="@starship" />
```

Die `ValidationMessage` Komponente zeigt Validierungs Meldungen für ein bestimmtes Feld an, das dem [Validierungs Nachrichten-taghilfsprogramm](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)ähnelt. Geben Sie das Feld für die Überprüfung mit dem `For`-Attribut und einem Lambda-Ausdruck an, der die Modell Eigenschaft benennt:

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

Die Komponenten `ValidationMessage` und `ValidationSummary` unterstützen beliebige Attribute. Ein beliebiges Attribut, das nicht mit einem Komponenten Parameter identisch ist, wird dem generierten `<div>` oder `<ul>` Element hinzugefügt.

### <a name="custom-validation-attributes"></a>Benutzerdefinierte Validierungs Attribute

Um sicherzustellen, dass ein Validierungs Ergebnis ordnungsgemäß mit einem Feld verknüpft ist, wenn Sie ein [benutzerdefiniertes Validierungs Attribut](xref:mvc/models/validation#custom-attributes)verwenden, übergeben Sie den <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> des Validierungs Kontexts beim Erstellen des <xref:System.ComponentModel.DataAnnotations.ValidationResult>:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class MyCustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

### <a name="opno-locblazor-data-annotations-validation-package"></a>Validierungs Paket für Blazor Daten Anmerkungen

[Microsoft. aspnetcore.Blazor. DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) ist ein Paket, das Validierungs Erfahrungs Lücken mithilfe der `DataAnnotationsValidator` Komponente füllt. Das Paket ist zurzeit *experimentell*.

### <a name="compareproperty-attribute"></a>[CompareProperty]-Attribut

Der <xref:System.ComponentModel.DataAnnotations.CompareAttribute> funktioniert nicht gut mit der `DataAnnotationsValidator` Komponente, da er das Validierungs Ergebnis nicht einem bestimmten Element zuordnet. Dies kann zu inkonsistentem Verhalten zwischen der Überprüfung auf Feldebene und dem Zeitpunkt der Überprüfung des gesamten Modells auf einem Submit [Microsoft. aspnetcore.Blazor. Mit dem experimentellen DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) -Paket wird ein zusätzliches Validierungs Attribut (`ComparePropertyAttribute`) eingeführt, das diese Einschränkungen behandelt. In einer Blazor-APP ist `[CompareProperty]` ein direkter Ersatz für das `[Compare]`-Attribut.

### <a name="nested-models-collection-types-and-complex-types"></a>Untergeordnete Modelle, Sammlungs Typen und komplexe Typen

Blazor bietet Unterstützung für das Validieren von Formular Eingaben mithilfe von Daten Anmerkungen mit dem integrierten `DataAnnotationsValidator`. Allerdings überprüft das `DataAnnotationsValidator` nur die Eigenschaften der obersten Ebene des Modells, die an das Formular gebunden sind, das keine Eigenschaften vom Typ "Collection" oder "Complex" ist.

Verwenden Sie die `ObjectGraphDataAnnotationsValidator` der *experimentellen* [Microsoft. aspnetcore.Blazor, um das gesamte Objekt Diagramm des gebundenen Modells zu validieren, einschließlich Eigenschaften vom Typ "Collection" und "Complex". DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) -Paket:

```razor
<EditForm Model="@model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

Kommentieren Sie Modell Eigenschaften mit `[ValidateComplexType]`. In den folgenden Modellklassen enthält die `ShipDescription`-Klasse zusätzliche Daten Anmerkungen, die überprüft werden können, wenn das Modell an das Formular gebunden ist:

*Starship.cs*:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; }

    ...
}
```

*ShipDescription.cs*:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }
    
    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

### <a name="enable-the-submit-button-based-on-form-validation"></a>Aktivieren der Schaltfläche "Senden" basierend auf der Formular Validierung

So aktivieren und deaktivieren Sie die Schaltfläche "Senden" basierend auf der Formular Validierung:

* Verwenden Sie das `EditContext` des Formulars, um das Modell zuzuweisen, wenn die Komponente initialisiert wird.
* Überprüfen Sie das Formular im `OnFieldChanged` Rückruf des Kontexts, um die Schaltfläche Senden zu aktivieren und zu deaktivieren.

```razor
<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private bool formInvalid = true;
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);

        editContext.OnFieldChanged += (_, __) =>
        {
            formInvalid = !editContext.Validate();
            StateHasChanged();
        };
    }
}
```

Legen Sie im vorhergehenden Beispiel `formInvalid` auf `false` if:

* Das Formular wird mit gültigen Standardwerten vorab geladen.
* Wenn das Formular geladen wird, soll die Schaltfläche "Senden" aktiviert werden.

Ein Nebeneffekt des vorangehenden Ansatzes besteht darin, dass eine `ValidationSummary` Komponente mit ungültigen Feldern aufgefüllt wird, nachdem der Benutzer mit einem Feld interagiert hat. Dieses Szenario kann auf eine der folgenden Arten adressiert werden:

* Verwenden Sie keine `ValidationSummary` Komponente auf dem Formular.
* Legen Sie die `ValidationSummary` Komponente sichtbar, wenn die Schaltfläche "Senden" ausgewählt ist (z. b. in einer `HandleValidSubmit`-Methode).

```razor
<EditForm EditContext="@editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@displaySummary" />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private string displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        displaySummary = "display:block";
    }
}
```

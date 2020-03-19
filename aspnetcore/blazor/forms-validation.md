---
title: Blazor-Formulare und -Validierung in ASP.NET Core
author: guardrex
description: Hier erfahren Sie, wie Formular- und Feldvalidierungsszenarios in Blazor verwendet werden.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 5aad5a4d4303151ef5be82481dfae7367abeffbc
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083226"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a>Core Blazor-Formulare und -Validierung in ASP.NET Core

Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

Formulare und Validierungen werden in Blazor mithilfe von [Datenanmerkungen](xref:mvc/models/validation) unterstützt.

Der folgende `ExampleModel`-Typ definiert die Validierungslogik mithilfe von Datenanmerkungen:

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

Ein Formular wird mithilfe der `EditForm`-Komponente definiert. Im folgenden Formular sehen Sie typische Elemente, Komponenten und typischen Razor-Code:

```razor
<EditForm Model="@_exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="_exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel _exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

Im vorherigen Beispiel:

* Das Formular validiert Benutzereingaben im `name`-Feld mithilfe der im `ExampleModel`-Typ definierten Validierung. Das Modell wird im `@code`-Block der Komponente erstellt und in einem privaten Feld (`_exampleModel`) gespeichert. Das Feld wird dem `Model`-Attribut des `<EditForm>`-Elements zugewiesen.
* Der `@bind-Value` der `InputText`-Komponente bindet Folgendes:
  * Die Modelleigenschaft (`_exampleModel.Name`) an die `Value`-Eigenschaft der `InputText`-Komponente.
  * Den Delegat eines Änderungsereignisses an die `ValueChanged`-Eigenschaft der `InputText`-Komponente.
* Die `DataAnnotationsValidator`-Komponente fügt Validierungsunterstützung mithilfe von Datenanmerkungen hinzu.
* Die `ValidationSummary`-Komponente fasst Validierungsnachrichten zusammen.
* `HandleValidSubmit` wird ausgelöst, wenn das Formular erfolgreich gesendet wird (die Validierung erfolgreich besteht).

Einige integrierte Eingabekomponenten sind verfügbar, um Benutzereingaben zu empfangen und zu validieren. Eingaben werden validiert, wenn sie geändert werden und wenn ein Formular gesendet wird. In der folgenden Tabelle finden Sie verfügbare Eingabekomponenten.

| Eingabekomponenten | Wird gerendert als&hellip;       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

Alle Eingabekomponenten einschließlich `EditForm` unterstützen arbiträre Attribute. Attribute, die nicht mit einem Komponentenparameter übereinstimmen, werden dem gerenderten HTML-Element hinzugefügt.

Eingabekomponenten stellen Standardverhalten für das Validieren bei Bearbeitung zur Verfügung und ändern ihre CSS-Klasse, um den Feldzustand darzustellen. Einige Komponenten beinhalten hilfreiche Parsinglogik. `InputDate` und `InputNumber` verarbeiten beispielsweise nicht parsbare Werte ordnungsgemäß. Typen, die NULL-Werte akzeptieren, unterstützen auch die NULL-Zulässigkeit des Zielfelds (z. B. `int?`).

Der folgende `Starship`-Typ definiert eine Validierungslogik mithilfe einer größeren Anzahl Eigenschaften und Datenanmerkungen wie die frühere `ExampleModel`-Klasse:

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

Im obigen Beispiel ist `Description` optional, da keine Datenanmerkungen vorhanden sind.

Das folgende Formular validiert Benutzereingaben mithilfe der im `Starship`-Modell definierten Validierung:

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@_starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="_starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="_starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="_starship.Classification">
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
            <InputNumber @bind-Value="_starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="_starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="_starship.ProductionDate" />
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
    private Starship _starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

`EditForm` erstellt `EditContext` als [kaskadierenden Wert](xref:blazor/components#cascading-values-and-parameters), der Metadaten zum Bearbeitungsprozess erfasst, einschließlich der Felder, die geändert wurden und den aktuellen Validierungsnachrichten. `EditForm` stellt auch für gültige und ungültige Sendevorgänge (`OnValidSubmit`, `OnInvalidSubmit`) geeignete Ereignisse zur Verfügung. Alternativ können Sie `OnSubmit` verwenden, um die Validierung auszulösen und Feldwerte mit benutzerdefiniertem Validierungscode zu überprüfen.

Im folgenden Beispiel:

* Die `HandleSubmit`-Methode wird ausgeführt, wenn auf die Schaltfläche **Senden** geklickt wird.
* Das Formular wird mithilfe der `EditContext`-Klasse des Formulars validiert.
* Das Formular wird dann weiter validiert, indem `EditContext` an die `ServerValidate`-Methode übergeben wird, die einen Web-API-Endpunkt auf dem Server aufruft (*wird hier nicht gezeigt*).
* Je nach Ergebnis der client- und serverseitigen Validierung, indem `isValid` überprüft wird, wird weiterer Code ausgeführt.

```razor
<EditForm EditContext="@_editContext" OnSubmit="@HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship _starship = new Starship();
    private EditContext _editContext;

    protected override void OnInitialized()
    {
        _editContext = new EditContext(_starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = _editContext.Validate() && 
            await ServerValidate(_editContext);

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

## <a name="inputtext-based-on-the-input-event"></a>InputText basierend auf dem Eingabeereignis

Verwenden Sie die `InputText`-Komponente, um eine benutzerdefinierte Komponente zu erstellen, die das `input`-Ereignis anstelle des `change`-Ereignisses verwendet.

Erstellen Sie eine Komponente mit dem folgenden Markup, und verwenden Sie die Komponente genauso, wie `InputText` verwendet wird.

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a>Verwenden von Optionsfeldern

Beim Verwenden von Optionsfeldern in einem Formular werden Datenbindungen nicht wie andere Elemente verarbeitet, da Optionsfelder als Gruppe ausgewertet werden. Der Wert der einzelnen Optionsfelder ist fest. Der Wert der Optionsfeldgruppe ist jedoch der Wert des ausgewählten Optionsfelds. Das folgende Beispiel veranschaulicht die Vorgehensweise:

* Verarbeiten von Datenbindungen für eine Optionsfeldgruppe
* Unterstützen der Validierung mithilfe einer benutzerdefinierten `InputRadio`-Komponente

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

Die folgende `EditForm`-Klasse verwendet die vorangehende `InputRadio`-Komponente, um eine Bewertung vom Benutzer zu erhalten und sie zu bewerten:

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="_model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="_model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @_model.Rating</p>

@code {
    private Model _model = new Model();

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

## <a name="validation-support"></a>Validierungsunterstützung

Die `DataAnnotationsValidator`-Komponente fügt Validierungsunterstützung mithilfe von Datenanmerkungen an die kaskadierte `EditContext`-Klasse an. Für das Aktivieren der Validierungsunterstützung mithilfe von Datenanmerkungen ist eine explizite Geste erforderlich. Wenn Sie ein anderes Validierungssystem als Datenanmerkungen verwenden möchten, ersetzen Sie `DataAnnotationsValidator` durch eine benutzerdefinierte Implementierung. Die ASP.NET Core-Implementierung können Sie sich in der Referenzquelle ansehen: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).

Blazor führt zwei Validierungstypen aus:

* Die *Feldvalidierung* wird ausgeführt, wenn der Benutzer auf eine Stelle außerhalb des Felds tippt. Während der Feldvalidierung ordnet die `DataAnnotationsValidator`-Komponente alle gemeldeten Validierungsergebnisse dem Feld zu.
* *Modellvalidierung* wird ausgeführt, wenn der Benutzer ein Formular sendet. Während der Modellvalidierung versucht die `DataAnnotationsValidator`-Komponente, das Feld basierend auf dem Namen des Members zu ermitteln, das vom Validierungsergebnis gemeldet wird. Validierungsergebnisse, die keinem einzelnen Member zugeordnet werden, werden dem Modell und keinem Feld zugeordnet.

### <a name="validation-summary-and-validation-message-components"></a>Komponenten der Validierungszusammenfassung und der Validierungsnachricht

Die `ValidationSummary`-Komponente fasst alle Validierungsnachrichten zusammen. Das ähnelt dem [Taghilfsprogramm für Validierungszusammenfassungen](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):

```razor
<ValidationSummary />
```

Validierungsmeldungen für ein bestimmtes Modell werden mit dem `Model`-Parameter ausgegeben:
  
```razor
<ValidationSummary Model="@_starship" />
```

Die `ValidationMessage`-Komponente zeigt Validierungsnachrichten für ein bestimmtes Feld an. Das ähnelt dem [Taghilfsprogramm für Validierungsmeldungen](xref:mvc/views/working-with-forms#the-validation-message-tag-helper). Das Validierungsfeld wird mit dem `For`-Attribut und einem Lambdaausdruck angegeben, der die Modelleigenschaft benennt:

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

Die `ValidationMessage`- und `ValidationSummary`-Komponenten unterstützen arbiträre Attribute. Attribute, die nicht mit einem Komponentenparameter übereinstimmen, werden dem gerenderten `<div>`- oder `<ul>`-Element hinzugefügt.

### <a name="custom-validation-attributes"></a>Benutzerdefinierte Validierungsattribute

Übergeben Sie die <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName>-Eigenschaft des Validierungskontexts bei der Erstellung der <xref:System.ComponentModel.DataAnnotations.ValidationResult>-Klasse, um sicherzustellen, dass ein Validierungsergebnis korrekt einem Feld zugeordnet wird, wenn ein [benutzerdefiniertes Validierungsattribut](xref:mvc/models/validation#custom-attributes) verwendet wird:

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

### <a name="opno-locblazor-data-annotations-validation-package"></a>Validierungspakete für Datenanmerkungen in Blazor

Bei [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) handelt es sich um ein Paket, das Lücken bei der Validierung mithilfe der `DataAnnotationsValidator`-Komponente beheben kann. Das Paket ist aktuell *experimentell*.

### <a name="compareproperty-attribute"></a>[CompareProperty]-Attribut

<xref:System.ComponentModel.DataAnnotations.CompareAttribute> funktioniert nicht gut mit der `DataAnnotationsValidator`-Komponente, da hier das Validierungsergebnis nicht einem bestimmten Member zugeordnet wird. Das führt zu einem nicht konsistenten Verhalten zwischen Validierung auf Feldebene und der Validierung des gesamten Modells bei Sendevorgängen. Das *experimentelle* Paket [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)-Paket führt ein zusätzliches Validierungsattribut ein (`ComparePropertyAttribute`), das diese Begrenzungen umgehen kann. In einer Blazor-App handelt es sich bei `[CompareProperty]` um eine direkte Ersetzung des `[Compare]`-Attributs.

### <a name="nested-models-collection-types-and-complex-types"></a>Verschachtelte Modelle, Sammlungstypen und komplexe Typen

Blazor bietet Unterstützung für die Validierung von Formulareingaben mithilfe von Datenanmerkungen mit dem integrierten `DataAnnotationsValidator`. `DataAnnotationsValidator` validiert jedoch nur Eigenschaften des Modells auf oberster Ebene, die an das Formular gebunden sind, bei denen es sich aber um keine Sammlungs- oder komplexen Eigenschaften handelt.

Verwenden Sie den vom *experimentellen* Paket [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) bereitgestellten `ObjectGraphDataAnnotationsValidator`, um den gesamten Objektgraph des gebundenen Modells zu validieren, einschließlich Sammlungseigenschaften und komplexen Eigenschaften:

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

Fügen Sie Modelleigenschaften Anmerkungen mit `[ValidateComplexType]` hinzu. In den folgenden Modellklassen enthält die `ShipDescription`-Klasse zusätzliche Datenanmerkungen für die Validierung, wenn das Modell an das Formular gebunden ist:

*Starship.cs:*

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

*ShipDescription.cs:*

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

### <a name="enable-the-submit-button-based-on-form-validation"></a>Aktivieren der Schaltfläche zum Senden basierend auf der Formularvalidierung

So aktivieren oder deaktivieren Sie die Schaltfläche zum Senden basierend auf der Formularvalidierung:

* Verwenden Sie die `EditContext`-Klasse des Formulars, um das Modell zuzuweisen, wenn die Komponente initialisiert wird.
* Validieren Sie das Formular im `OnFieldChanged`-Rückruf des Kontexts, um die Schaltfläche zum Senden zu aktivieren und zu deaktivieren.

```razor
<EditForm EditContext="@_editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@_formInvalid">Submit</button>
</EditForm>

@code {
    private Starship _starship = new Starship();
    private bool _formInvalid = true;
    private EditContext _editContext;

    protected override void OnInitialized()
    {
        _editContext = new EditContext(_starship);

        _editContext.OnFieldChanged += (_, __) =>
        {
            _formInvalid = !_editContext.Validate();
            StateHasChanged();
        };
    }
}
```

Im vorherigen Beispiel ist `_formInvalid` in folgenden Fällen auf `false` festgelegt:

* Das Formular ist bereits mit gültigen Standardwerten ausgefüllt.
* Sie möchten die Schaltfläche zum Senden aktivieren, wenn das Formular geladen wird.

Eine Nebenwirkung des vorangehenden Ansatzes ist, dass eine `ValidationSummary`-Komponente mit ungültigen Feldern aufgefüllt wird, nachdem der Benutzer mit einem beliebigen Feld interagiert hat. Dieses Szenario lässt sich folgendermaßen auflösen:

* Verwenden Sie keine `ValidationSummary`-Komponente des Formulars.
* Lassen Sie die `ValidationSummary`-Komponente einblenden, wenn auf die Schaltfläche zum Senden geklickt wird, z. B. in einer `HandleValidSubmit`-Methode.

```razor
<EditForm EditContext="@_editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@_displaySummary" />

    ...

    <button type="submit" disabled="@_formInvalid">Submit</button>
</EditForm>

@code {
    private string _displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        _displaySummary = "display:block";
    }
}
```

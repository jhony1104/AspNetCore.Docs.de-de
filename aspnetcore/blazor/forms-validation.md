---
title: Blazor-Formulare und -Validierung in ASP.NET Core
author: guardrex
description: Hier erfahren Sie, wie Formular- und Feldvalidierungsszenarios in Blazor verwendet werden.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 0359a9337860d9b8ce0b81d8833a034a898b05a5
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218959"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="e0ba5-103">Core Blazor-Formulare und -Validierung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e0ba5-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="e0ba5-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e0ba5-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e0ba5-105">Formulare und Validierungen werden in Blazor mithilfe von [Datenanmerkungen](xref:mvc/models/validation) unterstützt.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="e0ba5-106">Der folgende `ExampleModel`-Typ definiert die Validierungslogik mithilfe von Datenanmerkungen:</span><span class="sxs-lookup"><span data-stu-id="e0ba5-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="e0ba5-107">Ein Formular wird mithilfe der `EditForm`-Komponente definiert.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="e0ba5-108">Im folgenden Formular sehen Sie typische Elemente, Komponenten und typischen Razor-Code:</span><span class="sxs-lookup"><span data-stu-id="e0ba5-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

<span data-ttu-id="e0ba5-109">Im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e0ba5-109">In the preceding example:</span></span>

* <span data-ttu-id="e0ba5-110">Das Formular validiert Benutzereingaben im `name`-Feld mithilfe der im `ExampleModel`-Typ definierten Validierung.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="e0ba5-111">Das Modell wird im `@code`-Block der Komponente erstellt und in einem privaten Feld (`_exampleModel`) gespeichert.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-111">The model is created in the component's `@code` block and held in a private field (`_exampleModel`).</span></span> <span data-ttu-id="e0ba5-112">Das Feld wird dem `Model`-Attribut des `<EditForm>`-Elements zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="e0ba5-113">Der `@bind-Value` der `InputText`-Komponente bindet Folgendes:</span><span class="sxs-lookup"><span data-stu-id="e0ba5-113">The `InputText` component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="e0ba5-114">Die Modelleigenschaft (`_exampleModel.Name`) an die `Value`-Eigenschaft der `InputText`-Komponente.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-114">The model property (`_exampleModel.Name`) to the `InputText` component's `Value` property.</span></span>
  * <span data-ttu-id="e0ba5-115">Den Delegat eines Änderungsereignisses an die `ValueChanged`-Eigenschaft der `InputText`-Komponente.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-115">A change event delegate to the `InputText` component's `ValueChanged` property.</span></span>
* <span data-ttu-id="e0ba5-116">Die `DataAnnotationsValidator`-Komponente fügt Validierungsunterstützung mithilfe von Datenanmerkungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-116">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="e0ba5-117">Die `ValidationSummary`-Komponente fasst Validierungsnachrichten zusammen.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-117">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="e0ba5-118">`HandleValidSubmit` wird ausgelöst, wenn das Formular erfolgreich gesendet wird (die Validierung erfolgreich besteht).</span><span class="sxs-lookup"><span data-stu-id="e0ba5-118">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="e0ba5-119">Einige integrierte Eingabekomponenten sind verfügbar, um Benutzereingaben zu empfangen und zu validieren.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-119">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="e0ba5-120">Eingaben werden validiert, wenn sie geändert werden und wenn ein Formular gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-120">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="e0ba5-121">In der folgenden Tabelle finden Sie verfügbare Eingabekomponenten.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-121">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="e0ba5-122">Eingabekomponenten</span><span class="sxs-lookup"><span data-stu-id="e0ba5-122">Input component</span></span> | <span data-ttu-id="e0ba5-123">Wird gerendert als&hellip;</span><span class="sxs-lookup"><span data-stu-id="e0ba5-123">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="e0ba5-124">Alle Eingabekomponenten einschließlich `EditForm` unterstützen arbiträre Attribute.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-124">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="e0ba5-125">Attribute, die nicht mit einem Komponentenparameter übereinstimmen, werden dem gerenderten HTML-Element hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-125">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="e0ba5-126">Eingabekomponenten stellen Standardverhalten für das Validieren bei Bearbeitung zur Verfügung und ändern ihre CSS-Klasse, um den Feldzustand darzustellen.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-126">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="e0ba5-127">Einige Komponenten beinhalten hilfreiche Parsinglogik.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-127">Some components include useful parsing logic.</span></span> <span data-ttu-id="e0ba5-128">`InputDate` und `InputNumber` verarbeiten beispielsweise nicht parsbare Werte ordnungsgemäß.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-128">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="e0ba5-129">Typen, die NULL-Werte akzeptieren, unterstützen auch die NULL-Zulässigkeit des Zielfelds (z. B. `int?`).</span><span class="sxs-lookup"><span data-stu-id="e0ba5-129">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="e0ba5-130">Der folgende `Starship`-Typ definiert eine Validierungslogik mithilfe einer größeren Anzahl Eigenschaften und Datenanmerkungen wie die frühere `ExampleModel`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="e0ba5-130">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="e0ba5-131">Im obigen Beispiel ist `Description` optional, da keine Datenanmerkungen vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-131">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="e0ba5-132">Das folgende Formular validiert Benutzereingaben mithilfe der im `Starship`-Modell definierten Validierung:</span><span class="sxs-lookup"><span data-stu-id="e0ba5-132">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="e0ba5-133">`EditForm` erstellt `EditContext` als [kaskadierenden Wert](xref:blazor/components#cascading-values-and-parameters), der Metadaten zum Bearbeitungsprozess erfasst, einschließlich der Felder, die geändert wurden und den aktuellen Validierungsnachrichten.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-133">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="e0ba5-134">`EditForm` stellt auch für gültige und ungültige Sendevorgänge (`OnValidSubmit`, `OnInvalidSubmit`) geeignete Ereignisse zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-134">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="e0ba5-135">Alternativ können Sie `OnSubmit` verwenden, um die Validierung auszulösen und Feldwerte mit benutzerdefiniertem Validierungscode zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-135">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="e0ba5-136">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e0ba5-136">In the following example:</span></span>

* <span data-ttu-id="e0ba5-137">Die `HandleSubmit`-Methode wird ausgeführt, wenn auf die Schaltfläche **Senden** geklickt wird.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-137">The `HandleSubmit` method runs when the **Submit** button is selected.</span></span>
* <span data-ttu-id="e0ba5-138">Das Formular wird mithilfe der `EditContext`-Klasse des Formulars validiert.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-138">The form is validated using the form's `EditContext`.</span></span>
* <span data-ttu-id="e0ba5-139">Das Formular wird dann weiter validiert, indem `EditContext` an die `ServerValidate`-Methode übergeben wird, die einen Web-API-Endpunkt auf dem Server aufruft (*wird hier nicht gezeigt*).</span><span class="sxs-lookup"><span data-stu-id="e0ba5-139">The form is further validated by passing the `EditContext` to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="e0ba5-140">Je nach Ergebnis der client- und serverseitigen Validierung, indem `isValid` überprüft wird, wird weiterer Code ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-140">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

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

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="e0ba5-141">InputText basierend auf dem Eingabeereignis</span><span class="sxs-lookup"><span data-stu-id="e0ba5-141">InputText based on the input event</span></span>

<span data-ttu-id="e0ba5-142">Verwenden Sie die `InputText`-Komponente, um eine benutzerdefinierte Komponente zu erstellen, die das `input`-Ereignis anstelle des `change`-Ereignisses verwendet.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-142">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="e0ba5-143">Erstellen Sie eine Komponente mit dem folgenden Markup, und verwenden Sie die Komponente genauso, wie `InputText` verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-143">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a><span data-ttu-id="e0ba5-144">Verwenden von Optionsfeldern</span><span class="sxs-lookup"><span data-stu-id="e0ba5-144">Work with radio buttons</span></span>

<span data-ttu-id="e0ba5-145">Beim Verwenden von Optionsfeldern in einem Formular werden Datenbindungen nicht wie andere Elemente verarbeitet, da Optionsfelder als Gruppe ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-145">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="e0ba5-146">Der Wert der einzelnen Optionsfelder ist fest. Der Wert der Optionsfeldgruppe ist jedoch der Wert des ausgewählten Optionsfelds.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-146">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="e0ba5-147">Das folgende Beispiel veranschaulicht die Vorgehensweise:</span><span class="sxs-lookup"><span data-stu-id="e0ba5-147">The following example shows how to:</span></span>

* <span data-ttu-id="e0ba5-148">Verarbeiten von Datenbindungen für eine Optionsfeldgruppe</span><span class="sxs-lookup"><span data-stu-id="e0ba5-148">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="e0ba5-149">Unterstützen der Validierung mithilfe einer benutzerdefinierten `InputRadio`-Komponente</span><span class="sxs-lookup"><span data-stu-id="e0ba5-149">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="e0ba5-150">Die folgende `EditForm`-Klasse verwendet die vorangehende `InputRadio`-Komponente, um eine Bewertung vom Benutzer zu erhalten und sie zu bewerten:</span><span class="sxs-lookup"><span data-stu-id="e0ba5-150">The following `EditForm` uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

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

## <a name="validation-support"></a><span data-ttu-id="e0ba5-151">Validierungsunterstützung</span><span class="sxs-lookup"><span data-stu-id="e0ba5-151">Validation support</span></span>

<span data-ttu-id="e0ba5-152">Die `DataAnnotationsValidator`-Komponente fügt Validierungsunterstützung mithilfe von Datenanmerkungen an die kaskadierte `EditContext`-Klasse an.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-152">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="e0ba5-153">Für das Aktivieren der Validierungsunterstützung mithilfe von Datenanmerkungen ist eine explizite Geste erforderlich.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-153">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="e0ba5-154">Wenn Sie ein anderes Validierungssystem als Datenanmerkungen verwenden möchten, ersetzen Sie `DataAnnotationsValidator` durch eine benutzerdefinierte Implementierung.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-154">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="e0ba5-155">Die ASP.NET Core-Implementierung können Sie sich in der Referenzquelle ansehen: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="e0ba5-155">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="e0ba5-156"> führt zwei Validierungstypen aus:</span><span class="sxs-lookup"><span data-stu-id="e0ba5-156"> performs two types of validation:</span></span>

* <span data-ttu-id="e0ba5-157">Die *Feldvalidierung* wird ausgeführt, wenn der Benutzer auf eine Stelle außerhalb des Felds tippt.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-157">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="e0ba5-158">Während der Feldvalidierung ordnet die `DataAnnotationsValidator`-Komponente alle gemeldeten Validierungsergebnisse dem Feld zu.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-158">During field validation, the `DataAnnotationsValidator` component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="e0ba5-159">*Modellvalidierung* wird ausgeführt, wenn der Benutzer ein Formular sendet.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-159">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="e0ba5-160">Während der Modellvalidierung versucht die `DataAnnotationsValidator`-Komponente, das Feld basierend auf dem Namen des Members zu ermitteln, das vom Validierungsergebnis gemeldet wird.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-160">During model validation, the `DataAnnotationsValidator` component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="e0ba5-161">Validierungsergebnisse, die keinem einzelnen Member zugeordnet werden, werden dem Modell und keinem Feld zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-161">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="e0ba5-162">Komponenten der Validierungszusammenfassung und der Validierungsnachricht</span><span class="sxs-lookup"><span data-stu-id="e0ba5-162">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="e0ba5-163">Die `ValidationSummary`-Komponente fasst alle Validierungsnachrichten zusammen. Das ähnelt dem [Taghilfsprogramm für Validierungszusammenfassungen](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="e0ba5-163">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="e0ba5-164">Validierungsmeldungen für ein bestimmtes Modell werden mit dem `Model`-Parameter ausgegeben:</span><span class="sxs-lookup"><span data-stu-id="e0ba5-164">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@_starship" />
```

<span data-ttu-id="e0ba5-165">Die `ValidationMessage`-Komponente zeigt Validierungsnachrichten für ein bestimmtes Feld an. Das ähnelt dem [Taghilfsprogramm für Validierungsmeldungen](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="e0ba5-165">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="e0ba5-166">Das Validierungsfeld wird mit dem `For`-Attribut und einem Lambdaausdruck angegeben, der die Modelleigenschaft benennt:</span><span class="sxs-lookup"><span data-stu-id="e0ba5-166">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

<span data-ttu-id="e0ba5-167">Die `ValidationMessage`- und `ValidationSummary`-Komponenten unterstützen arbiträre Attribute.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-167">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="e0ba5-168">Attribute, die nicht mit einem Komponentenparameter übereinstimmen, werden dem gerenderten `<div>`- oder `<ul>`-Element hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-168">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="e0ba5-169">Benutzerdefinierte Validierungsattribute</span><span class="sxs-lookup"><span data-stu-id="e0ba5-169">Custom validation attributes</span></span>

<span data-ttu-id="e0ba5-170">Übergeben Sie die <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName>-Eigenschaft des Validierungskontexts bei der Erstellung der <xref:System.ComponentModel.DataAnnotations.ValidationResult>-Klasse, um sicherzustellen, dass ein Validierungsergebnis korrekt einem Feld zugeordnet wird, wenn ein [benutzerdefiniertes Validierungsattribut](xref:mvc/models/validation#custom-attributes) verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="e0ba5-170">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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

### <a name="opno-locblazor-data-annotations-validation-package"></a><span data-ttu-id="e0ba5-171">Validierungspakete für Datenanmerkungen in Blazor</span><span class="sxs-lookup"><span data-stu-id="e0ba5-171">Blazor data annotations validation package</span></span>

<span data-ttu-id="e0ba5-172">Bei [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) handelt es sich um ein Paket, das Lücken bei der Validierung mithilfe der `DataAnnotationsValidator`-Komponente beheben kann.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-172">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="e0ba5-173">Das Paket ist aktuell *experimentell*.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-173">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="e0ba5-174">[CompareProperty]-Attribut</span><span class="sxs-lookup"><span data-stu-id="e0ba5-174">[CompareProperty] attribute</span></span>

<span data-ttu-id="e0ba5-175"><xref:System.ComponentModel.DataAnnotations.CompareAttribute> funktioniert nicht gut mit der `DataAnnotationsValidator`-Komponente, da hier das Validierungsergebnis nicht einem bestimmten Member zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-175">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the `DataAnnotationsValidator` component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="e0ba5-176">Das führt zu einem nicht konsistenten Verhalten zwischen Validierung auf Feldebene und der Validierung des gesamten Modells bei Sendevorgängen.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-176">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="e0ba5-177">Das *experimentelle* Paket [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)-Paket führt ein zusätzliches Validierungsattribut ein (`ComparePropertyAttribute`), das diese Begrenzungen umgehen kann.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-177">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="e0ba5-178">In einer Blazor-App handelt es sich bei `[CompareProperty]` um eine direkte Ersetzung des `[Compare]`-Attributs.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-178">In a Blazor app, `[CompareProperty]` is a direct replacement for the `[Compare]` attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="e0ba5-179">Verschachtelte Modelle, Sammlungstypen und komplexe Typen</span><span class="sxs-lookup"><span data-stu-id="e0ba5-179">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="e0ba5-180"> bietet Unterstützung für die Validierung von Formulareingaben mithilfe von Datenanmerkungen mit dem integrierten `DataAnnotationsValidator`.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-180"> provides support for validating form input using data annotations with the built-in `DataAnnotationsValidator`.</span></span> <span data-ttu-id="e0ba5-181">`DataAnnotationsValidator` validiert jedoch nur Eigenschaften des Modells auf oberster Ebene, die an das Formular gebunden sind, bei denen es sich aber um keine Sammlungs- oder komplexen Eigenschaften handelt.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-181">However, the `DataAnnotationsValidator` only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="e0ba5-182">Verwenden Sie den vom *experimentellen* Paket [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) bereitgestellten `ObjectGraphDataAnnotationsValidator`, um den gesamten Objektgraph des gebundenen Modells zu validieren, einschließlich Sammlungseigenschaften und komplexen Eigenschaften:</span><span class="sxs-lookup"><span data-stu-id="e0ba5-182">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="e0ba5-183">Fügen Sie Modelleigenschaften Anmerkungen mit `[ValidateComplexType]` hinzu.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-183">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="e0ba5-184">In den folgenden Modellklassen enthält die `ShipDescription`-Klasse zusätzliche Datenanmerkungen für die Validierung, wenn das Modell an das Formular gebunden ist:</span><span class="sxs-lookup"><span data-stu-id="e0ba5-184">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="e0ba5-185">*Starship.cs:*</span><span class="sxs-lookup"><span data-stu-id="e0ba5-185">*Starship.cs*:</span></span>

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

<span data-ttu-id="e0ba5-186">*ShipDescription.cs:*</span><span class="sxs-lookup"><span data-stu-id="e0ba5-186">*ShipDescription.cs*:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="e0ba5-187">Aktivieren der Schaltfläche zum Senden basierend auf der Formularvalidierung</span><span class="sxs-lookup"><span data-stu-id="e0ba5-187">Enable the submit button based on form validation</span></span>

<span data-ttu-id="e0ba5-188">So aktivieren oder deaktivieren Sie die Schaltfläche zum Senden basierend auf der Formularvalidierung:</span><span class="sxs-lookup"><span data-stu-id="e0ba5-188">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="e0ba5-189">Verwenden Sie die `EditContext`-Klasse des Formulars, um das Modell zuzuweisen, wenn die Komponente initialisiert wird.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-189">Use the form's `EditContext` to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="e0ba5-190">Validieren Sie das Formular im `OnFieldChanged`-Rückruf des Kontexts, um die Schaltfläche zum Senden zu aktivieren und zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-190">Validate the form in the context's `OnFieldChanged` callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="e0ba5-191">Heben Sie die Einbindung des Ereignishandlers in der `Dispose`-Methode auf.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-191">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="e0ba5-192">Weitere Informationen finden Sie unter <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-192">For more information, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

```razor
@implements IDisposable

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
        _editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        _formInvalid = !_editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        _editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

<span data-ttu-id="e0ba5-193">Im vorherigen Beispiel ist `_formInvalid` in folgenden Fällen auf `false` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="e0ba5-193">In the preceding example, set `_formInvalid` to `false` if:</span></span>

* <span data-ttu-id="e0ba5-194">Das Formular ist bereits mit gültigen Standardwerten ausgefüllt.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-194">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="e0ba5-195">Sie möchten die Schaltfläche zum Senden aktivieren, wenn das Formular geladen wird.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-195">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="e0ba5-196">Eine Nebenwirkung des vorangehenden Ansatzes ist, dass eine `ValidationSummary`-Komponente mit ungültigen Feldern aufgefüllt wird, nachdem der Benutzer mit einem beliebigen Feld interagiert hat.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-196">A side effect of the preceding approach is that a `ValidationSummary` component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="e0ba5-197">Dieses Szenario lässt sich folgendermaßen auflösen:</span><span class="sxs-lookup"><span data-stu-id="e0ba5-197">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="e0ba5-198">Verwenden Sie keine `ValidationSummary`-Komponente des Formulars.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-198">Don't use a `ValidationSummary` component on the form.</span></span>
* <span data-ttu-id="e0ba5-199">Lassen Sie die `ValidationSummary`-Komponente einblenden, wenn auf die Schaltfläche zum Senden geklickt wird, z. B. in einer `HandleValidSubmit`-Methode.</span><span class="sxs-lookup"><span data-stu-id="e0ba5-199">Make the `ValidationSummary` component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

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

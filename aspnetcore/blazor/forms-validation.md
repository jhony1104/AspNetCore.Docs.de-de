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
ms.openlocfilehash: 2758bcbbc76c8a59716fe224dd2deb4ca8c06929
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76726881"
---
# <a name="aspnet-core-opno-locblazor-forms-and-validation"></a><span data-ttu-id="9b9ac-103">ASP.net Core Blazor Formularen und Validierung</span><span class="sxs-lookup"><span data-stu-id="9b9ac-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="9b9ac-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9b9ac-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9b9ac-105">Formulare und Validierung werden in Blazor mithilfe von [Daten Anmerkungen](xref:mvc/models/validation)unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="9b9ac-106">Der folgende `ExampleModel` Typ definiert die Validierungs Logik mithilfe von Daten Anmerkungen:</span><span class="sxs-lookup"><span data-stu-id="9b9ac-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="9b9ac-107">Ein Formular wird mithilfe der `EditForm` Komponente definiert.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="9b9ac-108">In der folgenden Form werden typische Elemente, Komponenten und Razor-Code veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="9b9ac-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

<span data-ttu-id="9b9ac-109">Im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9b9ac-109">In the preceding example:</span></span>

* <span data-ttu-id="9b9ac-110">Das Formular überprüft Benutzereingaben im `name` Feld mithilfe der im `ExampleModel`-Typ definierten Validierung.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="9b9ac-111">Das Modell wird im `@code` Block der Komponente erstellt und in einem privaten Feld (`_exampleModel`) gespeichert.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-111">The model is created in the component's `@code` block and held in a private field (`_exampleModel`).</span></span> <span data-ttu-id="9b9ac-112">Das-Feld wird dem `Model`-Attribut des `<EditForm>`-Elements zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="9b9ac-113">Die `@bind-Value` der `InputText` Komponente gebunden:</span><span class="sxs-lookup"><span data-stu-id="9b9ac-113">The `InputText` component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="9b9ac-114">Die Model-Eigenschaft (`_exampleModel.Name`) zur `Value`-Eigenschaft der `InputText` Komponente.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-114">The model property (`_exampleModel.Name`) to the `InputText` component's `Value` property.</span></span>
  * <span data-ttu-id="9b9ac-115">Ein Änderungs Ereignis Delegat für die `ValueChanged`-Eigenschaft der `InputText` Komponente.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-115">A change event delegate to the `InputText` component's `ValueChanged` property.</span></span>
* <span data-ttu-id="9b9ac-116">Die `DataAnnotationsValidator` Komponente fügt die Validierungs Unterstützung mithilfe von Daten Anmerkungen an.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-116">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="9b9ac-117">Die `ValidationSummary` Komponente fasst Validierungs Nachrichten zusammen.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-117">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="9b9ac-118">`HandleValidSubmit` wird ausgelöst, wenn das Formular erfolgreich übermittelt wird (Überprüfung wird bestanden).</span><span class="sxs-lookup"><span data-stu-id="9b9ac-118">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="9b9ac-119">Eine Reihe integrierter Eingabe Komponenten ist verfügbar, um Benutzereingaben zu empfangen und zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-119">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="9b9ac-120">Eingaben werden überprüft, wenn Sie geändert werden und wenn ein Formular übermittelt wird.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-120">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="9b9ac-121">In der folgenden Tabelle sind die verfügbaren Eingabe Komponenten aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-121">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="9b9ac-122">Eingabe Komponente</span><span class="sxs-lookup"><span data-stu-id="9b9ac-122">Input component</span></span> | <span data-ttu-id="9b9ac-123">Als&hellip; gerendert</span><span class="sxs-lookup"><span data-stu-id="9b9ac-123">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="9b9ac-124">Alle Eingabe Komponenten, einschließlich `EditForm`, unterstützen beliebige Attribute.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-124">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="9b9ac-125">Dem gerenderten HTML-Element wird ein beliebiges Attribut hinzugefügt, das keinem Komponenten Parameter entspricht.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-125">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="9b9ac-126">Eingabe Komponenten stellen das Standardverhalten für die Validierung bei der Bearbeitung und das Ändern der CSS-Klasse bereit, um den feldzustand widerzuspiegeln.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-126">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="9b9ac-127">Einige Komponenten enthalten nützliche Logik für die Verarbeitung.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-127">Some components include useful parsing logic.</span></span> <span data-ttu-id="9b9ac-128">Beispielsweise können `InputDate` und `InputNumber` nicht Parier Bare Werte ordnungsgemäß behandeln, indem Sie Sie als Validierungs Fehler registrieren.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-128">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="9b9ac-129">Typen, die NULL-Werte akzeptieren können, unterstützen auch die NULL-Zulässigkeit des Zielfelds (z. b. `int?`).</span><span class="sxs-lookup"><span data-stu-id="9b9ac-129">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="9b9ac-130">Der folgende `Starship` Typ definiert die Validierungs Logik mit einem größeren Satz von Eigenschaften und Daten Anmerkungen als die früheren `ExampleModel`:</span><span class="sxs-lookup"><span data-stu-id="9b9ac-130">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="9b9ac-131">Im vorherigen Beispiel ist `Description` optional, da keine Daten Anmerkungen vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-131">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="9b9ac-132">Im folgenden Formular werden Benutzereingaben mithilfe der im `Starship` Modell definierten Validierung überprüft:</span><span class="sxs-lookup"><span data-stu-id="9b9ac-132">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="9b9ac-133">Der `EditForm` erstellt eine `EditContext` als einen [kaskadierenden Wert](xref:blazor/components#cascading-values-and-parameters) , der Metadaten zum Bearbeitungsprozess nachverfolgt, einschließlich der geänderten Felder und der aktuellen Validierungs Meldungen.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-133">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="9b9ac-134">Der `EditForm` bietet auch praktische Ereignisse für gültige und ungültige übermitteln (`OnValidSubmit`, `OnInvalidSubmit`).</span><span class="sxs-lookup"><span data-stu-id="9b9ac-134">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="9b9ac-135">Alternativ können Sie `OnSubmit` verwenden, um die Validierungs-und Prüf Feldwerte mit benutzerdefiniertem Validierungscode zu initiieren.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-135">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="9b9ac-136">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9b9ac-136">In the following example:</span></span>

* <span data-ttu-id="9b9ac-137">Die `HandleSubmit`-Methode wird ausgeführt, wenn die Schaltfläche **senden** ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-137">The `HandleSubmit` method runs when the **Submit** button is selected.</span></span>
* <span data-ttu-id="9b9ac-138">Das Formular wird mit dem `EditContext`des Formulars überprüft.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-138">The form is validated using the form's `EditContext`.</span></span>
* <span data-ttu-id="9b9ac-139">Das Formular wird weiter überprüft, indem der `EditContext` an die `ServerValidate` Methode übergeben wird, die einen Web-API-Endpunkt auf dem Server aufruft (*nicht angezeigt*).</span><span class="sxs-lookup"><span data-stu-id="9b9ac-139">The form is further validated by passing the `EditContext` to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="9b9ac-140">Zusätzlicher Code wird ausgeführt, abhängig vom Ergebnis der Client-und serverseitigen Validierung durch Überprüfung `isValid`.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-140">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

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

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="9b9ac-141">InputText basierend auf dem Eingabe Ereignis</span><span class="sxs-lookup"><span data-stu-id="9b9ac-141">InputText based on the input event</span></span>

<span data-ttu-id="9b9ac-142">Verwenden Sie die `InputText` Komponente, um eine benutzerdefinierte Komponente zu erstellen, die das Ereignis `input` anstelle des `change` Ereignisses verwendet.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-142">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="9b9ac-143">Erstellen Sie eine-Komponente mit dem folgenden Markup, und verwenden Sie die-Komponente, ebenso wie `InputText` verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="9b9ac-143">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a><span data-ttu-id="9b9ac-144">Arbeiten mit Options Feldern</span><span class="sxs-lookup"><span data-stu-id="9b9ac-144">Work with radio buttons</span></span>

<span data-ttu-id="9b9ac-145">Beim Arbeiten mit Options Feldern in einem Formular wird die Datenbindung anders behandelt als andere Elemente, da Options Felder als Gruppe ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-145">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="9b9ac-146">Der Wert jeder Options Schaltfläche ist korrigiert, aber der Wert der Optionsfeld Gruppe ist der Wert des ausgewählten Options Felds.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-146">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="9b9ac-147">Das folgende Beispiel zeigt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="9b9ac-147">The following example shows how to:</span></span>

* <span data-ttu-id="9b9ac-148">Verarbeiten der Datenbindung für eine Optionsfeld Gruppe.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-148">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="9b9ac-149">Unterstützen Sie die Validierung mithilfe einer benutzerdefinierten `InputRadio` Komponente.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-149">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="9b9ac-150">In der folgenden `EditForm` wird die vorangehende `InputRadio` Komponente verwendet, um eine Bewertung vom Benutzer abzurufen und zu überprüfen:</span><span class="sxs-lookup"><span data-stu-id="9b9ac-150">The following `EditForm` uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

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

## <a name="validation-support"></a><span data-ttu-id="9b9ac-151">Validierungs Unterstützung</span><span class="sxs-lookup"><span data-stu-id="9b9ac-151">Validation support</span></span>

<span data-ttu-id="9b9ac-152">Die `DataAnnotationsValidator` Komponente fügt die Validierungs Unterstützung mithilfe von Daten Anmerkungen an den kaskadierenden `EditContext`an.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-152">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="9b9ac-153">Das Aktivieren der Unterstützung für die Validierung mithilfe von Daten Anmerkungen erfordert diese explizite Geste.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-153">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="9b9ac-154">Um ein anderes Validierungssystem als Daten Anmerkungen zu verwenden, ersetzen Sie den `DataAnnotationsValidator` durch eine benutzerdefinierte Implementierung.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-154">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="9b9ac-155">Die ASP.net Core-Implementierung ist für die Untersuchung in der Verweis Quelle verfügbar: [dataannotationsvalidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[adddataannotationsvalidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="9b9ac-155">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="9b9ac-156"> führt zwei Validierungs Typen aus:</span><span class="sxs-lookup"><span data-stu-id="9b9ac-156"> performs two types of validation:</span></span>

* <span data-ttu-id="9b9ac-157">Die *Feld Validierung* wird ausgeführt, wenn der Benutzer ein Feld ausstellt.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-157">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="9b9ac-158">Während der Feld Validierung ordnet die `DataAnnotationsValidator` Komponente alle gemeldeten Überprüfungs Ergebnisse dem Feld zu.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-158">During field validation, the `DataAnnotationsValidator` component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="9b9ac-159">Die *Modell Validierung* wird ausgeführt, wenn der Benutzer das Formular sendet.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-159">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="9b9ac-160">Während der Modell Validierung versucht die `DataAnnotationsValidator` Komponente, das Feld basierend auf dem Elementnamen zu ermitteln, den das Validierungs Ergebnis meldet.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-160">During model validation, the `DataAnnotationsValidator` component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="9b9ac-161">Validierungs Ergebnisse, die keinem einzelnen Element zugeordnet sind, sind dem Modell und nicht einem Feld zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-161">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="9b9ac-162">Validierungs Zusammenfassungs-und Validierungs Nachrichten Komponenten</span><span class="sxs-lookup"><span data-stu-id="9b9ac-162">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="9b9ac-163">Die `ValidationSummary` Komponente fasst alle Validierungs Nachrichten zusammen, die mit dem taghilfsprogramm für die [Validierungs Zusammenfassung](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)vergleichbar sind:</span><span class="sxs-lookup"><span data-stu-id="9b9ac-163">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="9b9ac-164">Gibt Validierungs Nachrichten für ein bestimmtes Modell mit dem `Model`-Parameter aus:</span><span class="sxs-lookup"><span data-stu-id="9b9ac-164">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@_starship" />
```

<span data-ttu-id="9b9ac-165">Die `ValidationMessage` Komponente zeigt Validierungs Meldungen für ein bestimmtes Feld an, das dem [Validierungs Nachrichten-taghilfsprogramm](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)ähnelt.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-165">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="9b9ac-166">Geben Sie das Feld für die Überprüfung mit dem `For`-Attribut und einem Lambda-Ausdruck an, der die Modell Eigenschaft benennt:</span><span class="sxs-lookup"><span data-stu-id="9b9ac-166">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

<span data-ttu-id="9b9ac-167">Die Komponenten `ValidationMessage` und `ValidationSummary` unterstützen beliebige Attribute.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-167">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="9b9ac-168">Ein beliebiges Attribut, das nicht mit einem Komponenten Parameter identisch ist, wird dem generierten `<div>` oder `<ul>` Element hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-168">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="9b9ac-169">Benutzerdefinierte Validierungs Attribute</span><span class="sxs-lookup"><span data-stu-id="9b9ac-169">Custom validation attributes</span></span>

<span data-ttu-id="9b9ac-170">Um sicherzustellen, dass ein Validierungs Ergebnis ordnungsgemäß mit einem Feld verknüpft ist, wenn Sie ein [benutzerdefiniertes Validierungs Attribut](xref:mvc/models/validation#custom-attributes)verwenden, übergeben Sie den <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> des Validierungs Kontexts beim Erstellen des <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span><span class="sxs-lookup"><span data-stu-id="9b9ac-170">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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

### <a name="opno-locblazor-data-annotations-validation-package"></a><span data-ttu-id="9b9ac-171">Validierungs Paket für Blazor Daten Anmerkungen</span><span class="sxs-lookup"><span data-stu-id="9b9ac-171">Blazor data annotations validation package</span></span>

<span data-ttu-id="9b9ac-172">[Microsoft. aspnetcore.Blazor. DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) ist ein Paket, das Validierungs Erfahrungs Lücken mithilfe der `DataAnnotationsValidator` Komponente füllt.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-172">The [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) is a package that fills validation experience gaps using the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="9b9ac-173">Das Paket ist zurzeit *experimentell*.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-173">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="9b9ac-174">[CompareProperty]-Attribut</span><span class="sxs-lookup"><span data-stu-id="9b9ac-174">[CompareProperty] attribute</span></span>

<span data-ttu-id="9b9ac-175">Der <xref:System.ComponentModel.DataAnnotations.CompareAttribute> funktioniert nicht gut mit der `DataAnnotationsValidator` Komponente, da er das Validierungs Ergebnis nicht einem bestimmten Element zuordnet.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-175">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the `DataAnnotationsValidator` component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="9b9ac-176">Dies kann zu inkonsistentem Verhalten zwischen der Überprüfung auf Feldebene und dem Zeitpunkt der Überprüfung des gesamten Modells auf einem Submit</span><span class="sxs-lookup"><span data-stu-id="9b9ac-176">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="9b9ac-177">[Microsoft. aspnetcore.Blazor. Mit dem experimentellen DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) -Paket wird ein zusätzliches Validierungs Attribut (`ComparePropertyAttribute`) eingeführt, das diese Einschränkungen behandelt.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-177">The [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="9b9ac-178">In einer Blazor-APP ist `[CompareProperty]` ein direkter Ersatz für das `[Compare]`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-178">In a Blazor app, `[CompareProperty]` is a direct replacement for the `[Compare]` attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="9b9ac-179">Untergeordnete Modelle, Sammlungs Typen und komplexe Typen</span><span class="sxs-lookup"><span data-stu-id="9b9ac-179">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="9b9ac-180"> bietet Unterstützung für das Validieren von Formular Eingaben mithilfe von Daten Anmerkungen mit dem integrierten `DataAnnotationsValidator`.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-180"> provides support for validating form input using data annotations with the built-in `DataAnnotationsValidator`.</span></span> <span data-ttu-id="9b9ac-181">Allerdings überprüft das `DataAnnotationsValidator` nur die Eigenschaften der obersten Ebene des Modells, die an das Formular gebunden sind, das keine Eigenschaften vom Typ "Collection" oder "Complex" ist.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-181">However, the `DataAnnotationsValidator` only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="9b9ac-182">Verwenden Sie die `ObjectGraphDataAnnotationsValidator` der *experimentellen* [Microsoft. aspnetcore.Blazor, um das gesamte Objekt Diagramm des gebundenen Modells zu validieren, einschließlich Eigenschaften vom Typ "Collection" und "Complex". DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) -Paket:</span><span class="sxs-lookup"><span data-stu-id="9b9ac-182">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="9b9ac-183">Kommentieren Sie Modell Eigenschaften mit `[ValidateComplexType]`.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-183">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="9b9ac-184">In den folgenden Modellklassen enthält die `ShipDescription`-Klasse zusätzliche Daten Anmerkungen, die überprüft werden können, wenn das Modell an das Formular gebunden ist:</span><span class="sxs-lookup"><span data-stu-id="9b9ac-184">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="9b9ac-185">*Starship.cs*:</span><span class="sxs-lookup"><span data-stu-id="9b9ac-185">*Starship.cs*:</span></span>

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

<span data-ttu-id="9b9ac-186">*ShipDescription.cs*:</span><span class="sxs-lookup"><span data-stu-id="9b9ac-186">*ShipDescription.cs*:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="9b9ac-187">Aktivieren der Schaltfläche "Senden" basierend auf der Formular Validierung</span><span class="sxs-lookup"><span data-stu-id="9b9ac-187">Enable the submit button based on form validation</span></span>

<span data-ttu-id="9b9ac-188">So aktivieren und deaktivieren Sie die Schaltfläche "Senden" basierend auf der Formular Validierung:</span><span class="sxs-lookup"><span data-stu-id="9b9ac-188">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="9b9ac-189">Verwenden Sie das `EditContext` des Formulars, um das Modell zuzuweisen, wenn die Komponente initialisiert wird.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-189">Use the form's `EditContext` to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="9b9ac-190">Überprüfen Sie das Formular im `OnFieldChanged` Rückruf des Kontexts, um die Schaltfläche Senden zu aktivieren und zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-190">Validate the form in the context's `OnFieldChanged` callback to enable and disable the submit button.</span></span>

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

<span data-ttu-id="9b9ac-191">Legen Sie im vorhergehenden Beispiel `_formInvalid` auf `false` if:</span><span class="sxs-lookup"><span data-stu-id="9b9ac-191">In the preceding example, set `_formInvalid` to `false` if:</span></span>

* <span data-ttu-id="9b9ac-192">Das Formular wird mit gültigen Standardwerten vorab geladen.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-192">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="9b9ac-193">Wenn das Formular geladen wird, soll die Schaltfläche "Senden" aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-193">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="9b9ac-194">Ein Nebeneffekt des vorangehenden Ansatzes besteht darin, dass eine `ValidationSummary` Komponente mit ungültigen Feldern aufgefüllt wird, nachdem der Benutzer mit einem Feld interagiert hat.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-194">A side effect of the preceding approach is that a `ValidationSummary` component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="9b9ac-195">Dieses Szenario kann auf eine der folgenden Arten adressiert werden:</span><span class="sxs-lookup"><span data-stu-id="9b9ac-195">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="9b9ac-196">Verwenden Sie keine `ValidationSummary` Komponente auf dem Formular.</span><span class="sxs-lookup"><span data-stu-id="9b9ac-196">Don't use a `ValidationSummary` component on the form.</span></span>
* <span data-ttu-id="9b9ac-197">Legen Sie die `ValidationSummary` Komponente sichtbar, wenn die Schaltfläche "Senden" ausgewählt ist (z. b. in einer `HandleValidSubmit`-Methode).</span><span class="sxs-lookup"><span data-stu-id="9b9ac-197">Make the `ValidationSummary` component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

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

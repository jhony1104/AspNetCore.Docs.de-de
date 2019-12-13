---
title: ASP.net Core Blazor Formularen und Validierung
author: guardrex
description: Erfahren Sie, wie Sie Formular-und Feld Validierungs Szenarien in Blazorverwenden.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
uid: blazor/forms-validation
ms.openlocfilehash: f4c1845ee4b6ff9274b7117167367ccdd9f36c12
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74943692"
---
# <a name="aspnet-core-opno-locblazor-forms-and-validation"></a><span data-ttu-id="5b2e9-103">ASP.net Core Blazor Formularen und Validierung</span><span class="sxs-lookup"><span data-stu-id="5b2e9-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="5b2e9-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5b2e9-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5b2e9-105">Formulare und Validierung werden in Blazor mithilfe von [Daten Anmerkungen](xref:mvc/models/validation)unterstützt.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="5b2e9-106">Der folgende `ExampleModel` Typ definiert die Validierungs Logik mithilfe von Daten Anmerkungen:</span><span class="sxs-lookup"><span data-stu-id="5b2e9-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="5b2e9-107">Ein Formular wird mithilfe der `EditForm` Komponente definiert.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="5b2e9-108">In der folgenden Form werden typische Elemente, Komponenten und Razor-Code veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="5b2e9-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

```csharp
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
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

* <span data-ttu-id="5b2e9-109">Das Formular überprüft Benutzereingaben im `name` Feld mithilfe der im `ExampleModel`-Typ definierten Validierung.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-109">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="5b2e9-110">Das Modell wird im `@code` Block der Komponente erstellt und in einem privaten Feld (`exampleModel`) gespeichert.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-110">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="5b2e9-111">Das-Feld wird dem `Model`-Attribut des `<EditForm>`-Elements zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-111">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="5b2e9-112">Die `DataAnnotationsValidator` Komponente fügt die Validierungs Unterstützung mithilfe von Daten Anmerkungen an.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-112">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="5b2e9-113">Die `ValidationSummary` Komponente fasst Validierungs Nachrichten zusammen.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-113">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="5b2e9-114">`HandleValidSubmit` wird ausgelöst, wenn das Formular erfolgreich übermittelt wird (Überprüfung wird bestanden).</span><span class="sxs-lookup"><span data-stu-id="5b2e9-114">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="5b2e9-115">Eine Reihe integrierter Eingabe Komponenten ist verfügbar, um Benutzereingaben zu empfangen und zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-115">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="5b2e9-116">Eingaben werden überprüft, wenn Sie geändert werden und wenn ein Formular übermittelt wird.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-116">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="5b2e9-117">In der folgenden Tabelle sind die verfügbaren Eingabe Komponenten aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-117">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="5b2e9-118">Eingabe Komponente</span><span class="sxs-lookup"><span data-stu-id="5b2e9-118">Input component</span></span> | <span data-ttu-id="5b2e9-119">Als&hellip; gerendert</span><span class="sxs-lookup"><span data-stu-id="5b2e9-119">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="5b2e9-120">Alle Eingabe Komponenten, einschließlich `EditForm`, unterstützen beliebige Attribute.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-120">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="5b2e9-121">Dem gerenderten HTML-Element wird ein beliebiges Attribut hinzugefügt, das keinem Komponenten Parameter entspricht.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-121">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="5b2e9-122">Eingabe Komponenten stellen das Standardverhalten für die Validierung bei der Bearbeitung und das Ändern der CSS-Klasse bereit, um den feldzustand widerzuspiegeln.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-122">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="5b2e9-123">Einige Komponenten enthalten nützliche Logik für die Verarbeitung.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-123">Some components include useful parsing logic.</span></span> <span data-ttu-id="5b2e9-124">Beispielsweise können `InputDate` und `InputNumber` nicht Parier Bare Werte ordnungsgemäß behandeln, indem Sie Sie als Validierungs Fehler registrieren.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-124">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="5b2e9-125">Typen, die NULL-Werte akzeptieren können, unterstützen auch die NULL-Zulässigkeit des Zielfelds (z. b. `int?`).</span><span class="sxs-lookup"><span data-stu-id="5b2e9-125">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="5b2e9-126">Der folgende `Starship` Typ definiert die Validierungs Logik mit einem größeren Satz von Eigenschaften und Daten Anmerkungen als die früheren `ExampleModel`:</span><span class="sxs-lookup"><span data-stu-id="5b2e9-126">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="5b2e9-127">Im vorherigen Beispiel ist `Description` optional, da keine Daten Anmerkungen vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-127">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="5b2e9-128">Im folgenden Formular werden Benutzereingaben mithilfe der im `Starship` Modell definierten Validierung überprüft:</span><span class="sxs-lookup"><span data-stu-id="5b2e9-128">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label for="identifier">Identifier: </label>
        <InputText id="identifier" @bind-Value="starship.Identifier" />
    </p>
    <p>
        <label for="description">Description (optional): </label>
        <InputTextArea id="description" @bind-Value="starship.Description" />
    </p>
    <p>
        <label for="classification">Primary Classification: </label>
        <InputSelect id="classification" @bind-Value="starship.Classification">
            <option value="">Select classification ...</option>
            <option value="Exploration">Exploration</option>
            <option value="Diplomacy">Diplomacy</option>
            <option value="Defense">Defense</option>
        </InputSelect>
    </p>
    <p>
        <label for="accommodation">Maximum Accommodation: </label>
        <InputNumber id="accommodation" 
            @bind-Value="starship.MaximumAccommodation" />
    </p>
    <p>
        <label for="valid">Engineering Approval: </label>
        <InputCheckbox id="valid" @bind-Value="starship.IsValidatedDesign" />
    </p>
    <p>
        <label for="productionDate">Production Date: </label>
        <InputDate id="productionDate" @bind-Value="starship.ProductionDate" />
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

<span data-ttu-id="5b2e9-129">Der `EditForm` erstellt eine `EditContext` als einen [kaskadierenden Wert](xref:blazor/components#cascading-values-and-parameters) , der Metadaten zum Bearbeitungsprozess nachverfolgt, einschließlich der geänderten Felder und der aktuellen Validierungs Meldungen.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-129">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="5b2e9-130">Der `EditForm` bietet auch praktische Ereignisse für gültige und ungültige übermitteln (`OnValidSubmit`, `OnInvalidSubmit`).</span><span class="sxs-lookup"><span data-stu-id="5b2e9-130">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="5b2e9-131">Alternativ können Sie `OnSubmit` verwenden, um die Validierungs-und Prüf Feldwerte mit benutzerdefiniertem Validierungscode zu initiieren.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-131">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="5b2e9-132">InputText basierend auf dem Eingabe Ereignis</span><span class="sxs-lookup"><span data-stu-id="5b2e9-132">InputText based on the input event</span></span>

<span data-ttu-id="5b2e9-133">Verwenden Sie die `InputText` Komponente, um eine benutzerdefinierte Komponente zu erstellen, die das Ereignis `input` anstelle des `change` Ereignisses verwendet.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-133">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="5b2e9-134">Erstellen Sie eine-Komponente mit dem folgenden Markup, und verwenden Sie die-Komponente, ebenso wie `InputText` verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="5b2e9-134">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="validation-support"></a><span data-ttu-id="5b2e9-135">Validierungs Unterstützung</span><span class="sxs-lookup"><span data-stu-id="5b2e9-135">Validation support</span></span>

<span data-ttu-id="5b2e9-136">Die `DataAnnotationsValidator` Komponente fügt die Validierungs Unterstützung mithilfe von Daten Anmerkungen an den kaskadierenden `EditContext`an.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-136">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="5b2e9-137">Das Aktivieren der Unterstützung für die Validierung mithilfe von Daten Anmerkungen erfordert diese explizite Geste.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-137">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="5b2e9-138">Um ein anderes Validierungssystem als Daten Anmerkungen zu verwenden, ersetzen Sie den `DataAnnotationsValidator` durch eine benutzerdefinierte Implementierung.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-138">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="5b2e9-139">Die ASP.net Core-Implementierung ist zur Überprüfung in der Verweis Quelle verfügbar: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="5b2e9-139">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="5b2e9-140"> führt zwei Validierungs Typen aus:</span><span class="sxs-lookup"><span data-stu-id="5b2e9-140"> performs two types of validation:</span></span>

* <span data-ttu-id="5b2e9-141">Die *Feld Validierung* wird ausgeführt, wenn der Benutzer ein Feld ausstellt.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-141">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="5b2e9-142">Während der Feld Validierung ordnet die `DataAnnotationsValidator` Komponente alle gemeldeten Überprüfungs Ergebnisse dem Feld zu.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-142">During field validation, the `DataAnnotationsValidator` component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="5b2e9-143">Die *Modell Validierung* wird ausgeführt, wenn der Benutzer das Formular sendet.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-143">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="5b2e9-144">Während der Modell Validierung versucht die `DataAnnotationsValidator` Komponente, das Feld basierend auf dem Elementnamen zu ermitteln, den das Validierungs Ergebnis meldet.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-144">During model validation, the `DataAnnotationsValidator` component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="5b2e9-145">Validierungs Ergebnisse, die keinem einzelnen Element zugeordnet sind, sind dem Modell und nicht einem Feld zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-145">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="5b2e9-146">Validierungs Zusammenfassungs-und Validierungs Nachrichten Komponenten</span><span class="sxs-lookup"><span data-stu-id="5b2e9-146">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="5b2e9-147">Die `ValidationSummary` Komponente fasst alle Validierungs Nachrichten zusammen, die mit dem taghilfsprogramm für die [Validierungs Zusammenfassung](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)vergleichbar sind:</span><span class="sxs-lookup"><span data-stu-id="5b2e9-147">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="5b2e9-148">Gibt Validierungs Nachrichten für ein bestimmtes Modell mit dem `Model`-Parameter aus:</span><span class="sxs-lookup"><span data-stu-id="5b2e9-148">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="5b2e9-149">Die `ValidationMessage` Komponente zeigt Validierungs Meldungen für ein bestimmtes Feld an, das dem [Validierungs Nachrichten-taghilfsprogramm](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)ähnelt.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-149">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="5b2e9-150">Geben Sie das Feld für die Überprüfung mit dem `For`-Attribut und einem Lambda-Ausdruck an, der die Modell Eigenschaft benennt:</span><span class="sxs-lookup"><span data-stu-id="5b2e9-150">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="5b2e9-151">Die Komponenten `ValidationMessage` und `ValidationSummary` unterstützen beliebige Attribute.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-151">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="5b2e9-152">Ein beliebiges Attribut, das nicht mit einem Komponenten Parameter identisch ist, wird dem generierten `<div>` oder `<ul>` Element hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-152">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="5b2e9-153">Benutzerdefinierte Validierungs Attribute</span><span class="sxs-lookup"><span data-stu-id="5b2e9-153">Custom validation attributes</span></span>

<span data-ttu-id="5b2e9-154">Um sicherzustellen, dass ein Validierungs Ergebnis ordnungsgemäß mit einem Feld verknüpft ist, wenn Sie ein [benutzerdefiniertes Validierungs Attribut](xref:mvc/models/validation#custom-attributes)verwenden, übergeben Sie den <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> des Validierungs Kontexts beim Erstellen des <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span><span class="sxs-lookup"><span data-stu-id="5b2e9-154">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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

::: moniker range=">= aspnetcore-3.1"

### <a name="opno-locblazor-data-annotations-validation-package"></a><span data-ttu-id="5b2e9-155">Validierungs Paket für Blazor Daten Anmerkungen</span><span class="sxs-lookup"><span data-stu-id="5b2e9-155">Blazor data annotations validation package</span></span>

<span data-ttu-id="5b2e9-156">[Microsoft. aspnetcore.Blazor. DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) ist ein Paket, das Validierungs Erfahrungs Lücken mithilfe der `DataAnnotationsValidator` Komponente füllt.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-156">The [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) is a package that fills validation experience gaps using the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="5b2e9-157">Das Paket ist zurzeit *experimentell*.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-157">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="5b2e9-158">[CompareProperty]-Attribut</span><span class="sxs-lookup"><span data-stu-id="5b2e9-158">[CompareProperty] attribute</span></span>

<span data-ttu-id="5b2e9-159">Die <xref:System.ComponentModel.DataAnnotations.CompareAttribute> funktioniert nicht gut mit der `DataAnnotationsValidator` Komponente.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-159">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="5b2e9-160">[Microsoft. aspnetcore.Blazor. Mit dem experimentellen DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) -Paket wird ein zusätzliches Validierungs Attribut (`ComparePropertyAttribute`) eingeführt, das diese Einschränkungen behandelt.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-160">The [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="5b2e9-161">In einer Blazor-APP ist `[CompareProperty]` ein direkter Ersatz für das `[Compare]`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-161">In a Blazor app, `[CompareProperty]` is a direct replacement for the `[Compare]` attribute.</span></span> <span data-ttu-id="5b2e9-162">Weitere Informationen finden Sie unter [compareattribute ignoriert mit onvalidsubmit EditForm (ASPNET/aspnetcore-#10643)](https://github.com/aspnet/AspNetCore/issues/10643#issuecomment-543909748).</span><span class="sxs-lookup"><span data-stu-id="5b2e9-162">For more information, see [CompareAttribute ignored with OnValidSubmit EditForm (aspnet/AspNetCore #10643)](https://github.com/aspnet/AspNetCore/issues/10643#issuecomment-543909748).</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="5b2e9-163">Untergeordnete Modelle, Sammlungs Typen und komplexe Typen</span><span class="sxs-lookup"><span data-stu-id="5b2e9-163">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="5b2e9-164"> bietet Unterstützung für das Validieren von Formular Eingaben mithilfe von Daten Anmerkungen mit dem integrierten `DataAnnotationsValidator`.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-164"> provides support for validating form input using data annotations with the built-in `DataAnnotationsValidator`.</span></span> <span data-ttu-id="5b2e9-165">Allerdings überprüft das `DataAnnotationsValidator` nur die Eigenschaften der obersten Ebene des Modells, die an das Formular gebunden sind, das keine Eigenschaften vom Typ "Collection" oder "Complex" ist.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-165">However, the `DataAnnotationsValidator` only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="5b2e9-166">Verwenden Sie die `ObjectGraphDataAnnotationsValidator` der *experimentellen* [Microsoft. aspnetcore.Blazor, um das gesamte Objekt Diagramm des gebundenen Modells zu validieren, einschließlich Eigenschaften vom Typ "Collection" und "Complex". DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) -Paket:</span><span class="sxs-lookup"><span data-stu-id="5b2e9-166">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="5b2e9-167">Kommentieren Sie Modell Eigenschaften mit `[ValidateComplexType]`.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-167">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="5b2e9-168">In den folgenden Modellklassen enthält die `ShipDescription`-Klasse zusätzliche Daten Anmerkungen, die überprüft werden können, wenn das Modell an das Formular gebunden ist:</span><span class="sxs-lookup"><span data-stu-id="5b2e9-168">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="5b2e9-169">*Starship.cs*:</span><span class="sxs-lookup"><span data-stu-id="5b2e9-169">*Starship.cs*:</span></span>

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

<span data-ttu-id="5b2e9-170">*ShipDescription.cs*:</span><span class="sxs-lookup"><span data-stu-id="5b2e9-170">*ShipDescription.cs*:</span></span>

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

::: moniker-end

::: moniker range="< aspnetcore-3.1"

### <a name="validation-of-complex-or-collection-type-properties"></a><span data-ttu-id="5b2e9-171">Überprüfung komplexer Eigenschaften oder Auflistungstyp Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="5b2e9-171">Validation of complex or collection type properties</span></span>

<span data-ttu-id="5b2e9-172">Validierungs Attribute, die auf die Eigenschaften eines Modells angewendet werden, überprüfen, wenn das Formular übermittelt wird.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-172">Validation attributes applied to the properties of a model validate when the form is submitted.</span></span> <span data-ttu-id="5b2e9-173">Die Eigenschaften von Auflistungen oder komplexen Datentypen eines Modells werden jedoch nicht bei der Formular Übermittlung durch die `DataAnnotationsValidator` Komponente überprüft.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-173">However, the properties of collections or complex data types of a model aren't validated on form submission by the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="5b2e9-174">Verwenden Sie eine benutzerdefinierte Validierungs Komponente, um die in diesem Szenario überbten Attribute der Überprüfung zu berücksichtigen.</span><span class="sxs-lookup"><span data-stu-id="5b2e9-174">To honor the nested validation attributes in this scenario, use a custom validation component.</span></span> <span data-ttu-id="5b2e9-175">Ein Beispiel finden Sie unter Beispiel für [Blazor Validierung (ASPNET/Samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span><span class="sxs-lookup"><span data-stu-id="5b2e9-175">For an example, see the [Blazor Validation sample (aspnet/samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span></span>

::: moniker-end

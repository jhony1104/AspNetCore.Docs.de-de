---
title: ASP.net Core blazor-Formulare und-Validierung
author: guardrex
description: Erfahren Sie, wie Sie in blazor Formular-und Feld Validierungs Szenarios verwenden.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/04/2019
uid: blazor/forms-validation
ms.openlocfilehash: 6dcc36c5133367493b476655dbdf73b75db9d168
ms.sourcegitcommit: a7bbe3890befead19440075b05b9674351f98872
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905730"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="64460-103">ASP.net Core blazor-Formulare und-Validierung</span><span class="sxs-lookup"><span data-stu-id="64460-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="64460-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="64460-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="64460-105">Formulare und Validierung werden in blazor mithilfe von [Daten Anmerkungen](xref:mvc/models/validation)unterstützt.</span><span class="sxs-lookup"><span data-stu-id="64460-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="64460-106">Der folgende `ExampleModel` Typ definiert die Validierungs Logik mithilfe von Daten Anmerkungen:</span><span class="sxs-lookup"><span data-stu-id="64460-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="64460-107">Ein Formular wird mithilfe der `EditForm` Komponente definiert.</span><span class="sxs-lookup"><span data-stu-id="64460-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="64460-108">In der folgenden Form werden typische Elemente, Komponenten und Razor-Code veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="64460-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

* <span data-ttu-id="64460-109">Das Formular überprüft Benutzereingaben im `name` Feld mithilfe der im `ExampleModel`-Typ definierten Validierung.</span><span class="sxs-lookup"><span data-stu-id="64460-109">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="64460-110">Das Modell wird im `@code` Block der Komponente erstellt und in einem privaten Feld (`exampleModel`) gespeichert.</span><span class="sxs-lookup"><span data-stu-id="64460-110">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="64460-111">Das-Feld wird dem `Model`-Attribut des `<EditForm>`-Elements zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="64460-111">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="64460-112">Die `DataAnnotationsValidator` Komponente fügt die Validierungs Unterstützung mithilfe von Daten Anmerkungen an.</span><span class="sxs-lookup"><span data-stu-id="64460-112">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="64460-113">Die `ValidationSummary` Komponente fasst Validierungs Nachrichten zusammen.</span><span class="sxs-lookup"><span data-stu-id="64460-113">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="64460-114">`HandleValidSubmit` wird ausgelöst, wenn das Formular erfolgreich übermittelt wird (Überprüfung wird bestanden).</span><span class="sxs-lookup"><span data-stu-id="64460-114">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="64460-115">Eine Reihe integrierter Eingabe Komponenten ist verfügbar, um Benutzereingaben zu empfangen und zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="64460-115">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="64460-116">Eingaben werden überprüft, wenn Sie geändert werden und wenn ein Formular übermittelt wird.</span><span class="sxs-lookup"><span data-stu-id="64460-116">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="64460-117">In der folgenden Tabelle sind die verfügbaren Eingabe Komponenten aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="64460-117">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="64460-118">Eingabe Komponente</span><span class="sxs-lookup"><span data-stu-id="64460-118">Input component</span></span> | <span data-ttu-id="64460-119">Als&hellip; gerendert</span><span class="sxs-lookup"><span data-stu-id="64460-119">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="64460-120">Alle Eingabe Komponenten, einschließlich `EditForm`, unterstützen beliebige Attribute.</span><span class="sxs-lookup"><span data-stu-id="64460-120">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="64460-121">Dem gerenderten HTML-Element wird ein beliebiges Attribut hinzugefügt, das keinem Komponenten Parameter entspricht.</span><span class="sxs-lookup"><span data-stu-id="64460-121">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="64460-122">Eingabe Komponenten stellen das Standardverhalten für die Validierung bei der Bearbeitung und das Ändern der CSS-Klasse bereit, um den feldzustand widerzuspiegeln.</span><span class="sxs-lookup"><span data-stu-id="64460-122">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="64460-123">Einige Komponenten enthalten nützliche Logik für die Verarbeitung.</span><span class="sxs-lookup"><span data-stu-id="64460-123">Some components include useful parsing logic.</span></span> <span data-ttu-id="64460-124">Beispielsweise können `InputDate` und `InputNumber` nicht Parier Bare Werte ordnungsgemäß behandeln, indem Sie Sie als Validierungs Fehler registrieren.</span><span class="sxs-lookup"><span data-stu-id="64460-124">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="64460-125">Typen, die NULL-Werte akzeptieren können, unterstützen auch die NULL-Zulässigkeit des Zielfelds (z. b. `int?`).</span><span class="sxs-lookup"><span data-stu-id="64460-125">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="64460-126">Der folgende `Starship` Typ definiert die Validierungs Logik mit einem größeren Satz von Eigenschaften und Daten Anmerkungen als die früheren `ExampleModel`:</span><span class="sxs-lookup"><span data-stu-id="64460-126">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="64460-127">Im vorherigen Beispiel ist `Description` optional, da keine Daten Anmerkungen vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="64460-127">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="64460-128">Im folgenden Formular werden Benutzereingaben mithilfe der im `Starship` Modell definierten Validierung überprüft:</span><span class="sxs-lookup"><span data-stu-id="64460-128">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```cshtml
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

<span data-ttu-id="64460-129">Der `EditForm` erstellt eine `EditContext` als einen [kaskadierenden Wert](xref:blazor/components#cascading-values-and-parameters) , der Metadaten zum Bearbeitungsprozess nachverfolgt, einschließlich der geänderten Felder und der aktuellen Validierungs Meldungen.</span><span class="sxs-lookup"><span data-stu-id="64460-129">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="64460-130">Der `EditForm` bietet auch praktische Ereignisse für gültige und ungültige übermitteln (`OnValidSubmit`, `OnInvalidSubmit`).</span><span class="sxs-lookup"><span data-stu-id="64460-130">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="64460-131">Alternativ können Sie `OnSubmit` verwenden, um die Validierungs-und Prüf Feldwerte mit benutzerdefiniertem Validierungscode zu initiieren.</span><span class="sxs-lookup"><span data-stu-id="64460-131">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="64460-132">InputText basierend auf dem Eingabe Ereignis</span><span class="sxs-lookup"><span data-stu-id="64460-132">InputText based on the input event</span></span>

<span data-ttu-id="64460-133">Verwenden Sie die `InputText` Komponente, um eine benutzerdefinierte Komponente zu erstellen, die das Ereignis `input` anstelle des `change` Ereignisses verwendet.</span><span class="sxs-lookup"><span data-stu-id="64460-133">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="64460-134">Erstellen Sie eine-Komponente mit dem folgenden Markup, und verwenden Sie die-Komponente, ebenso wie `InputText` verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="64460-134">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```cshtml
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="validation-support"></a><span data-ttu-id="64460-135">Validierungs Unterstützung</span><span class="sxs-lookup"><span data-stu-id="64460-135">Validation support</span></span>

<span data-ttu-id="64460-136">Die `DataAnnotationsValidator` Komponente fügt die Validierungs Unterstützung mithilfe von Daten Anmerkungen an den kaskadierenden `EditContext`an.</span><span class="sxs-lookup"><span data-stu-id="64460-136">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="64460-137">Das Aktivieren der Unterstützung für die Validierung mithilfe von Daten Anmerkungen erfordert diese explizite Geste.</span><span class="sxs-lookup"><span data-stu-id="64460-137">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="64460-138">Um ein anderes Validierungssystem als Daten Anmerkungen zu verwenden, ersetzen Sie den `DataAnnotationsValidator` durch eine benutzerdefinierte Implementierung.</span><span class="sxs-lookup"><span data-stu-id="64460-138">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="64460-139">Die ASP.net Core-Implementierung ist für die Untersuchung in der Verweis Quelle verfügbar: [dataannotationsvalidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[adddataannotationsvalidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="64460-139">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

<span data-ttu-id="64460-140">Die `ValidationSummary` Komponente fasst alle Validierungs Nachrichten zusammen, die dem taghilfsprogramm für die [Validierungs Zusammenfassung](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)ähneln.</span><span class="sxs-lookup"><span data-stu-id="64460-140">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span></span>

<span data-ttu-id="64460-141">Die `ValidationMessage` Komponente zeigt Validierungs Meldungen für ein bestimmtes Feld an, das dem [Validierungs Nachrichten-taghilfsprogramm](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)ähnelt.</span><span class="sxs-lookup"><span data-stu-id="64460-141">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="64460-142">Geben Sie das Feld für die Überprüfung mit dem `For`-Attribut und einem Lambda-Ausdruck an, der die Modell Eigenschaft benennt:</span><span class="sxs-lookup"><span data-stu-id="64460-142">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="64460-143">Die Komponenten `ValidationMessage` und `ValidationSummary` unterstützen beliebige Attribute.</span><span class="sxs-lookup"><span data-stu-id="64460-143">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="64460-144">Ein beliebiges Attribut, das nicht mit einem Komponenten Parameter identisch ist, wird dem generierten `<div>` oder `<ul>` Element hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="64460-144">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="64460-145">**Paket "Microsoft. aspnetcore. blazor. DataAnnotations. Validation"**</span><span class="sxs-lookup"><span data-stu-id="64460-145">**Microsoft.AspNetCore.Blazor.DataAnnotations.Validation package**</span></span>

<span data-ttu-id="64460-146">[Microsoft. aspnetcore. blazor. DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) ist ein Paket, das die Lücken der Überprüfungs Möglichkeiten mithilfe der `DataAnnotationsValidator` Komponente füllt.</span><span class="sxs-lookup"><span data-stu-id="64460-146">The [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) is a package that fills validation experience gaps using the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="64460-147">Das Paket ist zurzeit *experimentell*, und wir planen, diese Szenarien in einer zukünftigen Version in das ASP.net Core Framework aufzunehmen.</span><span class="sxs-lookup"><span data-stu-id="64460-147">The package is currently *experimental*, and we plan to add these scenarios into the ASP.NET Core framework in a future release.</span></span>

<span data-ttu-id="64460-148">Die `DataAnnotationsValidator` Komponente überprüft keine untergeordneten Eigenschaften komplexer Eigenschaften für ein Validierungs Modell.</span><span class="sxs-lookup"><span data-stu-id="64460-148">The `DataAnnotationsValidator` component doesn't validate subproperties of complex properties on a validating model.</span></span> <span data-ttu-id="64460-149">Elemente von Auflistungstyp Eigenschaften werden nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="64460-149">Items of collection-type properties aren't validated.</span></span> <span data-ttu-id="64460-150">Zum Überprüfen dieser Typen führt das `Microsoft.AspNetCore.Blazor.DataAnnotations.Validation`-Paket das `ValidateComplexType` Validation-Attribut ein, das zusammen mit der `ObjectGraphDataAnnotationsValidator` Komponente funktioniert.</span><span class="sxs-lookup"><span data-stu-id="64460-150">To validate these types, the `Microsoft.AspNetCore.Blazor.DataAnnotations.Validation` package introduces the `ValidateComplexType` validation attribute that works in tandem with the `ObjectGraphDataAnnotationsValidator` component.</span></span> <span data-ttu-id="64460-151">Ein Beispiel für diese verwendeten Typen finden Sie im [GitHub-Repository für die blazor-Validierung ](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span><span class="sxs-lookup"><span data-stu-id="64460-151">For an example of these types in use, see the [Blazor Validation sample in the aspnet/samples GitHub repository ](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span></span>

<span data-ttu-id="64460-152">Die <xref:System.ComponentModel.DataAnnotations.CompareAttribute> funktioniert nicht gut mit der `DataAnnotationsValidator` Komponente.</span><span class="sxs-lookup"><span data-stu-id="64460-152">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="64460-153">Das `Microsoft.AspNetCore.Blazor.DataAnnotations.Validation`-Paket führt ein zusätzliches Validierungs Attribut, `ComparePropertyAttribute`, ein, das diese Einschränkungen umgangen.</span><span class="sxs-lookup"><span data-stu-id="64460-153">The `Microsoft.AspNetCore.Blazor.DataAnnotations.Validation` package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="64460-154">In einer blazor-APP ist `ComparePropertyAttribute` ein direkter Ersatz für die `CompareAttribute`.</span><span class="sxs-lookup"><span data-stu-id="64460-154">In a Blazor app, `ComparePropertyAttribute` is a direct replacement for the `CompareAttribute`.</span></span> <span data-ttu-id="64460-155">Weitere Informationen finden Sie unter [compareattribute ignoriert mit onvalidsubmit EditForm (ASPNET/aspnetcore \#10643)](https://github.com/aspnet/AspNetCore/issues/10643#issuecomment-543909748).</span><span class="sxs-lookup"><span data-stu-id="64460-155">For more information, see [CompareAttribute ignored with OnValidSubmit EditForm (aspnet/AspNetCore \#10643)](https://github.com/aspnet/AspNetCore/issues/10643#issuecomment-543909748).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.1"

### <a name="validation-of-complex-or-collection-type-properties"></a><span data-ttu-id="64460-156">Überprüfung komplexer Eigenschaften oder Auflistungstyp Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="64460-156">Validation of complex or collection type properties</span></span>

<span data-ttu-id="64460-157">Validierungs Attribute, die auf die Eigenschaften eines Modells angewendet werden, überprüfen, wenn das Formular übermittelt wird.</span><span class="sxs-lookup"><span data-stu-id="64460-157">Validation attributes applied to the properties of a model validate when the form is submitted.</span></span> <span data-ttu-id="64460-158">Die Eigenschaften von Auflistungen oder komplexen Datentypen eines Modells werden jedoch nicht bei der Formular Übermittlung durch die `DataAnnotationsValidator` Komponente überprüft.</span><span class="sxs-lookup"><span data-stu-id="64460-158">However, the properties of collections or complex data types of a model aren't validated on form submission by the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="64460-159">Verwenden Sie eine benutzerdefinierte Validierungs Komponente, um die in diesem Szenario überbten Attribute der Überprüfung zu berücksichtigen.</span><span class="sxs-lookup"><span data-stu-id="64460-159">To honor the nested validation attributes in this scenario, use a custom validation component.</span></span> <span data-ttu-id="64460-160">Ein Beispiel finden Sie [im GitHub-Repository ASPNET/Samples im Beispiel zur blazor-Validierung](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span><span class="sxs-lookup"><span data-stu-id="64460-160">For an example, see the [Blazor Validation sample in the aspnet/samples GitHub repository](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span></span>

::: moniker-end

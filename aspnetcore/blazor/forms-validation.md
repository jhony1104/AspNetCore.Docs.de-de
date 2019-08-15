---
title: ASP.net Core blazor-Formulare und-Validierung
author: guardrex
description: Erfahren Sie, wie Sie in blazor Formular-und Feld Validierungs Szenarios verwenden.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/forms-validation
ms.openlocfilehash: 0b2e38cdbd974a28960b917fb6b5ce370f8c4659
ms.sourcegitcommit: f5f0ff65d4e2a961939762fb00e654491a2c772a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030337"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="e5a70-103">ASP.net Core blazor-Formulare und-Validierung</span><span class="sxs-lookup"><span data-stu-id="e5a70-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="e5a70-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e5a70-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e5a70-105">Formulare und Validierung werden in blazor mithilfe von [Daten Anmerkungen](xref:mvc/models/validation)unterstützt.</span><span class="sxs-lookup"><span data-stu-id="e5a70-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

> [!NOTE]
> <span data-ttu-id="e5a70-106">Formulare und Validierungs Szenarien werden wahrscheinlich mit jeder Vorschauversion geändert.</span><span class="sxs-lookup"><span data-stu-id="e5a70-106">Forms and validation scenarios are likely to change with each preview release.</span></span>

<span data-ttu-id="e5a70-107">Der folgende `ExampleModel` Typ definiert die Validierungs Logik mithilfe von Daten Anmerkungen:</span><span class="sxs-lookup"><span data-stu-id="e5a70-107">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="e5a70-108">Ein Formular wird mithilfe der `EditForm` -Komponente definiert.</span><span class="sxs-lookup"><span data-stu-id="e5a70-108">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="e5a70-109">In der folgenden Form werden typische Elemente, Komponenten und Razor-Code veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="e5a70-109">The following form demonstrates typical elements, components, and Razor code:</span></span>

```csharp
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="@exampleModel.Name" />

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

* <span data-ttu-id="e5a70-110">Das Formular überprüft die Benutzereingaben im `name` Feld mithilfe der `ExampleModel` im-Typ definierten Validierung.</span><span class="sxs-lookup"><span data-stu-id="e5a70-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="e5a70-111">Das Modell wird im `@code` Block der Komponente erstellt und in einem privaten Feld (`exampleModel`) gespeichert.</span><span class="sxs-lookup"><span data-stu-id="e5a70-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="e5a70-112">Das-Feld wird dem `Model` -Attribut `<EditForm>` des-Elements zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="e5a70-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="e5a70-113">Die `DataAnnotationsValidator` Komponente fügt die Validierungs Unterstützung mithilfe von Daten Anmerkungen an.</span><span class="sxs-lookup"><span data-stu-id="e5a70-113">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="e5a70-114">Die `ValidationSummary` Komponente fasst Validierungs Nachrichten zusammen.</span><span class="sxs-lookup"><span data-stu-id="e5a70-114">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="e5a70-115">`HandleValidSubmit`wird ausgelöst, wenn das Formular erfolgreich übermittelt wird (Überprüfung wird bestanden).</span><span class="sxs-lookup"><span data-stu-id="e5a70-115">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="e5a70-116">Eine Reihe integrierter Eingabe Komponenten ist verfügbar, um Benutzereingaben zu empfangen und zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="e5a70-116">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="e5a70-117">Eingaben werden überprüft, wenn Sie geändert werden und wenn ein Formular übermittelt wird.</span><span class="sxs-lookup"><span data-stu-id="e5a70-117">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="e5a70-118">In der folgenden Tabelle sind die verfügbaren Eingabe Komponenten aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="e5a70-118">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="e5a70-119">Eingabe Komponente</span><span class="sxs-lookup"><span data-stu-id="e5a70-119">Input component</span></span> | <span data-ttu-id="e5a70-120">Gerendert als&hellip;</span><span class="sxs-lookup"><span data-stu-id="e5a70-120">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="e5a70-121">Alle Eingabe Komponenten, einschließlich `EditForm`, unterstützen beliebige Attribute.</span><span class="sxs-lookup"><span data-stu-id="e5a70-121">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="e5a70-122">Dem gerenderten HTML-Element wird ein beliebiges Attribut hinzugefügt, das keinem Komponenten Parameter entspricht.</span><span class="sxs-lookup"><span data-stu-id="e5a70-122">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="e5a70-123">Eingabe Komponenten stellen das Standardverhalten für die Validierung bei der Bearbeitung und das Ändern der CSS-Klasse bereit, um den feldzustand widerzuspiegeln.</span><span class="sxs-lookup"><span data-stu-id="e5a70-123">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="e5a70-124">Einige Komponenten enthalten nützliche Logik für die Verarbeitung.</span><span class="sxs-lookup"><span data-stu-id="e5a70-124">Some components include useful parsing logic.</span></span> <span data-ttu-id="e5a70-125">Beispielsweise können `InputDate` und `InputNumber` nicht Parier Bare Werte ordnungsgemäß behandeln, indem Sie als Validierungs Fehler registriert werden.</span><span class="sxs-lookup"><span data-stu-id="e5a70-125">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="e5a70-126">Typen, die NULL-Werte akzeptieren können, unterstützen auch die NULL-Zulässigkeit des Zielfelds (z `int?`. b.).</span><span class="sxs-lookup"><span data-stu-id="e5a70-126">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="e5a70-127">Der folgende `Starship` Typ definiert die Validierungs Logik mit einem größeren Satz von Eigenschaften und Daten Anmerkungen als die früheren `ExampleModel`:</span><span class="sxs-lookup"><span data-stu-id="e5a70-127">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="e5a70-128">Im vorherigen Beispiel ist optional `Description` , da keine Daten Anmerkungen vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="e5a70-128">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="e5a70-129">Im folgenden Formular werden Benutzereingaben mithilfe der im `Starship` Modell definierten Validierung überprüft:</span><span class="sxs-lookup"><span data-stu-id="e5a70-129">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="e5a70-130">Erstellt ein `EditContext` -Objekt als einen [kaskadierenden Wert](xref:blazor/components#cascading-values-and-parameters) , der Metadaten zum Bearbeitungsprozess nachverfolgt, einschließlich der geänderten Felder und der aktuellen Validierungs Meldungen. `EditForm`</span><span class="sxs-lookup"><span data-stu-id="e5a70-130">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="e5a70-131">Bietet auch praktische Ereignisse für gültige und ungültige übermitteln`OnValidSubmit`( `OnInvalidSubmit`,). `EditForm`</span><span class="sxs-lookup"><span data-stu-id="e5a70-131">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="e5a70-132">Alternativ können Sie `OnSubmit` verwenden, um die Validierungs-und Prüf Feldwerte mit benutzerdefiniertem Validierungscode zu initiieren.</span><span class="sxs-lookup"><span data-stu-id="e5a70-132">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="e5a70-133">Die `DataAnnotationsValidator` Komponente fügt die Validierungs Unterstützung mithilfe von Daten Anmerkungen an den `EditContext`kaskadierenden an.</span><span class="sxs-lookup"><span data-stu-id="e5a70-133">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="e5a70-134">Das Aktivieren der Unterstützung für die Validierung mithilfe von Daten Anmerkungen erfordert derzeit diese explizite Geste, aber wir erwägen, dies als Standardverhalten festzustellen, das Sie dann außer Kraft setzen können.</span><span class="sxs-lookup"><span data-stu-id="e5a70-134">Enabling support for validation using data annotations currently requires this explicit gesture, but we're considering making this the default behavior that you can then override.</span></span> <span data-ttu-id="e5a70-135">Um ein anderes Validierungssystem als Daten Anmerkungen zu verwenden, ersetzen `DataAnnotationsValidator` Sie durch eine benutzerdefinierte Implementierung.</span><span class="sxs-lookup"><span data-stu-id="e5a70-135">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="e5a70-136">Die ASP.net Core-Implementierung ist zur Überprüfung in der Verweis Quelle verfügbar: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="e5a70-136">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs).</span></span> <span data-ttu-id="e5a70-137">*Die ASP.net Core Implementierung unterliegt während des Vorschau Zeitraums schnelle Updates.*</span><span class="sxs-lookup"><span data-stu-id="e5a70-137">*The ASP.NET Core implementation is subject to rapid updates during the preview release period.*</span></span>

<span data-ttu-id="e5a70-138">Die `ValidationSummary` Komponente fasst alle Validierungs Nachrichten zusammen, die dem taghilfsprogramm für die [Validierungs Zusammenfassung](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)ähneln.</span><span class="sxs-lookup"><span data-stu-id="e5a70-138">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span></span>

<span data-ttu-id="e5a70-139">Die `ValidationMessage` Komponente zeigt Validierungs Meldungen für ein bestimmtes Feld an, das dem [Validierungs Nachrichten](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)-taghilfsprogramm ähnelt.</span><span class="sxs-lookup"><span data-stu-id="e5a70-139">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="e5a70-140">Geben Sie das Feld zur Validierung mit `For` dem-Attribut und einem Lambda-Ausdruck an, der die Model-Eigenschaft benennt:</span><span class="sxs-lookup"><span data-stu-id="e5a70-140">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="e5a70-141">Die `ValidationMessage` Komponenten `ValidationSummary` und unterstützen beliebige Attribute.</span><span class="sxs-lookup"><span data-stu-id="e5a70-141">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="e5a70-142">Alle Attribute, die nicht mit einem Komponenten Parameter identisch sind, werden `<div>` dem `<ul>` generierten-oder-Element hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e5a70-142">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

---
title: Razor-Komponenten-Formulare und Überprüfung
author: guardrex
description: Erfahren Sie, wie Formulare und Szenarien zur Überprüfung von Feld in Razor-Komponenten verwenden.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2019
uid: razor-components/forms-validation
ms.openlocfilehash: a4ed75efc6b5a733ce4ff4e82f354a8e2fd48500
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/10/2019
ms.locfileid: "59515543"
---
# <a name="razor-components-forms-and-validation"></a><span data-ttu-id="02646-103">Razor-Komponenten-Formulare und Überprüfung</span><span class="sxs-lookup"><span data-stu-id="02646-103">Razor Components forms and validation</span></span>

<span data-ttu-id="02646-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="02646-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="02646-105">Formulare und Überprüfung sind in Razor-Komponenten, die mithilfe von unterstützt [datenanmerkungen](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="02646-105">Forms and validation are supported in Razor Components using [data annotations](xref:mvc/models/validation).</span></span>

> [!NOTE]
> <span data-ttu-id="02646-106">Formulare und Szenarien zur Überprüfung von sind wahrscheinlich mit jeder Preview-Version zu ändern.</span><span class="sxs-lookup"><span data-stu-id="02646-106">Forms and validation scenarios are likely to change with each preview release.</span></span>

<span data-ttu-id="02646-107">Die folgenden `ExampleModel` Typ definiert mithilfe von datenanmerkungen Validierungslogik:</span><span class="sxs-lookup"><span data-stu-id="02646-107">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="02646-108">Ein Formular wird mit definiert die `<EditForm>` Komponente.</span><span class="sxs-lookup"><span data-stu-id="02646-108">A form is defined using the `<EditForm>` component.</span></span> <span data-ttu-id="02646-109">Das folgende Format zeigt typische Elemente, Komponenten und Razor-Code:</span><span class="sxs-lookup"><span data-stu-id="02646-109">The following form demonstrates typical elements, components, and Razor code:</span></span>

```csharp
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" bind-Value="@exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@functions {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

* <span data-ttu-id="02646-110">Das Formular validiert Benutzereingaben in die `name` Feld mit der Überprüfung in definiert die `ExampleModel` Typ.</span><span class="sxs-lookup"><span data-stu-id="02646-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="02646-111">Das Modell wird in der Komponente erstellt `@functions` blockieren und in einem privaten Feld gespeichert (`exampleModel`).</span><span class="sxs-lookup"><span data-stu-id="02646-111">The model is created in the component's `@functions` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="02646-112">Das Feld zugewiesen wird die `Model` Attribut der `<EditForm>`.</span><span class="sxs-lookup"><span data-stu-id="02646-112">The field is assigned to the `Model` attribute of the `<EditForm>`.</span></span>
* <span data-ttu-id="02646-113">Die validierungssteuerelementkomponente für Data Annotations (`<DataAnnotationsValidator>`) Fügt die Unterstützung der Validierung mit datenanmerkungen.</span><span class="sxs-lookup"><span data-stu-id="02646-113">The Data Annotations Validator component (`<DataAnnotationsValidator>`) attaches validation support using data annotations.</span></span>
* <span data-ttu-id="02646-114">Die Validierungszusammenfassung-Komponente (`<ValidationSummary>`) werden die validierungsmeldungen zusammengefasst.</span><span class="sxs-lookup"><span data-stu-id="02646-114">The Validation Summary component (`<ValidationSummary>`) summarizes validation messages.</span></span>
* `HandleValidSubmit` <span data-ttu-id="02646-115">wird ausgelöst, wenn das Formular erfolgreich (die Überprüfung besteht) sendet.</span><span class="sxs-lookup"><span data-stu-id="02646-115">is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="02646-116">Eine Reihe von integrierten Eingabe-Komponenten stehen zum Empfangen und Validieren von Benutzereingaben.</span><span class="sxs-lookup"><span data-stu-id="02646-116">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="02646-117">Eingaben werden überprüft, wenn sie geändert haben und ein Formular übermittelt wird.</span><span class="sxs-lookup"><span data-stu-id="02646-117">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="02646-118">In der folgenden Tabelle werden die verfügbare Eingabe-Komponenten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="02646-118">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="02646-119">Input-Komponente</span><span class="sxs-lookup"><span data-stu-id="02646-119">Input component</span></span>   | <span data-ttu-id="02646-120">Rendern als&hellip;</span><span class="sxs-lookup"><span data-stu-id="02646-120">Rendered as&hellip;</span></span>       |
| ----------------- | ------------------------- |
| `<InputText>`     | `<input>`                 |
| `<InputTextArea>` | `<textarea>`              |
| `<InputSelect>`   | `<select>`                |
| `<InputNumber>`   | `<input type="number">`   |
| `<InputCheckbox>` | `<input type="checkbox">` |
| `<InputDate>`     | `<input type="date">`     |

<span data-ttu-id="02646-121">Eingabe-Komponenten bieten Standardverhalten für die Validierung von bearbeiten und ihre CSS-Klasse den Feld Zustand entsprechend zu ändern.</span><span class="sxs-lookup"><span data-stu-id="02646-121">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="02646-122">Einige Komponenten enthalten nützliche Analyselogik erforderlich.</span><span class="sxs-lookup"><span data-stu-id="02646-122">Some components include useful parsing logic.</span></span> <span data-ttu-id="02646-123">Z. B. `<InputDate>` und `<InputNumber>` nicht analysierbaren Werte ordnungsgemäß behandeln, indem Sie sie als Validierungsfehler zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="02646-123">For example, `<InputDate>` and `<InputNumber>` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="02646-124">Typen, die null-Werte akzeptiert auch NULL-Zulässigkeit des Feld "Ziel" unterstützen (z. B. `int?`).</span><span class="sxs-lookup"><span data-stu-id="02646-124">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="02646-125">Die folgenden `Starship` Typ definiert Validierungslogik, die mit einem größeren Satz von Eigenschaften und [datenanmerkungen](xref:mvc/models/validation) als die vorherige `ExampleModel`:</span><span class="sxs-lookup"><span data-stu-id="02646-125">The following `Starship` type defines validation logic using a larger set of properties and [data annotations](xref:mvc/models/validation) than the earlier `ExampleModel`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, 
        ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    // Optional (no data annotations)
    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "Form disallowed for unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

<span data-ttu-id="02646-126">Das folgende Format überprüft der Benutzereingabe mit der Überprüfung in definiert die `Starship` Modell:</span><span class="sxs-lookup"><span data-stu-id="02646-126">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```cshtml
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label for="identifier">Identifier: </label>
        <InputText id="identifier" bind-Value="@starship.Identifier" />
    </p>
    <p>
        <label for="description">Description (optional): </label>
        <InputTextArea Id="description" bind-Value="@starship.Description" />
    </p>
    <p>
        <label for="classification">Primary Classification: </label>
        <InputSelect id="classification" bind-Value="@starship.Classification">
            <option value"">Select classification ...</option>
            <option value="Defense">Defense</option>
            <option value="Exploration">Exploration</option>
            <option value="Diplomacy">Diplomacy</option>
        </InputSelect>
    </p>
    <p>
        <label for="accommodation">Maximum Accommodation: </label>
        <InputNumber id="accommodation" 
            bind-Value="@starship.MaximumAccommodation" />
    </p>
    <p>
        <label for="valid">Engineering Approval: </label>
        <InputCheckbox id="valid" bind-Value="@starship.IsValidatedDesign" />
    </p>
    <p>
        <label for="productionDate">Production Date: </label>
        <InputDate Id="productionDate" bind-Value="@starship.ProductionDate" />
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="http://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@functions {
    private Starship starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="02646-127">Die `<EditForm>` erstellt eine `EditContext` als eine [kaskadierende Wert](xref:razor-components/components#cascading-values-and-parameters) Metadaten über den bearbeiten-Prozess, einschließlich welche Felder geändert wurden und die aktuelle Nachrichten zur inhaltsprüfung wird nachverfolgt.</span><span class="sxs-lookup"><span data-stu-id="02646-127">The `<EditForm>` creates an `EditContext` as a [cascading value](xref:razor-components/components#cascading-values-and-parameters) that tracks metadata about the edit process, including what fields have been modified and the current validation messages.</span></span> <span data-ttu-id="02646-128">Die `<EditForm>` bietet außerdem einfache Ereignisse für die gültigen und ungültigen übermittelt (`OnValidSubmit`, `OnInvalidSubmit`).</span><span class="sxs-lookup"><span data-stu-id="02646-128">The `<EditForm>` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="02646-129">Verwenden Sie alternativ `OnSubmit` die Überprüfung auszulösen, und überprüfen Sie die Feldwerte mit benutzerdefiniertem Überprüfungscode.</span><span class="sxs-lookup"><span data-stu-id="02646-129">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="02646-130">Die validierungssteuerelementkomponente für Data Annotations (`<DataAnnotationsValidator>`) Fügt die Unterstützung der Validierung mit datenanmerkungen, die kaskadierenden `EditContext`.</span><span class="sxs-lookup"><span data-stu-id="02646-130">The Data Annotations Validator component (`<DataAnnotationsValidator>`) attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="02646-131">Aktivieren der Unterstützung für die Validierung mit datenanmerkungen derzeit diese explizite Geste mit erfordert jedoch dadurch das Standardverhalten, das Sie, klicken Sie dann überschreiben können wir ziehen.</span><span class="sxs-lookup"><span data-stu-id="02646-131">Enabling support for validation using data annotations currently requires this explicit gesture, but we're considering making this the default behavior that you can then override.</span></span> <span data-ttu-id="02646-132">Um eine andere System als datenanmerkungen verwenden zu können, ersetzen Sie das Data Annotations-Validierungssteuerelement durch eine benutzerdefinierte Implementierung.</span><span class="sxs-lookup"><span data-stu-id="02646-132">To use a different validation system than data annotations, replace the Data Annotations Validator with a custom implementation.</span></span> <span data-ttu-id="02646-133">Die ASP.NET Core-Implementierung ist für die Überprüfung in der Referenz-Quelle verfügbar: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="02646-133">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs).</span></span> *<span data-ttu-id="02646-134">Die ASP.NET Core-Implementierung ist gelegentlich schnelle Updates während der Vorschau-Version.</span><span class="sxs-lookup"><span data-stu-id="02646-134">The ASP.NET Core implementation is subject to rapid updates during the preview release period.</span></span>*

<span data-ttu-id="02646-135">Die Validierungszusammenfassung-Komponente (`<ValidationSummary>`) Zusammenfassung aller validierungsmeldungen, der ähnelt der [Taghilfsprogramm für die Zusammenfassung der Validierung](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="02646-135">The Validation Summary component (`<ValidationSummary>`) summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span></span>

<span data-ttu-id="02646-136">Die Validierungsmeldung-Komponente (`<ValidationMessage>`) zeigt überprüfungsmeldungen für ein bestimmtes Feld, in dem ähnelt der [Taghilfsprogramm für Validierungsmeldungen](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="02646-136">The Validation Message component (`<ValidationMessage>`) displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="02646-137">Geben Sie das Feld für die Validierung mit dem `For` Attribut und einen Lambda-Ausdruck, der die Modelleigenschaft benennen:</span><span class="sxs-lookup"><span data-stu-id="02646-137">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

> [!NOTE]
> <span data-ttu-id="02646-138">Integrierte Eingabe Komponenten haben Einschränkungen, die wir erwarten, dass in zukünftigen Releases lösen.</span><span class="sxs-lookup"><span data-stu-id="02646-138">Built-in input components have limitations that we expect to resolve in future releases.</span></span> <span data-ttu-id="02646-139">Beispielsweise können Sie beliebige Attribute für die generierte angeben `<input>` Tags.</span><span class="sxs-lookup"><span data-stu-id="02646-139">For example, you can't specify arbitrary attributes on the generated `<input>` tags.</span></span> <span data-ttu-id="02646-140">Erstellen Sie eigene Unterklassen Komponente zum Behandeln von Szenarien mit nicht verfügbarer.</span><span class="sxs-lookup"><span data-stu-id="02646-140">Build your own component subclasses to handle unavailable scenarios.</span></span>

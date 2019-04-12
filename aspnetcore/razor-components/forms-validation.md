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
# <a name="razor-components-forms-and-validation"></a>Razor-Komponenten-Formulare und Überprüfung

Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

Formulare und Überprüfung sind in Razor-Komponenten, die mithilfe von unterstützt [datenanmerkungen](xref:mvc/models/validation).

> [!NOTE]
> Formulare und Szenarien zur Überprüfung von sind wahrscheinlich mit jeder Preview-Version zu ändern.

Die folgenden `ExampleModel` Typ definiert mithilfe von datenanmerkungen Validierungslogik:

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

Ein Formular wird mit definiert die `<EditForm>` Komponente. Das folgende Format zeigt typische Elemente, Komponenten und Razor-Code:

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

* Das Formular validiert Benutzereingaben in die `name` Feld mit der Überprüfung in definiert die `ExampleModel` Typ. Das Modell wird in der Komponente erstellt `@functions` blockieren und in einem privaten Feld gespeichert (`exampleModel`). Das Feld zugewiesen wird die `Model` Attribut der `<EditForm>`.
* Die validierungssteuerelementkomponente für Data Annotations (`<DataAnnotationsValidator>`) Fügt die Unterstützung der Validierung mit datenanmerkungen.
* Die Validierungszusammenfassung-Komponente (`<ValidationSummary>`) werden die validierungsmeldungen zusammengefasst.
* `HandleValidSubmit` wird ausgelöst, wenn das Formular erfolgreich (die Überprüfung besteht) sendet.

Eine Reihe von integrierten Eingabe-Komponenten stehen zum Empfangen und Validieren von Benutzereingaben. Eingaben werden überprüft, wenn sie geändert haben und ein Formular übermittelt wird. In der folgenden Tabelle werden die verfügbare Eingabe-Komponenten angezeigt.

| Input-Komponente   | Rendern als&hellip;       |
| ----------------- | ------------------------- |
| `<InputText>`     | `<input>`                 |
| `<InputTextArea>` | `<textarea>`              |
| `<InputSelect>`   | `<select>`                |
| `<InputNumber>`   | `<input type="number">`   |
| `<InputCheckbox>` | `<input type="checkbox">` |
| `<InputDate>`     | `<input type="date">`     |

Eingabe-Komponenten bieten Standardverhalten für die Validierung von bearbeiten und ihre CSS-Klasse den Feld Zustand entsprechend zu ändern. Einige Komponenten enthalten nützliche Analyselogik erforderlich. Z. B. `<InputDate>` und `<InputNumber>` nicht analysierbaren Werte ordnungsgemäß behandeln, indem Sie sie als Validierungsfehler zu registrieren. Typen, die null-Werte akzeptiert auch NULL-Zulässigkeit des Feld "Ziel" unterstützen (z. B. `int?`).

Die folgenden `Starship` Typ definiert Validierungslogik, die mit einem größeren Satz von Eigenschaften und [datenanmerkungen](xref:mvc/models/validation) als die vorherige `ExampleModel`:

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

Das folgende Format überprüft der Benutzereingabe mit der Überprüfung in definiert die `Starship` Modell:

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

Die `<EditForm>` erstellt eine `EditContext` als eine [kaskadierende Wert](xref:razor-components/components#cascading-values-and-parameters) Metadaten über den bearbeiten-Prozess, einschließlich welche Felder geändert wurden und die aktuelle Nachrichten zur inhaltsprüfung wird nachverfolgt. Die `<EditForm>` bietet außerdem einfache Ereignisse für die gültigen und ungültigen übermittelt (`OnValidSubmit`, `OnInvalidSubmit`). Verwenden Sie alternativ `OnSubmit` die Überprüfung auszulösen, und überprüfen Sie die Feldwerte mit benutzerdefiniertem Überprüfungscode.

Die validierungssteuerelementkomponente für Data Annotations (`<DataAnnotationsValidator>`) Fügt die Unterstützung der Validierung mit datenanmerkungen, die kaskadierenden `EditContext`. Aktivieren der Unterstützung für die Validierung mit datenanmerkungen derzeit diese explizite Geste mit erfordert jedoch dadurch das Standardverhalten, das Sie, klicken Sie dann überschreiben können wir ziehen. Um eine andere System als datenanmerkungen verwenden zu können, ersetzen Sie das Data Annotations-Validierungssteuerelement durch eine benutzerdefinierte Implementierung. Die ASP.NET Core-Implementierung ist für die Überprüfung in der Referenz-Quelle verfügbar: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs). *Die ASP.NET Core-Implementierung ist gelegentlich schnelle Updates während der Vorschau-Version.*

Die Validierungszusammenfassung-Komponente (`<ValidationSummary>`) Zusammenfassung aller validierungsmeldungen, der ähnelt der [Taghilfsprogramm für die Zusammenfassung der Validierung](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).

Die Validierungsmeldung-Komponente (`<ValidationMessage>`) zeigt überprüfungsmeldungen für ein bestimmtes Feld, in dem ähnelt der [Taghilfsprogramm für Validierungsmeldungen](xref:mvc/views/working-with-forms#the-validation-message-tag-helper). Geben Sie das Feld für die Validierung mit dem `For` Attribut und einen Lambda-Ausdruck, der die Modelleigenschaft benennen:

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

> [!NOTE]
> Integrierte Eingabe Komponenten haben Einschränkungen, die wir erwarten, dass in zukünftigen Releases lösen. Beispielsweise können Sie beliebige Attribute für die generierte angeben `<input>` Tags. Erstellen Sie eigene Unterklassen Komponente zum Behandeln von Szenarien mit nicht verfügbarer.

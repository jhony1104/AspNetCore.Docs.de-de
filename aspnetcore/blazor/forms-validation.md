---
title: ASP.net Core blazor-Formulare und-Validierung
author: guardrex
description: Erfahren Sie, wie Sie in blazor Formular-und Feld Validierungs Szenarios verwenden.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/15/2019
uid: blazor/forms-validation
ms.openlocfilehash: 2fd76db90a53e328cd2ac8f452fba58365db0384
ms.sourcegitcommit: dc5b293e08336dc236de66ed1834f7ef78359531
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/16/2019
ms.locfileid: "71011061"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a>ASP.net Core blazor-Formulare und-Validierung

Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

Formulare und Validierung werden in blazor mithilfe von [Daten Anmerkungen](xref:mvc/models/validation)unterstützt.

> [!NOTE]
> Formulare und Validierungs Szenarien werden wahrscheinlich mit jeder Vorschauversion geändert.

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

Ein Formular wird mithilfe der `EditForm` -Komponente definiert. In der folgenden Form werden typische Elemente, Komponenten und Razor-Code veranschaulicht:

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

* Das Formular überprüft die Benutzereingaben im `name` Feld mithilfe der `ExampleModel` im-Typ definierten Validierung. Das Modell wird im `@code` Block der Komponente erstellt und in einem privaten Feld (`exampleModel`) gespeichert. Das-Feld wird dem `Model` -Attribut `<EditForm>` des-Elements zugewiesen.
* Die `DataAnnotationsValidator` Komponente fügt die Validierungs Unterstützung mithilfe von Daten Anmerkungen an.
* Die `ValidationSummary` Komponente fasst Validierungs Nachrichten zusammen.
* `HandleValidSubmit`wird ausgelöst, wenn das Formular erfolgreich übermittelt wird (Überprüfung wird bestanden).

Eine Reihe integrierter Eingabe Komponenten ist verfügbar, um Benutzereingaben zu empfangen und zu überprüfen. Eingaben werden überprüft, wenn Sie geändert werden und wenn ein Formular übermittelt wird. In der folgenden Tabelle sind die verfügbaren Eingabe Komponenten aufgeführt.

| Eingabe Komponente | Gerendert als&hellip;       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

Alle Eingabe Komponenten, einschließlich `EditForm`, unterstützen beliebige Attribute. Dem gerenderten HTML-Element wird ein beliebiges Attribut hinzugefügt, das keinem Komponenten Parameter entspricht.

Eingabe Komponenten stellen das Standardverhalten für die Validierung bei der Bearbeitung und das Ändern der CSS-Klasse bereit, um den feldzustand widerzuspiegeln. Einige Komponenten enthalten nützliche Logik für die Verarbeitung. Beispielsweise können `InputDate` und `InputNumber` nicht Parier Bare Werte ordnungsgemäß behandeln, indem Sie als Validierungs Fehler registriert werden. Typen, die NULL-Werte akzeptieren können, unterstützen auch die NULL-Zulässigkeit des Zielfelds (z `int?`. b.).

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

Im vorherigen Beispiel ist optional `Description` , da keine Daten Anmerkungen vorhanden sind.

Im folgenden Formular werden Benutzereingaben mithilfe der im `Starship` Modell definierten Validierung überprüft:

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

Erstellt ein `EditContext` -Objekt als einen [kaskadierenden Wert](xref:blazor/components#cascading-values-and-parameters) , der Metadaten zum Bearbeitungsprozess nachverfolgt, einschließlich der geänderten Felder und der aktuellen Validierungs Meldungen. `EditForm` Bietet auch praktische Ereignisse für gültige und ungültige übermitteln`OnValidSubmit`( `OnInvalidSubmit`,). `EditForm` Alternativ können Sie `OnSubmit` verwenden, um die Validierungs-und Prüf Feldwerte mit benutzerdefiniertem Validierungscode zu initiieren.

## <a name="inputtext-based-on-the-input-event"></a>InputText basierend auf dem Eingabe Ereignis

Verwenden Sie `InputText` die-Komponente, um eine benutzerdefinierte Komponente `input` zu erstellen, die `change` anstelle des-Ereignisses das-Ereignis verwendet.

Erstellen Sie eine-Komponente mit dem folgenden Markup, und verwenden Sie die `InputText` -Komponente wie verwendet:

```cshtml
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="validation-support"></a>Validierungs Unterstützung

Die `DataAnnotationsValidator` Komponente fügt die Validierungs Unterstützung mithilfe von Daten Anmerkungen an den `EditContext`kaskadierenden an. Das Aktivieren der Unterstützung für die Validierung mithilfe von Daten Anmerkungen erfordert diese explizite Geste. Um ein anderes Validierungssystem als Daten Anmerkungen zu verwenden, ersetzen `DataAnnotationsValidator` Sie durch eine benutzerdefinierte Implementierung. Die ASP.net Core-Implementierung ist zur Überprüfung in der Verweis Quelle verfügbar: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).

Die `ValidationSummary` Komponente fasst alle Validierungs Nachrichten zusammen, die dem taghilfsprogramm für die [Validierungs Zusammenfassung](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)ähneln.

Die `ValidationMessage` Komponente zeigt Validierungs Meldungen für ein bestimmtes Feld an, das dem [Validierungs Nachrichten-taghilfsprogramm](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)ähnelt. Geben Sie das Feld zur Validierung mit `For` dem-Attribut und einem Lambda-Ausdruck an, der die Model-Eigenschaft benennt:

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

Die `ValidationMessage` Komponenten `ValidationSummary` und unterstützen beliebige Attribute. Alle Attribute, die nicht mit einem Komponenten Parameter identisch sind, werden `<div>` dem `<ul>` generierten-oder-Element hinzugefügt.

### <a name="validation-of-complex-or-collection-type-properties"></a>Überprüfung komplexer Eigenschaften oder Auflistungstyp Eigenschaften

Validierungs Attribute, die auf die Eigenschaften eines Modells angewendet werden, überprüfen, wenn das Formular übermittelt wird. Die Eigenschaften von Auflistungen oder komplexen Datentypen eines Modells werden jedoch nicht bei der Formular Übermittlung überprüft. Verwenden Sie eine benutzerdefinierte Validierungs Komponente, um die in diesem Szenario überbten Attribute der Überprüfung zu berücksichtigen. Ein Beispiel finden Sie [im GitHub-Repository ASPNET/Samples im Beispiel zur blazor-Validierung](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).

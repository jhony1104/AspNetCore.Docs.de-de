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

* Das Formular überprüft Benutzereingaben im `name` Feld mithilfe der im `ExampleModel`-Typ definierten Validierung. Das Modell wird im `@code` Block der Komponente erstellt und in einem privaten Feld (`exampleModel`) gespeichert. Das-Feld wird dem `Model`-Attribut des `<EditForm>`-Elements zugewiesen.
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

Der `EditForm` erstellt eine `EditContext` als einen [kaskadierenden Wert](xref:blazor/components#cascading-values-and-parameters) , der Metadaten zum Bearbeitungsprozess nachverfolgt, einschließlich der geänderten Felder und der aktuellen Validierungs Meldungen. Der `EditForm` bietet auch praktische Ereignisse für gültige und ungültige übermitteln (`OnValidSubmit`, `OnInvalidSubmit`). Alternativ können Sie `OnSubmit` verwenden, um die Validierungs-und Prüf Feldwerte mit benutzerdefiniertem Validierungscode zu initiieren.

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

## <a name="validation-support"></a>Validierungs Unterstützung

Die `DataAnnotationsValidator` Komponente fügt die Validierungs Unterstützung mithilfe von Daten Anmerkungen an den kaskadierenden `EditContext`an. Das Aktivieren der Unterstützung für die Validierung mithilfe von Daten Anmerkungen erfordert diese explizite Geste. Um ein anderes Validierungssystem als Daten Anmerkungen zu verwenden, ersetzen Sie den `DataAnnotationsValidator` durch eine benutzerdefinierte Implementierung. Die ASP.net Core-Implementierung ist zur Überprüfung in der Verweis Quelle verfügbar: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).

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

::: moniker range=">= aspnetcore-3.1"

### <a name="opno-locblazor-data-annotations-validation-package"></a>Validierungs Paket für Blazor Daten Anmerkungen

[Microsoft. aspnetcore.Blazor. DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) ist ein Paket, das Validierungs Erfahrungs Lücken mithilfe der `DataAnnotationsValidator` Komponente füllt. Das Paket ist zurzeit *experimentell*.

### <a name="compareproperty-attribute"></a>[CompareProperty]-Attribut

Die <xref:System.ComponentModel.DataAnnotations.CompareAttribute> funktioniert nicht gut mit der `DataAnnotationsValidator` Komponente. [Microsoft. aspnetcore.Blazor. Mit dem experimentellen DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) -Paket wird ein zusätzliches Validierungs Attribut (`ComparePropertyAttribute`) eingeführt, das diese Einschränkungen behandelt. In einer Blazor-APP ist `[CompareProperty]` ein direkter Ersatz für das `[Compare]`-Attribut. Weitere Informationen finden Sie unter [compareattribute ignoriert mit onvalidsubmit EditForm (ASPNET/aspnetcore-#10643)](https://github.com/aspnet/AspNetCore/issues/10643#issuecomment-543909748).

### <a name="nested-models-collection-types-and-complex-types"></a>Untergeordnete Modelle, Sammlungs Typen und komplexe Typen

Blazor bietet Unterstützung für das Validieren von Formular Eingaben mithilfe von Daten Anmerkungen mit dem integrierten `DataAnnotationsValidator`. Allerdings überprüft das `DataAnnotationsValidator` nur die Eigenschaften der obersten Ebene des Modells, die an das Formular gebunden sind, das keine Eigenschaften vom Typ "Collection" oder "Complex" ist.

Verwenden Sie die `ObjectGraphDataAnnotationsValidator` der *experimentellen* [Microsoft. aspnetcore.Blazor, um das gesamte Objekt Diagramm des gebundenen Modells zu validieren, einschließlich Eigenschaften vom Typ "Collection" und "Complex". DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) -Paket:

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
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

::: moniker-end

::: moniker range="< aspnetcore-3.1"

### <a name="validation-of-complex-or-collection-type-properties"></a>Überprüfung komplexer Eigenschaften oder Auflistungstyp Eigenschaften

Validierungs Attribute, die auf die Eigenschaften eines Modells angewendet werden, überprüfen, wenn das Formular übermittelt wird. Die Eigenschaften von Auflistungen oder komplexen Datentypen eines Modells werden jedoch nicht bei der Formular Übermittlung durch die `DataAnnotationsValidator` Komponente überprüft. Verwenden Sie eine benutzerdefinierte Validierungs Komponente, um die in diesem Szenario überbten Attribute der Überprüfung zu berücksichtigen. Ein Beispiel finden Sie unter Beispiel für [Blazor Validierung (ASPNET/Samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).

::: moniker-end

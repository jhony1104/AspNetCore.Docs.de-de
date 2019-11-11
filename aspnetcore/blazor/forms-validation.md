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
# <a name="aspnet-core-blazor-forms-and-validation"></a>ASP.net Core blazor-Formulare und-Validierung

Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

Formulare und Validierung werden in blazor mithilfe von [Daten Anmerkungen](xref:mvc/models/validation)unterstützt.

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

Der `EditForm` erstellt eine `EditContext` als einen [kaskadierenden Wert](xref:blazor/components#cascading-values-and-parameters) , der Metadaten zum Bearbeitungsprozess nachverfolgt, einschließlich der geänderten Felder und der aktuellen Validierungs Meldungen. Der `EditForm` bietet auch praktische Ereignisse für gültige und ungültige übermitteln (`OnValidSubmit`, `OnInvalidSubmit`). Alternativ können Sie `OnSubmit` verwenden, um die Validierungs-und Prüf Feldwerte mit benutzerdefiniertem Validierungscode zu initiieren.

## <a name="inputtext-based-on-the-input-event"></a>InputText basierend auf dem Eingabe Ereignis

Verwenden Sie die `InputText` Komponente, um eine benutzerdefinierte Komponente zu erstellen, die das Ereignis `input` anstelle des `change` Ereignisses verwendet.

Erstellen Sie eine-Komponente mit dem folgenden Markup, und verwenden Sie die-Komponente, ebenso wie `InputText` verwendet wird:

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

Die `DataAnnotationsValidator` Komponente fügt die Validierungs Unterstützung mithilfe von Daten Anmerkungen an den kaskadierenden `EditContext`an. Das Aktivieren der Unterstützung für die Validierung mithilfe von Daten Anmerkungen erfordert diese explizite Geste. Um ein anderes Validierungssystem als Daten Anmerkungen zu verwenden, ersetzen Sie den `DataAnnotationsValidator` durch eine benutzerdefinierte Implementierung. Die ASP.net Core-Implementierung ist für die Untersuchung in der Verweis Quelle verfügbar: [dataannotationsvalidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[adddataannotationsvalidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).

Die `ValidationSummary` Komponente fasst alle Validierungs Nachrichten zusammen, die dem taghilfsprogramm für die [Validierungs Zusammenfassung](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)ähneln.

Die `ValidationMessage` Komponente zeigt Validierungs Meldungen für ein bestimmtes Feld an, das dem [Validierungs Nachrichten-taghilfsprogramm](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)ähnelt. Geben Sie das Feld für die Überprüfung mit dem `For`-Attribut und einem Lambda-Ausdruck an, der die Modell Eigenschaft benennt:

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

Die Komponenten `ValidationMessage` und `ValidationSummary` unterstützen beliebige Attribute. Ein beliebiges Attribut, das nicht mit einem Komponenten Parameter identisch ist, wird dem generierten `<div>` oder `<ul>` Element hinzugefügt.

::: moniker range=">= aspnetcore-3.1"

**Paket "Microsoft. aspnetcore. blazor. DataAnnotations. Validation"**

[Microsoft. aspnetcore. blazor. DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) ist ein Paket, das die Lücken der Überprüfungs Möglichkeiten mithilfe der `DataAnnotationsValidator` Komponente füllt. Das Paket ist zurzeit *experimentell*, und wir planen, diese Szenarien in einer zukünftigen Version in das ASP.net Core Framework aufzunehmen.

Die `DataAnnotationsValidator` Komponente überprüft keine untergeordneten Eigenschaften komplexer Eigenschaften für ein Validierungs Modell. Elemente von Auflistungstyp Eigenschaften werden nicht überprüft. Zum Überprüfen dieser Typen führt das `Microsoft.AspNetCore.Blazor.DataAnnotations.Validation`-Paket das `ValidateComplexType` Validation-Attribut ein, das zusammen mit der `ObjectGraphDataAnnotationsValidator` Komponente funktioniert. Ein Beispiel für diese verwendeten Typen finden Sie im [GitHub-Repository für die blazor-Validierung ](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).

Die <xref:System.ComponentModel.DataAnnotations.CompareAttribute> funktioniert nicht gut mit der `DataAnnotationsValidator` Komponente. Das `Microsoft.AspNetCore.Blazor.DataAnnotations.Validation`-Paket führt ein zusätzliches Validierungs Attribut, `ComparePropertyAttribute`, ein, das diese Einschränkungen umgangen. In einer blazor-APP ist `ComparePropertyAttribute` ein direkter Ersatz für die `CompareAttribute`. Weitere Informationen finden Sie unter [compareattribute ignoriert mit onvalidsubmit EditForm (ASPNET/aspnetcore \#10643)](https://github.com/aspnet/AspNetCore/issues/10643#issuecomment-543909748).

::: moniker-end

::: moniker range="< aspnetcore-3.1"

### <a name="validation-of-complex-or-collection-type-properties"></a>Überprüfung komplexer Eigenschaften oder Auflistungstyp Eigenschaften

Validierungs Attribute, die auf die Eigenschaften eines Modells angewendet werden, überprüfen, wenn das Formular übermittelt wird. Die Eigenschaften von Auflistungen oder komplexen Datentypen eines Modells werden jedoch nicht bei der Formular Übermittlung durch die `DataAnnotationsValidator` Komponente überprüft. Verwenden Sie eine benutzerdefinierte Validierungs Komponente, um die in diesem Szenario überbten Attribute der Überprüfung zu berücksichtigen. Ein Beispiel finden Sie [im GitHub-Repository ASPNET/Samples im Beispiel zur blazor-Validierung](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).

::: moniker-end

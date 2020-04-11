---
title: Komponenten-Tag-Helfer in ASP.NET Core
author: guardrex
ms.author: riande
description: Erfahren Sie, wie Sie den ASP.NET Core Component Tag Helper verwenden, um Razor-Komponenten in Seiten und Ansichten zu rendern.
ms.custom: mvc
ms.date: 04/01/2020
no-loc:
- Blazor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 4a6b21229ce086099fcddfeb51c3a959ef639f24
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123429"
---
# <a name="component-tag-helper-in-aspnet-core"></a>Komponenten-Tag-Helfer in ASP.NET Core

Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

Um eine Komponente von einer Seite oder Ansicht zu rendern, verwenden Sie den [Komponenten-Tag-Hilfsfeld .](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper)

## <a name="prerequisites"></a>Voraussetzungen

Befolgen Sie die Anleitung im Abschnitt *Vorbereiten der App, um Komponenten in Seiten und Ansichten* des <xref:blazor/integrate-components#prepare-the-app-to-use-components-in-pages-and-views> Artikels zu verwenden.

## <a name="component-tag-helper"></a>Component Tag Helper

Der folgende Komponenten-Tag-Hilfsfeld wird die `Counter` Komponente auf einer Seite oder Ansicht rendern:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

Im vorherigen Beispiel `Counter` wird davon ausgegangen, dass sich die Komponente im Ordner *Pages* der App befindet.

Der Komponenten-Tag-Hilfssystem kann auch Parameter an Komponenten übergeben. Betrachten Sie `ColorfulCheckbox` die folgende Komponente, die die Farbe und Größe des Kontrollkästchens festlegt:

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying Blazor?
</label>

@code {
    [Parameter]
    public bool Value { get; set; }

    [Parameter]
    public int Size { get; set; } = 8;

    [Parameter]
    public string Color { get; set; }

    protected override void OnInitialized()
    {
        Size += 10;
    }
}
```

Die `Size` `int` [Komponentenparameter](xref:blazor/components#component-parameters) `string`( ) und `Color` ( ) können vom Component Tag Helper festgelegt werden:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

Im vorherigen Beispiel `ColorfulCheckbox` wird davon ausgegangen, dass sich die Komponente im *freigegebenen* Ordner der App befindet.

Der folgende HTML-Code wird auf der Seite oder Ansicht gerendert:

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

Das Übergeben einer zeichenfolgen Zeichenfolge erfordert `param-Color` einen [expliziten Razor-Ausdruck](xref:mvc/views/razor#explicit-razor-expressions), wie im vorherigen Beispiel gezeigt. Das Razor-Analyseverhalten `string` für einen Typwert gilt `param-*` nicht für ein `object` Attribut, da es sich bei dem Attribut um einen Typ handelt.

Der Parametertyp muss JSON serialisierbar sein, was in der Regel bedeutet, dass der Typ über einen Standardkonstruktor und settable-Eigenschaften verfügen muss. Sie können z. B. `Size` `Color` einen Wert für und `Size` im `Color` vorherigen Beispiel`int` `string`angeben, da die Typen von und die primitivetypen ( und ) vom JSON-Serialisierungsmodul unterstützt werden.

Im folgenden Beispiel wird ein Klassenobjekt an die Komponente übergeben:

*MyClass.cs*:

```csharp
public class MyClass
{
    public MyClass()
    {
    }

    public int MyInt { get; set; } = 999;
    public string MyString { get; set; } = "Initial value";
}
```

**Die Klasse muss über einen öffentlichen parameterlosen Konstruktor verfügen.**

*Shared/MyComponent.razor*:

```razor
<h2>MyComponent</h2>

<p>Int: @MyObject.MyInt</p>
<p>String: @MyObject.MyString</p>

@code
{
    [Parameter]
    public MyClass MyObject { get; set; }
}
```

*Seiten/MyPage.cshtml*:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}
@using {APP ASSEMBLY}.Shared

...

@{
    var myObject = new MyClass();
    myObject.MyInt = 7;
    myObject.MyString = "Set by MyPage";
}

<component type="typeof(MyComponent)" render-mode="ServerPrerendered" 
    param-MyObject="@myObject" />
```

Im vorherigen Beispiel `MyComponent` wird davon ausgegangen, dass sich die Komponente im *freigegebenen* Ordner der App befindet. `MyClass`sich im Namespace der`{APP ASSEMBLY}`App befindet ( ).

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>konfiguriert, ob die Komponente:

* Wird in die Seite vorgerendert.
* Wird als statischer HTML auf der Seite gerendert oder wenn es die erforderlichen Informationen enthält, um eine Blazor-App vom Benutzer-Agent zu booten.

| Rendermodus | BESCHREIBUNG |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Rendert die Komponente in statischen HTML Blazor und enthält eine Markierung für eine Server-App. Wenn der Benutzer-Agent gestartet wird, wird Blazor diese Markierung verwendet, um eine App zu booten. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Rendert eine Markierung Blazor für eine Server-App. Die Ausgabe der Komponente ist nicht enthalten. Wenn der Benutzer-Agent gestartet wird, wird Blazor diese Markierung verwendet, um eine App zu booten. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Rendert die Komponente in statischen HTML. |

Seiten und Ansichten können zwar Komponenten verwenden, aber das Gegenteil ist nicht wahr. Komponenten können keine ansichts- und seitenspezifischen Features wie Teilansichten und Abschnitte verwenden. Um Logik aus einer Teilansicht in einer Komponente zu verwenden, faktoridiere die partielle Ansichtslogik in eine Komponente.

Das Rendern von Serverkomponenten von einer statischen HTML-Seite wird nicht unterstützt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>

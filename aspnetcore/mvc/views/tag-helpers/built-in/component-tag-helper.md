---
title: Komponententaghilfsprogramm in ASP.net Core
author: guardrex
ms.author: riande
description: Erfahren Sie, wie Sie das taghilfsprogramm ASP.net Core Component zum Darstellen von Razor-Komponenten in Seiten und Ansichten verwenden.
ms.custom: mvc
ms.date: 03/18/2020
no-loc:
- Blazor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 801ceb73de5bb4ef7500624e1fbddbf96d1ab89c
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80226381"
---
# <a name="component-tag-helper-in-aspnet-core"></a>Komponententaghilfsprogramm in ASP.net Core

Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

Verwenden Sie das [komponententaghilfsprogramm](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper), um eine Komponente aus einer Seite oder Sicht zu Rendering.

Das folgende komponententaghilfsprogramm rendert die `Counter` Komponente in einer Seite oder Ansicht:

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

Mit dem komponententaghilfsprogramm können auch Parameter an Komponenten übergeben werden. Beachten Sie die folgende `ColorfulCheckbox` Komponente, mit der die Farbe und Größe der Kontrollkästchen Bezeichnung festgelegt wird:

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

Die [Komponenten Parameter](xref:blazor/components#component-parameters) `Size` (`int`) und `Color` (`string`) können vom komponententaghilfsprogramm festgelegt werden:

```cshtml
<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

Der folgende HTML-Code wird in der Seite oder Sicht gerendert:

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

Das übergeben einer Zeichenfolge in Anführungszeichen erfordert einen [expliziten Razor-Ausdruck](xref:mvc/views/razor#explicit-razor-expressions), wie für `param-Color` im vorherigen Beispiel gezeigt. Das Razor-erteilungsverhalten für einen `string` Type-Wert gilt nicht für ein `param-*` Attribut, da das Attribut ein `object` Typ ist.

Der Parametertyp muss mit JSON serialisierbar sein, in der Regel bedeutet das, dass der Typ über einen Standardkonstruktor und festlegbare Eigenschaften verfügen muss. Beispielsweise können Sie im vorhergehenden Beispiel einen Wert für `Size` und `Color` angeben, da es sich bei den Typen von `Size` und `Color` um primitive Typen (`int` und `string`) handelt, die vom JSON-Serialisierungsprogramm unterstützt werden.

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> konfiguriert folgende Einstellungen für die Komponente:

* Ob die Komponente zuvor für die Seite gerendert wird
* Ob die Komponente als statische HTML auf der Seite gerendert wird oder ob sie die nötigen Informationen für das Bootstrapping einer Blazor-App über den Benutzer-Agent enthält

| Rendermodus | BESCHREIBUNG |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Rendert die Komponente in statische HTML und fügt einen Marker für eine Blazor Server-App hinzu. Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Rendert einen Marker für eine Blazor Server-App. Die Ausgabe der Komponente ist nicht enthalten. Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Rendert die Komponente in statischen HTML-Code. |

Seiten und Ansichten können zwar Komponenten verwenden, doch das Gegenteil ist nicht der Fall. Komponenten können keine Ansichts-und Seiten spezifischen Funktionen verwenden, wie z. b. partielle Sichten und Abschnitte. Wenn Sie Logik aus einer partiellen Sicht in einer Komponente verwenden möchten, müssen Sie die partielle Sicht Logik in eine Komponente einbeziehen.

Das Rendern von Serverkomponenten über eine statische HTML-Seite wird nicht unterstützt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>

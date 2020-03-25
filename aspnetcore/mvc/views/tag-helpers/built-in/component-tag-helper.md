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
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="1cf5f-103">Komponententaghilfsprogramm in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="1cf5f-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="1cf5f-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1cf5f-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1cf5f-105">Verwenden Sie das [komponententaghilfsprogramm](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper), um eine Komponente aus einer Seite oder Sicht zu Rendering.</span><span class="sxs-lookup"><span data-stu-id="1cf5f-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

<span data-ttu-id="1cf5f-106">Das folgende komponententaghilfsprogramm rendert die `Counter` Komponente in einer Seite oder Ansicht:</span><span class="sxs-lookup"><span data-stu-id="1cf5f-106">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="1cf5f-107">Mit dem komponententaghilfsprogramm können auch Parameter an Komponenten übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="1cf5f-107">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="1cf5f-108">Beachten Sie die folgende `ColorfulCheckbox` Komponente, mit der die Farbe und Größe der Kontrollkästchen Bezeichnung festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="1cf5f-108">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

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

<span data-ttu-id="1cf5f-109">Die [Komponenten Parameter](xref:blazor/components#component-parameters) `Size` (`int`) und `Color` (`string`) können vom komponententaghilfsprogramm festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="1cf5f-109">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="1cf5f-110">Der folgende HTML-Code wird in der Seite oder Sicht gerendert:</span><span class="sxs-lookup"><span data-stu-id="1cf5f-110">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="1cf5f-111">Das übergeben einer Zeichenfolge in Anführungszeichen erfordert einen [expliziten Razor-Ausdruck](xref:mvc/views/razor#explicit-razor-expressions), wie für `param-Color` im vorherigen Beispiel gezeigt.</span><span class="sxs-lookup"><span data-stu-id="1cf5f-111">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="1cf5f-112">Das Razor-erteilungsverhalten für einen `string` Type-Wert gilt nicht für ein `param-*` Attribut, da das Attribut ein `object` Typ ist.</span><span class="sxs-lookup"><span data-stu-id="1cf5f-112">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="1cf5f-113">Der Parametertyp muss mit JSON serialisierbar sein, in der Regel bedeutet das, dass der Typ über einen Standardkonstruktor und festlegbare Eigenschaften verfügen muss.</span><span class="sxs-lookup"><span data-stu-id="1cf5f-113">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="1cf5f-114">Beispielsweise können Sie im vorhergehenden Beispiel einen Wert für `Size` und `Color` angeben, da es sich bei den Typen von `Size` und `Color` um primitive Typen (`int` und `string`) handelt, die vom JSON-Serialisierungsprogramm unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="1cf5f-114">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="1cf5f-115"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> konfiguriert folgende Einstellungen für die Komponente:</span><span class="sxs-lookup"><span data-stu-id="1cf5f-115"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="1cf5f-116">Ob die Komponente zuvor für die Seite gerendert wird</span><span class="sxs-lookup"><span data-stu-id="1cf5f-116">Is prerendered into the page.</span></span>
* <span data-ttu-id="1cf5f-117">Ob die Komponente als statische HTML auf der Seite gerendert wird oder ob sie die nötigen Informationen für das Bootstrapping einer Blazor-App über den Benutzer-Agent enthält</span><span class="sxs-lookup"><span data-stu-id="1cf5f-117">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="1cf5f-118">Rendermodus</span><span class="sxs-lookup"><span data-stu-id="1cf5f-118">Render Mode</span></span> | <span data-ttu-id="1cf5f-119">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="1cf5f-119">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="1cf5f-120">Rendert die Komponente in statische HTML und fügt einen Marker für eine Blazor Server-App hinzu.</span><span class="sxs-lookup"><span data-stu-id="1cf5f-120">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="1cf5f-121">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="1cf5f-121">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="1cf5f-122">Rendert einen Marker für eine Blazor Server-App.</span><span class="sxs-lookup"><span data-stu-id="1cf5f-122">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="1cf5f-123">Die Ausgabe der Komponente ist nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="1cf5f-123">Output from the component isn't included.</span></span> <span data-ttu-id="1cf5f-124">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="1cf5f-124">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="1cf5f-125">Rendert die Komponente in statischen HTML-Code.</span><span class="sxs-lookup"><span data-stu-id="1cf5f-125">Renders the component into static HTML.</span></span> |

<span data-ttu-id="1cf5f-126">Seiten und Ansichten können zwar Komponenten verwenden, doch das Gegenteil ist nicht der Fall.</span><span class="sxs-lookup"><span data-stu-id="1cf5f-126">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="1cf5f-127">Komponenten können keine Ansichts-und Seiten spezifischen Funktionen verwenden, wie z. b. partielle Sichten und Abschnitte.</span><span class="sxs-lookup"><span data-stu-id="1cf5f-127">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="1cf5f-128">Wenn Sie Logik aus einer partiellen Sicht in einer Komponente verwenden möchten, müssen Sie die partielle Sicht Logik in eine Komponente einbeziehen.</span><span class="sxs-lookup"><span data-stu-id="1cf5f-128">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="1cf5f-129">Das Rendern von Serverkomponenten über eine statische HTML-Seite wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="1cf5f-129">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1cf5f-130">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="1cf5f-130">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>

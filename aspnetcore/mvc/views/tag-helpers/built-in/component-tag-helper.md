---
title: Komponenten-Tag-Helfer in ASP.NET Core
author: guardrex
ms.author: riande
description: Erfahren Sie, wie Sie den ASP.NET Core Component Tag Helper verwenden, um Razor-Komponenten in Seiten und Ansichten zu rendern.
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: aaa4b92a8912b4f52d861ed07432aa7cf3ca5240
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440960"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="84337-103">Komponenten-Tag-Helfer in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="84337-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="84337-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="84337-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="84337-105">Um eine Komponente von einer Seite oder Ansicht zu rendern, verwenden Sie den [Komponenten-Tag-Hilfsfeld .](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper)</span><span class="sxs-lookup"><span data-stu-id="84337-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="84337-106">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="84337-106">Prerequisites</span></span>

<span data-ttu-id="84337-107">Befolgen Sie die Anleitung im Abschnitt *Vorbereiten der App, um Komponenten in Seiten und Ansichten* des <xref:blazor/integrate-components#prepare-the-app> Artikels zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="84337-107">Follow the guidance in the *Prepare the app to use components in pages and views* section of the <xref:blazor/integrate-components#prepare-the-app> article.</span></span>

## <a name="component-tag-helper"></a><span data-ttu-id="84337-108">Component Tag Helper</span><span class="sxs-lookup"><span data-stu-id="84337-108">Component Tag Helper</span></span>

<span data-ttu-id="84337-109">Der folgende Komponenten-Tag-Hilfsfeld wird die `Counter` Komponente auf einer Seite oder Ansicht rendern:</span><span class="sxs-lookup"><span data-stu-id="84337-109">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="84337-110">Im vorherigen Beispiel `Counter` wird davon ausgegangen, dass sich die Komponente im Ordner *Pages* der App befindet.</span><span class="sxs-lookup"><span data-stu-id="84337-110">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span>

<span data-ttu-id="84337-111">Der Komponenten-Tag-Hilfssystem kann auch Parameter an Komponenten übergeben.</span><span class="sxs-lookup"><span data-stu-id="84337-111">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="84337-112">Betrachten Sie `ColorfulCheckbox` die folgende Komponente, die die Farbe und Größe des Kontrollkästchens festlegt:</span><span class="sxs-lookup"><span data-stu-id="84337-112">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

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

<span data-ttu-id="84337-113">Die `Size` `int` [Komponentenparameter](xref:blazor/components#component-parameters) `string`( ) und `Color` ( ) können vom Component Tag Helper festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="84337-113">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="84337-114">Im vorherigen Beispiel `ColorfulCheckbox` wird davon ausgegangen, dass sich die Komponente im *freigegebenen* Ordner der App befindet.</span><span class="sxs-lookup"><span data-stu-id="84337-114">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span>

<span data-ttu-id="84337-115">Der folgende HTML-Code wird auf der Seite oder Ansicht gerendert:</span><span class="sxs-lookup"><span data-stu-id="84337-115">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="84337-116">Das Übergeben einer zeichenfolgen Zeichenfolge erfordert `param-Color` einen [expliziten Razor-Ausdruck](xref:mvc/views/razor#explicit-razor-expressions), wie im vorherigen Beispiel gezeigt.</span><span class="sxs-lookup"><span data-stu-id="84337-116">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="84337-117">Das Razor-Analyseverhalten `string` für einen Typwert gilt `param-*` nicht für ein `object` Attribut, da es sich bei dem Attribut um einen Typ handelt.</span><span class="sxs-lookup"><span data-stu-id="84337-117">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="84337-118">Der Parametertyp muss JSON serialisierbar sein, was in der Regel bedeutet, dass der Typ über einen Standardkonstruktor und settable-Eigenschaften verfügen muss.</span><span class="sxs-lookup"><span data-stu-id="84337-118">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="84337-119">Sie können z. B. `Size` `Color` einen Wert für und `Size` im `Color` vorherigen Beispiel`int` `string`angeben, da die Typen von und die primitivetypen ( und ) vom JSON-Serialisierungsmodul unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="84337-119">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="84337-120">Im folgenden Beispiel wird ein Klassenobjekt an die Komponente übergeben:</span><span class="sxs-lookup"><span data-stu-id="84337-120">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="84337-121">*MyClass.cs*:</span><span class="sxs-lookup"><span data-stu-id="84337-121">*MyClass.cs*:</span></span>

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

<span data-ttu-id="84337-122">**Die Klasse muss über einen öffentlichen parameterlosen Konstruktor verfügen.**</span><span class="sxs-lookup"><span data-stu-id="84337-122">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="84337-123">*Shared/MyComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="84337-123">*Shared/MyComponent.razor*:</span></span>

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

<span data-ttu-id="84337-124">*Seiten/MyPage.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="84337-124">*Pages/MyPage.cshtml*:</span></span>

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

<span data-ttu-id="84337-125">Im vorherigen Beispiel `MyComponent` wird davon ausgegangen, dass sich die Komponente im *freigegebenen* Ordner der App befindet.</span><span class="sxs-lookup"><span data-stu-id="84337-125">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="84337-126">`MyClass`sich im Namespace der`{APP ASSEMBLY}`App befindet ( ).</span><span class="sxs-lookup"><span data-stu-id="84337-126">`MyClass` is in the app's namespace (`{APP ASSEMBLY}`).</span></span>

<span data-ttu-id="84337-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>konfiguriert, ob die Komponente:</span><span class="sxs-lookup"><span data-stu-id="84337-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="84337-128">Wird in die Seite vorgerendert.</span><span class="sxs-lookup"><span data-stu-id="84337-128">Is prerendered into the page.</span></span>
* <span data-ttu-id="84337-129">Wird als statischer HTML auf der Seite gerendert oder wenn es die erforderlichen Informationen enthält, um eine Blazor-App vom Benutzer-Agent zu booten.</span><span class="sxs-lookup"><span data-stu-id="84337-129">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="84337-130">Rendermodus</span><span class="sxs-lookup"><span data-stu-id="84337-130">Render Mode</span></span> | <span data-ttu-id="84337-131">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="84337-131">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="84337-132">Rendert die Komponente in statischen HTML Blazor und enthält eine Markierung für eine Server-App.</span><span class="sxs-lookup"><span data-stu-id="84337-132">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="84337-133">Wenn der Benutzer-Agent gestartet wird, wird Blazor diese Markierung verwendet, um eine App zu booten.</span><span class="sxs-lookup"><span data-stu-id="84337-133">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="84337-134">Rendert eine Markierung Blazor für eine Server-App.</span><span class="sxs-lookup"><span data-stu-id="84337-134">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="84337-135">Die Ausgabe der Komponente ist nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="84337-135">Output from the component isn't included.</span></span> <span data-ttu-id="84337-136">Wenn der Benutzer-Agent gestartet wird, wird Blazor diese Markierung verwendet, um eine App zu booten.</span><span class="sxs-lookup"><span data-stu-id="84337-136">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="84337-137">Rendert die Komponente in statischen HTML.</span><span class="sxs-lookup"><span data-stu-id="84337-137">Renders the component into static HTML.</span></span> |

<span data-ttu-id="84337-138">Seiten und Ansichten können zwar Komponenten verwenden, aber das Gegenteil ist nicht wahr.</span><span class="sxs-lookup"><span data-stu-id="84337-138">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="84337-139">Komponenten können keine ansichts- und seitenspezifischen Features wie Teilansichten und Abschnitte verwenden.</span><span class="sxs-lookup"><span data-stu-id="84337-139">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="84337-140">Um Logik aus einer Teilansicht in einer Komponente zu verwenden, faktoridiere die partielle Ansichtslogik in eine Komponente.</span><span class="sxs-lookup"><span data-stu-id="84337-140">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="84337-141">Das Rendern von Serverkomponenten von einer statischen HTML-Seite wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="84337-141">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="84337-142">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="84337-142">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>

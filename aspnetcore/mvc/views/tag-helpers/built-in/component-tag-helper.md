---
title: Komponententaghilfsprogramm in ASP.net Core
author: guardrex
ms.author: riande
description: Erfahren Sie, wie Sie das taghilfsprogramm ASP.net Core Component Razor zum Rendering von Komponenten in Seiten und Ansichten verwenden.
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 4e003e5ed5e7863d8a218c0f02bb37e214e31910
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773928"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="eb25f-103">Komponententaghilfsprogramm in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="eb25f-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="eb25f-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="eb25f-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="eb25f-105">Verwenden Sie das [Komponententaghilfsprogramm](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) zum Rendern einer Komponente einer Seite oder Ansicht.</span><span class="sxs-lookup"><span data-stu-id="eb25f-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="eb25f-106">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="eb25f-106">Prerequisites</span></span>

<span data-ttu-id="eb25f-107">Befolgen Sie die Anweisungen im Abschnitt *Vorbereiten der APP für die Verwendung von Komponenten im Bereich "Seiten und Ansichten* " des <xref:blazor/integrate-components#prepare-the-app> Artikels.</span><span class="sxs-lookup"><span data-stu-id="eb25f-107">Follow the guidance in the *Prepare the app to use components in pages and views* section of the <xref:blazor/integrate-components#prepare-the-app> article.</span></span>

## <a name="component-tag-helper"></a><span data-ttu-id="eb25f-108">Komponententaghilfsprogramm</span><span class="sxs-lookup"><span data-stu-id="eb25f-108">Component Tag Helper</span></span>

<span data-ttu-id="eb25f-109">Das folgende komponententaghilfsprogramm `Counter` rendert die Komponente in einer Seite oder Ansicht:</span><span class="sxs-lookup"><span data-stu-id="eb25f-109">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="eb25f-110">Im vorangehenden Beispiel wird davon `Counter` ausgegangen, dass sich die Komponente im *Seiten* Ordner der APP befindet.</span><span class="sxs-lookup"><span data-stu-id="eb25f-110">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span>

<span data-ttu-id="eb25f-111">Mit dem komponententaghilfsprogramm können auch Parameter an Komponenten übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="eb25f-111">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="eb25f-112">Sehen Sie sich `ColorfulCheckbox` die folgende Komponente an, mit der die Farbe und Größe der Kontrollkästchen Bezeichnung festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="eb25f-112">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

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

<span data-ttu-id="eb25f-113">Die `Size` `int` [Komponenten Parameter](xref:blazor/components#component-parameters) ( `Color` )`string`und () können vom komponententaghilfsprogramm festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="eb25f-113">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="eb25f-114">Im vorangehenden Beispiel wird davon `ColorfulCheckbox` ausgegangen, dass sich die Komponente im frei *gegebenen* Ordner der APP befindet.</span><span class="sxs-lookup"><span data-stu-id="eb25f-114">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span>

<span data-ttu-id="eb25f-115">Der folgende HTML-Code wird in der Seite oder Sicht gerendert:</span><span class="sxs-lookup"><span data-stu-id="eb25f-115">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="eb25f-116">Das übergeben einer Zeichenfolge in Anführungszeichen erfordert einen [expliziten Razor-Ausdruck](xref:mvc/views/razor#explicit-razor-expressions), wie `param-Color` im vorherigen Beispiel gezeigt.</span><span class="sxs-lookup"><span data-stu-id="eb25f-116">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="eb25f-117">Das Razor-Verarbeitungs Verhalten für einen `string` Typwert gilt nicht für ein `param-*` -Attribut, da das- `object` Attribut ein-Typ ist.</span><span class="sxs-lookup"><span data-stu-id="eb25f-117">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="eb25f-118">Der Parametertyp muss JSON-serialisierbar sein. Dies bedeutet in der Regel, dass der Typ einen Standardkonstruktor und festleg bare Eigenschaften aufweisen muss.</span><span class="sxs-lookup"><span data-stu-id="eb25f-118">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="eb25f-119">Beispielsweise können Sie einen `Size` Wert für und `Color` im vorangehenden Beispiel angeben, da die Typen von `Size` und `Color` primitive Typen (`int` und `string`) sind, die vom JSON-Serialisierungsprogramm unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="eb25f-119">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="eb25f-120">Im folgenden Beispiel wird ein-Klassenobjekt an die-Komponente übermittelt:</span><span class="sxs-lookup"><span data-stu-id="eb25f-120">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="eb25f-121">*MyClass.cs*:</span><span class="sxs-lookup"><span data-stu-id="eb25f-121">*MyClass.cs*:</span></span>

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

<span data-ttu-id="eb25f-122">**Die Klasse muss über einen öffentlichen Parameter losen Konstruktor verfügen.**</span><span class="sxs-lookup"><span data-stu-id="eb25f-122">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="eb25f-123">*Shared/MyComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="eb25f-123">*Shared/MyComponent.razor*:</span></span>

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

<span data-ttu-id="eb25f-124">*Pages/mypage. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="eb25f-124">*Pages/MyPage.cshtml*:</span></span>

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

<span data-ttu-id="eb25f-125">Im vorangehenden Beispiel wird davon `MyComponent` ausgegangen, dass sich die Komponente im frei *gegebenen* Ordner der APP befindet.</span><span class="sxs-lookup"><span data-stu-id="eb25f-125">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="eb25f-126">`MyClass`befindet sich im-Namespace der APP`{APP ASSEMBLY}`().</span><span class="sxs-lookup"><span data-stu-id="eb25f-126">`MyClass` is in the app's namespace (`{APP ASSEMBLY}`).</span></span>

<span data-ttu-id="eb25f-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> konfiguriert folgende Einstellungen für die Komponente:</span><span class="sxs-lookup"><span data-stu-id="eb25f-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="eb25f-128">Ob die Komponente zuvor für die Seite gerendert wird</span><span class="sxs-lookup"><span data-stu-id="eb25f-128">Is prerendered into the page.</span></span>
* <span data-ttu-id="eb25f-129">Ob die Komponente als statische HTML auf der Seite gerendert wird oder ob sie die nötigen Informationen für das Bootstrapping einer Blazor-App über den Benutzer-Agent enthält</span><span class="sxs-lookup"><span data-stu-id="eb25f-129">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="eb25f-130">Rendermodus</span><span class="sxs-lookup"><span data-stu-id="eb25f-130">Render Mode</span></span> | <span data-ttu-id="eb25f-131">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="eb25f-131">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="eb25f-132">Rendert die Komponente in statische HTML und fügt einen Marker für eine Blazor Server-App hinzu.</span><span class="sxs-lookup"><span data-stu-id="eb25f-132">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="eb25f-133">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="eb25f-133">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="eb25f-134">Rendert einen Marker für eine Blazor Server-App.</span><span class="sxs-lookup"><span data-stu-id="eb25f-134">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="eb25f-135">Die Ausgabe der Komponente ist nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="eb25f-135">Output from the component isn't included.</span></span> <span data-ttu-id="eb25f-136">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="eb25f-136">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="eb25f-137">Rendert die Komponente in statischen HTML-Code.</span><span class="sxs-lookup"><span data-stu-id="eb25f-137">Renders the component into static HTML.</span></span> |

<span data-ttu-id="eb25f-138">Während Seiten und Ansichten Komponenten verwenden können, ist das Gegenteil nicht der Fall.</span><span class="sxs-lookup"><span data-stu-id="eb25f-138">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="eb25f-139">Komponenten können keine Ansichts-und Seiten spezifischen Funktionen verwenden, wie z. b. partielle Sichten und Abschnitte.</span><span class="sxs-lookup"><span data-stu-id="eb25f-139">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="eb25f-140">Wenn Sie Logik aus einer partiellen Sicht in einer Komponente verwenden möchten, müssen Sie die partielle Sicht Logik in eine Komponente einbeziehen.</span><span class="sxs-lookup"><span data-stu-id="eb25f-140">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="eb25f-141">Das Rendern von Serverkomponenten über eine statische HTML-Seite wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="eb25f-141">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eb25f-142">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="eb25f-142">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>

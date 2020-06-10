---
title: "title: 'ASP.NET Core-Komponentenvorlagen in Blazor' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:"
author: guardrex
description: "'Blazor'"
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templated-components
ms.openlocfilehash: 8c970387fa79b02127c8a2375195373148749679
ms.sourcegitcommit: 04eeffe9f2f07aaa9e04720adb2e37245f5ce17d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/26/2020
ms.locfileid: "83851888"
---
# <a name="aspnet-core-blazor-templated-components"></a><span data-ttu-id="59a2a-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="59a2a-103">'Identity'</span></span>

<span data-ttu-id="59a2a-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="59a2a-104">'Let's Encrypt'</span></span>

<span data-ttu-id="59a2a-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="59a2a-105">'Razor'</span></span> <span data-ttu-id="59a2a-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="59a2a-106">'SignalR' uid:</span></span> <span data-ttu-id="59a2a-107">ASP.NET Core-Komponentenvorlagen in Blazor</span><span class="sxs-lookup"><span data-stu-id="59a2a-107">ASP.NET Core Blazor templated components</span></span>

* <span data-ttu-id="59a2a-108">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="59a2a-108">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>
* <span data-ttu-id="59a2a-109">Bei Komponentenvorlagen geht es um Komponenten, die eine oder mehrere Benutzeroberflächenvorlagen als Parameter akzeptieren, die dann wiederum als Teil der Renderlogik der Komponente verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="59a2a-109">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span>

## <a name="template-parameters"></a><span data-ttu-id="59a2a-110">Mit Komponentenvorlagen können Sie Komponenten höherer Ebenen erstellen, die einfacher wiederverwendet werden können als reguläre Komponenten.</span><span class="sxs-lookup"><span data-stu-id="59a2a-110">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span>

<span data-ttu-id="59a2a-111">Nachstehend sind einige Beispiele angegeben:</span><span class="sxs-lookup"><span data-stu-id="59a2a-111">A couple of examples include:</span></span> <span data-ttu-id="59a2a-112">Eine Tabellenkomponente, mit der ein Benutzer Vorlagen für die Überschrift, die Zeilen und die Fußzeile einer Tabelle angeben kann</span><span class="sxs-lookup"><span data-stu-id="59a2a-112">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span> <span data-ttu-id="59a2a-113">Eine Listenkomponente, mit der ein Benutzer eine Vorlage für Renderelemente in einer Liste angeben kann</span><span class="sxs-lookup"><span data-stu-id="59a2a-113">A list component that allows a user to specify a template for rendering items in a list.</span></span>

<span data-ttu-id="59a2a-114">Vorlagenparameter</span><span class="sxs-lookup"><span data-stu-id="59a2a-114">Template parameters</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="59a2a-115">Eine Komponentenvorlage wird definiert, indem mindestens ein Komponentenparameter des Typs <xref:Microsoft.AspNetCore.Components.RenderFragment> oder <xref:Microsoft.AspNetCore.Components.RenderFragment%601> angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="59a2a-115">A templated component is defined by specifying one or more component parameters of type <xref:Microsoft.AspNetCore.Components.RenderFragment> or <xref:Microsoft.AspNetCore.Components.RenderFragment%601>.</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

> [!NOTE]
> <span data-ttu-id="59a2a-116">Ein Renderfragment stellt dabei ein Segment der Benutzeroberfläche dar, das gerendert werden soll.</span><span class="sxs-lookup"><span data-stu-id="59a2a-116">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="59a2a-117"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> akzeptiert einen Typparameter, der angegeben werden kann, wenn das Renderfragment aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="59a2a-117"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> takes a type parameter that can be specified when the render fragment is invoked.</span></span>

## <a name="template-context-parameters"></a><span data-ttu-id="59a2a-118">`TableTemplate`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="59a2a-118">`TableTemplate` component:</span></span>

<span data-ttu-id="59a2a-119">Bei der Verwendung einer Komponentenvorlage werden die Vorlagenparameter mithilfe eines untergeordneten Elements angegeben, das mit den Namen der Parameter übereinstimmt (`TableHeader` und `RowTemplate` im folgenden Beispiel):</span><span class="sxs-lookup"><span data-stu-id="59a2a-119">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span> <span data-ttu-id="59a2a-120">Generische Typeinschränkungen werden in einem zukünftigen Release unterstützt.</span><span class="sxs-lookup"><span data-stu-id="59a2a-120">Generic type constraints will be supported in a future release.</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

<span data-ttu-id="59a2a-121">Weitere Informationen finden Sie unter [Zulassen generischer Typeinschränkungen (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span><span class="sxs-lookup"><span data-stu-id="59a2a-121">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span> <span data-ttu-id="59a2a-122">Vorlagenkontextparameter</span><span class="sxs-lookup"><span data-stu-id="59a2a-122">Template context parameters</span></span> <span data-ttu-id="59a2a-123">Komponentenargument des Typs <xref:Microsoft.AspNetCore.Components.RenderFragment%601>, die als Elemente übergeben werden, verfügen über einen impliziten Parameter namens `context` (beispielsweise `@context.PetId` im vorherigen Codebeispiel). Sie können den Parameternamen jedoch mithilfe des `Context`-Attributs für das untergeordnete Element ändern.</span><span class="sxs-lookup"><span data-stu-id="59a2a-123">Component arguments of type <xref:Microsoft.AspNetCore.Components.RenderFragment%601> passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="59a2a-124">Im folgenden Beispiel gibt das `Context`-Attribut des `RowTemplate`-Elements den `pet`-Parameter an:</span><span class="sxs-lookup"><span data-stu-id="59a2a-124">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```razor
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

## <a name="generic-typed-components"></a><span data-ttu-id="59a2a-125">Alternativ können Sie das `Context`-Attribut im Komponentenelement angeben.</span><span class="sxs-lookup"><span data-stu-id="59a2a-125">Alternatively, you can specify the `Context` attribute on the component element.</span></span>

<span data-ttu-id="59a2a-126">Das angegebene `Context`-Attribut gilt für alle angegebenen Vorlagenparameter.</span><span class="sxs-lookup"><span data-stu-id="59a2a-126">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="59a2a-127">Dies ist hilfreich, wenn Sie den Inhaltsparametername für einen impliziten untergeordneten Inhalt angeben möchten, ohne das untergeordnete Elemente umschließen zu müssen.</span><span class="sxs-lookup"><span data-stu-id="59a2a-127">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="59a2a-128">Im folgenden Beispiel wird das `Context`-Attribut auf dem `TableTemplate`-Element angezeigt und gilt für alle Vorlagenparameter:</span><span class="sxs-lookup"><span data-stu-id="59a2a-128">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="59a2a-129">Komponenten mit generischem Typ</span><span class="sxs-lookup"><span data-stu-id="59a2a-129">Generic-typed components</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="59a2a-130">Komponentenvorlagen haben oft einen generischen Typ.</span><span class="sxs-lookup"><span data-stu-id="59a2a-130">Templated components are often generically typed.</span></span> <span data-ttu-id="59a2a-131">Eine generische `ListViewTemplate`-Komponenten kann beispielsweise verwendet werden, um `IEnumerable<T>`-Werte zu rendern.</span><span class="sxs-lookup"><span data-stu-id="59a2a-131">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

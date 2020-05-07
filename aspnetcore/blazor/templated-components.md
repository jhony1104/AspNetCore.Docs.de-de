---
title: ASP.NET Core-Komponentenvorlagen in Blazor
author: guardrex
description: Hier erfahren Sie, wie Komponentenvorlagen eine oder mehrere Benutzeroberflächenvorlagen als Parameter akzeptieren können, die dann wiederum als Teil der Renderlogik der Komponente verwendet werden können.
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
ms.openlocfilehash: de603d3520c124b278312e5167a2f8bad14cf6e9
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771066"
---
# <a name="aspnet-core-blazor-templated-components"></a><span data-ttu-id="44ca1-103">ASP.NET Core-Komponentenvorlagen in Blazor</span><span class="sxs-lookup"><span data-stu-id="44ca1-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="44ca1-104">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="44ca1-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="44ca1-105">Bei Komponentenvorlagen geht es um Komponenten, die eine oder mehrere Benutzeroberflächenvorlagen als Parameter akzeptieren, die dann wiederum als Teil der Renderlogik der Komponente verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="44ca1-105">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="44ca1-106">Mit Komponentenvorlagen können Sie Komponenten höherer Ebenen erstellen, die einfacher wiederverwendet werden können als reguläre Komponenten.</span><span class="sxs-lookup"><span data-stu-id="44ca1-106">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="44ca1-107">Nachstehend sind einige Beispiele angegeben:</span><span class="sxs-lookup"><span data-stu-id="44ca1-107">A couple of examples include:</span></span>

* <span data-ttu-id="44ca1-108">Eine Tabellenkomponente, mit der ein Benutzer Vorlagen für die Überschrift, die Zeilen und die Fußzeile einer Tabelle angeben kann</span><span class="sxs-lookup"><span data-stu-id="44ca1-108">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="44ca1-109">Eine Listenkomponente, mit der ein Benutzer eine Vorlage für Renderelemente in einer Liste angeben kann</span><span class="sxs-lookup"><span data-stu-id="44ca1-109">A list component that allows a user to specify a template for rendering items in a list.</span></span>

## <a name="template-parameters"></a><span data-ttu-id="44ca1-110">Vorlagenparameter</span><span class="sxs-lookup"><span data-stu-id="44ca1-110">Template parameters</span></span>

<span data-ttu-id="44ca1-111">Eine Komponentenvorlage wird definiert, indem mindestens ein Komponentenparameter des Typs `RenderFragment` oder `RenderFragment<T>` angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="44ca1-111">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="44ca1-112">Ein Renderfragment stellt dabei ein Segment der Benutzeroberfläche dar, das gerendert werden soll.</span><span class="sxs-lookup"><span data-stu-id="44ca1-112">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="44ca1-113">`RenderFragment<T>` akzeptiert einen Typparameter, der angegeben werden kann, wenn das Renderfragment aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="44ca1-113">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="44ca1-114">`TableTemplate`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="44ca1-114">`TableTemplate` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="44ca1-115">Bei der Verwendung einer Komponentenvorlage werden die Vorlagenparameter mithilfe eines untergeordneten Elements angegeben, das mit den Namen der Parameter übereinstimmt (`TableHeader` und `RowTemplate` im folgenden Beispiel):</span><span class="sxs-lookup"><span data-stu-id="44ca1-115">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

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
> <span data-ttu-id="44ca1-116">Generische Typeinschränkungen werden in einem zukünftigen Release unterstützt.</span><span class="sxs-lookup"><span data-stu-id="44ca1-116">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="44ca1-117">Weitere Informationen finden Sie unter [Zulassen generischer Typeinschränkungen (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span><span class="sxs-lookup"><span data-stu-id="44ca1-117">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="template-context-parameters"></a><span data-ttu-id="44ca1-118">Vorlagenkontextparameter</span><span class="sxs-lookup"><span data-stu-id="44ca1-118">Template context parameters</span></span>

<span data-ttu-id="44ca1-119">Komponentenargument des Typs `RenderFragment<T>`, die als Elemente übergeben werden, verfügen über einen impliziten Parameter namens `context` (beispielsweise `@context.PetId` im vorherigen Codebeispiel). Sie können den Parameternamen jedoch mithilfe des `Context`-Attributs für das untergeordnete Element ändern.</span><span class="sxs-lookup"><span data-stu-id="44ca1-119">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="44ca1-120">Im folgenden Beispiel gibt das `RowTemplate`-Attribut des `Context`-Elements den `pet`-Parameter an:</span><span class="sxs-lookup"><span data-stu-id="44ca1-120">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

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

<span data-ttu-id="44ca1-121">Alternativ können Sie das `Context`-Attribut im Komponentenelement angeben.</span><span class="sxs-lookup"><span data-stu-id="44ca1-121">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="44ca1-122">Das angegebene `Context`-Attribut gilt für alle angegebenen Vorlagenparameter.</span><span class="sxs-lookup"><span data-stu-id="44ca1-122">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="44ca1-123">Dies ist hilfreich, wenn Sie den Inhaltsparametername für einen impliziten untergeordneten Inhalt angeben möchten, ohne das untergeordnete Elemente umschließen zu müssen.</span><span class="sxs-lookup"><span data-stu-id="44ca1-123">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="44ca1-124">Im folgenden Beispiel wird das `Context`-Attribut auf dem `TableTemplate`-Element angezeigt und gilt für alle Vorlagenparameter:</span><span class="sxs-lookup"><span data-stu-id="44ca1-124">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

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

## <a name="generic-typed-components"></a><span data-ttu-id="44ca1-125">Komponenten mit generischem Typ</span><span class="sxs-lookup"><span data-stu-id="44ca1-125">Generic-typed components</span></span>

<span data-ttu-id="44ca1-126">Komponentenvorlagen haben oft einen generischen Typ.</span><span class="sxs-lookup"><span data-stu-id="44ca1-126">Templated components are often generically typed.</span></span> <span data-ttu-id="44ca1-127">Eine generische `ListViewTemplate`-Komponenten kann beispielsweise verwendet werden, um `IEnumerable<T>`-Werte zu rendern.</span><span class="sxs-lookup"><span data-stu-id="44ca1-127">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="44ca1-128">Verwenden Sie die [`@typeparam`](xref:mvc/views/razor#typeparam)-Direktive, um Typenparameter anzugeben, um eine generische Komponente zu definieren:</span><span class="sxs-lookup"><span data-stu-id="44ca1-128">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="44ca1-129">Bei der Verwendung einer Komponente mit generischem Typ, wird der Typparameter wenn möglich abgeleitet:</span><span class="sxs-lookup"><span data-stu-id="44ca1-129">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="44ca1-130">Andernfalls muss der Typparameter explizit mithilfe eines Attributs angegeben werden, der mit dem Namen des Typparameters übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="44ca1-130">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="44ca1-131">Im folgenden Beispiel wird der Typ von `TItem="Pet"` angegeben:</span><span class="sxs-lookup"><span data-stu-id="44ca1-131">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

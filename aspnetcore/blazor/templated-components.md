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
# <a name="aspnet-core-blazor-templated-components"></a>'Identity'

'Let's Encrypt'

'Razor' 'SignalR' uid: ASP.NET Core-Komponentenvorlagen in Blazor

* Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)
* Bei Komponentenvorlagen geht es um Komponenten, die eine oder mehrere Benutzeroberflächenvorlagen als Parameter akzeptieren, die dann wiederum als Teil der Renderlogik der Komponente verwendet werden können.

## <a name="template-parameters"></a>Mit Komponentenvorlagen können Sie Komponenten höherer Ebenen erstellen, die einfacher wiederverwendet werden können als reguläre Komponenten.

Nachstehend sind einige Beispiele angegeben: Eine Tabellenkomponente, mit der ein Benutzer Vorlagen für die Überschrift, die Zeilen und die Fußzeile einer Tabelle angeben kann Eine Listenkomponente, mit der ein Benutzer eine Vorlage für Renderelemente in einer Liste angeben kann

Vorlagenparameter

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

Eine Komponentenvorlage wird definiert, indem mindestens ein Komponentenparameter des Typs <xref:Microsoft.AspNetCore.Components.RenderFragment> oder <xref:Microsoft.AspNetCore.Components.RenderFragment%601> angegeben wird.

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
> Ein Renderfragment stellt dabei ein Segment der Benutzeroberfläche dar, das gerendert werden soll. <xref:Microsoft.AspNetCore.Components.RenderFragment%601> akzeptiert einen Typparameter, der angegeben werden kann, wenn das Renderfragment aufgerufen wird.

## <a name="template-context-parameters"></a>`TableTemplate`-Komponente:

Bei der Verwendung einer Komponentenvorlage werden die Vorlagenparameter mithilfe eines untergeordneten Elements angegeben, das mit den Namen der Parameter übereinstimmt (`TableHeader` und `RowTemplate` im folgenden Beispiel): Generische Typeinschränkungen werden in einem zukünftigen Release unterstützt.

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

Weitere Informationen finden Sie unter [Zulassen generischer Typeinschränkungen (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433). Vorlagenkontextparameter Komponentenargument des Typs <xref:Microsoft.AspNetCore.Components.RenderFragment%601>, die als Elemente übergeben werden, verfügen über einen impliziten Parameter namens `context` (beispielsweise `@context.PetId` im vorherigen Codebeispiel). Sie können den Parameternamen jedoch mithilfe des `Context`-Attributs für das untergeordnete Element ändern. Im folgenden Beispiel gibt das `Context`-Attribut des `RowTemplate`-Elements den `pet`-Parameter an:

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

## <a name="generic-typed-components"></a>Alternativ können Sie das `Context`-Attribut im Komponentenelement angeben.

Das angegebene `Context`-Attribut gilt für alle angegebenen Vorlagenparameter. Dies ist hilfreich, wenn Sie den Inhaltsparametername für einen impliziten untergeordneten Inhalt angeben möchten, ohne das untergeordnete Elemente umschließen zu müssen. Im folgenden Beispiel wird das `Context`-Attribut auf dem `TableTemplate`-Element angezeigt und gilt für alle Vorlagenparameter:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

Komponenten mit generischem Typ

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

Komponentenvorlagen haben oft einen generischen Typ. Eine generische `ListViewTemplate`-Komponenten kann beispielsweise verwendet werden, um `IEnumerable<T>`-Werte zu rendern.

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

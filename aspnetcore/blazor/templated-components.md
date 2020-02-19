---
title: ASP.net Core Blazor Vorlagen Komponenten
author: guardrex
description: Erfahren Sie, wie vorlagenbasierte Komponenten eine oder mehrere UI-Vorlagen als Parameter akzeptieren können, die dann als Teil der Renderinglogik der Komponente verwendet werden können.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templated-components
ms.openlocfilehash: b64d6a731e540b13c50b2c6108f75efd0ac9290c
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453151"
---
# <a name="aspnet-core-opno-locblazor-templated-components"></a>ASP.net Core Blazor Vorlagen Komponenten

Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)

Vorlagen Komponenten sind Komponenten, die eine oder mehrere Benutzeroberflächen Vorlagen als Parameter akzeptieren, die dann als Teil der Renderinglogik der Komponente verwendet werden können. Mit Vorlagen basierten Komponenten können Sie Komponenten höherer Ebene erstellen, die besser wiederverwendbar als reguläre Komponenten sind. Einige Beispiele sind:

* Eine Tabellenkomponente, mit der Benutzervorlagen für den Header, die Zeilen und die Fußzeile der Tabelle angeben können.
* Eine Listen Komponente, die es Benutzern ermöglicht, eine Vorlage zum Rendern von Elementen in einer Liste anzugeben.

## <a name="template-parameters"></a>Vorlagenparameter

Eine auf Vorlagen basierende Komponente wird durch Angabe eines oder mehrerer Komponenten Parameter vom Typ `RenderFragment` oder `RenderFragment<T>`definiert. Ein Rendering-Fragment stellt ein Segment der zu Rendering enden Benutzeroberfläche dar. `RenderFragment<T>` nimmt einen Typparameter an, der angegeben werden kann, wenn das Rendering-Fragment aufgerufen wird.

`TableTemplate` Komponente:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

Wenn Sie eine auf Vorlagen basierende Komponente verwenden, können die Vorlagen Parameter mit untergeordneten Elementen angegeben werden, die den Namen der Parameter (`TableHeader` und `RowTemplate` im folgenden Beispiel) entsprechen:

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

## <a name="template-context-parameters"></a>Vorlagen Kontext Parameter

Komponenten Argumente vom Typ `RenderFragment<T>` als Elemente übergeben, haben einen impliziten Parameter mit dem Namen `context` (z. b. aus dem vorangehenden Codebeispiel `@context.PetId`), Sie können jedoch den Parameternamen mithilfe des `Context`-Attributs für das untergeordnete Element ändern. Im folgenden Beispiel gibt das `Context`-Attribut des `RowTemplate` Elements den `pet`-Parameter an:

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

Alternativ können Sie das `Context`-Attribut für das Component-Element angeben. Das angegebene `Context` Attribut gilt für alle angegebenen Vorlagen Parameter. Dies kann hilfreich sein, wenn Sie den Inhalts Parameternamen für impliziten untergeordneten Inhalt angeben möchten (ohne ein untergeordnetes Wrapping Element). Im folgenden Beispiel wird das `Context`-Attribut im `TableTemplate`-Element angezeigt und gilt für alle Vorlagen Parameter:

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

## <a name="generic-typed-components"></a>Generisch typisierte Komponenten

Auf Vorlagen basierende Komponenten werden oft generisch typisiert. Beispielsweise kann eine generische `ListViewTemplate` Komponente zum Rendering `IEnumerable<T>` Werte verwendet werden. Verwenden Sie zum Definieren einer generischen Komponente die [`@typeparam`](xref:mvc/views/razor#typeparam) -Direktive, um Typparameter anzugeben:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

Wenn Sie generische typisierte Komponenten verwenden, wird der Typparameter nach Möglichkeit abgeleitet:

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

Andernfalls muss der Typparameter explizit mithilfe eines Attributs angegeben werden, das mit dem Namen des Typparameters übereinstimmt. Im folgenden Beispiel gibt `TItem="Pet"` den Typ an:

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

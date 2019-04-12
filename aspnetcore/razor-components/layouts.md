---
title: Layouts für Razor-Komponenten
author: guardrex
description: Erfahren Sie, wie Komponenten von wiederverwendbares Layout für Razor-Komponenten-apps zu erstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: razor-components/layouts
ms.openlocfilehash: 31ed940ce40e3ae6e3744418cf241d396308f4fe
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59515525"
---
# <a name="razor-components-layouts"></a><span data-ttu-id="47b25-103">Layouts für Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="47b25-103">Razor Components layouts</span></span>

<span data-ttu-id="47b25-104">Durch [Rainer Stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="47b25-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

<span data-ttu-id="47b25-105">Apps werden in der Regel mehr als eine Komponente enthalten.</span><span class="sxs-lookup"><span data-stu-id="47b25-105">Apps typically contain more than one component.</span></span> <span data-ttu-id="47b25-106">Layoutelemente, z. B. Menüs, copyright Nachrichten und Logos, müssen auf alle Komponenten vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="47b25-106">Layout elements, such as menus, copyright messages, and logos, must be present on all components.</span></span> <span data-ttu-id="47b25-107">Kopieren den Code für diese Layoutelemente in alle Komponenten einer App ist ein effizienter Ansatz nicht.</span><span class="sxs-lookup"><span data-stu-id="47b25-107">Copying the code of these layout elements into all of the components of an app isn't an efficient approach.</span></span> <span data-ttu-id="47b25-108">Solche Duplizierung ist schwer zu verwalten und wahrscheinlich führt zu einer inkonsistenten Inhalt im Laufe der Zeit.</span><span class="sxs-lookup"><span data-stu-id="47b25-108">Such duplication is hard to maintain and probably leads to inconsistent content over time.</span></span> <span data-ttu-id="47b25-109">*Layouts* dieses Problem zu lösen.</span><span class="sxs-lookup"><span data-stu-id="47b25-109">*Layouts* solve this problem.</span></span>

<span data-ttu-id="47b25-110">Technisch gesehen ist ein Layout nur eine andere Komponente.</span><span class="sxs-lookup"><span data-stu-id="47b25-110">Technically, a layout is just another component.</span></span> <span data-ttu-id="47b25-111">Ein Layout wird definiert, in einer Razor-Vorlage oder in C# code und dürfen [Datenbindung](xref:razor-components/components#data-binding), [Abhängigkeitsinjektion](xref:razor-components/dependency-injection), und andere gewöhnlichen Funktionen von Komponenten.</span><span class="sxs-lookup"><span data-stu-id="47b25-111">A layout is defined in a Razor template or in C# code and can contain [data binding](xref:razor-components/components#data-binding), [dependency injection](xref:razor-components/dependency-injection), and other ordinary features of components.</span></span>

<span data-ttu-id="47b25-112">Aktivieren Sie zwei zusätzliche Aspekte einer *Komponente* in einer *Layout*</span><span class="sxs-lookup"><span data-stu-id="47b25-112">Two additional aspects turn a *component* into a *layout*</span></span>

* <span data-ttu-id="47b25-113">Die Layoutkomponente muss vom erben `LayoutComponentBase`.</span><span class="sxs-lookup"><span data-stu-id="47b25-113">The layout component must inherit from `LayoutComponentBase`.</span></span> `LayoutComponentBase` <span data-ttu-id="47b25-114">definiert eine `Body` -Eigenschaft, die den Inhalt enthält, in das Layout gerendert werden.</span><span class="sxs-lookup"><span data-stu-id="47b25-114">defines a `Body` property that contains the content to be rendered inside the layout.</span></span>
* <span data-ttu-id="47b25-115">Die Layoutkomponente verwendet die `Body` Eigenschaft, um anzugeben, wo der Inhalt gerendert wird, mit der Razor-Syntax `@Body`.</span><span class="sxs-lookup"><span data-stu-id="47b25-115">The layout component uses the `Body` property to specify where the body content should be rendered using the Razor syntax `@Body`.</span></span> <span data-ttu-id="47b25-116">Während des Renderns `@Body` durch den Inhalt des Layouts ersetzt wird.</span><span class="sxs-lookup"><span data-stu-id="47b25-116">During rendering, `@Body` is replaced by the content of the layout.</span></span>

<span data-ttu-id="47b25-117">Das folgende Codebeispiel zeigt die Razor-Vorlage für eine Layoutkomponente.</span><span class="sxs-lookup"><span data-stu-id="47b25-117">The following code sample shows the Razor template of a layout component.</span></span> <span data-ttu-id="47b25-118">Beachten Sie die Verwendung von `LayoutComponentBase` und `@Body`:</span><span class="sxs-lookup"><span data-stu-id="47b25-118">Note the use of `LayoutComponentBase` and `@Body`:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.cshtml)]

## <a name="use-a-layout-in-a-component"></a><span data-ttu-id="47b25-119">Verwenden Sie ein Layout in einer Komponente</span><span class="sxs-lookup"><span data-stu-id="47b25-119">Use a layout in a component</span></span>

<span data-ttu-id="47b25-120">Verwenden Sie die Razor-Direktive `@layout` ein Layout auf eine Komponente anwenden.</span><span class="sxs-lookup"><span data-stu-id="47b25-120">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="47b25-121">Der Compiler konvertiert diese Richtlinie in eine `LayoutAttribute`, das auf die Component-Klasse angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="47b25-121">The compiler converts this directive into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="47b25-122">Im folgenden Codebeispiel wird das Konzept veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="47b25-122">The following code sample demonstrates the concept.</span></span> <span data-ttu-id="47b25-123">Der Inhalt dieser Komponente eingefügt, in der *MasterLayout* an der Position des `@Body`:</span><span class="sxs-lookup"><span data-stu-id="47b25-123">The content of this component is inserted into the *MasterLayout* at the position of `@Body`:</span></span>

```cshtml
@layout MasterLayout
@page "/master-list"

<h2>Master Episode List</h2>
```

## <a name="centralized-layout-selection"></a><span data-ttu-id="47b25-124">Zentralisierte Layoutauswahl.</span><span class="sxs-lookup"><span data-stu-id="47b25-124">Centralized layout selection</span></span>

<span data-ttu-id="47b25-125">Jeder Ordner, der eine app kann optional eine Vorlagendatei, die mit dem Namen enthalten *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="47b25-125">Every folder of a an app can optionally contain a template file named *_ViewImports.cshtml*.</span></span> <span data-ttu-id="47b25-126">Der Compiler schließt die Anweisungen in der Ansicht Imports-Datei in der Razor-Vorlagen im selben Ordner und rekursiv in allen Unterordnern angegeben.</span><span class="sxs-lookup"><span data-stu-id="47b25-126">The compiler includes the directives specified in the view imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="47b25-127">Aus diesem Grund eine *_ViewImports.cshtml* Datei mit `@layout MainLayout` wird sichergestellt, dass alle Komponenten in einem Ordner die *MainLayout* Layout.</span><span class="sxs-lookup"><span data-stu-id="47b25-127">Therefore, a *_ViewImports.cshtml* file containing `@layout MainLayout` ensures that all of the components in a folder use the *MainLayout* layout.</span></span> <span data-ttu-id="47b25-128">Besteht keine Notwendigkeit wiederholt hinzufügen `@layout` aller der *.razor* Dateien.</span><span class="sxs-lookup"><span data-stu-id="47b25-128">There's no need to repeatedly add `@layout` to all of the *.razor* files.</span></span>

<span data-ttu-id="47b25-129">Beachten Sie, die die Standardvorlage verwendet die *_ViewImports.cshtml* Mechanismus zur Layoutauswahl.</span><span class="sxs-lookup"><span data-stu-id="47b25-129">Note that the default template uses the *_ViewImports.cshtml* mechanism for layout selection.</span></span> <span data-ttu-id="47b25-130">Eine neu erstellte app enthält die *_ViewImports.cshtml* Datei die *Komponenten/Seiten* Ordner.</span><span class="sxs-lookup"><span data-stu-id="47b25-130">A newly created app contains the *_ViewImports.cshtml* file in the *Components/Pages* folder.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="47b25-131">Geschachtelte layouts</span><span class="sxs-lookup"><span data-stu-id="47b25-131">Nested layouts</span></span>

<span data-ttu-id="47b25-132">Apps können auf geschachtelte Layouts bestehen.</span><span class="sxs-lookup"><span data-stu-id="47b25-132">Apps can consist of nested layouts.</span></span> <span data-ttu-id="47b25-133">Eine Komponente kann es sich um ein Layout verweisen, das wiederum ein anderes Layout verweist.</span><span class="sxs-lookup"><span data-stu-id="47b25-133">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="47b25-134">Beispielsweise können schachteln Layouts verwendet werden, mit mehreren Ebenen mit eine Menüstruktur entsprechend.</span><span class="sxs-lookup"><span data-stu-id="47b25-134">For example, nesting layouts can be used to reflect a multi-level menu structure.</span></span>

<span data-ttu-id="47b25-135">Die folgenden Codebeispiele zeigen, wie geschachtelte Layouts verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="47b25-135">The following code samples show how to use nested layouts.</span></span> <span data-ttu-id="47b25-136">Die *EpisodesComponent.cshtml* Datei ist die Komponente angezeigt.</span><span class="sxs-lookup"><span data-stu-id="47b25-136">The *EpisodesComponent.cshtml* file is the component to display.</span></span> <span data-ttu-id="47b25-137">Beachten Sie, dass die Komponente verweist, das Layout auf `MasterListLayout`.</span><span class="sxs-lookup"><span data-stu-id="47b25-137">Note that the component references the layout `MasterListLayout`.</span></span>

<span data-ttu-id="47b25-138">*EpisodesComponent.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="47b25-138">*EpisodesComponent.cshtml*:</span></span>

```cshtml
@layout MasterListLayout
@page "/master-list/episodes"

<h1>Episodes</h1>
```

<span data-ttu-id="47b25-139">Die *MasterListLayout.cshtml* Datei enthält die `MasterListLayout`.</span><span class="sxs-lookup"><span data-stu-id="47b25-139">The *MasterListLayout.cshtml* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="47b25-140">Das Layout verweist auf ein anderes Layout `MasterLayout`, wohin diese fließen, die eingebettet werden.</span><span class="sxs-lookup"><span data-stu-id="47b25-140">The layout references another layout, `MasterLayout`, where it's going to be embedded.</span></span>

<span data-ttu-id="47b25-141">*MasterListLayout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="47b25-141">*MasterListLayout.cshtml*:</span></span>

```cshtml
@layout MasterLayout
@inherits LayoutComponentBase

<nav>
    <!-- Menu structure of master list -->
    ...
</nav>

@Body
```

<span data-ttu-id="47b25-142">Zum Schluss `MasterLayout` enthält die Layoutelemente der obersten Ebene, z. B. die Header, Footer und im Hauptmenü.</span><span class="sxs-lookup"><span data-stu-id="47b25-142">Finally, `MasterLayout` contains the top-level layout elements, such as the header, footer, and main menu.</span></span>

<span data-ttu-id="47b25-143">*MasterLayout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="47b25-143">*MasterLayout.cshtml*:</span></span>

```cshtml
@inherits LayoutComponentBase

<header>...</header>
<nav>...</nav>

@Body
```

---
title: ASP.net Core blazor-Layouts
author: guardrex
description: Erfahren Sie, wie Sie wiederverwendbare Layoutkomponenten für blazor-Apps erstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2019
uid: blazor/layouts
ms.openlocfilehash: 2d652e149381f0a93e3135da978ab5737d47c6f1
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/03/2019
ms.locfileid: "68948220"
---
# <a name="aspnet-core-blazor-layouts"></a><span data-ttu-id="58fab-103">ASP.net Core blazor-Layouts</span><span class="sxs-lookup"><span data-stu-id="58fab-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="58fab-104">Von [Rainer stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="58fab-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

<span data-ttu-id="58fab-105">Einige APP-Elemente, z. b. Menüs, Copyright Meldungen und Firmenlogos, sind in der Regel Teil des gesamten Layouts der APP und werden von jeder Komponente in der APP verwendet.</span><span class="sxs-lookup"><span data-stu-id="58fab-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="58fab-106">Das Kopieren des Codes dieser Elemente in alle Komponenten einer APP ist kein effizienter Ansatz&mdash;, wenn eines der Elemente ein Update erfordert, muss jede Komponente aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="58fab-106">Copying the code of these elements into all of the components of an app isn't an efficient approach&mdash;every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="58fab-107">Eine solche Duplizierung ist schwierig zu verwalten und kann zu inkonsistenten Inhalten im Laufe der Zeit führen.</span><span class="sxs-lookup"><span data-stu-id="58fab-107">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="58fab-108">*Layouts* lösen dieses Problem.</span><span class="sxs-lookup"><span data-stu-id="58fab-108">*Layouts* solve this problem.</span></span>

<span data-ttu-id="58fab-109">Technisch gesehen ist ein Layout nur eine andere Komponente.</span><span class="sxs-lookup"><span data-stu-id="58fab-109">Technically, a layout is just another component.</span></span> <span data-ttu-id="58fab-110">Ein Layout ist in einer Razor-Vorlage oder im C# Code definiert und kann [Datenbindung](xref:blazor/components#data-binding), [Abhängigkeitsinjektion](xref:blazor/dependency-injection)und andere Komponenten Szenarios verwenden.</span><span class="sxs-lookup"><span data-stu-id="58fab-110">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/components#data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="58fab-111">Um eine *Komponente* in ein *Layout*umzuwandeln, ist die Komponente:</span><span class="sxs-lookup"><span data-stu-id="58fab-111">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="58fab-112">Erbt von `LayoutComponentBase`, wodurch eine `Body` Eigenschaft für den gerenderten Inhalt innerhalb des Layouts definiert wird.</span><span class="sxs-lookup"><span data-stu-id="58fab-112">Inherits from `LayoutComponentBase`, which defines a `Body` property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="58fab-113">Verwendet die Razor-Syntax `@Body` , um den Speicherort im layoutmarkup anzugeben, an dem der Inhalt gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="58fab-113">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="58fab-114">Das folgende Codebeispiel zeigt die Razor-Vorlage der Layoutkomponente *MainLayout. Razor*.</span><span class="sxs-lookup"><span data-stu-id="58fab-114">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="58fab-115">Das Layout erbt `LayoutComponentBase` und legt die `@Body` zwischen der Navigationsleiste und der Fußzeile fest:</span><span class="sxs-lookup"><span data-stu-id="58fab-115">The layout inherits `LayoutComponentBase` and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="58fab-116">Angeben eines Layouts in einer Komponente</span><span class="sxs-lookup"><span data-stu-id="58fab-116">Specify a layout in a component</span></span>

<span data-ttu-id="58fab-117">Verwenden Sie die Razor `@layout` -Direktive, um ein Layout auf eine Komponente anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="58fab-117">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="58fab-118">Der Compiler konvertiert `@layout` in eine `LayoutAttribute`, die auf die Component-Klasse angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="58fab-118">The compiler converts `@layout` into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="58fab-119">Der Inhalt der folgenden Komponente, *Masterlist. Razor*, wird `MainLayout` an der Position von `@Body`in eingefügt:</span><span class="sxs-lookup"><span data-stu-id="58fab-119">The content of the following component, *MasterList.razor*, is inserted into the `MainLayout` at the position of `@Body`:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

## <a name="centralized-layout-selection"></a><span data-ttu-id="58fab-120">Zentrale Layoutauswahl</span><span class="sxs-lookup"><span data-stu-id="58fab-120">Centralized layout selection</span></span>

<span data-ttu-id="58fab-121">Jeder Ordner einer APP kann optional eine Vorlagen Datei mit dem Namen *_Imports. Razor*enthalten.</span><span class="sxs-lookup"><span data-stu-id="58fab-121">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="58fab-122">Der Compiler schließt die in der Imports-Datei angegebenen Direktiven in allen Razor-Vorlagen im gleichen Ordner und rekursiv in allen Unterordnern ein.</span><span class="sxs-lookup"><span data-stu-id="58fab-122">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="58fab-123">Daher wird durch eine *_Imports. Razor* - `@layout MainLayout` Datei sichergestellt, dass alle Komponenten in einem Ordner `MainLayout`verwenden.</span><span class="sxs-lookup"><span data-stu-id="58fab-123">Therefore, an *_Imports.razor* file containing `@layout MainLayout` ensures that all of the components in a folder use `MainLayout`.</span></span> <span data-ttu-id="58fab-124">Es ist nicht erforderlich, alle `@layout MainLayout` *Razor* -Dateien im Ordner und in den Unterordnern wiederholt hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="58fab-124">There's no need to repeatedly add `@layout MainLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="58fab-125">`@using`Direktiven werden auch auf die gleiche Weise auf Komponenten angewendet.</span><span class="sxs-lookup"><span data-stu-id="58fab-125">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="58fab-126">Die folgenden *_Imports. Razor* -Datei wird importiert:</span><span class="sxs-lookup"><span data-stu-id="58fab-126">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="58fab-127">`MainLayout`.</span><span class="sxs-lookup"><span data-stu-id="58fab-127">`MainLayout`.</span></span>
* <span data-ttu-id="58fab-128">Alle Razor-Komponenten im selben Ordner und in allen Unterordnern.</span><span class="sxs-lookup"><span data-stu-id="58fab-128">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="58fab-129">Der `BlazorApp1.Data` -Namespace.</span><span class="sxs-lookup"><span data-stu-id="58fab-129">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-cshtml[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="58fab-130">Die Datei *_Imports. Razor* ähnelt der [Datei _ViewImports. cshtml für Razor-Ansichten und-Seiten](xref:mvc/views/layout#importing-shared-directives) , die jedoch speziell auf Razor-Komponenten Dateien angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="58fab-130">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="58fab-131">In den blazor-Vorlagen werden *_Imports. Razor* -Dateien für die Layoutauswahl verwendet.</span><span class="sxs-lookup"><span data-stu-id="58fab-131">The Blazor templates use *_Imports.razor* files for layout selection.</span></span> <span data-ttu-id="58fab-132">Eine APP, die über eine blazor-Vorlage erstellt wurde, enthält die Datei *_Imports. Razor* im Stammverzeichnis des Projekts und im Ordner *pages* .</span><span class="sxs-lookup"><span data-stu-id="58fab-132">An app created from a Blazor template contains the *_Imports.razor* file in the root of the project and in the *Pages* folder.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="58fab-133">In-Netz-Layouts</span><span class="sxs-lookup"><span data-stu-id="58fab-133">Nested layouts</span></span>

<span data-ttu-id="58fab-134">Apps können aus den in einem Netz gefügten Layouts bestehen.</span><span class="sxs-lookup"><span data-stu-id="58fab-134">Apps can consist of nested layouts.</span></span> <span data-ttu-id="58fab-135">Eine Komponente kann auf ein Layout verweisen, das wiederum auf ein anderes Layout verweist.</span><span class="sxs-lookup"><span data-stu-id="58fab-135">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="58fab-136">Schachtelungs Layouts werden z. b. verwendet, um eine Menüstruktur mit mehreren Ebenen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="58fab-136">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="58fab-137">Im folgenden Beispiel wird gezeigt, wie Sie die Verwendung von Netz Layouts durchführen.</span><span class="sxs-lookup"><span data-stu-id="58fab-137">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="58fab-138">Die Datei " *episodescomponent. Razor* " ist die Komponente, die angezeigt werden soll.</span><span class="sxs-lookup"><span data-stu-id="58fab-138">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="58fab-139">Die Komponente verweist auf `MasterListLayout`das:</span><span class="sxs-lookup"><span data-stu-id="58fab-139">The component references the `MasterListLayout`:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="58fab-140">Die Datei *masterlistlayout. Razor* stellt den `MasterListLayout`bereit.</span><span class="sxs-lookup"><span data-stu-id="58fab-140">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="58fab-141">Das Layout verweist auf ein anderes `MasterLayout`Layout,, in dem es gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="58fab-141">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="58fab-142">`EpisodesComponent`wird gerendert, wo `@Body` angezeigt wird</span><span class="sxs-lookup"><span data-stu-id="58fab-142">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="58fab-143">Schließlich enthält in Masterlayout. Razor die Layoutelemente der obersten Ebene, z. b. Header, Hauptmenü und Fußzeile. `MasterLayout`</span><span class="sxs-lookup"><span data-stu-id="58fab-143">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="58fab-144">`MasterListLayout`mit `EpisodesComponent` werden gerendert, wo `@Body` angezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="58fab-144">`MasterListLayout` with `EpisodesComponent` are rendered where `@Body` appears:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="additional-resources"></a><span data-ttu-id="58fab-145">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="58fab-145">Additional resources</span></span>

* <xref:mvc/views/layout>

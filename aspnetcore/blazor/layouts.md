---
title: ASP.net Core blazor-Layouts
author: guardrex
description: Erfahren Sie, wie Sie wiederverwendbare Layoutkomponenten für blazor-Apps erstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/06/2019
uid: blazor/layouts
ms.openlocfilehash: 05a38c10e18407d50422192ab1ddf3ff4b0f3a5b
ms.sourcegitcommit: 43c6335b5859282f64d66a7696c5935a2bcdf966
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2019
ms.locfileid: "70800360"
---
# <a name="aspnet-core-blazor-layouts"></a><span data-ttu-id="3909b-103">ASP.net Core blazor-Layouts</span><span class="sxs-lookup"><span data-stu-id="3909b-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="3909b-104">Von [Rainer stropek](https://www.timecockpit.com) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3909b-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="3909b-105">Einige APP-Elemente, z. b. Menüs, Copyright Meldungen und Firmenlogos, sind in der Regel Teil des gesamten Layouts der APP und werden von jeder Komponente in der APP verwendet.</span><span class="sxs-lookup"><span data-stu-id="3909b-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="3909b-106">Das Kopieren des Codes dieser Elemente in alle Komponenten einer APP ist kein effizienter Ansatz&mdash;, wenn eines der Elemente ein Update erfordert, muss jede Komponente aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="3909b-106">Copying the code of these elements into all of the components of an app isn't an efficient approach&mdash;every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="3909b-107">Eine solche Duplizierung ist schwierig zu verwalten und kann zu inkonsistenten Inhalten im Laufe der Zeit führen.</span><span class="sxs-lookup"><span data-stu-id="3909b-107">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="3909b-108">*Layouts* lösen dieses Problem.</span><span class="sxs-lookup"><span data-stu-id="3909b-108">*Layouts* solve this problem.</span></span>

<span data-ttu-id="3909b-109">Technisch gesehen ist ein Layout nur eine andere Komponente.</span><span class="sxs-lookup"><span data-stu-id="3909b-109">Technically, a layout is just another component.</span></span> <span data-ttu-id="3909b-110">Ein Layout ist in einer Razor-Vorlage oder im C# Code definiert und kann [Datenbindung](xref:blazor/components#data-binding), [Abhängigkeitsinjektion](xref:blazor/dependency-injection)und andere Komponenten Szenarios verwenden.</span><span class="sxs-lookup"><span data-stu-id="3909b-110">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/components#data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="3909b-111">Um eine *Komponente* in ein *Layout*umzuwandeln, ist die Komponente:</span><span class="sxs-lookup"><span data-stu-id="3909b-111">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="3909b-112">Erbt von `LayoutComponentBase`, wodurch eine `Body` Eigenschaft für den gerenderten Inhalt innerhalb des Layouts definiert wird.</span><span class="sxs-lookup"><span data-stu-id="3909b-112">Inherits from `LayoutComponentBase`, which defines a `Body` property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="3909b-113">Verwendet die Razor-Syntax `@Body` , um den Speicherort im layoutmarkup anzugeben, an dem der Inhalt gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="3909b-113">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="3909b-114">Das folgende Codebeispiel zeigt die Razor-Vorlage der Layoutkomponente *MainLayout. Razor*.</span><span class="sxs-lookup"><span data-stu-id="3909b-114">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="3909b-115">Das Layout erbt `LayoutComponentBase` und legt die `@Body` zwischen der Navigationsleiste und der Fußzeile fest:</span><span class="sxs-lookup"><span data-stu-id="3909b-115">The layout inherits `LayoutComponentBase` and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

<span data-ttu-id="3909b-116">In einer APP, die auf einer der Vorlagen der blazor-APP `MainLayout` basiert, befindet sich die Komponente (*MainLayout. Razor*) im frei *gegebenen* Ordner der app.</span><span class="sxs-lookup"><span data-stu-id="3909b-116">In an app based on one of the Blazor app templates, the `MainLayout` component (*MainLayout.razor*) is in the app's *Shared* folder.</span></span>

## <a name="default-layout"></a><span data-ttu-id="3909b-117">Standardlayout</span><span class="sxs-lookup"><span data-stu-id="3909b-117">Default layout</span></span>

<span data-ttu-id="3909b-118">Geben Sie das standardmäßige APP- `Router` Layout in der-Komponente in der *app. Razor* -Datei der APP an.</span><span class="sxs-lookup"><span data-stu-id="3909b-118">Specify the default app layout in the `Router` component in the app's *App.razor* file.</span></span> <span data-ttu-id="3909b-119">Mit der `Router` folgenden Komponente, die von den Standardvorlagen für blazor bereitgestellt wird, wird das Standard `MainLayout` Layout auf die Komponente festgelegt:</span><span class="sxs-lookup"><span data-stu-id="3909b-119">The following `Router` component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="3909b-120">Wenn Sie ein Standardlayout für `NotFound` den Inhalt angeben möchten `LayoutView` , `NotFound` geben Sie einen für Inhalt an:</span><span class="sxs-lookup"><span data-stu-id="3909b-120">To supply a default layout for `NotFound` content, specify a `LayoutView` for `NotFound` content:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="3909b-121">Weitere Informationen zur `Router` -Komponente finden <xref:blazor/routing>Sie unter.</span><span class="sxs-lookup"><span data-stu-id="3909b-121">For more information on the `Router` component, see <xref:blazor/routing>.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="3909b-122">Angeben eines Layouts in einer Komponente</span><span class="sxs-lookup"><span data-stu-id="3909b-122">Specify a layout in a component</span></span>

<span data-ttu-id="3909b-123">Verwenden Sie die Razor `@layout` -Direktive, um ein Layout auf eine Komponente anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="3909b-123">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="3909b-124">Der Compiler konvertiert `@layout` in eine `LayoutAttribute`, die auf die Component-Klasse angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="3909b-124">The compiler converts `@layout` into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="3909b-125">Der Inhalt der folgenden `MasterList` Komponente wird `MasterLayout` an der Position von `@Body`in eingefügt:</span><span class="sxs-lookup"><span data-stu-id="3909b-125">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

## <a name="centralized-layout-selection"></a><span data-ttu-id="3909b-126">Zentrale Layoutauswahl</span><span class="sxs-lookup"><span data-stu-id="3909b-126">Centralized layout selection</span></span>

<span data-ttu-id="3909b-127">Jeder Ordner einer APP kann optional eine Vorlagen Datei mit dem Namen *_Imports. Razor*enthalten.</span><span class="sxs-lookup"><span data-stu-id="3909b-127">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="3909b-128">Der Compiler schließt die in der Imports-Datei angegebenen Direktiven in allen Razor-Vorlagen im gleichen Ordner und rekursiv in allen Unterordnern ein.</span><span class="sxs-lookup"><span data-stu-id="3909b-128">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="3909b-129">Daher wird durch eine *_Imports. Razor* - `@layout MyCoolLayout` Datei sichergestellt, dass alle Komponenten in einem Ordner `MyCoolLayout`verwenden.</span><span class="sxs-lookup"><span data-stu-id="3909b-129">Therefore, an *_Imports.razor* file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="3909b-130">Es ist nicht erforderlich, alle `@layout MyCoolLayout` *Razor* -Dateien im Ordner und in den Unterordnern wiederholt hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="3909b-130">There's no need to repeatedly add `@layout MyCoolLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="3909b-131">`@using`Direktiven werden auch auf die gleiche Weise auf Komponenten angewendet.</span><span class="sxs-lookup"><span data-stu-id="3909b-131">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="3909b-132">Die folgenden *_Imports. Razor* -Datei wird importiert:</span><span class="sxs-lookup"><span data-stu-id="3909b-132">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="3909b-133">`MyCoolLayout`</span><span class="sxs-lookup"><span data-stu-id="3909b-133">`MyCoolLayout`.</span></span>
* <span data-ttu-id="3909b-134">Alle Razor-Komponenten im selben Ordner und in allen Unterordnern.</span><span class="sxs-lookup"><span data-stu-id="3909b-134">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="3909b-135">Der `BlazorApp1.Data` -Namespace.</span><span class="sxs-lookup"><span data-stu-id="3909b-135">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-cshtml[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="3909b-136">Die Datei *_Imports. Razor* ähnelt der [Datei _ViewImports. cshtml für Razor-Ansichten und-Seiten](xref:mvc/views/layout#importing-shared-directives) , die jedoch speziell auf Razor-Komponenten Dateien angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="3909b-136">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="3909b-137">In-Netz-Layouts</span><span class="sxs-lookup"><span data-stu-id="3909b-137">Nested layouts</span></span>

<span data-ttu-id="3909b-138">Apps können aus den in einem Netz gefügten Layouts bestehen.</span><span class="sxs-lookup"><span data-stu-id="3909b-138">Apps can consist of nested layouts.</span></span> <span data-ttu-id="3909b-139">Eine Komponente kann auf ein Layout verweisen, das wiederum auf ein anderes Layout verweist.</span><span class="sxs-lookup"><span data-stu-id="3909b-139">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="3909b-140">Schachtelungs Layouts werden z. b. verwendet, um eine Menüstruktur mit mehreren Ebenen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="3909b-140">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="3909b-141">Im folgenden Beispiel wird gezeigt, wie Sie die Verwendung von Netz Layouts durchführen.</span><span class="sxs-lookup"><span data-stu-id="3909b-141">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="3909b-142">Die Datei " *episodescomponent. Razor* " ist die Komponente, die angezeigt werden soll.</span><span class="sxs-lookup"><span data-stu-id="3909b-142">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="3909b-143">Die Komponente verweist auf `MasterListLayout`das:</span><span class="sxs-lookup"><span data-stu-id="3909b-143">The component references the `MasterListLayout`:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="3909b-144">Die Datei *masterlistlayout. Razor* stellt den `MasterListLayout`bereit.</span><span class="sxs-lookup"><span data-stu-id="3909b-144">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="3909b-145">Das Layout verweist auf ein anderes `MasterLayout`Layout,, in dem es gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="3909b-145">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="3909b-146">`EpisodesComponent`wird gerendert, wo `@Body` angezeigt wird</span><span class="sxs-lookup"><span data-stu-id="3909b-146">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="3909b-147">Schließlich enthält in Masterlayout. Razor die Layoutelemente der obersten Ebene, z. b. Header, Hauptmenü und Fußzeile. `MasterLayout`</span><span class="sxs-lookup"><span data-stu-id="3909b-147">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="3909b-148">`MasterListLayout`mit wird gerendert `@Body` , wenn Folgendes angezeigt wird: `EpisodesComponent`</span><span class="sxs-lookup"><span data-stu-id="3909b-148">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="additional-resources"></a><span data-ttu-id="3909b-149">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="3909b-149">Additional resources</span></span>

* <xref:mvc/views/layout>

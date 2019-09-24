---
title: ASP.net Core blazor-Layouts
author: guardrex
description: Erfahren Sie, wie Sie wiederverwendbare Layoutkomponenten für blazor-Apps erstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/layouts
ms.openlocfilehash: 064ffafb8e7f3e76e5bd7bde0e06ef271fe92542
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211581"
---
# <a name="aspnet-core-blazor-layouts"></a><span data-ttu-id="89e92-103">ASP.net Core blazor-Layouts</span><span class="sxs-lookup"><span data-stu-id="89e92-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="89e92-104">Von [Rainer stropek](https://www.timecockpit.com) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="89e92-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="89e92-105">Einige APP-Elemente, z. b. Menüs, Copyright Meldungen und Firmenlogos, sind in der Regel Teil des gesamten Layouts der APP und werden von jeder Komponente in der APP verwendet.</span><span class="sxs-lookup"><span data-stu-id="89e92-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="89e92-106">Das Kopieren des Codes dieser Elemente in alle Komponenten einer APP ist kein effizienter Ansatz&mdash;, wenn eines der Elemente ein Update erfordert, muss jede Komponente aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="89e92-106">Copying the code of these elements into all of the components of an app isn't an efficient approach&mdash;every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="89e92-107">Eine solche Duplizierung ist schwierig zu verwalten und kann zu inkonsistenten Inhalten im Laufe der Zeit führen.</span><span class="sxs-lookup"><span data-stu-id="89e92-107">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="89e92-108">*Layouts* lösen dieses Problem.</span><span class="sxs-lookup"><span data-stu-id="89e92-108">*Layouts* solve this problem.</span></span>

<span data-ttu-id="89e92-109">Technisch gesehen ist ein Layout nur eine andere Komponente.</span><span class="sxs-lookup"><span data-stu-id="89e92-109">Technically, a layout is just another component.</span></span> <span data-ttu-id="89e92-110">Ein Layout ist in einer Razor-Vorlage oder im C# Code definiert und kann [Datenbindung](xref:blazor/components#data-binding), [Abhängigkeitsinjektion](xref:blazor/dependency-injection)und andere Komponenten Szenarios verwenden.</span><span class="sxs-lookup"><span data-stu-id="89e92-110">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/components#data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="89e92-111">Um eine *Komponente* in ein *Layout*umzuwandeln, ist die Komponente:</span><span class="sxs-lookup"><span data-stu-id="89e92-111">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="89e92-112">Erbt von `LayoutComponentBase`, wodurch eine `Body` Eigenschaft für den gerenderten Inhalt innerhalb des Layouts definiert wird.</span><span class="sxs-lookup"><span data-stu-id="89e92-112">Inherits from `LayoutComponentBase`, which defines a `Body` property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="89e92-113">Verwendet die Razor-Syntax `@Body` , um den Speicherort im layoutmarkup anzugeben, an dem der Inhalt gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="89e92-113">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="89e92-114">Das folgende Codebeispiel zeigt die Razor-Vorlage der Layoutkomponente *MainLayout. Razor*.</span><span class="sxs-lookup"><span data-stu-id="89e92-114">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="89e92-115">Das Layout erbt `LayoutComponentBase` und legt die `@Body` zwischen der Navigationsleiste und der Fußzeile fest:</span><span class="sxs-lookup"><span data-stu-id="89e92-115">The layout inherits `LayoutComponentBase` and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

<span data-ttu-id="89e92-116">In einer APP, die auf einer der Vorlagen der blazor-APP `MainLayout` basiert, befindet sich die Komponente (*MainLayout. Razor*) im frei *gegebenen* Ordner der app.</span><span class="sxs-lookup"><span data-stu-id="89e92-116">In an app based on one of the Blazor app templates, the `MainLayout` component (*MainLayout.razor*) is in the app's *Shared* folder.</span></span>

## <a name="default-layout"></a><span data-ttu-id="89e92-117">Standardlayout</span><span class="sxs-lookup"><span data-stu-id="89e92-117">Default layout</span></span>

<span data-ttu-id="89e92-118">Geben Sie das standardmäßige APP- `Router` Layout in der-Komponente in der *app. Razor* -Datei der APP an.</span><span class="sxs-lookup"><span data-stu-id="89e92-118">Specify the default app layout in the `Router` component in the app's *App.razor* file.</span></span> <span data-ttu-id="89e92-119">Mit der `Router` folgenden Komponente, die von den Standardvorlagen für blazor bereitgestellt wird, wird das Standard `MainLayout` Layout auf die Komponente festgelegt:</span><span class="sxs-lookup"><span data-stu-id="89e92-119">The following `Router` component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="89e92-120">Wenn Sie ein Standardlayout für `NotFound` den Inhalt angeben möchten `LayoutView` , `NotFound` geben Sie einen für Inhalt an:</span><span class="sxs-lookup"><span data-stu-id="89e92-120">To supply a default layout for `NotFound` content, specify a `LayoutView` for `NotFound` content:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="89e92-121">Weitere Informationen zur `Router` -Komponente finden <xref:blazor/routing>Sie unter.</span><span class="sxs-lookup"><span data-stu-id="89e92-121">For more information on the `Router` component, see <xref:blazor/routing>.</span></span>

<span data-ttu-id="89e92-122">Die Angabe des Layouts als Standardlayout im Router ist eine sinnvolle Vorgehensweise, da Sie für einzelne Komponenten oder Ordner pro Ordner überschrieben werden kann.</span><span class="sxs-lookup"><span data-stu-id="89e92-122">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="89e92-123">Verwenden Sie den Router, um das Standardlayout der App festzulegen, da es sich hierbei um die allgemeinste Methode handelt.</span><span class="sxs-lookup"><span data-stu-id="89e92-123">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="89e92-124">Angeben eines Layouts in einer Komponente</span><span class="sxs-lookup"><span data-stu-id="89e92-124">Specify a layout in a component</span></span>

<span data-ttu-id="89e92-125">Verwenden Sie die Razor `@layout` -Direktive, um ein Layout auf eine Komponente anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="89e92-125">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="89e92-126">Der Compiler konvertiert `@layout` in eine `LayoutAttribute`, die auf die Component-Klasse angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="89e92-126">The compiler converts `@layout` into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="89e92-127">Der Inhalt der folgenden `MasterList` Komponente wird `MasterLayout` an der Position von `@Body`in eingefügt:</span><span class="sxs-lookup"><span data-stu-id="89e92-127">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="89e92-128">Wenn Sie das Layout direkt in einer Komponente angeben, wird ein *Standardlayout* im Router oder eine `@layout` aus *_Imports. Razor*importierte Direktive überschrieben.</span><span class="sxs-lookup"><span data-stu-id="89e92-128">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from *_Imports.razor*.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="89e92-129">Zentrale Layoutauswahl</span><span class="sxs-lookup"><span data-stu-id="89e92-129">Centralized layout selection</span></span>

<span data-ttu-id="89e92-130">Jeder Ordner einer APP kann optional eine Vorlagen Datei mit dem Namen *_Imports. Razor*enthalten.</span><span class="sxs-lookup"><span data-stu-id="89e92-130">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="89e92-131">Der Compiler schließt die in der Imports-Datei angegebenen Direktiven in allen Razor-Vorlagen im gleichen Ordner und rekursiv in allen Unterordnern ein.</span><span class="sxs-lookup"><span data-stu-id="89e92-131">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="89e92-132">Daher wird durch eine *_Imports. Razor* - `@layout MyCoolLayout` Datei sichergestellt, dass alle Komponenten in einem Ordner `MyCoolLayout`verwenden.</span><span class="sxs-lookup"><span data-stu-id="89e92-132">Therefore, an *_Imports.razor* file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="89e92-133">Es ist nicht erforderlich, alle `@layout MyCoolLayout` *Razor* -Dateien im Ordner und in den Unterordnern wiederholt hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="89e92-133">There's no need to repeatedly add `@layout MyCoolLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="89e92-134">`@using`Direktiven werden auch auf die gleiche Weise auf Komponenten angewendet.</span><span class="sxs-lookup"><span data-stu-id="89e92-134">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="89e92-135">Die folgenden *_Imports. Razor* -Datei wird importiert:</span><span class="sxs-lookup"><span data-stu-id="89e92-135">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="89e92-136">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="89e92-136">`MyCoolLayout`.</span></span>
* <span data-ttu-id="89e92-137">Alle Razor-Komponenten im selben Ordner und in allen Unterordnern.</span><span class="sxs-lookup"><span data-stu-id="89e92-137">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="89e92-138">Der `BlazorApp1.Data` -Namespace.</span><span class="sxs-lookup"><span data-stu-id="89e92-138">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-cshtml[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="89e92-139">Die Datei *_Imports. Razor* ähnelt der [Datei _ViewImports. cshtml für Razor-Ansichten und-Seiten](xref:mvc/views/layout#importing-shared-directives) , die jedoch speziell auf Razor-Komponenten Dateien angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="89e92-139">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="89e92-140">Durch die Angabe eines Layouts in *_Imports. Razor* wird ein Layout überschrieben, das als *Standardlayout*des Routers angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="89e92-140">Specifying a layout in *_Imports.razor* overrides a layout specified as the router's *default layout*.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="89e92-141">In-Netz-Layouts</span><span class="sxs-lookup"><span data-stu-id="89e92-141">Nested layouts</span></span>

<span data-ttu-id="89e92-142">Apps können aus den in einem Netz gefügten Layouts bestehen.</span><span class="sxs-lookup"><span data-stu-id="89e92-142">Apps can consist of nested layouts.</span></span> <span data-ttu-id="89e92-143">Eine Komponente kann auf ein Layout verweisen, das wiederum auf ein anderes Layout verweist.</span><span class="sxs-lookup"><span data-stu-id="89e92-143">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="89e92-144">Schachtelungs Layouts werden z. b. verwendet, um eine Menüstruktur mit mehreren Ebenen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="89e92-144">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="89e92-145">Im folgenden Beispiel wird gezeigt, wie Sie die Verwendung von Netz Layouts durchführen.</span><span class="sxs-lookup"><span data-stu-id="89e92-145">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="89e92-146">Die Datei " *episodescomponent. Razor* " ist die Komponente, die angezeigt werden soll.</span><span class="sxs-lookup"><span data-stu-id="89e92-146">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="89e92-147">Die Komponente verweist auf `MasterListLayout`das:</span><span class="sxs-lookup"><span data-stu-id="89e92-147">The component references the `MasterListLayout`:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="89e92-148">Die Datei *masterlistlayout. Razor* stellt den `MasterListLayout`bereit.</span><span class="sxs-lookup"><span data-stu-id="89e92-148">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="89e92-149">Das Layout verweist auf ein anderes `MasterLayout`Layout,, in dem es gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="89e92-149">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="89e92-150">`EpisodesComponent`wird gerendert, wo `@Body` angezeigt wird</span><span class="sxs-lookup"><span data-stu-id="89e92-150">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="89e92-151">Schließlich enthält in Masterlayout. Razor die Layoutelemente der obersten Ebene, z. b. Header, Hauptmenü und Fußzeile. `MasterLayout`</span><span class="sxs-lookup"><span data-stu-id="89e92-151">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="89e92-152">`MasterListLayout`mit wird gerendert `@Body` , wenn Folgendes angezeigt wird: `EpisodesComponent`</span><span class="sxs-lookup"><span data-stu-id="89e92-152">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="additional-resources"></a><span data-ttu-id="89e92-153">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="89e92-153">Additional resources</span></span>

* <xref:mvc/views/layout>

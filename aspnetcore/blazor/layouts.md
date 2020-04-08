---
title: Blazor-Layouts in ASP.NET Core
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie wiederverwendbare Layoutkomponenten für Blazor-Apps erstellen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: 5b6e1c7ceb4a6e41230e31bbe379bde1bb0a8286
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2020
ms.locfileid: "78647923"
---
# <a name="aspnet-core-opno-locblazor-layouts"></a><span data-ttu-id="effdf-103">Blazor-Layouts in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="effdf-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="effdf-104">Von [Rainer Stropek](https://www.timecockpit.com) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="effdf-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="effdf-105">Einige App-Elemente wie Menüs, Copyrightmeldungen und Firmenlogos sind in der Regel Teil des allgemeinen Layouts von Apps und werden von allen Komponenten der App verwendet.</span><span class="sxs-lookup"><span data-stu-id="effdf-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="effdf-106">Es ist nicht sonderlich effizient, den Code dieser Elemente in alle anderen Komponenten einer App zu kopieren. Jedes Mal, wenn eines dieser Elemente aktualisiert werden muss, müssen alle Komponenten aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="effdf-106">Copying the code of these elements into all of the components of an app isn't an efficient approach&mdash;every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="effdf-107">Eine solche Duplizierung ist schwer zu verwalten und kann im Laufe der Zeit zu inkonsistenten Inhalten führen.</span><span class="sxs-lookup"><span data-stu-id="effdf-107">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="effdf-108">Mit *Layouts* wird dieses Problem gelöst.</span><span class="sxs-lookup"><span data-stu-id="effdf-108">*Layouts* solve this problem.</span></span>

<span data-ttu-id="effdf-109">Technisch gesehen ist ein Layout nur eine weitere Komponente.</span><span class="sxs-lookup"><span data-stu-id="effdf-109">Technically, a layout is just another component.</span></span> <span data-ttu-id="effdf-110">Layouts werden in Razor-Vorlagen oder in C#-Code definiert und können [Datenbindungen](xref:blazor/data-binding), [Abhängigkeitsinjektion](xref:blazor/dependency-injection) und andere Komponentenszenarios nutzen.</span><span class="sxs-lookup"><span data-stu-id="effdf-110">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="effdf-111">Zum Umwandeln einer *Komponente* in ein *Layout* muss die Komponente:</span><span class="sxs-lookup"><span data-stu-id="effdf-111">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="effdf-112">von `LayoutComponentBase` erben, was eine `Body`-Eigenschaft für die gerenderten Inhalte im Layout definiert.</span><span class="sxs-lookup"><span data-stu-id="effdf-112">Inherits from `LayoutComponentBase`, which defines a `Body` property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="effdf-113">die Razor-Syntax `@Body` verwenden, um den Standort im Layoutmarkup anzugeben, ab den der Inhalt gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="effdf-113">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="effdf-114">Im folgenden Codebeispiel wird eine Razor-Vorlage einer Layoutkomponente (*MainLayout.razor*) veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="effdf-114">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="effdf-115">Das Layout erbt `LayoutComponentBase` und legt `@Body` zwischen der Navigationsleiste und der Fußzeile fest:</span><span class="sxs-lookup"><span data-stu-id="effdf-115">The layout inherits `LayoutComponentBase` and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

<span data-ttu-id="effdf-116">Bei Apps, die auf Blazor-App-Vorlagen basieren, befindet sich die `MainLayout`-Komponente (*MainLayout.razor*) im Ordner *Shared* der App.</span><span class="sxs-lookup"><span data-stu-id="effdf-116">In an app based on one of the Blazor app templates, the `MainLayout` component (*MainLayout.razor*) is in the app's *Shared* folder.</span></span>

## <a name="default-layout"></a><span data-ttu-id="effdf-117">Standardlayout</span><span class="sxs-lookup"><span data-stu-id="effdf-117">Default layout</span></span>

<span data-ttu-id="effdf-118">Legen Sie das Standardlayout für die App in der `Router`-Komponente der Datei *App.razor* der App fest.</span><span class="sxs-lookup"><span data-stu-id="effdf-118">Specify the default app layout in the `Router` component in the app's *App.razor* file.</span></span> <span data-ttu-id="effdf-119">Mit der folgenden `Router`-Komponente, die von den Blazor-Standardvorlagen bereitgestellt wird, wird das Standardlayout auf die `MainLayout`-Komponente festgelegt:</span><span class="sxs-lookup"><span data-stu-id="effdf-119">The following `Router` component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="effdf-120">Geben Sie `NotFound` für `LayoutView`-Inhalt an, um ein Standardlayout für `NotFound`-Inhalt anzugeben:</span><span class="sxs-lookup"><span data-stu-id="effdf-120">To supply a default layout for `NotFound` content, specify a `LayoutView` for `NotFound` content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="effdf-121">Weitere Informationen zur `Router`-Komponente finden Sie unter <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="effdf-121">For more information on the `Router` component, see <xref:blazor/routing>.</span></span>

<span data-ttu-id="effdf-122">Das Festlegen des Layouts als Standardlayout im Router ist nützlich, da es pro Komponente oder pro Ordner überschrieben werden kann.</span><span class="sxs-lookup"><span data-stu-id="effdf-122">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="effdf-123">Das Verwenden des Routers zum Festlegen des Standardlayouts der App wird bevorzugt, da es sich dabei um die allgemeinste Methode handelt.</span><span class="sxs-lookup"><span data-stu-id="effdf-123">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="effdf-124">Festlegen eines Layouts in einer Komponente</span><span class="sxs-lookup"><span data-stu-id="effdf-124">Specify a layout in a component</span></span>

<span data-ttu-id="effdf-125">Verwenden Sie die Razor-Anweisung `@layout`, um ein Layout auf eine Komponente anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="effdf-125">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="effdf-126">Der Compiler konvertiert `@layout` in eine `LayoutAttribute`-Eigenschaft, die auf die Komponentenklasse angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="effdf-126">The compiler converts `@layout` into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="effdf-127">Der Inhalt der folgenden `MasterList`-Komponente wird bei der Position von `MasterLayout` in `@Body` eingefügt:</span><span class="sxs-lookup"><span data-stu-id="effdf-127">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="effdf-128">Durch das direkte Festlegen eines Layouts in einer Komponente wird das im Router oder mit einer aus *_Imports.razor* importierten `@layout`-Anweisung festgelegte *Standardlayout* überschrieben.</span><span class="sxs-lookup"><span data-stu-id="effdf-128">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from *_Imports.razor*.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="effdf-129">Zentrale Layoutauswahl</span><span class="sxs-lookup"><span data-stu-id="effdf-129">Centralized layout selection</span></span>

<span data-ttu-id="effdf-130">Alle Ordner einer App können optional eine Vorlagendatei namens *_Imports.razor* enthalten.</span><span class="sxs-lookup"><span data-stu-id="effdf-130">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="effdf-131">Der Compiler enthält die in der Importdatei angegebenen Anweisungen in allen Razor-Vorlagen im gleichen Ordner und rekursiv in allen Unterordnern.</span><span class="sxs-lookup"><span data-stu-id="effdf-131">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="effdf-132">Daher wird mit einer *_Imports.razor*-Datei, die `@layout MyCoolLayout` enthält, sichergestellt, dass alle Komponenten in einem Ordner `MyCoolLayout` verwenden.</span><span class="sxs-lookup"><span data-stu-id="effdf-132">Therefore, an *_Imports.razor* file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="effdf-133">`@layout MyCoolLayout` muss nicht wiederholt zu allen *RAZOR*-Dateien im Ordner und in den Unterordnern hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="effdf-133">There's no need to repeatedly add `@layout MyCoolLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="effdf-134">`@using`-Anweisungen werden auf die gleiche Weise auf Komponenten angewendet.</span><span class="sxs-lookup"><span data-stu-id="effdf-134">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="effdf-135">Die folgende Datei *_Imports.razor* importiert Folgendes:</span><span class="sxs-lookup"><span data-stu-id="effdf-135">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="effdf-136">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="effdf-136">`MyCoolLayout`.</span></span>
* <span data-ttu-id="effdf-137">Alle Razor-Komponenten im gleichen Ordner und in dessen Unterordnern</span><span class="sxs-lookup"><span data-stu-id="effdf-137">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="effdf-138">Der `BlazorApp1.Data` -Namespace.</span><span class="sxs-lookup"><span data-stu-id="effdf-138">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="effdf-139">Die Datei *_Imports.razor* ähnelt der Datei [_ViewImports.cshtml für Razor-Ansichten und -Seiten](xref:mvc/views/layout#importing-shared-directives), gilt aber spezifisch für Razor-Komponentendateien.</span><span class="sxs-lookup"><span data-stu-id="effdf-139">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="effdf-140">Durch das Festlegen eines Layouts in *_Imports.razor* wird ein Layout überschrieben, das als *Standardlayout* des Routers festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="effdf-140">Specifying a layout in *_Imports.razor* overrides a layout specified as the router's *default layout*.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="effdf-141">Geschachtelte Layouts</span><span class="sxs-lookup"><span data-stu-id="effdf-141">Nested layouts</span></span>

<span data-ttu-id="effdf-142">Apps können aus geschachtelten Layouts bestehen.</span><span class="sxs-lookup"><span data-stu-id="effdf-142">Apps can consist of nested layouts.</span></span> <span data-ttu-id="effdf-143">Eine Komponente kann auf ein Layout verweisen, das wiederum auf ein anderes Layout verweist.</span><span class="sxs-lookup"><span data-stu-id="effdf-143">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="effdf-144">Geschachtelte Layouts werden beispielsweise dazu verwendet, eine Menüstruktur mit mehreren Ebenen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="effdf-144">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="effdf-145">Im folgenden Beispiel wird die Verwendung von geschachtelten Layouts veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="effdf-145">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="effdf-146">Die Datei *EpisodesComponent.razor* ist die Komponente, die angezeigt werden soll.</span><span class="sxs-lookup"><span data-stu-id="effdf-146">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="effdf-147">Die Komponente verweist auf `MasterListLayout`:</span><span class="sxs-lookup"><span data-stu-id="effdf-147">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="effdf-148">Die Datei *MasterListLayout.razor* stellt `MasterListLayout` bereit.</span><span class="sxs-lookup"><span data-stu-id="effdf-148">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="effdf-149">Das Layout verweist auf ein anderes Layout (`MasterLayout`), in dem es gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="effdf-149">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="effdf-150">`EpisodesComponent` wird dort gerendert, wo `@Body` vorkommt:</span><span class="sxs-lookup"><span data-stu-id="effdf-150">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="effdf-151">Schließlich enthält `MasterLayout` in der Datei *MasterLayout.razor* die allgemeinen Layoutelemente, z. B. die Kopfzeile, das Hauptmenü und die Fußzeile.</span><span class="sxs-lookup"><span data-stu-id="effdf-151">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="effdf-152">Das Layout `MasterListLayout` mit der Komponente `EpisodesComponent` wird dort gerendert, wo `@Body` vorkommt:</span><span class="sxs-lookup"><span data-stu-id="effdf-152">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a><span data-ttu-id="effdf-153">Freigeben eines Razor Pages-Layouts mit integrierten Komponenten</span><span class="sxs-lookup"><span data-stu-id="effdf-153">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="effdf-154">Wenn routingfähige Komponenten in eine Razor Pages-App integriert werden, kann das freigegebene Layout der App mit den Komponenten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="effdf-154">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="effdf-155">Weitere Informationen finden Sie unter <xref:blazor/integrate-components>.</span><span class="sxs-lookup"><span data-stu-id="effdf-155">For more information, see <xref:blazor/integrate-components>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="effdf-156">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="effdf-156">Additional resources</span></span>

* <xref:mvc/views/layout>

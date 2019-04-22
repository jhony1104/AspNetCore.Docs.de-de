---
title: Hilfsprogramm für Teiltags in ASP.NET Core
author: scottaddie
description: Lernen Sie das ASP.NET Core-Hilfsprogramm für Teiltags und die Rolle seiner Attribute beim Rendern einer Teilansicht kennen.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 04/06/2019
uid: mvc/views/tag-helpers/builtin-th/partial-tag-helper
ms.openlocfilehash: 116fce7af5dc138fbbb0351a4f38f59e88c8f338
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59468681"
---
# <a name="partial-tag-helper-in-aspnet-core"></a><span data-ttu-id="d16bc-103">Hilfsprogramm für Teiltags in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d16bc-103">Partial Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="d16bc-104">Von [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="d16bc-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="d16bc-105">Eine Übersicht der Taghilfsprogramme finden Sie unter <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="d16bc-105">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="d16bc-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d16bc-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview"></a><span data-ttu-id="d16bc-107">Übersicht</span><span class="sxs-lookup"><span data-stu-id="d16bc-107">Overview</span></span>

<span data-ttu-id="d16bc-108">Das Hilfsprogramm für Teiltags wird für das Rendern einer [Teilansicht](xref:mvc/views/partial) auf Razor-Seiten und in MVC-Apps verwendet.</span><span class="sxs-lookup"><span data-stu-id="d16bc-108">The Partial Tag Helper is used for rendering a [partial view](xref:mvc/views/partial) in Razor Pages and MVC apps.</span></span> <span data-ttu-id="d16bc-109">Bedenken Sie dabei Folgendes:</span><span class="sxs-lookup"><span data-stu-id="d16bc-109">Consider that it:</span></span>

* <span data-ttu-id="d16bc-110">Das Programm erfordert ASP.NET Core 2.1 oder höher.</span><span class="sxs-lookup"><span data-stu-id="d16bc-110">Requires ASP.NET Core 2.1 or later.</span></span>
* <span data-ttu-id="d16bc-111">Es stellt eine Alternative zur [Syntax des HTML-Hilfsprogramms](xref:mvc/views/partial#reference-a-partial-view) dar.</span><span class="sxs-lookup"><span data-stu-id="d16bc-111">Is an alternative to [HTML Helper syntax](xref:mvc/views/partial#reference-a-partial-view).</span></span>
* <span data-ttu-id="d16bc-112">Es rendert die Teilansicht asynchron.</span><span class="sxs-lookup"><span data-stu-id="d16bc-112">Renders the partial view asynchronously.</span></span>

<span data-ttu-id="d16bc-113">Folgende zählen zu den Optionen des HTML-Hilfsprogramms für das Rendern einer Teilansicht:</span><span class="sxs-lookup"><span data-stu-id="d16bc-113">The HTML Helper options for rendering a partial view include:</span></span>

* [<span data-ttu-id="d16bc-114">@await Html.PartialAsync</span><span class="sxs-lookup"><span data-stu-id="d16bc-114">@await Html.PartialAsync</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partialasync)
* [<span data-ttu-id="d16bc-115">@await Html.RenderPartialAsync</span><span class="sxs-lookup"><span data-stu-id="d16bc-115">@await Html.RenderPartialAsync</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartialasync)
* [@Html.Partial](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partial)
* [@Html.RenderPartial](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartial)

<span data-ttu-id="d16bc-116">Das Modell *Product* wird in den Beispielen in diesem Dokument verwendet:</span><span class="sxs-lookup"><span data-stu-id="d16bc-116">The *Product* model is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Models/Product.cs)]

<span data-ttu-id="d16bc-117">Eine Auflistung der im Hilfsprogramm für Teiltags enthaltenen Attribute folgt.</span><span class="sxs-lookup"><span data-stu-id="d16bc-117">An inventory of the Partial Tag Helper attributes follows.</span></span>

## <a name="name"></a><span data-ttu-id="d16bc-118">Name</span><span class="sxs-lookup"><span data-stu-id="d16bc-118">name</span></span>

<span data-ttu-id="d16bc-119">Das `name`-Attribut ist erforderlich.</span><span class="sxs-lookup"><span data-stu-id="d16bc-119">The `name` attribute is required.</span></span> <span data-ttu-id="d16bc-120">Es gibt den Namen oder den Pfad der Teilansicht an, die gerendert werden soll.</span><span class="sxs-lookup"><span data-stu-id="d16bc-120">It indicates the name or the path of the partial view to be rendered.</span></span> <span data-ttu-id="d16bc-121">Wenn der Name einer Teilansicht bereitgestellt wird, wird der Prozess [Ansichtsermittlung](xref:mvc/views/overview#view-discovery) initiiert.</span><span class="sxs-lookup"><span data-stu-id="d16bc-121">When a partial view name is provided, the [view discovery](xref:mvc/views/overview#view-discovery) process is initiated.</span></span> <span data-ttu-id="d16bc-122">Dieser Prozess wird umgangen, wenn ein expliziter Pfad bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="d16bc-122">That process is bypassed when an explicit path is provided.</span></span> <span data-ttu-id="d16bc-123">Eine Übersicht über alle verfügbaren `name`-Werte finden Sie unter [Ermitteln von Teilansichten](xref:mvc/views/partial#partial-view-discovery).</span><span class="sxs-lookup"><span data-stu-id="d16bc-123">For all acceptable `name` values, see [Partial view discovery](xref:mvc/views/partial#partial-view-discovery).</span></span>

<span data-ttu-id="d16bc-124">Folgendes Markup verwendet einen expliziten Pfad, der angibt, dass *_ProductPartial.cshtml* aus dem Ordner *Shared* (Freigegeben) geladen werden soll.</span><span class="sxs-lookup"><span data-stu-id="d16bc-124">The following markup uses an explicit path, indicating that *_ProductPartial.cshtml* is to be loaded from the *Shared* folder.</span></span> <span data-ttu-id="d16bc-125">Wenn Sie das Attribut [for](#for) verwenden, wird an Modell zur Bindung an die Teilansicht übergeben.</span><span class="sxs-lookup"><span data-stu-id="d16bc-125">Using the [for](#for) attribute, a model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Name)]

## <a name="for"></a><span data-ttu-id="d16bc-126">for</span><span class="sxs-lookup"><span data-stu-id="d16bc-126">for</span></span>

<span data-ttu-id="d16bc-127">Das `for`-Attribut weist ein [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression)-Element zu, das für das aktuelle Modell ausgewertet werden soll.</span><span class="sxs-lookup"><span data-stu-id="d16bc-127">The `for` attribute assigns a [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) to be evaluated against the current model.</span></span> <span data-ttu-id="d16bc-128">Ein `ModelExpression`-Element leitet die `@Model.`-Syntax ab.</span><span class="sxs-lookup"><span data-stu-id="d16bc-128">A `ModelExpression` infers the `@Model.` syntax.</span></span> <span data-ttu-id="d16bc-129">`for="Product"` kann beispielsweise anstelle von `for="@Model.Product"` verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d16bc-129">For example, `for="Product"` can be used instead of `for="@Model.Product"`.</span></span> <span data-ttu-id="d16bc-130">Dieses Standardverhalten für die Ableitung kann überschrieben werden, indem Sie das `@`-Symbol zum Definieren eines Inlineausdrucks verwenden.</span><span class="sxs-lookup"><span data-stu-id="d16bc-130">This default inference behavior is overridden by using the `@` symbol to define an inline expression.</span></span>

<span data-ttu-id="d16bc-131">Folgendes Markup lädt *_ProductPartial.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d16bc-131">The following markup loads *_ProductPartial.cshtml*:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_For)]

<span data-ttu-id="d16bc-132">Die Teilansicht ist an die `Product`-Eigenschaft des zugehörigen Seitenmodells gebunden:</span><span class="sxs-lookup"><span data-stu-id="d16bc-132">The partial view is bound to the associated page model's `Product` property:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Product.cshtml.cs?highlight=8)]

## <a name="model"></a><span data-ttu-id="d16bc-133">model</span><span class="sxs-lookup"><span data-stu-id="d16bc-133">model</span></span>

<span data-ttu-id="d16bc-134">Das `model`-Attribut weist eine Modellinstanz zu, die an die Teilansicht übergeben werden soll.</span><span class="sxs-lookup"><span data-stu-id="d16bc-134">The `model` attribute assigns a model instance to pass to the partial view.</span></span> <span data-ttu-id="d16bc-135">Das `model`-Attribut kann nicht mit dem [for](#for)-Attribut verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d16bc-135">The `model` attribute can't be used with the [for](#for) attribute.</span></span>

<span data-ttu-id="d16bc-136">Im folgenden Markup wird ein neues `Product`-Objekt instanziiert und an das `model`-Attribut zur Bindung übergeben:</span><span class="sxs-lookup"><span data-stu-id="d16bc-136">In the following markup, a new `Product` object is instantiated and passed to the `model` attribute for binding:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Model)]

## <a name="view-data"></a><span data-ttu-id="d16bc-137">view-data</span><span class="sxs-lookup"><span data-stu-id="d16bc-137">view-data</span></span>

<span data-ttu-id="d16bc-138">Das `view-data`-Attribut weist ein [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary)-Element zu, das an die Teilansicht übergeben werden soll.</span><span class="sxs-lookup"><span data-stu-id="d16bc-138">The `view-data` attribute assigns a [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) to pass to the partial view.</span></span> <span data-ttu-id="d16bc-139">Folgendes Markup stellt die gesamte ViewData-Auflistung für die Teilansicht zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="d16bc-139">The following markup makes the entire ViewData collection accessible to the partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_ViewData&highlight=5-)]

<span data-ttu-id="d16bc-140">Im vorangehenden Code wird der Schlüsselwert `IsNumberReadOnly` auf `true` festgelegt und zur ViewData-Auflistung hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d16bc-140">In the preceding code, the `IsNumberReadOnly` key value is set to `true` and added to the ViewData collection.</span></span> <span data-ttu-id="d16bc-141">Folglich wird `ViewData["IsNumberReadOnly"]` innerhalb der folgenden Teilansicht zur Verfügung gestellt:</span><span class="sxs-lookup"><span data-stu-id="d16bc-141">Consequently, `ViewData["IsNumberReadOnly"]` is made accessible within the following partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Shared/_ProductViewDataPartial.cshtml?highlight=5)]

<span data-ttu-id="d16bc-142">In diesem Beispiel bestimmt der Wert von `ViewData["IsNumberReadOnly"]`, ob das Feld *Number* (Anzahl) schreibgeschützt sein soll.</span><span class="sxs-lookup"><span data-stu-id="d16bc-142">In this example, the value of `ViewData["IsNumberReadOnly"]` determines whether the *Number* field is displayed as read only.</span></span>

## <a name="migrate-from-an-html-helper"></a><span data-ttu-id="d16bc-143">Migrieren von einem HTML-Hilfsprogramm</span><span class="sxs-lookup"><span data-stu-id="d16bc-143">Migrate from an HTML Helper</span></span>

<span data-ttu-id="d16bc-144">Sehen Sie sich das folgende Beispiel für das Hilfsprogramm für asynchrone HTML an.</span><span class="sxs-lookup"><span data-stu-id="d16bc-144">Consider the following asynchronous HTML Helper example.</span></span> <span data-ttu-id="d16bc-145">Eine Sammlung von Produkten wird durchlaufen und dargestellt.</span><span class="sxs-lookup"><span data-stu-id="d16bc-145">A collection of products is iterated and displayed.</span></span> <span data-ttu-id="d16bc-146">Laut dem ersten Parameter der `PartialAsync`-Methode wird die Teilansicht *_ProductPartial.cshtml* geladen.</span><span class="sxs-lookup"><span data-stu-id="d16bc-146">Per the `PartialAsync` method's first parameter, the *_ProductPartial.cshtml* partial view is loaded.</span></span> <span data-ttu-id="d16bc-147">Eine Instanz des `Product`-Modells wird für die Bindung an die Teilansicht übergeben.</span><span class="sxs-lookup"><span data-stu-id="d16bc-147">An instance of the `Product` model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_HtmlHelper&highlight=3)]

<span data-ttu-id="d16bc-148">Das folgende Teiltaghilfsprogramm erreicht dasselbe asynchrone Renderingverhalten wie das `PartialAsync`-HTML-Hilfsprogramm.</span><span class="sxs-lookup"><span data-stu-id="d16bc-148">The following Partial Tag Helper achieves the same asynchronous rendering behavior as the `PartialAsync` HTML Helper.</span></span> <span data-ttu-id="d16bc-149">Dem `model`-Attribut wird eine `Product`-Modellinstanz zum Binden an die Teilansicht zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="d16bc-149">The `model` attribute is assigned a `Product` model instance for binding to the partial view.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_TagHelper&highlight=3)]

## <a name="additional-resources"></a><span data-ttu-id="d16bc-150">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d16bc-150">Additional resources</span></span>

* <xref:mvc/views/partial>
* <xref:mvc/views/overview#weakly-typed-data-viewdata-viewdata-attribute-and-viewbag>

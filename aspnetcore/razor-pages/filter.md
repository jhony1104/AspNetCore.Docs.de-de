---
title: Filtermethoden für Razor-Seiten in ASP-NET Core
author: Rick-Anderson
description: Erfahren Sie, wie Sie Filtermethoden für Razor-Seiten in ASP.NET Core erstellen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/05/2018
uid: razor-pages/filter
ms.openlocfilehash: 32613d75d966a698c6478234f3f5f9d5fc0628bc
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58264800"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a><span data-ttu-id="67162-103">Filtermethoden für Razor-Seiten in ASP-NET Core</span><span class="sxs-lookup"><span data-stu-id="67162-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="67162-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="67162-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="67162-105">Eine Razor-Seite filtert [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0). [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) erlaubt Razor-Seiten Code auszuführen, bevor und nachdem ein Handler für Razor-Seiten ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="67162-105">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="67162-106">Filter für Razor-Seiten ähneln [ASP.NET Core MVC-Aktionsfiltern](xref:mvc/controllers/filters#action-filters), sie können allerdings nicht auf einzelne Seitenhandlermethoden angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="67162-106">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span> 

<span data-ttu-id="67162-107">Filter für Razor-Seiten:</span><span class="sxs-lookup"><span data-stu-id="67162-107">Razor Page filters:</span></span>

* <span data-ttu-id="67162-108">Führen Code aus, nachdem eine Handlermethode ausgewählt wurde, aber bevor die Modellbindung erfolgt</span><span class="sxs-lookup"><span data-stu-id="67162-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="67162-109">Führen Code aus, bevor die Handlermethode ausgeführt wird, nachdem die Modellbindung abgeschlossen ist</span><span class="sxs-lookup"><span data-stu-id="67162-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="67162-110">Führen Code aus, nachdem die Handlermethode ausgeführt wird</span><span class="sxs-lookup"><span data-stu-id="67162-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="67162-111">Können auf einer Seite oder global implementiert werden</span><span class="sxs-lookup"><span data-stu-id="67162-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="67162-112">Können nicht auf seitenspezifische Handlermethoden angewendet werden</span><span class="sxs-lookup"><span data-stu-id="67162-112">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="67162-113">Code kann ausgeführt werden, bevor eine Handlermethode ausgeführt wird, indem man den Seitenkonstruktor oder Middleware benutzt, aber nur Filter für Razor-Seiten haben Zugriff auf [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span><span class="sxs-lookup"><span data-stu-id="67162-113">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="67162-114">Filter verfügen über einen Parameter, der von [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) abgeleitet ist, um Zugriff auf `HttpContext` zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="67162-114">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="67162-115">Das Beispiel [Implementieren eines Filterattributs](#ifa) fügt der Antwort z.B. einen Header hinzu. Dies kann nicht über Konstruktoren oder Middleware erfolgen.</span><span class="sxs-lookup"><span data-stu-id="67162-115">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="67162-116">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="67162-116">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="67162-117">Filter für Razor-Seiten bieten die folgenden Methoden, die global oder auf Seitenebene angewendet werden können:</span><span class="sxs-lookup"><span data-stu-id="67162-117">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="67162-118">Synchrone Methoden:</span><span class="sxs-lookup"><span data-stu-id="67162-118">Synchronous methods:</span></span>

  * <span data-ttu-id="67162-119">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Wird aufgerufen, nachdem eine Handlermethode ausgewählt wurde, aber vor dem Modell Bindung erfolgt.</span><span class="sxs-lookup"><span data-stu-id="67162-119">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="67162-120">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Vor die Handlermethode ausgeführt wird, nach der modellbindung abgeschlossen ist, aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="67162-120">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="67162-121">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Wird aufgerufen, nachdem die Handlermethode ausgeführt, bevor das Aktionsergebnis wird.</span><span class="sxs-lookup"><span data-stu-id="67162-121">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="67162-122">Asynchrone Methoden:</span><span class="sxs-lookup"><span data-stu-id="67162-122">Asynchronous methods:</span></span>

  * <span data-ttu-id="67162-123">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Asynchron aufgerufen, nachdem die Handlermethode ausgewählt wurde, aber bevor die modellbindung erfolgt.</span><span class="sxs-lookup"><span data-stu-id="67162-123">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="67162-124">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Asynchron aufgerufen, bevor die Ereignishandlermethode aufgerufen wird, nach Abschluss der modellbindung.</span><span class="sxs-lookup"><span data-stu-id="67162-124">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="67162-125">Implementieren Sie **entweder** die synchrone oder asynchrone Version einer Filterschnittstelle, aber nicht beide.</span><span class="sxs-lookup"><span data-stu-id="67162-125">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="67162-126">Das Framework prüft zuerst, ob der Filter die asynchrone Schnittstelle implementiert, und wenn dies der Fall ist, ruft es sie auf.</span><span class="sxs-lookup"><span data-stu-id="67162-126">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="67162-127">Wenn dies nicht der Fall ist, ruft es die Methode(n) der synchronen Schnittstelle auf.</span><span class="sxs-lookup"><span data-stu-id="67162-127">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="67162-128">Wenn beide Schnittstellen implementiert werden, werden nur die asynchronen Methoden aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="67162-128">If both interfaces are implemented, only the async methods are be called.</span></span> <span data-ttu-id="67162-129">Die gleiche Regel gilt für Überschreibungen in Seiten. Implementieren Sie die synchrone oder asynchrone Version der Überschreibung, nicht beide.</span><span class="sxs-lookup"><span data-stu-id="67162-129">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="67162-130">Globales implementieren von Filtern für Razor-Seiten</span><span class="sxs-lookup"><span data-stu-id="67162-130">Implement Razor Page filters globally</span></span>

<span data-ttu-id="67162-131">Mit dem folgenden Code wird das `IAsyncPageFilter`-Element implementiert:</span><span class="sxs-lookup"><span data-stu-id="67162-131">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="67162-132">Im vorhergehenden Code wird [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) nicht benötigt.</span><span class="sxs-lookup"><span data-stu-id="67162-132">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="67162-133">Es wird im Beispiel verwendet, um Überwachungsinformationen für die Anwendung bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="67162-133">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="67162-134">Der folgende Code aktiviert `SampleAsyncPageFilter` in der `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="67162-134">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="67162-135">Der folgende Code veranschaulicht die vollständige `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="67162-135">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="67162-136">Der folgende Code ruft `AddFolderApplicationModelConvention` auf, damit `SampleAsyncPageFilter` nur auf Seiten in */subFolder* angewendet wird:</span><span class="sxs-lookup"><span data-stu-id="67162-136">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="67162-137">Im folgenden Code wird `IPageFilter` synchron implementiert:</span><span class="sxs-lookup"><span data-stu-id="67162-137">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="67162-138">Der folgende Code aktiviert `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="67162-138">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

::: moniker range=">= aspnetcore-2.1"

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="67162-139">Implementieren von Filtern für Razor-Seiten durch Überschreiben von Filtermethoden</span><span class="sxs-lookup"><span data-stu-id="67162-139">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="67162-140">Der folgende Code überschreibt die synchronen Filter für Razor-Seiten:</span><span class="sxs-lookup"><span data-stu-id="67162-140">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

::: moniker-end

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="67162-141">Implementieren eines Filterattributs</span><span class="sxs-lookup"><span data-stu-id="67162-141">Implement a filter attribute</span></span>

<span data-ttu-id="67162-142">Der integrierte attributbasierte Filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) kann als Unterklasse definiert werden.</span><span class="sxs-lookup"><span data-stu-id="67162-142">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="67162-143">Der folgende Filter fügt der Antwort einen Header hinzu:</span><span class="sxs-lookup"><span data-stu-id="67162-143">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="67162-144">Der folgende Code wendet das Attribut `AddHeader` an:</span><span class="sxs-lookup"><span data-stu-id="67162-144">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="67162-145">Informationen zum Überschreiben der Reihenfolge finden Sie unter [Überschreiben der Standardreihenfolge](xref:mvc/controllers/filters#overriding-the-default-order).</span><span class="sxs-lookup"><span data-stu-id="67162-145">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="67162-146">Informationen zum Kurzschließen der Filterpipeline eines Filters finden Sie unter [Abbrechen und Kurzschließen](xref:mvc/controllers/filters#cancellation-and-short-circuiting).</span><span class="sxs-lookup"><span data-stu-id="67162-146">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="67162-147">Autorisieren eines Filterattributs</span><span class="sxs-lookup"><span data-stu-id="67162-147">Authorize filter attribute</span></span>

<span data-ttu-id="67162-148">Das Attribut [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) kann auf ein `PageModel` angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="67162-148">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

---
title: Filtermethoden für Razor-Seiten in ASP-NET Core
author: Rick-Anderson
description: Erfahren Sie, wie Sie Filtermethoden für Razor-Seiten in ASP.NET Core erstellen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
uid: razor-pages/filter
ms.openlocfilehash: a60b17685c6f836de7c0afcc5b89a9894fb8b28f
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447230"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a><span data-ttu-id="8a88e-103">Filtermethoden für Razor-Seiten in ASP-NET Core</span><span class="sxs-lookup"><span data-stu-id="8a88e-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8a88e-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8a88e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8a88e-105">Eine Razor-Seite filtert [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0). [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) erlaubt Razor-Seiten Code auszuführen, bevor und nachdem ein Handler für Razor-Seiten ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="8a88e-105">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="8a88e-106">Filter für Razor-Seiten ähneln [ASP.NET Core MVC-Aktionsfiltern](xref:mvc/controllers/filters#action-filters), sie können allerdings nicht auf einzelne Seitenhandlermethoden angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="8a88e-106">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="8a88e-107">Filter für Razor-Seiten:</span><span class="sxs-lookup"><span data-stu-id="8a88e-107">Razor Page filters:</span></span>

* <span data-ttu-id="8a88e-108">Führen Code aus, nachdem eine Handlermethode ausgewählt wurde, aber bevor die Modellbindung erfolgt</span><span class="sxs-lookup"><span data-stu-id="8a88e-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="8a88e-109">Führen Code aus, bevor die Handlermethode ausgeführt wird, nachdem die Modellbindung abgeschlossen ist</span><span class="sxs-lookup"><span data-stu-id="8a88e-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="8a88e-110">Führen Code aus, nachdem die Handlermethode ausgeführt wird</span><span class="sxs-lookup"><span data-stu-id="8a88e-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="8a88e-111">Können auf einer Seite oder global implementiert werden</span><span class="sxs-lookup"><span data-stu-id="8a88e-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="8a88e-112">Können nicht auf seitenspezifische Handlermethoden angewendet werden</span><span class="sxs-lookup"><span data-stu-id="8a88e-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="8a88e-113">Kann über von der [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (di) aufgefüllte konstruktorabhängigkeiten verfügen.</span><span class="sxs-lookup"><span data-stu-id="8a88e-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="8a88e-114">Weitere Informationen finden Sie unter [servicefilterattribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) und [typefilterattribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span><span class="sxs-lookup"><span data-stu-id="8a88e-114">For more information, see [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) and [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span></span>

<span data-ttu-id="8a88e-115">Wenngleich seitenkonstruktoren und Middleware das Ausführen von benutzerdefiniertem Code vor der Ausführung einer Handlermethode ermöglichen, ermöglichen nur Razor Page Filter den Zugriff auf <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> und die Seite.</span><span class="sxs-lookup"><span data-stu-id="8a88e-115">While page constructors and middleware enable executing custom code before a handler method executes, only Razor Page filters enable access to <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> and the page.</span></span> <span data-ttu-id="8a88e-116">Die Middleware hat Zugriff auf die `HttpContext`, jedoch nicht auf den "Seiten Kontext".</span><span class="sxs-lookup"><span data-stu-id="8a88e-116">Middleware has access to the `HttpContext`, but not to the "page context".</span></span> <span data-ttu-id="8a88e-117">Filter verfügen über einen <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> abgeleiteten Parameter, der Zugriff auf `HttpContext`ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="8a88e-117">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="8a88e-118">Das Beispiel [Implementieren eines Filterattributs](#ifa) fügt der Antwort z.B. einen Header hinzu. Dies kann nicht über Konstruktoren oder Middleware erfolgen.</span><span class="sxs-lookup"><span data-stu-id="8a88e-118">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="8a88e-119">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8a88e-119">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="8a88e-120">Filter für Razor-Seiten bieten die folgenden Methoden, die global oder auf Seitenebene angewendet werden können:</span><span class="sxs-lookup"><span data-stu-id="8a88e-120">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="8a88e-121">Synchrone Methoden:</span><span class="sxs-lookup"><span data-stu-id="8a88e-121">Synchronous methods:</span></span>

  * <span data-ttu-id="8a88e-122">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0): Wird aufgerufen, nachdem eine Handlermethode ausgewählt wurde, aber bevor die Modellbindung erfolgt</span><span class="sxs-lookup"><span data-stu-id="8a88e-122">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="8a88e-123">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0): Wird aufgerufen, bevor die Handlermethode ausgeführt wird, nachdem die Modellbindung abgeschlossen ist</span><span class="sxs-lookup"><span data-stu-id="8a88e-123">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="8a88e-124">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0): Wird aufgerufen, nachdem die Handlermethode ausgeführt wird, bevor das Aktionsergebnis angezeigt wird</span><span class="sxs-lookup"><span data-stu-id="8a88e-124">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="8a88e-125">Asynchrone Methoden:</span><span class="sxs-lookup"><span data-stu-id="8a88e-125">Asynchronous methods:</span></span>

  * <span data-ttu-id="8a88e-126">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0): Wird asynchron aufgerufen, nachdem die Handlermethode ausgewählt wurde, aber bevor die Modellbindung erfolgt</span><span class="sxs-lookup"><span data-stu-id="8a88e-126">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="8a88e-127">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0): Wird asynchron aufgerufen, bevor die Handlermethode aufgerufen wird, nachdem die Modellbindung abgeschlossen ist</span><span class="sxs-lookup"><span data-stu-id="8a88e-127">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="8a88e-128">Implementieren Sie **entweder** die synchrone oder asynchrone Version einer Filterschnittstelle, aber **nicht** beide.</span><span class="sxs-lookup"><span data-stu-id="8a88e-128">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="8a88e-129">Das Framework prüft zuerst, ob der Filter die asynchrone Schnittstelle implementiert, und wenn dies der Fall ist, ruft es sie auf.</span><span class="sxs-lookup"><span data-stu-id="8a88e-129">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="8a88e-130">Wenn dies nicht der Fall ist, ruft es die Methode(n) der synchronen Schnittstelle auf.</span><span class="sxs-lookup"><span data-stu-id="8a88e-130">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="8a88e-131">Wenn beide Schnittstellen implementiert werden, werden nur die Async-Methoden aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="8a88e-131">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="8a88e-132">Die gleiche Regel gilt für Überschreibungen in Seiten. Implementieren Sie die synchrone oder asynchrone Version der Überschreibung, nicht beide.</span><span class="sxs-lookup"><span data-stu-id="8a88e-132">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="8a88e-133">Globales implementieren von Filtern für Razor-Seiten</span><span class="sxs-lookup"><span data-stu-id="8a88e-133">Implement Razor Page filters globally</span></span>

<span data-ttu-id="8a88e-134">Mit dem folgenden Code wird das `IAsyncPageFilter`-Element implementiert:</span><span class="sxs-lookup"><span data-stu-id="8a88e-134">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="8a88e-135">Im vorangehenden Code ist `ProcessUserAgent.Write` vom Benutzer bereitgestellter Code, der mit der Zeichenfolge des Benutzer-Agents arbeitet.</span><span class="sxs-lookup"><span data-stu-id="8a88e-135">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="8a88e-136">Der folgende Code aktiviert `SampleAsyncPageFilter` in der `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="8a88e-136">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="8a88e-137">Im folgenden Code wird <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> aufgerufen, um die `SampleAsyncPageFilter` auf Seiten in */Movies*anzuwenden:</span><span class="sxs-lookup"><span data-stu-id="8a88e-137">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies*:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="8a88e-138">Im folgenden Code wird `IPageFilter` synchron implementiert:</span><span class="sxs-lookup"><span data-stu-id="8a88e-138">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="8a88e-139">Der folgende Code aktiviert `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="8a88e-139">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="8a88e-140">Implementieren von Filtern für Razor-Seiten durch Überschreiben von Filtermethoden</span><span class="sxs-lookup"><span data-stu-id="8a88e-140">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="8a88e-141">Der folgende Code überschreibt die asynchronen Razor Page-Filter:</span><span class="sxs-lookup"><span data-stu-id="8a88e-141">The following code overrides the asynchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="8a88e-142">Implementieren eines Filterattributs</span><span class="sxs-lookup"><span data-stu-id="8a88e-142">Implement a filter attribute</span></span>

<span data-ttu-id="8a88e-143">Der integrierte Attribut basierte Filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> Filter kann unter klassifiziert werden.</span><span class="sxs-lookup"><span data-stu-id="8a88e-143">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="8a88e-144">Der folgende Filter fügt der Antwort einen Header hinzu:</span><span class="sxs-lookup"><span data-stu-id="8a88e-144">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="8a88e-145">Der folgende Code wendet das Attribut `AddHeader` an:</span><span class="sxs-lookup"><span data-stu-id="8a88e-145">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="8a88e-146">Verwenden Sie ein Tool wie die Browser Entwicklertools, um die Header zu untersuchen.</span><span class="sxs-lookup"><span data-stu-id="8a88e-146">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="8a88e-147">Unter **Antwortheader** wird `author: Rick` angezeigt.</span><span class="sxs-lookup"><span data-stu-id="8a88e-147">Under **Response Headers**, `author: Rick` is displayed.</span></span>

<span data-ttu-id="8a88e-148">Informationen zum Überschreiben der Reihenfolge finden Sie unter [Überschreiben der Standardreihenfolge](xref:mvc/controllers/filters#overriding-the-default-order).</span><span class="sxs-lookup"><span data-stu-id="8a88e-148">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="8a88e-149">Informationen zum Kurzschließen der Filterpipeline eines Filters finden Sie unter [Abbrechen und Kurzschließen](xref:mvc/controllers/filters#cancellation-and-short-circuiting).</span><span class="sxs-lookup"><span data-stu-id="8a88e-149">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="8a88e-150">Autorisieren eines Filterattributs</span><span class="sxs-lookup"><span data-stu-id="8a88e-150">Authorize filter attribute</span></span>

<span data-ttu-id="8a88e-151">Das Attribut [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) kann auf ein `PageModel` angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="8a88e-151">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8a88e-152">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8a88e-152">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8a88e-153">Eine Razor-Seite filtert [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0). [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) erlaubt Razor-Seiten Code auszuführen, bevor und nachdem ein Handler für Razor-Seiten ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="8a88e-153">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="8a88e-154">Filter für Razor-Seiten ähneln [ASP.NET Core MVC-Aktionsfiltern](xref:mvc/controllers/filters#action-filters), sie können allerdings nicht auf einzelne Seitenhandlermethoden angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="8a88e-154">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="8a88e-155">Filter für Razor-Seiten:</span><span class="sxs-lookup"><span data-stu-id="8a88e-155">Razor Page filters:</span></span>

* <span data-ttu-id="8a88e-156">Führen Code aus, nachdem eine Handlermethode ausgewählt wurde, aber bevor die Modellbindung erfolgt</span><span class="sxs-lookup"><span data-stu-id="8a88e-156">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="8a88e-157">Führen Code aus, bevor die Handlermethode ausgeführt wird, nachdem die Modellbindung abgeschlossen ist</span><span class="sxs-lookup"><span data-stu-id="8a88e-157">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="8a88e-158">Führen Code aus, nachdem die Handlermethode ausgeführt wird</span><span class="sxs-lookup"><span data-stu-id="8a88e-158">Run code after the handler method executes.</span></span>
* <span data-ttu-id="8a88e-159">Können auf einer Seite oder global implementiert werden</span><span class="sxs-lookup"><span data-stu-id="8a88e-159">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="8a88e-160">Können nicht auf seitenspezifische Handlermethoden angewendet werden</span><span class="sxs-lookup"><span data-stu-id="8a88e-160">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="8a88e-161">Code kann ausgeführt werden, bevor eine Handlermethode ausgeführt wird, indem man den Seitenkonstruktor oder Middleware benutzt, aber nur Filter für Razor-Seiten haben Zugriff auf [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span><span class="sxs-lookup"><span data-stu-id="8a88e-161">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="8a88e-162">Filter verfügen über einen Parameter, der von [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) abgeleitet ist, um Zugriff auf `HttpContext` zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="8a88e-162">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="8a88e-163">Das Beispiel [Implementieren eines Filterattributs](#ifa) fügt der Antwort z.B. einen Header hinzu. Dies kann nicht über Konstruktoren oder Middleware erfolgen.</span><span class="sxs-lookup"><span data-stu-id="8a88e-163">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="8a88e-164">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8a88e-164">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="8a88e-165">Filter für Razor-Seiten bieten die folgenden Methoden, die global oder auf Seitenebene angewendet werden können:</span><span class="sxs-lookup"><span data-stu-id="8a88e-165">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="8a88e-166">Synchrone Methoden:</span><span class="sxs-lookup"><span data-stu-id="8a88e-166">Synchronous methods:</span></span>

  * <span data-ttu-id="8a88e-167">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0): Wird aufgerufen, nachdem eine Handlermethode ausgewählt wurde, aber bevor die Modellbindung erfolgt</span><span class="sxs-lookup"><span data-stu-id="8a88e-167">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="8a88e-168">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0): Wird aufgerufen, bevor die Handlermethode ausgeführt wird, nachdem die Modellbindung abgeschlossen ist</span><span class="sxs-lookup"><span data-stu-id="8a88e-168">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="8a88e-169">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0): Wird aufgerufen, nachdem die Handlermethode ausgeführt wird, bevor das Aktionsergebnis angezeigt wird</span><span class="sxs-lookup"><span data-stu-id="8a88e-169">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="8a88e-170">Asynchrone Methoden:</span><span class="sxs-lookup"><span data-stu-id="8a88e-170">Asynchronous methods:</span></span>

  * <span data-ttu-id="8a88e-171">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0): Wird asynchron aufgerufen, nachdem die Handlermethode ausgewählt wurde, aber bevor die Modellbindung erfolgt</span><span class="sxs-lookup"><span data-stu-id="8a88e-171">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="8a88e-172">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0): Wird asynchron aufgerufen, bevor die Handlermethode aufgerufen wird, nachdem die Modellbindung abgeschlossen ist</span><span class="sxs-lookup"><span data-stu-id="8a88e-172">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="8a88e-173">Implementieren Sie **entweder** die synchrone oder asynchrone Version einer Filterschnittstelle, aber nicht beide.</span><span class="sxs-lookup"><span data-stu-id="8a88e-173">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="8a88e-174">Das Framework prüft zuerst, ob der Filter die asynchrone Schnittstelle implementiert, und wenn dies der Fall ist, ruft es sie auf.</span><span class="sxs-lookup"><span data-stu-id="8a88e-174">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="8a88e-175">Wenn dies nicht der Fall ist, ruft es die Methode(n) der synchronen Schnittstelle auf.</span><span class="sxs-lookup"><span data-stu-id="8a88e-175">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="8a88e-176">Wenn beide Schnittstellen implementiert werden, werden nur die Async-Methoden aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="8a88e-176">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="8a88e-177">Die gleiche Regel gilt für Überschreibungen in Seiten. Implementieren Sie die synchrone oder asynchrone Version der Überschreibung, nicht beide.</span><span class="sxs-lookup"><span data-stu-id="8a88e-177">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="8a88e-178">Globales implementieren von Filtern für Razor-Seiten</span><span class="sxs-lookup"><span data-stu-id="8a88e-178">Implement Razor Page filters globally</span></span>

<span data-ttu-id="8a88e-179">Mit dem folgenden Code wird das `IAsyncPageFilter`-Element implementiert:</span><span class="sxs-lookup"><span data-stu-id="8a88e-179">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="8a88e-180">Im vorhergehenden Code wird [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) nicht benötigt.</span><span class="sxs-lookup"><span data-stu-id="8a88e-180">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="8a88e-181">Es wird im Beispiel verwendet, um Überwachungsinformationen für die Anwendung bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="8a88e-181">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="8a88e-182">Der folgende Code aktiviert `SampleAsyncPageFilter` in der `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="8a88e-182">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="8a88e-183">Der folgende Code veranschaulicht die vollständige `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="8a88e-183">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="8a88e-184">Der folgende Code ruft `AddFolderApplicationModelConvention` auf, damit `SampleAsyncPageFilter` nur auf Seiten in */subFolder* angewendet wird:</span><span class="sxs-lookup"><span data-stu-id="8a88e-184">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="8a88e-185">Im folgenden Code wird `IPageFilter` synchron implementiert:</span><span class="sxs-lookup"><span data-stu-id="8a88e-185">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="8a88e-186">Der folgende Code aktiviert `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="8a88e-186">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="8a88e-187">Implementieren von Filtern für Razor-Seiten durch Überschreiben von Filtermethoden</span><span class="sxs-lookup"><span data-stu-id="8a88e-187">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="8a88e-188">Der folgende Code überschreibt die synchronen Filter für Razor-Seiten:</span><span class="sxs-lookup"><span data-stu-id="8a88e-188">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="8a88e-189">Implementieren eines Filterattributs</span><span class="sxs-lookup"><span data-stu-id="8a88e-189">Implement a filter attribute</span></span>

<span data-ttu-id="8a88e-190">Der integrierte attributbasierte Filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) kann als Unterklasse definiert werden.</span><span class="sxs-lookup"><span data-stu-id="8a88e-190">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="8a88e-191">Der folgende Filter fügt der Antwort einen Header hinzu:</span><span class="sxs-lookup"><span data-stu-id="8a88e-191">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="8a88e-192">Der folgende Code wendet das Attribut `AddHeader` an:</span><span class="sxs-lookup"><span data-stu-id="8a88e-192">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="8a88e-193">Informationen zum Überschreiben der Reihenfolge finden Sie unter [Überschreiben der Standardreihenfolge](xref:mvc/controllers/filters#overriding-the-default-order).</span><span class="sxs-lookup"><span data-stu-id="8a88e-193">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="8a88e-194">Informationen zum Kurzschließen der Filterpipeline eines Filters finden Sie unter [Abbrechen und Kurzschließen](xref:mvc/controllers/filters#cancellation-and-short-circuiting).</span><span class="sxs-lookup"><span data-stu-id="8a88e-194">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="8a88e-195">Autorisieren eines Filterattributs</span><span class="sxs-lookup"><span data-stu-id="8a88e-195">Authorize filter attribute</span></span>

<span data-ttu-id="8a88e-196">Das Attribut [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) kann auf ein `PageModel` angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="8a88e-196">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

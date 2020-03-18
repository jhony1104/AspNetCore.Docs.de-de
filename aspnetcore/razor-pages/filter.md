---
title: Filtermethoden für Razor-Seiten in ASP-NET Core
author: Rick-Anderson
description: Erfahren Sie, wie Sie Filtermethoden für Razor-Seiten in ASP.NET Core erstellen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
uid: razor-pages/filter
ms.openlocfilehash: cd772da8ed565bc779d8c6bcc7c9949a0c1c7c60
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78648049"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a><span data-ttu-id="a5071-103">Filtermethoden für Razor-Seiten in ASP-NET Core</span><span class="sxs-lookup"><span data-stu-id="a5071-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a5071-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a5071-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a5071-105">Eine Razor-Seite filtert [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0). [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) erlaubt Razor-Seiten Code auszuführen, bevor und nachdem ein Handler für Razor-Seiten ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="a5071-105">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="a5071-106">Filter für Razor-Seiten ähneln [ASP.NET Core MVC-Aktionsfiltern](xref:mvc/controllers/filters#action-filters), sie können allerdings nicht auf einzelne Seitenhandlermethoden angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="a5071-106">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="a5071-107">Filter für Razor-Seiten:</span><span class="sxs-lookup"><span data-stu-id="a5071-107">Razor Page filters:</span></span>

* <span data-ttu-id="a5071-108">Führen Code aus, nachdem eine Handlermethode ausgewählt wurde, aber bevor die Modellbindung erfolgt</span><span class="sxs-lookup"><span data-stu-id="a5071-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="a5071-109">Führen Code aus, bevor die Handlermethode ausgeführt wird, nachdem die Modellbindung abgeschlossen ist</span><span class="sxs-lookup"><span data-stu-id="a5071-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="a5071-110">Führen Code aus, nachdem die Handlermethode ausgeführt wird</span><span class="sxs-lookup"><span data-stu-id="a5071-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="a5071-111">Können auf einer Seite oder global implementiert werden</span><span class="sxs-lookup"><span data-stu-id="a5071-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="a5071-112">Können nicht auf seitenspezifische Handlermethoden angewendet werden</span><span class="sxs-lookup"><span data-stu-id="a5071-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="a5071-113">Alle Konstruktorabhängigkeiten werden durch die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="a5071-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="a5071-114">Weitere Informationen finden Sie unter [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) und [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span><span class="sxs-lookup"><span data-stu-id="a5071-114">For more information, see [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) and [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span></span>

<span data-ttu-id="a5071-115">Während Seitenkonstruktoren und Middleware die Ausführung von benutzerdefiniertem Code vor der Ausführung einer Handlermethode ermöglichen, gestatten nur Razor Page-Filter den Zugriff auf <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> und die Seite.</span><span class="sxs-lookup"><span data-stu-id="a5071-115">While page constructors and middleware enable executing custom code before a handler method executes, only Razor Page filters enable access to <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> and the page.</span></span> <span data-ttu-id="a5071-116">Middleware hat Zugriff auf den `HttpContext`, aber nicht auf den „Seitenkontext“.</span><span class="sxs-lookup"><span data-stu-id="a5071-116">Middleware has access to the `HttpContext`, but not to the "page context".</span></span> <span data-ttu-id="a5071-117">Filter verfügen über einen Parameter, der von <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> abgeleitet ist, um Zugriff auf `HttpContext` zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="a5071-117">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="a5071-118">Das Beispiel [Implementieren eines Filterattributs](#ifa) fügt der Antwort z.B. einen Header hinzu. Dies kann nicht über Konstruktoren oder Middleware erfolgen.</span><span class="sxs-lookup"><span data-stu-id="a5071-118">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="a5071-119">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a5071-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a5071-120">Filter für Razor-Seiten bieten die folgenden Methoden, die global oder auf Seitenebene angewendet werden können:</span><span class="sxs-lookup"><span data-stu-id="a5071-120">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="a5071-121">Synchrone Methoden:</span><span class="sxs-lookup"><span data-stu-id="a5071-121">Synchronous methods:</span></span>

  * <span data-ttu-id="a5071-122">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0): Wird aufgerufen, nachdem eine Handlermethode ausgewählt wurde, aber bevor die Modellbindung erfolgt.</span><span class="sxs-lookup"><span data-stu-id="a5071-122">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="a5071-123">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0): Wird aufgerufen, bevor die Handlermethode ausgeführt wird, nachdem die Modellbindung abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="a5071-123">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="a5071-124">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0): Wird aufgerufen, nachdem die Handlermethode ausgeführt wird, bevor das Aktionsergebnis angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="a5071-124">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="a5071-125">Asynchrone Methoden:</span><span class="sxs-lookup"><span data-stu-id="a5071-125">Asynchronous methods:</span></span>

  * <span data-ttu-id="a5071-126">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0): Wird asynchron aufgerufen, nachdem die Handlermethode ausgewählt wurde, aber bevor die Modellbindung erfolgt.</span><span class="sxs-lookup"><span data-stu-id="a5071-126">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="a5071-127">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0): Wird asynchron aufgerufen, bevor die Handlermethode aufgerufen wird, nachdem die Modellbindung abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="a5071-127">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="a5071-128">Implementieren Sie **entweder** die synchrone oder asynchrone Version einer Filterschnittstelle, aber **nicht** beide.</span><span class="sxs-lookup"><span data-stu-id="a5071-128">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="a5071-129">Das Framework prüft zuerst, ob der Filter die asynchrone Schnittstelle implementiert, und wenn dies der Fall ist, ruft es sie auf.</span><span class="sxs-lookup"><span data-stu-id="a5071-129">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="a5071-130">Wenn dies nicht der Fall ist, ruft es die Methode(n) der synchronen Schnittstelle auf.</span><span class="sxs-lookup"><span data-stu-id="a5071-130">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="a5071-131">Wenn beide Schnittstellen implementiert werden, werden nur die asynchronen Methoden aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="a5071-131">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="a5071-132">Die gleiche Regel gilt für Überschreibungen in Seiten. Implementieren Sie die synchrone oder asynchrone Version der Überschreibung, nicht beide.</span><span class="sxs-lookup"><span data-stu-id="a5071-132">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="a5071-133">Globales implementieren von Filtern für Razor-Seiten</span><span class="sxs-lookup"><span data-stu-id="a5071-133">Implement Razor Page filters globally</span></span>

<span data-ttu-id="a5071-134">Mit dem folgenden Code wird das `IAsyncPageFilter`-Element implementiert:</span><span class="sxs-lookup"><span data-stu-id="a5071-134">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="a5071-135">Im vorhergehenden Code ist `ProcessUserAgent.Write` der vom Benutzer bereitgestellte Code, der mit der Benutzer-Agent-Zeichenfolge arbeitet.</span><span class="sxs-lookup"><span data-stu-id="a5071-135">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="a5071-136">Der folgende Code aktiviert `SampleAsyncPageFilter` in der `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="a5071-136">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="a5071-137">Der folgende Code ruft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> auf, damit `SampleAsyncPageFilter` nur auf Seiten in */Movies* angewendet wird:</span><span class="sxs-lookup"><span data-stu-id="a5071-137">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies*:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="a5071-138">Im folgenden Code wird `IPageFilter` synchron implementiert:</span><span class="sxs-lookup"><span data-stu-id="a5071-138">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="a5071-139">Der folgende Code aktiviert `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="a5071-139">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="a5071-140">Implementieren von Filtern für Razor-Seiten durch Überschreiben von Filtermethoden</span><span class="sxs-lookup"><span data-stu-id="a5071-140">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="a5071-141">Der folgende Code überschreibt die asynchronen Razor Page-Filter:</span><span class="sxs-lookup"><span data-stu-id="a5071-141">The following code overrides the asynchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="a5071-142">Implementieren eines Filterattributs</span><span class="sxs-lookup"><span data-stu-id="a5071-142">Implement a filter attribute</span></span>

<span data-ttu-id="a5071-143">Der integrierte attributbasierte Filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> kann als Unterklasse definiert werden.</span><span class="sxs-lookup"><span data-stu-id="a5071-143">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="a5071-144">Der folgende Filter fügt der Antwort einen Header hinzu:</span><span class="sxs-lookup"><span data-stu-id="a5071-144">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="a5071-145">Der folgende Code wendet das Attribut `AddHeader` an:</span><span class="sxs-lookup"><span data-stu-id="a5071-145">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="a5071-146">Verwenden Sie ein Tool wie die Browser-Entwicklertools, um die Header zu untersuchen.</span><span class="sxs-lookup"><span data-stu-id="a5071-146">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="a5071-147">Unter **Antwortheader** wird `author: Rick` angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a5071-147">Under **Response Headers**, `author: Rick` is displayed.</span></span>

<span data-ttu-id="a5071-148">Informationen zum Überschreiben der Reihenfolge finden Sie unter [Überschreiben der Standardreihenfolge](xref:mvc/controllers/filters#overriding-the-default-order).</span><span class="sxs-lookup"><span data-stu-id="a5071-148">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="a5071-149">Informationen zum Kurzschließen der Filterpipeline eines Filters finden Sie unter [Abbrechen und Kurzschließen](xref:mvc/controllers/filters#cancellation-and-short-circuiting).</span><span class="sxs-lookup"><span data-stu-id="a5071-149">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="a5071-150">Autorisieren eines Filterattributs</span><span class="sxs-lookup"><span data-stu-id="a5071-150">Authorize filter attribute</span></span>

<span data-ttu-id="a5071-151">Das Attribut [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) kann auf ein `PageModel` angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="a5071-151">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a5071-152">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a5071-152">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a5071-153">Eine Razor-Seite filtert [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0). [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) erlaubt Razor-Seiten Code auszuführen, bevor und nachdem ein Handler für Razor-Seiten ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="a5071-153">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="a5071-154">Filter für Razor-Seiten ähneln [ASP.NET Core MVC-Aktionsfiltern](xref:mvc/controllers/filters#action-filters), sie können allerdings nicht auf einzelne Seitenhandlermethoden angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="a5071-154">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="a5071-155">Filter für Razor-Seiten:</span><span class="sxs-lookup"><span data-stu-id="a5071-155">Razor Page filters:</span></span>

* <span data-ttu-id="a5071-156">Führen Code aus, nachdem eine Handlermethode ausgewählt wurde, aber bevor die Modellbindung erfolgt</span><span class="sxs-lookup"><span data-stu-id="a5071-156">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="a5071-157">Führen Code aus, bevor die Handlermethode ausgeführt wird, nachdem die Modellbindung abgeschlossen ist</span><span class="sxs-lookup"><span data-stu-id="a5071-157">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="a5071-158">Führen Code aus, nachdem die Handlermethode ausgeführt wird</span><span class="sxs-lookup"><span data-stu-id="a5071-158">Run code after the handler method executes.</span></span>
* <span data-ttu-id="a5071-159">Können auf einer Seite oder global implementiert werden</span><span class="sxs-lookup"><span data-stu-id="a5071-159">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="a5071-160">Können nicht auf seitenspezifische Handlermethoden angewendet werden</span><span class="sxs-lookup"><span data-stu-id="a5071-160">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="a5071-161">Code kann ausgeführt werden, bevor eine Handlermethode ausgeführt wird, indem man den Seitenkonstruktor oder Middleware benutzt, aber nur Filter für Razor-Seiten haben Zugriff auf [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span><span class="sxs-lookup"><span data-stu-id="a5071-161">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="a5071-162">Filter verfügen über einen Parameter, der von [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) abgeleitet ist, um Zugriff auf `HttpContext` zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="a5071-162">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="a5071-163">Das Beispiel [Implementieren eines Filterattributs](#ifa) fügt der Antwort z.B. einen Header hinzu. Dies kann nicht über Konstruktoren oder Middleware erfolgen.</span><span class="sxs-lookup"><span data-stu-id="a5071-163">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="a5071-164">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a5071-164">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a5071-165">Filter für Razor-Seiten bieten die folgenden Methoden, die global oder auf Seitenebene angewendet werden können:</span><span class="sxs-lookup"><span data-stu-id="a5071-165">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="a5071-166">Synchrone Methoden:</span><span class="sxs-lookup"><span data-stu-id="a5071-166">Synchronous methods:</span></span>

  * <span data-ttu-id="a5071-167">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0): Wird aufgerufen, nachdem eine Handlermethode ausgewählt wurde, aber bevor die Modellbindung erfolgt.</span><span class="sxs-lookup"><span data-stu-id="a5071-167">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="a5071-168">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0): Wird aufgerufen, bevor die Handlermethode ausgeführt wird, nachdem die Modellbindung abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="a5071-168">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="a5071-169">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0): Wird aufgerufen, nachdem die Handlermethode ausgeführt wird, bevor das Aktionsergebnis angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="a5071-169">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="a5071-170">Asynchrone Methoden:</span><span class="sxs-lookup"><span data-stu-id="a5071-170">Asynchronous methods:</span></span>

  * <span data-ttu-id="a5071-171">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0): Wird asynchron aufgerufen, nachdem die Handlermethode ausgewählt wurde, aber bevor die Modellbindung erfolgt.</span><span class="sxs-lookup"><span data-stu-id="a5071-171">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="a5071-172">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0): Wird asynchron aufgerufen, bevor die Handlermethode aufgerufen wird, nachdem die Modellbindung abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="a5071-172">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="a5071-173">Implementieren Sie **entweder** die synchrone oder asynchrone Version einer Filterschnittstelle, aber nicht beide.</span><span class="sxs-lookup"><span data-stu-id="a5071-173">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="a5071-174">Das Framework prüft zuerst, ob der Filter die asynchrone Schnittstelle implementiert, und wenn dies der Fall ist, ruft es sie auf.</span><span class="sxs-lookup"><span data-stu-id="a5071-174">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="a5071-175">Wenn dies nicht der Fall ist, ruft es die Methode(n) der synchronen Schnittstelle auf.</span><span class="sxs-lookup"><span data-stu-id="a5071-175">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="a5071-176">Wenn beide Schnittstellen implementiert werden, werden nur die asynchronen Methoden aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="a5071-176">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="a5071-177">Die gleiche Regel gilt für Überschreibungen in Seiten. Implementieren Sie die synchrone oder asynchrone Version der Überschreibung, nicht beide.</span><span class="sxs-lookup"><span data-stu-id="a5071-177">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="a5071-178">Globales implementieren von Filtern für Razor-Seiten</span><span class="sxs-lookup"><span data-stu-id="a5071-178">Implement Razor Page filters globally</span></span>

<span data-ttu-id="a5071-179">Mit dem folgenden Code wird das `IAsyncPageFilter`-Element implementiert:</span><span class="sxs-lookup"><span data-stu-id="a5071-179">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="a5071-180">Im vorhergehenden Code wird [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) nicht benötigt.</span><span class="sxs-lookup"><span data-stu-id="a5071-180">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="a5071-181">Es wird im Beispiel verwendet, um Überwachungsinformationen für die Anwendung bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="a5071-181">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="a5071-182">Der folgende Code aktiviert `SampleAsyncPageFilter` in der `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="a5071-182">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="a5071-183">Der folgende Code veranschaulicht die vollständige `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="a5071-183">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="a5071-184">Der folgende Code ruft `AddFolderApplicationModelConvention` auf, damit `SampleAsyncPageFilter` nur auf Seiten in */subFolder* angewendet wird:</span><span class="sxs-lookup"><span data-stu-id="a5071-184">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="a5071-185">Im folgenden Code wird `IPageFilter` synchron implementiert:</span><span class="sxs-lookup"><span data-stu-id="a5071-185">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="a5071-186">Der folgende Code aktiviert `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="a5071-186">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="a5071-187">Implementieren von Filtern für Razor-Seiten durch Überschreiben von Filtermethoden</span><span class="sxs-lookup"><span data-stu-id="a5071-187">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="a5071-188">Der folgende Code überschreibt die synchronen Filter für Razor-Seiten:</span><span class="sxs-lookup"><span data-stu-id="a5071-188">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="a5071-189">Implementieren eines Filterattributs</span><span class="sxs-lookup"><span data-stu-id="a5071-189">Implement a filter attribute</span></span>

<span data-ttu-id="a5071-190">Der integrierte attributbasierte Filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) kann als Unterklasse definiert werden.</span><span class="sxs-lookup"><span data-stu-id="a5071-190">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="a5071-191">Der folgende Filter fügt der Antwort einen Header hinzu:</span><span class="sxs-lookup"><span data-stu-id="a5071-191">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="a5071-192">Der folgende Code wendet das Attribut `AddHeader` an:</span><span class="sxs-lookup"><span data-stu-id="a5071-192">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="a5071-193">Informationen zum Überschreiben der Reihenfolge finden Sie unter [Überschreiben der Standardreihenfolge](xref:mvc/controllers/filters#overriding-the-default-order).</span><span class="sxs-lookup"><span data-stu-id="a5071-193">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="a5071-194">Informationen zum Kurzschließen der Filterpipeline eines Filters finden Sie unter [Abbrechen und Kurzschließen](xref:mvc/controllers/filters#cancellation-and-short-circuiting).</span><span class="sxs-lookup"><span data-stu-id="a5071-194">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="a5071-195">Autorisieren eines Filterattributs</span><span class="sxs-lookup"><span data-stu-id="a5071-195">Authorize filter attribute</span></span>

<span data-ttu-id="a5071-196">Das Attribut [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) kann auf ein `PageModel` angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="a5071-196">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

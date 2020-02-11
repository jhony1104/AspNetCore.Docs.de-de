---
title: Filter in ASP.NET Core
author: Rick-Anderson
description: Erfahren Sie, wie Filter funktionieren und wie Sie sie in ASP.NET Core verwenden.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
uid: mvc/controllers/filters
ms.openlocfilehash: c4bb9d5746e494106ead6ad5bbf972bbcc5a39f1
ms.sourcegitcommit: 0e21d4f8111743bcb205a2ae0f8e57910c3e8c25
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77034064"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="6ff89-103">Filter in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6ff89-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6ff89-104">Von [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="6ff89-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="6ff89-105">*Filter* ermöglichen es in ASP.NET Core, Code vor oder nach bestimmten Phasen der Anforderungsverarbeitungspipeline auszuführen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="6ff89-106">Integrierte Filter sind für folgende Aufgaben zuständig:</span><span class="sxs-lookup"><span data-stu-id="6ff89-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="6ff89-107">Autorisierung (der Zugriff auf Ressourcen, für die ein Benutzer nicht autorisiert ist, wird verhindert)</span><span class="sxs-lookup"><span data-stu-id="6ff89-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="6ff89-108">Zwischenspeicherung von Antworten (die Anforderungspipeline wird unterbrochen, damit eine zwischengespeicherte Antwort zurückgegeben wird)</span><span class="sxs-lookup"><span data-stu-id="6ff89-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="6ff89-109">Durch die Erstellung benutzerdefinierter Filter kann mit aktionsübergreifenden Problemen umgegangen werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="6ff89-110">Zu solchen übergreifenden Problemen gehören beispielsweise Fehlerbehandlung, Caching, Konfiguration, Autorisierung und Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="6ff89-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="6ff89-111">Mit Filtern lässt sich die Duplizierung von Code vermeiden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="6ff89-112">Sie können zum Beispiel die Fehlerbehandlung in einem Ausnahmefilter konsolidieren.</span><span class="sxs-lookup"><span data-stu-id="6ff89-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="6ff89-113">Dieses Dokument gilt für Razor Pages, API-Controller und Controller mit Ansichten.</span><span class="sxs-lookup"><span data-stu-id="6ff89-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="6ff89-114">Filter können nicht direkt mit [Razor-Komponenten](xref:blazor/components) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-114">Filters don't work directly with [Razor components](xref:blazor/components).</span></span> <span data-ttu-id="6ff89-115">Ein Filter kann nur indirekt Einfluss auf eine Komponente haben, wenn Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="6ff89-116">Die Komponente ist in eine Seite oder Ansicht eingebettet.</span><span class="sxs-lookup"><span data-stu-id="6ff89-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="6ff89-117">Die Seite oder der Controller/die Ansicht verwendet den Filter.</span><span class="sxs-lookup"><span data-stu-id="6ff89-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="6ff89-118">[Zeigen Sie ein Beispiel an, oder laden Sie es herunter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="6ff89-118">[View or download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="6ff89-119">Die Funktionsweise von Filtern</span><span class="sxs-lookup"><span data-stu-id="6ff89-119">How filters work</span></span>

<span data-ttu-id="6ff89-120">Filter werden in der *ASP.NET Core-Aktionsaufrufpipeline* ausgeführt, die manchmal auch als *Filterpipeline* bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="6ff89-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="6ff89-121">Die Filterpipeline wird ausgeführt, nachdem ASP.NET Core die auszuführende Aktion ausgewählt hat.</span><span class="sxs-lookup"><span data-stu-id="6ff89-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Die Anforderung wird von weiterer Middleware, Routingmiddleware, Aktionsauswahl und der Aktionsaufrufpipeline verarbeitet.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="6ff89-124">Filtertypen</span><span class="sxs-lookup"><span data-stu-id="6ff89-124">Filter types</span></span>

<span data-ttu-id="6ff89-125">Jeder Filtertyp wird in einer anderen Phase der Filterpipeline ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="6ff89-126">[Autorisierungsfilter](#authorization-filters) werden zuerst ausgeführt und dienen dazu, festzustellen, ob der Benutzer für die Anforderung berechtigt ist.</span><span class="sxs-lookup"><span data-stu-id="6ff89-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="6ff89-127">Autorisierungsfilter schließen die Pipeline kurz, wenn die Anforderung nicht autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="6ff89-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="6ff89-128">[Ressourcenfilter](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="6ff89-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="6ff89-129">Werden nach der Autorisierung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-129">Run after authorization.</span></span>  
  * <span data-ttu-id="6ff89-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> führt Code vor dem Rest der Filterpipeline aus.</span><span class="sxs-lookup"><span data-stu-id="6ff89-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="6ff89-131">Beispielsweise führt `OnResourceExecuting` Code vor der Modellbindung aus.</span><span class="sxs-lookup"><span data-stu-id="6ff89-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="6ff89-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> führt Code aus, nachdem der Rest der Pipeline abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="6ff89-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="6ff89-133">[Für Aktionsfilter gilt Folgendes](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="6ff89-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="6ff89-134">Sie führen Code unmittelbar vor und nach dem Aufruf einer Aktionsmethode aus.</span><span class="sxs-lookup"><span data-stu-id="6ff89-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="6ff89-135">Sie können die an eine Aktion übergebenen Argumente ändern.</span><span class="sxs-lookup"><span data-stu-id="6ff89-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="6ff89-136">Sie können das von der Aktion zurückgegebene Ergebnis ändern.</span><span class="sxs-lookup"><span data-stu-id="6ff89-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="6ff89-137">Sie werden in Razor Pages **nicht** unterstützt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="6ff89-138">[Ausnahmefilter](#exception-filters) wenden globale Richtlinien auf unbehandelte Ausnahmen an, die auftreten, bevor etwas in den Antworttext geschrieben wurde.</span><span class="sxs-lookup"><span data-stu-id="6ff89-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="6ff89-139">[Ergebnisfilter](#result-filters) führen Code unmittelbar vor und nach der Ausführung von Aktionsergebnissen aus.</span><span class="sxs-lookup"><span data-stu-id="6ff89-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="6ff89-140">Sie werden nur ausgeführt, wenn die Aktionsmethode erfolgreich ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="6ff89-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="6ff89-141">Ergebnisfilter sind hilfreich für Logik, die die Ausführung von Ansichten oder Formatierern umfasst.</span><span class="sxs-lookup"><span data-stu-id="6ff89-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="6ff89-142">Das folgende Diagramm veranschaulicht, wie die Filtertypen mit der Filterpipeline interagieren.</span><span class="sxs-lookup"><span data-stu-id="6ff89-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Die Anforderung wird von Autorisierungsfilter, Ressourcenfilter, Modellbindung, Aktionsfilter, Aktionsausführung sowie Aktionsergebniskonvertierung, Ausnahmefilter, Ergebnisfilter und Ergebnisausführung verarbeitet.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="6ff89-145">Implementierung</span><span class="sxs-lookup"><span data-stu-id="6ff89-145">Implementation</span></span>

<span data-ttu-id="6ff89-146">Filter unterstützen sowohl synchrone als auch asynchrone Implementierungen über verschiedene Schnittstellendefinitionen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="6ff89-147">Synchrone Filter führen Code vor und nach der jeweiligen Pipelinephase aus.</span><span class="sxs-lookup"><span data-stu-id="6ff89-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="6ff89-148"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> beispielsweise wird aufgerufen, bevor die Aktionsmethode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="6ff89-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="6ff89-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> wird nach der Rückgabe der Aktionsmethode aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="6ff89-150">Asynchrone Filter definieren eine `On-Stage-ExecutionAsync`-Methode.</span><span class="sxs-lookup"><span data-stu-id="6ff89-150">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="6ff89-151">Zum Beispiel <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span><span class="sxs-lookup"><span data-stu-id="6ff89-151">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="6ff89-152">Im oben stehenden Code weist der `SampleAsyncActionFilter` einen <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) auf, der die Aktionsmethode ausführt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-152">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="6ff89-153">Mehrere Filterphasen</span><span class="sxs-lookup"><span data-stu-id="6ff89-153">Multiple filter stages</span></span>

<span data-ttu-id="6ff89-154">Schnittstellen für mehrere Filterphasen können in einer einzigen Klasse implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-154">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="6ff89-155">Beispielsweise implementiert die <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>-Klasse:</span><span class="sxs-lookup"><span data-stu-id="6ff89-155">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="6ff89-156">Synchron: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> und <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="6ff89-156">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="6ff89-157">Asynchron: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> und <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="6ff89-157">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="6ff89-158">Implementieren Sie **entweder** die synchrone oder asynchrone Version einer Filterschnittstelle, aber **nicht** beide.</span><span class="sxs-lookup"><span data-stu-id="6ff89-158">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="6ff89-159">Die Runtime prüft zuerst, ob der Filter die asynchrone Schnittstelle implementiert, und wenn dies der Fall ist, ruft die Runtime diese Schnittstelle auf.</span><span class="sxs-lookup"><span data-stu-id="6ff89-159">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="6ff89-160">Wenn dies nicht der Fall ist, ruft es die Methode(n) der synchronen Schnittstelle auf.</span><span class="sxs-lookup"><span data-stu-id="6ff89-160">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="6ff89-161">Wenn die asynchrone und die synchrone Schnittstelle in einer einzigen Klasse implementiert sind, wird nur die asynchrone Methode aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-161">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="6ff89-162">Bei Verwendung von abstrakten Klassen wie <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> überschreiben Sie nur die synchronen Methoden oder die asynchrone Methode für jeden Filtertyp.</span><span class="sxs-lookup"><span data-stu-id="6ff89-162">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="6ff89-163">Integrierte Filterattribute</span><span class="sxs-lookup"><span data-stu-id="6ff89-163">Built-in filter attributes</span></span>

<span data-ttu-id="6ff89-164">ASP.NET Core enthält integrierte attributbasierte Filter, die als Unterklasse erstellt und angepasst werden können.</span><span class="sxs-lookup"><span data-stu-id="6ff89-164">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="6ff89-165">Zum Beispiel fügt der folgende Ergebnisfilter der Antwort einen Header hinzu:</span><span class="sxs-lookup"><span data-stu-id="6ff89-165">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="6ff89-166">Wie im obigen Beispiel gezeigt, können Filter mithilfe von Attributen Argumente akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="6ff89-166">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="6ff89-167">Wenden Sie das `AddHeaderAttribute` auf einen Controller oder eine Aktionsmethode an, und geben Sie den Namen und den Wert des HTTP-Headers an:</span><span class="sxs-lookup"><span data-stu-id="6ff89-167">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="6ff89-168">Verwenden Sie ein Tool wie die [Browser-Entwicklertools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools), um die Header zu untersuchen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-168">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="6ff89-169">Unter **Antwortheader** wird `author: Rick Anderson` angezeigt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-169">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="6ff89-170">Mit dem folgenden Code wird ein `ActionFilterAttribute`-Element implementiert, das diese Aktionen ausführt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-170">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="6ff89-171">Liest den Titel und den Namen aus dem Konfigurationssystem.</span><span class="sxs-lookup"><span data-stu-id="6ff89-171">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="6ff89-172">Im Gegensatz zum vorherigen Beispiel erfordert der folgende Code nicht, dass dem Code Filterparameter hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-172">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="6ff89-173">Fügt dem Antwortheader den Titel und den Namen hinzu.</span><span class="sxs-lookup"><span data-stu-id="6ff89-173">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="6ff89-174">Die Konfigurationsoptionen werden vom [Konfigurationssystem](xref:fundamentals/configuration/index) mithilfe der [Optionsmuster](xref:fundamentals/configuration/options) bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-174">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="6ff89-175">Beispielsweise aus der Datei *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="6ff89-175">For example, from the *appsettings.json* file:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="6ff89-176">In `StartUp.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6ff89-176">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="6ff89-177">Die `PositionOptions`-Klasse wird dem Dienstcontainer mit dem Konfigurationsbereich `"Position"` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-177">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="6ff89-178">`MyActionFilterAttribute` wird dem Dienstcontainer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-178">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="6ff89-179">Der folgende Code zeigt die `PositionOptions`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="6ff89-179">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="6ff89-180">Der folgende Code wendet das `MyActionFilterAttribute` auf die Methode `Index2` an:</span><span class="sxs-lookup"><span data-stu-id="6ff89-180">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="6ff89-181">Unter **Antwortheader** werden `author: Rick Anderson` und `Editor: Joe Smith` angezeigt, wenn der `Sample/Index2`-Endpunkt aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="6ff89-181">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="6ff89-182">Der folgende Code wendet das `MyActionFilterAttribute` und das `AddHeaderAttribute` auf die Razor Page an:</span><span class="sxs-lookup"><span data-stu-id="6ff89-182">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="6ff89-183">Es können keine Filter auf Handlermethoden von Razor Pages angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-183">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="6ff89-184">Sie können entweder auf das Razor Page-Modell oder global angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-184">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="6ff89-185">Einige der Filterschnittstellen besitzen entsprechende Attribute, die als Basisklassen für benutzerdefinierte Implementierungen verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="6ff89-185">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="6ff89-186">Filterattribute:</span><span class="sxs-lookup"><span data-stu-id="6ff89-186">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="6ff89-187">Filterbereiche und Reihenfolge der Ausführung</span><span class="sxs-lookup"><span data-stu-id="6ff89-187">Filter scopes and order of execution</span></span>

<span data-ttu-id="6ff89-188">Der Pipeline kann in einem von drei *Bereichen* ein Filter hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="6ff89-188">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="6ff89-189">Verwenden eines Attributs für eine Controller-Aktion.</span><span class="sxs-lookup"><span data-stu-id="6ff89-189">Using an attribute on a controller action.</span></span> <span data-ttu-id="6ff89-190">Es können keine Filterattribute auf Handlermethoden von Razor Pages angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-190">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="6ff89-191">Verwenden eines Attributs für einen Controller oder eine Razor Page.</span><span class="sxs-lookup"><span data-stu-id="6ff89-191">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="6ff89-192">Global für alle Controller und Aktionen, Razor Pages wie im folgenden Code gezeigt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-192">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="6ff89-193">Standardreihenfolge der Ausführung</span><span class="sxs-lookup"><span data-stu-id="6ff89-193">Default order of execution</span></span>

<span data-ttu-id="6ff89-194">Wenn es mehrere Filter für eine bestimmte Stufe der Pipeline gibt, bestimmt der Bereich die Standardreihenfolge der Filterausführung.</span><span class="sxs-lookup"><span data-stu-id="6ff89-194">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="6ff89-195">Globale Filter umfassen Klassenfilter, welche hingegen Methodenfilter umfassen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-195">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="6ff89-196">Als Ergebnis der Filterschachtelung wird der *nach* einer Pipelinephase auszuführende Code in umgekehrter Reihenfolge zum Code *vor* einer Pipelinephase ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-196">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="6ff89-197">Filterreihenfolge:</span><span class="sxs-lookup"><span data-stu-id="6ff89-197">The filter sequence:</span></span>

* <span data-ttu-id="6ff89-198">Der Code von globalen Filtern, der *vor* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="6ff89-198">The *before* code of global filters.</span></span>
  * <span data-ttu-id="6ff89-199">Der Code von Controllerfiltern und Razor Page-Filtern, der *vor* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="6ff89-199">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="6ff89-200">Der Code von Aktionsmethodenfiltern, der *vor* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="6ff89-200">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="6ff89-201">Der Code von Aktionsmethodenfiltern, der *nach* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="6ff89-201">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="6ff89-202">Der Code von Controllerfiltern und Razor Page-Filtern, der *nach* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="6ff89-202">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="6ff89-203">Der Code von globalen Filtern, der *nach* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="6ff89-203">The *after* code of global filters.</span></span>
  
<span data-ttu-id="6ff89-204">Das folgende Beispiel veranschaulicht die Reihenfolge, in der Filtermethoden für synchrone Aktionsfilter aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-204">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="6ff89-205">Sequenz</span><span class="sxs-lookup"><span data-stu-id="6ff89-205">Sequence</span></span> | <span data-ttu-id="6ff89-206">Filterbereich</span><span class="sxs-lookup"><span data-stu-id="6ff89-206">Filter scope</span></span> | <span data-ttu-id="6ff89-207">Filtermethode</span><span class="sxs-lookup"><span data-stu-id="6ff89-207">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="6ff89-208">1</span><span class="sxs-lookup"><span data-stu-id="6ff89-208">1</span></span> | <span data-ttu-id="6ff89-209">Global</span><span class="sxs-lookup"><span data-stu-id="6ff89-209">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="6ff89-210">2</span><span class="sxs-lookup"><span data-stu-id="6ff89-210">2</span></span> | <span data-ttu-id="6ff89-211">Controller oder Razor Page</span><span class="sxs-lookup"><span data-stu-id="6ff89-211">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="6ff89-212">3</span><span class="sxs-lookup"><span data-stu-id="6ff89-212">3</span></span> | <span data-ttu-id="6ff89-213">Methode</span><span class="sxs-lookup"><span data-stu-id="6ff89-213">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="6ff89-214">4</span><span class="sxs-lookup"><span data-stu-id="6ff89-214">4</span></span> | <span data-ttu-id="6ff89-215">Methode</span><span class="sxs-lookup"><span data-stu-id="6ff89-215">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="6ff89-216">5</span><span class="sxs-lookup"><span data-stu-id="6ff89-216">5</span></span> | <span data-ttu-id="6ff89-217">Controller oder Razor Page</span><span class="sxs-lookup"><span data-stu-id="6ff89-217">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="6ff89-218">6</span><span class="sxs-lookup"><span data-stu-id="6ff89-218">6</span></span> | <span data-ttu-id="6ff89-219">Global</span><span class="sxs-lookup"><span data-stu-id="6ff89-219">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="6ff89-220">Filter auf Controllerebene</span><span class="sxs-lookup"><span data-stu-id="6ff89-220">Controller level filters</span></span>

<span data-ttu-id="6ff89-221">Jeder Controller, der von der Basisklasse <xref:Microsoft.AspNetCore.Mvc.Controller> erbt, enthält die Methoden [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) und [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="6ff89-221">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="6ff89-222">Diese Methoden führen Folgendes aus:</span><span class="sxs-lookup"><span data-stu-id="6ff89-222">These methods:</span></span>

* <span data-ttu-id="6ff89-223">Die Filter, die für eine bestimmte Aktion ausgeführt werden, werden umschlossen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-223">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="6ff89-224">`OnActionExecuting` wird vor allen Filtern der Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-224">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="6ff89-225">`OnActionExecuted` wird nach allen Filtern der Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-225">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="6ff89-226">`OnActionExecutionAsync` wird vor allen Filtern der Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-226">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="6ff89-227">Code im Filter nach `next` wird nach der Aktionsmethode ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-227">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="6ff89-228">Im Downloadbeispiel wird `MySampleActionFilter` global beim Start angewendet.</span><span class="sxs-lookup"><span data-stu-id="6ff89-228">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="6ff89-229">Die `TestController`:</span><span class="sxs-lookup"><span data-stu-id="6ff89-229">The `TestController`:</span></span>

* <span data-ttu-id="6ff89-230">Wendet das `SampleActionFilterAttribute` (`[SampleActionFilter]`) auf die Aktion `FilterTest2` an.</span><span class="sxs-lookup"><span data-stu-id="6ff89-230">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="6ff89-231">Überschreibt `OnActionExecuting` und `OnActionExecuted`:</span><span class="sxs-lookup"><span data-stu-id="6ff89-231">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="6ff89-232">Durch Navigieren zu `https://localhost:5001/Test2/FilterTest2` wird der folgende Code ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-232">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="6ff89-233">Filter auf Controllerebene legen die Eigenschaft [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) auf `int.MinValue` fest.</span><span class="sxs-lookup"><span data-stu-id="6ff89-233">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="6ff89-234">Für Filter auf Controllerebene kann die Ausführung nach Filtern, die auf Methoden angewendet werden **nicht** festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-234">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="6ff89-235">Die Reihenfolge wird im nächsten Abschnitt erläutert.</span><span class="sxs-lookup"><span data-stu-id="6ff89-235">Order is explained in the next section.</span></span>

<span data-ttu-id="6ff89-236">Informationen zu Razor Pages finden Sie unter [Implementieren von Filtern für Razor-Seiten durch Überschreiben von Filtermethoden](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="6ff89-236">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="6ff89-237">Überschreiben der Standardreihenfolge</span><span class="sxs-lookup"><span data-stu-id="6ff89-237">Overriding the default order</span></span>

<span data-ttu-id="6ff89-238">Die Standardreihenfolge der Ausführung kann durch Implementieren von <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-238">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="6ff89-239">`IOrderedFilter` macht die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> verfügbar, die bei der Bestimmung der Ausführungsreihenfolge Vorrang vor dem Bereich hat.</span><span class="sxs-lookup"><span data-stu-id="6ff89-239">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="6ff89-240">Für einen Filter mit geringerem `Order`-Wert gilt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-240">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="6ff89-241">Führt den Code, der *vor* einer Pipelinephase ausgeführt werden soll, vor dem Code eines Filters mit einem höheren Wert für `Order` aus.</span><span class="sxs-lookup"><span data-stu-id="6ff89-241">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="6ff89-242">Führt den Code, der *nach* einer Pipelinephase ausgeführt werden soll, nach dem Code eines Filters mit einem höheren Wert für `Order` aus.</span><span class="sxs-lookup"><span data-stu-id="6ff89-242">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="6ff89-243">Die Eigenschaft `Order` wird mit einem Konstruktorparameter festgelegt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-243">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="6ff89-244">Sehen Sie sich die zwei Aktionsfilter im folgenden Controller an:</span><span class="sxs-lookup"><span data-stu-id="6ff89-244">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="6ff89-245">In `StartUp.ConfigureServices` wird ein globaler Filter hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-245">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="6ff89-246">Die 3 Filter werden in der folgenden Reihenfolge ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-246">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="6ff89-247">Die Eigenschaft `Order` hat bei der Bestimmung der Reihenfolge, in der Filter ausgeführt werden, Vorrang vor dem Bereich.</span><span class="sxs-lookup"><span data-stu-id="6ff89-247">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="6ff89-248">Filter werden erst nach der Reihenfolge sortiert, und dann werden Gleichstände über den Bereich aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="6ff89-248">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="6ff89-249">Alle integrierten Filter implementieren `IOrderedFilter` und legen den Standartwert von `Order` auf 0 fest.</span><span class="sxs-lookup"><span data-stu-id="6ff89-249">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="6ff89-250">Wie zuvor erwähnt, legen Filter auf Controllerebene die [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17)-Eigenschaft auf `int.MinValue` fest. Bei integrierten Filtern bestimmt der Umfang die Reihenfolge, es sei denn, `Order` ist auf einen Wert ungleich null festgelegt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-250">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="6ff89-251">Im vorstehenden Code hat `MySampleActionFilter` globalen Umfang und wird daher vor `MyAction2FilterAttribute` ausgeführt, der Controllerumfang aufweist.</span><span class="sxs-lookup"><span data-stu-id="6ff89-251">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="6ff89-252">Um `MyAction2FilterAttribute` zuerst ausführen zu lassen, legen Sie die Reihenfolge auf `int.MinValue` fest:</span><span class="sxs-lookup"><span data-stu-id="6ff89-252">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="6ff89-253">Um zuerst den globalen Filter `MySampleActionFilter` ausführen zu lassen, legen Sie `Order` auf `int.MinValue` fest:</span><span class="sxs-lookup"><span data-stu-id="6ff89-253">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="6ff89-254">Abbrechen und Kurzschließen</span><span class="sxs-lookup"><span data-stu-id="6ff89-254">Cancellation and short-circuiting</span></span>

<span data-ttu-id="6ff89-255">Die Filterpipeline kann kurzgeschlossen werden, indem die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> auf den Parameter <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> festgelegt wird, der in der Filtermethode angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="6ff89-255">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="6ff89-256">Zum Beispiel verhindert der folgende Ressourcenfilter, dass der Rest der Pipeline ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="6ff89-256">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="6ff89-257">Im folgenden Code verwenden sowohl der Filter `ShortCircuitingResourceFilter` und der Filter `AddHeader` die Aktionsmethode `SomeResource` als Ziel.</span><span class="sxs-lookup"><span data-stu-id="6ff89-257">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="6ff89-258">Die `ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="6ff89-258">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="6ff89-259">Der Filter wird zuerst ausgeführt, da es sich um einen Ressourcenfilter handelt und `AddHeader` ein Aktionsfilter ist.</span><span class="sxs-lookup"><span data-stu-id="6ff89-259">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="6ff89-260">Der Filter unterbricht alle verbleibenden Pipelineschritte.</span><span class="sxs-lookup"><span data-stu-id="6ff89-260">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="6ff89-261">Der `AddHeader`-Filter wird daher nie für die `SomeResource`-Aktion ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-261">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="6ff89-262">Dasselbe Verhalten würde auch dann auftreten, wenn beide Filter auf Aktionsmethodenebene angewendet würden, wenn `ShortCircuitingResourceFilter` zuerst ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="6ff89-262">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="6ff89-263">Der `ShortCircuitingResourceFilter` wird aufgrund seines Filtertyps oder durch die explizite Verwendung der `Order`-Eigenschaft zuerst ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-263">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="6ff89-264">Dependency Injection</span><span class="sxs-lookup"><span data-stu-id="6ff89-264">Dependency injection</span></span>

<span data-ttu-id="6ff89-265">Filter können nach Typ oder Instanz hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-265">Filters can be added by type or by instance.</span></span> <span data-ttu-id="6ff89-266">Wenn eine Instanz hinzugefügt wird, wird diese Instanz für jede Anforderung verwendet.</span><span class="sxs-lookup"><span data-stu-id="6ff89-266">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="6ff89-267">Wenn ein Typ hinzugefügt wird, ist der Filter typaktiviert.</span><span class="sxs-lookup"><span data-stu-id="6ff89-267">If a type is added, it's type-activated.</span></span> <span data-ttu-id="6ff89-268">Ein typaktivierter Filter bedeutet Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6ff89-268">A type-activated filter means:</span></span>

* <span data-ttu-id="6ff89-269">Für jede Anforderung wird eine Instanz erstellt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-269">An instance is created for each request.</span></span>
* <span data-ttu-id="6ff89-270">Alle Konstruktorabhängigkeiten werden durch [Dependency Injection](xref:fundamentals/dependency-injection) (DI) aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-270">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="6ff89-271">Filter, die als Attribute implementiert und Controllerklassen oder Aktionsmethoden direkt hinzugefügt werden, können keine Konstruktorabhängigkeiten von [Dependency Injection](xref:fundamentals/dependency-injection) (DI) erhalten.</span><span class="sxs-lookup"><span data-stu-id="6ff89-271">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="6ff89-272">Konstruktorabhängigkeiten können aus folgenden Gründen von DI nicht bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="6ff89-272">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="6ff89-273">Für Attribute müssen Konstruktorparameter bereitgestellt werden, wenn sie angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-273">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="6ff89-274">Dies ist eine Einschränkung der Funktionsweise von Attributen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-274">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="6ff89-275">Die folgenden Filter unterstützen von DI bereitgestellte Konstruktorabhängigkeiten:</span><span class="sxs-lookup"><span data-stu-id="6ff89-275">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="6ff89-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>, im Attribut implementiert</span><span class="sxs-lookup"><span data-stu-id="6ff89-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="6ff89-277">Die oben genannten Filter können auf einen Controller oder eine Aktionsmethode angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="6ff89-277">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="6ff89-278">DI bietet Protokollierungen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-278">Loggers are available from DI.</span></span> <span data-ttu-id="6ff89-279">Vermeiden Sie es jedoch, Filter nur zum Zweck der Protokollierung zu erstellen und zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-279">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="6ff89-280">Die integrierte [Frameworkprotokollierung](xref:fundamentals/logging/index) bietet in der Regel alle Elemente, die für die Protokollierung erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="6ff89-280">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="6ff89-281">Wenn Protokollierung zu Filtern hinzugefügt wird, gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6ff89-281">Logging added to filters:</span></span>

* <span data-ttu-id="6ff89-282">Die Protokollierung sollte mit Schwerpunkt auf geschäftlichen Aspekten oder verhaltensspezifisch für den Filter erfolgen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-282">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="6ff89-283">Aktionen oder andere Frameworkereignisse sollten **nicht** protokolliert werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-283">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="6ff89-284">Die integrierten Filter protokollieren Aktionen und Frameworkereignisse.</span><span class="sxs-lookup"><span data-stu-id="6ff89-284">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="6ff89-285">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="6ff89-285">ServiceFilterAttribute</span></span>

<span data-ttu-id="6ff89-286">Die Typen der Dienstfilterimplementierung werden in `ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="6ff89-286">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="6ff89-287">Ein <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> ruft eine Instanz des Filter von DI ab.</span><span class="sxs-lookup"><span data-stu-id="6ff89-287">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="6ff89-288">Der folgende Code zeigt den `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="6ff89-288">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="6ff89-289">Im folgenden Code wird `AddHeaderResultServiceFilter` zum DI-Container hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-289">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="6ff89-290">Im folgenden Code ruft das `ServiceFilter`-Attribut eine Instanz des `AddHeaderResultServiceFilter`-Filters aus DI ab:</span><span class="sxs-lookup"><span data-stu-id="6ff89-290">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="6ff89-291">Beim Verwenden von `ServiceFilterAttribute` wird [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) festgelegt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-291">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="6ff89-292">Gibt einen Hinweis darauf, dass die Filterinstanz *möglicherweise* außerhalb des Anforderungsbereichs, in dem sie erstellt wurde, wiederverwendet wird.</span><span class="sxs-lookup"><span data-stu-id="6ff89-292">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="6ff89-293">Die ASP.NET Core-Runtime bietet keine Garantie für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6ff89-293">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="6ff89-294">Eine einzelne Instanz des Filters wird erstellt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-294">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="6ff89-295">Der Filter wird nicht zu einem späteren Zeitpunkt vom DI-Container erneut angefordert.</span><span class="sxs-lookup"><span data-stu-id="6ff89-295">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="6ff89-296">Sollte nicht mit einem Filter verwendet werden, der von Diensten mit einer anderen Lebensdauer als der eines Singletons abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="6ff89-296">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="6ff89-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementiert <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="6ff89-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="6ff89-298">`IFilterFactory` macht die <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*>-Methode zum Erstellen einer <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>-Instanz verfügbar.</span><span class="sxs-lookup"><span data-stu-id="6ff89-298">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="6ff89-299">`CreateInstance` lädt den angegebenen Typ aus DI.</span><span class="sxs-lookup"><span data-stu-id="6ff89-299">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="6ff89-300">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="6ff89-300">TypeFilterAttribute</span></span>

<span data-ttu-id="6ff89-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> ähnelt <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>. Der zugehörige Typ wird allerdings nicht direkt vom DI-Container aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="6ff89-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="6ff89-302">Stattdessen wird der Typ mithilfe von <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> instanziiert.</span><span class="sxs-lookup"><span data-stu-id="6ff89-302">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="6ff89-303">Da `TypeFilterAttribute`-Typen nicht direkt vom DI-Container aufgelöst werden, gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6ff89-303">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="6ff89-304">Typen, auf die mithilfe von `TypeFilterAttribute` verwiesen wird, müssen nicht beim DI-Container registriert werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-304">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="6ff89-305">Ihre Abhängigkeiten werden vom DI-Container erfüllt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-305">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="6ff89-306">`TypeFilterAttribute` kann optional Konstruktorargumente für den Typ akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="6ff89-306">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="6ff89-307">Beim Verwenden von `TypeFilterAttribute` wird [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) festgelegt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-307">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="6ff89-308">Gibt einen Hinweis darauf, dass die Filterinstanz *möglicherweise* außerhalb des Anforderungsbereichs wiederverwendet wird, in dem sie erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="6ff89-308">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="6ff89-309">Die ASP.NET Core-Runtime bietet keine Garantie dafür, dass eine einzelne Instanz des Filters erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="6ff89-309">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="6ff89-310">Sollte nicht mit einem Filter verwendet werden, der von Diensten mit einer anderen Lebensdauer als der eines Singletons abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="6ff89-310">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="6ff89-311">Das folgende Beispiel zeigt, wie Sie Argumente durch Verwendung von `TypeFilterAttribute` an einen Typ übergeben:</span><span class="sxs-lookup"><span data-stu-id="6ff89-311">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="6ff89-312">Autorisierungsfilter</span><span class="sxs-lookup"><span data-stu-id="6ff89-312">Authorization filters</span></span>

<span data-ttu-id="6ff89-313">Für Autorisierungsfilter gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6ff89-313">Authorization filters:</span></span>

* <span data-ttu-id="6ff89-314">Sie werden als erste Filter in der Filterpipeline ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-314">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="6ff89-315">Sie steuern den Zugriff auf Aktionsmethoden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-315">Control access to action methods.</span></span>
* <span data-ttu-id="6ff89-316">Sie verfügen nur über eine Methode, die vor der Aktion ausgeführt wird, nicht jedoch über eine Methode, die nach der Aktion ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="6ff89-316">Have a before method, but no after method.</span></span>

<span data-ttu-id="6ff89-317">Benutzerdefinierte Autorisierungsfilter erfordern ein benutzerdefiniertes Autorisierungsframework.</span><span class="sxs-lookup"><span data-stu-id="6ff89-317">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="6ff89-318">Statt einen benutzerdefinierten Filter zu schreiben, sollten Sie die Autorisierungsrichtlinien konfigurieren oder eine benutzerdefinierte Autorisierungsrichtlinie schreiben.</span><span class="sxs-lookup"><span data-stu-id="6ff89-318">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="6ff89-319">Für den integrierten Autorisierungsfilter gilt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-319">The built-in authorization filter:</span></span>

* <span data-ttu-id="6ff89-320">Er ruft das Autorisierungssystem auf.</span><span class="sxs-lookup"><span data-stu-id="6ff89-320">Calls the authorization system.</span></span>
* <span data-ttu-id="6ff89-321">Er autorisiert keine Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-321">Does not authorize requests.</span></span>

<span data-ttu-id="6ff89-322">Lösen Sie **keine** Ausnahmen in Autorisierungsfiltern aus:</span><span class="sxs-lookup"><span data-stu-id="6ff89-322">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="6ff89-323">Die Ausnahme wird nicht behandelt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-323">The exception will not be handled.</span></span>
* <span data-ttu-id="6ff89-324">Ausnahmefilter behandeln die Ausnahme nicht.</span><span class="sxs-lookup"><span data-stu-id="6ff89-324">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="6ff89-325">Beim Auftreten einer Ausnahme in einem Autorisierungsfilter sollten Sie eine Abfrage erwägen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-325">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="6ff89-326">Weitere Informationen finden Sie unter [Autorisierung](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="6ff89-326">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="6ff89-327">Ressourcenfilter</span><span class="sxs-lookup"><span data-stu-id="6ff89-327">Resource filters</span></span>

<span data-ttu-id="6ff89-328">Für Ressourcenfilter gilt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-328">Resource filters:</span></span>

* <span data-ttu-id="6ff89-329">Sie implementieren entweder die Schnittstelle <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="6ff89-329">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="6ff89-330">Die Ausführung umschließt den größten Teil der Filterpipeline.</span><span class="sxs-lookup"><span data-stu-id="6ff89-330">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="6ff89-331">Nur [Autorisierungsfilter](#authorization-filters) werden vor Ressourcenfiltern ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-331">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="6ff89-332">Ressourcenfilter sind hilfreich, um den größten Teil der Pipeline kurzzuschließen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-332">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="6ff89-333">Ein Cachingfilter kann beispielsweise bei einem Cachetreffer den Rest der Pipeline überspringen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-333">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="6ff89-334">Beispiele für Ressourcenfilter:</span><span class="sxs-lookup"><span data-stu-id="6ff89-334">Resource filter examples:</span></span>

* <span data-ttu-id="6ff89-335">Der oben gezeigte [Ressourcenfilter zum Kurzschließen](#short-circuiting-resource-filter).</span><span class="sxs-lookup"><span data-stu-id="6ff89-335">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="6ff89-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="6ff89-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="6ff89-337">Verhindert, dass die Modellbindung auf die Formulardaten zugreift.</span><span class="sxs-lookup"><span data-stu-id="6ff89-337">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="6ff89-338">Wird bei großen Dateiuploads verwendet, um zu verhindern, dass die Formulardaten in den Arbeitsspeicher eingelesen werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-338">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="6ff89-339">Aktionsfilter</span><span class="sxs-lookup"><span data-stu-id="6ff89-339">Action filters</span></span>

<span data-ttu-id="6ff89-340">Aktionsfilter gelten **nicht** für Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="6ff89-340">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="6ff89-341">Razor Pages unterstützen <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> und <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter>.</span><span class="sxs-lookup"><span data-stu-id="6ff89-341">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="6ff89-342">Weitere Informationen finden Sie unter [Filtermethoden für Razor-Seiten](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="6ff89-342">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="6ff89-343">Für Aktionsfilter gilt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-343">Action filters:</span></span>

* <span data-ttu-id="6ff89-344">Sie implementieren entweder die Schnittstelle <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="6ff89-344">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="6ff89-345">Ihre Ausführung umfasst die Ausführung von Aktionsmethoden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-345">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="6ff89-346">Der folgende Code zeigt einen Beispielaktionsfilter:</span><span class="sxs-lookup"><span data-stu-id="6ff89-346">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="6ff89-347"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> bietet folgende Eigenschaften:</span><span class="sxs-lookup"><span data-stu-id="6ff89-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="6ff89-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> ermöglicht das Lesen der Eingaben für eine Aktionsmethode.</span><span class="sxs-lookup"><span data-stu-id="6ff89-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="6ff89-349"><xref:Microsoft.AspNetCore.Mvc.Controller> ermöglicht das Ändern der Controllerinstanz.</span><span class="sxs-lookup"><span data-stu-id="6ff89-349"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="6ff89-350"><xref:System.Web.Mvc.ActionExecutingContext.Result>: Die Festlegung von `Result` schließt die Ausführung der Aktionsmethode und der nachfolgenden Aktionsfilter kurz.</span><span class="sxs-lookup"><span data-stu-id="6ff89-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="6ff89-351">Durch Auslösen einer Ausnahme in einer Aktionsmethode geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6ff89-351">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="6ff89-352">Die Ausführung nachfolgender Filter wird verhindert.</span><span class="sxs-lookup"><span data-stu-id="6ff89-352">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="6ff89-353">Im Gegensatz zum Festlegen von `Result` wird die ausgelöste Ausnahme wird nicht als erfolgreiches Ergebnis, sondern als Fehler behandelt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-353">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="6ff89-354"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> bietet `Controller` und `Result` sowie die folgenden Eigenschaften:</span><span class="sxs-lookup"><span data-stu-id="6ff89-354">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="6ff89-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> ist TRUE, wenn die Aktionsausführung durch einen anderen Filter kurzgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="6ff89-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="6ff89-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> ist ungleich NULL, wenn die Aktion oder ein zuvor ausgeführter Aktionsfilter eine Ausnahme ausgelöst hat.</span><span class="sxs-lookup"><span data-stu-id="6ff89-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="6ff89-357">Durch Festlegen dieser Eigenschaft auf NULL geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6ff89-357">Setting this property to null:</span></span>

  * <span data-ttu-id="6ff89-358">Die Ausnahme wird effektiv behandelt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-358">Effectively handles the exception.</span></span>
  * <span data-ttu-id="6ff89-359">`Result` wird so ausgeführt, als wäre das Ergebnis von der Aktionsmethode zurückgegeben worden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-359">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="6ff89-360">Bei einem `IAsyncActionFilter` hat der Aufruf von <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> folgende Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="6ff89-360">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="6ff89-361">Alle nachfolgenden Aktionsfilter und die Aktionsmethode werden ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-361">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="6ff89-362">Gibt `ActionExecutedContext`zurück.</span><span class="sxs-lookup"><span data-stu-id="6ff89-362">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="6ff89-363">Weisen Sie einer Ergebnisinstanz <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> zu, und rufen Sie nicht `next` (den `ActionExecutionDelegate`) auf, um die Pipeline kurzzuschließen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-363">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="6ff89-364">Das Framework stellt ein abstraktes <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> bereit, das als Unterklasse verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="6ff89-364">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="6ff89-365">Der Aktionsfilter `OnActionExecuting` kann für Folgendes verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="6ff89-365">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="6ff89-366">Überprüfen des Modellzustands.</span><span class="sxs-lookup"><span data-stu-id="6ff89-366">Validate model state.</span></span>
* <span data-ttu-id="6ff89-367">Zurückgeben eines Fehlers, wenn der Zustand ungültig ist.</span><span class="sxs-lookup"><span data-stu-id="6ff89-367">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="6ff89-368">Die Methode `OnActionExecuted` wird nach der Aktionsmethode ausgeführt. Es gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6ff89-368">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="6ff89-369">Die Methode kann die Ergebnisse der Aktion durch die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> sehen und ändern.</span><span class="sxs-lookup"><span data-stu-id="6ff89-369">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="6ff89-370"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> wird auf TRUE festgelegt, wenn die Ausführung der Aktion durch einen anderen Filter kurzgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="6ff89-370"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="6ff89-371"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> wird auf einen Wert ungleich NULL festgelegt, wenn die Aktion oder ein nachfolgender Aktionsfilter eine Ausnahme ausgelöst hat.</span><span class="sxs-lookup"><span data-stu-id="6ff89-371"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="6ff89-372">Wenn für `Exception` NULL festgelegt wird,</span><span class="sxs-lookup"><span data-stu-id="6ff89-372">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="6ff89-373">werden Ausnahmen effektiv behandelt,</span><span class="sxs-lookup"><span data-stu-id="6ff89-373">Effectively handles an exception.</span></span>
  * <span data-ttu-id="6ff89-374">und `ActionExecutedContext.Result` wird so ausgeführt, als würde die Eigenschaft normal von der Aktionsmethode zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-374">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="6ff89-375">Ausnahmefilter</span><span class="sxs-lookup"><span data-stu-id="6ff89-375">Exception filters</span></span>

<span data-ttu-id="6ff89-376">Für Ausnahmefilter gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6ff89-376">Exception filters:</span></span>

* <span data-ttu-id="6ff89-377">Sie implementieren <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="6ff89-377">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="6ff89-378">Sie können verwendet werden, um gängige Fehlerbehandlungsrichtlinien zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="6ff89-378">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="6ff89-379">Der folgende Beispielausnahmefilter verwendet eine benutzerdefinierte Fehleransicht, um Details zu Ausnahmen anzuzeigen, die auftreten, wenn die App sich in der Entwicklung befindet:</span><span class="sxs-lookup"><span data-stu-id="6ff89-379">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="6ff89-380">Mit dem folgenden Code wird der Ausnahmefilter getestet:</span><span class="sxs-lookup"><span data-stu-id="6ff89-380">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="6ff89-381">Für Ausnahmefilter gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6ff89-381">Exception filters:</span></span>

* <span data-ttu-id="6ff89-382">Sie verfügen nicht über vorangehende oder darauffolgende Ereignisse.</span><span class="sxs-lookup"><span data-stu-id="6ff89-382">Don't have before and after events.</span></span>
* <span data-ttu-id="6ff89-383">Sie implementieren <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="6ff89-383">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="6ff89-384">Sie behandeln Ausnahmefehler, die bei der Erstellung von Razor Pages oder Controllern, bei der [Modellbindung](xref:mvc/models/model-binding), bei Aktionsfiltern oder bei Aktionsmethoden auftreten.</span><span class="sxs-lookup"><span data-stu-id="6ff89-384">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="6ff89-385">Sie erfassen **keine** Ausnahmen, die in Ressourcenfiltern, Ergebnisfiltern oder bei der Ausführung von MVC-Ergebnissen auftreten.</span><span class="sxs-lookup"><span data-stu-id="6ff89-385">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="6ff89-386">Um eine Ausnahme zu behandeln, muss die Eigenschaft <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> auf `true` festgelegt oder eine Antwort geschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-386">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="6ff89-387">Dadurch wird die Weitergabe der Ausnahme beendet.</span><span class="sxs-lookup"><span data-stu-id="6ff89-387">This stops propagation of the exception.</span></span> <span data-ttu-id="6ff89-388">Ein Ausnahmefilter kann eine Ausnahme nicht in ein „erfolgreiches Ergebnis“ umwandeln.</span><span class="sxs-lookup"><span data-stu-id="6ff89-388">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="6ff89-389">Nur ein Aktionsfilter kann das.</span><span class="sxs-lookup"><span data-stu-id="6ff89-389">Only an action filter can do that.</span></span>

<span data-ttu-id="6ff89-390">Für Ausnahmefilter gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6ff89-390">Exception filters:</span></span>

* <span data-ttu-id="6ff89-391">Sie eignen sich für das Abfangen von Ausnahmen, die in Aktionen auftreten.</span><span class="sxs-lookup"><span data-stu-id="6ff89-391">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="6ff89-392">Sie sind im Vergleich zu Middleware für die Fehlerbehandlung weniger flexibel.</span><span class="sxs-lookup"><span data-stu-id="6ff89-392">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="6ff89-393">Sie sollten bei der Ausnahmebehandlung vorzugsweise Middleware verwenden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-393">Prefer middleware for exception handling.</span></span> <span data-ttu-id="6ff89-394">Verwenden Sie Ausnahmefilter nur, wenn sich die Fehlerbehandlung *unterscheidet*, je nachdem, welche Aktionsmethode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="6ff89-394">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="6ff89-395">Zum Beispiel verfügt eine App möglicherweise über Aktionsmethoden für beide API-Endpunkte und für Ansichten bzw. HTML.</span><span class="sxs-lookup"><span data-stu-id="6ff89-395">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="6ff89-396">Die API-Endpunkte können Fehlerinformationen als JSON zurückgeben, während ansichtsbasierte Aktionen eine Fehlerseite als HTML zurückgeben können.</span><span class="sxs-lookup"><span data-stu-id="6ff89-396">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="6ff89-397">Ergebnisfilter</span><span class="sxs-lookup"><span data-stu-id="6ff89-397">Result filters</span></span>

<span data-ttu-id="6ff89-398">Für Ergebnisfilter gilt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-398">Result filters:</span></span>

* <span data-ttu-id="6ff89-399">Implementieren einer Schnittstelle:</span><span class="sxs-lookup"><span data-stu-id="6ff89-399">Implement an interface:</span></span>
  * <span data-ttu-id="6ff89-400"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="6ff89-400"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="6ff89-401"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="6ff89-401"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="6ff89-402">Ihre Ausführung umfasst die Ausführung von Aktionsergebnissen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-402">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="6ff89-403">IResultFilter und IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="6ff89-403">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="6ff89-404">Der folgende Code zeigt einen Ergebnisfilter, der einen HTTP-Header hinzufügt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-404">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="6ff89-405">Die Art des Ergebnisses, das ausgeführt wird, hängt von der jeweiligen Aktion ab.</span><span class="sxs-lookup"><span data-stu-id="6ff89-405">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="6ff89-406">Eine Aktion, die eine Ansicht zurückgibt, beinhaltet die gesamte Razor-Verarbeitung als Teil der Ausführung von <xref:Microsoft.AspNetCore.Mvc.ViewResult>.</span><span class="sxs-lookup"><span data-stu-id="6ff89-406">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="6ff89-407">Eine API-Methode führt möglicherweise eine Art Serialisierung als Teil der Ausführung des Ergebnisses durch.</span><span class="sxs-lookup"><span data-stu-id="6ff89-407">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="6ff89-408">Erfahren Sie mehr zu [Aktionsergebnissen](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="6ff89-408">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="6ff89-409">Ergebnisfilter werden nur ausgeführt, wenn eine Aktion oder ein Aktionsfilter ein Aktionsergebnis liefert.</span><span class="sxs-lookup"><span data-stu-id="6ff89-409">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="6ff89-410">Ergebnisfilter werden in folgenden Fällen nicht ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-410">Result filters are not executed when:</span></span>

* <span data-ttu-id="6ff89-411">Ein Autorisierungs- oder Ressourcenfilter schließt die Pipeline kurz.</span><span class="sxs-lookup"><span data-stu-id="6ff89-411">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="6ff89-412">Ein Ausnahmefilter behandelt eine Ausnahme durch Erzeugen eines Aktionsergebnisses.</span><span class="sxs-lookup"><span data-stu-id="6ff89-412">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="6ff89-413">Die Methode <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> kann die Ausführung des Aktionsergebnisses und der nachfolgenden Ergebnisfilter durch Festlegen von <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> auf `true` kurzschließen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-413">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="6ff89-414">Schreiben Sie beim Kurzschließen in das Antwortobjekt, um zu verhindern, dass eine leere Antwort generiert wird.</span><span class="sxs-lookup"><span data-stu-id="6ff89-414">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="6ff89-415">Das Auslösen einer Ausnahme in `IResultFilter.OnResultExecuting` bewirkt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6ff89-415">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="6ff89-416">Die Ausführung des Aktionsergebnisses und der nachfolgenden Filter wird verhindert.</span><span class="sxs-lookup"><span data-stu-id="6ff89-416">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="6ff89-417">Die ausgelöste Ausnahme wird nicht als erfolgreiches Ergebnis, sondern als Fehler behandelt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-417">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="6ff89-418">Wenn die <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>-Methode ausgeführt wird, wurde die Antwort wahrscheinlich bereits an den Client gesendet.</span><span class="sxs-lookup"><span data-stu-id="6ff89-418">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="6ff89-419">Wenn die Antwort bereits an den Client gesendet wurde, kann sie nicht geändert werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-419">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="6ff89-420">`ResultExecutedContext.Canceled` wird auf `true` festgelegt, wenn die Ausführung des Aktionsergebnisses durch einen anderen Filter kurzgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="6ff89-420">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="6ff89-421">`ResultExecutedContext.Exception` wird auf einen Wert ungleich NULL festgelegt, wenn das Aktionsergebnis oder ein nachfolgender Ergebnisfilter eine Ausnahme ausgelöst hat.</span><span class="sxs-lookup"><span data-stu-id="6ff89-421">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="6ff89-422">Das Festlegen von `Exception` auf NULL behandelt eine Ausnahme und verhindert, dass die Ausnahme zu einem späteren Zeitpunkt in der Pipeline ein weiteres Mal ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="6ff89-422">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="6ff89-423">Beim Behandeln einer Ausnahme in einem Ergebnisfilter gibt es keine zuverlässige Möglichkeit, Daten in eine Antwort zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="6ff89-423">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="6ff89-424">Wenn ein Aktionsergebnis eine Ausnahme auslöst und die Header bereits an den Client übergeben wurden, gibt es keinen zuverlässigen Mechanismus, einen Fehlercode zu senden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-424">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="6ff89-425">Bei einem <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> führt ein Aufruf von `await next` im <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> alle nachfolgenden Ergebnisfilter und das Aktionsergebnis aus.</span><span class="sxs-lookup"><span data-stu-id="6ff89-425">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="6ff89-426">Zum Kurzschließen legen Sie [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) auf `true` fest und rufen den `ResultExecutionDelegate` nicht auf:</span><span class="sxs-lookup"><span data-stu-id="6ff89-426">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="6ff89-427">Das Framework stellt ein abstraktes `ResultFilterAttribute` bereit, das als Unterklasse verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="6ff89-427">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="6ff89-428">Die weiter oben gezeigte Klasse [AddHeaderAttribute](#add-header-attribute) ist ein Beispiel für ein Ergebnisfilterattribut.</span><span class="sxs-lookup"><span data-stu-id="6ff89-428">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="6ff89-429">IAlwaysRunResultFilter und IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="6ff89-429">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="6ff89-430">Die Schnittstellen <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> und <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklarieren eine <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter>-Implementierung, die für alle Aktionsergebnisse ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="6ff89-430">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="6ff89-431">Dies schließt Aktionsergebnisse ein, die von folgenden Filtern generiert werden:</span><span class="sxs-lookup"><span data-stu-id="6ff89-431">This includes action results produced by:</span></span>

* <span data-ttu-id="6ff89-432">Autorisierungs- und Ressourcenfilter, die einen Kurzschluss bewirken</span><span class="sxs-lookup"><span data-stu-id="6ff89-432">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="6ff89-433">Ausnahmefilter</span><span class="sxs-lookup"><span data-stu-id="6ff89-433">Exception filters.</span></span>

<span data-ttu-id="6ff89-434">Beispielsweise wird der folgende Filter immer ausgeführt und legt ein Aktionsergebnis (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) mit dem Statuscode *422 – Einheit kann nicht bearbeitet werden* fest, wenn bei der Inhaltsaushandlung ein Fehler auftritt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-434">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="6ff89-435">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="6ff89-435">IFilterFactory</span></span>

<span data-ttu-id="6ff89-436"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementiert <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="6ff89-436"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="6ff89-437">Deshalb kann eine `IFilterFactory`-Instanz an einer beliebigen Stelle in der Filterpipeline als `IFilterMetadata`-Instanz verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-437">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="6ff89-438">Wenn die Runtime den Aufruf des Filters vorbereitet, versucht sie, ihn in eine `IFilterFactory` umzuwandeln.</span><span class="sxs-lookup"><span data-stu-id="6ff89-438">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="6ff89-439">Wenn diese Umwandlung gelingt, wird die Methode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> aufgerufen, um die Instanz `IFilterMetadata` für den Aufruf zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-439">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="6ff89-440">Da die exakte Filterpipeline beim Start der Anwendung nicht explizit festgelegt werden muss, wird dadurch ein sehr flexibles Design ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="6ff89-440">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="6ff89-441">Als weiteres Verfahren zum Erstellen von Filtern kann `IFilterFactory` mithilfe von benutzerdefinierten Attributimplementierungen implementiert werden:</span><span class="sxs-lookup"><span data-stu-id="6ff89-441">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="6ff89-442">Der Filter wird im folgenden Code angewendet:</span><span class="sxs-lookup"><span data-stu-id="6ff89-442">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="6ff89-443">Testen Sie den vorangehenden Code durch Ausführen des [Downloadbeispiels](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="6ff89-443">Test the preceding code by running the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="6ff89-444">Rufen Sie die F12-Entwicklungstools auf.</span><span class="sxs-lookup"><span data-stu-id="6ff89-444">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="6ff89-445">Navigieren Sie zu `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="6ff89-445">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="6ff89-446">Die F12-Entwicklungstools zeigen folgende Antwortheader an, die vom Beispielcode hinzugefügt wurden:</span><span class="sxs-lookup"><span data-stu-id="6ff89-446">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="6ff89-447">**author:** `Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="6ff89-447">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="6ff89-448">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="6ff89-448">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="6ff89-449">**internal:** `My header`</span><span class="sxs-lookup"><span data-stu-id="6ff89-449">**internal:** `My header`</span></span>

<span data-ttu-id="6ff89-450">Der oben stehende Code erstellt den Antwortheader „**internal:** `My header`“.</span><span class="sxs-lookup"><span data-stu-id="6ff89-450">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="6ff89-451">In einem Attribut implementierte IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="6ff89-451">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="6ff89-452">Filter, die `IFilterFactory` implementieren, eignen sich in folgenden Fällen:</span><span class="sxs-lookup"><span data-stu-id="6ff89-452">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="6ff89-453">Sie erfordern kein Übergeben von Parametern.</span><span class="sxs-lookup"><span data-stu-id="6ff89-453">Don't require passing parameters.</span></span>
* <span data-ttu-id="6ff89-454">Sie verfügen über Konstruktorabhängigkeiten, die durch DI erfüllt werden müssen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-454">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="6ff89-455"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementiert <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="6ff89-455"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="6ff89-456">`IFilterFactory` macht die <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*>-Methode zum Erstellen einer <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>-Instanz verfügbar.</span><span class="sxs-lookup"><span data-stu-id="6ff89-456">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="6ff89-457">`CreateInstance` lädt den angegebenen Typ aus dem Dienstecontainer (DI).</span><span class="sxs-lookup"><span data-stu-id="6ff89-457">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="6ff89-458">Der folgende Code zeigt drei Ansätze zum Anwenden von `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="6ff89-458">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="6ff89-459">Im oben stehenden Code ist das Ergänzen der Methode um `[SampleActionFilter]` der bevorzugte Ansatz zum Anwenden von `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="6ff89-459">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="6ff89-460">Verwenden von Middleware in der Filterpipeline</span><span class="sxs-lookup"><span data-stu-id="6ff89-460">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="6ff89-461">Ressourcenfilter funktionieren wie [Middleware](xref:fundamentals/middleware/index), sie umschließen alle Ausführungen, die später in der Pipeline enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="6ff89-461">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="6ff89-462">Filter unterscheiden sich jedoch insofern von Middleware, dass sie Teil der Runtime sind, was bedeutet, dass sie Zugriff auf Kontext und Konstrukte haben.</span><span class="sxs-lookup"><span data-stu-id="6ff89-462">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="6ff89-463">Um Middleware als Filter zu verwenden, erstellen Sie einen Typ mit einer `Configure`-Methode, die die Middleware festlegt, die in die Filterpipeline eingefügt werden soll.</span><span class="sxs-lookup"><span data-stu-id="6ff89-463">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="6ff89-464">Das folgende Beispiel verwendet die Lokalisierungsmiddleware, um die aktuelle Kultur einer Anforderung einzurichten:</span><span class="sxs-lookup"><span data-stu-id="6ff89-464">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="6ff89-465">Verwenden Sie das <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> zum Ausführen der Middleware:</span><span class="sxs-lookup"><span data-stu-id="6ff89-465">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="6ff89-466">Middlewarefilter werden zum selben Zeitpunkt in der Filterpipeline wie Ressourcenfilter, vor der Modellbindung und nach dem Rest der Pipeline ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-466">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="6ff89-467">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="6ff89-467">Next actions</span></span>

* <span data-ttu-id="6ff89-468">Siehe [Filtermethoden für Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="6ff89-468">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="6ff89-469">Um Filter näher kennenzulernen, [laden Sie das GitHub-Beispiel herunter, und testen und bearbeiten Sie es](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="6ff89-469">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6ff89-470">Von [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="6ff89-470">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="6ff89-471">*Filter* ermöglichen es in ASP.NET Core, Code vor oder nach bestimmten Phasen der Anforderungsverarbeitungspipeline auszuführen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-471">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="6ff89-472">Integrierte Filter sind für folgende Aufgaben zuständig:</span><span class="sxs-lookup"><span data-stu-id="6ff89-472">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="6ff89-473">Autorisierung (der Zugriff auf Ressourcen, für die ein Benutzer nicht autorisiert ist, wird verhindert)</span><span class="sxs-lookup"><span data-stu-id="6ff89-473">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="6ff89-474">Zwischenspeicherung von Antworten (die Anforderungspipeline wird unterbrochen, damit eine zwischengespeicherte Antwort zurückgegeben wird)</span><span class="sxs-lookup"><span data-stu-id="6ff89-474">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="6ff89-475">Durch die Erstellung benutzerdefinierter Filter kann mit aktionsübergreifenden Problemen umgegangen werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-475">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="6ff89-476">Zu solchen übergreifenden Problemen gehören beispielsweise Fehlerbehandlung, Caching, Konfiguration, Autorisierung und Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="6ff89-476">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="6ff89-477">Mit Filtern lässt sich die Duplizierung von Code vermeiden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-477">Filters avoid duplicating code.</span></span> <span data-ttu-id="6ff89-478">Sie können zum Beispiel die Fehlerbehandlung in einem Ausnahmefilter konsolidieren.</span><span class="sxs-lookup"><span data-stu-id="6ff89-478">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="6ff89-479">Dieses Dokument gilt für Razor Pages, API-Controller und Controller mit Ansichten.</span><span class="sxs-lookup"><span data-stu-id="6ff89-479">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="6ff89-480">[Zeigen Sie ein Beispiel an, oder laden Sie es herunter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="6ff89-480">[View or download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="6ff89-481">Die Funktionsweise von Filtern</span><span class="sxs-lookup"><span data-stu-id="6ff89-481">How filters work</span></span>

<span data-ttu-id="6ff89-482">Filter werden in der *ASP.NET Core-Aktionsaufrufpipeline* ausgeführt, die manchmal auch als *Filterpipeline* bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="6ff89-482">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="6ff89-483">Die Filterpipeline wird ausgeführt, nachdem ASP.NET Core die auszuführende Aktion ausgewählt hat.</span><span class="sxs-lookup"><span data-stu-id="6ff89-483">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Die Anforderung wird von weiterer Middleware, Routingmiddleware, Aktionsauswahl und der ASP.NET Core-Aktionsaufrufpipeline verarbeitet.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="6ff89-486">Filtertypen</span><span class="sxs-lookup"><span data-stu-id="6ff89-486">Filter types</span></span>

<span data-ttu-id="6ff89-487">Jeder Filtertyp wird in einer anderen Phase der Filterpipeline ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-487">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="6ff89-488">[Autorisierungsfilter](#authorization-filters) werden zuerst ausgeführt und dienen dazu, festzustellen, ob der Benutzer für die Anforderung berechtigt ist.</span><span class="sxs-lookup"><span data-stu-id="6ff89-488">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="6ff89-489">Autorisierungsfilter schließen die Pipeline kurz, wenn die Anforderung nicht autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="6ff89-489">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="6ff89-490">[Ressourcenfilter](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="6ff89-490">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="6ff89-491">Werden nach der Autorisierung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-491">Run after authorization.</span></span>  
  * <span data-ttu-id="6ff89-492"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> kann Code vor dem Rest der Filterpipeline ausführen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-492"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="6ff89-493">Beispielsweise kann `OnResourceExecuting` Code vor der Modellbindung ausführen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-493">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="6ff89-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> kann Code nach Abschluss der restlichen Pipeline ausführen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="6ff89-495">[Aktionsfilter](#action-filters) können, vor und nach dem Aufruf einer individuelle Aktionsmethode, Code ausführen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-495">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="6ff89-496">Sie können zur Bearbeitung der Argumente, die an eine Aktion übermittelt werden, und des Ergebnisses, das von der Aktion zurückgegeben wird, verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-496">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="6ff89-497">Aktionsfilter werden in Razor Pages **nicht** unterstützt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-497">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="6ff89-498">[Ausnahmefilter](#exception-filters) werden dazu verwendet, globale Richtlinien auf unbehandelte Ausnahmen anzuwenden, die auftreten, bevor etwas in den Antworttext geschrieben wurde.</span><span class="sxs-lookup"><span data-stu-id="6ff89-498">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="6ff89-499">[Ergebnisfilter](#result-filters) können, vor und nach dem Ergebnis einer ausgeführten individuellen Aktion, Code ausführen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-499">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="6ff89-500">Sie werden nur ausgeführt, wenn die Aktionsmethode erfolgreich ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="6ff89-500">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="6ff89-501">Ergebnisfilter sind hilfreich für Logik, die die Ausführung von Ansichten oder Formatierern umfasst.</span><span class="sxs-lookup"><span data-stu-id="6ff89-501">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="6ff89-502">Das folgende Diagramm veranschaulicht, wie die Filtertypen mit der Filterpipeline interagieren.</span><span class="sxs-lookup"><span data-stu-id="6ff89-502">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Die Anforderung wird von Autorisierungsfilter, Ressourcenfilter, Modellbindung, Aktionsfilter, Aktionsausführung sowie Aktionsergebniskonvertierung, Ausnahmefilter, Ergebnisfilter und Ergebnisausführung verarbeitet.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="6ff89-505">Implementierung</span><span class="sxs-lookup"><span data-stu-id="6ff89-505">Implementation</span></span>

<span data-ttu-id="6ff89-506">Filter unterstützen sowohl synchrone als auch asynchrone Implementierungen über verschiedene Schnittstellendefinitionen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-506">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="6ff89-507">Synchrone Filter können Code vor (`On-Stage-Executing`) und nach (`On-Stage-Executed`) der jeweiligen Pipelinephase ausführen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-507">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="6ff89-508">`OnActionExecuting` beispielsweise wird aufgerufen, bevor die Aktionsmethode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="6ff89-508">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="6ff89-509">`OnActionExecuted` wird nach der Rückgabe der Aktionsmethode aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-509">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="6ff89-510">Asynchrone Filter definieren eine `On-Stage-ExecutionAsync`-Methode:</span><span class="sxs-lookup"><span data-stu-id="6ff89-510">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="6ff89-511">Im oben stehenden Code weist der `SampleAsyncActionFilter` einen <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) auf, der die Aktionsmethode ausführt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-511">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="6ff89-512">Jede der `On-Stage-ExecutionAsync`-Methoden akzeptiert einen `FilterType-ExecutionDelegate`, der die Pipelinephase des Filters ausführt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-512">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="6ff89-513">Mehrere Filterphasen</span><span class="sxs-lookup"><span data-stu-id="6ff89-513">Multiple filter stages</span></span>

<span data-ttu-id="6ff89-514">Schnittstellen für mehrere Filterphasen können in einer einzigen Klasse implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-514">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="6ff89-515">Beispielsweise implementiert die Klasse <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>`IActionFilter`, `IResultFilter`, und deren asynchrone Gegenstücke.</span><span class="sxs-lookup"><span data-stu-id="6ff89-515">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="6ff89-516">Implementieren Sie **entweder** die synchrone oder asynchrone Version einer Filterschnittstelle, aber **nicht** beide.</span><span class="sxs-lookup"><span data-stu-id="6ff89-516">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="6ff89-517">Die Runtime prüft zuerst, ob der Filter die asynchrone Schnittstelle implementiert, und wenn dies der Fall ist, ruft die Runtime diese Schnittstelle auf.</span><span class="sxs-lookup"><span data-stu-id="6ff89-517">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="6ff89-518">Wenn dies nicht der Fall ist, ruft es die Methode(n) der synchronen Schnittstelle auf.</span><span class="sxs-lookup"><span data-stu-id="6ff89-518">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="6ff89-519">Wenn die asynchrone und die synchrone Schnittstelle in einer einzigen Klasse implementiert sind, wird nur die asynchrone Methode aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-519">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="6ff89-520">Bei Verwendung von abstrakten Klassen wie <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> überschreiben Sie nur die synchronen Methoden oder die asynchrone Methode für jeden Filtertyp.</span><span class="sxs-lookup"><span data-stu-id="6ff89-520">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="6ff89-521">Integrierte Filterattribute</span><span class="sxs-lookup"><span data-stu-id="6ff89-521">Built-in filter attributes</span></span>

<span data-ttu-id="6ff89-522">ASP.NET Core enthält integrierte attributbasierte Filter, die als Unterklasse erstellt und angepasst werden können.</span><span class="sxs-lookup"><span data-stu-id="6ff89-522">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="6ff89-523">Zum Beispiel fügt der folgende Ergebnisfilter der Antwort einen Header hinzu:</span><span class="sxs-lookup"><span data-stu-id="6ff89-523">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="6ff89-524">Wie im obigen Beispiel gezeigt, können Filter mithilfe von Attributen Argumente akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="6ff89-524">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="6ff89-525">Wenden Sie das `AddHeaderAttribute` auf einen Controller oder eine Aktionsmethode an, und geben Sie den Namen und den Wert des HTTP-Headers an:</span><span class="sxs-lookup"><span data-stu-id="6ff89-525">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="6ff89-526">Einige der Filterschnittstellen besitzen entsprechende Attribute, die als Basisklassen für benutzerdefinierte Implementierungen verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="6ff89-526">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="6ff89-527">Filterattribute:</span><span class="sxs-lookup"><span data-stu-id="6ff89-527">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="6ff89-528">Filterbereiche und Reihenfolge der Ausführung</span><span class="sxs-lookup"><span data-stu-id="6ff89-528">Filter scopes and order of execution</span></span>

<span data-ttu-id="6ff89-529">Der Pipeline kann in einem von drei *Bereichen* ein Filter hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="6ff89-529">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="6ff89-530">Verwenden eines Attributs in einer Aktion</span><span class="sxs-lookup"><span data-stu-id="6ff89-530">Using an attribute on an action.</span></span>
* <span data-ttu-id="6ff89-531">Verwenden eines Attributs in einem Controller</span><span class="sxs-lookup"><span data-stu-id="6ff89-531">Using an attribute on a controller.</span></span>
* <span data-ttu-id="6ff89-532">Global für alle Controller und Aktionen, wie in folgendem Code gezeigt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-532">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="6ff89-533">Der oben stehende Code fügt mithilfe der Sammlung [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) global drei Filter hinzu.</span><span class="sxs-lookup"><span data-stu-id="6ff89-533">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="6ff89-534">Standardreihenfolge der Ausführung</span><span class="sxs-lookup"><span data-stu-id="6ff89-534">Default order of execution</span></span>

<span data-ttu-id="6ff89-535">Wenn mehrere Filter *des gleichen Typs* vorhanden sind, bestimmt der Umfang die Standardreihenfolge ihrer Ausführung.</span><span class="sxs-lookup"><span data-stu-id="6ff89-535">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="6ff89-536">Globale Filter umschließen Klassenfilter.</span><span class="sxs-lookup"><span data-stu-id="6ff89-536">Global filters surround class filters.</span></span> <span data-ttu-id="6ff89-537">Klassenfilter umschließen Methodenfilter.</span><span class="sxs-lookup"><span data-stu-id="6ff89-537">Class filters surround method filters.</span></span>

<span data-ttu-id="6ff89-538">Als Ergebnis der Filterschachtelung wird der *nach* einer Pipelinephase auszuführende Code in umgekehrter Reihenfolge zum Code *vor* einer Pipelinephase ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-538">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="6ff89-539">Filterreihenfolge:</span><span class="sxs-lookup"><span data-stu-id="6ff89-539">The filter sequence:</span></span>

* <span data-ttu-id="6ff89-540">Der Code von globalen Filtern, der *vor* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="6ff89-540">The *before* code of global filters.</span></span>
  * <span data-ttu-id="6ff89-541">Der Code von Controllerfiltern, der *vor* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="6ff89-541">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="6ff89-542">Der Code von Aktionsmethodenfiltern, der *vor* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="6ff89-542">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="6ff89-543">Der Code von Aktionsmethodenfiltern, der *nach* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="6ff89-543">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="6ff89-544">Der Code von Controllerfiltern, der *nach* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="6ff89-544">The *after* code of controller filters.</span></span>
* <span data-ttu-id="6ff89-545">Der Code von globalen Filtern, der *nach* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="6ff89-545">The *after* code of global filters.</span></span>
  
<span data-ttu-id="6ff89-546">Das folgende Beispiel veranschaulicht die Reihenfolge, in der Filtermethoden für synchrone Aktionsfilter aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-546">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="6ff89-547">Sequenz</span><span class="sxs-lookup"><span data-stu-id="6ff89-547">Sequence</span></span> | <span data-ttu-id="6ff89-548">Filterbereich</span><span class="sxs-lookup"><span data-stu-id="6ff89-548">Filter scope</span></span> | <span data-ttu-id="6ff89-549">Filtermethode</span><span class="sxs-lookup"><span data-stu-id="6ff89-549">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="6ff89-550">1</span><span class="sxs-lookup"><span data-stu-id="6ff89-550">1</span></span> | <span data-ttu-id="6ff89-551">Global</span><span class="sxs-lookup"><span data-stu-id="6ff89-551">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="6ff89-552">2</span><span class="sxs-lookup"><span data-stu-id="6ff89-552">2</span></span> | <span data-ttu-id="6ff89-553">Controller</span><span class="sxs-lookup"><span data-stu-id="6ff89-553">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="6ff89-554">3</span><span class="sxs-lookup"><span data-stu-id="6ff89-554">3</span></span> | <span data-ttu-id="6ff89-555">Methode</span><span class="sxs-lookup"><span data-stu-id="6ff89-555">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="6ff89-556">4</span><span class="sxs-lookup"><span data-stu-id="6ff89-556">4</span></span> | <span data-ttu-id="6ff89-557">Methode</span><span class="sxs-lookup"><span data-stu-id="6ff89-557">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="6ff89-558">5</span><span class="sxs-lookup"><span data-stu-id="6ff89-558">5</span></span> | <span data-ttu-id="6ff89-559">Controller</span><span class="sxs-lookup"><span data-stu-id="6ff89-559">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="6ff89-560">6</span><span class="sxs-lookup"><span data-stu-id="6ff89-560">6</span></span> | <span data-ttu-id="6ff89-561">Global</span><span class="sxs-lookup"><span data-stu-id="6ff89-561">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="6ff89-562">Diese Sequenz veranschaulicht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6ff89-562">This sequence shows:</span></span>

* <span data-ttu-id="6ff89-563">Der Methodenfilter ist innerhalb des Controllerfilters geschachtelt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-563">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="6ff89-564">Der Controllerfilter ist innerhalb des globalen Filters geschachtelt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-564">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="6ff89-565">Filter auf Controller- und Razor Page-Ebene</span><span class="sxs-lookup"><span data-stu-id="6ff89-565">Controller and Razor Page level filters</span></span>

<span data-ttu-id="6ff89-566">Jeder Controller, der von der Basisklasse <xref:Microsoft.AspNetCore.Mvc.Controller> erbt, enthält die Methoden [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) und [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="6ff89-566">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="6ff89-567">Diese Methoden führen Folgendes aus:</span><span class="sxs-lookup"><span data-stu-id="6ff89-567">These methods:</span></span>

* <span data-ttu-id="6ff89-568">Die Filter, die für eine bestimmte Aktion ausgeführt werden, werden umschlossen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-568">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="6ff89-569">`OnActionExecuting` wird vor allen Filtern der Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-569">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="6ff89-570">`OnActionExecuted` wird nach allen Filtern der Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-570">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="6ff89-571">`OnActionExecutionAsync` wird vor allen Filtern der Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-571">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="6ff89-572">Code im Filter nach `next` wird nach der Aktionsmethode ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-572">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="6ff89-573">Im Downloadbeispiel wird `MySampleActionFilter` global beim Start angewendet.</span><span class="sxs-lookup"><span data-stu-id="6ff89-573">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="6ff89-574">Die `TestController`:</span><span class="sxs-lookup"><span data-stu-id="6ff89-574">The `TestController`:</span></span>

* <span data-ttu-id="6ff89-575">Wendet das `SampleActionFilterAttribute` (`[SampleActionFilter]`) auf die Aktion `FilterTest2` an.</span><span class="sxs-lookup"><span data-stu-id="6ff89-575">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="6ff89-576">Überschreibt `OnActionExecuting` und `OnActionExecuted`:</span><span class="sxs-lookup"><span data-stu-id="6ff89-576">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="6ff89-577">Durch Navigieren zu `https://localhost:5001/Test/FilterTest2` wird der folgende Code ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-577">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="6ff89-578">Informationen zu Razor Pages finden Sie unter [Implementieren von Filtern für Razor-Seiten durch Überschreiben von Filtermethoden](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="6ff89-578">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="6ff89-579">Überschreiben der Standardreihenfolge</span><span class="sxs-lookup"><span data-stu-id="6ff89-579">Overriding the default order</span></span>

<span data-ttu-id="6ff89-580">Die Standardreihenfolge der Ausführung kann durch Implementieren von <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-580">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="6ff89-581">`IOrderedFilter` macht die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> verfügbar, die bei der Bestimmung der Ausführungsreihenfolge Vorrang vor dem Bereich hat.</span><span class="sxs-lookup"><span data-stu-id="6ff89-581">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="6ff89-582">Für einen Filter mit geringerem `Order`-Wert gilt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-582">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="6ff89-583">Führt den Code, der *vor* einer Pipelinephase ausgeführt werden soll, vor dem Code eines Filters mit einem höheren Wert für `Order` aus.</span><span class="sxs-lookup"><span data-stu-id="6ff89-583">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="6ff89-584">Führt den Code, der *nach* einer Pipelinephase ausgeführt werden soll, nach dem Code eines Filters mit einem höheren Wert für `Order` aus.</span><span class="sxs-lookup"><span data-stu-id="6ff89-584">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="6ff89-585">Die Eigenschaft `Order` kann mit einem Konstruktorparameter festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="6ff89-585">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="6ff89-586">Sehen Sie sich die drei Aktionsfilter an, die im vorherigen Beispiel gezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-586">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="6ff89-587">Wenn die `Order`-Eigenschaft des Controllers und der globalen Filter auf 1 bzw. 2 festgelegt ist, wird die Reihenfolge der Ausführung umgekehrt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-587">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="6ff89-588">Sequenz</span><span class="sxs-lookup"><span data-stu-id="6ff89-588">Sequence</span></span> | <span data-ttu-id="6ff89-589">Filterbereich</span><span class="sxs-lookup"><span data-stu-id="6ff89-589">Filter scope</span></span> | <span data-ttu-id="6ff89-590">`Order` -Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="6ff89-590">`Order` property</span></span> | <span data-ttu-id="6ff89-591">Filtermethode</span><span class="sxs-lookup"><span data-stu-id="6ff89-591">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="6ff89-592">1</span><span class="sxs-lookup"><span data-stu-id="6ff89-592">1</span></span> | <span data-ttu-id="6ff89-593">Methode</span><span class="sxs-lookup"><span data-stu-id="6ff89-593">Method</span></span> | <span data-ttu-id="6ff89-594">0</span><span class="sxs-lookup"><span data-stu-id="6ff89-594">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="6ff89-595">2</span><span class="sxs-lookup"><span data-stu-id="6ff89-595">2</span></span> | <span data-ttu-id="6ff89-596">Controller</span><span class="sxs-lookup"><span data-stu-id="6ff89-596">Controller</span></span> | <span data-ttu-id="6ff89-597">1</span><span class="sxs-lookup"><span data-stu-id="6ff89-597">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="6ff89-598">3</span><span class="sxs-lookup"><span data-stu-id="6ff89-598">3</span></span> | <span data-ttu-id="6ff89-599">Global</span><span class="sxs-lookup"><span data-stu-id="6ff89-599">Global</span></span> | <span data-ttu-id="6ff89-600">2</span><span class="sxs-lookup"><span data-stu-id="6ff89-600">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="6ff89-601">4</span><span class="sxs-lookup"><span data-stu-id="6ff89-601">4</span></span> | <span data-ttu-id="6ff89-602">Global</span><span class="sxs-lookup"><span data-stu-id="6ff89-602">Global</span></span> | <span data-ttu-id="6ff89-603">2</span><span class="sxs-lookup"><span data-stu-id="6ff89-603">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="6ff89-604">5</span><span class="sxs-lookup"><span data-stu-id="6ff89-604">5</span></span> | <span data-ttu-id="6ff89-605">Controller</span><span class="sxs-lookup"><span data-stu-id="6ff89-605">Controller</span></span> | <span data-ttu-id="6ff89-606">1</span><span class="sxs-lookup"><span data-stu-id="6ff89-606">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="6ff89-607">6</span><span class="sxs-lookup"><span data-stu-id="6ff89-607">6</span></span> | <span data-ttu-id="6ff89-608">Methode</span><span class="sxs-lookup"><span data-stu-id="6ff89-608">Method</span></span> | <span data-ttu-id="6ff89-609">0</span><span class="sxs-lookup"><span data-stu-id="6ff89-609">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="6ff89-610">Die Eigenschaft `Order` hat bei der Bestimmung der Reihenfolge, in der Filter ausgeführt werden, Vorrang vor dem Bereich.</span><span class="sxs-lookup"><span data-stu-id="6ff89-610">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="6ff89-611">Filter werden erst nach der Reihenfolge sortiert, und dann werden Gleichstände über den Bereich aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="6ff89-611">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="6ff89-612">Alle integrierten Filter implementieren `IOrderedFilter` und legen den Standartwert von `Order` auf 0 fest.</span><span class="sxs-lookup"><span data-stu-id="6ff89-612">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="6ff89-613">Bei integrierten Filtern bestimmt der Bereich die Reihenfolge, falls `Order` nicht auf einen Wert ungleich 0 festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="6ff89-613">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="6ff89-614">Abbrechen und Kurzschließen</span><span class="sxs-lookup"><span data-stu-id="6ff89-614">Cancellation and short-circuiting</span></span>

<span data-ttu-id="6ff89-615">Die Filterpipeline kann kurzgeschlossen werden, indem die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> auf den Parameter <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> festgelegt wird, der in der Filtermethode angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="6ff89-615">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="6ff89-616">Zum Beispiel verhindert der folgende Ressourcenfilter, dass der Rest der Pipeline ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="6ff89-616">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="6ff89-617">Im folgenden Code verwenden sowohl der Filter `ShortCircuitingResourceFilter` und der Filter `AddHeader` die Aktionsmethode `SomeResource` als Ziel.</span><span class="sxs-lookup"><span data-stu-id="6ff89-617">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="6ff89-618">Die `ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="6ff89-618">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="6ff89-619">Der Filter wird zuerst ausgeführt, da es sich um einen Ressourcenfilter handelt und `AddHeader` ein Aktionsfilter ist.</span><span class="sxs-lookup"><span data-stu-id="6ff89-619">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="6ff89-620">Der Filter unterbricht alle verbleibenden Pipelineschritte.</span><span class="sxs-lookup"><span data-stu-id="6ff89-620">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="6ff89-621">Der `AddHeader`-Filter wird daher nie für die `SomeResource`-Aktion ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-621">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="6ff89-622">Dasselbe Verhalten würde auch dann auftreten, wenn beide Filter auf Aktionsmethodenebene angewendet würden, wenn `ShortCircuitingResourceFilter` zuerst ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="6ff89-622">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="6ff89-623">Der `ShortCircuitingResourceFilter` wird aufgrund seines Filtertyps oder durch die explizite Verwendung der `Order`-Eigenschaft zuerst ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-623">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="6ff89-624">Dependency Injection</span><span class="sxs-lookup"><span data-stu-id="6ff89-624">Dependency injection</span></span>

<span data-ttu-id="6ff89-625">Filter können nach Typ oder Instanz hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-625">Filters can be added by type or by instance.</span></span> <span data-ttu-id="6ff89-626">Wenn eine Instanz hinzugefügt wird, wird diese Instanz für jede Anforderung verwendet.</span><span class="sxs-lookup"><span data-stu-id="6ff89-626">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="6ff89-627">Wenn ein Typ hinzugefügt wird, ist der Filter typaktiviert.</span><span class="sxs-lookup"><span data-stu-id="6ff89-627">If a type is added, it's type-activated.</span></span> <span data-ttu-id="6ff89-628">Ein typaktivierter Filter bedeutet Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6ff89-628">A type-activated filter means:</span></span>

* <span data-ttu-id="6ff89-629">Für jede Anforderung wird eine Instanz erstellt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-629">An instance is created for each request.</span></span>
* <span data-ttu-id="6ff89-630">Alle Konstruktorabhängigkeiten werden durch [Dependency Injection](xref:fundamentals/dependency-injection) (DI) aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-630">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="6ff89-631">Filter, die als Attribute implementiert und Controllerklassen oder Aktionsmethoden direkt hinzugefügt werden, können keine Konstruktorabhängigkeiten von [Dependency Injection](xref:fundamentals/dependency-injection) (DI) erhalten.</span><span class="sxs-lookup"><span data-stu-id="6ff89-631">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="6ff89-632">Konstruktorabhängigkeiten können aus folgenden Gründen von DI nicht bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="6ff89-632">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="6ff89-633">Für Attribute müssen Konstruktorparameter bereitgestellt werden, wenn sie angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-633">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="6ff89-634">Dies ist eine Einschränkung der Funktionsweise von Attributen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-634">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="6ff89-635">Die folgenden Filter unterstützen von DI bereitgestellte Konstruktorabhängigkeiten:</span><span class="sxs-lookup"><span data-stu-id="6ff89-635">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="6ff89-636"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>, im Attribut implementiert</span><span class="sxs-lookup"><span data-stu-id="6ff89-636"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="6ff89-637">Die oben genannten Filter können auf einen Controller oder eine Aktionsmethode angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="6ff89-637">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="6ff89-638">DI bietet Protokollierungen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-638">Loggers are available from DI.</span></span> <span data-ttu-id="6ff89-639">Vermeiden Sie es jedoch, Filter nur zum Zweck der Protokollierung zu erstellen und zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-639">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="6ff89-640">Die integrierte [Frameworkprotokollierung](xref:fundamentals/logging/index) bietet in der Regel alle Elemente, die für die Protokollierung erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="6ff89-640">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="6ff89-641">Wenn Protokollierung zu Filtern hinzugefügt wird, gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6ff89-641">Logging added to filters:</span></span>

* <span data-ttu-id="6ff89-642">Die Protokollierung sollte mit Schwerpunkt auf geschäftlichen Aspekten oder verhaltensspezifisch für den Filter erfolgen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-642">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="6ff89-643">Aktionen oder andere Frameworkereignisse sollten **nicht** protokolliert werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-643">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="6ff89-644">Die integrierten Filter protokollieren Aktionen und Frameworkereignisse.</span><span class="sxs-lookup"><span data-stu-id="6ff89-644">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="6ff89-645">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="6ff89-645">ServiceFilterAttribute</span></span>

<span data-ttu-id="6ff89-646">Die Typen der Dienstfilterimplementierung werden in `ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="6ff89-646">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="6ff89-647">Ein <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> ruft eine Instanz des Filter von DI ab.</span><span class="sxs-lookup"><span data-stu-id="6ff89-647">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="6ff89-648">Der folgende Code zeigt den `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="6ff89-648">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="6ff89-649">Im folgenden Code wird `AddHeaderResultServiceFilter` zum DI-Container hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-649">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="6ff89-650">Im folgenden Code ruft das `ServiceFilter`-Attribut eine Instanz des `AddHeaderResultServiceFilter`-Filters aus DI ab:</span><span class="sxs-lookup"><span data-stu-id="6ff89-650">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="6ff89-651">Beim Verwenden von `ServiceFilterAttribute` wird [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) festgelegt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-651">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="6ff89-652">Gibt einen Hinweis darauf, dass die Filterinstanz *möglicherweise* außerhalb des Anforderungsbereichs, in dem sie erstellt wurde, wiederverwendet wird.</span><span class="sxs-lookup"><span data-stu-id="6ff89-652">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="6ff89-653">Die ASP.NET Core-Runtime bietet keine Garantie für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6ff89-653">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="6ff89-654">Eine einzelne Instanz des Filters wird erstellt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-654">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="6ff89-655">Der Filter wird nicht zu einem späteren Zeitpunkt vom DI-Container erneut angefordert.</span><span class="sxs-lookup"><span data-stu-id="6ff89-655">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="6ff89-656">Sollte nicht mit einem Filter verwendet werden, der von Diensten mit einer anderen Lebensdauer als der eines Singletons abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="6ff89-656">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="6ff89-657"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementiert <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="6ff89-657"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="6ff89-658">`IFilterFactory` macht die <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*>-Methode zum Erstellen einer <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>-Instanz verfügbar.</span><span class="sxs-lookup"><span data-stu-id="6ff89-658">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="6ff89-659">`CreateInstance` lädt den angegebenen Typ aus DI.</span><span class="sxs-lookup"><span data-stu-id="6ff89-659">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="6ff89-660">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="6ff89-660">TypeFilterAttribute</span></span>

<span data-ttu-id="6ff89-661"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> ähnelt <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>. Der zugehörige Typ wird allerdings nicht direkt vom DI-Container aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="6ff89-661"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="6ff89-662">Stattdessen wird der Typ mithilfe von <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> instanziiert.</span><span class="sxs-lookup"><span data-stu-id="6ff89-662">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="6ff89-663">Da `TypeFilterAttribute`-Typen nicht direkt vom DI-Container aufgelöst werden, gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6ff89-663">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="6ff89-664">Typen, auf die mithilfe von `TypeFilterAttribute` verwiesen wird, müssen nicht beim DI-Container registriert werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-664">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="6ff89-665">Ihre Abhängigkeiten werden vom DI-Container erfüllt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-665">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="6ff89-666">`TypeFilterAttribute` kann optional Konstruktorargumente für den Typ akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="6ff89-666">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="6ff89-667">Beim Verwenden von `TypeFilterAttribute` wird [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) festgelegt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-667">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="6ff89-668">Gibt einen Hinweis darauf, dass die Filterinstanz *möglicherweise* außerhalb des Anforderungsbereichs wiederverwendet wird, in dem sie erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="6ff89-668">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="6ff89-669">Die ASP.NET Core-Runtime bietet keine Garantie dafür, dass eine einzelne Instanz des Filters erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="6ff89-669">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="6ff89-670">Sollte nicht mit einem Filter verwendet werden, der von Diensten mit einer anderen Lebensdauer als der eines Singletons abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="6ff89-670">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="6ff89-671">Das folgende Beispiel zeigt, wie Sie Argumente durch Verwendung von `TypeFilterAttribute` an einen Typ übergeben:</span><span class="sxs-lookup"><span data-stu-id="6ff89-671">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="6ff89-672">Autorisierungsfilter</span><span class="sxs-lookup"><span data-stu-id="6ff89-672">Authorization filters</span></span>

<span data-ttu-id="6ff89-673">Für Autorisierungsfilter gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6ff89-673">Authorization filters:</span></span>

* <span data-ttu-id="6ff89-674">Sie werden als erste Filter in der Filterpipeline ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-674">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="6ff89-675">Sie steuern den Zugriff auf Aktionsmethoden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-675">Control access to action methods.</span></span>
* <span data-ttu-id="6ff89-676">Sie verfügen nur über eine Methode, die vor der Aktion ausgeführt wird, nicht jedoch über eine Methode, die nach der Aktion ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="6ff89-676">Have a before method, but no after method.</span></span>

<span data-ttu-id="6ff89-677">Benutzerdefinierte Autorisierungsfilter erfordern ein benutzerdefiniertes Autorisierungsframework.</span><span class="sxs-lookup"><span data-stu-id="6ff89-677">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="6ff89-678">Statt einen benutzerdefinierten Filter zu schreiben, sollten Sie die Autorisierungsrichtlinien konfigurieren oder eine benutzerdefinierte Autorisierungsrichtlinie schreiben.</span><span class="sxs-lookup"><span data-stu-id="6ff89-678">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="6ff89-679">Für den integrierten Autorisierungsfilter gilt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-679">The built-in authorization filter:</span></span>

* <span data-ttu-id="6ff89-680">Er ruft das Autorisierungssystem auf.</span><span class="sxs-lookup"><span data-stu-id="6ff89-680">Calls the authorization system.</span></span>
* <span data-ttu-id="6ff89-681">Er autorisiert keine Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-681">Does not authorize requests.</span></span>

<span data-ttu-id="6ff89-682">Lösen Sie **keine** Ausnahmen in Autorisierungsfiltern aus:</span><span class="sxs-lookup"><span data-stu-id="6ff89-682">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="6ff89-683">Die Ausnahme wird nicht behandelt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-683">The exception will not be handled.</span></span>
* <span data-ttu-id="6ff89-684">Ausnahmefilter behandeln die Ausnahme nicht.</span><span class="sxs-lookup"><span data-stu-id="6ff89-684">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="6ff89-685">Beim Auftreten einer Ausnahme in einem Autorisierungsfilter sollten Sie eine Abfrage erwägen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-685">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="6ff89-686">Weitere Informationen finden Sie unter [Autorisierung](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="6ff89-686">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="6ff89-687">Ressourcenfilter</span><span class="sxs-lookup"><span data-stu-id="6ff89-687">Resource filters</span></span>

<span data-ttu-id="6ff89-688">Für Ressourcenfilter gilt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-688">Resource filters:</span></span>

* <span data-ttu-id="6ff89-689">Sie implementieren entweder die Schnittstelle <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="6ff89-689">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="6ff89-690">Die Ausführung umschließt den größten Teil der Filterpipeline.</span><span class="sxs-lookup"><span data-stu-id="6ff89-690">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="6ff89-691">Nur [Autorisierungsfilter](#authorization-filters) werden vor Ressourcenfiltern ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-691">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="6ff89-692">Ressourcenfilter sind hilfreich, um den größten Teil der Pipeline kurzzuschließen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-692">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="6ff89-693">Ein Cachingfilter kann beispielsweise bei einem Cachetreffer den Rest der Pipeline überspringen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-693">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="6ff89-694">Beispiele für Ressourcenfilter:</span><span class="sxs-lookup"><span data-stu-id="6ff89-694">Resource filter examples:</span></span>

* <span data-ttu-id="6ff89-695">Der oben gezeigte [Ressourcenfilter zum Kurzschließen](#short-circuiting-resource-filter).</span><span class="sxs-lookup"><span data-stu-id="6ff89-695">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="6ff89-696">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="6ff89-696">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="6ff89-697">Verhindert, dass die Modellbindung auf die Formulardaten zugreift.</span><span class="sxs-lookup"><span data-stu-id="6ff89-697">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="6ff89-698">Wird bei großen Dateiuploads verwendet, um zu verhindern, dass die Formulardaten in den Arbeitsspeicher eingelesen werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-698">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="6ff89-699">Aktionsfilter</span><span class="sxs-lookup"><span data-stu-id="6ff89-699">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6ff89-700">Aktionsfilter gelten **nicht** für Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="6ff89-700">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="6ff89-701">Razor Pages unterstützen <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> und <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter>.</span><span class="sxs-lookup"><span data-stu-id="6ff89-701">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="6ff89-702">Weitere Informationen finden Sie unter [Filtermethoden für Razor-Seiten](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="6ff89-702">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="6ff89-703">Für Aktionsfilter gilt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-703">Action filters:</span></span>

* <span data-ttu-id="6ff89-704">Sie implementieren entweder die Schnittstelle <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="6ff89-704">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="6ff89-705">Ihre Ausführung umfasst die Ausführung von Aktionsmethoden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-705">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="6ff89-706">Der folgende Code zeigt einen Beispielaktionsfilter:</span><span class="sxs-lookup"><span data-stu-id="6ff89-706">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="6ff89-707"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> bietet folgende Eigenschaften:</span><span class="sxs-lookup"><span data-stu-id="6ff89-707">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="6ff89-708"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> ermöglicht das Lesen der Eingaben für eine Aktionsmethode.</span><span class="sxs-lookup"><span data-stu-id="6ff89-708"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="6ff89-709"><xref:Microsoft.AspNetCore.Mvc.Controller> ermöglicht das Ändern der Controllerinstanz.</span><span class="sxs-lookup"><span data-stu-id="6ff89-709"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="6ff89-710"><xref:System.Web.Mvc.ActionExecutingContext.Result>: Die Festlegung von `Result` schließt die Ausführung der Aktionsmethode und der nachfolgenden Aktionsfilter kurz.</span><span class="sxs-lookup"><span data-stu-id="6ff89-710"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="6ff89-711">Durch Auslösen einer Ausnahme in einer Aktionsmethode geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6ff89-711">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="6ff89-712">Die Ausführung nachfolgender Filter wird verhindert.</span><span class="sxs-lookup"><span data-stu-id="6ff89-712">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="6ff89-713">Im Gegensatz zum Festlegen von `Result` wird die ausgelöste Ausnahme wird nicht als erfolgreiches Ergebnis, sondern als Fehler behandelt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-713">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="6ff89-714"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> bietet `Controller` und `Result` sowie die folgenden Eigenschaften:</span><span class="sxs-lookup"><span data-stu-id="6ff89-714">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="6ff89-715"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> ist TRUE, wenn die Aktionsausführung durch einen anderen Filter kurzgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="6ff89-715"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="6ff89-716"><xref:System.Web.Mvc.ActionExecutedContext.Exception> ist ungleich NULL, wenn die Aktion oder ein zuvor ausgeführter Aktionsfilter eine Ausnahme ausgelöst hat.</span><span class="sxs-lookup"><span data-stu-id="6ff89-716"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="6ff89-717">Durch Festlegen dieser Eigenschaft auf NULL geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6ff89-717">Setting this property to null:</span></span>

  * <span data-ttu-id="6ff89-718">Die Ausnahme wird effektiv behandelt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-718">Effectively handles the exception.</span></span>
  * <span data-ttu-id="6ff89-719">`Result` wird so ausgeführt, als wäre das Ergebnis von der Aktionsmethode zurückgegeben worden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-719">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="6ff89-720">Bei einem `IAsyncActionFilter` hat der Aufruf von <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> folgende Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="6ff89-720">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="6ff89-721">Alle nachfolgenden Aktionsfilter und die Aktionsmethode werden ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-721">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="6ff89-722">Gibt `ActionExecutedContext`zurück.</span><span class="sxs-lookup"><span data-stu-id="6ff89-722">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="6ff89-723">Weisen Sie einer Ergebnisinstanz <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> zu, und rufen Sie nicht `next` (den `ActionExecutionDelegate`) auf, um die Pipeline kurzzuschließen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-723">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="6ff89-724">Das Framework stellt ein abstraktes <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> bereit, das als Unterklasse verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="6ff89-724">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="6ff89-725">Der Aktionsfilter `OnActionExecuting` kann für Folgendes verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="6ff89-725">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="6ff89-726">Überprüfen des Modellzustands.</span><span class="sxs-lookup"><span data-stu-id="6ff89-726">Validate model state.</span></span>
* <span data-ttu-id="6ff89-727">Zurückgeben eines Fehlers, wenn der Zustand ungültig ist.</span><span class="sxs-lookup"><span data-stu-id="6ff89-727">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="6ff89-728">Die Methode `OnActionExecuted` wird nach der Aktionsmethode ausgeführt. Es gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6ff89-728">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="6ff89-729">Die Methode kann die Ergebnisse der Aktion durch die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> sehen und ändern.</span><span class="sxs-lookup"><span data-stu-id="6ff89-729">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="6ff89-730"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> wird auf TRUE festgelegt, wenn die Ausführung der Aktion durch einen anderen Filter kurzgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="6ff89-730"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="6ff89-731"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> wird auf einen Wert ungleich NULL festgelegt, wenn die Aktion oder ein nachfolgender Aktionsfilter eine Ausnahme ausgelöst hat.</span><span class="sxs-lookup"><span data-stu-id="6ff89-731"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="6ff89-732">Wenn für `Exception` NULL festgelegt wird,</span><span class="sxs-lookup"><span data-stu-id="6ff89-732">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="6ff89-733">werden Ausnahmen effektiv behandelt,</span><span class="sxs-lookup"><span data-stu-id="6ff89-733">Effectively handles an exception.</span></span>
  * <span data-ttu-id="6ff89-734">und `ActionExecutedContext.Result` wird so ausgeführt, als würde die Eigenschaft normal von der Aktionsmethode zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-734">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="6ff89-735">Ausnahmefilter</span><span class="sxs-lookup"><span data-stu-id="6ff89-735">Exception filters</span></span>

<span data-ttu-id="6ff89-736">Für Ausnahmefilter gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6ff89-736">Exception filters:</span></span>

* <span data-ttu-id="6ff89-737">Sie implementieren <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="6ff89-737">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="6ff89-738">Sie können verwendet werden, um gängige Fehlerbehandlungsrichtlinien zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="6ff89-738">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="6ff89-739">Der folgende Beispielausnahmefilter verwendet eine benutzerdefinierte Fehleransicht, um Details zu Ausnahmen anzuzeigen, die auftreten, wenn die App sich in der Entwicklung befindet:</span><span class="sxs-lookup"><span data-stu-id="6ff89-739">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="6ff89-740">Für Ausnahmefilter gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6ff89-740">Exception filters:</span></span>

* <span data-ttu-id="6ff89-741">Sie verfügen nicht über vorangehende oder darauffolgende Ereignisse.</span><span class="sxs-lookup"><span data-stu-id="6ff89-741">Don't have before and after events.</span></span>
* <span data-ttu-id="6ff89-742">Sie implementieren <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="6ff89-742">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="6ff89-743">Sie behandeln Ausnahmefehler, die bei der Erstellung von Razor Pages oder Controllern, bei der [Modellbindung](xref:mvc/models/model-binding), bei Aktionsfiltern oder bei Aktionsmethoden auftreten.</span><span class="sxs-lookup"><span data-stu-id="6ff89-743">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="6ff89-744">Sie erfassen **keine** Ausnahmen, die in Ressourcenfiltern, Ergebnisfiltern oder bei der Ausführung von MVC-Ergebnissen auftreten.</span><span class="sxs-lookup"><span data-stu-id="6ff89-744">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="6ff89-745">Um eine Ausnahme zu behandeln, muss die Eigenschaft <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> auf `true` festgelegt oder eine Antwort geschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-745">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="6ff89-746">Dadurch wird die Weitergabe der Ausnahme beendet.</span><span class="sxs-lookup"><span data-stu-id="6ff89-746">This stops propagation of the exception.</span></span> <span data-ttu-id="6ff89-747">Ein Ausnahmefilter kann eine Ausnahme nicht in ein „erfolgreiches Ergebnis“ umwandeln.</span><span class="sxs-lookup"><span data-stu-id="6ff89-747">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="6ff89-748">Nur ein Aktionsfilter kann das.</span><span class="sxs-lookup"><span data-stu-id="6ff89-748">Only an action filter can do that.</span></span>

<span data-ttu-id="6ff89-749">Für Ausnahmefilter gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6ff89-749">Exception filters:</span></span>

* <span data-ttu-id="6ff89-750">Sie eignen sich für das Abfangen von Ausnahmen, die in Aktionen auftreten.</span><span class="sxs-lookup"><span data-stu-id="6ff89-750">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="6ff89-751">Sie sind im Vergleich zu Middleware für die Fehlerbehandlung weniger flexibel.</span><span class="sxs-lookup"><span data-stu-id="6ff89-751">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="6ff89-752">Sie sollten bei der Ausnahmebehandlung vorzugsweise Middleware verwenden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-752">Prefer middleware for exception handling.</span></span> <span data-ttu-id="6ff89-753">Verwenden Sie Ausnahmefilter nur, wenn sich die Fehlerbehandlung *unterscheidet*, je nachdem, welche Aktionsmethode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="6ff89-753">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="6ff89-754">Zum Beispiel verfügt eine App möglicherweise über Aktionsmethoden für beide API-Endpunkte und für Ansichten bzw. HTML.</span><span class="sxs-lookup"><span data-stu-id="6ff89-754">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="6ff89-755">Die API-Endpunkte können Fehlerinformationen als JSON zurückgeben, während ansichtsbasierte Aktionen eine Fehlerseite als HTML zurückgeben können.</span><span class="sxs-lookup"><span data-stu-id="6ff89-755">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="6ff89-756">Ergebnisfilter</span><span class="sxs-lookup"><span data-stu-id="6ff89-756">Result filters</span></span>

<span data-ttu-id="6ff89-757">Für Ergebnisfilter gilt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-757">Result filters:</span></span>

* <span data-ttu-id="6ff89-758">Implementieren einer Schnittstelle:</span><span class="sxs-lookup"><span data-stu-id="6ff89-758">Implement an interface:</span></span>
  * <span data-ttu-id="6ff89-759"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="6ff89-759"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="6ff89-760"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="6ff89-760"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="6ff89-761">Ihre Ausführung umfasst die Ausführung von Aktionsergebnissen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-761">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="6ff89-762">IResultFilter und IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="6ff89-762">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="6ff89-763">Der folgende Code zeigt einen Ergebnisfilter, der einen HTTP-Header hinzufügt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-763">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="6ff89-764">Die Art des Ergebnisses, das ausgeführt wird, hängt von der jeweiligen Aktion ab.</span><span class="sxs-lookup"><span data-stu-id="6ff89-764">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="6ff89-765">Eine Aktion, die eine Ansicht zurückgibt, würde die gesamte Razor-Verarbeitung als Teil der Ausführung von <xref:Microsoft.AspNetCore.Mvc.ViewResult> beinhalten.</span><span class="sxs-lookup"><span data-stu-id="6ff89-765">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="6ff89-766">Eine API-Methode führt möglicherweise eine Art Serialisierung als Teil der Ausführung des Ergebnisses durch.</span><span class="sxs-lookup"><span data-stu-id="6ff89-766">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="6ff89-767">Erfahren Sie mehr zu [Aktionsergebnissen](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="6ff89-767">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="6ff89-768">Ergebnisfilter werden nur ausgeführt, wenn eine Aktion oder ein Aktionsfilter ein Aktionsergebnis liefert.</span><span class="sxs-lookup"><span data-stu-id="6ff89-768">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="6ff89-769">Ergebnisfilter werden in folgenden Fällen nicht ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-769">Result filters are not executed when:</span></span>

* <span data-ttu-id="6ff89-770">Ein Autorisierungs- oder Ressourcenfilter schließt die Pipeline kurz.</span><span class="sxs-lookup"><span data-stu-id="6ff89-770">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="6ff89-771">Ein Ausnahmefilter behandelt eine Ausnahme durch Erzeugen eines Aktionsergebnisses.</span><span class="sxs-lookup"><span data-stu-id="6ff89-771">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="6ff89-772">Die Methode <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> kann die Ausführung des Aktionsergebnisses und der nachfolgenden Ergebnisfilter durch Festlegen von <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> auf `true` kurzschließen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-772">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="6ff89-773">Schreiben Sie beim Kurzschließen in das Antwortobjekt, um zu verhindern, dass eine leere Antwort generiert wird.</span><span class="sxs-lookup"><span data-stu-id="6ff89-773">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="6ff89-774">Beim Auslösen einer Ausnahme in `IResultFilter.OnResultExecuting` geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6ff89-774">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="6ff89-775">Die Ausführung des Aktionsergebnisses und der nachfolgenden Filter wird verhindert.</span><span class="sxs-lookup"><span data-stu-id="6ff89-775">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="6ff89-776">Die ausgelöste Ausnahme wird nicht als erfolgreiches Ergebnis, sondern als Fehler behandelt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-776">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="6ff89-777">Wenn die <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>-Methode ausgeführt wird, wurde die Antwort wahrscheinlich bereits an den Client gesendet.</span><span class="sxs-lookup"><span data-stu-id="6ff89-777">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="6ff89-778">Wenn die Antwort bereits an den Client gesendet wurde, kann sie nicht mehr geändert werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-778">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="6ff89-779">`ResultExecutedContext.Canceled` wird auf `true` festgelegt, wenn die Ausführung des Aktionsergebnisses durch einen anderen Filter kurzgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="6ff89-779">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="6ff89-780">`ResultExecutedContext.Exception` wird auf einen Wert ungleich NULL festgelegt, wenn das Aktionsergebnis oder ein nachfolgender Ergebnisfilter eine Ausnahme ausgelöst hat.</span><span class="sxs-lookup"><span data-stu-id="6ff89-780">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="6ff89-781">Das Festlegen von `Exception` auf NULL behandelt eine Ausnahme effektiv und verhindert, dass die Ausnahme später in der Pipeline erneut von ASP.NET Core ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="6ff89-781">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="6ff89-782">Beim Behandeln einer Ausnahme in einem Ergebnisfilter gibt es keine zuverlässige Möglichkeit, Daten in eine Antwort zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="6ff89-782">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="6ff89-783">Wenn ein Aktionsergebnis eine Ausnahme auslöst und die Header bereits an den Client übergeben wurden, gibt es keinen zuverlässigen Mechanismus, einen Fehlercode zu senden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-783">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="6ff89-784">Bei einem <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> führt ein Aufruf von `await next` im <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> alle nachfolgenden Ergebnisfilter und das Aktionsergebnis aus.</span><span class="sxs-lookup"><span data-stu-id="6ff89-784">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="6ff89-785">Zum Kurzschließen legen Sie [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) auf `true` fest und rufen den `ResultExecutionDelegate` nicht auf:</span><span class="sxs-lookup"><span data-stu-id="6ff89-785">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="6ff89-786">Das Framework stellt ein abstraktes `ResultFilterAttribute` bereit, das als Unterklasse verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="6ff89-786">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="6ff89-787">Die weiter oben gezeigte Klasse [AddHeaderAttribute](#add-header-attribute) ist ein Beispiel für ein Ergebnisfilterattribut.</span><span class="sxs-lookup"><span data-stu-id="6ff89-787">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="6ff89-788">IAlwaysRunResultFilter und IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="6ff89-788">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="6ff89-789">Die Schnittstellen <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> und <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklarieren eine <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter>-Implementierung, die für alle Aktionsergebnisse ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="6ff89-789">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="6ff89-790">Dies schließt Aktionsergebnisse ein, die von folgenden Filtern generiert werden:</span><span class="sxs-lookup"><span data-stu-id="6ff89-790">This includes action results produced by:</span></span>

* <span data-ttu-id="6ff89-791">Autorisierungs- und Ressourcenfilter, die einen Kurzschluss bewirken</span><span class="sxs-lookup"><span data-stu-id="6ff89-791">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="6ff89-792">Ausnahmefilter</span><span class="sxs-lookup"><span data-stu-id="6ff89-792">Exception filters.</span></span>

<span data-ttu-id="6ff89-793">Beispielsweise wird der folgende Filter immer ausgeführt und legt ein Aktionsergebnis (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) mit dem Statuscode *422 – Einheit kann nicht bearbeitet werden* fest, wenn bei der Inhaltsaushandlung ein Fehler auftritt:</span><span class="sxs-lookup"><span data-stu-id="6ff89-793">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="6ff89-794">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="6ff89-794">IFilterFactory</span></span>

<span data-ttu-id="6ff89-795"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementiert <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="6ff89-795"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="6ff89-796">Deshalb kann eine `IFilterFactory`-Instanz an einer beliebigen Stelle in der Filterpipeline als `IFilterMetadata`-Instanz verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6ff89-796">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="6ff89-797">Wenn die Runtime den Aufruf des Filters vorbereitet, versucht sie, ihn in eine `IFilterFactory` umzuwandeln.</span><span class="sxs-lookup"><span data-stu-id="6ff89-797">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="6ff89-798">Wenn diese Umwandlung gelingt, wird die Methode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> aufgerufen, um die Instanz `IFilterMetadata` für den Aufruf zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-798">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="6ff89-799">Da die exakte Filterpipeline beim Start der Anwendung nicht explizit festgelegt werden muss, wird dadurch ein sehr flexibles Design ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="6ff89-799">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="6ff89-800">Als weiteres Verfahren zum Erstellen von Filtern kann `IFilterFactory` mithilfe von benutzerdefinierten Attributimplementierungen implementiert werden:</span><span class="sxs-lookup"><span data-stu-id="6ff89-800">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="6ff89-801">Der oben stehende Code kann durch Ausführen des [Downloadbeispiels](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) getestet werden:</span><span class="sxs-lookup"><span data-stu-id="6ff89-801">The preceding code can be tested by running the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="6ff89-802">Rufen Sie die F12-Entwicklungstools auf.</span><span class="sxs-lookup"><span data-stu-id="6ff89-802">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="6ff89-803">Navigieren Sie zu `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="6ff89-803">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="6ff89-804">Die F12-Entwicklungstools zeigen folgende Antwortheader an, die vom Beispielcode hinzugefügt wurden:</span><span class="sxs-lookup"><span data-stu-id="6ff89-804">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="6ff89-805">**author:** `Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="6ff89-805">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="6ff89-806">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="6ff89-806">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="6ff89-807">**internal:** `My header`</span><span class="sxs-lookup"><span data-stu-id="6ff89-807">**internal:** `My header`</span></span>

<span data-ttu-id="6ff89-808">Der oben stehende Code erstellt den Antwortheader „**internal:** `My header`“.</span><span class="sxs-lookup"><span data-stu-id="6ff89-808">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="6ff89-809">In einem Attribut implementierte IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="6ff89-809">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="6ff89-810">Filter, die `IFilterFactory` implementieren, eignen sich in folgenden Fällen:</span><span class="sxs-lookup"><span data-stu-id="6ff89-810">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="6ff89-811">Sie erfordern kein Übergeben von Parametern.</span><span class="sxs-lookup"><span data-stu-id="6ff89-811">Don't require passing parameters.</span></span>
* <span data-ttu-id="6ff89-812">Sie verfügen über Konstruktorabhängigkeiten, die durch DI erfüllt werden müssen.</span><span class="sxs-lookup"><span data-stu-id="6ff89-812">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="6ff89-813"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementiert <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="6ff89-813"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="6ff89-814">`IFilterFactory` macht die <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*>-Methode zum Erstellen einer <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>-Instanz verfügbar.</span><span class="sxs-lookup"><span data-stu-id="6ff89-814">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="6ff89-815">`CreateInstance` lädt den angegebenen Typ aus dem Dienstecontainer (DI).</span><span class="sxs-lookup"><span data-stu-id="6ff89-815">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="6ff89-816">Der folgende Code zeigt drei Ansätze zum Anwenden von `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="6ff89-816">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="6ff89-817">Im oben stehenden Code ist das Ergänzen der Methode um `[SampleActionFilter]` der bevorzugte Ansatz zum Anwenden von `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="6ff89-817">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="6ff89-818">Verwenden von Middleware in der Filterpipeline</span><span class="sxs-lookup"><span data-stu-id="6ff89-818">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="6ff89-819">Ressourcenfilter funktionieren wie [Middleware](xref:fundamentals/middleware/index), sie umschließen alle Ausführungen, die später in der Pipeline enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="6ff89-819">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="6ff89-820">Filter unterscheiden sich jedoch in der Hinsicht von Middleware, dass sie Teil der ASP.NET Core-Runtime sind, was bedeutet, dass sie Zugriff auf ASP.NET Core-Kontext und -Konstrukte haben.</span><span class="sxs-lookup"><span data-stu-id="6ff89-820">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="6ff89-821">Um Middleware als Filter zu verwenden, erstellen Sie einen Typ mit einer `Configure`-Methode, die die Middleware festlegt, die in die Filterpipeline eingefügt werden soll.</span><span class="sxs-lookup"><span data-stu-id="6ff89-821">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="6ff89-822">Das folgende Beispiel verwendet die Lokalisierungsmiddleware, um die aktuelle Kultur einer Anforderung einzurichten:</span><span class="sxs-lookup"><span data-stu-id="6ff89-822">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="6ff89-823">Verwenden Sie das <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> zum Ausführen der Middleware:</span><span class="sxs-lookup"><span data-stu-id="6ff89-823">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="6ff89-824">Middlewarefilter werden zum selben Zeitpunkt in der Filterpipeline wie Ressourcenfilter, vor der Modellbindung und nach dem Rest der Pipeline ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="6ff89-824">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="6ff89-825">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="6ff89-825">Next actions</span></span>

* <span data-ttu-id="6ff89-826">Siehe [Filtermethoden für Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="6ff89-826">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="6ff89-827">Um Filter näher kennenzulernen, [laden Sie das GitHub-Beispiel herunter, und testen und bearbeiten Sie es](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="6ff89-827">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end

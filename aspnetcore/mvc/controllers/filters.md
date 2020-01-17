---
title: Filter in ASP.NET Core
author: Rick-Anderson
description: Erfahren Sie, wie Filter funktionieren und wie Sie sie in ASP.NET Core verwenden.
ms.author: riande
ms.custom: mvc
ms.date: 1/1/2020
uid: mvc/controllers/filters
ms.openlocfilehash: 759c150e7f35f3f6a52947edc5ef41448dc227fe
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828970"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="815d6-103">Filter in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="815d6-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="815d6-104">Von [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="815d6-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="815d6-105">*Filter* ermöglichen es in ASP.NET Core, Code vor oder nach bestimmten Phasen der Anforderungsverarbeitungspipeline auszuführen.</span><span class="sxs-lookup"><span data-stu-id="815d6-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="815d6-106">Integrierte Filter sind für folgende Aufgaben zuständig:</span><span class="sxs-lookup"><span data-stu-id="815d6-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="815d6-107">Autorisierung (der Zugriff auf Ressourcen, für die ein Benutzer nicht autorisiert ist, wird verhindert)</span><span class="sxs-lookup"><span data-stu-id="815d6-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="815d6-108">Zwischenspeicherung von Antworten (die Anforderungspipeline wird unterbrochen, damit eine zwischengespeicherte Antwort zurückgegeben wird)</span><span class="sxs-lookup"><span data-stu-id="815d6-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="815d6-109">Durch die Erstellung benutzerdefinierter Filter kann mit aktionsübergreifenden Problemen umgegangen werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="815d6-110">Zu solchen übergreifenden Problemen gehören beispielsweise Fehlerbehandlung, Caching, Konfiguration, Autorisierung und Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="815d6-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="815d6-111">Mit Filtern lässt sich die Duplizierung von Code vermeiden.</span><span class="sxs-lookup"><span data-stu-id="815d6-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="815d6-112">Sie können zum Beispiel die Fehlerbehandlung in einem Ausnahmefilter konsolidieren.</span><span class="sxs-lookup"><span data-stu-id="815d6-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="815d6-113">Dieses Dokument gilt für Razor Pages, API-Controller und Controller mit Ansichten.</span><span class="sxs-lookup"><span data-stu-id="815d6-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="815d6-114">[Zeigen Sie ein Beispiel an, oder laden Sie es herunter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="815d6-114">[View or download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="815d6-115">Die Funktionsweise von Filtern</span><span class="sxs-lookup"><span data-stu-id="815d6-115">How filters work</span></span>

<span data-ttu-id="815d6-116">Filter werden in der *ASP.NET Core-Aktionsaufrufpipeline* ausgeführt, die manchmal auch als *Filterpipeline* bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="815d6-116">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="815d6-117">Die Filterpipeline wird ausgeführt, nachdem ASP.NET Core die auszuführende Aktion ausgewählt hat.</span><span class="sxs-lookup"><span data-stu-id="815d6-117">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Die Anforderung wird von weiterer Middleware, Routingmiddleware, Aktionsauswahl und der Aktionsaufrufpipeline verarbeitet.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="815d6-120">Filtertypen</span><span class="sxs-lookup"><span data-stu-id="815d6-120">Filter types</span></span>

<span data-ttu-id="815d6-121">Jeder Filtertyp wird in einer anderen Phase der Filterpipeline ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="815d6-121">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="815d6-122">[Autorisierungsfilter](#authorization-filters) werden zuerst ausgeführt und dienen dazu, festzustellen, ob der Benutzer für die Anforderung berechtigt ist.</span><span class="sxs-lookup"><span data-stu-id="815d6-122">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="815d6-123">Autorisierungsfilter schließen die Pipeline kurz, wenn die Anforderung nicht autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="815d6-123">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="815d6-124">[Ressourcenfilter](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="815d6-124">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="815d6-125">Werden nach der Autorisierung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="815d6-125">Run after authorization.</span></span>  
  * <span data-ttu-id="815d6-126"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> führt Code vor dem Rest der Filterpipeline aus.</span><span class="sxs-lookup"><span data-stu-id="815d6-126"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="815d6-127">Beispielsweise führt `OnResourceExecuting` Code vor der Modellbindung aus.</span><span class="sxs-lookup"><span data-stu-id="815d6-127">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="815d6-128"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> führt Code aus, nachdem der Rest der Pipeline abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="815d6-128"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="815d6-129">[Für Aktionsfilter gilt Folgendes](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="815d6-129">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="815d6-130">Sie führen Code unmittelbar vor und nach dem Aufruf einer Aktionsmethode aus.</span><span class="sxs-lookup"><span data-stu-id="815d6-130">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="815d6-131">Sie können die an eine Aktion übergebenen Argumente ändern.</span><span class="sxs-lookup"><span data-stu-id="815d6-131">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="815d6-132">Sie können das von der Aktion zurückgegebene Ergebnis ändern.</span><span class="sxs-lookup"><span data-stu-id="815d6-132">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="815d6-133">Sie werden in Razor Pages **nicht** unterstützt.</span><span class="sxs-lookup"><span data-stu-id="815d6-133">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="815d6-134">[Ausnahmefilter](#exception-filters) wenden globale Richtlinien auf unbehandelte Ausnahmen an, die auftreten, bevor etwas in den Antworttext geschrieben wurde.</span><span class="sxs-lookup"><span data-stu-id="815d6-134">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="815d6-135">[Ergebnisfilter](#result-filters) führen Code unmittelbar vor und nach der Ausführung von Aktionsergebnissen aus.</span><span class="sxs-lookup"><span data-stu-id="815d6-135">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="815d6-136">Sie werden nur ausgeführt, wenn die Aktionsmethode erfolgreich ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="815d6-136">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="815d6-137">Ergebnisfilter sind hilfreich für Logik, die die Ausführung von Ansichten oder Formatierern umfasst.</span><span class="sxs-lookup"><span data-stu-id="815d6-137">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="815d6-138">Das folgende Diagramm veranschaulicht, wie die Filtertypen mit der Filterpipeline interagieren.</span><span class="sxs-lookup"><span data-stu-id="815d6-138">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Die Anforderung wird von Autorisierungsfilter, Ressourcenfilter, Modellbindung, Aktionsfilter, Aktionsausführung sowie Aktionsergebniskonvertierung, Ausnahmefilter, Ergebnisfilter und Ergebnisausführung verarbeitet.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="815d6-141">Implementierung</span><span class="sxs-lookup"><span data-stu-id="815d6-141">Implementation</span></span>

<span data-ttu-id="815d6-142">Filter unterstützen sowohl synchrone als auch asynchrone Implementierungen über verschiedene Schnittstellendefinitionen.</span><span class="sxs-lookup"><span data-stu-id="815d6-142">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="815d6-143">Synchrone Filter führen Code vor und nach der jeweiligen Pipelinephase aus.</span><span class="sxs-lookup"><span data-stu-id="815d6-143">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="815d6-144"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> beispielsweise wird aufgerufen, bevor die Aktionsmethode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="815d6-144">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="815d6-145"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> wird nach der Rückgabe der Aktionsmethode aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="815d6-145"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="815d6-146">Asynchrone Filter definieren eine `On-Stage-ExecutionAsync`-Methode.</span><span class="sxs-lookup"><span data-stu-id="815d6-146">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="815d6-147">Zum Beispiel <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span><span class="sxs-lookup"><span data-stu-id="815d6-147">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="815d6-148">Im oben stehenden Code weist der `SampleAsyncActionFilter` einen <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) auf, der die Aktionsmethode ausführt.</span><span class="sxs-lookup"><span data-stu-id="815d6-148">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="815d6-149">Mehrere Filterphasen</span><span class="sxs-lookup"><span data-stu-id="815d6-149">Multiple filter stages</span></span>

<span data-ttu-id="815d6-150">Schnittstellen für mehrere Filterphasen können in einer einzigen Klasse implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-150">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="815d6-151">Beispielsweise implementiert die <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>-Klasse:</span><span class="sxs-lookup"><span data-stu-id="815d6-151">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="815d6-152">Synchron: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> und <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="815d6-152">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="815d6-153">Asynchron: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> und <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="815d6-153">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="815d6-154">Implementieren Sie **entweder** die synchrone oder asynchrone Version einer Filterschnittstelle, aber **nicht** beide.</span><span class="sxs-lookup"><span data-stu-id="815d6-154">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="815d6-155">Die Runtime prüft zuerst, ob der Filter die asynchrone Schnittstelle implementiert, und wenn dies der Fall ist, ruft die Runtime diese Schnittstelle auf.</span><span class="sxs-lookup"><span data-stu-id="815d6-155">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="815d6-156">Wenn dies nicht der Fall ist, ruft es die Methode(n) der synchronen Schnittstelle auf.</span><span class="sxs-lookup"><span data-stu-id="815d6-156">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="815d6-157">Wenn die asynchrone und die synchrone Schnittstelle in einer einzigen Klasse implementiert sind, wird nur die asynchrone Methode aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="815d6-157">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="815d6-158">Bei Verwendung von abstrakten Klassen wie <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> überschreiben Sie nur die synchronen Methoden oder die asynchrone Methode für jeden Filtertyp.</span><span class="sxs-lookup"><span data-stu-id="815d6-158">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="815d6-159">Integrierte Filterattribute</span><span class="sxs-lookup"><span data-stu-id="815d6-159">Built-in filter attributes</span></span>

<span data-ttu-id="815d6-160">ASP.NET Core enthält integrierte attributbasierte Filter, die als Unterklasse erstellt und angepasst werden können.</span><span class="sxs-lookup"><span data-stu-id="815d6-160">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="815d6-161">Zum Beispiel fügt der folgende Ergebnisfilter der Antwort einen Header hinzu:</span><span class="sxs-lookup"><span data-stu-id="815d6-161">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="815d6-162">Wie im obigen Beispiel gezeigt, können Filter mithilfe von Attributen Argumente akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="815d6-162">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="815d6-163">Wenden Sie das `AddHeaderAttribute` auf einen Controller oder eine Aktionsmethode an, und geben Sie den Namen und den Wert des HTTP-Headers an:</span><span class="sxs-lookup"><span data-stu-id="815d6-163">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="815d6-164">Verwenden Sie ein Tool wie die [Browser-Entwicklertools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools), um die Header zu untersuchen.</span><span class="sxs-lookup"><span data-stu-id="815d6-164">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="815d6-165">Unter **Antwortheader** wird `author: Rick Anderson` angezeigt.</span><span class="sxs-lookup"><span data-stu-id="815d6-165">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="815d6-166">Mit dem folgenden Code wird ein `ActionFilterAttribute`-Element implementiert, das diese Aktionen ausführt:</span><span class="sxs-lookup"><span data-stu-id="815d6-166">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="815d6-167">Liest den Titel und den Namen aus dem Konfigurationssystem.</span><span class="sxs-lookup"><span data-stu-id="815d6-167">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="815d6-168">Im Gegensatz zum vorherigen Beispiel erfordert der folgende Code nicht, dass dem Code Filterparameter hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-168">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="815d6-169">Fügt dem Antwortheader den Titel und den Namen hinzu.</span><span class="sxs-lookup"><span data-stu-id="815d6-169">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="815d6-170">Die Konfigurationsoptionen werden vom [Konfigurationssystem](xref:fundamentals/configuration/index) mithilfe der [Optionsmuster](xref:fundamentals/configuration/options) bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="815d6-170">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="815d6-171">Beispielsweise aus der Datei *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="815d6-171">For example, from the *appsettings.json* file:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="815d6-172">In `StartUp.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="815d6-172">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="815d6-173">Die `PositionOptions`-Klasse wird dem Dienstcontainer mit dem Konfigurationsbereich `"Position"` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="815d6-173">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="815d6-174">`MyActionFilterAttribute` wird dem Dienstcontainer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="815d6-174">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="815d6-175">Der folgende Code zeigt die `PositionOptions`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="815d6-175">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="815d6-176">Der folgende Code wendet das `MyActionFilterAttribute` auf die Methode `Index2` an:</span><span class="sxs-lookup"><span data-stu-id="815d6-176">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="815d6-177">Unter **Antwortheader** werden `author: Rick Anderson` und `Editor: Joe Smith` angezeigt, wenn der `Sample/Index2`-Endpunkt aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="815d6-177">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="815d6-178">Der folgende Code wendet das `MyActionFilterAttribute` und das `AddHeaderAttribute` auf die Razor Page an:</span><span class="sxs-lookup"><span data-stu-id="815d6-178">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="815d6-179">Es können keine Filter auf Handlermethoden von Razor Pages angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-179">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="815d6-180">Sie können entweder auf das Razor Page-Modell oder global angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-180">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="815d6-181">Einige der Filterschnittstellen besitzen entsprechende Attribute, die als Basisklassen für benutzerdefinierte Implementierungen verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="815d6-181">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="815d6-182">Filterattribute:</span><span class="sxs-lookup"><span data-stu-id="815d6-182">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="815d6-183">Filterbereiche und Reihenfolge der Ausführung</span><span class="sxs-lookup"><span data-stu-id="815d6-183">Filter scopes and order of execution</span></span>

<span data-ttu-id="815d6-184">Der Pipeline kann in einem von drei *Bereichen* ein Filter hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="815d6-184">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="815d6-185">Verwenden eines Attributs für eine Controller-Aktion.</span><span class="sxs-lookup"><span data-stu-id="815d6-185">Using an attribute on a controller action.</span></span> <span data-ttu-id="815d6-186">Es können keine Filterattribute auf Handlermethoden von Razor Pages angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-186">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="815d6-187">Verwenden eines Attributs für einen Controller oder eine Razor Page.</span><span class="sxs-lookup"><span data-stu-id="815d6-187">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="815d6-188">Global für alle Controller und Aktionen, Razor Pages wie im folgenden Code gezeigt:</span><span class="sxs-lookup"><span data-stu-id="815d6-188">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="815d6-189">Standardreihenfolge der Ausführung</span><span class="sxs-lookup"><span data-stu-id="815d6-189">Default order of execution</span></span>

<span data-ttu-id="815d6-190">Wenn es mehrere Filter für eine bestimmte Stufe der Pipeline gibt, bestimmt der Bereich die Standardreihenfolge der Filterausführung.</span><span class="sxs-lookup"><span data-stu-id="815d6-190">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="815d6-191">Globale Filter umfassen Klassenfilter, welche hingegen Methodenfilter umfassen.</span><span class="sxs-lookup"><span data-stu-id="815d6-191">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="815d6-192">Als Ergebnis der Filterschachtelung wird der *nach* einer Pipelinephase auszuführende Code in umgekehrter Reihenfolge zum Code *vor* einer Pipelinephase ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="815d6-192">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="815d6-193">Filterreihenfolge:</span><span class="sxs-lookup"><span data-stu-id="815d6-193">The filter sequence:</span></span>

* <span data-ttu-id="815d6-194">Der Code von globalen Filtern, der *vor* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="815d6-194">The *before* code of global filters.</span></span>
  * <span data-ttu-id="815d6-195">Der Code von Controllerfiltern und Razor Page-Filtern, der *vor* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="815d6-195">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="815d6-196">Der Code von Aktionsmethodenfiltern, der *vor* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="815d6-196">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="815d6-197">Der Code von Aktionsmethodenfiltern, der *nach* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="815d6-197">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="815d6-198">Der Code von Controllerfiltern und Razor Page-Filtern, der *nach* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="815d6-198">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="815d6-199">Der Code von globalen Filtern, der *nach* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="815d6-199">The *after* code of global filters.</span></span>
  
<span data-ttu-id="815d6-200">Das folgende Beispiel veranschaulicht die Reihenfolge, in der Filtermethoden für synchrone Aktionsfilter aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-200">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="815d6-201">Sequenz</span><span class="sxs-lookup"><span data-stu-id="815d6-201">Sequence</span></span> | <span data-ttu-id="815d6-202">Filterbereich</span><span class="sxs-lookup"><span data-stu-id="815d6-202">Filter scope</span></span> | <span data-ttu-id="815d6-203">Filtermethode</span><span class="sxs-lookup"><span data-stu-id="815d6-203">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="815d6-204">1</span><span class="sxs-lookup"><span data-stu-id="815d6-204">1</span></span> | <span data-ttu-id="815d6-205">Global</span><span class="sxs-lookup"><span data-stu-id="815d6-205">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="815d6-206">2</span><span class="sxs-lookup"><span data-stu-id="815d6-206">2</span></span> | <span data-ttu-id="815d6-207">Controller oder Razor Page</span><span class="sxs-lookup"><span data-stu-id="815d6-207">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="815d6-208">3</span><span class="sxs-lookup"><span data-stu-id="815d6-208">3</span></span> | <span data-ttu-id="815d6-209">Methode</span><span class="sxs-lookup"><span data-stu-id="815d6-209">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="815d6-210">4</span><span class="sxs-lookup"><span data-stu-id="815d6-210">4</span></span> | <span data-ttu-id="815d6-211">Methode</span><span class="sxs-lookup"><span data-stu-id="815d6-211">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="815d6-212">5</span><span class="sxs-lookup"><span data-stu-id="815d6-212">5</span></span> | <span data-ttu-id="815d6-213">Controller oder Razor Page</span><span class="sxs-lookup"><span data-stu-id="815d6-213">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="815d6-214">6</span><span class="sxs-lookup"><span data-stu-id="815d6-214">6</span></span> | <span data-ttu-id="815d6-215">Global</span><span class="sxs-lookup"><span data-stu-id="815d6-215">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="815d6-216">Filter auf Controllerebene</span><span class="sxs-lookup"><span data-stu-id="815d6-216">Controller level filters</span></span>

<span data-ttu-id="815d6-217">Jeder Controller, der von der Basisklasse <xref:Microsoft.AspNetCore.Mvc.Controller> erbt, enthält die Methoden [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) und [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="815d6-217">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="815d6-218">Diese Methoden führen Folgendes aus:</span><span class="sxs-lookup"><span data-stu-id="815d6-218">These methods:</span></span>

* <span data-ttu-id="815d6-219">Die Filter, die für eine bestimmte Aktion ausgeführt werden, werden umschlossen.</span><span class="sxs-lookup"><span data-stu-id="815d6-219">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="815d6-220">`OnActionExecuting` wird vor allen Filtern der Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="815d6-220">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="815d6-221">`OnActionExecuted` wird nach allen Filtern der Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="815d6-221">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="815d6-222">`OnActionExecutionAsync` wird vor allen Filtern der Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="815d6-222">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="815d6-223">Code im Filter nach `next` wird nach der Aktionsmethode ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="815d6-223">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="815d6-224">Im Downloadbeispiel wird `MySampleActionFilter` global beim Start angewendet.</span><span class="sxs-lookup"><span data-stu-id="815d6-224">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="815d6-225">Die `TestController`:</span><span class="sxs-lookup"><span data-stu-id="815d6-225">The `TestController`:</span></span>

* <span data-ttu-id="815d6-226">Wendet das `SampleActionFilterAttribute` (`[SampleActionFilter]`) auf die Aktion `FilterTest2` an.</span><span class="sxs-lookup"><span data-stu-id="815d6-226">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="815d6-227">Überschreibt `OnActionExecuting` und `OnActionExecuted`:</span><span class="sxs-lookup"><span data-stu-id="815d6-227">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="815d6-228">Durch Navigieren zu `https://localhost:5001/Test2/FilterTest2` wird der folgende Code ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="815d6-228">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="815d6-229">Filter auf Controllerebene legen die Eigenschaft [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) auf `int.MinValue` fest.</span><span class="sxs-lookup"><span data-stu-id="815d6-229">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="815d6-230">Für Filter auf Controllerebene kann die Ausführung nach Filtern, die auf Methoden angewendet werden **nicht** festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-230">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="815d6-231">Die Reihenfolge wird im nächsten Abschnitt erläutert.</span><span class="sxs-lookup"><span data-stu-id="815d6-231">Order is explained in the next section.</span></span>

<span data-ttu-id="815d6-232">Informationen zu Razor Pages finden Sie unter [Implementieren von Filtern für Razor-Seiten durch Überschreiben von Filtermethoden](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="815d6-232">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="815d6-233">Überschreiben der Standardreihenfolge</span><span class="sxs-lookup"><span data-stu-id="815d6-233">Overriding the default order</span></span>

<span data-ttu-id="815d6-234">Die Standardreihenfolge der Ausführung kann durch Implementieren von <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-234">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="815d6-235">`IOrderedFilter` macht die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> verfügbar, die bei der Bestimmung der Ausführungsreihenfolge Vorrang vor dem Bereich hat.</span><span class="sxs-lookup"><span data-stu-id="815d6-235">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="815d6-236">Für einen Filter mit geringerem `Order`-Wert gilt:</span><span class="sxs-lookup"><span data-stu-id="815d6-236">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="815d6-237">Führt den Code, der *vor* einer Pipelinephase ausgeführt werden soll, vor dem Code eines Filters mit einem höheren Wert für `Order` aus.</span><span class="sxs-lookup"><span data-stu-id="815d6-237">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="815d6-238">Führt den Code, der *nach* einer Pipelinephase ausgeführt werden soll, nach dem Code eines Filters mit einem höheren Wert für `Order` aus.</span><span class="sxs-lookup"><span data-stu-id="815d6-238">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="815d6-239">Die Eigenschaft `Order` wird mit einem Konstruktorparameter festgelegt:</span><span class="sxs-lookup"><span data-stu-id="815d6-239">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="815d6-240">Sehen Sie sich die zwei Aktionsfilter im folgenden Controller an:</span><span class="sxs-lookup"><span data-stu-id="815d6-240">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="815d6-241">In `StartUp.ConfigureServices` wird ein globaler Filter hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="815d6-241">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="815d6-242">Die 3 Filter werden in der folgenden Reihenfolge ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="815d6-242">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="815d6-243">Die Eigenschaft `Order` hat bei der Bestimmung der Reihenfolge, in der Filter ausgeführt werden, Vorrang vor dem Bereich.</span><span class="sxs-lookup"><span data-stu-id="815d6-243">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="815d6-244">Filter werden erst nach der Reihenfolge sortiert, und dann werden Gleichstände über den Bereich aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="815d6-244">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="815d6-245">Alle integrierten Filter implementieren `IOrderedFilter` und legen den Standartwert von `Order` auf 0 fest.</span><span class="sxs-lookup"><span data-stu-id="815d6-245">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="815d6-246">Wie zuvor erwähnt, legen Filter auf Controllerebene die [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17)-Eigenschaft auf `int.MinValue` fest. Bei integrierten Filtern bestimmt der Umfang die Reihenfolge, es sei denn, `Order` ist auf einen Wert ungleich null festgelegt.</span><span class="sxs-lookup"><span data-stu-id="815d6-246">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="815d6-247">Im vorstehenden Code hat `MySampleActionFilter` globalen Umfang und wird daher vor `MyAction2FilterAttribute` ausgeführt, der Controllerumfang aufweist.</span><span class="sxs-lookup"><span data-stu-id="815d6-247">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="815d6-248">Um `MyAction2FilterAttribute` zuerst ausführen zu lassen, legen Sie die Reihenfolge auf `int.MinValue` fest:</span><span class="sxs-lookup"><span data-stu-id="815d6-248">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="815d6-249">Um zuerst den globalen Filter `MySampleActionFilter` ausführen zu lassen, legen Sie `Order` auf `int.MinValue` fest:</span><span class="sxs-lookup"><span data-stu-id="815d6-249">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="815d6-250">Abbrechen und Kurzschließen</span><span class="sxs-lookup"><span data-stu-id="815d6-250">Cancellation and short-circuiting</span></span>

<span data-ttu-id="815d6-251">Die Filterpipeline kann kurzgeschlossen werden, indem die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> auf den Parameter <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> festgelegt wird, der in der Filtermethode angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="815d6-251">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="815d6-252">Zum Beispiel verhindert der folgende Ressourcenfilter, dass der Rest der Pipeline ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="815d6-252">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="815d6-253">Im folgenden Code verwenden sowohl der Filter `ShortCircuitingResourceFilter` und der Filter `AddHeader` die Aktionsmethode `SomeResource` als Ziel.</span><span class="sxs-lookup"><span data-stu-id="815d6-253">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="815d6-254">Die `ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="815d6-254">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="815d6-255">Der Filter wird zuerst ausgeführt, da es sich um einen Ressourcenfilter handelt und `AddHeader` ein Aktionsfilter ist.</span><span class="sxs-lookup"><span data-stu-id="815d6-255">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="815d6-256">Der Filter unterbricht alle verbleibenden Pipelineschritte.</span><span class="sxs-lookup"><span data-stu-id="815d6-256">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="815d6-257">Der `AddHeader`-Filter wird daher nie für die `SomeResource`-Aktion ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="815d6-257">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="815d6-258">Dasselbe Verhalten würde auch dann auftreten, wenn beide Filter auf Aktionsmethodenebene angewendet würden, wenn `ShortCircuitingResourceFilter` zuerst ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="815d6-258">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="815d6-259">Der `ShortCircuitingResourceFilter` wird aufgrund seines Filtertyps oder durch die explizite Verwendung der `Order`-Eigenschaft zuerst ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="815d6-259">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="815d6-260">Dependency Injection</span><span class="sxs-lookup"><span data-stu-id="815d6-260">Dependency injection</span></span>

<span data-ttu-id="815d6-261">Filter können nach Typ oder Instanz hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-261">Filters can be added by type or by instance.</span></span> <span data-ttu-id="815d6-262">Wenn eine Instanz hinzugefügt wird, wird diese Instanz für jede Anforderung verwendet.</span><span class="sxs-lookup"><span data-stu-id="815d6-262">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="815d6-263">Wenn ein Typ hinzugefügt wird, ist der Filter typaktiviert.</span><span class="sxs-lookup"><span data-stu-id="815d6-263">If a type is added, it's type-activated.</span></span> <span data-ttu-id="815d6-264">Ein typaktivierter Filter bedeutet Folgendes:</span><span class="sxs-lookup"><span data-stu-id="815d6-264">A type-activated filter means:</span></span>

* <span data-ttu-id="815d6-265">Für jede Anforderung wird eine Instanz erstellt.</span><span class="sxs-lookup"><span data-stu-id="815d6-265">An instance is created for each request.</span></span>
* <span data-ttu-id="815d6-266">Alle Konstruktorabhängigkeiten werden durch [Dependency Injection](xref:fundamentals/dependency-injection) (DI) aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="815d6-266">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="815d6-267">Filter, die als Attribute implementiert und Controllerklassen oder Aktionsmethoden direkt hinzugefügt werden, können keine Konstruktorabhängigkeiten von [Dependency Injection](xref:fundamentals/dependency-injection) (DI) erhalten.</span><span class="sxs-lookup"><span data-stu-id="815d6-267">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="815d6-268">Konstruktorabhängigkeiten können aus folgenden Gründen von DI nicht bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="815d6-268">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="815d6-269">Für Attribute müssen Konstruktorparameter bereitgestellt werden, wenn sie angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-269">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="815d6-270">Dies ist eine Einschränkung der Funktionsweise von Attributen.</span><span class="sxs-lookup"><span data-stu-id="815d6-270">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="815d6-271">Die folgenden Filter unterstützen von DI bereitgestellte Konstruktorabhängigkeiten:</span><span class="sxs-lookup"><span data-stu-id="815d6-271">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="815d6-272"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>, im Attribut implementiert</span><span class="sxs-lookup"><span data-stu-id="815d6-272"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="815d6-273">Die oben genannten Filter können auf einen Controller oder eine Aktionsmethode angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="815d6-273">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="815d6-274">DI bietet Protokollierungen.</span><span class="sxs-lookup"><span data-stu-id="815d6-274">Loggers are available from DI.</span></span> <span data-ttu-id="815d6-275">Vermeiden Sie es jedoch, Filter nur zum Zweck der Protokollierung zu erstellen und zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="815d6-275">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="815d6-276">Die integrierte [Frameworkprotokollierung](xref:fundamentals/logging/index) bietet in der Regel alle Elemente, die für die Protokollierung erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="815d6-276">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="815d6-277">Wenn Protokollierung zu Filtern hinzugefügt wird, gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="815d6-277">Logging added to filters:</span></span>

* <span data-ttu-id="815d6-278">Die Protokollierung sollte mit Schwerpunkt auf geschäftlichen Aspekten oder verhaltensspezifisch für den Filter erfolgen.</span><span class="sxs-lookup"><span data-stu-id="815d6-278">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="815d6-279">Aktionen oder andere Frameworkereignisse sollten **nicht** protokolliert werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-279">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="815d6-280">Die integrierten Filter protokollieren Aktionen und Frameworkereignisse.</span><span class="sxs-lookup"><span data-stu-id="815d6-280">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="815d6-281">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="815d6-281">ServiceFilterAttribute</span></span>

<span data-ttu-id="815d6-282">Die Typen der Dienstfilterimplementierung werden in `ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="815d6-282">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="815d6-283">Ein <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> ruft eine Instanz des Filter von DI ab.</span><span class="sxs-lookup"><span data-stu-id="815d6-283">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="815d6-284">Der folgende Code zeigt den `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="815d6-284">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="815d6-285">Im folgenden Code wird `AddHeaderResultServiceFilter` zum DI-Container hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="815d6-285">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="815d6-286">Im folgenden Code ruft das `ServiceFilter`-Attribut eine Instanz des `AddHeaderResultServiceFilter`-Filters aus DI ab:</span><span class="sxs-lookup"><span data-stu-id="815d6-286">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="815d6-287">Beim Verwenden von `ServiceFilterAttribute` wird [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) festgelegt:</span><span class="sxs-lookup"><span data-stu-id="815d6-287">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="815d6-288">Gibt einen Hinweis darauf, dass die Filterinstanz *möglicherweise* außerhalb des Anforderungsbereichs, in dem sie erstellt wurde, wiederverwendet wird.</span><span class="sxs-lookup"><span data-stu-id="815d6-288">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="815d6-289">Die ASP.NET Core-Runtime bietet keine Garantie für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="815d6-289">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="815d6-290">Eine einzelne Instanz des Filters wird erstellt.</span><span class="sxs-lookup"><span data-stu-id="815d6-290">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="815d6-291">Der Filter wird nicht zu einem späteren Zeitpunkt vom DI-Container erneut angefordert.</span><span class="sxs-lookup"><span data-stu-id="815d6-291">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="815d6-292">Sollte nicht mit einem Filter verwendet werden, der von Diensten mit einer anderen Lebensdauer als der eines Singletons abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="815d6-292">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="815d6-293"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementiert <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="815d6-293"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="815d6-294">`IFilterFactory` macht die <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*>-Methode zum Erstellen einer <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>-Instanz verfügbar.</span><span class="sxs-lookup"><span data-stu-id="815d6-294">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="815d6-295">`CreateInstance` lädt den angegebenen Typ aus DI.</span><span class="sxs-lookup"><span data-stu-id="815d6-295">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="815d6-296">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="815d6-296">TypeFilterAttribute</span></span>

<span data-ttu-id="815d6-297"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> ähnelt <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>. Der zugehörige Typ wird allerdings nicht direkt vom DI-Container aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="815d6-297"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="815d6-298">Stattdessen wird der Typ mithilfe von <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> instanziiert.</span><span class="sxs-lookup"><span data-stu-id="815d6-298">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="815d6-299">Da `TypeFilterAttribute`-Typen nicht direkt vom DI-Container aufgelöst werden, gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="815d6-299">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="815d6-300">Typen, auf die mithilfe von `TypeFilterAttribute` verwiesen wird, müssen nicht beim DI-Container registriert werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-300">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="815d6-301">Ihre Abhängigkeiten werden vom DI-Container erfüllt.</span><span class="sxs-lookup"><span data-stu-id="815d6-301">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="815d6-302">`TypeFilterAttribute` kann optional Konstruktorargumente für den Typ akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="815d6-302">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="815d6-303">Beim Verwenden von `TypeFilterAttribute` wird [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) festgelegt:</span><span class="sxs-lookup"><span data-stu-id="815d6-303">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="815d6-304">Gibt einen Hinweis darauf, dass die Filterinstanz *möglicherweise* außerhalb des Anforderungsbereichs wiederverwendet wird, in dem sie erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="815d6-304">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="815d6-305">Die ASP.NET Core-Runtime bietet keine Garantie dafür, dass eine einzelne Instanz des Filters erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="815d6-305">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="815d6-306">Sollte nicht mit einem Filter verwendet werden, der von Diensten mit einer anderen Lebensdauer als der eines Singletons abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="815d6-306">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="815d6-307">Das folgende Beispiel zeigt, wie Sie Argumente durch Verwendung von `TypeFilterAttribute` an einen Typ übergeben:</span><span class="sxs-lookup"><span data-stu-id="815d6-307">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="815d6-308">Autorisierungsfilter</span><span class="sxs-lookup"><span data-stu-id="815d6-308">Authorization filters</span></span>

<span data-ttu-id="815d6-309">Für Autorisierungsfilter gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="815d6-309">Authorization filters:</span></span>

* <span data-ttu-id="815d6-310">Sie werden als erste Filter in der Filterpipeline ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="815d6-310">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="815d6-311">Sie steuern den Zugriff auf Aktionsmethoden.</span><span class="sxs-lookup"><span data-stu-id="815d6-311">Control access to action methods.</span></span>
* <span data-ttu-id="815d6-312">Sie verfügen nur über eine Methode, die vor der Aktion ausgeführt wird, nicht jedoch über eine Methode, die nach der Aktion ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="815d6-312">Have a before method, but no after method.</span></span>

<span data-ttu-id="815d6-313">Benutzerdefinierte Autorisierungsfilter erfordern ein benutzerdefiniertes Autorisierungsframework.</span><span class="sxs-lookup"><span data-stu-id="815d6-313">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="815d6-314">Statt einen benutzerdefinierten Filter zu schreiben, sollten Sie die Autorisierungsrichtlinien konfigurieren oder eine benutzerdefinierte Autorisierungsrichtlinie schreiben.</span><span class="sxs-lookup"><span data-stu-id="815d6-314">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="815d6-315">Für den integrierten Autorisierungsfilter gilt:</span><span class="sxs-lookup"><span data-stu-id="815d6-315">The built-in authorization filter:</span></span>

* <span data-ttu-id="815d6-316">Er ruft das Autorisierungssystem auf.</span><span class="sxs-lookup"><span data-stu-id="815d6-316">Calls the authorization system.</span></span>
* <span data-ttu-id="815d6-317">Er autorisiert keine Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="815d6-317">Does not authorize requests.</span></span>

<span data-ttu-id="815d6-318">Lösen Sie **keine** Ausnahmen in Autorisierungsfiltern aus:</span><span class="sxs-lookup"><span data-stu-id="815d6-318">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="815d6-319">Die Ausnahme wird nicht behandelt.</span><span class="sxs-lookup"><span data-stu-id="815d6-319">The exception will not be handled.</span></span>
* <span data-ttu-id="815d6-320">Ausnahmefilter behandeln die Ausnahme nicht.</span><span class="sxs-lookup"><span data-stu-id="815d6-320">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="815d6-321">Beim Auftreten einer Ausnahme in einem Autorisierungsfilter sollten Sie eine Abfrage erwägen.</span><span class="sxs-lookup"><span data-stu-id="815d6-321">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="815d6-322">Weitere Informationen finden Sie unter [Autorisierung](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="815d6-322">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="815d6-323">Ressourcenfilter</span><span class="sxs-lookup"><span data-stu-id="815d6-323">Resource filters</span></span>

<span data-ttu-id="815d6-324">Für Ressourcenfilter gilt:</span><span class="sxs-lookup"><span data-stu-id="815d6-324">Resource filters:</span></span>

* <span data-ttu-id="815d6-325">Sie implementieren entweder die Schnittstelle <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="815d6-325">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="815d6-326">Die Ausführung umschließt den größten Teil der Filterpipeline.</span><span class="sxs-lookup"><span data-stu-id="815d6-326">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="815d6-327">Nur [Autorisierungsfilter](#authorization-filters) werden vor Ressourcenfiltern ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="815d6-327">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="815d6-328">Ressourcenfilter sind hilfreich, um den größten Teil der Pipeline kurzzuschließen.</span><span class="sxs-lookup"><span data-stu-id="815d6-328">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="815d6-329">Ein Cachingfilter kann beispielsweise bei einem Cachetreffer den Rest der Pipeline überspringen.</span><span class="sxs-lookup"><span data-stu-id="815d6-329">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="815d6-330">Beispiele für Ressourcenfilter:</span><span class="sxs-lookup"><span data-stu-id="815d6-330">Resource filter examples:</span></span>

* <span data-ttu-id="815d6-331">Der oben gezeigte [Ressourcenfilter zum Kurzschließen](#short-circuiting-resource-filter).</span><span class="sxs-lookup"><span data-stu-id="815d6-331">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="815d6-332">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="815d6-332">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="815d6-333">Verhindert, dass die Modellbindung auf die Formulardaten zugreift.</span><span class="sxs-lookup"><span data-stu-id="815d6-333">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="815d6-334">Wird bei großen Dateiuploads verwendet, um zu verhindern, dass die Formulardaten in den Arbeitsspeicher eingelesen werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-334">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="815d6-335">Aktionsfilter</span><span class="sxs-lookup"><span data-stu-id="815d6-335">Action filters</span></span>

<span data-ttu-id="815d6-336">Aktionsfilter gelten **nicht** für Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="815d6-336">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="815d6-337">Razor Pages unterstützen <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> und <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter>.</span><span class="sxs-lookup"><span data-stu-id="815d6-337">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="815d6-338">Weitere Informationen finden Sie unter [Filtermethoden für Razor-Seiten](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="815d6-338">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="815d6-339">Für Aktionsfilter gilt:</span><span class="sxs-lookup"><span data-stu-id="815d6-339">Action filters:</span></span>

* <span data-ttu-id="815d6-340">Sie implementieren entweder die Schnittstelle <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="815d6-340">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="815d6-341">Ihre Ausführung umfasst die Ausführung von Aktionsmethoden.</span><span class="sxs-lookup"><span data-stu-id="815d6-341">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="815d6-342">Der folgende Code zeigt einen Beispielaktionsfilter:</span><span class="sxs-lookup"><span data-stu-id="815d6-342">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="815d6-343"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> bietet folgende Eigenschaften:</span><span class="sxs-lookup"><span data-stu-id="815d6-343">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="815d6-344"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> ermöglicht das Lesen der Eingaben für eine Aktionsmethode.</span><span class="sxs-lookup"><span data-stu-id="815d6-344"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="815d6-345"><xref:Microsoft.AspNetCore.Mvc.Controller> ermöglicht das Ändern der Controllerinstanz.</span><span class="sxs-lookup"><span data-stu-id="815d6-345"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="815d6-346"><xref:System.Web.Mvc.ActionExecutingContext.Result>: Die Festlegung von `Result` schließt die Ausführung der Aktionsmethode und der nachfolgenden Aktionsfilter kurz.</span><span class="sxs-lookup"><span data-stu-id="815d6-346"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="815d6-347">Durch Auslösen einer Ausnahme in einer Aktionsmethode geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="815d6-347">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="815d6-348">Die Ausführung nachfolgender Filter wird verhindert.</span><span class="sxs-lookup"><span data-stu-id="815d6-348">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="815d6-349">Im Gegensatz zum Festlegen von `Result` wird die ausgelöste Ausnahme wird nicht als erfolgreiches Ergebnis, sondern als Fehler behandelt.</span><span class="sxs-lookup"><span data-stu-id="815d6-349">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="815d6-350"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> bietet `Controller` und `Result` sowie die folgenden Eigenschaften:</span><span class="sxs-lookup"><span data-stu-id="815d6-350">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="815d6-351"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> ist TRUE, wenn die Aktionsausführung durch einen anderen Filter kurzgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="815d6-351"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="815d6-352"><xref:System.Web.Mvc.ActionExecutedContext.Exception> ist ungleich NULL, wenn die Aktion oder ein zuvor ausgeführter Aktionsfilter eine Ausnahme ausgelöst hat.</span><span class="sxs-lookup"><span data-stu-id="815d6-352"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="815d6-353">Durch Festlegen dieser Eigenschaft auf NULL geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="815d6-353">Setting this property to null:</span></span>

  * <span data-ttu-id="815d6-354">Die Ausnahme wird effektiv behandelt.</span><span class="sxs-lookup"><span data-stu-id="815d6-354">Effectively handles the exception.</span></span>
  * <span data-ttu-id="815d6-355">`Result` wird so ausgeführt, als wäre das Ergebnis von der Aktionsmethode zurückgegeben worden.</span><span class="sxs-lookup"><span data-stu-id="815d6-355">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="815d6-356">Bei einem `IAsyncActionFilter` hat der Aufruf von <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> folgende Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="815d6-356">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="815d6-357">Alle nachfolgenden Aktionsfilter und die Aktionsmethode werden ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="815d6-357">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="815d6-358">Gibt `ActionExecutedContext`zurück.</span><span class="sxs-lookup"><span data-stu-id="815d6-358">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="815d6-359">Weisen Sie einer Ergebnisinstanz <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> zu, und rufen Sie nicht `next` (den `ActionExecutionDelegate`) auf, um die Pipeline kurzzuschließen.</span><span class="sxs-lookup"><span data-stu-id="815d6-359">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="815d6-360">Das Framework stellt ein abstraktes <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> bereit, das als Unterklasse verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="815d6-360">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="815d6-361">Der Aktionsfilter `OnActionExecuting` kann für Folgendes verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="815d6-361">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="815d6-362">Überprüfen des Modellzustands.</span><span class="sxs-lookup"><span data-stu-id="815d6-362">Validate model state.</span></span>
* <span data-ttu-id="815d6-363">Zurückgeben eines Fehlers, wenn der Zustand ungültig ist.</span><span class="sxs-lookup"><span data-stu-id="815d6-363">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="815d6-364">Die Methode `OnActionExecuted` wird nach der Aktionsmethode ausgeführt. Es gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="815d6-364">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="815d6-365">Die Methode kann die Ergebnisse der Aktion durch die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> sehen und ändern.</span><span class="sxs-lookup"><span data-stu-id="815d6-365">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="815d6-366"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> wird auf TRUE festgelegt, wenn die Ausführung der Aktion durch einen anderen Filter kurzgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="815d6-366"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="815d6-367"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> wird auf einen Wert ungleich NULL festgelegt, wenn die Aktion oder ein nachfolgender Aktionsfilter eine Ausnahme ausgelöst hat.</span><span class="sxs-lookup"><span data-stu-id="815d6-367"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="815d6-368">Wenn für `Exception` NULL festgelegt wird,</span><span class="sxs-lookup"><span data-stu-id="815d6-368">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="815d6-369">werden Ausnahmen effektiv behandelt,</span><span class="sxs-lookup"><span data-stu-id="815d6-369">Effectively handles an exception.</span></span>
  * <span data-ttu-id="815d6-370">und `ActionExecutedContext.Result` wird so ausgeführt, als würde die Eigenschaft normal von der Aktionsmethode zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-370">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="815d6-371">Ausnahmefilter</span><span class="sxs-lookup"><span data-stu-id="815d6-371">Exception filters</span></span>

<span data-ttu-id="815d6-372">Für Ausnahmefilter gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="815d6-372">Exception filters:</span></span>

* <span data-ttu-id="815d6-373">Sie implementieren <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="815d6-373">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="815d6-374">Sie können verwendet werden, um gängige Fehlerbehandlungsrichtlinien zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="815d6-374">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="815d6-375">Der folgende Beispielausnahmefilter verwendet eine benutzerdefinierte Fehleransicht, um Details zu Ausnahmen anzuzeigen, die auftreten, wenn die App sich in der Entwicklung befindet:</span><span class="sxs-lookup"><span data-stu-id="815d6-375">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="815d6-376">Mit dem folgenden Code wird der Ausnahmefilter getestet:</span><span class="sxs-lookup"><span data-stu-id="815d6-376">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="815d6-377">Für Ausnahmefilter gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="815d6-377">Exception filters:</span></span>

* <span data-ttu-id="815d6-378">Sie verfügen nicht über vorangehende oder darauffolgende Ereignisse.</span><span class="sxs-lookup"><span data-stu-id="815d6-378">Don't have before and after events.</span></span>
* <span data-ttu-id="815d6-379">Sie implementieren <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="815d6-379">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="815d6-380">Sie behandeln Ausnahmefehler, die bei der Erstellung von Razor Pages oder Controllern, bei der [Modellbindung](xref:mvc/models/model-binding), bei Aktionsfiltern oder bei Aktionsmethoden auftreten.</span><span class="sxs-lookup"><span data-stu-id="815d6-380">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="815d6-381">Sie erfassen **keine** Ausnahmen, die in Ressourcenfiltern, Ergebnisfiltern oder bei der Ausführung von MVC-Ergebnissen auftreten.</span><span class="sxs-lookup"><span data-stu-id="815d6-381">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="815d6-382">Um eine Ausnahme zu behandeln, muss die Eigenschaft <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> auf `true` festgelegt oder eine Antwort geschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-382">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="815d6-383">Dadurch wird die Weitergabe der Ausnahme beendet.</span><span class="sxs-lookup"><span data-stu-id="815d6-383">This stops propagation of the exception.</span></span> <span data-ttu-id="815d6-384">Ein Ausnahmefilter kann eine Ausnahme nicht in ein „erfolgreiches Ergebnis“ umwandeln.</span><span class="sxs-lookup"><span data-stu-id="815d6-384">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="815d6-385">Nur ein Aktionsfilter kann das.</span><span class="sxs-lookup"><span data-stu-id="815d6-385">Only an action filter can do that.</span></span>

<span data-ttu-id="815d6-386">Für Ausnahmefilter gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="815d6-386">Exception filters:</span></span>

* <span data-ttu-id="815d6-387">Sie eignen sich für das Abfangen von Ausnahmen, die in Aktionen auftreten.</span><span class="sxs-lookup"><span data-stu-id="815d6-387">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="815d6-388">Sie sind im Vergleich zu Middleware für die Fehlerbehandlung weniger flexibel.</span><span class="sxs-lookup"><span data-stu-id="815d6-388">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="815d6-389">Sie sollten bei der Ausnahmebehandlung vorzugsweise Middleware verwenden.</span><span class="sxs-lookup"><span data-stu-id="815d6-389">Prefer middleware for exception handling.</span></span> <span data-ttu-id="815d6-390">Verwenden Sie Ausnahmefilter nur, wenn sich die Fehlerbehandlung *unterscheidet*, je nachdem, welche Aktionsmethode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="815d6-390">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="815d6-391">Zum Beispiel verfügt eine App möglicherweise über Aktionsmethoden für beide API-Endpunkte und für Ansichten bzw. HTML.</span><span class="sxs-lookup"><span data-stu-id="815d6-391">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="815d6-392">Die API-Endpunkte können Fehlerinformationen als JSON zurückgeben, während ansichtsbasierte Aktionen eine Fehlerseite als HTML zurückgeben können.</span><span class="sxs-lookup"><span data-stu-id="815d6-392">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="815d6-393">Ergebnisfilter</span><span class="sxs-lookup"><span data-stu-id="815d6-393">Result filters</span></span>

<span data-ttu-id="815d6-394">Für Ergebnisfilter gilt:</span><span class="sxs-lookup"><span data-stu-id="815d6-394">Result filters:</span></span>

* <span data-ttu-id="815d6-395">Implementieren einer Schnittstelle:</span><span class="sxs-lookup"><span data-stu-id="815d6-395">Implement an interface:</span></span>
  * <span data-ttu-id="815d6-396"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="815d6-396"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="815d6-397"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="815d6-397"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="815d6-398">Ihre Ausführung umfasst die Ausführung von Aktionsergebnissen.</span><span class="sxs-lookup"><span data-stu-id="815d6-398">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="815d6-399">IResultFilter und IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="815d6-399">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="815d6-400">Der folgende Code zeigt einen Ergebnisfilter, der einen HTTP-Header hinzufügt:</span><span class="sxs-lookup"><span data-stu-id="815d6-400">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="815d6-401">Die Art des Ergebnisses, das ausgeführt wird, hängt von der jeweiligen Aktion ab.</span><span class="sxs-lookup"><span data-stu-id="815d6-401">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="815d6-402">Eine Aktion, die eine Ansicht zurückgibt, beinhaltet die gesamte Razor-Verarbeitung als Teil der Ausführung von <xref:Microsoft.AspNetCore.Mvc.ViewResult>.</span><span class="sxs-lookup"><span data-stu-id="815d6-402">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="815d6-403">Eine API-Methode führt möglicherweise eine Art Serialisierung als Teil der Ausführung des Ergebnisses durch.</span><span class="sxs-lookup"><span data-stu-id="815d6-403">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="815d6-404">Erfahren Sie mehr zu [Aktionsergebnissen](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="815d6-404">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="815d6-405">Ergebnisfilter werden nur ausgeführt, wenn eine Aktion oder ein Aktionsfilter ein Aktionsergebnis liefert.</span><span class="sxs-lookup"><span data-stu-id="815d6-405">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="815d6-406">Ergebnisfilter werden in folgenden Fällen nicht ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="815d6-406">Result filters are not executed when:</span></span>

* <span data-ttu-id="815d6-407">Ein Autorisierungs- oder Ressourcenfilter schließt die Pipeline kurz.</span><span class="sxs-lookup"><span data-stu-id="815d6-407">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="815d6-408">Ein Ausnahmefilter behandelt eine Ausnahme durch Erzeugen eines Aktionsergebnisses.</span><span class="sxs-lookup"><span data-stu-id="815d6-408">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="815d6-409">Die Methode <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> kann die Ausführung des Aktionsergebnisses und der nachfolgenden Ergebnisfilter durch Festlegen von <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> auf `true` kurzschließen.</span><span class="sxs-lookup"><span data-stu-id="815d6-409">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="815d6-410">Schreiben Sie beim Kurzschließen in das Antwortobjekt, um zu verhindern, dass eine leere Antwort generiert wird.</span><span class="sxs-lookup"><span data-stu-id="815d6-410">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="815d6-411">Das Auslösen einer Ausnahme in `IResultFilter.OnResultExecuting` bewirkt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="815d6-411">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="815d6-412">Die Ausführung des Aktionsergebnisses und der nachfolgenden Filter wird verhindert.</span><span class="sxs-lookup"><span data-stu-id="815d6-412">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="815d6-413">Die ausgelöste Ausnahme wird nicht als erfolgreiches Ergebnis, sondern als Fehler behandelt.</span><span class="sxs-lookup"><span data-stu-id="815d6-413">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="815d6-414">Wenn die <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>-Methode ausgeführt wird, wurde die Antwort wahrscheinlich bereits an den Client gesendet.</span><span class="sxs-lookup"><span data-stu-id="815d6-414">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="815d6-415">Wenn die Antwort bereits an den Client gesendet wurde, kann sie nicht geändert werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-415">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="815d6-416">`ResultExecutedContext.Canceled` wird auf `true` festgelegt, wenn die Ausführung des Aktionsergebnisses durch einen anderen Filter kurzgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="815d6-416">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="815d6-417">`ResultExecutedContext.Exception` wird auf einen Wert ungleich NULL festgelegt, wenn das Aktionsergebnis oder ein nachfolgender Ergebnisfilter eine Ausnahme ausgelöst hat.</span><span class="sxs-lookup"><span data-stu-id="815d6-417">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="815d6-418">Das Festlegen von `Exception` auf NULL behandelt eine Ausnahme und verhindert, dass die Ausnahme zu einem späteren Zeitpunkt in der Pipeline ein weiteres Mal ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="815d6-418">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="815d6-419">Beim Behandeln einer Ausnahme in einem Ergebnisfilter gibt es keine zuverlässige Möglichkeit, Daten in eine Antwort zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="815d6-419">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="815d6-420">Wenn ein Aktionsergebnis eine Ausnahme auslöst und die Header bereits an den Client übergeben wurden, gibt es keinen zuverlässigen Mechanismus, einen Fehlercode zu senden.</span><span class="sxs-lookup"><span data-stu-id="815d6-420">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="815d6-421">Bei einem <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> führt ein Aufruf von `await next` im <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> alle nachfolgenden Ergebnisfilter und das Aktionsergebnis aus.</span><span class="sxs-lookup"><span data-stu-id="815d6-421">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="815d6-422">Zum Kurzschließen legen Sie [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) auf `true` fest und rufen den `ResultExecutionDelegate` nicht auf:</span><span class="sxs-lookup"><span data-stu-id="815d6-422">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="815d6-423">Das Framework stellt ein abstraktes `ResultFilterAttribute` bereit, das als Unterklasse verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="815d6-423">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="815d6-424">Die weiter oben gezeigte Klasse [AddHeaderAttribute](#add-header-attribute) ist ein Beispiel für ein Ergebnisfilterattribut.</span><span class="sxs-lookup"><span data-stu-id="815d6-424">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="815d6-425">IAlwaysRunResultFilter und IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="815d6-425">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="815d6-426">Die Schnittstellen <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> und <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklarieren eine <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter>-Implementierung, die für alle Aktionsergebnisse ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="815d6-426">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="815d6-427">Dies schließt Aktionsergebnisse ein, die von folgenden Filtern generiert werden:</span><span class="sxs-lookup"><span data-stu-id="815d6-427">This includes action results produced by:</span></span>

* <span data-ttu-id="815d6-428">Autorisierungs- und Ressourcenfilter, die einen Kurzschluss bewirken</span><span class="sxs-lookup"><span data-stu-id="815d6-428">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="815d6-429">Ausnahmefilter</span><span class="sxs-lookup"><span data-stu-id="815d6-429">Exception filters.</span></span>

<span data-ttu-id="815d6-430">Beispielsweise wird der folgende Filter immer ausgeführt und legt ein Aktionsergebnis (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) mit dem Statuscode *422 – Einheit kann nicht bearbeitet werden* fest, wenn bei der Inhaltsaushandlung ein Fehler auftritt:</span><span class="sxs-lookup"><span data-stu-id="815d6-430">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="815d6-431">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="815d6-431">IFilterFactory</span></span>

<span data-ttu-id="815d6-432"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementiert <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="815d6-432"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="815d6-433">Deshalb kann eine `IFilterFactory`-Instanz an einer beliebigen Stelle in der Filterpipeline als `IFilterMetadata`-Instanz verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-433">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="815d6-434">Wenn die Runtime den Aufruf des Filters vorbereitet, versucht sie, ihn in eine `IFilterFactory` umzuwandeln.</span><span class="sxs-lookup"><span data-stu-id="815d6-434">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="815d6-435">Wenn diese Umwandlung gelingt, wird die Methode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> aufgerufen, um die Instanz `IFilterMetadata` für den Aufruf zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="815d6-435">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="815d6-436">Da die exakte Filterpipeline beim Start der Anwendung nicht explizit festgelegt werden muss, wird dadurch ein sehr flexibles Design ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="815d6-436">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="815d6-437">Als weiteres Verfahren zum Erstellen von Filtern kann `IFilterFactory` mithilfe von benutzerdefinierten Attributimplementierungen implementiert werden:</span><span class="sxs-lookup"><span data-stu-id="815d6-437">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="815d6-438">Der Filter wird im folgenden Code angewendet:</span><span class="sxs-lookup"><span data-stu-id="815d6-438">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="815d6-439">Testen Sie den vorangehenden Code durch Ausführen des [Downloadbeispiels](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="815d6-439">Test the preceding code by running the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="815d6-440">Rufen Sie die F12-Entwicklungstools auf.</span><span class="sxs-lookup"><span data-stu-id="815d6-440">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="815d6-441">Navigieren Sie zu `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="815d6-441">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="815d6-442">Die F12-Entwicklungstools zeigen folgende Antwortheader an, die vom Beispielcode hinzugefügt wurden:</span><span class="sxs-lookup"><span data-stu-id="815d6-442">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="815d6-443">**author:** `Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="815d6-443">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="815d6-444">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="815d6-444">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="815d6-445">**internal:** `My header`</span><span class="sxs-lookup"><span data-stu-id="815d6-445">**internal:** `My header`</span></span>

<span data-ttu-id="815d6-446">Der oben stehende Code erstellt den Antwortheader „**internal:** `My header`“.</span><span class="sxs-lookup"><span data-stu-id="815d6-446">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="815d6-447">In einem Attribut implementierte IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="815d6-447">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="815d6-448">Filter, die `IFilterFactory` implementieren, eignen sich in folgenden Fällen:</span><span class="sxs-lookup"><span data-stu-id="815d6-448">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="815d6-449">Sie erfordern kein Übergeben von Parametern.</span><span class="sxs-lookup"><span data-stu-id="815d6-449">Don't require passing parameters.</span></span>
* <span data-ttu-id="815d6-450">Sie verfügen über Konstruktorabhängigkeiten, die durch DI erfüllt werden müssen.</span><span class="sxs-lookup"><span data-stu-id="815d6-450">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="815d6-451"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementiert <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="815d6-451"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="815d6-452">`IFilterFactory` macht die <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*>-Methode zum Erstellen einer <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>-Instanz verfügbar.</span><span class="sxs-lookup"><span data-stu-id="815d6-452">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="815d6-453">`CreateInstance` lädt den angegebenen Typ aus dem Dienstecontainer (DI).</span><span class="sxs-lookup"><span data-stu-id="815d6-453">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="815d6-454">Der folgende Code zeigt drei Ansätze zum Anwenden von `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="815d6-454">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="815d6-455">Im oben stehenden Code ist das Ergänzen der Methode um `[SampleActionFilter]` der bevorzugte Ansatz zum Anwenden von `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="815d6-455">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="815d6-456">Verwenden von Middleware in der Filterpipeline</span><span class="sxs-lookup"><span data-stu-id="815d6-456">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="815d6-457">Ressourcenfilter funktionieren wie [Middleware](xref:fundamentals/middleware/index), sie umschließen alle Ausführungen, die später in der Pipeline enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="815d6-457">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="815d6-458">Filter unterscheiden sich jedoch insofern von Middleware, dass sie Teil der Runtime sind, was bedeutet, dass sie Zugriff auf Kontext und Konstrukte haben.</span><span class="sxs-lookup"><span data-stu-id="815d6-458">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="815d6-459">Um Middleware als Filter zu verwenden, erstellen Sie einen Typ mit einer `Configure`-Methode, die die Middleware festlegt, die in die Filterpipeline eingefügt werden soll.</span><span class="sxs-lookup"><span data-stu-id="815d6-459">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="815d6-460">Das folgende Beispiel verwendet die Lokalisierungsmiddleware, um die aktuelle Kultur einer Anforderung einzurichten:</span><span class="sxs-lookup"><span data-stu-id="815d6-460">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="815d6-461">Verwenden Sie das <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> zum Ausführen der Middleware:</span><span class="sxs-lookup"><span data-stu-id="815d6-461">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="815d6-462">Middlewarefilter werden zum selben Zeitpunkt in der Filterpipeline wie Ressourcenfilter, vor der Modellbindung und nach dem Rest der Pipeline ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="815d6-462">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="815d6-463">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="815d6-463">Next actions</span></span>

* <span data-ttu-id="815d6-464">Siehe [Filtermethoden für Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="815d6-464">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="815d6-465">Um Filter näher kennenzulernen, [laden Sie das GitHub-Beispiel herunter, und testen und bearbeiten Sie es](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="815d6-465">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="815d6-466">Von [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="815d6-466">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="815d6-467">*Filter* ermöglichen es in ASP.NET Core, Code vor oder nach bestimmten Phasen der Anforderungsverarbeitungspipeline auszuführen.</span><span class="sxs-lookup"><span data-stu-id="815d6-467">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="815d6-468">Integrierte Filter sind für folgende Aufgaben zuständig:</span><span class="sxs-lookup"><span data-stu-id="815d6-468">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="815d6-469">Autorisierung (der Zugriff auf Ressourcen, für die ein Benutzer nicht autorisiert ist, wird verhindert)</span><span class="sxs-lookup"><span data-stu-id="815d6-469">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="815d6-470">Zwischenspeicherung von Antworten (die Anforderungspipeline wird unterbrochen, damit eine zwischengespeicherte Antwort zurückgegeben wird)</span><span class="sxs-lookup"><span data-stu-id="815d6-470">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="815d6-471">Durch die Erstellung benutzerdefinierter Filter kann mit aktionsübergreifenden Problemen umgegangen werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-471">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="815d6-472">Zu solchen übergreifenden Problemen gehören beispielsweise Fehlerbehandlung, Caching, Konfiguration, Autorisierung und Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="815d6-472">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="815d6-473">Mit Filtern lässt sich die Duplizierung von Code vermeiden.</span><span class="sxs-lookup"><span data-stu-id="815d6-473">Filters avoid duplicating code.</span></span> <span data-ttu-id="815d6-474">Sie können zum Beispiel die Fehlerbehandlung in einem Ausnahmefilter konsolidieren.</span><span class="sxs-lookup"><span data-stu-id="815d6-474">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="815d6-475">Dieses Dokument gilt für Razor Pages, API-Controller und Controller mit Ansichten.</span><span class="sxs-lookup"><span data-stu-id="815d6-475">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="815d6-476">[Zeigen Sie ein Beispiel an, oder laden Sie es herunter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="815d6-476">[View or download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="815d6-477">Die Funktionsweise von Filtern</span><span class="sxs-lookup"><span data-stu-id="815d6-477">How filters work</span></span>

<span data-ttu-id="815d6-478">Filter werden in der *ASP.NET Core-Aktionsaufrufpipeline* ausgeführt, die manchmal auch als *Filterpipeline* bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="815d6-478">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="815d6-479">Die Filterpipeline wird ausgeführt, nachdem ASP.NET Core die auszuführende Aktion ausgewählt hat.</span><span class="sxs-lookup"><span data-stu-id="815d6-479">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Die Anforderung wird von weiterer Middleware, Routingmiddleware, Aktionsauswahl und der ASP.NET Core-Aktionsaufrufpipeline verarbeitet.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="815d6-482">Filtertypen</span><span class="sxs-lookup"><span data-stu-id="815d6-482">Filter types</span></span>

<span data-ttu-id="815d6-483">Jeder Filtertyp wird in einer anderen Phase der Filterpipeline ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="815d6-483">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="815d6-484">[Autorisierungsfilter](#authorization-filters) werden zuerst ausgeführt und dienen dazu, festzustellen, ob der Benutzer für die Anforderung berechtigt ist.</span><span class="sxs-lookup"><span data-stu-id="815d6-484">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="815d6-485">Autorisierungsfilter schließen die Pipeline kurz, wenn die Anforderung nicht autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="815d6-485">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="815d6-486">[Ressourcenfilter](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="815d6-486">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="815d6-487">Werden nach der Autorisierung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="815d6-487">Run after authorization.</span></span>  
  * <span data-ttu-id="815d6-488"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> kann Code vor dem Rest der Filterpipeline ausführen.</span><span class="sxs-lookup"><span data-stu-id="815d6-488"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="815d6-489">Beispielsweise kann `OnResourceExecuting` Code vor der Modellbindung ausführen.</span><span class="sxs-lookup"><span data-stu-id="815d6-489">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="815d6-490"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> kann Code nach Abschluss der restlichen Pipeline ausführen.</span><span class="sxs-lookup"><span data-stu-id="815d6-490"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="815d6-491">[Aktionsfilter](#action-filters) können, vor und nach dem Aufruf einer individuelle Aktionsmethode, Code ausführen.</span><span class="sxs-lookup"><span data-stu-id="815d6-491">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="815d6-492">Sie können zur Bearbeitung der Argumente, die an eine Aktion übermittelt werden, und des Ergebnisses, das von der Aktion zurückgegeben wird, verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-492">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="815d6-493">Aktionsfilter werden in Razor Pages **nicht** unterstützt.</span><span class="sxs-lookup"><span data-stu-id="815d6-493">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="815d6-494">[Ausnahmefilter](#exception-filters) werden dazu verwendet, globale Richtlinien auf unbehandelte Ausnahmen anzuwenden, die auftreten, bevor etwas in den Antworttext geschrieben wurde.</span><span class="sxs-lookup"><span data-stu-id="815d6-494">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="815d6-495">[Ergebnisfilter](#result-filters) können, vor und nach dem Ergebnis einer ausgeführten individuellen Aktion, Code ausführen.</span><span class="sxs-lookup"><span data-stu-id="815d6-495">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="815d6-496">Sie werden nur ausgeführt, wenn die Aktionsmethode erfolgreich ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="815d6-496">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="815d6-497">Ergebnisfilter sind hilfreich für Logik, die die Ausführung von Ansichten oder Formatierern umfasst.</span><span class="sxs-lookup"><span data-stu-id="815d6-497">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="815d6-498">Das folgende Diagramm veranschaulicht, wie die Filtertypen mit der Filterpipeline interagieren.</span><span class="sxs-lookup"><span data-stu-id="815d6-498">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Die Anforderung wird von Autorisierungsfilter, Ressourcenfilter, Modellbindung, Aktionsfilter, Aktionsausführung sowie Aktionsergebniskonvertierung, Ausnahmefilter, Ergebnisfilter und Ergebnisausführung verarbeitet.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="815d6-501">Implementierung</span><span class="sxs-lookup"><span data-stu-id="815d6-501">Implementation</span></span>

<span data-ttu-id="815d6-502">Filter unterstützen sowohl synchrone als auch asynchrone Implementierungen über verschiedene Schnittstellendefinitionen.</span><span class="sxs-lookup"><span data-stu-id="815d6-502">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="815d6-503">Synchrone Filter können Code vor (`On-Stage-Executing`) und nach (`On-Stage-Executed`) der jeweiligen Pipelinephase ausführen.</span><span class="sxs-lookup"><span data-stu-id="815d6-503">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="815d6-504">`OnActionExecuting` beispielsweise wird aufgerufen, bevor die Aktionsmethode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="815d6-504">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="815d6-505">`OnActionExecuted` wird nach der Rückgabe der Aktionsmethode aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="815d6-505">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="815d6-506">Asynchrone Filter definieren eine `On-Stage-ExecutionAsync`-Methode:</span><span class="sxs-lookup"><span data-stu-id="815d6-506">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="815d6-507">Im oben stehenden Code weist der `SampleAsyncActionFilter` einen <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) auf, der die Aktionsmethode ausführt.</span><span class="sxs-lookup"><span data-stu-id="815d6-507">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="815d6-508">Jede der `On-Stage-ExecutionAsync`-Methoden akzeptiert einen `FilterType-ExecutionDelegate`, der die Pipelinephase des Filters ausführt.</span><span class="sxs-lookup"><span data-stu-id="815d6-508">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="815d6-509">Mehrere Filterphasen</span><span class="sxs-lookup"><span data-stu-id="815d6-509">Multiple filter stages</span></span>

<span data-ttu-id="815d6-510">Schnittstellen für mehrere Filterphasen können in einer einzigen Klasse implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-510">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="815d6-511">Beispielsweise implementiert die Klasse <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>`IActionFilter`, `IResultFilter`, und deren asynchrone Gegenstücke.</span><span class="sxs-lookup"><span data-stu-id="815d6-511">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="815d6-512">Implementieren Sie **entweder** die synchrone oder asynchrone Version einer Filterschnittstelle, aber **nicht** beide.</span><span class="sxs-lookup"><span data-stu-id="815d6-512">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="815d6-513">Die Runtime prüft zuerst, ob der Filter die asynchrone Schnittstelle implementiert, und wenn dies der Fall ist, ruft die Runtime diese Schnittstelle auf.</span><span class="sxs-lookup"><span data-stu-id="815d6-513">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="815d6-514">Wenn dies nicht der Fall ist, ruft es die Methode(n) der synchronen Schnittstelle auf.</span><span class="sxs-lookup"><span data-stu-id="815d6-514">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="815d6-515">Wenn die asynchrone und die synchrone Schnittstelle in einer einzigen Klasse implementiert sind, wird nur die asynchrone Methode aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="815d6-515">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="815d6-516">Bei Verwendung von abstrakten Klassen wie <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> überschreiben Sie nur die synchronen Methoden oder die asynchrone Methode für jeden Filtertyp.</span><span class="sxs-lookup"><span data-stu-id="815d6-516">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="815d6-517">Integrierte Filterattribute</span><span class="sxs-lookup"><span data-stu-id="815d6-517">Built-in filter attributes</span></span>

<span data-ttu-id="815d6-518">ASP.NET Core enthält integrierte attributbasierte Filter, die als Unterklasse erstellt und angepasst werden können.</span><span class="sxs-lookup"><span data-stu-id="815d6-518">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="815d6-519">Zum Beispiel fügt der folgende Ergebnisfilter der Antwort einen Header hinzu:</span><span class="sxs-lookup"><span data-stu-id="815d6-519">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="815d6-520">Wie im obigen Beispiel gezeigt, können Filter mithilfe von Attributen Argumente akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="815d6-520">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="815d6-521">Wenden Sie das `AddHeaderAttribute` auf einen Controller oder eine Aktionsmethode an, und geben Sie den Namen und den Wert des HTTP-Headers an:</span><span class="sxs-lookup"><span data-stu-id="815d6-521">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="815d6-522">Einige der Filterschnittstellen besitzen entsprechende Attribute, die als Basisklassen für benutzerdefinierte Implementierungen verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="815d6-522">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="815d6-523">Filterattribute:</span><span class="sxs-lookup"><span data-stu-id="815d6-523">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="815d6-524">Filterbereiche und Reihenfolge der Ausführung</span><span class="sxs-lookup"><span data-stu-id="815d6-524">Filter scopes and order of execution</span></span>

<span data-ttu-id="815d6-525">Der Pipeline kann in einem von drei *Bereichen* ein Filter hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="815d6-525">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="815d6-526">Verwenden eines Attributs in einer Aktion</span><span class="sxs-lookup"><span data-stu-id="815d6-526">Using an attribute on an action.</span></span>
* <span data-ttu-id="815d6-527">Verwenden eines Attributs in einem Controller</span><span class="sxs-lookup"><span data-stu-id="815d6-527">Using an attribute on a controller.</span></span>
* <span data-ttu-id="815d6-528">Global für alle Controller und Aktionen, wie in folgendem Code gezeigt:</span><span class="sxs-lookup"><span data-stu-id="815d6-528">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="815d6-529">Der oben stehende Code fügt mithilfe der Sammlung [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) global drei Filter hinzu.</span><span class="sxs-lookup"><span data-stu-id="815d6-529">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="815d6-530">Standardreihenfolge der Ausführung</span><span class="sxs-lookup"><span data-stu-id="815d6-530">Default order of execution</span></span>

<span data-ttu-id="815d6-531">Wenn mehrere Filter *des gleichen Typs* vorhanden sind, bestimmt der Umfang die Standardreihenfolge ihrer Ausführung.</span><span class="sxs-lookup"><span data-stu-id="815d6-531">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="815d6-532">Globale Filter umschließen Klassenfilter.</span><span class="sxs-lookup"><span data-stu-id="815d6-532">Global filters surround class filters.</span></span> <span data-ttu-id="815d6-533">Klassenfilter umschließen Methodenfilter.</span><span class="sxs-lookup"><span data-stu-id="815d6-533">Class filters surround method filters.</span></span>

<span data-ttu-id="815d6-534">Als Ergebnis der Filterschachtelung wird der *nach* einer Pipelinephase auszuführende Code in umgekehrter Reihenfolge zum Code *vor* einer Pipelinephase ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="815d6-534">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="815d6-535">Filterreihenfolge:</span><span class="sxs-lookup"><span data-stu-id="815d6-535">The filter sequence:</span></span>

* <span data-ttu-id="815d6-536">Der Code von globalen Filtern, der *vor* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="815d6-536">The *before* code of global filters.</span></span>
  * <span data-ttu-id="815d6-537">Der Code von Controllerfiltern, der *vor* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="815d6-537">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="815d6-538">Der Code von Aktionsmethodenfiltern, der *vor* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="815d6-538">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="815d6-539">Der Code von Aktionsmethodenfiltern, der *nach* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="815d6-539">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="815d6-540">Der Code von Controllerfiltern, der *nach* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="815d6-540">The *after* code of controller filters.</span></span>
* <span data-ttu-id="815d6-541">Der Code von globalen Filtern, der *nach* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="815d6-541">The *after* code of global filters.</span></span>
  
<span data-ttu-id="815d6-542">Das folgende Beispiel veranschaulicht die Reihenfolge, in der Filtermethoden für synchrone Aktionsfilter aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-542">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="815d6-543">Sequenz</span><span class="sxs-lookup"><span data-stu-id="815d6-543">Sequence</span></span> | <span data-ttu-id="815d6-544">Filterbereich</span><span class="sxs-lookup"><span data-stu-id="815d6-544">Filter scope</span></span> | <span data-ttu-id="815d6-545">Filtermethode</span><span class="sxs-lookup"><span data-stu-id="815d6-545">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="815d6-546">1</span><span class="sxs-lookup"><span data-stu-id="815d6-546">1</span></span> | <span data-ttu-id="815d6-547">Global</span><span class="sxs-lookup"><span data-stu-id="815d6-547">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="815d6-548">2</span><span class="sxs-lookup"><span data-stu-id="815d6-548">2</span></span> | <span data-ttu-id="815d6-549">Controller</span><span class="sxs-lookup"><span data-stu-id="815d6-549">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="815d6-550">3</span><span class="sxs-lookup"><span data-stu-id="815d6-550">3</span></span> | <span data-ttu-id="815d6-551">Methode</span><span class="sxs-lookup"><span data-stu-id="815d6-551">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="815d6-552">4</span><span class="sxs-lookup"><span data-stu-id="815d6-552">4</span></span> | <span data-ttu-id="815d6-553">Methode</span><span class="sxs-lookup"><span data-stu-id="815d6-553">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="815d6-554">5</span><span class="sxs-lookup"><span data-stu-id="815d6-554">5</span></span> | <span data-ttu-id="815d6-555">Controller</span><span class="sxs-lookup"><span data-stu-id="815d6-555">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="815d6-556">6</span><span class="sxs-lookup"><span data-stu-id="815d6-556">6</span></span> | <span data-ttu-id="815d6-557">Global</span><span class="sxs-lookup"><span data-stu-id="815d6-557">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="815d6-558">Diese Sequenz veranschaulicht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="815d6-558">This sequence shows:</span></span>

* <span data-ttu-id="815d6-559">Der Methodenfilter ist innerhalb des Controllerfilters geschachtelt.</span><span class="sxs-lookup"><span data-stu-id="815d6-559">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="815d6-560">Der Controllerfilter ist innerhalb des globalen Filters geschachtelt.</span><span class="sxs-lookup"><span data-stu-id="815d6-560">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="815d6-561">Filter auf Controller- und Razor Page-Ebene</span><span class="sxs-lookup"><span data-stu-id="815d6-561">Controller and Razor Page level filters</span></span>

<span data-ttu-id="815d6-562">Jeder Controller, der von der Basisklasse <xref:Microsoft.AspNetCore.Mvc.Controller> erbt, enthält die Methoden [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) und [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="815d6-562">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="815d6-563">Diese Methoden führen Folgendes aus:</span><span class="sxs-lookup"><span data-stu-id="815d6-563">These methods:</span></span>

* <span data-ttu-id="815d6-564">Die Filter, die für eine bestimmte Aktion ausgeführt werden, werden umschlossen.</span><span class="sxs-lookup"><span data-stu-id="815d6-564">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="815d6-565">`OnActionExecuting` wird vor allen Filtern der Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="815d6-565">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="815d6-566">`OnActionExecuted` wird nach allen Filtern der Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="815d6-566">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="815d6-567">`OnActionExecutionAsync` wird vor allen Filtern der Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="815d6-567">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="815d6-568">Code im Filter nach `next` wird nach der Aktionsmethode ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="815d6-568">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="815d6-569">Im Downloadbeispiel wird `MySampleActionFilter` global beim Start angewendet.</span><span class="sxs-lookup"><span data-stu-id="815d6-569">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="815d6-570">Die `TestController`:</span><span class="sxs-lookup"><span data-stu-id="815d6-570">The `TestController`:</span></span>

* <span data-ttu-id="815d6-571">Wendet das `SampleActionFilterAttribute` (`[SampleActionFilter]`) auf die Aktion `FilterTest2` an.</span><span class="sxs-lookup"><span data-stu-id="815d6-571">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="815d6-572">Überschreibt `OnActionExecuting` und `OnActionExecuted`:</span><span class="sxs-lookup"><span data-stu-id="815d6-572">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="815d6-573">Durch Navigieren zu `https://localhost:5001/Test/FilterTest2` wird der folgende Code ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="815d6-573">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="815d6-574">Informationen zu Razor Pages finden Sie unter [Implementieren von Filtern für Razor-Seiten durch Überschreiben von Filtermethoden](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="815d6-574">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="815d6-575">Überschreiben der Standardreihenfolge</span><span class="sxs-lookup"><span data-stu-id="815d6-575">Overriding the default order</span></span>

<span data-ttu-id="815d6-576">Die Standardreihenfolge der Ausführung kann durch Implementieren von <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-576">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="815d6-577">`IOrderedFilter` macht die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> verfügbar, die bei der Bestimmung der Ausführungsreihenfolge Vorrang vor dem Bereich hat.</span><span class="sxs-lookup"><span data-stu-id="815d6-577">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="815d6-578">Für einen Filter mit geringerem `Order`-Wert gilt:</span><span class="sxs-lookup"><span data-stu-id="815d6-578">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="815d6-579">Führt den Code, der *vor* einer Pipelinephase ausgeführt werden soll, vor dem Code eines Filters mit einem höheren Wert für `Order` aus.</span><span class="sxs-lookup"><span data-stu-id="815d6-579">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="815d6-580">Führt den Code, der *nach* einer Pipelinephase ausgeführt werden soll, nach dem Code eines Filters mit einem höheren Wert für `Order` aus.</span><span class="sxs-lookup"><span data-stu-id="815d6-580">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="815d6-581">Die Eigenschaft `Order` kann mit einem Konstruktorparameter festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="815d6-581">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="815d6-582">Sehen Sie sich die drei Aktionsfilter an, die im vorherigen Beispiel gezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-582">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="815d6-583">Wenn die `Order`-Eigenschaft des Controllers und der globalen Filter auf 1 bzw. 2 festgelegt ist, wird die Reihenfolge der Ausführung umgekehrt.</span><span class="sxs-lookup"><span data-stu-id="815d6-583">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="815d6-584">Sequenz</span><span class="sxs-lookup"><span data-stu-id="815d6-584">Sequence</span></span> | <span data-ttu-id="815d6-585">Filterbereich</span><span class="sxs-lookup"><span data-stu-id="815d6-585">Filter scope</span></span> | <span data-ttu-id="815d6-586">`Order` -Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="815d6-586">`Order` property</span></span> | <span data-ttu-id="815d6-587">Filtermethode</span><span class="sxs-lookup"><span data-stu-id="815d6-587">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="815d6-588">1</span><span class="sxs-lookup"><span data-stu-id="815d6-588">1</span></span> | <span data-ttu-id="815d6-589">Methode</span><span class="sxs-lookup"><span data-stu-id="815d6-589">Method</span></span> | <span data-ttu-id="815d6-590">0</span><span class="sxs-lookup"><span data-stu-id="815d6-590">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="815d6-591">2</span><span class="sxs-lookup"><span data-stu-id="815d6-591">2</span></span> | <span data-ttu-id="815d6-592">Controller</span><span class="sxs-lookup"><span data-stu-id="815d6-592">Controller</span></span> | <span data-ttu-id="815d6-593">1</span><span class="sxs-lookup"><span data-stu-id="815d6-593">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="815d6-594">3</span><span class="sxs-lookup"><span data-stu-id="815d6-594">3</span></span> | <span data-ttu-id="815d6-595">Global</span><span class="sxs-lookup"><span data-stu-id="815d6-595">Global</span></span> | <span data-ttu-id="815d6-596">2</span><span class="sxs-lookup"><span data-stu-id="815d6-596">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="815d6-597">4</span><span class="sxs-lookup"><span data-stu-id="815d6-597">4</span></span> | <span data-ttu-id="815d6-598">Global</span><span class="sxs-lookup"><span data-stu-id="815d6-598">Global</span></span> | <span data-ttu-id="815d6-599">2</span><span class="sxs-lookup"><span data-stu-id="815d6-599">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="815d6-600">5</span><span class="sxs-lookup"><span data-stu-id="815d6-600">5</span></span> | <span data-ttu-id="815d6-601">Controller</span><span class="sxs-lookup"><span data-stu-id="815d6-601">Controller</span></span> | <span data-ttu-id="815d6-602">1</span><span class="sxs-lookup"><span data-stu-id="815d6-602">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="815d6-603">6</span><span class="sxs-lookup"><span data-stu-id="815d6-603">6</span></span> | <span data-ttu-id="815d6-604">Methode</span><span class="sxs-lookup"><span data-stu-id="815d6-604">Method</span></span> | <span data-ttu-id="815d6-605">0</span><span class="sxs-lookup"><span data-stu-id="815d6-605">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="815d6-606">Die Eigenschaft `Order` hat bei der Bestimmung der Reihenfolge, in der Filter ausgeführt werden, Vorrang vor dem Bereich.</span><span class="sxs-lookup"><span data-stu-id="815d6-606">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="815d6-607">Filter werden erst nach der Reihenfolge sortiert, und dann werden Gleichstände über den Bereich aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="815d6-607">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="815d6-608">Alle integrierten Filter implementieren `IOrderedFilter` und legen den Standartwert von `Order` auf 0 fest.</span><span class="sxs-lookup"><span data-stu-id="815d6-608">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="815d6-609">Bei integrierten Filtern bestimmt der Bereich die Reihenfolge, falls `Order` nicht auf einen Wert ungleich 0 festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="815d6-609">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="815d6-610">Abbrechen und Kurzschließen</span><span class="sxs-lookup"><span data-stu-id="815d6-610">Cancellation and short-circuiting</span></span>

<span data-ttu-id="815d6-611">Die Filterpipeline kann kurzgeschlossen werden, indem die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> auf den Parameter <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> festgelegt wird, der in der Filtermethode angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="815d6-611">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="815d6-612">Zum Beispiel verhindert der folgende Ressourcenfilter, dass der Rest der Pipeline ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="815d6-612">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="815d6-613">Im folgenden Code verwenden sowohl der Filter `ShortCircuitingResourceFilter` und der Filter `AddHeader` die Aktionsmethode `SomeResource` als Ziel.</span><span class="sxs-lookup"><span data-stu-id="815d6-613">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="815d6-614">Die `ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="815d6-614">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="815d6-615">Der Filter wird zuerst ausgeführt, da es sich um einen Ressourcenfilter handelt und `AddHeader` ein Aktionsfilter ist.</span><span class="sxs-lookup"><span data-stu-id="815d6-615">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="815d6-616">Der Filter unterbricht alle verbleibenden Pipelineschritte.</span><span class="sxs-lookup"><span data-stu-id="815d6-616">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="815d6-617">Der `AddHeader`-Filter wird daher nie für die `SomeResource`-Aktion ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="815d6-617">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="815d6-618">Dasselbe Verhalten würde auch dann auftreten, wenn beide Filter auf Aktionsmethodenebene angewendet würden, wenn `ShortCircuitingResourceFilter` zuerst ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="815d6-618">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="815d6-619">Der `ShortCircuitingResourceFilter` wird aufgrund seines Filtertyps oder durch die explizite Verwendung der `Order`-Eigenschaft zuerst ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="815d6-619">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="815d6-620">Dependency Injection</span><span class="sxs-lookup"><span data-stu-id="815d6-620">Dependency injection</span></span>

<span data-ttu-id="815d6-621">Filter können nach Typ oder Instanz hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-621">Filters can be added by type or by instance.</span></span> <span data-ttu-id="815d6-622">Wenn eine Instanz hinzugefügt wird, wird diese Instanz für jede Anforderung verwendet.</span><span class="sxs-lookup"><span data-stu-id="815d6-622">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="815d6-623">Wenn ein Typ hinzugefügt wird, ist der Filter typaktiviert.</span><span class="sxs-lookup"><span data-stu-id="815d6-623">If a type is added, it's type-activated.</span></span> <span data-ttu-id="815d6-624">Ein typaktivierter Filter bedeutet Folgendes:</span><span class="sxs-lookup"><span data-stu-id="815d6-624">A type-activated filter means:</span></span>

* <span data-ttu-id="815d6-625">Für jede Anforderung wird eine Instanz erstellt.</span><span class="sxs-lookup"><span data-stu-id="815d6-625">An instance is created for each request.</span></span>
* <span data-ttu-id="815d6-626">Alle Konstruktorabhängigkeiten werden durch [Dependency Injection](xref:fundamentals/dependency-injection) (DI) aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="815d6-626">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="815d6-627">Filter, die als Attribute implementiert und Controllerklassen oder Aktionsmethoden direkt hinzugefügt werden, können keine Konstruktorabhängigkeiten von [Dependency Injection](xref:fundamentals/dependency-injection) (DI) erhalten.</span><span class="sxs-lookup"><span data-stu-id="815d6-627">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="815d6-628">Konstruktorabhängigkeiten können aus folgenden Gründen von DI nicht bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="815d6-628">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="815d6-629">Für Attribute müssen Konstruktorparameter bereitgestellt werden, wenn sie angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-629">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="815d6-630">Dies ist eine Einschränkung der Funktionsweise von Attributen.</span><span class="sxs-lookup"><span data-stu-id="815d6-630">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="815d6-631">Die folgenden Filter unterstützen von DI bereitgestellte Konstruktorabhängigkeiten:</span><span class="sxs-lookup"><span data-stu-id="815d6-631">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="815d6-632"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>, im Attribut implementiert</span><span class="sxs-lookup"><span data-stu-id="815d6-632"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="815d6-633">Die oben genannten Filter können auf einen Controller oder eine Aktionsmethode angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="815d6-633">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="815d6-634">DI bietet Protokollierungen.</span><span class="sxs-lookup"><span data-stu-id="815d6-634">Loggers are available from DI.</span></span> <span data-ttu-id="815d6-635">Vermeiden Sie es jedoch, Filter nur zum Zweck der Protokollierung zu erstellen und zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="815d6-635">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="815d6-636">Die integrierte [Frameworkprotokollierung](xref:fundamentals/logging/index) bietet in der Regel alle Elemente, die für die Protokollierung erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="815d6-636">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="815d6-637">Wenn Protokollierung zu Filtern hinzugefügt wird, gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="815d6-637">Logging added to filters:</span></span>

* <span data-ttu-id="815d6-638">Die Protokollierung sollte mit Schwerpunkt auf geschäftlichen Aspekten oder verhaltensspezifisch für den Filter erfolgen.</span><span class="sxs-lookup"><span data-stu-id="815d6-638">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="815d6-639">Aktionen oder andere Frameworkereignisse sollten **nicht** protokolliert werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-639">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="815d6-640">Die integrierten Filter protokollieren Aktionen und Frameworkereignisse.</span><span class="sxs-lookup"><span data-stu-id="815d6-640">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="815d6-641">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="815d6-641">ServiceFilterAttribute</span></span>

<span data-ttu-id="815d6-642">Die Typen der Dienstfilterimplementierung werden in `ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="815d6-642">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="815d6-643">Ein <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> ruft eine Instanz des Filter von DI ab.</span><span class="sxs-lookup"><span data-stu-id="815d6-643">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="815d6-644">Der folgende Code zeigt den `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="815d6-644">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="815d6-645">Im folgenden Code wird `AddHeaderResultServiceFilter` zum DI-Container hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="815d6-645">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="815d6-646">Im folgenden Code ruft das `ServiceFilter`-Attribut eine Instanz des `AddHeaderResultServiceFilter`-Filters aus DI ab:</span><span class="sxs-lookup"><span data-stu-id="815d6-646">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="815d6-647">Beim Verwenden von `ServiceFilterAttribute` wird [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) festgelegt:</span><span class="sxs-lookup"><span data-stu-id="815d6-647">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="815d6-648">Gibt einen Hinweis darauf, dass die Filterinstanz *möglicherweise* außerhalb des Anforderungsbereichs, in dem sie erstellt wurde, wiederverwendet wird.</span><span class="sxs-lookup"><span data-stu-id="815d6-648">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="815d6-649">Die ASP.NET Core-Runtime bietet keine Garantie für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="815d6-649">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="815d6-650">Eine einzelne Instanz des Filters wird erstellt.</span><span class="sxs-lookup"><span data-stu-id="815d6-650">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="815d6-651">Der Filter wird nicht zu einem späteren Zeitpunkt vom DI-Container erneut angefordert.</span><span class="sxs-lookup"><span data-stu-id="815d6-651">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="815d6-652">Sollte nicht mit einem Filter verwendet werden, der von Diensten mit einer anderen Lebensdauer als der eines Singletons abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="815d6-652">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="815d6-653"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementiert <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="815d6-653"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="815d6-654">`IFilterFactory` macht die <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*>-Methode zum Erstellen einer <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>-Instanz verfügbar.</span><span class="sxs-lookup"><span data-stu-id="815d6-654">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="815d6-655">`CreateInstance` lädt den angegebenen Typ aus DI.</span><span class="sxs-lookup"><span data-stu-id="815d6-655">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="815d6-656">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="815d6-656">TypeFilterAttribute</span></span>

<span data-ttu-id="815d6-657"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> ähnelt <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>. Der zugehörige Typ wird allerdings nicht direkt vom DI-Container aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="815d6-657"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="815d6-658">Stattdessen wird der Typ mithilfe von <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> instanziiert.</span><span class="sxs-lookup"><span data-stu-id="815d6-658">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="815d6-659">Da `TypeFilterAttribute`-Typen nicht direkt vom DI-Container aufgelöst werden, gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="815d6-659">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="815d6-660">Typen, auf die mithilfe von `TypeFilterAttribute` verwiesen wird, müssen nicht beim DI-Container registriert werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-660">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="815d6-661">Ihre Abhängigkeiten werden vom DI-Container erfüllt.</span><span class="sxs-lookup"><span data-stu-id="815d6-661">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="815d6-662">`TypeFilterAttribute` kann optional Konstruktorargumente für den Typ akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="815d6-662">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="815d6-663">Beim Verwenden von `TypeFilterAttribute` wird [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) festgelegt:</span><span class="sxs-lookup"><span data-stu-id="815d6-663">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="815d6-664">Gibt einen Hinweis darauf, dass die Filterinstanz *möglicherweise* außerhalb des Anforderungsbereichs wiederverwendet wird, in dem sie erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="815d6-664">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="815d6-665">Die ASP.NET Core-Runtime bietet keine Garantie dafür, dass eine einzelne Instanz des Filters erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="815d6-665">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="815d6-666">Sollte nicht mit einem Filter verwendet werden, der von Diensten mit einer anderen Lebensdauer als der eines Singletons abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="815d6-666">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="815d6-667">Das folgende Beispiel zeigt, wie Sie Argumente durch Verwendung von `TypeFilterAttribute` an einen Typ übergeben:</span><span class="sxs-lookup"><span data-stu-id="815d6-667">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="815d6-668">Autorisierungsfilter</span><span class="sxs-lookup"><span data-stu-id="815d6-668">Authorization filters</span></span>

<span data-ttu-id="815d6-669">Für Autorisierungsfilter gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="815d6-669">Authorization filters:</span></span>

* <span data-ttu-id="815d6-670">Sie werden als erste Filter in der Filterpipeline ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="815d6-670">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="815d6-671">Sie steuern den Zugriff auf Aktionsmethoden.</span><span class="sxs-lookup"><span data-stu-id="815d6-671">Control access to action methods.</span></span>
* <span data-ttu-id="815d6-672">Sie verfügen nur über eine Methode, die vor der Aktion ausgeführt wird, nicht jedoch über eine Methode, die nach der Aktion ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="815d6-672">Have a before method, but no after method.</span></span>

<span data-ttu-id="815d6-673">Benutzerdefinierte Autorisierungsfilter erfordern ein benutzerdefiniertes Autorisierungsframework.</span><span class="sxs-lookup"><span data-stu-id="815d6-673">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="815d6-674">Statt einen benutzerdefinierten Filter zu schreiben, sollten Sie die Autorisierungsrichtlinien konfigurieren oder eine benutzerdefinierte Autorisierungsrichtlinie schreiben.</span><span class="sxs-lookup"><span data-stu-id="815d6-674">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="815d6-675">Für den integrierten Autorisierungsfilter gilt:</span><span class="sxs-lookup"><span data-stu-id="815d6-675">The built-in authorization filter:</span></span>

* <span data-ttu-id="815d6-676">Er ruft das Autorisierungssystem auf.</span><span class="sxs-lookup"><span data-stu-id="815d6-676">Calls the authorization system.</span></span>
* <span data-ttu-id="815d6-677">Er autorisiert keine Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="815d6-677">Does not authorize requests.</span></span>

<span data-ttu-id="815d6-678">Lösen Sie **keine** Ausnahmen in Autorisierungsfiltern aus:</span><span class="sxs-lookup"><span data-stu-id="815d6-678">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="815d6-679">Die Ausnahme wird nicht behandelt.</span><span class="sxs-lookup"><span data-stu-id="815d6-679">The exception will not be handled.</span></span>
* <span data-ttu-id="815d6-680">Ausnahmefilter behandeln die Ausnahme nicht.</span><span class="sxs-lookup"><span data-stu-id="815d6-680">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="815d6-681">Beim Auftreten einer Ausnahme in einem Autorisierungsfilter sollten Sie eine Abfrage erwägen.</span><span class="sxs-lookup"><span data-stu-id="815d6-681">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="815d6-682">Weitere Informationen finden Sie unter [Autorisierung](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="815d6-682">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="815d6-683">Ressourcenfilter</span><span class="sxs-lookup"><span data-stu-id="815d6-683">Resource filters</span></span>

<span data-ttu-id="815d6-684">Für Ressourcenfilter gilt:</span><span class="sxs-lookup"><span data-stu-id="815d6-684">Resource filters:</span></span>

* <span data-ttu-id="815d6-685">Sie implementieren entweder die Schnittstelle <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="815d6-685">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="815d6-686">Die Ausführung umschließt den größten Teil der Filterpipeline.</span><span class="sxs-lookup"><span data-stu-id="815d6-686">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="815d6-687">Nur [Autorisierungsfilter](#authorization-filters) werden vor Ressourcenfiltern ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="815d6-687">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="815d6-688">Ressourcenfilter sind hilfreich, um den größten Teil der Pipeline kurzzuschließen.</span><span class="sxs-lookup"><span data-stu-id="815d6-688">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="815d6-689">Ein Cachingfilter kann beispielsweise bei einem Cachetreffer den Rest der Pipeline überspringen.</span><span class="sxs-lookup"><span data-stu-id="815d6-689">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="815d6-690">Beispiele für Ressourcenfilter:</span><span class="sxs-lookup"><span data-stu-id="815d6-690">Resource filter examples:</span></span>

* <span data-ttu-id="815d6-691">Der oben gezeigte [Ressourcenfilter zum Kurzschließen](#short-circuiting-resource-filter).</span><span class="sxs-lookup"><span data-stu-id="815d6-691">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="815d6-692">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="815d6-692">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="815d6-693">Verhindert, dass die Modellbindung auf die Formulardaten zugreift.</span><span class="sxs-lookup"><span data-stu-id="815d6-693">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="815d6-694">Wird bei großen Dateiuploads verwendet, um zu verhindern, dass die Formulardaten in den Arbeitsspeicher eingelesen werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-694">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="815d6-695">Aktionsfilter</span><span class="sxs-lookup"><span data-stu-id="815d6-695">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="815d6-696">Aktionsfilter gelten **nicht** für Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="815d6-696">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="815d6-697">Razor Pages unterstützen <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> und <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter>.</span><span class="sxs-lookup"><span data-stu-id="815d6-697">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="815d6-698">Weitere Informationen finden Sie unter [Filtermethoden für Razor-Seiten](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="815d6-698">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="815d6-699">Für Aktionsfilter gilt:</span><span class="sxs-lookup"><span data-stu-id="815d6-699">Action filters:</span></span>

* <span data-ttu-id="815d6-700">Sie implementieren entweder die Schnittstelle <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="815d6-700">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="815d6-701">Ihre Ausführung umfasst die Ausführung von Aktionsmethoden.</span><span class="sxs-lookup"><span data-stu-id="815d6-701">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="815d6-702">Der folgende Code zeigt einen Beispielaktionsfilter:</span><span class="sxs-lookup"><span data-stu-id="815d6-702">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="815d6-703"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> bietet folgende Eigenschaften:</span><span class="sxs-lookup"><span data-stu-id="815d6-703">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="815d6-704"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> ermöglicht das Lesen der Eingaben für eine Aktionsmethode.</span><span class="sxs-lookup"><span data-stu-id="815d6-704"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="815d6-705"><xref:Microsoft.AspNetCore.Mvc.Controller> ermöglicht das Ändern der Controllerinstanz.</span><span class="sxs-lookup"><span data-stu-id="815d6-705"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="815d6-706"><xref:System.Web.Mvc.ActionExecutingContext.Result>: Die Festlegung von `Result` schließt die Ausführung der Aktionsmethode und der nachfolgenden Aktionsfilter kurz.</span><span class="sxs-lookup"><span data-stu-id="815d6-706"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="815d6-707">Durch Auslösen einer Ausnahme in einer Aktionsmethode geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="815d6-707">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="815d6-708">Die Ausführung nachfolgender Filter wird verhindert.</span><span class="sxs-lookup"><span data-stu-id="815d6-708">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="815d6-709">Im Gegensatz zum Festlegen von `Result` wird die ausgelöste Ausnahme wird nicht als erfolgreiches Ergebnis, sondern als Fehler behandelt.</span><span class="sxs-lookup"><span data-stu-id="815d6-709">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="815d6-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> bietet `Controller` und `Result` sowie die folgenden Eigenschaften:</span><span class="sxs-lookup"><span data-stu-id="815d6-710">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="815d6-711"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> ist TRUE, wenn die Aktionsausführung durch einen anderen Filter kurzgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="815d6-711"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="815d6-712"><xref:System.Web.Mvc.ActionExecutedContext.Exception> ist ungleich NULL, wenn die Aktion oder ein zuvor ausgeführter Aktionsfilter eine Ausnahme ausgelöst hat.</span><span class="sxs-lookup"><span data-stu-id="815d6-712"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="815d6-713">Durch Festlegen dieser Eigenschaft auf NULL geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="815d6-713">Setting this property to null:</span></span>

  * <span data-ttu-id="815d6-714">Die Ausnahme wird effektiv behandelt.</span><span class="sxs-lookup"><span data-stu-id="815d6-714">Effectively handles the exception.</span></span>
  * <span data-ttu-id="815d6-715">`Result` wird so ausgeführt, als wäre das Ergebnis von der Aktionsmethode zurückgegeben worden.</span><span class="sxs-lookup"><span data-stu-id="815d6-715">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="815d6-716">Bei einem `IAsyncActionFilter` hat der Aufruf von <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> folgende Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="815d6-716">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="815d6-717">Alle nachfolgenden Aktionsfilter und die Aktionsmethode werden ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="815d6-717">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="815d6-718">Gibt `ActionExecutedContext`zurück.</span><span class="sxs-lookup"><span data-stu-id="815d6-718">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="815d6-719">Weisen Sie einer Ergebnisinstanz <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> zu, und rufen Sie nicht `next` (den `ActionExecutionDelegate`) auf, um die Pipeline kurzzuschließen.</span><span class="sxs-lookup"><span data-stu-id="815d6-719">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="815d6-720">Das Framework stellt ein abstraktes <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> bereit, das als Unterklasse verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="815d6-720">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="815d6-721">Der Aktionsfilter `OnActionExecuting` kann für Folgendes verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="815d6-721">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="815d6-722">Überprüfen des Modellzustands.</span><span class="sxs-lookup"><span data-stu-id="815d6-722">Validate model state.</span></span>
* <span data-ttu-id="815d6-723">Zurückgeben eines Fehlers, wenn der Zustand ungültig ist.</span><span class="sxs-lookup"><span data-stu-id="815d6-723">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="815d6-724">Die Methode `OnActionExecuted` wird nach der Aktionsmethode ausgeführt. Es gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="815d6-724">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="815d6-725">Die Methode kann die Ergebnisse der Aktion durch die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> sehen und ändern.</span><span class="sxs-lookup"><span data-stu-id="815d6-725">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="815d6-726"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> wird auf TRUE festgelegt, wenn die Ausführung der Aktion durch einen anderen Filter kurzgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="815d6-726"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="815d6-727"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> wird auf einen Wert ungleich NULL festgelegt, wenn die Aktion oder ein nachfolgender Aktionsfilter eine Ausnahme ausgelöst hat.</span><span class="sxs-lookup"><span data-stu-id="815d6-727"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="815d6-728">Wenn für `Exception` NULL festgelegt wird,</span><span class="sxs-lookup"><span data-stu-id="815d6-728">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="815d6-729">werden Ausnahmen effektiv behandelt,</span><span class="sxs-lookup"><span data-stu-id="815d6-729">Effectively handles an exception.</span></span>
  * <span data-ttu-id="815d6-730">und `ActionExecutedContext.Result` wird so ausgeführt, als würde die Eigenschaft normal von der Aktionsmethode zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-730">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="815d6-731">Ausnahmefilter</span><span class="sxs-lookup"><span data-stu-id="815d6-731">Exception filters</span></span>

<span data-ttu-id="815d6-732">Für Ausnahmefilter gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="815d6-732">Exception filters:</span></span>

* <span data-ttu-id="815d6-733">Sie implementieren <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="815d6-733">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="815d6-734">Sie können verwendet werden, um gängige Fehlerbehandlungsrichtlinien zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="815d6-734">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="815d6-735">Der folgende Beispielausnahmefilter verwendet eine benutzerdefinierte Fehleransicht, um Details zu Ausnahmen anzuzeigen, die auftreten, wenn die App sich in der Entwicklung befindet:</span><span class="sxs-lookup"><span data-stu-id="815d6-735">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="815d6-736">Für Ausnahmefilter gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="815d6-736">Exception filters:</span></span>

* <span data-ttu-id="815d6-737">Sie verfügen nicht über vorangehende oder darauffolgende Ereignisse.</span><span class="sxs-lookup"><span data-stu-id="815d6-737">Don't have before and after events.</span></span>
* <span data-ttu-id="815d6-738">Sie implementieren <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="815d6-738">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="815d6-739">Sie behandeln Ausnahmefehler, die bei der Erstellung von Razor Pages oder Controllern, bei der [Modellbindung](xref:mvc/models/model-binding), bei Aktionsfiltern oder bei Aktionsmethoden auftreten.</span><span class="sxs-lookup"><span data-stu-id="815d6-739">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="815d6-740">Sie erfassen **keine** Ausnahmen, die in Ressourcenfiltern, Ergebnisfiltern oder bei der Ausführung von MVC-Ergebnissen auftreten.</span><span class="sxs-lookup"><span data-stu-id="815d6-740">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="815d6-741">Um eine Ausnahme zu behandeln, muss die Eigenschaft <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> auf `true` festgelegt oder eine Antwort geschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-741">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="815d6-742">Dadurch wird die Weitergabe der Ausnahme beendet.</span><span class="sxs-lookup"><span data-stu-id="815d6-742">This stops propagation of the exception.</span></span> <span data-ttu-id="815d6-743">Ein Ausnahmefilter kann eine Ausnahme nicht in ein „erfolgreiches Ergebnis“ umwandeln.</span><span class="sxs-lookup"><span data-stu-id="815d6-743">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="815d6-744">Nur ein Aktionsfilter kann das.</span><span class="sxs-lookup"><span data-stu-id="815d6-744">Only an action filter can do that.</span></span>

<span data-ttu-id="815d6-745">Für Ausnahmefilter gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="815d6-745">Exception filters:</span></span>

* <span data-ttu-id="815d6-746">Sie eignen sich für das Abfangen von Ausnahmen, die in Aktionen auftreten.</span><span class="sxs-lookup"><span data-stu-id="815d6-746">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="815d6-747">Sie sind im Vergleich zu Middleware für die Fehlerbehandlung weniger flexibel.</span><span class="sxs-lookup"><span data-stu-id="815d6-747">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="815d6-748">Sie sollten bei der Ausnahmebehandlung vorzugsweise Middleware verwenden.</span><span class="sxs-lookup"><span data-stu-id="815d6-748">Prefer middleware for exception handling.</span></span> <span data-ttu-id="815d6-749">Verwenden Sie Ausnahmefilter nur, wenn sich die Fehlerbehandlung *unterscheidet*, je nachdem, welche Aktionsmethode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="815d6-749">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="815d6-750">Zum Beispiel verfügt eine App möglicherweise über Aktionsmethoden für beide API-Endpunkte und für Ansichten bzw. HTML.</span><span class="sxs-lookup"><span data-stu-id="815d6-750">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="815d6-751">Die API-Endpunkte können Fehlerinformationen als JSON zurückgeben, während ansichtsbasierte Aktionen eine Fehlerseite als HTML zurückgeben können.</span><span class="sxs-lookup"><span data-stu-id="815d6-751">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="815d6-752">Ergebnisfilter</span><span class="sxs-lookup"><span data-stu-id="815d6-752">Result filters</span></span>

<span data-ttu-id="815d6-753">Für Ergebnisfilter gilt:</span><span class="sxs-lookup"><span data-stu-id="815d6-753">Result filters:</span></span>

* <span data-ttu-id="815d6-754">Implementieren einer Schnittstelle:</span><span class="sxs-lookup"><span data-stu-id="815d6-754">Implement an interface:</span></span>
  * <span data-ttu-id="815d6-755"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="815d6-755"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="815d6-756"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="815d6-756"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="815d6-757">Ihre Ausführung umfasst die Ausführung von Aktionsergebnissen.</span><span class="sxs-lookup"><span data-stu-id="815d6-757">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="815d6-758">IResultFilter und IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="815d6-758">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="815d6-759">Der folgende Code zeigt einen Ergebnisfilter, der einen HTTP-Header hinzufügt:</span><span class="sxs-lookup"><span data-stu-id="815d6-759">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="815d6-760">Die Art des Ergebnisses, das ausgeführt wird, hängt von der jeweiligen Aktion ab.</span><span class="sxs-lookup"><span data-stu-id="815d6-760">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="815d6-761">Eine Aktion, die eine Ansicht zurückgibt, würde die gesamte Razor-Verarbeitung als Teil der Ausführung von <xref:Microsoft.AspNetCore.Mvc.ViewResult> beinhalten.</span><span class="sxs-lookup"><span data-stu-id="815d6-761">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="815d6-762">Eine API-Methode führt möglicherweise eine Art Serialisierung als Teil der Ausführung des Ergebnisses durch.</span><span class="sxs-lookup"><span data-stu-id="815d6-762">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="815d6-763">Erfahren Sie mehr zu [Aktionsergebnissen](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="815d6-763">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="815d6-764">Ergebnisfilter werden nur ausgeführt, wenn eine Aktion oder ein Aktionsfilter ein Aktionsergebnis liefert.</span><span class="sxs-lookup"><span data-stu-id="815d6-764">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="815d6-765">Ergebnisfilter werden in folgenden Fällen nicht ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="815d6-765">Result filters are not executed when:</span></span>

* <span data-ttu-id="815d6-766">Ein Autorisierungs- oder Ressourcenfilter schließt die Pipeline kurz.</span><span class="sxs-lookup"><span data-stu-id="815d6-766">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="815d6-767">Ein Ausnahmefilter behandelt eine Ausnahme durch Erzeugen eines Aktionsergebnisses.</span><span class="sxs-lookup"><span data-stu-id="815d6-767">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="815d6-768">Die Methode <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> kann die Ausführung des Aktionsergebnisses und der nachfolgenden Ergebnisfilter durch Festlegen von <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> auf `true` kurzschließen.</span><span class="sxs-lookup"><span data-stu-id="815d6-768">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="815d6-769">Schreiben Sie beim Kurzschließen in das Antwortobjekt, um zu verhindern, dass eine leere Antwort generiert wird.</span><span class="sxs-lookup"><span data-stu-id="815d6-769">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="815d6-770">Beim Auslösen einer Ausnahme in `IResultFilter.OnResultExecuting` geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="815d6-770">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="815d6-771">Die Ausführung des Aktionsergebnisses und der nachfolgenden Filter wird verhindert.</span><span class="sxs-lookup"><span data-stu-id="815d6-771">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="815d6-772">Die ausgelöste Ausnahme wird nicht als erfolgreiches Ergebnis, sondern als Fehler behandelt.</span><span class="sxs-lookup"><span data-stu-id="815d6-772">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="815d6-773">Wenn die <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>-Methode ausgeführt wird, wurde die Antwort wahrscheinlich bereits an den Client gesendet.</span><span class="sxs-lookup"><span data-stu-id="815d6-773">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="815d6-774">Wenn die Antwort bereits an den Client gesendet wurde, kann sie nicht mehr geändert werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-774">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="815d6-775">`ResultExecutedContext.Canceled` wird auf `true` festgelegt, wenn die Ausführung des Aktionsergebnisses durch einen anderen Filter kurzgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="815d6-775">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="815d6-776">`ResultExecutedContext.Exception` wird auf einen Wert ungleich NULL festgelegt, wenn das Aktionsergebnis oder ein nachfolgender Ergebnisfilter eine Ausnahme ausgelöst hat.</span><span class="sxs-lookup"><span data-stu-id="815d6-776">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="815d6-777">Das Festlegen von `Exception` auf NULL behandelt eine Ausnahme effektiv und verhindert, dass die Ausnahme später in der Pipeline erneut von ASP.NET Core ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="815d6-777">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="815d6-778">Beim Behandeln einer Ausnahme in einem Ergebnisfilter gibt es keine zuverlässige Möglichkeit, Daten in eine Antwort zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="815d6-778">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="815d6-779">Wenn ein Aktionsergebnis eine Ausnahme auslöst und die Header bereits an den Client übergeben wurden, gibt es keinen zuverlässigen Mechanismus, einen Fehlercode zu senden.</span><span class="sxs-lookup"><span data-stu-id="815d6-779">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="815d6-780">Bei einem <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> führt ein Aufruf von `await next` im <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> alle nachfolgenden Ergebnisfilter und das Aktionsergebnis aus.</span><span class="sxs-lookup"><span data-stu-id="815d6-780">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="815d6-781">Zum Kurzschließen legen Sie [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) auf `true` fest und rufen den `ResultExecutionDelegate` nicht auf:</span><span class="sxs-lookup"><span data-stu-id="815d6-781">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="815d6-782">Das Framework stellt ein abstraktes `ResultFilterAttribute` bereit, das als Unterklasse verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="815d6-782">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="815d6-783">Die weiter oben gezeigte Klasse [AddHeaderAttribute](#add-header-attribute) ist ein Beispiel für ein Ergebnisfilterattribut.</span><span class="sxs-lookup"><span data-stu-id="815d6-783">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="815d6-784">IAlwaysRunResultFilter und IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="815d6-784">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="815d6-785">Die Schnittstellen <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> und <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklarieren eine <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter>-Implementierung, die für alle Aktionsergebnisse ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="815d6-785">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="815d6-786">Dies schließt Aktionsergebnisse ein, die von folgenden Filtern generiert werden:</span><span class="sxs-lookup"><span data-stu-id="815d6-786">This includes action results produced by:</span></span>

* <span data-ttu-id="815d6-787">Autorisierungs- und Ressourcenfilter, die einen Kurzschluss bewirken</span><span class="sxs-lookup"><span data-stu-id="815d6-787">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="815d6-788">Ausnahmefilter</span><span class="sxs-lookup"><span data-stu-id="815d6-788">Exception filters.</span></span>

<span data-ttu-id="815d6-789">Beispielsweise wird der folgende Filter immer ausgeführt und legt ein Aktionsergebnis (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) mit dem Statuscode *422 – Einheit kann nicht bearbeitet werden* fest, wenn bei der Inhaltsaushandlung ein Fehler auftritt:</span><span class="sxs-lookup"><span data-stu-id="815d6-789">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="815d6-790">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="815d6-790">IFilterFactory</span></span>

<span data-ttu-id="815d6-791"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementiert <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="815d6-791"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="815d6-792">Deshalb kann eine `IFilterFactory`-Instanz an einer beliebigen Stelle in der Filterpipeline als `IFilterMetadata`-Instanz verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="815d6-792">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="815d6-793">Wenn die Runtime den Aufruf des Filters vorbereitet, versucht sie, ihn in eine `IFilterFactory` umzuwandeln.</span><span class="sxs-lookup"><span data-stu-id="815d6-793">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="815d6-794">Wenn diese Umwandlung gelingt, wird die Methode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> aufgerufen, um die Instanz `IFilterMetadata` für den Aufruf zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="815d6-794">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="815d6-795">Da die exakte Filterpipeline beim Start der Anwendung nicht explizit festgelegt werden muss, wird dadurch ein sehr flexibles Design ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="815d6-795">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="815d6-796">Als weiteres Verfahren zum Erstellen von Filtern kann `IFilterFactory` mithilfe von benutzerdefinierten Attributimplementierungen implementiert werden:</span><span class="sxs-lookup"><span data-stu-id="815d6-796">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="815d6-797">Der oben stehende Code kann durch Ausführen des [Downloadbeispiels](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) getestet werden:</span><span class="sxs-lookup"><span data-stu-id="815d6-797">The preceding code can be tested by running the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="815d6-798">Rufen Sie die F12-Entwicklungstools auf.</span><span class="sxs-lookup"><span data-stu-id="815d6-798">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="815d6-799">Navigieren Sie zu `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="815d6-799">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="815d6-800">Die F12-Entwicklungstools zeigen folgende Antwortheader an, die vom Beispielcode hinzugefügt wurden:</span><span class="sxs-lookup"><span data-stu-id="815d6-800">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="815d6-801">**author:** `Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="815d6-801">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="815d6-802">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="815d6-802">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="815d6-803">**internal:** `My header`</span><span class="sxs-lookup"><span data-stu-id="815d6-803">**internal:** `My header`</span></span>

<span data-ttu-id="815d6-804">Der oben stehende Code erstellt den Antwortheader „**internal:** `My header`“.</span><span class="sxs-lookup"><span data-stu-id="815d6-804">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="815d6-805">In einem Attribut implementierte IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="815d6-805">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="815d6-806">Filter, die `IFilterFactory` implementieren, eignen sich in folgenden Fällen:</span><span class="sxs-lookup"><span data-stu-id="815d6-806">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="815d6-807">Sie erfordern kein Übergeben von Parametern.</span><span class="sxs-lookup"><span data-stu-id="815d6-807">Don't require passing parameters.</span></span>
* <span data-ttu-id="815d6-808">Sie verfügen über Konstruktorabhängigkeiten, die durch DI erfüllt werden müssen.</span><span class="sxs-lookup"><span data-stu-id="815d6-808">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="815d6-809"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementiert <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="815d6-809"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="815d6-810">`IFilterFactory` macht die <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*>-Methode zum Erstellen einer <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>-Instanz verfügbar.</span><span class="sxs-lookup"><span data-stu-id="815d6-810">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="815d6-811">`CreateInstance` lädt den angegebenen Typ aus dem Dienstecontainer (DI).</span><span class="sxs-lookup"><span data-stu-id="815d6-811">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="815d6-812">Der folgende Code zeigt drei Ansätze zum Anwenden von `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="815d6-812">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="815d6-813">Im oben stehenden Code ist das Ergänzen der Methode um `[SampleActionFilter]` der bevorzugte Ansatz zum Anwenden von `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="815d6-813">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="815d6-814">Verwenden von Middleware in der Filterpipeline</span><span class="sxs-lookup"><span data-stu-id="815d6-814">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="815d6-815">Ressourcenfilter funktionieren wie [Middleware](xref:fundamentals/middleware/index), sie umschließen alle Ausführungen, die später in der Pipeline enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="815d6-815">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="815d6-816">Filter unterscheiden sich jedoch in der Hinsicht von Middleware, dass sie Teil der ASP.NET Core-Runtime sind, was bedeutet, dass sie Zugriff auf ASP.NET Core-Kontext und -Konstrukte haben.</span><span class="sxs-lookup"><span data-stu-id="815d6-816">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="815d6-817">Um Middleware als Filter zu verwenden, erstellen Sie einen Typ mit einer `Configure`-Methode, die die Middleware festlegt, die in die Filterpipeline eingefügt werden soll.</span><span class="sxs-lookup"><span data-stu-id="815d6-817">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="815d6-818">Das folgende Beispiel verwendet die Lokalisierungsmiddleware, um die aktuelle Kultur einer Anforderung einzurichten:</span><span class="sxs-lookup"><span data-stu-id="815d6-818">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="815d6-819">Verwenden Sie das <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> zum Ausführen der Middleware:</span><span class="sxs-lookup"><span data-stu-id="815d6-819">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="815d6-820">Middlewarefilter werden zum selben Zeitpunkt in der Filterpipeline wie Ressourcenfilter, vor der Modellbindung und nach dem Rest der Pipeline ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="815d6-820">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="815d6-821">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="815d6-821">Next actions</span></span>

* <span data-ttu-id="815d6-822">Siehe [Filtermethoden für Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="815d6-822">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="815d6-823">Um Filter näher kennenzulernen, [laden Sie das GitHub-Beispiel herunter, und testen und bearbeiten Sie es](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="815d6-823">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end

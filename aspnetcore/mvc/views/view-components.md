---
title: Ansichtskomponenten in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Ansichtskomponenten in ASP.NET Core verwendet werden und wie sie Apps hinzugefügt werden.
ms.author: riande
ms.custom: mvc
ms.date: 5/14/2019
uid: mvc/views/view-components
ms.openlocfilehash: 17fd7aa977868d522df9f27e0c23d07b016bfb7c
ms.sourcegitcommit: 3ee6ee0051c3d2c8d47a58cb17eef1a84a4c46a0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65621073"
---
# <a name="view-components-in-aspnet-core"></a><span data-ttu-id="fb71f-103">Ansichtskomponenten in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fb71f-103">View components in ASP.NET Core</span></span>

<span data-ttu-id="fb71f-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fb71f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="fb71f-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fb71f-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="view-components"></a><span data-ttu-id="fb71f-106">Ansichtskomponenten</span><span class="sxs-lookup"><span data-stu-id="fb71f-106">View components</span></span>

<span data-ttu-id="fb71f-107">Ansichtskomponenten ähneln zwar den Teilansichten, sind aber wesentlich leistungsstärker.</span><span class="sxs-lookup"><span data-stu-id="fb71f-107">View components are similar to partial views, but they're much more powerful.</span></span> <span data-ttu-id="fb71f-108">Ansichtskomponenten verwenden keine Modellbindungen und sind nur von den Daten abhängig, die bei ihrem Aufruf bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="fb71f-108">View components don't use model binding, and only depend on the data provided when calling into it.</span></span> <span data-ttu-id="fb71f-109">Beim Schreiben dieses Artikels wurden Controller und Ansichten verwendet, aber Ansichtskomponenten funktionieren auch zusammen mit Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="fb71f-109">This article was written using controllers and views, but view components also work with Razor Pages.</span></span>

<span data-ttu-id="fb71f-110">Eine Ansichtskomponente:</span><span class="sxs-lookup"><span data-stu-id="fb71f-110">A view component:</span></span>

* <span data-ttu-id="fb71f-111">Rendert nur einen Block statt einer gesamten Antwort.</span><span class="sxs-lookup"><span data-stu-id="fb71f-111">Renders a chunk rather than a whole response.</span></span>
* <span data-ttu-id="fb71f-112">Umfasst die gleiche Trennung von Belangen und Vorzüge der Testbarkeit, die auch zwischen einem Controller und einer Ansicht bestehen.</span><span class="sxs-lookup"><span data-stu-id="fb71f-112">Includes the same separation-of-concerns and testability benefits found between a controller and view.</span></span>
* <span data-ttu-id="fb71f-113">Kann Parameter und Geschäftslogik aufweisen.</span><span class="sxs-lookup"><span data-stu-id="fb71f-113">Can have parameters and business logic.</span></span>
* <span data-ttu-id="fb71f-114">Wird normalerweise von einer Layoutseite aus aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="fb71f-114">Is typically invoked from a layout page.</span></span>

<span data-ttu-id="fb71f-115">Ansichtskomponenten wurden für wiederverwendbare Renderinglogik entwickelt, die für eine Teilansicht zu komplex ist. Dazu gehören:</span><span class="sxs-lookup"><span data-stu-id="fb71f-115">View components are intended anywhere you have reusable rendering logic that's too complex for a partial view, such as:</span></span>

* <span data-ttu-id="fb71f-116">Dynamische Navigationsmenüs</span><span class="sxs-lookup"><span data-stu-id="fb71f-116">Dynamic navigation menus</span></span>
* <span data-ttu-id="fb71f-117">Tag Cloud (dort, wo die Datenbank abgefragt wird)</span><span class="sxs-lookup"><span data-stu-id="fb71f-117">Tag cloud (where it queries the database)</span></span>
* <span data-ttu-id="fb71f-118">ein Anmeldebereich</span><span class="sxs-lookup"><span data-stu-id="fb71f-118">Login panel</span></span>
* <span data-ttu-id="fb71f-119">Einkaufswagen</span><span class="sxs-lookup"><span data-stu-id="fb71f-119">Shopping cart</span></span>
* <span data-ttu-id="fb71f-120">vor Kurzem veröffentlichte Artikel</span><span class="sxs-lookup"><span data-stu-id="fb71f-120">Recently published articles</span></span>
* <span data-ttu-id="fb71f-121">Inhalt in einer Seitenleiste auf einem klassischen Blog</span><span class="sxs-lookup"><span data-stu-id="fb71f-121">Sidebar content on a typical blog</span></span>
* <span data-ttu-id="fb71f-122">Ein Anmeldebereich, der auf jeder Seite gerendert wird und der die Links zum Abmelden bzw. Anmelden anzeigt, je nachdem, ob der Benutzer an- oder abgemeldet ist</span><span class="sxs-lookup"><span data-stu-id="fb71f-122">A login panel that would be rendered on every page and show either the links to log out or log in, depending on the log in state of the user</span></span>

<span data-ttu-id="fb71f-123">Eine Ansichtskomponenten besteht aus zwei Teilen: der Klasse (normalerweise von [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent) abgeleitet) und dem von dieser Klasse zurückgegebenen Ergebnis (normalerweise eine Ansicht).</span><span class="sxs-lookup"><span data-stu-id="fb71f-123">A view component consists of two parts: the class (typically derived from [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)) and the result it returns (typically a view).</span></span> <span data-ttu-id="fb71f-124">Eine Ansichtskomponente kann, ähnlich wie Controller, ein POCO sein. Die meisten Entwickler sollten jedoch von den Methoden und Eigenschaften, die von `ViewComponent` abgeleitet werden, Gebrauch machen.</span><span class="sxs-lookup"><span data-stu-id="fb71f-124">Like controllers, a view component can be a POCO, but most developers will want to take advantage of the methods and properties available by deriving from `ViewComponent`.</span></span>

<span data-ttu-id="fb71f-125">Bei der Überlegung, ob Ansichtskomponenten den Spezifikationen einer App entsprechen, sollten Sie stattdessen Razor Components in Betracht ziehen.</span><span class="sxs-lookup"><span data-stu-id="fb71f-125">When considering if view components meet an app's specifications, consider using Razor Components instead.</span></span> <span data-ttu-id="fb71f-126">Razor Components kombinieren zudem Markup mit C#-Code, um wiederverwendbare UI-Einheiten zu erzeugen.</span><span class="sxs-lookup"><span data-stu-id="fb71f-126">Razor Components also combine markup with C# code to produce reusable UI units.</span></span> <span data-ttu-id="fb71f-127">Razor Components wurden für Entwicklerproduktivität bei der Bereitstellung von clientseitiger Benutzeroberflächenlogik und -gestaltung konzipiert.</span><span class="sxs-lookup"><span data-stu-id="fb71f-127">Razor Components are designed for developer productivity when providing client-side UI logic and composition.</span></span> <span data-ttu-id="fb71f-128">Weitere Informationen finden Sie unter <xref:blazor/components>.</span><span class="sxs-lookup"><span data-stu-id="fb71f-128">For more information, see <xref:blazor/components>.</span></span>

## <a name="creating-a-view-component"></a><span data-ttu-id="fb71f-129">Erstellen einer Ansichtskomponente</span><span class="sxs-lookup"><span data-stu-id="fb71f-129">Creating a view component</span></span>

<span data-ttu-id="fb71f-130">In diesem Abschnitt werden die allgemeinen Anforderungen zum Erstellen einer Ansichtskomponente beschrieben.</span><span class="sxs-lookup"><span data-stu-id="fb71f-130">This section contains the high-level requirements to create a view component.</span></span> <span data-ttu-id="fb71f-131">Im Folgenden wird jeder Schritt ausführlich betrachtet, und Sie erstellen im Zuge dessen eine Ansichtskomponente.</span><span class="sxs-lookup"><span data-stu-id="fb71f-131">Later in the article, we'll examine each step in detail and create a view component.</span></span>

### <a name="the-view-component-class"></a><span data-ttu-id="fb71f-132">Die Ansichtskomponentenklasse</span><span class="sxs-lookup"><span data-stu-id="fb71f-132">The view component class</span></span>

<span data-ttu-id="fb71f-133">Eine Ansichtskomponentenklasse kann durch folgende Aktionen erstellt werden:</span><span class="sxs-lookup"><span data-stu-id="fb71f-133">A view component class can be created by any of the following:</span></span>

* <span data-ttu-id="fb71f-134">durch die Ableitung von *ViewComponent*</span><span class="sxs-lookup"><span data-stu-id="fb71f-134">Deriving from *ViewComponent*</span></span>
* <span data-ttu-id="fb71f-135">durch Ergänzen der Klasse mit dem Attribut `[ViewComponent]` oder durch das Ableiten von einer Klasse mit dem Attribut `[ViewComponent]`</span><span class="sxs-lookup"><span data-stu-id="fb71f-135">Decorating a class with the `[ViewComponent]` attribute, or deriving from a class with the `[ViewComponent]` attribute</span></span>
* <span data-ttu-id="fb71f-136">durch das Erstellen einer Klasse, deren Name mit dem Suffix *ViewComponent* endet</span><span class="sxs-lookup"><span data-stu-id="fb71f-136">Creating a class where the name ends with the suffix *ViewComponent*</span></span>

<span data-ttu-id="fb71f-137">Ansichtskomponenten müssen, genauso wie Controller, öffentliche, unverschachtelt und nicht abstrakte Klassen sein.</span><span class="sxs-lookup"><span data-stu-id="fb71f-137">Like controllers, view components must be public, non-nested, and non-abstract classes.</span></span> <span data-ttu-id="fb71f-138">Der Ansichtskomponentenname ist der Klassenname ohne den Suffix „ViewComponent“.</span><span class="sxs-lookup"><span data-stu-id="fb71f-138">The view component name is the class name with the "ViewComponent" suffix removed.</span></span> <span data-ttu-id="fb71f-139">Er kann zudem explizit mit der Eigenschaft `ViewComponentAttribute.Name` angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="fb71f-139">It can also be explicitly specified using the `ViewComponentAttribute.Name` property.</span></span>

<span data-ttu-id="fb71f-140">Eine Ansichtskomponentenklasse:</span><span class="sxs-lookup"><span data-stu-id="fb71f-140">A view component class:</span></span>

* <span data-ttu-id="fb71f-141">Unterstützt [Constructor Dependency Injection](../../fundamentals/dependency-injection.md) vollständig</span><span class="sxs-lookup"><span data-stu-id="fb71f-141">Fully supports constructor [dependency injection](../../fundamentals/dependency-injection.md)</span></span>

* <span data-ttu-id="fb71f-142">Ist nicht Bestandteil des Controllerlebenszyklus. Dies bedeutet, dass Sie keine [Filter](../controllers/filters.md) in Ansichtskomponenten verwenden können.</span><span class="sxs-lookup"><span data-stu-id="fb71f-142">Doesn't take part in the controller lifecycle, which means you can't use [filters](../controllers/filters.md) in a view component</span></span>

### <a name="view-component-methods"></a><span data-ttu-id="fb71f-143">Ansichtskomponentenmethoden</span><span class="sxs-lookup"><span data-stu-id="fb71f-143">View component methods</span></span>

<span data-ttu-id="fb71f-144">Eine Ansichtskomponente definiert ihre Logik in einer `InvokeAsync`-Methode, die ein `Task<IViewComponentResult>` zurückgibt, oder in einer synchronen `Invoke`-Methode, die ein `IViewComponentResult` zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="fb71f-144">A view component defines its logic in an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or in a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span> <span data-ttu-id="fb71f-145">Parameter stammen direkt vom Aufruf der Ansichtskomponente und nicht von der Modellbindung.</span><span class="sxs-lookup"><span data-stu-id="fb71f-145">Parameters come directly from invocation of the view component, not from model binding.</span></span> <span data-ttu-id="fb71f-146">Eine Ansichtskomponente behandelt nie direkt eine Anfrage.</span><span class="sxs-lookup"><span data-stu-id="fb71f-146">A view component never directly handles a request.</span></span> <span data-ttu-id="fb71f-147">Normalerweise initialisiert eine Ansichtskomponente ein Modell und übergibt dieses an eine Ansicht, indem sie die `View`-Methode aufruft.</span><span class="sxs-lookup"><span data-stu-id="fb71f-147">Typically, a view component initializes a model and passes it to a view by calling the `View` method.</span></span> <span data-ttu-id="fb71f-148">Zusammengefasst bedeutet dies für Komponentenmethoden Folgendes:</span><span class="sxs-lookup"><span data-stu-id="fb71f-148">In summary, view component methods:</span></span>

* <span data-ttu-id="fb71f-149">Es wird eine `InvokeAsync`-Methode definiert, die ein `Task<IViewComponentResult>` zurückgibt, oder eine synchrone `Invoke`-Methode, die ein `IViewComponentResult` zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="fb71f-149">Define an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span>
* <span data-ttu-id="fb71f-150">Normalerweise initialisieren die Methoden ein Modell und übergeben dieses durch Aufrufen der `ViewComponent`-Methode `View` an eine Ansicht.</span><span class="sxs-lookup"><span data-stu-id="fb71f-150">Typically initializes a model and passes it to a view by calling the `ViewComponent` `View` method.</span></span>
* <span data-ttu-id="fb71f-151">Parameter stammen aus der aufrufenden Methode, nicht aus HTTP.</span><span class="sxs-lookup"><span data-stu-id="fb71f-151">Parameters come from the calling method, not HTTP.</span></span> <span data-ttu-id="fb71f-152">Es gibt keine Modellbindung.</span><span class="sxs-lookup"><span data-stu-id="fb71f-152">There's no model binding.</span></span>
* <span data-ttu-id="fb71f-153">Die Methoden sind nicht direkt als HTTP-Endpunkt erreichbar.</span><span class="sxs-lookup"><span data-stu-id="fb71f-153">Are not reachable directly as an HTTP endpoint.</span></span> <span data-ttu-id="fb71f-154">Sie werden von Ihrem Code aufgerufen (üblicherweise in einer Ansicht).</span><span class="sxs-lookup"><span data-stu-id="fb71f-154">They're invoked from your code (usually in a view).</span></span> <span data-ttu-id="fb71f-155">Eine Ansichtskomponente verarbeitet nie eine Anforderung.</span><span class="sxs-lookup"><span data-stu-id="fb71f-155">A view component never handles a request.</span></span>
* <span data-ttu-id="fb71f-156">Methoden werden in der Signatur überladen, nicht in Details der aktuellen HTTP-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="fb71f-156">Are overloaded on the signature rather than any details from the current HTTP request.</span></span>

### <a name="view-search-path"></a><span data-ttu-id="fb71f-157">Anzeigen des Suchpfads</span><span class="sxs-lookup"><span data-stu-id="fb71f-157">View search path</span></span>

<span data-ttu-id="fb71f-158">Die Runtime sucht in den folgenden Pfaden nach der Ansicht:</span><span class="sxs-lookup"><span data-stu-id="fb71f-158">The runtime searches for the view in the following paths:</span></span>

* <span data-ttu-id="fb71f-159">/Views/{Controllername} /Components/{Ansichtskomponentenname}/{Ansichtsname}</span><span class="sxs-lookup"><span data-stu-id="fb71f-159">/Views/{Controller Name}/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="fb71f-160">/Views/Shared/Components/{Ansichtskomponentenname}/{Ansichtsname}</span><span class="sxs-lookup"><span data-stu-id="fb71f-160">/Views/Shared/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="fb71f-161">/Pages/Shared/Components/{Ansichtskomponentenname}/{Ansichtsname}</span><span class="sxs-lookup"><span data-stu-id="fb71f-161">/Pages/Shared/Components/{View Component Name}/{View Name}</span></span>

<span data-ttu-id="fb71f-162">Der Suchpfad gilt für Projekte, die Controller und Ansichten sowie Razor Pages verwenden.</span><span class="sxs-lookup"><span data-stu-id="fb71f-162">The search path applies to projects using controllers + views and Razor Pages.</span></span>

<span data-ttu-id="fb71f-163">Der Standardansichtsname für die Ansichtskomponente ist *Default*. Dies bedeutet, dass Ihre Ansichtsdatei normalerweise *Default.cshtml* heißt.</span><span class="sxs-lookup"><span data-stu-id="fb71f-163">The default view name for a view component is *Default*, which means your view file will typically be named *Default.cshtml*.</span></span> <span data-ttu-id="fb71f-164">Sie können einen anderen Ansichtsnamen angeben, wenn Sie die Ansichtskomponentenergebnisse erstellen oder die `View`-Methode aufrufen.</span><span class="sxs-lookup"><span data-stu-id="fb71f-164">You can specify a different view name when creating the view component result or when calling the `View` method.</span></span>

<span data-ttu-id="fb71f-165">Es wird empfohlen, dass Sie die Ansichtsdatei *Default.cshtml* nennen und den Pfad *View/Shared/Components/{Ansichtskomponentenname}/{Ansichtsname}* verwenden.</span><span class="sxs-lookup"><span data-stu-id="fb71f-165">We recommend you name the view file *Default.cshtml* and use the *Views/Shared/Components/{View Component Name}/{View Name}* path.</span></span> <span data-ttu-id="fb71f-166">Die Ansichtskomponente `PriorityList`, die in diesem Beispiel verwendet wird, verwendet *Views/Shared/Components/PriorityList/Default.cshtml* für die Ansichtskomponentenansicht.</span><span class="sxs-lookup"><span data-stu-id="fb71f-166">The `PriorityList` view component used in this sample uses *Views/Shared/Components/PriorityList/Default.cshtml* for the view component view.</span></span>

## <a name="invoking-a-view-component"></a><span data-ttu-id="fb71f-167">Aufrufen einer Ansichtskomponente</span><span class="sxs-lookup"><span data-stu-id="fb71f-167">Invoking a view component</span></span>

<span data-ttu-id="fb71f-168">Rufen Sie Folgendes in der Ansicht auf, wenn Sie die Ansichtskomponente verwenden möchten:</span><span class="sxs-lookup"><span data-stu-id="fb71f-168">To use the view component, call the following inside a view:</span></span>

```cshtml
@await Component.InvokeAsync("Name of view component", {Anonymous Type Containing Parameters})
```

<span data-ttu-id="fb71f-169">Die Parameter werden an die `InvokeAsync`-Methode übergeben.</span><span class="sxs-lookup"><span data-stu-id="fb71f-169">The parameters will be passed to the `InvokeAsync` method.</span></span> <span data-ttu-id="fb71f-170">Die Ansichtskomponente `PriorityList`, die im Artikel entwickelt wurde, wird von der Ansichtsdatei *Views/ToDo/Index.cshtml* aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="fb71f-170">The `PriorityList` view component developed in the article is invoked from the *Views/ToDo/Index.cshtml* view file.</span></span> <span data-ttu-id="fb71f-171">Im folgenden Code wird die `InvokeAsync`-Methode mit zwei Parametern aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="fb71f-171">In the following, the `InvokeAsync` method is called with two parameters:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

::: moniker range=">= aspnetcore-1.1"

## <a name="invoking-a-view-component-as-a-tag-helper"></a><span data-ttu-id="fb71f-172">Aufrufen einer Ansichtskomponente als Taghilfsprogramm</span><span class="sxs-lookup"><span data-stu-id="fb71f-172">Invoking a view component as a Tag Helper</span></span>

<span data-ttu-id="fb71f-173">In ASP.NET Core 1.1 und höher können Sie eine Ansichtskomponente als [Taghilfsprogramm](xref:mvc/views/tag-helpers/intro) aufrufen.</span><span class="sxs-lookup"><span data-stu-id="fb71f-173">For ASP.NET Core 1.1 and higher, you can invoke a view component as a [Tag Helper](xref:mvc/views/tag-helpers/intro):</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="fb71f-174">Namen von Klassen und Methodenparameter für Taghilfsprogramme, die in Pascal-Schreibweise angegeben sind, werden in [Kebab-Schreibweise](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101) übersetzt.</span><span class="sxs-lookup"><span data-stu-id="fb71f-174">Pascal-cased class and method parameters for Tag Helpers are translated into their [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="fb71f-175">Das Taghilfsprogramm zum Aufrufen einer Ansichtskomponente verwendet das `<vc></vc>`-Element.</span><span class="sxs-lookup"><span data-stu-id="fb71f-175">The Tag Helper to invoke a view component uses the `<vc></vc>` element.</span></span> <span data-ttu-id="fb71f-176">Die Ansichtskomponente wird wie folgt angegeben:</span><span class="sxs-lookup"><span data-stu-id="fb71f-176">The view component is specified as follows:</span></span>

```cshtml
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

<span data-ttu-id="fb71f-177">Damit Sie eine Ansichtskomponente als Taghilfsprogramm verwenden können, registrieren Sie die Assembly, die die Ansichtskomponente enthält, mit der `@addTagHelper`-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="fb71f-177">To use a view component as a Tag Helper, register the assembly containing the view component using the `@addTagHelper` directive.</span></span> <span data-ttu-id="fb71f-178">Wenn Ihre Ansichtskomponente eine Assembly mit dem Namen `MyWebApp` ist, fügen Sie die folgende Anweisung zu der Datei *_ViewImports.cshtml* hinzu:</span><span class="sxs-lookup"><span data-stu-id="fb71f-178">If your view component is in an assembly called `MyWebApp`, add the following directive to the *_ViewImports.cshtml* file:</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="fb71f-179">Sie können eine Ansichtskomponente als Taghilfsprogramm für jede Datei registrieren, die auf die Ansichtskomponente verweist.</span><span class="sxs-lookup"><span data-stu-id="fb71f-179">You can register a view component as a Tag Helper to any file that references the view component.</span></span> <span data-ttu-id="fb71f-180">Weitere Informationen zum Registrieren eines Taghilfsprogramms finden Sie unter [Managing Tag Helper Scope (Verwalten des Bereichs des Taghilfsprogramms)](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope).</span><span class="sxs-lookup"><span data-stu-id="fb71f-180">See [Managing Tag Helper Scope](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) for more information on how to register Tag Helpers.</span></span>

<span data-ttu-id="fb71f-181">Die `InvokeAsync`-Methode, die in diesem Tutorial verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="fb71f-181">The `InvokeAsync` method used in this tutorial:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="fb71f-182">In Taghilfsprogramm-Markup:</span><span class="sxs-lookup"><span data-stu-id="fb71f-182">In Tag Helper markup:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="fb71f-183">Im Beispiel oben stehenden Beispiel wird die Ansichtskomponente `PriorityList` zu `priority-list`.</span><span class="sxs-lookup"><span data-stu-id="fb71f-183">In the sample above, the `PriorityList` view component becomes `priority-list`.</span></span> <span data-ttu-id="fb71f-184">Die Parameter der Ansichtskomponente werden als Attribute in Kebab-Schreibweise übergeben.</span><span class="sxs-lookup"><span data-stu-id="fb71f-184">The parameters to the view component are passed as attributes in kebab case.</span></span>

::: moniker-end

### <a name="invoking-a-view-component-directly-from-a-controller"></a><span data-ttu-id="fb71f-185">Direkter Aufrufe einer Ansichtskomponente von einem Controller</span><span class="sxs-lookup"><span data-stu-id="fb71f-185">Invoking a view component directly from a controller</span></span>

<span data-ttu-id="fb71f-186">Ansichtskomponenten werden normalerweise von einer Ansicht aus aufgerufen, aber Sie können sie auch direkt von einer Controllermethode aus aufrufen.</span><span class="sxs-lookup"><span data-stu-id="fb71f-186">View components are typically invoked from a view, but you can invoke them directly from a controller method.</span></span> <span data-ttu-id="fb71f-187">Obwohl Ansichtskomponenten keine Endpunkte so wie Controller definieren, können Sie trotzdem eine Controlleraktion implementieren, die den Inhalt von `ViewComponentResult` zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="fb71f-187">While view components don't define endpoints like controllers, you can easily implement a controller action that returns the content of a `ViewComponentResult`.</span></span>

<span data-ttu-id="fb71f-188">In diesem Beispiel wird die Ansichtskomponente direkt von einem Controller aus aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="fb71f-188">In this example, the view component is called directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a><span data-ttu-id="fb71f-189">Exemplarische Vorgehensweise: Erstellen einer einfachen Ansichtskomponente</span><span class="sxs-lookup"><span data-stu-id="fb71f-189">Walkthrough: Creating a simple view component</span></span>

<span data-ttu-id="fb71f-190">[Laden Sie den Startercode herunter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample), und erstellen und testen Sie diesen.</span><span class="sxs-lookup"><span data-stu-id="fb71f-190">[Download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample), build and test the starter code.</span></span> <span data-ttu-id="fb71f-191">Dabei handelt es sich um ein einfaches Projekt mit einem `ToDo`-Controller, in dem eine Liste von *ToDo*-Elementen angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="fb71f-191">It's a simple project with a `ToDo` controller that displays a list of *ToDo* items.</span></span>

![Liste mit ToDo-Elementen](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a><span data-ttu-id="fb71f-193">Hinzufügen einer ViewComponent-Klasse</span><span class="sxs-lookup"><span data-stu-id="fb71f-193">Add a ViewComponent class</span></span>

<span data-ttu-id="fb71f-194">Erstellen Sie einen *ViewComponents*-Ordner, und fügen Sie die folgende `PriorityListViewComponent`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="fb71f-194">Create a *ViewComponents* folder and add the following `PriorityListViewComponent` class:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

<span data-ttu-id="fb71f-195">Bemerkungen zum Code:</span><span class="sxs-lookup"><span data-stu-id="fb71f-195">Notes on the code:</span></span>

* <span data-ttu-id="fb71f-196">Ansichtskomponentenklassen können sich in **jedem** Ordner im Projekt befinden.</span><span class="sxs-lookup"><span data-stu-id="fb71f-196">View component classes can be contained in **any** folder in the project.</span></span>
* <span data-ttu-id="fb71f-197">Da der Klassenname „PriorityList**ViewComponent** mit dem Suffix **ViewComponent** endet, verwendet die Runtime die Zeichenfolge „PriorityList“, wenn Sie von einer Ansicht aus auf die Klassenkomponente verweist.</span><span class="sxs-lookup"><span data-stu-id="fb71f-197">Because the class name PriorityList**ViewComponent** ends with the suffix **ViewComponent**, the runtime will use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="fb71f-198">Dies wird im Folgenden noch ausführlicher erklärt.</span><span class="sxs-lookup"><span data-stu-id="fb71f-198">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="fb71f-199">Das `[ViewComponent]`-Attribut kann den Namen ändern, der zum Verweis auf eine Ansichtskomponente verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="fb71f-199">The `[ViewComponent]` attribute can change the name used to reference a view component.</span></span> <span data-ttu-id="fb71f-200">Wir hätten die Klasse auch `XYZ` nennen und das `ViewComponent`-Attribut anwenden können:</span><span class="sxs-lookup"><span data-stu-id="fb71f-200">For example, we could've named the class `XYZ` and applied the `ViewComponent` attribute:</span></span>

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* <span data-ttu-id="fb71f-201">Das oben stehende `[ViewComponent]`-Attribut teilt dem Ansichtskomponentenselektor mit, den Namen `PriorityList` zu verwenden, wenn er nach den Ansichten sucht, die mit der Komponente verknüpft sind. Zudem wird er informiert, die Zeichenfolge „“ zu verwenden, wenn er von einer Ansicht aus auf die Klassenkomponente verweist.</span><span class="sxs-lookup"><span data-stu-id="fb71f-201">The `[ViewComponent]` attribute above tells the view component selector to use the name `PriorityList` when looking for the views associated with the component, and to use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="fb71f-202">Dies wird im Folgenden noch ausführlicher erklärt.</span><span class="sxs-lookup"><span data-stu-id="fb71f-202">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="fb71f-203">Die Komponente verwendet [Dependency Injection](../../fundamentals/dependency-injection.md), um den Datenkontext verfügbar zu machen.</span><span class="sxs-lookup"><span data-stu-id="fb71f-203">The component uses [dependency injection](../../fundamentals/dependency-injection.md) to make the data context available.</span></span>
* <span data-ttu-id="fb71f-204">`InvokeAsync` macht eine Methode verfügbar, die von einer Ansicht aus aufgerufen werden kann, und akzeptiert eine arbiträre Anzahl von Argumenten.</span><span class="sxs-lookup"><span data-stu-id="fb71f-204">`InvokeAsync` exposes a method which can be called from a view, and it can take an arbitrary number of arguments.</span></span>
* <span data-ttu-id="fb71f-205">Die `InvokeAsync`-Methode gibt mehrere `ToDo`-Elemente zurück, die die Bedingungen der Parameter `isDone` und `maxPriority` erfüllen.</span><span class="sxs-lookup"><span data-stu-id="fb71f-205">The `InvokeAsync` method returns the set of `ToDo` items that satisfy the `isDone` and `maxPriority` parameters.</span></span>

### <a name="create-the-view-component-razor-view"></a><span data-ttu-id="fb71f-206">Erstellen der Razor-Ansicht der Ansichtskomponente</span><span class="sxs-lookup"><span data-stu-id="fb71f-206">Create the view component Razor view</span></span>

* <span data-ttu-id="fb71f-207">Erstellen Sie den Ordner *Views/Shared/Components*.</span><span class="sxs-lookup"><span data-stu-id="fb71f-207">Create the *Views/Shared/Components* folder.</span></span> <span data-ttu-id="fb71f-208">Diese Ordner **muss** den Namen *Components* besitzen.</span><span class="sxs-lookup"><span data-stu-id="fb71f-208">This folder **must** be named *Components*.</span></span>

* <span data-ttu-id="fb71f-209">Erstellen Sie den Ordner *Views/Shared/Components/PriorityList*.</span><span class="sxs-lookup"><span data-stu-id="fb71f-209">Create the *Views/Shared/Components/PriorityList* folder.</span></span> <span data-ttu-id="fb71f-210">Der Ordnername muss mit dem Namen der Ansichtskomponentenklasse oder mit dem Namen der Klasse ohne Suffix (wenn wir uns an die Konvention gehalten und *ViewComponent* als Suffix im Klassennamen verwendet haben) übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="fb71f-210">This folder name must match the name of the view component class, or the name of the class minus the suffix (if we followed convention and used the *ViewComponent* suffix in the class name).</span></span> <span data-ttu-id="fb71f-211">Wenn Sie das Attribut `ViewComponent` verwenden, muss der Klassenname mit der Attributbezeichnung übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="fb71f-211">If you used the `ViewComponent` attribute, the class name would need to match the attribute designation.</span></span>

* <span data-ttu-id="fb71f-212">Erstellen Sie eine *Views/Shared/Components/PriorityList/Default.cshtml*-Razor-Ansicht:</span><span class="sxs-lookup"><span data-stu-id="fb71f-212">Create a *Views/Shared/Components/PriorityList/Default.cshtml* Razor view:</span></span>


  [!code-cshtml[](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]

   <span data-ttu-id="fb71f-213">Die Razor-Ansicht nimmt eine Liste von `TodoItem` an und zeigt diese an.</span><span class="sxs-lookup"><span data-stu-id="fb71f-213">The Razor view takes a list of `TodoItem` and displays them.</span></span> <span data-ttu-id="fb71f-214">Wenn die `InvokeAsync`-Methode der Ansichtskomponente nicht den Namen der Ansicht übergibt (wie in unserem Beispiel), wird *Default* per Konvention für den Ansichtsnamen verwendet.</span><span class="sxs-lookup"><span data-stu-id="fb71f-214">If the view component `InvokeAsync` method doesn't pass the name of the view (as in our sample), *Default* is used for the view name by convention.</span></span> <span data-ttu-id="fb71f-215">Später in diesem Tutorial erfahren Sie, wie Sie den Namen der Ansicht übergeben.</span><span class="sxs-lookup"><span data-stu-id="fb71f-215">Later in the tutorial, I'll show you how to pass the name of the view.</span></span> <span data-ttu-id="fb71f-216">Fügen Sie eine Ansicht einem controllerspezifischen Ansichtsordner hinzu, um das Standardformat für einen spezifischen Controller zu überschreiben (z.B. *Views/ToDo/Components/PriorityList/Default.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="fb71f-216">To override the default styling for a specific controller, add a view to the controller-specific view folder (for example *Views/ToDo/Components/PriorityList/Default.cshtml)*.</span></span>

    <span data-ttu-id="fb71f-217">Wenn die Ansichtskomponente controllerspezifisch ist, können Sie sie dem controllerspezifischen Ordner hinzufügen (*Views/ToDo/Components/PriorityList/Default.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="fb71f-217">If the view component is controller-specific, you can add it to the controller-specific folder (*Views/ToDo/Components/PriorityList/Default.cshtml*).</span></span>

* <span data-ttu-id="fb71f-218">Fügen Sie ein `div`-Objekt, das einen Aufruf an eine Prioritätslistenkomponente enthält, am Ende der Datei *Views/ToDo/index.cshtml* hinzu:</span><span class="sxs-lookup"><span data-stu-id="fb71f-218">Add a `div` containing a call to the priority list component to the bottom of the *Views/ToDo/index.cshtml* file:</span></span>

    [!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFirst.cshtml?range=34-38)]

<span data-ttu-id="fb71f-219">Das Markup `@await Component.InvokeAsync` zeigt die Syntax für die aufrufenden Ansichtskomponenten an.</span><span class="sxs-lookup"><span data-stu-id="fb71f-219">The markup `@await Component.InvokeAsync` shows the syntax for calling view components.</span></span> <span data-ttu-id="fb71f-220">Das erste Argument ist der Name der aufzurufenden Komponente.</span><span class="sxs-lookup"><span data-stu-id="fb71f-220">The first argument is the name of the component we want to invoke or call.</span></span> <span data-ttu-id="fb71f-221">Darauffolgende Parameter werden an die Komponente übergeben.</span><span class="sxs-lookup"><span data-stu-id="fb71f-221">Subsequent parameters are passed to the component.</span></span> <span data-ttu-id="fb71f-222">`InvokeAsync` kann eine arbiträre Anzahl von Argumenten annehmen.</span><span class="sxs-lookup"><span data-stu-id="fb71f-222">`InvokeAsync` can take an arbitrary number of arguments.</span></span>

<span data-ttu-id="fb71f-223">Testen der App</span><span class="sxs-lookup"><span data-stu-id="fb71f-223">Test the app.</span></span> <span data-ttu-id="fb71f-224">In der folgenden Abbildung werden die ToDo-Liste und die Elemente mit Priorität angezeigt:</span><span class="sxs-lookup"><span data-stu-id="fb71f-224">The following image shows the ToDo list and the priority items:</span></span>

![ToDo-Liste und Prioritätselemente](view-components/_static/pi.png)

<span data-ttu-id="fb71f-226">Sie können Sie Ansichtskomponente auch direkt vom Controller aus aufrufen:</span><span class="sxs-lookup"><span data-stu-id="fb71f-226">You can also call the view component directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![Prioritätselemente der IndexVC-Aktion](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a><span data-ttu-id="fb71f-228">Angeben eines Ansichtsnamens</span><span class="sxs-lookup"><span data-stu-id="fb71f-228">Specifying a view name</span></span>

<span data-ttu-id="fb71f-229">Eine komplexe Ansichtskomponente erfordert möglicherweise, dass unter bestimmten Umständen eine Ansicht angegeben wird, die nicht dem Standard entspricht.</span><span class="sxs-lookup"><span data-stu-id="fb71f-229">A complex view component might need to specify a non-default view under some conditions.</span></span> <span data-ttu-id="fb71f-230">Der folgende Code zeigt, wie die Ansicht „PVC“ von der `InvokeAsync`-Methode aus angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="fb71f-230">The following code shows how to specify the "PVC" view  from the `InvokeAsync` method.</span></span> <span data-ttu-id="fb71f-231">Aktualisieren Sie die `InvokeAsync`-Methode in der `PriorityListViewComponent`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="fb71f-231">Update the `InvokeAsync` method in the `PriorityListViewComponent` class.</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

<span data-ttu-id="fb71f-232">Kopieren Sie die Datei *Views/Shared/Components/PriorityList/Default.cshtml* in eine Ansicht mit dem Namen *Views/Shared/Components/PriorityList/PVC.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="fb71f-232">Copy the *Views/Shared/Components/PriorityList/Default.cshtml* file to a view named *Views/Shared/Components/PriorityList/PVC.cshtml*.</span></span> <span data-ttu-id="fb71f-233">Fügen Sie eine Überschrift hinzu, um anzugeben, dass die PVC-Ansicht verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="fb71f-233">Add a heading to indicate the PVC view is being used.</span></span>

[!code-cshtml[](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

<span data-ttu-id="fb71f-234">Aktualisieren Sie *Views/ToDo/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="fb71f-234">Update *Views/ToDo/Index.cshtml*:</span></span>

<!-- Views/ToDo/Index.cshtml is never imported, so change to test tutorial -->

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="fb71f-235">Führen Sie die App aus und überprüfen Sie die PVC-Ansicht.</span><span class="sxs-lookup"><span data-stu-id="fb71f-235">Run the app and verify PVC view.</span></span>

![Ansichtskomponente mit Priorität](view-components/_static/pvc.png)

<span data-ttu-id="fb71f-237">Wenn die PVC-Ansicht nicht gerendert wird, stellen Sie sicher, dass Sie die Ansichtskomponente mit einer Priorität von 4 oder höher aufrufen.</span><span class="sxs-lookup"><span data-stu-id="fb71f-237">If the PVC view isn't rendered, verify you are calling the view component with a priority of 4 or higher.</span></span>

### <a name="examine-the-view-path"></a><span data-ttu-id="fb71f-238">Untersuchen des Ansichtspfads</span><span class="sxs-lookup"><span data-stu-id="fb71f-238">Examine the view path</span></span>

* <span data-ttu-id="fb71f-239">Ändern Sie den Prioritätsparameter in drei oder weniger, damit die Prioritätsansicht nicht zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="fb71f-239">Change the priority parameter to three or less so the priority view isn't returned.</span></span>
* <span data-ttu-id="fb71f-240">Benennen Sie *Views/ToDo/Components/PriorityList/Default.cshtml* vorübergehend in *1Default.cshtml* um.</span><span class="sxs-lookup"><span data-stu-id="fb71f-240">Temporarily rename the *Views/ToDo/Components/PriorityList/Default.cshtml* to *1Default.cshtml*.</span></span>
* <span data-ttu-id="fb71f-241">Wenn Sie die App testen, erhalten Sie die folgende Fehlermeldung:</span><span class="sxs-lookup"><span data-stu-id="fb71f-241">Test the app, you'll get the following error:</span></span>

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' wasn't found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* <span data-ttu-id="fb71f-242">Kopieren Sie *Views/ToDo/Components/PriorityList/1Default.cshtml* nach *Views/Shared/Components/PriorityList/Default.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="fb71f-242">Copy *Views/ToDo/Components/PriorityList/1Default.cshtml* to *Views/Shared/Components/PriorityList/Default.cshtml*.</span></span>
* <span data-ttu-id="fb71f-243">Fügen Sie der ToDo-Ansichtskomponentenansicht *Shared* (Freigegeben) Markup hinzu, um anzugeben, dass die Ansicht aus dem Ordner *Shared* stammt.</span><span class="sxs-lookup"><span data-stu-id="fb71f-243">Add some markup to the *Shared* ToDo view component view to indicate the view is from the *Shared* folder.</span></span>
* <span data-ttu-id="fb71f-244">Testen Sie die Komponentenansicht **Shared** (Freigegeben).</span><span class="sxs-lookup"><span data-stu-id="fb71f-244">Test the **Shared** component view.</span></span>

![ToDo-Ausgabe mit Komponentenansicht „Shared“](view-components/_static/shared.png)

### <a name="avoiding-hard-coded-strings"></a><span data-ttu-id="fb71f-246">Vermeiden hart codierter Zeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="fb71f-246">Avoiding hard-coded strings</span></span>

<span data-ttu-id="fb71f-247">Wenn Sie Sicherheit zu Kompilierzeit haben möchten, können Sie den hart codierten Komponentennamen durch den Klassennamen ersetzen.</span><span class="sxs-lookup"><span data-stu-id="fb71f-247">If you want compile time safety, you can replace the hard-coded view component name with the class name.</span></span> <span data-ttu-id="fb71f-248">Erstellen Sie die Ansichtskomponente ohne den Suffix „ViewComponent“:</span><span class="sxs-lookup"><span data-stu-id="fb71f-248">Create the view component without the "ViewComponent" suffix:</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]

<span data-ttu-id="fb71f-249">Fügen Sie eine `using`-Anweisung zu Ihrer Razor-Ansichtsdatei hinzu, und verwenden Sie den `nameof`-Operator:</span><span class="sxs-lookup"><span data-stu-id="fb71f-249">Add a `using` statement to your Razor view file, and use the `nameof` operator:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexNameof.cshtml?range=1-6,35-)]

## <a name="perform-synchronous-work"></a><span data-ttu-id="fb71f-250">Ausführen synchroner Verarbeitung</span><span class="sxs-lookup"><span data-stu-id="fb71f-250">Perform synchronous work</span></span>

<span data-ttu-id="fb71f-251">Das Framework verarbeitet den Aufruf einer synchronen `Invoke`-Methode, wenn Sie keine asynchrone Verarbeitung durchführen müssen.</span><span class="sxs-lookup"><span data-stu-id="fb71f-251">The framework handles invoking a synchronous `Invoke` method if you don't need to perform asynchronous work.</span></span> <span data-ttu-id="fb71f-252">Die folgende Methode erstellt eine synchrone `Invoke`-Ansichtskomponente:</span><span class="sxs-lookup"><span data-stu-id="fb71f-252">The following method creates a synchronous `Invoke` view component:</span></span>

```csharp
public class PriorityList : ViewComponent
{
    public IViewComponentResult Invoke(int maxPriority, bool isDone)
    {
        var items = new List<string> { $"maxPriority: {maxPriority}", $"isDone: {isDone}" };
        return View(items);
    }
}
```

<span data-ttu-id="fb71f-253">Die Razor-Datei der Ansichtskomponente listet die Zeichenfolgen auf, die an die `Invoke`-Methode übergeben wurden (*Views/Home/Components/PriorityList/Default.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="fb71f-253">The view component's Razor file lists the strings passed to the `Invoke` method (*Views/Home/Components/PriorityList/Default.cshtml*):</span></span>

```cshtml
@model List<string>

<h3>Priority Items</h3>
<ul>
    @foreach (var item in Model)
    {
        <li>@item</li>
    }
</ul>
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="fb71f-254">Die Ansichtskomponente wird in einer Razor-Datei (z.B. *Views/Home/Index.cshtml*) mit einem der folgenden Verfahren aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="fb71f-254">The view component is invoked in a Razor file (for example, *Views/Home/Index.cshtml*) using one of the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>
* [<span data-ttu-id="fb71f-255">Taghilfsprogramm</span><span class="sxs-lookup"><span data-stu-id="fb71f-255">Tag Helper</span></span>](xref:mvc/views/tag-helpers/intro)

<span data-ttu-id="fb71f-256">Rufen Sie `Component.InvokeAsync` auf, um das <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>-Verfahren zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="fb71f-256">To use the <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> approach, call `Component.InvokeAsync`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-1.1"

<span data-ttu-id="fb71f-257">Die Ansichtskomponente wird in einer Razor-Datei (z.B. *Views/Home/Index.cshtml*) mit <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="fb71f-257">The view component is invoked in a Razor file (for example, *Views/Home/Index.cshtml*) with <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>.</span></span>

<span data-ttu-id="fb71f-258">Rufen Sie `Component.InvokeAsync` auf:</span><span class="sxs-lookup"><span data-stu-id="fb71f-258">Call `Component.InvokeAsync`:</span></span>

::: moniker-end

```cshtml
@await Component.InvokeAsync(nameof(PriorityList), new { maxPriority = 4, isDone = true })
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="fb71f-259">Um das Taghilfsprogramm zu verwenden, registrieren Sie die Assembly, die die Ansichtskomponente enthält, mit der Anweisung `@addTagHelper` (die Ansichtskomponente befindet sich in einer Assembly namens `MyWebApp`):</span><span class="sxs-lookup"><span data-stu-id="fb71f-259">To use the Tag Helper, register the assembly containing the View Component using the `@addTagHelper` directive (the view component is in an assembly called `MyWebApp`):</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="fb71f-260">Verwenden Sie das Taghilfsprogramm der Ansichtskomponente in der Razor-Markupdatei:</span><span class="sxs-lookup"><span data-stu-id="fb71f-260">Use the view component Tag Helper in the Razor markup file:</span></span>

```cshtml
<vc:priority-list max-priority="999" is-done="false">
</vc:priority-list>
```

::: moniker-end

<span data-ttu-id="fb71f-261">Die Methodensignatur von `PriorityList.Invoke` ist synchron, aber Razor sucht nach der Methode und ruft die Methode mit `Component.InvokeAsync` in der Markupdatei auf.</span><span class="sxs-lookup"><span data-stu-id="fb71f-261">The method signature of `PriorityList.Invoke` is synchronous, but Razor finds and calls the method with `Component.InvokeAsync` in the markup file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fb71f-262">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="fb71f-262">Additional resources</span></span>

* [<span data-ttu-id="fb71f-263">Abhängigkeitsinjektion in Ansichten</span><span class="sxs-lookup"><span data-stu-id="fb71f-263">Dependency injection into views</span></span>](xref:mvc/views/dependency-injection)

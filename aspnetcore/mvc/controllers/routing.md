---
title: Routing zu Controlleraktionen in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie ASP.NET Core MVC Routingmiddleware verwendet, um die URLs der eingehenden Anforderungen abzugleichen und sie Aktionen zuzuordnen.
ms.author: riande
ms.date: 3/25/2020
uid: mvc/controllers/routing
ms.openlocfilehash: be7da9eeaf64c2f52c095b5179ccc22db43d57c3
ms.sourcegitcommit: 99e71ae03319ab386baf2ebde956fc2d511df8b8
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/25/2020
ms.locfileid: "80242573"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="a45e6-103">Routing zu Controlleraktionen in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a45e6-103">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="a45e6-104">Von [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5)und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a45e6-104">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a45e6-105">ASP.net Core Controller verwenden die Routing [Middleware](xref:fundamentals/middleware/index) , um die URLs eingehender Anforderungen abzugleichen und Sie [Aktionen](#action)zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-105">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="a45e6-106">Routen Vorlagen:</span><span class="sxs-lookup"><span data-stu-id="a45e6-106">Routes templates:</span></span>

* <span data-ttu-id="a45e6-107">Werden in Startcode oder Attributen definiert.</span><span class="sxs-lookup"><span data-stu-id="a45e6-107">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="a45e6-108">Beschreiben Sie, wie URL-Pfade mit [Aktionen](#action)abgeglichen werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-108">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="a45e6-109">Werden verwendet, um URLs für Verknüpfungen zu generieren.</span><span class="sxs-lookup"><span data-stu-id="a45e6-109">Are used to generate URLs for links.</span></span> <span data-ttu-id="a45e6-110">Die generierten Verknüpfungen werden in der Regel in Antworten zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="a45e6-110">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="a45e6-111">Aktionen werden entweder [konventionell](#cr) oder [Attribut](#ar)weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-111">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="a45e6-112">Durch das Platzieren einer Route auf dem Controller oder der [Aktion](#action) wird das Attribut geroutet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-112">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="a45e6-113">Weitere Informationen finden Sie im Abschnitt [Gemischtes Routing](#routing-mixed-ref-label).</span><span class="sxs-lookup"><span data-stu-id="a45e6-113">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="a45e6-114">Dieses Dokument:</span><span class="sxs-lookup"><span data-stu-id="a45e6-114">This document:</span></span>

* <span data-ttu-id="a45e6-115">Erläutert die Interaktionen zwischen MVC und Routing:</span><span class="sxs-lookup"><span data-stu-id="a45e6-115">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="a45e6-116">Verwendung von Routing Features durch typische MVC-apps</span><span class="sxs-lookup"><span data-stu-id="a45e6-116">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="a45e6-117">Umfasst beides:</span><span class="sxs-lookup"><span data-stu-id="a45e6-117">Covers both:</span></span>
    * <span data-ttu-id="a45e6-118">[Konventionell Routing](#cr) , das in der Regel für Controller und Ansichten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-118">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="a45e6-119">*Attribut Routing* , das mit Rest-APIs verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-119">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="a45e6-120">Wenn Sie hauptsächlich an der Weiterleitung von Rest-APIs interessiert sind, springen Sie zum Abschnitt [Attribut Routing für Rest-APIs](#ar) .</span><span class="sxs-lookup"><span data-stu-id="a45e6-120">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="a45e6-121">Weitere Informationen finden Sie unter [Routing](xref:fundamentals/routing) für erweiterte Routing Details.</span><span class="sxs-lookup"><span data-stu-id="a45e6-121">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="a45e6-122">Bezieht sich auf das Standard Routing System, das in ASP.net Core 3,0 hinzugefügt wurde, dem sogenannten Endpunkt Routing.</span><span class="sxs-lookup"><span data-stu-id="a45e6-122">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="a45e6-123">Es ist möglich, Controller mit der vorherigen Version des Routings zu Kompatibilitätszwecken zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-123">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="a45e6-124">Anweisungen hierzu finden Sie im [2.2-3.0-Migrations Handbuch](xref:migration/22-to-30) .</span><span class="sxs-lookup"><span data-stu-id="a45e6-124">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="a45e6-125">Das Referenzmaterial zum Legacy Routing System finden Sie in der [Version 2,2 dieses Dokuments](xref:mvc/controllers/routing?view=aspnetcore-2.2) .</span><span class="sxs-lookup"><span data-stu-id="a45e6-125">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="a45e6-126">Einrichten der herkömmlichen Route</span><span class="sxs-lookup"><span data-stu-id="a45e6-126">Set up conventional route</span></span>

<span data-ttu-id="a45e6-127">`Startup.Configure` in der Regel einen Code ähnlich dem folgenden, wenn [herkömmliches Routing](#crd)verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="a45e6-127">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="a45e6-128">Innerhalb des Aufrufes <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>wird <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> verwendet, um eine einzelne Route zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-128">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> is used to create a single route.</span></span> <span data-ttu-id="a45e6-129">Die einzelne Route wird `default` Route benannt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-129">The single route is named `default` route.</span></span> <span data-ttu-id="a45e6-130">Bei den meisten apps mit Controllern und Ansichten wird eine Routen Vorlage ähnlich der `default` Route verwendet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-130">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="a45e6-131">Rest-APIs sollten [Attribut Routing](#ar)verwenden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-131">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="a45e6-132">Die `"{controller=Home}/{action=Index}/{id?}"`für die Routen Vorlage:</span><span class="sxs-lookup"><span data-stu-id="a45e6-132">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="a45e6-133">Entspricht einem URL-Pfad wie `/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="a45e6-133">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="a45e6-134">Extrahiert die Routen Werte `{ controller = Products, action = Details, id = 5 }` durch das tokenalisieren des Pfads.</span><span class="sxs-lookup"><span data-stu-id="a45e6-134">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="a45e6-135">Die Extraktion von Routen Werten führt zu einer Abstimmung, wenn die APP einen Controller mit dem Namen `ProductsController` und eine `Details` Aktion aufweist:</span><span class="sxs-lookup"><span data-stu-id="a45e6-135">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

 <span data-ttu-id="a45e6-136">Die [mydisplayrouteinfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) -Methode ist im [Beispiel Download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) enthalten und wird zum Anzeigen von Routing Informationen verwendet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-136">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>

  * <span data-ttu-id="a45e6-137">`/Products/Details/5` Modell bindet den Wert von `id = 5`, um den `id`-Parameter auf `5`festzulegen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-137">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="a45e6-138">Weitere Informationen finden Sie unter [Modell Bindung](xref:mvc/models/model-binding) .</span><span class="sxs-lookup"><span data-stu-id="a45e6-138">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="a45e6-139">`{controller=Home}` `Home` als Standard `controller`definiert.</span><span class="sxs-lookup"><span data-stu-id="a45e6-139">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="a45e6-140">`{action=Index}` `Index` als Standard `action`definiert.</span><span class="sxs-lookup"><span data-stu-id="a45e6-140">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="a45e6-141">Das `?` Zeichen in `{id?}` definiert `id` als optional.</span><span class="sxs-lookup"><span data-stu-id="a45e6-141">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="a45e6-142">Standardmäßige und optionale Routenparameter müssen nicht im URL-Pfad vorhanden sein, damit es eine Übereinstimmung gibt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-142">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="a45e6-143">Eine ausführliche Beschreibung der Syntax der Routenvorlage finden Sie unter [Routenvorlagenreferenz](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="a45e6-143">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="a45e6-144">Entspricht dem URL-Pfad `/`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-144">Matches the URL path `/`.</span></span>
* <span data-ttu-id="a45e6-145">Erzeugt die Routen Werte `{ controller = Home, action = Index }`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-145">Produces the route values `{ controller = Home, action = Index }`.</span></span>
* <span data-ttu-id="a45e6-146">Die [mydisplayrouteinfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) -Methode ist im [Beispiel Download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) enthalten und wird zum Anzeigen von Routing Informationen verwendet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-146">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>

<span data-ttu-id="a45e6-147">Die Werte für `controller` und `action` verwenden die Standardwerte.</span><span class="sxs-lookup"><span data-stu-id="a45e6-147">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="a45e6-148">`id` erzeugt keinen Wert, da kein entsprechendes Segment im URL-Pfad vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="a45e6-148">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="a45e6-149">`/` nur Übereinstimmungen, wenn eine `HomeController` und eine `Index` Aktion vorhanden sind:</span><span class="sxs-lookup"><span data-stu-id="a45e6-149">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="a45e6-150">Mithilfe der vorangehenden Controller Definition und Routen Vorlage wird die `HomeController.Index` Aktion für die folgenden URL-Pfade ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="a45e6-150">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="a45e6-151">Der URL-Pfad `/` verwendet die Routen Vorlagen Standard `Home` Controller und `Index` Aktion.</span><span class="sxs-lookup"><span data-stu-id="a45e6-151">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="a45e6-152">Der URL-Pfad `/Home` verwendet die Standard `Index` Aktion der Routen Vorlage.</span><span class="sxs-lookup"><span data-stu-id="a45e6-152">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="a45e6-153">Mit der Hilfsmethode <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span><span class="sxs-lookup"><span data-stu-id="a45e6-153">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="a45e6-154">Arbeits</span><span class="sxs-lookup"><span data-stu-id="a45e6-154">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="a45e6-155">Das Routing wird mithilfe der <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*>-und <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> Middleware konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="a45e6-155">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> middleware.</span></span> <span data-ttu-id="a45e6-156">So verwenden Sie Controller:</span><span class="sxs-lookup"><span data-stu-id="a45e6-156">To use controllers:</span></span>

* <span data-ttu-id="a45e6-157">Ruft <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> innerhalb `UseEndpoints` auf, um Attribut Routing Controller [zuzuordnen](#ar) .</span><span class="sxs-lookup"><span data-stu-id="a45e6-157">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
* <span data-ttu-id="a45e6-158">Ruft <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> oder <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>auf, um [konventionell weitergeleitete](#cr) Controller zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-158">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>, to map [conventionally routed](#cr) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="a45e6-159">Herkömmliches Routing</span><span class="sxs-lookup"><span data-stu-id="a45e6-159">Conventional routing</span></span>

<span data-ttu-id="a45e6-160">Herkömmliches Routing wird mit Controllern und Ansichten verwendet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-160">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="a45e6-161">Die `default`-Route:</span><span class="sxs-lookup"><span data-stu-id="a45e6-161">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="a45e6-162">ist ein Beispiel für *herkömmliches Routing*.</span><span class="sxs-lookup"><span data-stu-id="a45e6-162">is an example of a *conventional routing*.</span></span> <span data-ttu-id="a45e6-163">Es wird als *herkömmliches Routing* bezeichnet, da es eine *Konvention* für URL-Pfade festlegt:</span><span class="sxs-lookup"><span data-stu-id="a45e6-163">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="a45e6-164">Das erste Pfad Segment, `{controller=Home}`, wird dem Controller Namen zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-164">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="a45e6-165">Das zweite Segment, `{action=Index}`, wird dem [Aktions](#action) Namen zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-165">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="a45e6-166">Das dritte Segment, `{id?}` wird für eine optionale `id`verwendet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-166">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="a45e6-167">Die `?` in `{id?}` die optional ist.</span><span class="sxs-lookup"><span data-stu-id="a45e6-167">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="a45e6-168">`id` wird für die Zuordnung zu einer Modell Entität verwendet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-168">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="a45e6-169">Verwenden Sie diese `default` Route, den URL-Pfad:</span><span class="sxs-lookup"><span data-stu-id="a45e6-169">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="a45e6-170">`/Products/List` der `ProductsController.List` Aktion zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-170">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="a45e6-171">`/Blog/Article/17` wird `BlogController.Article` zugeordnet. in der Regel bindet Model den `id`-Parameter an 17.</span><span class="sxs-lookup"><span data-stu-id="a45e6-171">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="a45e6-172">Diese Zuordnung:</span><span class="sxs-lookup"><span data-stu-id="a45e6-172">This mapping:</span></span>

* <span data-ttu-id="a45e6-173">Basiert **nur**auf den Controller-und [Aktions](#action) Namen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-173">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="a45e6-174">Basiert nicht auf Namespaces, Quelldatei Speicherorten oder Methoden Parametern.</span><span class="sxs-lookup"><span data-stu-id="a45e6-174">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="a45e6-175">Die Verwendung von herkömmlichem Routing mit der Standardroute ermöglicht das Erstellen der APP, ohne für jede Aktion ein neues URL-Muster zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-175">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="a45e6-176">Für eine APP mit [CRUD-](https://wikipedia.org/wiki/Create,_read,_update_and_delete) Stil Aktionen mit Konsistenz für die URLs über Controller hinweg:</span><span class="sxs-lookup"><span data-stu-id="a45e6-176">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="a45e6-177">Erleichtert das Vereinfachen des Codes.</span><span class="sxs-lookup"><span data-stu-id="a45e6-177">Helps simplify the code.</span></span>
* <span data-ttu-id="a45e6-178">Macht die Benutzeroberfläche berechenbarer.</span><span class="sxs-lookup"><span data-stu-id="a45e6-178">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="a45e6-179">Der `id` im vorangehenden Code wird von der Routen Vorlage als optional definiert.</span><span class="sxs-lookup"><span data-stu-id="a45e6-179">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="a45e6-180">Aktionen können ausgeführt werden, ohne dass die optionale ID als Teil der URL bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-180">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="a45e6-181">Im Allgemeinen gilt Folgendes, wenn`id` in der URL weggelassen wird:</span><span class="sxs-lookup"><span data-stu-id="a45e6-181">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="a45e6-182">`id` wird von der Modell Bindung auf `0` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-182">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="a45e6-183">In der Datenbank, die `id == 0`entspricht, wurde keine Entität gefunden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-183">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="a45e6-184">Das [Attribut Routing](#ar) ermöglicht eine differenzierte Steuerung, um die erforderliche ID für einige Aktionen und nicht für andere zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-184">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="a45e6-185">Gemäß der Konvention enthält die Dokumentation optionale Parameter wie `id`, wenn Sie wahrscheinlich in der richtigen Verwendung angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-185">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="a45e6-186">Für die meisten Apps sollte eine grundlegendes und beschreibendes Routingschema ausgewählt werden, um lesbare und aussagekräftige URLs zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="a45e6-186">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="a45e6-187">Für die konventionelle Standardroute `{controller=Home}/{action=Index}/{id?}` gilt:</span><span class="sxs-lookup"><span data-stu-id="a45e6-187">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="a45e6-188">Sie unterstützt ein grundlegendes und beschreibendes Routingschema.</span><span class="sxs-lookup"><span data-stu-id="a45e6-188">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="a45e6-189">Sie stellt einen nützlichen Startpunkt für benutzeroberflächenbasierte Apps dar.</span><span class="sxs-lookup"><span data-stu-id="a45e6-189">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="a45e6-190">Ist die einzige Routen Vorlage, die für viele Web-UI-apps benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-190">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="a45e6-191">Bei größeren Webanwendungs-Apps ist eine weitere Route, die [Bereiche](#areas) verwendet, wenn diese häufig benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-191">For larger web UI apps, another route using [Areas](#areas) if frequently all that's needed.</span></span>

<span data-ttu-id="a45e6-192"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> und <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*>:</span><span class="sxs-lookup"><span data-stu-id="a45e6-192"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span></span>

* <span data-ttu-id="a45e6-193">Weisen Sie Ihren Endpunkten automatisch einen **Bestell** Wert basierend auf der Reihenfolge zu, in der Sie aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-193">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="a45e6-194">Endpunkt Routing in ASP.net Core 3,0 und höher:</span><span class="sxs-lookup"><span data-stu-id="a45e6-194">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="a45e6-195">Hat kein Konzept von Routen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-195">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="a45e6-196">Bietet keine Bestell Garantien für die Ausführung der Erweiterbarkeit. alle Endpunkte werden gleichzeitig verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-196">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="a45e6-197">Wenn Sie die [Protokollierung](xref:fundamentals/logging/index) aktivieren, erfahren Sie, wie die integrierten Routingimplementierungen (z.B. <xref:Microsoft.AspNetCore.Routing.Route>) Zuordnungen für Anforderungen ermitteln.</span><span class="sxs-lookup"><span data-stu-id="a45e6-197">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="a45e6-198">Das [Attribut Routing](#ar) wird weiter unten in diesem Dokument erläutert.</span><span class="sxs-lookup"><span data-stu-id="a45e6-198">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="a45e6-199">Mehrere herkömmliche Routen</span><span class="sxs-lookup"><span data-stu-id="a45e6-199">Multiple conventional routes</span></span>

<span data-ttu-id="a45e6-200">Mehrere [herkömmliche Routen](#cr) können innerhalb `UseEndpoints` hinzugefügt werden, indem <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> und <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>weitere Aufrufe hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-200">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span></span> <span data-ttu-id="a45e6-201">Dies ermöglicht das Definieren mehrerer Konventionen oder das Hinzufügen herkömmlicher Routen, die einer bestimmten [Aktion](#action)zugeordnet sind, wie z. b.:</span><span class="sxs-lookup"><span data-stu-id="a45e6-201">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="a45e6-202">Die `blog` Route im vorangehenden Code ist eine **dedizierte konventionelle Route**.</span><span class="sxs-lookup"><span data-stu-id="a45e6-202">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="a45e6-203">Dies wird als dedizierte konventionelle Route bezeichnet:</span><span class="sxs-lookup"><span data-stu-id="a45e6-203">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="a45e6-204">Es verwendet [herkömmliches Routing](#cr).</span><span class="sxs-lookup"><span data-stu-id="a45e6-204">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="a45e6-205">Es ist für eine bestimmte [Aktion](#action)vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-205">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="a45e6-206">Da `controller` und `action` in der Routen Vorlage nicht `"blog/{*article}"` als Parameter angezeigt werden:</span><span class="sxs-lookup"><span data-stu-id="a45e6-206">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="a45e6-207">Sie können nur die Standardwerte `{ controller = "Blog", action = "Article" }`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-207">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="a45e6-208">Diese Route wird immer dem Aktions `BlogController.Article`zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-208">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="a45e6-209">`/Blog`, `/Blog/Article`und `/Blog/{any-string}` sind die einzigen URL-Pfade, die der Blog Route entsprechen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-209">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="a45e6-210">Das vorherige Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a45e6-210">The preceding example:</span></span>

* <span data-ttu-id="a45e6-211">`blog` Route hat eine höhere Priorität als die `default` Route, da Sie zuerst hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-211">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="a45e6-212">Ist ein Beispiel für das Routing im [Slug](https://developer.mozilla.org/docs/Glossary/Slug) -Format, in dem normalerweise ein Artikelname als Teil der URL vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="a45e6-212">Is and example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="a45e6-213">In ASP.net Core 3,0 und höher ist das Routing nicht:</span><span class="sxs-lookup"><span data-stu-id="a45e6-213">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="a45e6-214">Definieren Sie ein Konzept, das als *Route*bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-214">Define a concept called a *route*.</span></span> <span data-ttu-id="a45e6-215">`UseRouting` fügt der middlewarepipeline Routen Übereinstimmung hinzu.</span><span class="sxs-lookup"><span data-stu-id="a45e6-215">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="a45e6-216">Die `UseRouting` Middleware untersucht den Satz von Endpunkten, der in der APP definiert ist, und wählt basierend auf der Anforderung die beste Endpunkt Übereinstimmung aus.</span><span class="sxs-lookup"><span data-stu-id="a45e6-216">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="a45e6-217">Stellen Sie Garantien für die Ausführungsreihenfolge der Erweiterbarkeit wie <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> oder <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>bereit.</span><span class="sxs-lookup"><span data-stu-id="a45e6-217">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="a45e6-218">Referenzmaterial zum Routing finden Sie unter [Routing](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="a45e6-218">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="a45e6-219">Konventionelle Routing Reihenfolge</span><span class="sxs-lookup"><span data-stu-id="a45e6-219">Conventional routing order</span></span>

<span data-ttu-id="a45e6-220">Herkömmliches Routing stimmt nur mit einer Kombination aus Aktion und Controller überein, die von der APP definiert werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-220">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="a45e6-221">Dies dient zur Vereinfachung der Fälle, in denen herkömmliche Routen sich überlappen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-221">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="a45e6-222">Durch das Hinzufügen von Routen mithilfe von <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>und <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> wird ihren Endpunkten automatisch ein Bestellwert entsprechend der Reihenfolge zugewiesen, in der Sie aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-222">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="a45e6-223">Übereinstimmungen aus einer zuvor angezeigten Route haben eine höhere Priorität.</span><span class="sxs-lookup"><span data-stu-id="a45e6-223">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="a45e6-224">Beim herkömmlichen Routing ist die Reihenfolge wichtig.</span><span class="sxs-lookup"><span data-stu-id="a45e6-224">Conventional routing is order-dependent.</span></span> <span data-ttu-id="a45e6-225">Im Allgemeinen sollten Routen mit Bereichen früher platziert werden, da Sie spezifischer als Routen ohne Bereich sind.</span><span class="sxs-lookup"><span data-stu-id="a45e6-225">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="a45e6-226">[Dedizierte herkömmliche Routen](#dcr) mit catch all Route-Parameter wie `{*article}` können eine Route zu [gierig](xref:fundamentals/routing#greedy)machen, was bedeutet, dass Sie mit den URLs übereinstimmt, die Sie mit anderen Routen vergleichen wollten.</span><span class="sxs-lookup"><span data-stu-id="a45e6-226">[Dedicated conventional routes](#dcr) with catch all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="a45e6-227">Platzieren Sie die gierigen Routen später in der Routing Tabelle, um gierige Übereinstimmungen zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-227">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="a45e6-228">Auflösen mehrdeutiger Aktionen</span><span class="sxs-lookup"><span data-stu-id="a45e6-228">Resolving ambiguous actions</span></span>

<span data-ttu-id="a45e6-229">Wenn zwei Endpunkte über das Routing abgleichen, muss das Routing einen der folgenden Schritte ausführen:</span><span class="sxs-lookup"><span data-stu-id="a45e6-229">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="a45e6-230">Wählen Sie den besten Kandidaten aus.</span><span class="sxs-lookup"><span data-stu-id="a45e6-230">Choose the best candidate.</span></span>
* <span data-ttu-id="a45e6-231">Löst eine Ausnahme aus.</span><span class="sxs-lookup"><span data-stu-id="a45e6-231">Throw an exception.</span></span>

<span data-ttu-id="a45e6-232">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a45e6-232">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="a45e6-233">Der vorangehende Controller definiert zwei Aktionen, die mit identisch sind:</span><span class="sxs-lookup"><span data-stu-id="a45e6-233">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="a45e6-234">Der URL-Pfad `/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="a45e6-234">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="a45e6-235">Leiten Sie Daten `{ controller = Products33, action = Edit, id = 17 }`weiter.</span><span class="sxs-lookup"><span data-stu-id="a45e6-235">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="a45e6-236">Dies ist ein typisches Muster für MVC-Controller:</span><span class="sxs-lookup"><span data-stu-id="a45e6-236">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="a45e6-237">in `Edit(int)` wird ein Formular zum Bearbeiten eines Produkts angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-237">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="a45e6-238">`Edit(int, Product)` das gepostete Formular verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-238">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="a45e6-239">So beheben Sie die korrekte Route:</span><span class="sxs-lookup"><span data-stu-id="a45e6-239">To resolve the correct route:</span></span>

* <span data-ttu-id="a45e6-240">`Edit(int, Product)` ist ausgewählt, wenn die Anforderung ein HTTP-`POST`ist.</span><span class="sxs-lookup"><span data-stu-id="a45e6-240">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="a45e6-241">`Edit(int)` wird ausgewählt, wenn das [http-Verb](#verb) etwas anderes ist.</span><span class="sxs-lookup"><span data-stu-id="a45e6-241">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="a45e6-242">`Edit(int)` wird in der Regel über `GET`aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-242">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="a45e6-243">Der <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, wird für das Routing bereitgestellt, sodass er basierend auf der HTTP-Methode der Anforderung ausgewählt werden kann.</span><span class="sxs-lookup"><span data-stu-id="a45e6-243">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="a45e6-244">Der `HttpPostAttribute` macht `Edit(int, Product)` eine bessere Entsprechung als `Edit(int)`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-244">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="a45e6-245">Es ist wichtig, die Rolle von Attributen wie `HttpPostAttribute`zu verstehen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-245">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="a45e6-246">Ähnliche Attribute werden für andere [HTTP-Verben](#verb)definiert.</span><span class="sxs-lookup"><span data-stu-id="a45e6-246">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="a45e6-247">Bei [herkömmlichem Routing](#cr)wird bei Aktionen häufig derselbe Aktionsname verwendet, wenn Sie Teil eines anshow Formulars sind, Formular Workflow senden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-247">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="a45e6-248">Informationen hierzu finden Sie beispielsweise [unter Überprüfen der beiden Bearbeitungs Aktionsmethoden](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span><span class="sxs-lookup"><span data-stu-id="a45e6-248">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="a45e6-249">Wenn das Routing keinen besten Kandidaten auswählen kann, wird eine <xref:System.Reflection.AmbiguousMatchException> ausgelöst, die mehrere übereinstimmende Endpunkte auflistet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-249">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="a45e6-250">Herkömmliche Routennamen</span><span class="sxs-lookup"><span data-stu-id="a45e6-250">Conventional route names</span></span>

<span data-ttu-id="a45e6-251">Die Zeichen folgen `"blog"` und `"default"` in den folgenden Beispielen sind konventionelle Routennamen:</span><span class="sxs-lookup"><span data-stu-id="a45e6-251">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="a45e6-252">Mit den Routennamen wird der Route ein logischer Name zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-252">The route names give the route a logical name.</span></span> <span data-ttu-id="a45e6-253">Die benannte Route kann für die URL-Generierung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-253">The named route can be used for URL generation.</span></span> <span data-ttu-id="a45e6-254">Die Verwendung einer benannten Route vereinfacht die URL-Erstellung, wenn die Reihenfolge der Routen die URL-Generierung erschweren könnte.</span><span class="sxs-lookup"><span data-stu-id="a45e6-254">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="a45e6-255">Routennamen müssen eine eindeutige Anwendungs weite sein.</span><span class="sxs-lookup"><span data-stu-id="a45e6-255">Route names must be unique application wide.</span></span>

<span data-ttu-id="a45e6-256">Routennamen:</span><span class="sxs-lookup"><span data-stu-id="a45e6-256">Route names:</span></span>

* <span data-ttu-id="a45e6-257">Hat keine Auswirkung auf die URL-Übereinstimmung oder-Verarbeitung von Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-257">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="a45e6-258">Werden nur für die URL-Generierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-258">Are used only for URL generation.</span></span>

<span data-ttu-id="a45e6-259">Das Routing Namenskonzept wird als " [iendpointnamemetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata)" dargestellt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-259">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="a45e6-260">Die Begriffe **Routen Name** und **Endpunkt Name**:</span><span class="sxs-lookup"><span data-stu-id="a45e6-260">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="a45e6-261">Sind austauschbar.</span><span class="sxs-lookup"><span data-stu-id="a45e6-261">Are interchangeable.</span></span>
* <span data-ttu-id="a45e6-262">Welche Informationen in der Dokumentation und im Code verwendet werden, hängt von der API ab, die beschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-262">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="a45e6-263">Attribut Routing für Rest-APIs</span><span class="sxs-lookup"><span data-stu-id="a45e6-263">Attribute routing for REST APIs</span></span>

<span data-ttu-id="a45e6-264">Rest-APIs sollten das Attribut Routing verwenden, um die Funktionalität der App als Satz von Ressourcen zu modellieren, in denen Vorgänge durch [HTTP-Verben](#verb)dargestellt werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-264">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="a45e6-265">Beim Attributrouting werden Aktionen mithilfe von Attributen direkt Routenvorlagen zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-265">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="a45e6-266">Der folgende `StartUp.Configure` Code ist typisch für eine Rest-API und wird im nächsten Beispiel verwendet:</span><span class="sxs-lookup"><span data-stu-id="a45e6-266">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

<span data-ttu-id="a45e6-267">Im vorangehenden Code wird <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> in `UseEndpoints` aufgerufen, um Attribut Routing Controller zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-267">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="a45e6-268">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a45e6-268">In the following example:</span></span>

* <span data-ttu-id="a45e6-269">Die vorangehende `Configure` Methode wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-269">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="a45e6-270">`HomeController` entspricht einem Satz von URLs, die mit der herkömmlichen Standardroute `{controller=Home}/{action=Index}/{id?}` übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-270">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="a45e6-271">Die `HomeController.Index` Aktion wird für alle URL-Pfade `/`, `/Home`, `/Home/Index`oder `/Home/Index/3`ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-271">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="a45e6-272">In diesem Beispiel wird ein wichtiger Programmier Unterschied zwischen Attribut Routing und [herkömmlichem Routing](#cr)hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="a45e6-272">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="a45e6-273">Das Attribut Routing erfordert mehr Eingaben, um eine Route anzugeben.</span><span class="sxs-lookup"><span data-stu-id="a45e6-273">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="a45e6-274">Bei der herkömmlichen Standardroute werden Routen genauer behandelt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-274">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="a45e6-275">Das Attribut Routing ermöglicht und erfordert jedoch eine genaue Kontrolle darüber, welche Routen Vorlagen auf die einzelnen [Aktionen](#action)angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-275">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="a45e6-276">Beim Attribut Routing spielen Controller Name und Aktions Namen **keine** Rolle, in welcher Aktion abgeglichen wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-276">With attribute routing, the controller name and action names play **no** role in which action is matched.</span></span> <span data-ttu-id="a45e6-277">Das folgende Beispiel entspricht den gleichen URLs wie im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a45e6-277">The following example matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="a45e6-278">Im folgenden Code wird die Tokenersetzung für `action` und `controller`verwendet:</span><span class="sxs-lookup"><span data-stu-id="a45e6-278">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="a45e6-279">Der folgende Code wendet `[Route("[controller]/[action]")]` auf den Controller an:</span><span class="sxs-lookup"><span data-stu-id="a45e6-279">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="a45e6-280">Im vorangehenden Code müssen die Vorlagen der `Index`-Methode `/` oder `~/` den Routen Vorlagen vorangestellt werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-280">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="a45e6-281">Routenvorlagen, die auf eine Aktion angewendet werden, die mit einem `/` oder `~/` beginnen, können nicht mit Routenvorlagen kombiniert werden, die auf den Controller angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-281">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="a45e6-282">Weitere Informationen zur Routen Vorlagen Auswahl finden Sie unter [Routen Vorlagen Rangfolge](xref:fundamentals/routing#rtp) .</span><span class="sxs-lookup"><span data-stu-id="a45e6-282">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="a45e6-283">Reservierte Routingnamen</span><span class="sxs-lookup"><span data-stu-id="a45e6-283">Reserved routing names</span></span>

<span data-ttu-id="a45e6-284">Die folgenden Schlüsselwörter sind reservierte Routen Parameternamen, wenn Controller oder Razor Pages verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="a45e6-284">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="a45e6-285">Die Verwendung von `page` als Routen Parameter mit Attribut Routing ist ein häufiger Fehler.</span><span class="sxs-lookup"><span data-stu-id="a45e6-285">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="a45e6-286">Dies führt zu inkonsistentem und verwirdem Verhalten bei der URL-Generierung.</span><span class="sxs-lookup"><span data-stu-id="a45e6-286">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="a45e6-287">Die speziellen Parameternamen werden von der URL-Generierung verwendet, um zu bestimmen, ob ein URL-Generierungs Vorgang auf eine Razor Page oder einen Controller verweist.</span><span class="sxs-lookup"><span data-stu-id="a45e6-287">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="a45e6-288">HTTP-Verb Vorlagen</span><span class="sxs-lookup"><span data-stu-id="a45e6-288">HTTP verb templates</span></span>

<span data-ttu-id="a45e6-289">ASP.net Core verfügt über die folgenden HTTP-Verb Vorlagen:</span><span class="sxs-lookup"><span data-stu-id="a45e6-289">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="a45e6-290">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="a45e6-290">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="a45e6-291">[HttpPost](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="a45e6-291">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="a45e6-292">[HttpPut](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="a45e6-292">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="a45e6-293">[HttpDelete](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="a45e6-293">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="a45e6-294">[[Httphead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="a45e6-294">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="a45e6-295">[[Httppatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="a45e6-295">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="a45e6-296">Routenvorlagen</span><span class="sxs-lookup"><span data-stu-id="a45e6-296">Route templates</span></span>

<span data-ttu-id="a45e6-297">ASP.net Core verfügt über die folgenden Routen Vorlagen:</span><span class="sxs-lookup"><span data-stu-id="a45e6-297">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="a45e6-298">Alle [http-Verb Vorlagen](#verb) sind Routen Vorlagen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-298">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="a45e6-299">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="a45e6-299">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="a45e6-300">Attribut Routing mit HTTP-Verb Attributen</span><span class="sxs-lookup"><span data-stu-id="a45e6-300">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="a45e6-301">Beachten Sie den folgenden Controller:</span><span class="sxs-lookup"><span data-stu-id="a45e6-301">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="a45e6-302">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="a45e6-302">In the preceding code:</span></span>

* <span data-ttu-id="a45e6-303">Jede Aktion enthält das `[HttpGet]`-Attribut, das die Übereinstimmung von HTTP-GET-Anforderungen einschränkt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-303">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="a45e6-304">Die `GetProduct` Aktion enthält die `"{id}"` Vorlage. Daher wird `id` an die `"api/[controller]"` Vorlage auf dem Controller angehängt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-304">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="a45e6-305">Die Methoden Vorlage ist `"api/[controller]/"{id}""`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-305">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="a45e6-306">Daher entspricht diese Aktion nur Get-Anforderungen von für das Formular `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`usw.</span><span class="sxs-lookup"><span data-stu-id="a45e6-306">Therefore this action only matches GET requests of for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="a45e6-307">Die `GetIntProduct` Aktion enthält die `"int/{id:int}")` Vorlage.</span><span class="sxs-lookup"><span data-stu-id="a45e6-307">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="a45e6-308">Der `:int` Teil der Vorlage schränkt die `id` Routen Werte auf Zeichen folgen ein, die in eine ganze Zahl konvertiert werden können.</span><span class="sxs-lookup"><span data-stu-id="a45e6-308">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="a45e6-309">Eine GET-Anforderung zum `/api/test2/int/abc`:</span><span class="sxs-lookup"><span data-stu-id="a45e6-309">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="a45e6-310">Entspricht dieser Aktion nicht.</span><span class="sxs-lookup"><span data-stu-id="a45e6-310">Doesn't match this action.</span></span>
  * <span data-ttu-id="a45e6-311">Gibt den Fehler " [404 nicht gefunden](https://developer.mozilla.org/docs/Web/HTTP/Status/404) " zurück.</span><span class="sxs-lookup"><span data-stu-id="a45e6-311">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="a45e6-312">Die `GetInt2Product` Aktion enthält `{id}` in der Vorlage, schränkt `id` jedoch nicht auf Werte ein, die in eine ganze Zahl konvertiert werden können.</span><span class="sxs-lookup"><span data-stu-id="a45e6-312">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="a45e6-313">Eine GET-Anforderung zum `/api/test2/int2/abc`:</span><span class="sxs-lookup"><span data-stu-id="a45e6-313">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="a45e6-314">Entspricht dieser Route.</span><span class="sxs-lookup"><span data-stu-id="a45e6-314">Matches this route.</span></span>
  * <span data-ttu-id="a45e6-315">Die Modell Bindung kann `abc` nicht in eine ganze Zahl konvertieren.</span><span class="sxs-lookup"><span data-stu-id="a45e6-315">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="a45e6-316">Der `id`-Parameter der-Methode ist Integer.</span><span class="sxs-lookup"><span data-stu-id="a45e6-316">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="a45e6-317">Gibt eine ungültige [400-Anforderung](https://developer.mozilla.org/docs/Web/HTTP/Status/400) zurück, da die Modell Bindung`abc` nicht in eine ganze Zahl konvertieren konnte.</span><span class="sxs-lookup"><span data-stu-id="a45e6-317">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="a45e6-318">Beim Attribut Routing können <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> Attribute wie <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>und <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-318">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="a45e6-319">Alle HTTP- [Verb](#verb) Attribute akzeptieren eine Routen Vorlage.</span><span class="sxs-lookup"><span data-stu-id="a45e6-319">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="a45e6-320">Das folgende Beispiel zeigt zwei Aktionen, die derselben Routen Vorlage entsprechen:</span><span class="sxs-lookup"><span data-stu-id="a45e6-320">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="a45e6-321">Verwenden Sie den URL-Pfad `/products3`:</span><span class="sxs-lookup"><span data-stu-id="a45e6-321">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="a45e6-322">Die `MyProductsController.ListProducts` Aktion wird ausgeführt, wenn das [http-Verb](#verb) `GET`ist.</span><span class="sxs-lookup"><span data-stu-id="a45e6-322">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="a45e6-323">Die `MyProductsController.CreateProduct` Aktion wird ausgeführt, wenn das [http-Verb](#verb) `POST`ist.</span><span class="sxs-lookup"><span data-stu-id="a45e6-323">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="a45e6-324">Beim Entwickeln einer Rest-API müssen Sie in seltenen Fällen `[Route(...)]` für eine Aktionsmethode verwenden, da die Aktion alle HTTP-Methoden akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="a45e6-324">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="a45e6-325">Es ist besser, das spezifischere [http-Verb-Attribut](#verb) zu verwenden, um genau zu erfahren, was Ihre API unterstützt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-325">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="a45e6-326">REST-API-Clients sollten wissen, welche Pfade und HTTP-Verben bestimmten logischen Operationen entsprechen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-326">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="a45e6-327">Rest-APIs sollten das Attribut Routing verwenden, um die Funktionalität der App als Satz von Ressourcen zu modellieren, in denen Vorgänge durch HTTP-Verben dargestellt werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-327">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="a45e6-328">Dies bedeutet, dass viele Vorgänge, z. b. Get und Post, für dieselbe logische Ressource dieselbe URL verwenden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-328">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="a45e6-329">Das Attributrouting bietet eine Ebene der Steuerung, die für einen sorgfältigen Entwurf des öffentlichen Endpunktlayouts einer API erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="a45e6-329">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="a45e6-330">Da eine Attributroute für eine bestimmte Aktion gilt, ist es einfach, Parameter als Teil der Routenvorlagendefinition erforderlich festzulegen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-330">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="a45e6-331">Im folgenden Beispiel ist `id` als Teil des URL-Pfads erforderlich:</span><span class="sxs-lookup"><span data-stu-id="a45e6-331">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="a45e6-332">Die `Products2ApiController.GetProduct(int)` Aktion:</span><span class="sxs-lookup"><span data-stu-id="a45e6-332">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="a45e6-333">Wird mit URL-Pfad wie `/products2/3` ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-333">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="a45e6-334">Wird nicht mit dem URL-Pfad `/products2`ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-334">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="a45e6-335">Das [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)-Attribut ermöglicht es einer Aktion, die unterstützten Anforderungsinhaltstypen einzuschränken.</span><span class="sxs-lookup"><span data-stu-id="a45e6-335">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="a45e6-336">Weitere Informationen finden Sie [unter Definieren unterstützter Anforderungs Inhaltstypen mit dem Attribut "Verbrauchs](xref:web-api/index#consumes)".</span><span class="sxs-lookup"><span data-stu-id="a45e6-336">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="a45e6-337">Eine vollständige Beschreibung und Routenvorlagen und dazugehörige Optionen finden Sie unter [Routing in ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="a45e6-337">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="a45e6-338">Weitere Informationen zu `[ApiController]`finden Sie unter [apicontroller-Attribut](xref:web-api/index##apicontroller-attribute).</span><span class="sxs-lookup"><span data-stu-id="a45e6-338">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="a45e6-339">Routenname</span><span class="sxs-lookup"><span data-stu-id="a45e6-339">Route name</span></span>

<span data-ttu-id="a45e6-340">Der folgende Code definiert den Routennamen `Products_List`:</span><span class="sxs-lookup"><span data-stu-id="a45e6-340">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="a45e6-341">Routennamen können verwendet werden, um basierend auf einer bestimmten Route eine URL zu generieren.</span><span class="sxs-lookup"><span data-stu-id="a45e6-341">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="a45e6-342">Routennamen:</span><span class="sxs-lookup"><span data-stu-id="a45e6-342">Route names:</span></span>

* <span data-ttu-id="a45e6-343">Hat keine Auswirkung auf das URL-Vergleichs Verhalten des Routings.</span><span class="sxs-lookup"><span data-stu-id="a45e6-343">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="a45e6-344">Werden nur für die URL-Generierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-344">Are only used for URL generation.</span></span>

<span data-ttu-id="a45e6-345">Routennamen müssen anwendungsweit eindeutig sein.</span><span class="sxs-lookup"><span data-stu-id="a45e6-345">Route names must be unique application-wide.</span></span>

<span data-ttu-id="a45e6-346">Vergleichen Sie den vorangehenden Code mit der herkömmlichen Standardroute, die den `id` Parameter als optional (`{id?}`) definiert.</span><span class="sxs-lookup"><span data-stu-id="a45e6-346">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="a45e6-347">Die Möglichkeit, APIs genau anzugeben, bietet Vorteile, z. b. die Möglichkeit, `/products` und `/products/5` an verschiedene Aktionen weiter zusenden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-347">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="a45e6-348">Kombinieren von Attribut Routen</span><span class="sxs-lookup"><span data-stu-id="a45e6-348">Combining attribute routes</span></span>

<span data-ttu-id="a45e6-349">Um Attributrouting weniger repetitiv zu gestalten, werden Routenattribute auf dem Controller mit Routenattributen auf den einzelnen Aktionen kombiniert.</span><span class="sxs-lookup"><span data-stu-id="a45e6-349">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="a45e6-350">Alle auf dem Controller definierten Routenvorlagen werden den Routenvorlagen auf den Aktionen vorangestellt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-350">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="a45e6-351">Wenn Routenattribute auf dem Controller platziert werden, verwenden **alle** Aktionen im Controller Attributrouting.</span><span class="sxs-lookup"><span data-stu-id="a45e6-351">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="a45e6-352">Im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a45e6-352">In the preceding example:</span></span>

* <span data-ttu-id="a45e6-353">Der URL-Pfad `/products` kann `ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="a45e6-353">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="a45e6-354">Der URL-Pfad `/products/5` kann `ProductsApi.GetProduct(int)`entsprechen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-354">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="a45e6-355">Beide Aktionen entsprechen nur http-`GET`, da Sie mit dem `[HttpGet]`-Attribut markiert sind.</span><span class="sxs-lookup"><span data-stu-id="a45e6-355">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="a45e6-356">Routenvorlagen, die auf eine Aktion angewendet werden, die mit einem `/` oder `~/` beginnen, können nicht mit Routenvorlagen kombiniert werden, die auf den Controller angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-356">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="a45e6-357">Das folgende Beispiel entspricht einem Satz von URL-Pfaden, die der Standardroute ähneln.</span><span class="sxs-lookup"><span data-stu-id="a45e6-357">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="a45e6-358">In der folgenden Tabelle werden die `[Route]` Attribute im vorangehenden Code erläutert:</span><span class="sxs-lookup"><span data-stu-id="a45e6-358">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="a45e6-359">Attribut</span><span class="sxs-lookup"><span data-stu-id="a45e6-359">Attribute</span></span>               | <span data-ttu-id="a45e6-360">Kombiniert mit `[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="a45e6-360">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="a45e6-361">Definiert die Routen Vorlage.</span><span class="sxs-lookup"><span data-stu-id="a45e6-361">Defines route template</span></span> |
| ----------------- | ------------ | --------- |
| `[Route("")]` | <span data-ttu-id="a45e6-362">Ja</span><span class="sxs-lookup"><span data-stu-id="a45e6-362">Yes</span></span> | `"Home"` |
| `[Route("Index")]` | <span data-ttu-id="a45e6-363">Ja</span><span class="sxs-lookup"><span data-stu-id="a45e6-363">Yes</span></span> | `"Home/Index"` |
| `[Route("/")]` | <span data-ttu-id="a45e6-364">**No**</span><span class="sxs-lookup"><span data-stu-id="a45e6-364">**No**</span></span> | `""` |
| `[Route("About")]` | <span data-ttu-id="a45e6-365">Ja</span><span class="sxs-lookup"><span data-stu-id="a45e6-365">Yes</span></span> | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="a45e6-366">Attribut Routen Reihenfolge</span><span class="sxs-lookup"><span data-stu-id="a45e6-366">Attribute route order</span></span>

<span data-ttu-id="a45e6-367">Routing erstellt eine Struktur und gleicht alle Endpunkte gleichzeitig ab:</span><span class="sxs-lookup"><span data-stu-id="a45e6-367">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="a45e6-368">Die Routen Einträge Verhalten sich so, als ob Sie in einer idealen Reihenfolge platziert werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-368">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="a45e6-369">Die spezifischsten Routen können vor den allgemeineren Routen ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-369">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="a45e6-370">Beispielsweise ist eine Attribut Route wie `blog/search/{topic}` spezifischer als eine Attribut Route wie `blog/{*article}`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-370">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="a45e6-371">Die `blog/search/{topic}` Route hat standardmäßig eine höhere Priorität, da Sie spezifischer ist.</span><span class="sxs-lookup"><span data-stu-id="a45e6-371">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="a45e6-372">Mit [herkömmlichem Routing](#cr)ist der Entwickler für das Platzieren von Routen in der gewünschten Reihenfolge verantwortlich.</span><span class="sxs-lookup"><span data-stu-id="a45e6-372">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="a45e6-373">Attribut Routen können mithilfe der <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order>-Eigenschaft eine Bestellung konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="a45e6-373">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="a45e6-374">Alle vom Framework bereitgestellten [Routen Attribute](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) enthalten `Order`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-374">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="a45e6-375">Routen werden entsprechend einer aufsteigenden Reihenfolge der `Order`-Eigenschaft verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-375">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="a45e6-376">Die Standardreihenfolge ist `0`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-376">The default order is `0`.</span></span> <span data-ttu-id="a45e6-377">Das Festlegen einer Route mithilfe von `Order = -1` wird vor Routen ausgeführt, die keine Reihenfolge festlegen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-377">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="a45e6-378">Das Festlegen einer Route mithilfe von `Order = 1` wird nach der Standardrouten Anordnung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-378">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="a45e6-379">**Vermeiden** Sie die Abhängigkeit von `Order`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-379">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="a45e6-380">Wenn für den URL-Speicher einer APP explizite Auftrags Werte erforderlich sind, um ordnungsgemäß weiterzuleiten, ist es wahrscheinlich auch für Clients verwirrend.</span><span class="sxs-lookup"><span data-stu-id="a45e6-380">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="a45e6-381">Im Allgemeinen wählt das Attribut Routing die richtige Route mit URL-Übereinstimmung aus.</span><span class="sxs-lookup"><span data-stu-id="a45e6-381">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="a45e6-382">Wenn die für die URL-Generierung verwendete Standard Reihenfolge nicht funktioniert, ist die Verwendung eines Routen namens als außer Kraft Setzung in der Regel einfacher als das Anwenden der `Order` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="a45e6-382">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="a45e6-383">Beachten Sie die beiden folgenden Controller, die beide den `/home`für die Routen Zuordnung definieren:</span><span class="sxs-lookup"><span data-stu-id="a45e6-383">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="a45e6-384">Wenn Sie `/home` mit dem vorangehenden Code anfordern, wird eine Ausnahme ausgelöst, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="a45e6-384">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="a45e6-385">Durch das Hinzufügen von `Order` zu einem der Routen Attribute wird die Mehrdeutigkeit aufgelöst:</span><span class="sxs-lookup"><span data-stu-id="a45e6-385">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="a45e6-386">Mit dem vorangehenden Code führt `/home` den `HomeController.Index` Endpunkt aus.</span><span class="sxs-lookup"><span data-stu-id="a45e6-386">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="a45e6-387">Um zum `MyDemoController.MyIndex`zu gelangen, fordern Sie `/home/MyIndex`an.</span><span class="sxs-lookup"><span data-stu-id="a45e6-387">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="a45e6-388">**Hinweis:**</span><span class="sxs-lookup"><span data-stu-id="a45e6-388">**Note**:</span></span>

* <span data-ttu-id="a45e6-389">Der vorangehende Code ist ein Beispiel oder ein schlechtes Routing Design.</span><span class="sxs-lookup"><span data-stu-id="a45e6-389">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="a45e6-390">Es wurde verwendet, um die `Order`-Eigenschaft zu veranschaulichen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-390">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="a45e6-391">Die `Order`-Eigenschaft löst nur die Mehrdeutigkeit auf, diese Vorlage kann nicht abgeglichen werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-391">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="a45e6-392">Es wäre besser, die `[Route("Home")]` Vorlage zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-392">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="a45e6-393">Informationen zur Routen Reihenfolge mit Razor Pages finden Sie unter [Razor Pages Routen-und App-Konventionen: Routen Reihenfolge](xref:razor-pages/razor-pages-conventions#route-order) .</span><span class="sxs-lookup"><span data-stu-id="a45e6-393">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="a45e6-394">In einigen Fällen wird ein HTTP 500-Fehler mit mehrdeutigen Routen zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="a45e6-394">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="a45e6-395">Verwenden Sie die [Protokollierung](xref:fundamentals/logging/index) , um anzuzeigen, welche Endpunkte den `AmbiguousMatchException`verursacht haben</span><span class="sxs-lookup"><span data-stu-id="a45e6-395">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="a45e6-396">Ersetzung von Token in Routen Vorlagen [Controller], [Aktion], [Bereich]</span><span class="sxs-lookup"><span data-stu-id="a45e6-396">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="a45e6-397">Zur einfacheren Unterstützung unterstützen Attribut Routen die Tokenersetzung für reservierte Routen Parameter durch das Einschließen eines Tokens in einer der folgenden Werte:</span><span class="sxs-lookup"><span data-stu-id="a45e6-397">For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:</span></span>

* <span data-ttu-id="a45e6-398">Eckige Klammern: `[]`</span><span class="sxs-lookup"><span data-stu-id="a45e6-398">Square braces: `[]`</span></span>
* <span data-ttu-id="a45e6-399">Geschweifte Klammern: `{}`</span><span class="sxs-lookup"><span data-stu-id="a45e6-399">Curly braces: `{}`</span></span>

<span data-ttu-id="a45e6-400">Die Token `[action]`, `[area]`und `[controller]` werden durch die Werte für Aktionsname, Bereichs Name und Controller Name aus der Aktion ersetzt, in der die Route definiert ist:</span><span class="sxs-lookup"><span data-stu-id="a45e6-400">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="a45e6-401">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="a45e6-401">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="a45e6-402">Entspricht `/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="a45e6-402">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="a45e6-403">Entspricht `/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="a45e6-403">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="a45e6-404">Die Tokenersetzung tritt im letzten Schritt der Erstellung von Attributrouten auf.</span><span class="sxs-lookup"><span data-stu-id="a45e6-404">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="a45e6-405">Das vorherige Beispiel verhält sich wie der folgende Code:</span><span class="sxs-lookup"><span data-stu-id="a45e6-405">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="a45e6-406">Attributrouten können auch mit Vererbung kombiniert werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-406">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="a45e6-407">Dies ist in Kombination mit der Tokenersetzung stark.</span><span class="sxs-lookup"><span data-stu-id="a45e6-407">This is powerful combined with token replacement.</span></span> <span data-ttu-id="a45e6-408">Tokenersetzung gilt auch für Routennamen, die durch Attributrouten definiert werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-408">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="a45e6-409">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generiert für jede Aktion einen eindeutigen Routennamen:</span><span class="sxs-lookup"><span data-stu-id="a45e6-409">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="a45e6-410">Tokenersetzung gilt auch für Routennamen, die durch Attributrouten definiert werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-410">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="a45e6-411">generiert einen eindeutigen Routennamen für jede Aktion.</span><span class="sxs-lookup"><span data-stu-id="a45e6-411">generates a unique route name for each action.</span></span>

<span data-ttu-id="a45e6-412">Damit das Trennzeichen `[` oder `]` der Tokenersetzungs-Literalzeichenfolge bei einem Abgleich gefunden wird, muss es doppelt vorhanden sein (`[[` oder `]]`), was einem Escapezeichen entspricht.</span><span class="sxs-lookup"><span data-stu-id="a45e6-412">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="a45e6-413">Verwenden eines Parametertransformators zum Anpassen der Tokenersetzung</span><span class="sxs-lookup"><span data-stu-id="a45e6-413">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="a45e6-414">Die Tokenersetzung kann mit einem Parametertransformator angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-414">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="a45e6-415">Ein Parametertransformator implementiert <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> und wandelt den Wert der Parameter um.</span><span class="sxs-lookup"><span data-stu-id="a45e6-415">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="a45e6-416">Beispielsweise ändert ein benutzerdefinierter `SlugifyParameterTransformer`-parametertransformator den Wert für `SubscriptionManagement` Route in `subscription-management`:</span><span class="sxs-lookup"><span data-stu-id="a45e6-416">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="a45e6-417">Die <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> ist eine Anwendungsmodellkonvention, die Folgendes ausführt:</span><span class="sxs-lookup"><span data-stu-id="a45e6-417">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="a45e6-418">Wendet einen angegebenen Parametertransformator auf alle Attributrouten in der App an.</span><span class="sxs-lookup"><span data-stu-id="a45e6-418">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="a45e6-419">Passt die Tokenwerte für Attributrouten bei ihrer Ersetzung an.</span><span class="sxs-lookup"><span data-stu-id="a45e6-419">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="a45e6-420">Die vorangehende `ListAll` Methode entspricht `/subscription-management/list-all`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-420">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="a45e6-421">Die `RouteTokenTransformerConvention` wird als Option in `ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="a45e6-421">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="a45e6-422">Die Definition von Slug finden Sie unter [MDN-Webdokumentation zu Slug](https://developer.mozilla.org/docs/Glossary/Slug) .</span><span class="sxs-lookup"><span data-stu-id="a45e6-422">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="a45e6-423">Mehrere Attribut Routen</span><span class="sxs-lookup"><span data-stu-id="a45e6-423">Multiple attribute routes</span></span>

<span data-ttu-id="a45e6-424">Attributrouting unterstützt das Definieren mehrerer Routen, die zu derselben Aktion führen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-424">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="a45e6-425">Die häufigste Verwendung hierfür ist, das Verhalten der herkömmlichen Standardroute zu imitieren, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="a45e6-425">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="a45e6-426">Wenn Sie mehrere Routen Attribute auf dem Controller platzieren, bedeutet dies, dass jede mit den Routen Attributen der Aktionsmethoden kombiniert wird:</span><span class="sxs-lookup"><span data-stu-id="a45e6-426">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="a45e6-427">Alle [http-Verb](#verb) Routen Einschränkungen implementieren `IActionConstraint`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-427">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="a45e6-428">Wenn mehrere Routen Attribute, die <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> implementieren, in eine Aktion eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="a45e6-428">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="a45e6-429">Jede Aktions Einschränkung kombiniert mit der auf den Controller angewendeten Routen Vorlage.</span><span class="sxs-lookup"><span data-stu-id="a45e6-429">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="a45e6-430">Die Verwendung mehrerer Routen für Aktionen mag hilfreich und leistungsstark sein. es ist besser, den URL-Bereich ihrer App als "Basic" und "klar" festzuhalten.</span><span class="sxs-lookup"><span data-stu-id="a45e6-430">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="a45e6-431">Verwenden Sie mehrere Routen für Aktionen **nur** bei Bedarf, z. b. zur Unterstützung vorhandener Clients.</span><span class="sxs-lookup"><span data-stu-id="a45e6-431">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="a45e6-432">Angeben von optionalen Attributroutenparametern, Standardwerten und Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="a45e6-432">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="a45e6-433">Attributrouten unterstützen dieselbe Inline-Syntax wie herkömmliche Routen, um optionale Parameter, Standardwerte und Einschränkungen anzugeben.</span><span class="sxs-lookup"><span data-stu-id="a45e6-433">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="a45e6-434">Im vorangehenden Code wird `[HttpPost("product/{id:int}")]` eine Routen Einschränkung anwenden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-434">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="a45e6-435">Die `ProductsController.ShowProduct` Aktion wird nur mit URL-Pfaden wie `/product/3`abgeglichen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-435">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="a45e6-436">Der Weiterleitungs Vorlagen Teil `{id:int}` das Segment auf ganze Zahlen beschränkt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-436">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="a45e6-437">Eine ausführliche Beschreibung der Syntax der Routenvorlage finden Sie unter [Routenvorlagenreferenz](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="a45e6-437">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="a45e6-438">Benutzerdefinierte Routen Attribute mit iroutetemplateprovider</span><span class="sxs-lookup"><span data-stu-id="a45e6-438">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="a45e6-439">Alle [Routen Attribute](#rt) implementieren <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span><span class="sxs-lookup"><span data-stu-id="a45e6-439">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="a45e6-440">Die ASP.net Core Laufzeit:</span><span class="sxs-lookup"><span data-stu-id="a45e6-440">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="a45e6-441">Sucht nach Attributen in Controller Klassen und Aktionsmethoden, wenn die APP gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-441">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="a45e6-442">Verwendet die Attribute, die `IRouteTemplateProvider` implementieren, um den anfänglichen Satz von Routen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-442">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="a45e6-443">Implementieren Sie `IRouteTemplateProvider`, um benutzerdefinierte Routen Attribute zu definieren.</span><span class="sxs-lookup"><span data-stu-id="a45e6-443">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="a45e6-444">Jeder `IRouteTemplateProvider` lässt Sie eine einzelne Route mit einer benutzerdefinierten Routenvorlage, Reihenfolge und einem benutzerdefinierten Namen definieren:</span><span class="sxs-lookup"><span data-stu-id="a45e6-444">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="a45e6-445">Die vorangehende `Get` Methode gibt `Order = 2, Template = api/MyTestApi`zurück.</span><span class="sxs-lookup"><span data-stu-id="a45e6-445">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="a45e6-446">Verwenden des Anwendungs Modells zum Anpassen von Attribut Routen</span><span class="sxs-lookup"><span data-stu-id="a45e6-446">Use application model to customize attribute routes</span></span>

<span data-ttu-id="a45e6-447">Das Anwendungsmodell:</span><span class="sxs-lookup"><span data-stu-id="a45e6-447">The application model:</span></span>

* <span data-ttu-id="a45e6-448">Ist ein Objektmodell, das beim Start erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-448">Is an object model created at startup.</span></span>
* <span data-ttu-id="a45e6-449">Enthält alle Metadaten, die von ASP.net Core verwendet werden, um die Aktionen in einer APP weiterzuleiten und auszuführen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-449">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="a45e6-450">Das Anwendungsmodell umfasst alle Daten, die von Routen Attributen gesammelt werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-450">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="a45e6-451">Die Daten aus Routen Attributen werden von der `IRouteTemplateProvider`-Implementierung bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-451">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="a45e6-452">Geregelt</span><span class="sxs-lookup"><span data-stu-id="a45e6-452">Conventions:</span></span>

* <span data-ttu-id="a45e6-453">Kann geschrieben werden, um das Anwendungsmodell so zu ändern, dass das Routing Verhalten angepasst wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-453">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="a45e6-454">Werden beim APP-Start gelesen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-454">Are read at app startup.</span></span>

<span data-ttu-id="a45e6-455">Dieser Abschnitt zeigt ein einfaches Beispiel für das Anpassen des Routings mithilfe des Anwendungs Modells.</span><span class="sxs-lookup"><span data-stu-id="a45e6-455">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="a45e6-456">Der folgende Code bewirkt, dass Routen ungefähr mit der Ordnerstruktur des Projekts übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-456">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="a45e6-457">Der folgende Code verhindert, dass die `namespace` Konvention auf Controller angewendet wird, die Attribut weitergeleitet werden:</span><span class="sxs-lookup"><span data-stu-id="a45e6-457">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="a45e6-458">Der folgende Controller verwendet beispielsweise nicht `NamespaceRoutingConvention`:</span><span class="sxs-lookup"><span data-stu-id="a45e6-458">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="a45e6-459">Die `NamespaceRoutingConvention.Apply`-Methode:</span><span class="sxs-lookup"><span data-stu-id="a45e6-459">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="a45e6-460">Führt keine Aktion aus, wenn der Controller Attribut weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-460">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="a45e6-461">Legt die Controller Vorlage basierend auf dem `namespace`fest, wobei die Basis `namespace` entfernt wurde.</span><span class="sxs-lookup"><span data-stu-id="a45e6-461">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="a45e6-462">Der `NamespaceRoutingConvention` kann in `Startup.ConfigureServices`angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="a45e6-462">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="a45e6-463">Sehen Sie sich beispielsweise den folgenden Controller an:</span><span class="sxs-lookup"><span data-stu-id="a45e6-463">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="a45e6-464">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="a45e6-464">In the preceding code:</span></span>

* <span data-ttu-id="a45e6-465">Der Basis `namespace` ist `My.Application`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-465">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="a45e6-466">Der vollständige Name des vorangehenden Controllers ist `My.Application.Admin.Controllers.UsersController`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-466">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="a45e6-467">Der `NamespaceRoutingConvention` legt die Controller Vorlage auf `Admin/Controllers/Users/[action]/{id?`fest.</span><span class="sxs-lookup"><span data-stu-id="a45e6-467">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="a45e6-468">Der `NamespaceRoutingConvention` kann auch als Attribut auf einem Controller angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="a45e6-468">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="a45e6-469">Gemischtes Routing: Attributrouting vs. herkömmliches Routing</span><span class="sxs-lookup"><span data-stu-id="a45e6-469">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="a45e6-470">ASP.net Core-Apps können die Verwendung von herkömmlichem Routing und Attribut Routing vermischen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-470">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="a45e6-471">In der Regel werden herkömmliche Routen für Controller verwendet, die für Browser-HTML-Seiten gedacht sind, und das Attributrouting für Controller für REST-APIs.</span><span class="sxs-lookup"><span data-stu-id="a45e6-471">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="a45e6-472">Aktionen werden entweder herkömmlich oder über Attribute zugeordnet,</span><span class="sxs-lookup"><span data-stu-id="a45e6-472">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="a45e6-473">d.h., dass eine Route auf dem Controller oder der Aktion platziert wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-473">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="a45e6-474">Aktionen, die Attributrouten definieren, können nicht über die herkömmliche Routen und umgekehrt erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-474">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="a45e6-475">Alle **Routen Attribute** auf dem Controller machen **alle** Aktionen im Controller Attribut weiter.</span><span class="sxs-lookup"><span data-stu-id="a45e6-475">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="a45e6-476">Bei Attribut Routing und herkömmlichem Routing wird dieselbe Routing-Engine verwendet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-476">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="a45e6-477">URL-Generierung und Ambient-Werte</span><span class="sxs-lookup"><span data-stu-id="a45e6-477">URL Generation and ambient values</span></span>

<span data-ttu-id="a45e6-478">Apps können Routing-URL-Generierungs Features verwenden, um URL-Links zu Aktionen zu generieren.</span><span class="sxs-lookup"><span data-stu-id="a45e6-478">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="a45e6-479">Durch das Erstellen von URLs werden hart codierte URLs eliminiert, sodass der Code stabiler und verwallegbar wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-479">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="a45e6-480">Dieser Abschnitt konzentriert sich auf die Funktionen der URL-Generierung, die von MVC bereitgestellt werden</span><span class="sxs-lookup"><span data-stu-id="a45e6-480">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="a45e6-481">Eine detaillierte Beschreibung der URL-Generierung finden Sie unter [Routing in ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="a45e6-481">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="a45e6-482">Die <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>-Schnittstelle ist das zugrundeliegende Element der Infrastruktur zwischen MVC und dem Routing für die URL-Generierung.</span><span class="sxs-lookup"><span data-stu-id="a45e6-482">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="a45e6-483">Eine Instanz von `IUrlHelper` ist über die `Url`-Eigenschaft in Controllern, Ansichten und Ansichts Komponenten verfügbar.</span><span class="sxs-lookup"><span data-stu-id="a45e6-483">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="a45e6-484">Im folgenden Beispiel wird die `IUrlHelper`-Schnittstelle über die `Controller.Url`-Eigenschaft verwendet, um eine URL zu einer anderen Aktion zu generieren.</span><span class="sxs-lookup"><span data-stu-id="a45e6-484">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="a45e6-485">Wenn die APP die herkömmliche Standardroute verwendet, ist der Wert der `url`-Variablen die URL-Pfad Zeichenfolge `/UrlGeneration/Destination`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-485">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="a45e6-486">Dieser URL-Pfad wird durch das Routing erstellt, indem Folgendes kombiniert wird:</span><span class="sxs-lookup"><span data-stu-id="a45e6-486">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="a45e6-487">Die Routen Werte aus der aktuellen Anforderung, die als **Umgebungs Werte**bezeichnet werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-487">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="a45e6-488">Die an `Url.Action` weiter gegebenen Werte und ersetzen in der Routen Vorlage durch diese Werte:</span><span class="sxs-lookup"><span data-stu-id="a45e6-488">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="a45e6-489">Der Wert eines jeden Routenparameters wird in der Routenvorlage durch die entsprechenden Namen mit den Werten und Umgebungswerten ersetzt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-489">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="a45e6-490">Ein Routen Parameter, der keinen Wert hat, kann folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="a45e6-490">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="a45e6-491">Verwenden Sie ggf. einen Standardwert.</span><span class="sxs-lookup"><span data-stu-id="a45e6-491">Use a default value if it has one.</span></span>
* <span data-ttu-id="a45e6-492">Sie sollten übersprungen werden, wenn Sie optional ist.</span><span class="sxs-lookup"><span data-stu-id="a45e6-492">Be skipped if it's optional.</span></span> <span data-ttu-id="a45e6-493">Beispielsweise wird die `id` aus der Routen Vorlage `{controller}/{action}/{id?}`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-493">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="a45e6-494">Die URL-Generierung schlägt fehl, wenn für einen erforderlichen Routen Parameter kein entsprechender Wert vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="a45e6-494">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="a45e6-495">Wenn die URL-Generierung für eine Route fehlschlägt, wird die nächste Route ausprobiert, bis alle Routen getestet wurden oder eine Übereinstimmung gefunden wurde.</span><span class="sxs-lookup"><span data-stu-id="a45e6-495">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="a45e6-496">Im vorangehenden Beispiel `Url.Action` wird von [herkömmlichem Routing](#cr)ausgegangen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-496">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="a45e6-497">Die URL-Generierung funktioniert ähnlich wie das [Attribut Routing](#ar), obwohl sich die Konzepte unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-497">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="a45e6-498">Mit herkömmlichem Routing:</span><span class="sxs-lookup"><span data-stu-id="a45e6-498">With conventional routing:</span></span>

* <span data-ttu-id="a45e6-499">Die Routen Werte werden zum Erweitern einer Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-499">The route values are used to expand a template.</span></span>
* <span data-ttu-id="a45e6-500">Die Routen Werte für `controller` und `action` werden normalerweise in dieser Vorlage angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-500">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="a45e6-501">Dies funktioniert, da die mit dem Routing übereinstimmenden URLs einer Konvention entsprechen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-501">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="a45e6-502">Im folgenden Beispiel wird das Attribut Routing verwendet:</span><span class="sxs-lookup"><span data-stu-id="a45e6-502">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="a45e6-503">Die `Source` Aktion im vorangehenden Code generiert `custom/url/to/destination`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-503">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="a45e6-504"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> wurde in ASP.net Core 3,0 als Alternative zu `IUrlHelper`hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-504"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="a45e6-505">`LinkGenerator` bietet ähnliche, aber flexiblere Funktionen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-505">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="a45e6-506">Jede andere Methode auf `IUrlHelper` verfügt auch über eine entsprechende Familie von Methoden auf `LinkGenerator`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-506">Each other the methods on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="a45e6-507">Generieren von URLs nach Aktionsnamen</span><span class="sxs-lookup"><span data-stu-id="a45e6-507">Generating URLs by action name</span></span>

<span data-ttu-id="a45e6-508">" [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)", " [Linkgenerator. getpathbyaction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)" und alle zugehörigen über Ladungen sind so konzipiert, dass der Ziel Endpunkt durch Angabe eines Controller namens und Aktions namens generiert wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-508">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="a45e6-509">Wenn Sie `Url.Action`verwenden, werden die aktuellen Routen Werte für `controller` und `action` von der Laufzeit bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="a45e6-509">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="a45e6-510">Der Wert von `controller` und `action` ist Teil der [Ambient-Werte](#ambient) und-Werte.</span><span class="sxs-lookup"><span data-stu-id="a45e6-510">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="a45e6-511">Die-Methode `Url.Action` verwendet immer die aktuellen Werte `action` und `controller` und generiert einen URL-Pfad, der zur aktuellen Aktion weiterleitet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-511">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="a45e6-512">Beim Routing werden die Werte in Ambient-Werten verwendet, um Informationen einzugeben, die beim Erstellen einer URL nicht bereitgestellt wurden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-512">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="a45e6-513">Sehen Sie sich eine Route wie `{a}/{b}/{c}/{d}` mit Ambient-Werten an `{ a = Alice, b = Bob, c = Carol, d = David }`:</span><span class="sxs-lookup"><span data-stu-id="a45e6-513">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="a45e6-514">Das Routing verfügt über genügend Informationen, um eine URL ohne zusätzliche Werte zu generieren.</span><span class="sxs-lookup"><span data-stu-id="a45e6-514">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="a45e6-515">Das Routing verfügt über genügend Informationen, da alle Routen Parameter über einen Wert verfügen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-515">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="a45e6-516">Wenn der Wert `{ d = Donovan }` hinzugefügt wird:</span><span class="sxs-lookup"><span data-stu-id="a45e6-516">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="a45e6-517">Der Wert `{ d = David }` wird ignoriert.</span><span class="sxs-lookup"><span data-stu-id="a45e6-517">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="a45e6-518">Der generierte URL-Pfad ist `Alice/Bob/Carol/Donovan`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-518">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="a45e6-519">**Warnung**: URL-Pfade sind hierarchisch.</span><span class="sxs-lookup"><span data-stu-id="a45e6-519">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="a45e6-520">Im vorherigen Beispiel, wenn der Wert `{ c = Cheryl }` hinzugefügt wird:</span><span class="sxs-lookup"><span data-stu-id="a45e6-520">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="a45e6-521">Beide `{ c = Carol, d = David }` Werte werden ignoriert.</span><span class="sxs-lookup"><span data-stu-id="a45e6-521">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="a45e6-522">Es ist kein Wert für `d` mehr vorhanden, und die URL-Generierung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="a45e6-522">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="a45e6-523">Die gewünschten Werte `c` und `d` müssen angegeben werden, um eine URL zu generieren.</span><span class="sxs-lookup"><span data-stu-id="a45e6-523">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="a45e6-524">Möglicherweise erwarten Sie dieses Problem mit der Standardroute `{controller}/{action}/{id?}`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-524">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="a45e6-525">Dieses Problem tritt in der Praxis selten auf, weil `Url.Action` immer explizit einen `controller` und `action` Wert angibt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-525">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="a45e6-526">Mehrere über Ladungen von [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) nehmen ein Routen Werte Objekt an, um Werte für andere Routen Parameter als `controller` und `action`bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-526">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="a45e6-527">Das Routen Werte Objekt wird häufig mit `id`verwendet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-527">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="a45e6-528">Beispielsweise `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-528">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="a45e6-529">Das Routen Werte Objekt:</span><span class="sxs-lookup"><span data-stu-id="a45e6-529">The route values object:</span></span>

* <span data-ttu-id="a45e6-530">Gemäß der Konvention ist in der Regel ein Objekt des anonymen Typs.</span><span class="sxs-lookup"><span data-stu-id="a45e6-530">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="a45e6-531">Kann eine `IDictionary<>` oder [poco](https://wikipedia.org/wiki/Plain_old_CLR_object)sein).</span><span class="sxs-lookup"><span data-stu-id="a45e6-531">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="a45e6-532">Alle zusätzlichen Routenwerte, die keinen Routenparametern zugeordnet sind, werden in der Abfragezeichenfolge platziert.</span><span class="sxs-lookup"><span data-stu-id="a45e6-532">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="a45e6-533">Der vorangehende Code generiert `/Products/Buy/17?color=red`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-533">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="a45e6-534">Mit dem folgenden Code wird eine absolute URL generiert:</span><span class="sxs-lookup"><span data-stu-id="a45e6-534">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="a45e6-535">Um einen absolute URL zu erstellen, verwenden Sie eine der folgenden Aktionen:</span><span class="sxs-lookup"><span data-stu-id="a45e6-535">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="a45e6-536">Eine Überladung, die eine `protocol`akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="a45e6-536">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="a45e6-537">Beispielsweise der vorangehende Code.</span><span class="sxs-lookup"><span data-stu-id="a45e6-537">For example, the preceding code.</span></span>
* <span data-ttu-id="a45e6-538">[Linkgenerator. geturibyaction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), wodurch standardmäßig absolute URIs generiert werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-538">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="a45e6-539">Generieren von URLs nach Route</span><span class="sxs-lookup"><span data-stu-id="a45e6-539">Generate URLs by route</span></span>

<span data-ttu-id="a45e6-540">Der vorangehende Code hat das Erzeugen einer URL durch Übergeben des Controllers und des Aktions namens veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="a45e6-540">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="a45e6-541">`IUrlHelper` bietet auch die [URL. RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) -Familie der Methoden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-541">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="a45e6-542">Diese Methoden ähneln [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), kopieren jedoch nicht die aktuellen Werte `action` und `controller` in die Routen Werte.</span><span class="sxs-lookup"><span data-stu-id="a45e6-542">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="a45e6-543">Die häufigste Verwendung von `Url.RouteUrl`:</span><span class="sxs-lookup"><span data-stu-id="a45e6-543">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="a45e6-544">Gibt einen Routennamen zum Generieren der URL an.</span><span class="sxs-lookup"><span data-stu-id="a45e6-544">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="a45e6-545">In der Regel wird kein Controller-oder Aktionsname angegeben.</span><span class="sxs-lookup"><span data-stu-id="a45e6-545">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="a45e6-546">Die folgende Razor-Datei generiert einen HTML-Link zum `Destination_Route`:</span><span class="sxs-lookup"><span data-stu-id="a45e6-546">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a><span data-ttu-id="a45e6-547">Generieren von URLs in HTML und Razor</span><span class="sxs-lookup"><span data-stu-id="a45e6-547">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="a45e6-548"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> stellt die <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> Methoden [HTML. BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) und [HTML. Action Link](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) bereit, um `<form>` und `<a>` Elemente zu generieren.</span><span class="sxs-lookup"><span data-stu-id="a45e6-548"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="a45e6-549">Diese Methoden verwenden die [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) -Methode, um eine URL zu generieren, und Sie akzeptieren ähnliche Argumente.</span><span class="sxs-lookup"><span data-stu-id="a45e6-549">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="a45e6-550">Die `Url.RouteUrl`-Begleiter für `HtmlHelper` sind `Html.BeginRouteForm` und `Html.RouteLink`, die ähnliche Funktionen aufweisen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-550">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="a45e6-551">Taghilfsprogramme generieren URLs mit den Taghilfsprogrammen `form` und `<a>`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-551">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="a45e6-552">Beide implementieren mit `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-552">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="a45e6-553">Weitere Informationen finden Sie [unter taghilfsprogramme in Formularen](xref:mvc/views/working-with-forms) .</span><span class="sxs-lookup"><span data-stu-id="a45e6-553">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="a45e6-554">In Ansichten ist `IUrlHelper` über die `Url`-Eigenschaft für jede Ad-hoc-URL-Generierung verfügbar, die keine der oben genannten ist.</span><span class="sxs-lookup"><span data-stu-id="a45e6-554">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="a45e6-555">URL-Generierung in Aktions Ergebnissen</span><span class="sxs-lookup"><span data-stu-id="a45e6-555">URL generation in Action Results</span></span>

<span data-ttu-id="a45e6-556">In den vorangehenden Beispielen wurde die Verwendung `IUrlHelper` in einem Controller gezeigt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-556">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="a45e6-557">Die häufigste Verwendung eines Controllers ist das Generieren einer URL als Teil eines Aktions Ergebnisses.</span><span class="sxs-lookup"><span data-stu-id="a45e6-557">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="a45e6-558">Die Basisklassen <xref:Microsoft.AspNetCore.Mvc.ControllerBase> und <xref:Microsoft.AspNetCore.Mvc.Controller> stellen Hilfsmethoden für Aktionsergebnisse bereit, die auf eine andere Aktionen verweisen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-558">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="a45e6-559">Eine typische Verwendung besteht darin, nach Annahme der Benutzereingabe eine Umleitung durchzusetzen:</span><span class="sxs-lookup"><span data-stu-id="a45e6-559">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="a45e6-560">Die Aktions Ergebnis-Factorymethoden wie <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> und <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> folgen einem ähnlichen Muster wie die Methoden auf `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-560">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="a45e6-561">Sonderfall für dedizierte herkömmliche Routen</span><span class="sxs-lookup"><span data-stu-id="a45e6-561">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="a45e6-562">[Herkömmliches Routing](#cr) kann eine besondere Art von Routen Definition verwenden, die als [dedizierte konventionelle Route](#dcr)bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-562">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="a45e6-563">Im folgenden Beispiel ist die Route mit dem Namen `blog` eine dedizierte konventionelle Route:</span><span class="sxs-lookup"><span data-stu-id="a45e6-563">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="a45e6-564">Mit den vorangehenden Routen Definitionen generiert `Url.Action("Index", "Home")` den URL-Pfad `/` mithilfe der `default` Route, aber warum?</span><span class="sxs-lookup"><span data-stu-id="a45e6-564">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="a45e6-565">Man könnte meinen, dass die Routenwerte `{ controller = Home, action = Index }` ausreichen würden, um eine URL mithilfe von `blog` zu generieren, und das Ergebnis wäre `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-565">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="a45e6-566">[Dedizierte herkömmliche Routen](#dcr) basieren auf einem besonderen Verhalten von Standardwerten, die über keinen entsprechenden Routen Parameter verfügen, der verhindert, dass die Route mit der URL-Generierung zu [gierig](xref:fundamentals/routing#greedy) wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-566">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="a45e6-567">In diesem Fall sind die Standardwerte `{ controller = Blog, action = Article }`, und weder `controller` noch `action` werden als Routenparameter verwendet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-567">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="a45e6-568">Wenn das Routing die URL-Generierung ausführt, müssen die angegebenen Werte mit den Standardwerten übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-568">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="a45e6-569">Die URL-Generierung mithilfe von `blog` schlägt fehl, da die Werte `{ controller = Home, action = Index }` nicht `{ controller = Blog, action = Article }`entsprechen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-569">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="a45e6-570">Routing greift dann wieder auf `default` zurück, was erfolgreich ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-570">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="a45e6-571">Bereiche</span><span class="sxs-lookup"><span data-stu-id="a45e6-571">Areas</span></span>

<span data-ttu-id="a45e6-572">[Bereiche](xref:mvc/controllers/areas) sind ein MVC-Feature, das verwendet wird, um verwandte Funktionen in einer Gruppe als separate zu organisieren:</span><span class="sxs-lookup"><span data-stu-id="a45e6-572">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="a45e6-573">Routing Namespace für Controller Aktionen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-573">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="a45e6-574">Ordnerstruktur für Ansichten.</span><span class="sxs-lookup"><span data-stu-id="a45e6-574">Folder structure for views.</span></span>

<span data-ttu-id="a45e6-575">Die Verwendung von Bereichen ermöglicht es einer APP, mehrere Controller mit dem gleichen Namen zu verwenden, solange Sie über unterschiedliche Bereiche verfügen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-575">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="a45e6-576">Mithilfe von Bereichen wird außerdem eine Hierarchie erstellt, damit das Routing durch Hinzufügen eines anderen Routenparameters ausgeführt werden kann: `area` zu `controller` und `action`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-576">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="a45e6-577">In diesem Abschnitt wird erläutert, wie das Routing mit Bereichen interagiert.</span><span class="sxs-lookup"><span data-stu-id="a45e6-577">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="a45e6-578">Ausführliche Informationen zur Verwendung von Bereichen mit Ansichten finden Sie unter [Bereiche](xref:mvc/controllers/areas) .</span><span class="sxs-lookup"><span data-stu-id="a45e6-578">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="a45e6-579">Im folgenden Beispiel wird MVC für die Verwendung der herkömmlichen Standardroute und einer `area` Route für eine `area` mit dem Namen `Blog`konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="a45e6-579">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="a45e6-580">Im vorangehenden Code wird <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> aufgerufen, um die `"blog_route"`zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-580">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="a45e6-581">Der zweite Parameter, `"Blog"`, ist der Bereichs Name.</span><span class="sxs-lookup"><span data-stu-id="a45e6-581">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="a45e6-582">Beim Abgleichen eines URL-Pfads wie `/Manage/Users/AddUser`generiert die `"blog_route"` Route die Routen Werte `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-582">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="a45e6-583">Der `area` Routen Wert wird durch einen Standardwert für `area`erzeugt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-583">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="a45e6-584">Die von `MapAreaControllerRoute` erstellte Route entspricht Folgendem:</span><span class="sxs-lookup"><span data-stu-id="a45e6-584">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="a45e6-585">`MapAreaControllerRoute` erstellt mit einem Standardwert und einer Einschränkung für `area` sowie mit dem bereitgestellten Bereichsnamen (in diesem Fall `Blog`) eine Route.</span><span class="sxs-lookup"><span data-stu-id="a45e6-585">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="a45e6-586">Der Standardwert stellt sicher, dass die Route immer `{ area = Blog, ... }` erzeugt, die Einschränkung erfordert den Wert `{ area = Blog, ... }` für URL-Generierung.</span><span class="sxs-lookup"><span data-stu-id="a45e6-586">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="a45e6-587">Beim herkömmlichen Routing ist die Reihenfolge wichtig.</span><span class="sxs-lookup"><span data-stu-id="a45e6-587">Conventional routing is order-dependent.</span></span> <span data-ttu-id="a45e6-588">Im Allgemeinen sollten Routen mit Bereichen früher platziert werden, da Sie spezifischer als Routen ohne Bereich sind.</span><span class="sxs-lookup"><span data-stu-id="a45e6-588">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="a45e6-589">Im vorherigen Beispiel `{ area = Blog, controller = Users, action = AddUser }` die Routen Werte der folgenden Aktion entsprechen:</span><span class="sxs-lookup"><span data-stu-id="a45e6-589">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="a45e6-590">Das [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) -Attribut bezeichnet einen Controller als Teil eines Bereichs.</span><span class="sxs-lookup"><span data-stu-id="a45e6-590">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="a45e6-591">Dieser Controller befindet sich im `Blog` Bereich.</span><span class="sxs-lookup"><span data-stu-id="a45e6-591">This controller is in the `Blog` area.</span></span> <span data-ttu-id="a45e6-592">Controller ohne ein `[Area]` Attribut sind keine Elemente eines Bereichs und Stimmen **nicht** mit dem Wert der `area` Route durch Routing ab.</span><span class="sxs-lookup"><span data-stu-id="a45e6-592">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="a45e6-593">Im folgenden Beispiel kann nur der erste aufgelistete Controller die Routenwerte `{ area = Blog, controller = Users, action = AddUser }` abgleichen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-593">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="a45e6-594">Der Namespace der einzelnen Controller wird hier aus Gründen der Vollständigkeit angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-594">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="a45e6-595">Wenn die vorangehenden Controller denselben Namespace verwenden, wird ein Compilerfehler generiert.</span><span class="sxs-lookup"><span data-stu-id="a45e6-595">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="a45e6-596">Klassennamespaces haben keine Auswirkungen auf das MVC-Routing.</span><span class="sxs-lookup"><span data-stu-id="a45e6-596">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="a45e6-597">Die ersten beiden Controller gehören zu Bereichen und werden können nur abgleichen, wenn ihr jeweiliger Bereichsname vom `area`-Routenwert bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-597">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="a45e6-598">Der dritte Controller gehört keinem Bereich an und kann nur abgleichen, wenn vom Routing kein Wert für `area` bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-598">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="a45e6-599">Im Hinblick auf das Erkennen *keines Werts* hat die Abwesenheit des `area`-Werts dieselben Auswirkungen, wie wenn der Wert für `area` 0 (null) oder eine leere Zeichenfolge wäre.</span><span class="sxs-lookup"><span data-stu-id="a45e6-599">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="a45e6-600">Wenn eine Aktion in einem Bereich ausgeführt wird, ist der Routen Wert für `area` als [Ambient-Wert](#ambient) für das Routing zur URL-Generierung verfügbar.</span><span class="sxs-lookup"><span data-stu-id="a45e6-600">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="a45e6-601">Das bedeutet, dass Bereiche bei der URL-Generierung wie im folgenden Beispiel dargestellt standardmäßig *beständig* sind.</span><span class="sxs-lookup"><span data-stu-id="a45e6-601">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="a45e6-602">Der folgende Code generiert eine URL zum `/Zebra/Users/AddUser`:</span><span class="sxs-lookup"><span data-stu-id="a45e6-602">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="a45e6-603">Aktions Definition</span><span class="sxs-lookup"><span data-stu-id="a45e6-603">Action definition</span></span>

<span data-ttu-id="a45e6-604">Öffentliche Methoden auf einem Controller, außer denen mit dem [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) -Attribut, sind Aktionen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-604">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="a45e6-605">Beispielcode</span><span class="sxs-lookup"><span data-stu-id="a45e6-605">Sample code</span></span>

 * <span data-ttu-id="a45e6-606">Die [mydisplayrouteinfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) -Methode ist im [Beispiel Download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) enthalten und wird zum Anzeigen von Routing Informationen verwendet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-606">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>
* <span data-ttu-id="a45e6-607">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a45e6-607">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a45e6-608">ASP.NET Core MVC verwendet [Routing-Middleware](xref:fundamentals/middleware/index), um die URLs der eingehenden Anforderungen abzugleichen und sie Aktionen zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-608">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="a45e6-609">Routen werden im Startcode oder in Attributen definiert</span><span class="sxs-lookup"><span data-stu-id="a45e6-609">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="a45e6-610">und beschreiben, wie URL-Pfade Aktionen zugeordnet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-610">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="a45e6-611">Sie werden auch dazu verwendet, um URLs (für Links) zu generieren, die in Antworten gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-611">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="a45e6-612">Aktionen werden entweder herkömmlich oder über Attribute zugeordnet,</span><span class="sxs-lookup"><span data-stu-id="a45e6-612">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="a45e6-613">d.h., dass eine Route auf dem Controller oder der Aktion platziert wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-613">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="a45e6-614">Weitere Informationen finden Sie im Abschnitt [Gemischtes Routing](#routing-mixed-ref-label).</span><span class="sxs-lookup"><span data-stu-id="a45e6-614">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="a45e6-615">In diesem Artikel werden die Schnittstellen zwischen MVC und Routing und die Art und Weise erläutert, wie normale MVC-Apps Routingfeatures verwenden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-615">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="a45e6-616">Informationen zum erweiterten Routing finden Sie unter [Routing in ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="a45e6-616">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="a45e6-617">Einrichten der Routing-Middleware</span><span class="sxs-lookup"><span data-stu-id="a45e6-617">Setting up Routing Middleware</span></span>

<span data-ttu-id="a45e6-618">Ihre *Configure*-Methode kann Code wie diesen enthalten:</span><span class="sxs-lookup"><span data-stu-id="a45e6-618">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="a45e6-619">Innerhalb des Aufrufs von `UseMvc` wird mit `MapRoute` eine einzelne Route erstellt, die wir `default`-Route nennen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-619">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="a45e6-620">Die meisten MVC-Apps verwenden eine Route mit einer Vorlage, die der `default`-Route ähnelt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-620">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="a45e6-621">TDie Routenvorlage `"{controller=Home}/{action=Index}/{id?}"` kann einem URL-Pfad wie `/Products/Details/5` abgleichen und extrahiert die Routenwerte `{ controller = Products, action = Details, id = 5 }`, indem ein Token für den Pfad erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-621">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="a45e6-622">MVC versucht, einen Controller namens `ProductsController` zu suchen und die Aktion `Details` auszuführen:</span><span class="sxs-lookup"><span data-stu-id="a45e6-622">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="a45e6-623">In diesem Beispiel hat die Modellbindung den `id = 5`-Parameter mit dem Wert `id` beim Aufrufen dieser Aktion auf `5` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-623">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="a45e6-624">Weitere Informationen finden Sie unter [Modellbindung](../models/model-binding.md).</span><span class="sxs-lookup"><span data-stu-id="a45e6-624">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="a45e6-625">Verwenden der `default`-Route:</span><span class="sxs-lookup"><span data-stu-id="a45e6-625">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="a45e6-626">Die Routenvorlage:</span><span class="sxs-lookup"><span data-stu-id="a45e6-626">The route template:</span></span>

* <span data-ttu-id="a45e6-627">`{controller=Home}` definiert `Home` als Standard-`controller`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-627">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="a45e6-628">`{action=Index}` definiert `Index` als Standard-`action`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-628">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="a45e6-629">`{id?}` definiert `id` als optional.</span><span class="sxs-lookup"><span data-stu-id="a45e6-629">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="a45e6-630">Standardmäßige und optionale Routenparameter müssen nicht im URL-Pfad vorhanden sein, damit es eine Übereinstimmung gibt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-630">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="a45e6-631">Eine ausführliche Beschreibung der Syntax der Routenvorlage finden Sie unter [Routenvorlagenreferenz](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="a45e6-631">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="a45e6-632">`"{controller=Home}/{action=Index}/{id?}"` gleicht den URL-Pfad `/` ab und erzeugt die Routenwerte `{ controller = Home, action = Index }`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-632">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="a45e6-633">Die Werte für `controller` und `action` verwenden die Standardwerte. `id` erzeugt keine Werte, da kein entsprechendes Segment im URL-Pfad vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="a45e6-633">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="a45e6-634">MVC nutzt diese Routenwerte, um die `HomeController`- und `Index`-Aktion auszuwählen:</span><span class="sxs-lookup"><span data-stu-id="a45e6-634">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="a45e6-635">Mit dieser Controllerdefinition und dieser Routenvorlage wird die `HomeController.Index`-Aktion für jeden der folgenden URL-Pfade ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="a45e6-635">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="a45e6-636">Mit der Hilfsmethode `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="a45e6-636">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="a45e6-637">kann Folgendes ersetzt werden:</span><span class="sxs-lookup"><span data-stu-id="a45e6-637">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="a45e6-638">`UseMvc` und `UseMvcWithDefaultRoute` fügen der Middleware-Pipeline eine Instanz von `RouterMiddleware` hinzu.</span><span class="sxs-lookup"><span data-stu-id="a45e6-638">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="a45e6-639">MVC interagiert nicht direkt mit der Middleware und verarbeitet Anforderungen mithilfe von Routing.</span><span class="sxs-lookup"><span data-stu-id="a45e6-639">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="a45e6-640">MVC ist über eine Instanz von `MvcRouteHandler` mit den Routen verbunden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-640">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="a45e6-641">Der Code in `UseMvc` ähnelt Folgendem:</span><span class="sxs-lookup"><span data-stu-id="a45e6-641">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="a45e6-642">`UseMvc` definiert keine Routen direkt, sondern fügt der Routenauflistung einen Platzhalter für die `attribute`-Route hinzu.</span><span class="sxs-lookup"><span data-stu-id="a45e6-642">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="a45e6-643">Die Überladung `UseMvc(Action<IRouteBuilder>)` ermöglicht es Ihnen, Ihre eigenen Routen hinzuzufügen und unterstützt darüber hinaus das Routingattribut.</span><span class="sxs-lookup"><span data-stu-id="a45e6-643">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="a45e6-644">`UseMvc` und alle Varianten fügen einen Platzhalter für die Attributroute hinzu. Attributrouting ist immer verfügbar, unabhängig von der Konfiguration von `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-644">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="a45e6-645">`UseMvcWithDefaultRoute` definiert eine Standardroute und unterstützt Attributrouting.</span><span class="sxs-lookup"><span data-stu-id="a45e6-645">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="a45e6-646">Der Abschnitt [Attributrouting](#attribute-routing-ref-label) enthält weitere Informationen zu dem Thema.</span><span class="sxs-lookup"><span data-stu-id="a45e6-646">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="a45e6-647">Herkömmliches Routing</span><span class="sxs-lookup"><span data-stu-id="a45e6-647">Conventional routing</span></span>

<span data-ttu-id="a45e6-648">Die `default`-Route:</span><span class="sxs-lookup"><span data-stu-id="a45e6-648">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="a45e6-649">Der vorangehende Code ist ein Beispiel für ein herkömmliches Routing.</span><span class="sxs-lookup"><span data-stu-id="a45e6-649">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="a45e6-650">Dieser Stil wird als herkömmliches Routing bezeichnet, da er eine *Konvention* für URL-Pfade festlegt:</span><span class="sxs-lookup"><span data-stu-id="a45e6-650">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="a45e6-651">Das erste Pfad Segment wird dem Controller Namen zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-651">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="a45e6-652">Die zweite wird dem Aktions Namen zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-652">The second maps to the action name.</span></span>
* <span data-ttu-id="a45e6-653">Das dritte Segment wird für eine optionale `id`verwendet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-653">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="a45e6-654">`id` einer Modell Entität zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-654">`id` maps to a model entity.</span></span>

<span data-ttu-id="a45e6-655">Mit dieser `default`-Route wir der URL-Pfad `/Products/List` der `ProductsController.List`-Aktion und `/Blog/Article/17``BlogController.Article` zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-655">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="a45e6-656">Diese Zuordnung basiert **ausschließlich** auf den Controller- und Aktionsnamen und nicht auf Namespaces, Speicherorten für Quelldateien oder Methodenparametern.</span><span class="sxs-lookup"><span data-stu-id="a45e6-656">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="a45e6-657">Die Kombination aus herkömmlichem Routing und Standardrouting ermöglicht es Ihnen, Anwendungen schnell zu erstellen, ohne für jede definierte Aktion ein neues URL-Muster entwerfen zu müssen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-657">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="a45e6-658">Bei Anwendungen mit Aktionen im CRUD-Stil können einheitliche URLs auf allen Controllern dabei helfen, den Code zu vereinfachen und die UI vorhersehbarer zu machen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-658">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="a45e6-659">`id` wird von der Routenvorlage als optional definiert, was bedeutet, dass Ihre Aktionen ausgeführt werden, ohne dass die ID als Teil der URL bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-659">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="a45e6-660">Wenn `id` in der URL ausgelassen wird, wird es üblicherweise von der Modellbindung auf `0` festgelegt, und daher wird in der Datenbank keine Entität gefunden, die `id == 0` entspricht.</span><span class="sxs-lookup"><span data-stu-id="a45e6-660">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="a45e6-661">Mit dem Attributrouting können Sie präzise steuern, für welche Aktionen die ID erforderlich ist und für welche nicht.</span><span class="sxs-lookup"><span data-stu-id="a45e6-661">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="a45e6-662">Gemäß der Konvention enthält die Dokumentation optionale Parameter wie `id`, wenn sie wahrscheinlich in der richtigen Verwendung angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-662">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="a45e6-663">Mehrere Routen</span><span class="sxs-lookup"><span data-stu-id="a45e6-663">Multiple routes</span></span>

<span data-ttu-id="a45e6-664">Sie können mehrere Routen innerhalb von `UseMvc` hinzufügen, indem Sie weitere Aufrufe von `MapRoute` hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-664">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="a45e6-665">So können Sie mehrere Konventionen definieren oder herkömmlichen Routen hinzufügen, die einer bestimmten Aktion zugeordnet sind, z.B.:</span><span class="sxs-lookup"><span data-stu-id="a45e6-665">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="a45e6-666">Die `blog`-Route hier ist eine *dedizierte herkömmliche Route*, d.h., dass das herkömmliche Routingsystem verwendet wird, aber einer bestimmten Aktion zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="a45e6-666">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="a45e6-667">Da `controller` und `action` nicht als Parameter in der Routenvorlage vorkommen, können sie nur die Standardwerte haben. Daher wird diese Route immer der Aktion `BlogController.Article` zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-667">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="a45e6-668">Die Routen in der Routenauflistung sind geordnet und werden in der Reihenfolge verarbeitet, in der sie hinzugefügt wurden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-668">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="a45e6-669">In diesem Beispiel wird daher die Route `blog` vor der Route `default` überprüft.</span><span class="sxs-lookup"><span data-stu-id="a45e6-669">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="a45e6-670">*Dedizierte herkömmliche Routen* verwenden häufig **catch-all-** Routen Parameter wie `{*article}`, um den verbleibenden Teil des URL-Pfads zu erfassen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-670">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="a45e6-671">Dies kann eine Route „zu gierig“ machen, d.h., dass sie alle URLs abgleicht, die eigentlich von anderen Routen abgeglichen werden sollten.</span><span class="sxs-lookup"><span data-stu-id="a45e6-671">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="a45e6-672">Fügen Sie die „gierigen“ Routen weiter unten in die Routingtabelle ein, um dieses Problem zu beheben.</span><span class="sxs-lookup"><span data-stu-id="a45e6-672">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="a45e6-673">Fallback</span><span class="sxs-lookup"><span data-stu-id="a45e6-673">Fallback</span></span>

<span data-ttu-id="a45e6-674">Im Rahmen der Anforderungsverarbeitung überprüft MVC, ob mit den Routenwerten ein Controller und eine Aktion in Ihrer Anwendung gefunden werden können.</span><span class="sxs-lookup"><span data-stu-id="a45e6-674">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="a45e6-675">Falls die Routenwerte mit keiner Aktion übereinstimmen, gilt die Route nicht als Übereinstimmung, und die nächste Route wird überprüft.</span><span class="sxs-lookup"><span data-stu-id="a45e6-675">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="a45e6-676">Dabei spricht man von einem *Fallback*. Dieser Vorgang soll Szenarios vereinfachen, bei denen sich herkömmliche Routen überschneiden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-676">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="a45e6-677">Aktionen eindeutig zuordnen</span><span class="sxs-lookup"><span data-stu-id="a45e6-677">Disambiguating actions</span></span>

<span data-ttu-id="a45e6-678">Wenn zwei Aktionen beim Routing übereinstimmen, muss MVC beide analysieren und die beste auswählen oder eine Ausnahme auslösen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-678">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="a45e6-679">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a45e6-679">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="a45e6-680">Dieser Controller definiert zwei Aktionen, die mit dem URL-Pfad `/Products/Edit/17` übereinstimmen und die Daten `{ controller = Products, action = Edit, id = 17 }` weiterleiten.</span><span class="sxs-lookup"><span data-stu-id="a45e6-680">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="a45e6-681">Dies ist ein typisches Muster für MVC-Controller, in dem `Edit(int)` ein Formular zum Bearbeiten eines Produkts anzeigt und `Edit(int, Product)` das bereitgestellte Formular verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-681">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="a45e6-682">Um dies zu ermöglichen, muss MVC `Edit(int, Product)` auswählen, wenn die Anforderung HTTP-`POST` ist, und `Edit(int)`, wenn das HTTP-Verb ein anderes ist.</span><span class="sxs-lookup"><span data-stu-id="a45e6-682">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="a45e6-683">`HttpPostAttribute` (`[HttpPost]`) ist eine Implementierung von `IActionConstraint`, bei der die Aktion nur ausgewählt werden darf, wenn das HTTP-Verb `POST` ist.</span><span class="sxs-lookup"><span data-stu-id="a45e6-683">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="a45e6-684">Aufgrund des Vorhandenseins von `IActionConstraint` ist `Edit(int, Product)` die „bessere“ Übereinstimmung als `Edit(int)`, sodass `Edit(int, Product)` zuerst überprüft wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-684">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="a45e6-685">Benutzerdefinierte `IActionConstraint`-Implementierungen müssen Sie nur in speziellen Szenarios schreiben. Es ist jedoch wichtig, die Rolle von Attributen wie `HttpPostAttribute` zu kennen, denn ähnliche Attribute sind auch für andere HTTP-Verben definiert.</span><span class="sxs-lookup"><span data-stu-id="a45e6-685">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="a45e6-686">Beim herkömmlichen Routing nutzen Aktionen oft denselben Aktionsnamen, wenn sie Teil eines `show form -> submit form`-Workflows sind.</span><span class="sxs-lookup"><span data-stu-id="a45e6-686">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="a45e6-687">Die Vorteile dessen werden deutlicher, wenn Sie den Abschnitt [Verstehen von IActionConstraint](#understanding-iactionconstraint) gelesen haben.</span><span class="sxs-lookup"><span data-stu-id="a45e6-687">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="a45e6-688">Wenn mehrere Routen übereinstimmen und MVC die „beste“ nicht bestimmen kann, löst es eine `AmbiguousActionException` aus.</span><span class="sxs-lookup"><span data-stu-id="a45e6-688">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="a45e6-689">Routennamen</span><span class="sxs-lookup"><span data-stu-id="a45e6-689">Route names</span></span>

<span data-ttu-id="a45e6-690">Die Zeichenfolgen `"blog"` und `"default"` in den folgenden Beispielen sind Routennamen:</span><span class="sxs-lookup"><span data-stu-id="a45e6-690">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="a45e6-691">Der Routenname gibt der Route einen logischen Namen, damit die benannte Route bei der URL-Generierung verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="a45e6-691">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="a45e6-692">Dadurch wird die URL-Erstellung erheblich vereinfacht, obwohl die Reihenfolge der Routen die URL-Generierung verkomplizieren sollte.</span><span class="sxs-lookup"><span data-stu-id="a45e6-692">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="a45e6-693">Routennamen müssen anwendungsweit eindeutig sein.</span><span class="sxs-lookup"><span data-stu-id="a45e6-693">Route names must be unique application-wide.</span></span>

<span data-ttu-id="a45e6-694">Routennamen haben keine Auswirkung auf die URL-Zuordnung oder die Verarbeitung von Anforderungen. Sie dienen nur der URL-Generierung.</span><span class="sxs-lookup"><span data-stu-id="a45e6-694">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="a45e6-695">Unter [Routing](xref:fundamentals/routing) finden Sie weitere ausführliche Informationen zur URL-Generierung, einschließlich der Generierung in MVC-spezifischen Hilfsprogrammen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-695">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="a45e6-696">Attributrouting</span><span class="sxs-lookup"><span data-stu-id="a45e6-696">Attribute routing</span></span>

<span data-ttu-id="a45e6-697">Beim Attributrouting werden Aktionen mithilfe von Attributen direkt Routenvorlagen zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-697">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="a45e6-698">Im folgenden Beispiel wird `app.UseMvc();` in der `Configure`-Methode verwendet, und es wird keine Route übergeben.</span><span class="sxs-lookup"><span data-stu-id="a45e6-698">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="a45e6-699">`HomeController` entspricht URLs, die denen ähneln, denen die Standardroute `{controller=Home}/{action=Index}/{id?}` entsprechen würde:</span><span class="sxs-lookup"><span data-stu-id="a45e6-699">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

<span data-ttu-id="a45e6-700">Die `HomeController.Index()`-Aktion wird für die URL-Pfade `/`, `/Home` und `/Home/Index` jeweils ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-700">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="a45e6-701">In diesem Beispiel wird ein wichtiger Unterschied beim Programmieren zwischen dem Attributrouting und dem herkömmlichen Routing hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="a45e6-701">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="a45e6-702">Attributrouting erfordert mehr Eingabe, um eine Route anzugeben. Die herkömmliche Standardroute verarbeitet auch kürzere Routen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-702">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="a45e6-703">Attributrouting ermöglicht jedoch (und erfordert auch) die präzise Kontrolle der Routenvorlagen, die für die einzelnen Aktionen gelten.</span><span class="sxs-lookup"><span data-stu-id="a45e6-703">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="a45e6-704">Beim Attributrouting haben der Controller- und der Aktionsname **keine** Auswirkung darauf, welche Aktion ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-704">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="a45e6-705">In diesem Beispiel werden dieselben URLs wie im vorherigen Beispiel abgeglichen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-705">This example will match the same URLs as the previous example.</span></span>

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> <span data-ttu-id="a45e6-706">Die oben genannten Routenvorlagen definieren keine Routenparameter für `action`, `area` und `controller`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-706">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="a45e6-707">Diese Routenparameter sind in Attributrouten nicht zulässig.</span><span class="sxs-lookup"><span data-stu-id="a45e6-707">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="a45e6-708">Da die Routenvorlage bereits einer Aktion zugeordnet ist, wäre es nicht sinnvoll, den Aktionsnamen aus der URL zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="a45e6-708">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="a45e6-709">Attributrouting mit Http[Verb]-Attributen</span><span class="sxs-lookup"><span data-stu-id="a45e6-709">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="a45e6-710">Beim Attributrouting können auch `Http[Verb]`-Attribute wie `HttpPostAttribute` verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-710">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="a45e6-711">Alle diese Attribute können eine Routenvorlage akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="a45e6-711">All of these attributes can accept a route template.</span></span> <span data-ttu-id="a45e6-712">In diesem Beispiel werden zwei Aktionen gezeigt, die derselben Routenvorlage zugeordnet sind:</span><span class="sxs-lookup"><span data-stu-id="a45e6-712">This example shows two actions that match the same route template:</span></span>

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

<span data-ttu-id="a45e6-713">Für einen URL-Pfad wie `/products` wird die Aktion `ProductsApi.ListProducts` ausgeführt, wenn das HTTP-Verb `GET` ist, und `ProductsApi.CreateProduct` wird ausgeführt, wenn das HTTP-Verb `POST` entspricht.</span><span class="sxs-lookup"><span data-stu-id="a45e6-713">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="a45e6-714">Attributrouting gleicht zuerst die URL gegen die Routenvorlagen ab, die von den Routenattributen definiert werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-714">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="a45e6-715">Sobald eine Routenvorlage übereinstimmt, werden `IActionConstraint`-Einschränkungen angewendet, um zu bestimmen, welche Aktionen ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="a45e6-715">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="a45e6-716">Beim Erstellen einer REST-API wird `[Route(...)]` selten für eine Aktionsmethode verwendet, die Aktion alle HTTP-Methoden akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="a45e6-716">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="a45e6-717">Es ist besser, das spezifischere `Http*Verb*Attributes` zu nutzen, um präzise anzugeben, was Ihre API unterstützt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-717">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="a45e6-718">REST-API-Clients sollten wissen, welche Pfade und HTTP-Verben bestimmten logischen Operationen entsprechen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-718">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="a45e6-719">Da eine Attributroute für eine bestimmte Aktion gilt, ist es einfach, Parameter als Teil der Routenvorlagendefinition erforderlich festzulegen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-719">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="a45e6-720">In diesem Beispiel ist `id` als Teil des URL-Pfads erforderlich.</span><span class="sxs-lookup"><span data-stu-id="a45e6-720">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="a45e6-721">Die `ProductsApi.GetProduct(int)`-Aktion wird für einen URL-Pfad wie `/products/3` ausgeführt, jedoch nicht für einen URL-Pfad wie `/products`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-721">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="a45e6-722">Eine vollständige Beschreibung und Routenvorlagen und dazugehörige Optionen finden Sie unter [Routing in ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="a45e6-722">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="a45e6-723">Routenname</span><span class="sxs-lookup"><span data-stu-id="a45e6-723">Route Name</span></span>

<span data-ttu-id="a45e6-724">Im folgenden Code wird ein *Routenname* von `Products_List` definiert:</span><span class="sxs-lookup"><span data-stu-id="a45e6-724">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="a45e6-725">Routennamen können verwendet werden, um basierend auf einer bestimmten Route eine URL zu generieren.</span><span class="sxs-lookup"><span data-stu-id="a45e6-725">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="a45e6-726">Routennamen haben keine Auswirkung auf das URL-Zuordnungsverhalten des Routings und dienen nur zur URL-Generierung.</span><span class="sxs-lookup"><span data-stu-id="a45e6-726">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="a45e6-727">Routennamen müssen anwendungsweit eindeutig sein.</span><span class="sxs-lookup"><span data-stu-id="a45e6-727">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="a45e6-728">Vergleichen Sie dies mit der herkömmlichen *Standardroute*, die den `id`-Parameter als optional definiert (`{id?}`).</span><span class="sxs-lookup"><span data-stu-id="a45e6-728">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="a45e6-729">APIs präzise angeben zu können, hat Vorteile, z.B. können `/products` und `/products/5` an unterschiedliche Aktionen gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-729">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="a45e6-730">Kombinieren von Routen</span><span class="sxs-lookup"><span data-stu-id="a45e6-730">Combining routes</span></span>

<span data-ttu-id="a45e6-731">Um Attributrouting weniger repetitiv zu gestalten, werden Routenattribute auf dem Controller mit Routenattributen auf den einzelnen Aktionen kombiniert.</span><span class="sxs-lookup"><span data-stu-id="a45e6-731">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="a45e6-732">Alle auf dem Controller definierten Routenvorlagen werden den Routenvorlagen auf den Aktionen vorangestellt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-732">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="a45e6-733">Wenn Routenattribute auf dem Controller platziert werden, verwenden **alle** Aktionen im Controller Attributrouting.</span><span class="sxs-lookup"><span data-stu-id="a45e6-733">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="a45e6-734">In diesem Beispiel kann der URL-Pfad `/products``ProductsApi.ListProducts` und der URL-Pfad `/products/5``ProductsApi.GetProduct(int)` zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-734">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="a45e6-735">Beide Aktionen gleichen nur „HTTP `GET`“ ab, weil sie mit `HttpGetAttribute` markiert sind.</span><span class="sxs-lookup"><span data-stu-id="a45e6-735">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="a45e6-736">Routenvorlagen, die auf eine Aktion angewendet werden, die mit einem `/` oder `~/` beginnen, können nicht mit Routenvorlagen kombiniert werden, die auf den Controller angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-736">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="a45e6-737">In diesem Beispiel werden mehrere URL-Pfade zugeordnet, die der *Standardroute* ähneln.</span><span class="sxs-lookup"><span data-stu-id="a45e6-737">This example matches a set of URL paths similar to the *default route*.</span></span>

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a><span data-ttu-id="a45e6-738">Ordnen der Attributrouten</span><span class="sxs-lookup"><span data-stu-id="a45e6-738">Ordering attribute routes</span></span>

<span data-ttu-id="a45e6-739">Im Gegensatz zu herkömmlichen Routen, die in einer definierten Reihenfolge ausgeführt werden, erstellt das Attribut Routing eine Struktur und gleicht alle Routen gleichzeitig ab.</span><span class="sxs-lookup"><span data-stu-id="a45e6-739">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="a45e6-740">Der Vorgang wird also ausgeführt, als ob die Routeneinträge in der idealen Reihenfolge platziert worden wären: Die spezifischsten Routen können also vor den allgemeineren ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-740">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="a45e6-741">Eine Route wie `blog/search/{topic}` ist z.B. spezifischer als eine Route wie `blog/{*article}`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-741">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="a45e6-742">Logisch gesehen wird die Route `blog/search/{topic}` standardmäßig zuerst ausgeführt, da dies die einzig sinnvolle Reihenfolge ist.</span><span class="sxs-lookup"><span data-stu-id="a45e6-742">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="a45e6-743">Beim herkömmlichen Routing ist der Entwickler verantwortlich dafür, die Routen in die gewünschte Reihenfolge zu bringen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-743">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="a45e6-744">Attributrouten können mithilfe der `Order`-Eigenschaft aller vom Framework bereitgestellten Routenattribute eine Reihenfolge konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="a45e6-744">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="a45e6-745">Routen werden entsprechend einer aufsteigenden Reihenfolge der `Order`-Eigenschaft verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-745">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="a45e6-746">Die Standardreihenfolge ist `0`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-746">The default order is `0`.</span></span> <span data-ttu-id="a45e6-747">Wird für eine Route `Order = -1` festgelegt, wird sie vor denjenigen Routen ausgeführt, für die keine Reihenfolge festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="a45e6-747">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="a45e6-748">Wird für eine Route `Order = 1` festgelegt, wird sie ausgeführt, nachdem die Standardroutenreihenfolge ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="a45e6-748">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="a45e6-749">Vermeiden Sie eine Abhängigkeit von `Order`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-749">Avoid depending on `Order`.</span></span> <span data-ttu-id="a45e6-750">Wenn der URL-Raum explizite Reihenfolgenwerte erfordert, um korrekt weiterzuleiten, ist es wahrscheinlich auch für Clients verwirrend.</span><span class="sxs-lookup"><span data-stu-id="a45e6-750">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="a45e6-751">Beim Attributrouting wird im Allgemeinen mithilfe der URL-Zuordnung die richtige Route ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-751">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="a45e6-752">Wenn die für die URL-Generierung verwendete Standardreihenfolge nicht funktioniert, ist es meist einfacher, Routennamen als Außerkraftsetzung zu verwenden, statt die `Order`-Eigenschaft anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-752">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="a45e6-753">Razor Pages-Routing und MVC Controller-Routing verwenden eine gemeinsame Implementierung.</span><span class="sxs-lookup"><span data-stu-id="a45e6-753">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="a45e6-754">Informationen zur Routenreihenfolge in den Razor Pages-Themen finden Sie unter [Razor Pages-Route and App-Konventionen: Routenreihenfolge](xref:razor-pages/razor-pages-conventions#route-order).</span><span class="sxs-lookup"><span data-stu-id="a45e6-754">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="a45e6-755">Ersetzen von Token in Routenvorlagen ([controller], [action] [area])</span><span class="sxs-lookup"><span data-stu-id="a45e6-755">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="a45e6-756">Der Einfachheit halber unterstützen Attributrouten die *Tokenersetzung*, indem ein Token in eckige Klammern eingeschlossen wird (`[`, `]`).</span><span class="sxs-lookup"><span data-stu-id="a45e6-756">For convenience, attribute routes support *token replacement* by enclosing a token in square-braces (`[`, `]`).</span></span> <span data-ttu-id="a45e6-757">Die Token `[action]`, `[area]` und `[controller]` werden durch die Werte der Aktionsnamen, den Namen des Bereichs und des Controllers der Aktion ersetzt, in dem die Route definiert ist.</span><span class="sxs-lookup"><span data-stu-id="a45e6-757">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="a45e6-758">In dem folgenden Beispiel entsprechen die Aktionen wie in den Kommentaren beschrieben URL-Pfaden:</span><span class="sxs-lookup"><span data-stu-id="a45e6-758">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="a45e6-759">Die Tokenersetzung tritt im letzten Schritt der Erstellung von Attributrouten auf.</span><span class="sxs-lookup"><span data-stu-id="a45e6-759">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="a45e6-760">Der folgende Code verhält sich genauso wie der aus dem obigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a45e6-760">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="a45e6-761">Attributrouten können auch mit Vererbung kombiniert werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-761">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="a45e6-762">Dies ist besonders effektiv in Kombination mit der Tokenersetzung.</span><span class="sxs-lookup"><span data-stu-id="a45e6-762">This is particularly powerful combined with token replacement.</span></span>

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

<span data-ttu-id="a45e6-763">Tokenersetzung gilt auch für Routennamen, die durch Attributrouten definiert werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-763">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="a45e6-764">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generiert für jede Aktion einen eindeutigen Routennamen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-764">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="a45e6-765">Damit das Trennzeichen `[` oder `]` der Tokenersetzungs-Literalzeichenfolge bei einem Abgleich gefunden wird, muss es doppelt vorhanden sein (`[[` oder `]]`), was einem Escapezeichen entspricht.</span><span class="sxs-lookup"><span data-stu-id="a45e6-765">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="a45e6-766">Verwenden eines Parametertransformators zum Anpassen der Tokenersetzung</span><span class="sxs-lookup"><span data-stu-id="a45e6-766">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="a45e6-767">Die Tokenersetzung kann mit einem Parametertransformator angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-767">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="a45e6-768">Ein Parametertransformator implementiert `IOutboundParameterTransformer` und wandelt den Wert der Parameter um.</span><span class="sxs-lookup"><span data-stu-id="a45e6-768">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="a45e6-769">Beispielsweise ändert ein benutzerdefinierter `SlugifyParameterTransformer`-Parametertransformator den Routenwert `SubscriptionManagement` in `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-769">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="a45e6-770">Die `RouteTokenTransformerConvention` ist eine Anwendungsmodellkonvention, die Folgendes ausführt:</span><span class="sxs-lookup"><span data-stu-id="a45e6-770">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="a45e6-771">Wendet einen angegebenen Parametertransformator auf alle Attributrouten in der App an.</span><span class="sxs-lookup"><span data-stu-id="a45e6-771">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="a45e6-772">Passt die Tokenwerte für Attributrouten bei ihrer Ersetzung an.</span><span class="sxs-lookup"><span data-stu-id="a45e6-772">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="a45e6-773">Die `RouteTokenTransformerConvention` wird als Option in `ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="a45e6-773">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a><span data-ttu-id="a45e6-774">Mehrere Routen</span><span class="sxs-lookup"><span data-stu-id="a45e6-774">Multiple Routes</span></span>

<span data-ttu-id="a45e6-775">Attributrouting unterstützt das Definieren mehrerer Routen, die zu derselben Aktion führen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-775">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="a45e6-776">Dies wird am häufigsten beim Imitieren des Verhaltens der *herkömmlichen Standardroute* verwendet. Im folgenden Beispiel wird dies gezeigt:</span><span class="sxs-lookup"><span data-stu-id="a45e6-776">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="a45e6-777">Wenn mehrere Routenattribute auf dem Controller platziert werden, wird jedes mit jedem der Routenattribute auf den Aktionsmethoden kombiniert.</span><span class="sxs-lookup"><span data-stu-id="a45e6-777">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

<span data-ttu-id="a45e6-778">Werden mehrere Routenattribute (die `IActionConstraint` implementieren) auf einer Aktion platziert, wird jede Aktionseinschränkung mit der Routenvorlage aus dem Attribut kombiniert, das sie definiert.</span><span class="sxs-lookup"><span data-stu-id="a45e6-778">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> <span data-ttu-id="a45e6-779">Obwohl das Verwenden mehrerer Routen für Aktionen sehr hilfreich scheint, ist es empfehlenswerter, den URL-Raum der Anwendung einfach und gut definiert zu halten.</span><span class="sxs-lookup"><span data-stu-id="a45e6-779">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="a45e6-780">Verwenden Sie nur mehrere Routen für Aktionen, wenn dies notwendig ist, z.B. um vorhandene Clients zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-780">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="a45e6-781">Angeben von optionalen Attributroutenparametern, Standardwerten und Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="a45e6-781">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="a45e6-782">Attributrouten unterstützen dieselbe Inline-Syntax wie herkömmliche Routen, um optionale Parameter, Standardwerte und Einschränkungen anzugeben.</span><span class="sxs-lookup"><span data-stu-id="a45e6-782">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="a45e6-783">Eine ausführliche Beschreibung der Syntax der Routenvorlage finden Sie unter [Routenvorlagenreferenz](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="a45e6-783">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="a45e6-784">Benutzerdefinierte Routenattribute mithilfe von `IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="a45e6-784">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="a45e6-785">Alle im Framework bereitgestellten Routenattribute (`[Route(...)]`, `[HttpGet(...)]` usw.) implementieren die `IRouteTemplateProvider`-Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="a45e6-785">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="a45e6-786">MVC sucht in Controllerklassen und Aktionsmethoden nach Attributen, wenn die Anwendung gestartet wird, und verwendet diejenigen, die `IRouteTemplateProvider` implementieren, um die anfänglichen Routen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-786">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="a45e6-787">Sie können `IRouteTemplateProvider` implementieren, um eigene Routenattribute zu definieren.</span><span class="sxs-lookup"><span data-stu-id="a45e6-787">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="a45e6-788">Jeder `IRouteTemplateProvider` lässt Sie eine einzelne Route mit einer benutzerdefinierten Routenvorlage, Reihenfolge und einem benutzerdefinierten Namen definieren:</span><span class="sxs-lookup"><span data-stu-id="a45e6-788">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="a45e6-789">Das Attribut aus dem obigen Beispiel legt `Template` automatisch auf `"api/[controller]"` fest, wenn `[MyApiController]` angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-789">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="a45e6-790">Anpassen von Attributrouten mithilfe des Anwendungsmodells</span><span class="sxs-lookup"><span data-stu-id="a45e6-790">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="a45e6-791">Das *Anwendungsmodell* ist ein Objektmodell, das beim Starten aus allen Metadaten von MVC erstellt wird, um Ihre Aktionen weiterzuleiten und auszuführen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-791">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="a45e6-792">Das *Anwendungsmodell* enthält alle Daten, die aus Routenattributen (über `IRouteTemplateProvider`) erfasst wurden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-792">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="a45e6-793">Sie können *Konventionen* schreiben, damit das Anwendungsmodell beim Startzeitpunkt das Routingverhalten anpasst.</span><span class="sxs-lookup"><span data-stu-id="a45e6-793">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="a45e6-794">Dieser Abschnitt enthält ein einfaches Beispiel für das Anpassen des Routings mithilfe des Anwendungsmodells.</span><span class="sxs-lookup"><span data-stu-id="a45e6-794">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="a45e6-795">Gemischtes Routing: Attributrouting vs. herkömmliches Routing</span><span class="sxs-lookup"><span data-stu-id="a45e6-795">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="a45e6-796">MVC-Anwendungen können herkömmliches Routing und Attributrouting kombinieren.</span><span class="sxs-lookup"><span data-stu-id="a45e6-796">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="a45e6-797">In der Regel werden herkömmliche Routen für Controller verwendet, die für Browser-HTML-Seiten gedacht sind, und das Attributrouting für Controller für REST-APIs.</span><span class="sxs-lookup"><span data-stu-id="a45e6-797">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="a45e6-798">Aktionen werden entweder herkömmlich oder über Attribute zugeordnet,</span><span class="sxs-lookup"><span data-stu-id="a45e6-798">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="a45e6-799">d.h., dass eine Route auf dem Controller oder der Aktion platziert wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-799">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="a45e6-800">Aktionen, die Attributrouten definieren, können nicht über die herkömmliche Routen und umgekehrt erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-800">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="a45e6-801">Wenn nur **ein** Routenattribut auf dem Controller platziert wird, werden alle Aktionen im Controller über Attribute zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-801">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="a45e6-802">Die beiden Routingmethoden unterscheiden sich in dem Prozess, der angewendet wird, nachdem eine URL einer Routenvorlage zugeordnet wurde.</span><span class="sxs-lookup"><span data-stu-id="a45e6-802">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="a45e6-803">Beim herkömmlichen Routing dienen die Routenwerte aus der Zuordnung dazu, aus einer Nachschlagetabelle aller herkömmlich zugeordneten Aktionen die Aktion und den Controller auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-803">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="a45e6-804">Beim Attributrouting ist jede Vorlage bereits einer Aktion zugeordnet, und keine weitere Suche ist erforderlich.</span><span class="sxs-lookup"><span data-stu-id="a45e6-804">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="a45e6-805">Komplexe Segmente</span><span class="sxs-lookup"><span data-stu-id="a45e6-805">Complex segments</span></span>

<span data-ttu-id="a45e6-806">Komplexe Segmente (z.B. `[Route("/dog{token}cat")]`) werden von rechts nach links auf eine nicht gierige Weise durch entsprechende Literale verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-806">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="a45e6-807">Eine entsprechende Beschreibung finden Sie im [Quellcode](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296).</span><span class="sxs-lookup"><span data-stu-id="a45e6-807">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="a45e6-808">Weitere Informationen finden Sie in [diesem Thema](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span><span class="sxs-lookup"><span data-stu-id="a45e6-808">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="a45e6-809">URL-Generierung</span><span class="sxs-lookup"><span data-stu-id="a45e6-809">URL Generation</span></span>

<span data-ttu-id="a45e6-810">MVC-Anwendungen können Routingfeatures zur URL-Generierung verwenden, um URL-Links zu Aktionen zu generieren.</span><span class="sxs-lookup"><span data-stu-id="a45e6-810">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="a45e6-811">Durch das Generieren von URLs müssen URLs nicht mehr hartcodiert werden, sodass der Code robuster und leichter verwaltbar wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-811">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="a45e6-812">Dieser Abschnitt konzentriert sich auf die von MVC bereitgestellte URL-Generierung und befasst sich nur kurz mit der Funktionsweise.</span><span class="sxs-lookup"><span data-stu-id="a45e6-812">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="a45e6-813">Eine detaillierte Beschreibung der URL-Generierung finden Sie unter [Routing in ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="a45e6-813">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="a45e6-814">Die `IUrlHelper`-Schnittstelle ist die zugrunde liegende Infrastruktur zwischen MVC und dem Routing zur URL-Generierung.</span><span class="sxs-lookup"><span data-stu-id="a45e6-814">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="a45e6-815">Eine Instanz von `IUrlHelper` ist über die `Url`-Eigenschaft in Controllern, Ansichten und Ansichtskomponenten verfügbar.</span><span class="sxs-lookup"><span data-stu-id="a45e6-815">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="a45e6-816">In diesem Beispiel wird die `IUrlHelper`-Schnittstelle von der `Controller.Url`-Eigenschaft dazu verwendet, eine URL in einer anderen Aktion zu generieren.</span><span class="sxs-lookup"><span data-stu-id="a45e6-816">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="a45e6-817">Wenn die Anwendung die herkömmliche Standardroute verwendet, ist der Wert der `url`-Variable die URL-Pfadzeichenfolge `/UrlGeneration/Destination`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-817">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="a45e6-818">Dieser URL-Pfad wird vom Routing erstellt, indem Routenwerte aus der aktuellen Anforderung (Umgebungswerte) mit den an `Url.Action` übergebenen Werten kombiniert und anschließend in die Routenvorlage eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="a45e6-818">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="a45e6-819">Der Wert eines jeden Routenparameters wird in der Routenvorlage durch die entsprechenden Namen mit den Werten und Umgebungswerten ersetzt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-819">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="a45e6-820">Ein Routenparameter ohne Wert kann einen Standardwert verwenden, wenn er über einen verfügt, oder übersprungen werden, wenn er optional ist (wie im Fall von `id` in diesem Beispiel).</span><span class="sxs-lookup"><span data-stu-id="a45e6-820">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="a45e6-821">Die URL-Generierung schlägt fehl, wenn ein erforderlicher Routenparameter keinen entsprechenden Wert besitzt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-821">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="a45e6-822">Wenn die URL-Generierung für eine Route fehlschlägt, wird die nächste Route ausprobiert, bis alle Routen getestet wurden oder eine Übereinstimmung gefunden wurde.</span><span class="sxs-lookup"><span data-stu-id="a45e6-822">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="a45e6-823">In dem Beispiel mit `Url.Action` oben wird von herkömmlichem Routing ausgegangen. Die URL-Generierung funktioniert ähnlich wie das Attributrouting, obwohl sich die Konzepte unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-823">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="a45e6-824">Bei herkömmlichem Routing wird mit den Routenwerten eine Vorlage erweitert, und die Routenwerte für `controller` und `action` kommen in der Regel in dieser Vorlage vor. Das funktioniert, weil sich die mit dem Routing zugeordneten URLs an eine *Konvention* halten.</span><span class="sxs-lookup"><span data-stu-id="a45e6-824">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="a45e6-825">Beim Attributrouting dürfen die Routenwerte für `controller` und `action` nicht in der Vorlage vorkommen. Sie werden stattdessen verwendet, um nachzuschlagen, welche Vorlage verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="a45e6-825">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="a45e6-826">In diesem Beispiel wird das Attributrouting verwendet:</span><span class="sxs-lookup"><span data-stu-id="a45e6-826">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="a45e6-827">MVC erstellt eine Nachschlagetabelle aller über Attribute zugeordneten Aktionen und ordnet die `controller`- und `action`-Werte zu, um die Routenvorlage auszuwählen, die für die URL-Generierung verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="a45e6-827">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="a45e6-828">Im obigen Beispiel wird `custom/url/to/destination` generiert.</span><span class="sxs-lookup"><span data-stu-id="a45e6-828">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="a45e6-829">Generieren von URLs nach Aktionsnamen</span><span class="sxs-lookup"><span data-stu-id="a45e6-829">Generating URLs by action name</span></span>

<span data-ttu-id="a45e6-830">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="a45e6-830">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="a45e6-831">`Action`) und alle zugehörigen Überladungen bauen alle auf der Idee auf, dass Sie angeben, was Sie verknüpfen möchten, indem Sie einen Controllernamen und einen Aktionsnamen angeben.</span><span class="sxs-lookup"><span data-stu-id="a45e6-831">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="a45e6-832">Bei Verwendung von `Url.Action` werden die aktuellen Routenwerte für `controller` und `action` für Sie festgelegt. Die Werte von `controller` und `action` bestehen sowohl aus *Umgebungswerten* **als auch** aus *Werten*.</span><span class="sxs-lookup"><span data-stu-id="a45e6-832">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="a45e6-833">Die Methode `Url.Action` verwendet immer die aktuellen Werte von `action` und `controller` und generiert einen URL-Pfad, der zur aktuellen Aktion weiterleitet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-833">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="a45e6-834">Beim Routing wird versucht, mit den Werten in den Umgebungswerten Informationen auszufüllen, die Sie beim Generieren einer URL nicht bereitgestellt haben.</span><span class="sxs-lookup"><span data-stu-id="a45e6-834">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="a45e6-835">Mit Routen wie `{a}/{b}/{c}/{d}` und Umgebungswerten wie `{ a = Alice, b = Bob, c = Carol, d = David }` hat das Routing genügend Informationen, um eine URL ohne zusätzliche Werte zu generieren, da alle Routenparameter einen Wert aufweisen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-835">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="a45e6-836">Wenn Sie den Wert `{ d = Donovan }` hinzufügen, wird der Wert `{ d = David }` ignoriert, und der generierte URL-Pfad wäre `Alice/Bob/Carol/Donovan`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-836">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="a45e6-837">URL-Pfade sind hierarchisch.</span><span class="sxs-lookup"><span data-stu-id="a45e6-837">URL paths are hierarchical.</span></span> <span data-ttu-id="a45e6-838">Wenn Sie im obigen Beispiel den Wert `{ c = Cheryl }` hinzufügen, werden die beiden Werte `{ c = Carol, d = David }` ignoriert.</span><span class="sxs-lookup"><span data-stu-id="a45e6-838">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="a45e6-839">In diesem Fall haben wir keinen Wert für `d` mehr, und die URL-Generierung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="a45e6-839">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="a45e6-840">Sie müssten dann den gewünschten Wert von `c` und `d` angeben.</span><span class="sxs-lookup"><span data-stu-id="a45e6-840">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="a45e6-841">Man könnte annehmen, dass dieses Problem bei der Standardroute auftritt (`{controller}/{action}/{id?}`). Tatsächlich passiert es in der Praxis jedoch selten, das `Url.Action` immer explizit einen `controller`- und `action`-Wert angibt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-841">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="a45e6-842">Längere Überladungen von `Url.Action` akzeptieren auch ein zusätzliches *route values*-Objekt, um andere Werte für Routenparameter als `controller` und `action` bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-842">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="a45e6-843">Es wird in der Regel mit `id` verwendet, z.B. in `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-843">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="a45e6-844">Gemäß der Konvention ist das *Routenwerte*-Objekt eines des anonymen Typs, es kann aber auch ein `IDictionary<>`-Objekt oder ein *Plain Old .NET Object* sein.</span><span class="sxs-lookup"><span data-stu-id="a45e6-844">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="a45e6-845">Alle zusätzlichen Routenwerte, die keinen Routenparametern zugeordnet sind, werden in der Abfragezeichenfolge platziert.</span><span class="sxs-lookup"><span data-stu-id="a45e6-845">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="a45e6-846">Um eine absolute URL zu erstellen, verwenden Sie eine Überladung, die eine `protocol` akzeptiert: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="a45e6-846">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="a45e6-847">Generieren von URLs nach Routen</span><span class="sxs-lookup"><span data-stu-id="a45e6-847">Generating URLs by route</span></span>

<span data-ttu-id="a45e6-848">Im obigen Code wurde das Generieren einer URL durch das Übergeben des Controller- und Aktionsnamens veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="a45e6-848">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="a45e6-849">`IUrlHelper` stellt außerdem die `Url.RouteUrl`-Methodenfamilie bereit.</span><span class="sxs-lookup"><span data-stu-id="a45e6-849">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="a45e6-850">Diese Methoden ähneln `Url.Action`, kopieren jedoch nicht die aktuellen Werte `action` und `controller` in die Routenwerte.</span><span class="sxs-lookup"><span data-stu-id="a45e6-850">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="a45e6-851">Oftmals wird damit ein Routenname angegeben, um mit einer bestimmten Route die URL zu generieren, in der Regel *ohne* Angabe eines Controller- oder Aktionsnamens.</span><span class="sxs-lookup"><span data-stu-id="a45e6-851">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="a45e6-852">Generieren von URLs in HTML</span><span class="sxs-lookup"><span data-stu-id="a45e6-852">Generating URLs in HTML</span></span>

<span data-ttu-id="a45e6-853">`IHtmlHelper` stellt die `HtmlHelper`-Methoden `Html.BeginForm` und `Html.ActionLink` bereit, um jeweils `<form>`- und `<a>`-Elemente zu generieren.</span><span class="sxs-lookup"><span data-stu-id="a45e6-853">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="a45e6-854">Diese Methoden verwenden die `Url.Action`-Methode, um eine URL zu generieren, und akzeptieren ähnliche Argumente.</span><span class="sxs-lookup"><span data-stu-id="a45e6-854">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="a45e6-855">Die `Url.RouteUrl`-Begleiter für `HtmlHelper` sind `Html.BeginRouteForm` und `Html.RouteLink`, die ähnliche Funktionen aufweisen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-855">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="a45e6-856">Taghilfsprogramme generieren URLs mit den Taghilfsprogrammen `form` und `<a>`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-856">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="a45e6-857">Beide implementieren mit `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-857">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="a45e6-858">Weitere Informationen finden Sie unter [Einführung in die Verwendung von Taghilfsprogrammen in Formularen in ASP.NET Core](../views/working-with-forms.md).</span><span class="sxs-lookup"><span data-stu-id="a45e6-858">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="a45e6-859">In Ansichten ist `IUrlHelper` über die `Url`-Eigenschaft für jede Ad-hoc-URL-Generierung verfügbar, die keine der oben genannten ist.</span><span class="sxs-lookup"><span data-stu-id="a45e6-859">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="a45e6-860">Generieren von URLs in Aktionsergebnissen</span><span class="sxs-lookup"><span data-stu-id="a45e6-860">Generating URLS in Action Results</span></span>

<span data-ttu-id="a45e6-861">In den obigen Beispielen wurde gezeigt, wie `IUrlHelper` in einem Controller verwendet wird und dass es üblicherweise dazu dient, eine URL im Rahmen eines Aktionsergebnisses zu generieren.</span><span class="sxs-lookup"><span data-stu-id="a45e6-861">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="a45e6-862">Die Basisklassen `ControllerBase` und `Controller` stellen Hilfsmethoden für Aktionsergebnisse bereit, die auf eine andere Aktionen verweisen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-862">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="a45e6-863">Eine typische Verwendung besteht darin, nach dem Akzeptieren einer Benutzereingabe weiterzuleiten.</span><span class="sxs-lookup"><span data-stu-id="a45e6-863">One typical usage is to redirect after accepting user input.</span></span>

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

<span data-ttu-id="a45e6-864">Die Factorymethoden der Aktionsergebnisse folgen einem ähnlichen Muster wie die Methoden für `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-864">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="a45e6-865">Sonderfall für dedizierte herkömmliche Routen</span><span class="sxs-lookup"><span data-stu-id="a45e6-865">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="a45e6-866">Beim herkömmlichen Routing können Sie eine bestimmte Art von Routendefinition verwenden, die als *dedizierte herkömmliche Route* bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-866">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="a45e6-867">Die Route namens `blog` im folgenden Beispiel ist eine dedizierte herkömmliche Route.</span><span class="sxs-lookup"><span data-stu-id="a45e6-867">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="a45e6-868">Wenn Sie diese Routendefinitionen verwenden, generiert `Url.Action("Index", "Home")` den URL-Pfad `/` mit der `default`-Route. Die Frage ist nur warum?</span><span class="sxs-lookup"><span data-stu-id="a45e6-868">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="a45e6-869">Man könnte meinen, dass die Routenwerte `{ controller = Home, action = Index }` ausreichen würden, um eine URL mithilfe von `blog` zu generieren, und das Ergebnis wäre `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-869">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="a45e6-870">Dedizierte herkömmliche Routen nutzen ein spezielles Verhalten von Standardwerten, die keinen entsprechenden Routenparameter besitzen, der verhindert, dass die Route bei der URL-Generierung „zu gierig“ wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-870">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="a45e6-871">In diesem Fall sind die Standardwerte `{ controller = Blog, action = Article }`, und weder `controller` noch `action` werden als Routenparameter verwendet.</span><span class="sxs-lookup"><span data-stu-id="a45e6-871">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="a45e6-872">Wenn das Routing die URL-Generierung ausführt, müssen die angegebenen Werte mit den Standardwerten übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-872">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="a45e6-873">Die URL-Generierung mithilfe von `blog` schlägt fehl, da die Werte `{ controller = Home, action = Index }` nicht mit `{ controller = Blog, action = Article }` übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-873">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="a45e6-874">Routing greift dann wieder auf `default` zurück, was erfolgreich ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-874">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="a45e6-875">Bereiche</span><span class="sxs-lookup"><span data-stu-id="a45e6-875">Areas</span></span>

<span data-ttu-id="a45e6-876">[Bereiche](areas.md) sind ein MVC-Feature, mit dem verwandte Funktionen in einer Gruppe als separater Routing-Namespace (für Controlleraktionen) und als Ordnerstruktur (für Ansichten) organisiert werden können.</span><span class="sxs-lookup"><span data-stu-id="a45e6-876">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="a45e6-877">Mithilfe von Bereichen kann eine Anwendung mehrere Controller mit demselben Namen haben – solange sie verschiedene *Bereiche* haben.</span><span class="sxs-lookup"><span data-stu-id="a45e6-877">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="a45e6-878">Mithilfe von Bereichen wird außerdem eine Hierarchie erstellt, damit das Routing durch Hinzufügen eines anderen Routenparameters ausgeführt werden kann: `area` zu `controller` und `action`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-878">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="a45e6-879">In diesem Abschnitt wird erläutert, wie Routing mit Bereichen interagiert. Weitere Informationen zur Verwendung von Bereichen mit Ansichten finden Sie unter [Bereiche](areas.md).</span><span class="sxs-lookup"><span data-stu-id="a45e6-879">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="a45e6-880">Im folgenden Beispiel wird MVC konfiguriert, sodass es die standardmäßige herkömmliche Route verwendet und eine *Bereichsroute* für einen Bereich namens `Blog`:</span><span class="sxs-lookup"><span data-stu-id="a45e6-880">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="a45e6-881">Beim Abgleich eines URL-Pfads wie `/Manage/Users/AddUser` erzeugt die erste Route die Routenwerte `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-881">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="a45e6-882">Der `area`-Routenwert wird von einem Standardwert für `area` erzeugt. Die von `MapAreaRoute` erstellte Route entspricht in der Tat der folgenden:</span><span class="sxs-lookup"><span data-stu-id="a45e6-882">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="a45e6-883">`MapAreaRoute` erstellt mit einem Standardwert und einer Einschränkung für `area` sowie mit dem bereitgestellten Bereichsnamen (in diesem Fall `Blog`) eine Route.</span><span class="sxs-lookup"><span data-stu-id="a45e6-883">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="a45e6-884">Der Standardwert stellt sicher, dass die Route immer `{ area = Blog, ... }` erzeugt, die Einschränkung erfordert den Wert `{ area = Blog, ... }` für URL-Generierung.</span><span class="sxs-lookup"><span data-stu-id="a45e6-884">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="a45e6-885">Beim herkömmlichen Routing ist die Reihenfolge wichtig.</span><span class="sxs-lookup"><span data-stu-id="a45e6-885">Conventional routing is order-dependent.</span></span> <span data-ttu-id="a45e6-886">Routen mit Bereichen werden im Allgemeinen früher in der Routentabelle aufgeführt als die spezifischeren Routen ohne Bereich.</span><span class="sxs-lookup"><span data-stu-id="a45e6-886">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="a45e6-887">Die Routenwerte im oben genannten Beispiel werden der folgenden Aktion zugeordnet:</span><span class="sxs-lookup"><span data-stu-id="a45e6-887">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="a45e6-888">`AreaAttribute` kennzeichnet einen Controller als Teil eines Bereichs, z.B. des Bereichs `Blog`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-888">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="a45e6-889">Controller ohne `[Area]`-Attribut gehören demnach zu keinem Bereich und stimmen **nicht** überein, wenn die `area`-Route wird vom Routing bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-889">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="a45e6-890">Im folgenden Beispiel kann nur der erste aufgelistete Controller die Routenwerte `{ area = Blog, controller = Users, action = AddUser }` abgleichen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-890">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="a45e6-891">Aus Gründen der Vollständigkeit wird hier der Namespace jedes Controllers dargestellt. Andernfalls gäbe es einen Namenskonflikt zwischen den Controllern, und ein Compilerfehler würde generiert.</span><span class="sxs-lookup"><span data-stu-id="a45e6-891">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="a45e6-892">Klassennamespaces haben keine Auswirkungen auf das MVC-Routing.</span><span class="sxs-lookup"><span data-stu-id="a45e6-892">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="a45e6-893">Die ersten beiden Controller gehören zu Bereichen und werden können nur abgleichen, wenn ihr jeweiliger Bereichsname vom `area`-Routenwert bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-893">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="a45e6-894">Der dritte Controller gehört keinem Bereich an und kann nur abgleichen, wenn vom Routing kein Wert für `area` bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-894">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="a45e6-895">Im Hinblick auf das Erkennen *keines Werts* hat die Abwesenheit des `area`-Werts dieselben Auswirkungen, wie wenn der Wert für `area` 0 (null) oder eine leere Zeichenfolge wäre.</span><span class="sxs-lookup"><span data-stu-id="a45e6-895">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="a45e6-896">Beim Ausführen einer Aktion innerhalb eines Bereichs ist der Routenwert für `area` als *Umgebungswert* für das Routing verfügbar und kann für die URL-Generierung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-896">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="a45e6-897">Das bedeutet, dass Bereiche bei der URL-Generierung wie im folgenden Beispiel dargestellt standardmäßig *beständig* sind.</span><span class="sxs-lookup"><span data-stu-id="a45e6-897">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="a45e6-898">Verstehen von IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="a45e6-898">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="a45e6-899">Dieser Abschnitt enthält Details zu Framework-Mechanismen und dazu, wie eine auszuführende Aktion in MVC auswählt wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-899">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="a45e6-900">Eine typische Anwendung benötigt kein benutzerdefiniertes `IActionConstraint`.</span><span class="sxs-lookup"><span data-stu-id="a45e6-900">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="a45e6-901">Sie haben `IActionConstraint` wahrscheinlich bereits verwendet, auch wenn Sie mit der Oberfläche noch nicht vertraut sind.</span><span class="sxs-lookup"><span data-stu-id="a45e6-901">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="a45e6-902">Das `[HttpGet]`-Attribut und ähnliche `[Http-VERB]`-Attribute implementieren `IActionConstraint`, um die Ausführung einer Aktionsmethode einzuschränken.</span><span class="sxs-lookup"><span data-stu-id="a45e6-902">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="a45e6-903">Unter Annahme der herkömmlichen Standardroute würde der URL-Pfad `/Products/Edit` die Werte `{ controller = Products, action = Edit }` erzeugen, die **beiden** hier gezeigten Aktionen entsprechen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-903">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="a45e6-904">In `IActionConstraint`-Terminologie würde man sagen, dass es sich bei beiden genannten Aktionen um Kandidaten handelt, da beide den Routendaten entsprechen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-904">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="a45e6-905">Wenn `HttpGetAttribute` ausgeführt wird, wird angegeben, dass *Edit()* mit *GET* übereinstimmt und mit keinem anderen HTTP-Verb.</span><span class="sxs-lookup"><span data-stu-id="a45e6-905">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="a45e6-906">Für die `Edit(...)`-Aktion wurde keine Einschränkungen definiert, und daher stimmt sie mit allen HTTP-Verben überein.</span><span class="sxs-lookup"><span data-stu-id="a45e6-906">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="a45e6-907">Wenn wir von `POST` ausgehen, stimmt nur `Edit(...)` überein.</span><span class="sxs-lookup"><span data-stu-id="a45e6-907">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="a45e6-908">Für `GET` können jedoch beide Aktionen übereinstimmen. Eine Aktion mit `IActionConstraint` wird einer Aktion ohne jedoch immer *vorgezogen*.</span><span class="sxs-lookup"><span data-stu-id="a45e6-908">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="a45e6-909">Da `Edit()` über `[HttpGet]` verfügt, gilt die Methode als spezifischer und wird ausgewählt, wenn beide Aktionen das Verb erkennen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-909">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="a45e6-910">Im Prinzip ist `IActionConstraint` eine Form der *Überladung*. Anstatt jedoch Methoden mit demselben Namen zu überladen, führt es zu einer Überladung zwischen Aktionen, die dieselbe URL erkennen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-910">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="a45e6-911">Beim Attributrouting wird auch `IActionConstraint` verwendet. Aus diesem Grund kann es zu Aktionen aus verschiedenen Controllern führen, die beide als Kandidaten gelten.</span><span class="sxs-lookup"><span data-stu-id="a45e6-911">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="a45e6-912">Implementieren von IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="a45e6-912">Implementing IActionConstraint</span></span>

<span data-ttu-id="a45e6-913">`IActionConstraint` lässt sich am einfachsten erstellen, indem Sie eine abgeleitete Klasse von `System.Attribute` erstellen und sie auf Ihren Aktionen und Controllern platzieren.</span><span class="sxs-lookup"><span data-stu-id="a45e6-913">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="a45e6-914">MVC erkennt automatisch jedes `IActionConstraint`, das als Attribut verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-914">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="a45e6-915">Sie können mithilfe des Anwendungsmodells Einschränkungen anwenden. Dies ist wahrscheinlich die flexibelste Herangehensweise, da Sie über Metaprogrammierung festlegen können, wie die Einschränkungen angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="a45e6-915">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="a45e6-916">Im folgenden Beispiel wählt eine Einschränkung eine Aktion aus, die auf einem *Ländercode* aus den Routendaten basiert.</span><span class="sxs-lookup"><span data-stu-id="a45e6-916">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="a45e6-917">[Das vollständige Beispiel finden Sie auf GitHub.](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs)</span><span class="sxs-lookup"><span data-stu-id="a45e6-917">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

<span data-ttu-id="a45e6-918">Sie sind für die Implementierung der `Accept`-Methode und die Festlegung einer Reihenfolge (Order) verantwortlich, in der die Einschränkung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-918">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="a45e6-919">In diesem Fall gibt die `Accept`-Methode `true` zurück, um zu kennzeichnen, dass die Aktion eine Übereinstimmung ist, wenn die `country`-Routenwerte übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="a45e6-919">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="a45e6-920">Dies unterscheidet sich von `RouteValueAttribute` dahingehend, dass ein Fallback auf eine Aktion ohne Attribute zulässig ist.</span><span class="sxs-lookup"><span data-stu-id="a45e6-920">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="a45e6-921">In dem Beispiel wird gezeigt, dass wenn Sie eine `en-US`-Aktion definieren, ein Ländercode wie `fr-FR` auf einen generischeren Controller zurückgreift, für den `[CountrySpecific(...)]` nicht angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="a45e6-921">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="a45e6-922">Die `Order`-Eigenschaft entscheidet, zu welcher *Phase* die Einschränkung gehört.</span><span class="sxs-lookup"><span data-stu-id="a45e6-922">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="a45e6-923">Aktionseinschränkungen werden auf Grundlage von `Order` in Gruppen ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-923">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="a45e6-924">Beispiel: Alle vom Framework bereitgestellten HTTP-Methodenattribute verwenden denselben `Order`-Wert, sodass sie in derselben Phase ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="a45e6-924">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="a45e6-925">Es kann so viele Phasen geben, wie zur Implementierung der gewünschten Richtlinien notwendig.</span><span class="sxs-lookup"><span data-stu-id="a45e6-925">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="a45e6-926">Bei der Entscheidung über einen Wert für `Order` sollten Sie berücksichtigen, ob die Einschränkung vor HTTP-Methoden angewendet werden soll oder nicht.</span><span class="sxs-lookup"><span data-stu-id="a45e6-926">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="a45e6-927">Niedrigere Zahlen werden zuerst ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="a45e6-927">Lower numbers run first.</span></span>

::: moniker-end

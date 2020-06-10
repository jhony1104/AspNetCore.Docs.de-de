---
title: Routing zu Controlleraktionen in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie ASP.NET Core MVC Routingmiddleware verwendet, um die URLs der eingehenden Anforderungen abzugleichen und sie Aktionen zuzuordnen.
ms.author: riande
ms.date: 3/25/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/routing
ms.openlocfilehash: 0078ed5d4aa30ff7a7b76af8dffd015f2557e165
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/09/2020
ms.locfileid: "83998652"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="cab35-103">Routing zu Controlleraktionen in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cab35-103">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="cab35-104">Von [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cab35-104">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cab35-105">ASP.net Core Controller verwenden die Routing [Middleware](xref:fundamentals/middleware/index) , um die URLs eingehender Anforderungen abzugleichen und Sie [Aktionen](#action)zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="cab35-105">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="cab35-106">Routen Vorlagen:</span><span class="sxs-lookup"><span data-stu-id="cab35-106">Routes templates:</span></span>

* <span data-ttu-id="cab35-107">Werden in Startcode oder Attributen definiert.</span><span class="sxs-lookup"><span data-stu-id="cab35-107">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="cab35-108">Beschreiben Sie, wie URL-Pfade mit [Aktionen](#action)abgeglichen werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-108">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="cab35-109">Werden verwendet, um URLs für Verknüpfungen zu generieren.</span><span class="sxs-lookup"><span data-stu-id="cab35-109">Are used to generate URLs for links.</span></span> <span data-ttu-id="cab35-110">Die generierten Verknüpfungen werden in der Regel in Antworten zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="cab35-110">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="cab35-111">Aktionen werden entweder [konventionell](#cr) oder [Attribut](#ar)weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="cab35-111">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="cab35-112">Durch das Platzieren einer Route auf dem Controller oder der [Aktion](#action) wird das Attribut geroutet.</span><span class="sxs-lookup"><span data-stu-id="cab35-112">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="cab35-113">Weitere Informationen finden Sie im Abschnitt [Gemischtes Routing](#routing-mixed-ref-label).</span><span class="sxs-lookup"><span data-stu-id="cab35-113">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="cab35-114">Dieses Dokument:</span><span class="sxs-lookup"><span data-stu-id="cab35-114">This document:</span></span>

* <span data-ttu-id="cab35-115">Erläutert die Interaktionen zwischen MVC und Routing:</span><span class="sxs-lookup"><span data-stu-id="cab35-115">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="cab35-116">Verwendung von Routing Features durch typische MVC-apps</span><span class="sxs-lookup"><span data-stu-id="cab35-116">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="cab35-117">Umfasst beides:</span><span class="sxs-lookup"><span data-stu-id="cab35-117">Covers both:</span></span>
    * <span data-ttu-id="cab35-118">[Konventionell Routing](#cr) , das in der Regel für Controller und Ansichten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-118">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="cab35-119">*Attribut Routing* , das mit Rest-APIs verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-119">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="cab35-120">Wenn Sie hauptsächlich an der Weiterleitung von Rest-APIs interessiert sind, springen Sie zum Abschnitt [Attribut Routing für Rest-APIs](#ar) .</span><span class="sxs-lookup"><span data-stu-id="cab35-120">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="cab35-121">Weitere Informationen finden Sie unter [Routing](xref:fundamentals/routing) für erweiterte Routing Details.</span><span class="sxs-lookup"><span data-stu-id="cab35-121">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="cab35-122">Bezieht sich auf das Standard Routing System, das in ASP.net Core 3,0 hinzugefügt wurde, dem sogenannten Endpunkt Routing.</span><span class="sxs-lookup"><span data-stu-id="cab35-122">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="cab35-123">Es ist möglich, Controller mit der vorherigen Version des Routings zu Kompatibilitätszwecken zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="cab35-123">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="cab35-124">Anweisungen hierzu finden Sie im [2.2-3.0-Migrations Handbuch](xref:migration/22-to-30) .</span><span class="sxs-lookup"><span data-stu-id="cab35-124">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="cab35-125">Das Referenzmaterial zum Legacy Routing System finden Sie in der [Version 2,2 dieses Dokuments](xref:mvc/controllers/routing?view=aspnetcore-2.2) .</span><span class="sxs-lookup"><span data-stu-id="cab35-125">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="cab35-126">Einrichten der herkömmlichen Route</span><span class="sxs-lookup"><span data-stu-id="cab35-126">Set up conventional route</span></span>

<span data-ttu-id="cab35-127">`Startup.Configure`in der Regel ist Code ähnlich dem folgenden, wenn [herkömmliches Routing](#crd)verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="cab35-127">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="cab35-128">Innerhalb des <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> Aufrufes wird zum Erstellen einer einzelnen Route verwendet.</span><span class="sxs-lookup"><span data-stu-id="cab35-128">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> is used to create a single route.</span></span> <span data-ttu-id="cab35-129">Die einzelne Route heißt `default` Route.</span><span class="sxs-lookup"><span data-stu-id="cab35-129">The single route is named `default` route.</span></span> <span data-ttu-id="cab35-130">Die meisten apps mit Controllern und Ansichten verwenden eine Routen Vorlage ähnlich der `default` Route.</span><span class="sxs-lookup"><span data-stu-id="cab35-130">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="cab35-131">Rest-APIs sollten [Attribut Routing](#ar)verwenden.</span><span class="sxs-lookup"><span data-stu-id="cab35-131">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="cab35-132">Die Routen Vorlage `"{controller=Home}/{action=Index}/{id?}"` :</span><span class="sxs-lookup"><span data-stu-id="cab35-132">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="cab35-133">Entspricht einem URL-Pfad wie`/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="cab35-133">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="cab35-134">Extrahiert die Routen Werte, `{ controller = Products, action = Details, id = 5 }` indem der Pfad mit einem Token versehen wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-134">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="cab35-135">Die Extraktion von Routen Werten führt zu einer Abstimmung, wenn die APP einen Controller mit dem Namen `ProductsController` und eine `Details` Aktion aufweist:</span><span class="sxs-lookup"><span data-stu-id="cab35-135">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* <span data-ttu-id="cab35-136">`/Products/Details/5`Modell bindet den Wert von `id = 5` , um den- `id` Parameter auf festzulegen `5` .</span><span class="sxs-lookup"><span data-stu-id="cab35-136">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="cab35-137">Weitere Informationen finden Sie unter [Modell Bindung](xref:mvc/models/model-binding) .</span><span class="sxs-lookup"><span data-stu-id="cab35-137">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="cab35-138">`{controller=Home}`definiert `Home` als Standard `controller` .</span><span class="sxs-lookup"><span data-stu-id="cab35-138">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="cab35-139">`{action=Index}`definiert `Index` als Standard `action` .</span><span class="sxs-lookup"><span data-stu-id="cab35-139">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="cab35-140">Das `?` Zeichen in `{id?}` definiert `id` als optional.</span><span class="sxs-lookup"><span data-stu-id="cab35-140">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="cab35-141">Standardmäßige und optionale Routenparameter müssen nicht im URL-Pfad vorhanden sein, damit es eine Übereinstimmung gibt.</span><span class="sxs-lookup"><span data-stu-id="cab35-141">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="cab35-142">Eine ausführliche Beschreibung der Syntax der Routenvorlage finden Sie unter [Routenvorlagenreferenz](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="cab35-142">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="cab35-143">Entspricht dem URL-Pfad `/` .</span><span class="sxs-lookup"><span data-stu-id="cab35-143">Matches the URL path `/`.</span></span>
* <span data-ttu-id="cab35-144">Erzeugt die Routen Werte `{ controller = Home, action = Index }` .</span><span class="sxs-lookup"><span data-stu-id="cab35-144">Produces the route values `{ controller = Home, action = Index }`.</span></span>

<span data-ttu-id="cab35-145">Die Werte für `controller` und `action` verwenden die Standardwerte.</span><span class="sxs-lookup"><span data-stu-id="cab35-145">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="cab35-146">`id`erzeugt keinen Wert, da kein entsprechendes Segment im URL-Pfad vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="cab35-146">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="cab35-147">`/`stimmt nur überein, wenn eine `HomeController` and-Aktion vorhanden ist `Index` :</span><span class="sxs-lookup"><span data-stu-id="cab35-147">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="cab35-148">Mithilfe der vorangehenden Controller Definition und Routen Vorlage `HomeController.Index` wird die Aktion für die folgenden URL-Pfade ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="cab35-148">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="cab35-149">Der URL `/` -Pfad verwendet die Routen Vorlagen `Home` -Standard Controller und- `Index` Aktion.</span><span class="sxs-lookup"><span data-stu-id="cab35-149">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="cab35-150">Der URL-Pfad `/Home` verwendet die Standardaktion der Routen Vorlage `Index` .</span><span class="sxs-lookup"><span data-stu-id="cab35-150">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="cab35-151">Mit der Hilfsmethode <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span><span class="sxs-lookup"><span data-stu-id="cab35-151">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="cab35-152">Arbeits</span><span class="sxs-lookup"><span data-stu-id="cab35-152">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> <span data-ttu-id="cab35-153">Das Routing wird mithilfe der <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> Middleware und konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="cab35-153">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> middleware.</span></span> <span data-ttu-id="cab35-154">So verwenden Sie Controller:</span><span class="sxs-lookup"><span data-stu-id="cab35-154">To use controllers:</span></span>
>
> * <span data-ttu-id="cab35-155">Ruft <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> in `UseEndpoints` auf, um [Attribut](#ar) Routing Controller zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="cab35-155">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
> * <span data-ttu-id="cab35-156"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>Wird oder aufgerufen <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> , um sowohl [konventionell geroutete](#cr) Controller als auch Attribut Routing Controller [zuzuordnen](#ar) .</span><span class="sxs-lookup"><span data-stu-id="cab35-156">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>, to map both [conventionally routed](#cr) controllers and [attribute routed](#ar) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="cab35-157">Herkömmliches Routing</span><span class="sxs-lookup"><span data-stu-id="cab35-157">Conventional routing</span></span>

<span data-ttu-id="cab35-158">Herkömmliches Routing wird mit Controllern und Ansichten verwendet.</span><span class="sxs-lookup"><span data-stu-id="cab35-158">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="cab35-159">Die `default`-Route:</span><span class="sxs-lookup"><span data-stu-id="cab35-159">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="cab35-160">ist ein Beispiel für *herkömmliches Routing*.</span><span class="sxs-lookup"><span data-stu-id="cab35-160">is an example of a *conventional routing*.</span></span> <span data-ttu-id="cab35-161">Es wird als *herkömmliches Routing* bezeichnet, da es eine *Konvention* für URL-Pfade festlegt:</span><span class="sxs-lookup"><span data-stu-id="cab35-161">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="cab35-162">Das erste Pfad Segment, `{controller=Home}` , wird dem Controller Namen zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="cab35-162">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="cab35-163">Das zweite Segment, `{action=Index}` , wird dem [Aktions](#action) Namen zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="cab35-163">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="cab35-164">Das dritte Segment `{id?}` wird für eine optionale verwendet `id` .</span><span class="sxs-lookup"><span data-stu-id="cab35-164">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="cab35-165">Die `?` in `{id?}` macht Sie optional.</span><span class="sxs-lookup"><span data-stu-id="cab35-165">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="cab35-166">`id`wird verwendet, um einer Modell Entität zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="cab35-166">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="cab35-167">Verwenden Sie diese `default` Route, den URL-Pfad:</span><span class="sxs-lookup"><span data-stu-id="cab35-167">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="cab35-168">`/Products/List`wird der- `ProductsController.List` Aktion zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="cab35-168">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="cab35-169">`/Blog/Article/17`wird zugeordnet, und in der `BlogController.Article` Regel bindet Model den- `id` Parameter an 17.</span><span class="sxs-lookup"><span data-stu-id="cab35-169">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="cab35-170">Diese Zuordnung:</span><span class="sxs-lookup"><span data-stu-id="cab35-170">This mapping:</span></span>

* <span data-ttu-id="cab35-171">Basiert **nur**auf den Controller-und [Aktions](#action) Namen.</span><span class="sxs-lookup"><span data-stu-id="cab35-171">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="cab35-172">Basiert nicht auf Namespaces, Quelldatei Speicherorten oder Methoden Parametern.</span><span class="sxs-lookup"><span data-stu-id="cab35-172">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="cab35-173">Die Verwendung von herkömmlichem Routing mit der Standardroute ermöglicht das Erstellen der APP, ohne für jede Aktion ein neues URL-Muster zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="cab35-173">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="cab35-174">Für eine APP mit [CRUD-](https://wikipedia.org/wiki/Create,_read,_update_and_delete) Stil Aktionen mit Konsistenz für die URLs über Controller hinweg:</span><span class="sxs-lookup"><span data-stu-id="cab35-174">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="cab35-175">Erleichtert das Vereinfachen des Codes.</span><span class="sxs-lookup"><span data-stu-id="cab35-175">Helps simplify the code.</span></span>
* <span data-ttu-id="cab35-176">Macht die Benutzeroberfläche berechenbarer.</span><span class="sxs-lookup"><span data-stu-id="cab35-176">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="cab35-177">Der `id` im vorangehenden Code wird von der Routen Vorlage als optional definiert.</span><span class="sxs-lookup"><span data-stu-id="cab35-177">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="cab35-178">Aktionen können ausgeführt werden, ohne dass die optionale ID als Teil der URL bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-178">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="cab35-179">Im Allgemeinen gilt, wenn in `id` der URL weggelassen wird:</span><span class="sxs-lookup"><span data-stu-id="cab35-179">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="cab35-180">`id`wird `0` von der Modell Bindung auf festgelegt.</span><span class="sxs-lookup"><span data-stu-id="cab35-180">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="cab35-181">Es wurde keine Entität in der Daten Bank Übereinstimmung gefunden `id == 0` .</span><span class="sxs-lookup"><span data-stu-id="cab35-181">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="cab35-182">Das [Attribut Routing](#ar) ermöglicht eine differenzierte Steuerung, um die erforderliche ID für einige Aktionen und nicht für andere zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="cab35-182">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="cab35-183">Gemäß der Konvention enthält die Dokumentation optionale Parameter, z `id` . b., wenn Sie wahrscheinlich in der richtigen Verwendung angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-183">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="cab35-184">Für die meisten Apps sollte eine grundlegendes und beschreibendes Routingschema ausgewählt werden, um lesbare und aussagekräftige URLs zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="cab35-184">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="cab35-185">Für die konventionelle Standardroute `{controller=Home}/{action=Index}/{id?}` gilt:</span><span class="sxs-lookup"><span data-stu-id="cab35-185">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="cab35-186">Sie unterstützt ein grundlegendes und beschreibendes Routingschema.</span><span class="sxs-lookup"><span data-stu-id="cab35-186">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="cab35-187">Sie stellt einen nützlichen Startpunkt für benutzeroberflächenbasierte Apps dar.</span><span class="sxs-lookup"><span data-stu-id="cab35-187">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="cab35-188">Ist die einzige Routen Vorlage, die für viele Web-UI-apps benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-188">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="cab35-189">Bei größeren Webanwendungs-Apps ist eine weitere Route, die [Bereiche](#areas) verwendet, wenn diese häufig benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-189">For larger web UI apps, another route using [Areas](#areas) if frequently all that's needed.</span></span>

<span data-ttu-id="cab35-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>und <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span><span class="sxs-lookup"><span data-stu-id="cab35-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span></span>

* <span data-ttu-id="cab35-191">Weisen Sie Ihren Endpunkten automatisch einen **Bestell** Wert basierend auf der Reihenfolge zu, in der Sie aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-191">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="cab35-192">Endpunktrouting in ASP.NET Core 3.0 und höher:</span><span class="sxs-lookup"><span data-stu-id="cab35-192">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="cab35-193">Weist kein Konzept für Routen auf.</span><span class="sxs-lookup"><span data-stu-id="cab35-193">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="cab35-194">Bietet keine Bestell Garantien für die Ausführung der Erweiterbarkeit. alle Endpunkte werden gleichzeitig verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="cab35-194">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="cab35-195">Wenn Sie die [Protokollierung](xref:fundamentals/logging/index) aktivieren, erfahren Sie, wie die integrierten Routingimplementierungen (z.B. <xref:Microsoft.AspNetCore.Routing.Route>) Zuordnungen für Anforderungen ermitteln.</span><span class="sxs-lookup"><span data-stu-id="cab35-195">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="cab35-196">Das [Attribut Routing](#ar) wird weiter unten in diesem Dokument erläutert.</span><span class="sxs-lookup"><span data-stu-id="cab35-196">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="cab35-197">Mehrere herkömmliche Routen</span><span class="sxs-lookup"><span data-stu-id="cab35-197">Multiple conventional routes</span></span>

<span data-ttu-id="cab35-198">Mehrere [konventionelle Routen](#cr) können in hinzugefügt werden `UseEndpoints` , indem weitere Aufrufe von und hinzugefügt werden <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> .</span><span class="sxs-lookup"><span data-stu-id="cab35-198">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span></span> <span data-ttu-id="cab35-199">Dies ermöglicht das Definieren mehrerer Konventionen oder das Hinzufügen herkömmlicher Routen, die einer bestimmten [Aktion](#action)zugeordnet sind, wie z. b.:</span><span class="sxs-lookup"><span data-stu-id="cab35-199">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="cab35-200">Die `blog` Route im vorangehenden Code ist eine **dedizierte konventionelle Route**.</span><span class="sxs-lookup"><span data-stu-id="cab35-200">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="cab35-201">Dies wird als dedizierte konventionelle Route bezeichnet:</span><span class="sxs-lookup"><span data-stu-id="cab35-201">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="cab35-202">Es verwendet [herkömmliches Routing](#cr).</span><span class="sxs-lookup"><span data-stu-id="cab35-202">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="cab35-203">Es ist für eine bestimmte [Aktion](#action)vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="cab35-203">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="cab35-204">Da `controller` und `action` nicht in der Routen Vorlage `"blog/{*article}"` als Parameter angezeigt werden:</span><span class="sxs-lookup"><span data-stu-id="cab35-204">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="cab35-205">Sie können nur die Standardwerte aufweisen `{ controller = "Blog", action = "Article" }` .</span><span class="sxs-lookup"><span data-stu-id="cab35-205">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="cab35-206">Diese Route wird immer der Aktion zugeordnet `BlogController.Article` .</span><span class="sxs-lookup"><span data-stu-id="cab35-206">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="cab35-207">`/Blog`, `/Blog/Article` und `/Blog/{any-string}` sind die einzigen URL-Pfade, die der Blog Route entsprechen.</span><span class="sxs-lookup"><span data-stu-id="cab35-207">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="cab35-208">Das vorherige Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cab35-208">The preceding example:</span></span>

* <span data-ttu-id="cab35-209">`blog`Route hat eine höhere Priorität als die Route, `default` da Sie zuerst hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-209">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="cab35-210">Ist ein Beispiel für das Routing im [Slug](https://developer.mozilla.org/docs/Glossary/Slug) -Format, in dem normalerweise ein Artikelname als Teil der URL vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="cab35-210">Is and example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="cab35-211">In ASP.net Core 3,0 und höher ist das Routing nicht:</span><span class="sxs-lookup"><span data-stu-id="cab35-211">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="cab35-212">Definieren Sie ein Konzept, das als *Route*bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-212">Define a concept called a *route*.</span></span> <span data-ttu-id="cab35-213">`UseRouting` fügt der Middlewarepipeline einen Routenabgleich hinzu.</span><span class="sxs-lookup"><span data-stu-id="cab35-213">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="cab35-214">Die `UseRouting` Middleware prüft den Satz von Endpunkten, die in der APP definiert sind, und wählt basierend auf der Anforderung die beste Endpunkt Übereinstimmung aus.</span><span class="sxs-lookup"><span data-stu-id="cab35-214">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="cab35-215">Stellen Sie Garantien für die Ausführungsreihenfolge der Erweiterbarkeit wie <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> oder bereit <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> .</span><span class="sxs-lookup"><span data-stu-id="cab35-215">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="cab35-216">Referenzmaterial zum Routing finden Sie unter [Routing](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="cab35-216">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="cab35-217">Konventionelle Routing Reihenfolge</span><span class="sxs-lookup"><span data-stu-id="cab35-217">Conventional routing order</span></span>

<span data-ttu-id="cab35-218">Herkömmliches Routing stimmt nur mit einer Kombination aus Aktion und Controller überein, die von der APP definiert werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-218">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="cab35-219">Dies dient zur Vereinfachung der Fälle, in denen herkömmliche Routen sich überlappen.</span><span class="sxs-lookup"><span data-stu-id="cab35-219">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="cab35-220">Durch das Hinzufügen von Routen mithilfe von <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> , <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*> und wird <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> ihren Endpunkten automatisch ein Bestellwert entsprechend der Reihenfolge zugewiesen, in der Sie aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-220">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="cab35-221">Übereinstimmungen aus einer zuvor angezeigten Route haben eine höhere Priorität.</span><span class="sxs-lookup"><span data-stu-id="cab35-221">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="cab35-222">Beim herkömmlichen Routing ist die Reihenfolge wichtig.</span><span class="sxs-lookup"><span data-stu-id="cab35-222">Conventional routing is order-dependent.</span></span> <span data-ttu-id="cab35-223">Im Allgemeinen sollten Routen mit Bereichen früher platziert werden, da Sie spezifischer als Routen ohne Bereich sind.</span><span class="sxs-lookup"><span data-stu-id="cab35-223">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="cab35-224">[Dedizierte herkömmliche Routen](#dcr) mit "Catch-All"-Routen Parametern wie `{*article}` können eine Route zu [gierig](xref:fundamentals/routing#greedy)machen, was bedeutet, dass Sie mit den URLs übereinstimmt, die Sie mit anderen Routen vergleichen wollten.</span><span class="sxs-lookup"><span data-stu-id="cab35-224">[Dedicated conventional routes](#dcr) with catch-all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="cab35-225">Platzieren Sie die gierigen Routen später in der Routing Tabelle, um gierige Übereinstimmungen zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="cab35-225">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

[!INCLUDE[](~/includes/catchall.md)]

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="cab35-226">Auflösen mehrdeutiger Aktionen</span><span class="sxs-lookup"><span data-stu-id="cab35-226">Resolving ambiguous actions</span></span>

<span data-ttu-id="cab35-227">Wenn zwei Endpunkte über das Routing abgleichen, muss das Routing einen der folgenden Schritte ausführen:</span><span class="sxs-lookup"><span data-stu-id="cab35-227">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="cab35-228">Wählen Sie den besten Kandidaten aus.</span><span class="sxs-lookup"><span data-stu-id="cab35-228">Choose the best candidate.</span></span>
* <span data-ttu-id="cab35-229">Löst eine Ausnahme aus.</span><span class="sxs-lookup"><span data-stu-id="cab35-229">Throw an exception.</span></span>

<span data-ttu-id="cab35-230">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cab35-230">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="cab35-231">Der vorangehende Controller definiert zwei Aktionen, die mit identisch sind:</span><span class="sxs-lookup"><span data-stu-id="cab35-231">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="cab35-232">Der URL-Pfad.`/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="cab35-232">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="cab35-233">Weiterleiten von Daten `{ controller = Products33, action = Edit, id = 17 }` .</span><span class="sxs-lookup"><span data-stu-id="cab35-233">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="cab35-234">Dies ist ein typisches Muster für MVC-Controller:</span><span class="sxs-lookup"><span data-stu-id="cab35-234">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="cab35-235">`Edit(int)`zeigt ein Formular zum Bearbeiten eines Produkts an.</span><span class="sxs-lookup"><span data-stu-id="cab35-235">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="cab35-236">`Edit(int, Product)`verarbeitet das gepostete Formular.</span><span class="sxs-lookup"><span data-stu-id="cab35-236">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="cab35-237">So beheben Sie die korrekte Route:</span><span class="sxs-lookup"><span data-stu-id="cab35-237">To resolve the correct route:</span></span>

* <span data-ttu-id="cab35-238">`Edit(int, Product)`wird ausgewählt, wenn die Anforderung ein HTTP ist `POST` .</span><span class="sxs-lookup"><span data-stu-id="cab35-238">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="cab35-239">`Edit(int)`wird ausgewählt, wenn das [http-Verb](#verb) etwas anderes ist.</span><span class="sxs-lookup"><span data-stu-id="cab35-239">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="cab35-240">`Edit(int)`wird im Allgemeinen über aufgerufen `GET` .</span><span class="sxs-lookup"><span data-stu-id="cab35-240">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="cab35-241">Die <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , `[HttpPost]` , wird für das Routing bereitgestellt, sodass Sie basierend auf der HTTP-Methode der Anforderung ausgewählt werden kann.</span><span class="sxs-lookup"><span data-stu-id="cab35-241">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="cab35-242">Der `HttpPostAttribute` ist `Edit(int, Product)` besser geeignet als `Edit(int)` .</span><span class="sxs-lookup"><span data-stu-id="cab35-242">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="cab35-243">Es ist wichtig, die Rolle von Attributen wie zu verstehen `HttpPostAttribute` .</span><span class="sxs-lookup"><span data-stu-id="cab35-243">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="cab35-244">Ähnliche Attribute werden für andere [HTTP-Verben](#verb)definiert.</span><span class="sxs-lookup"><span data-stu-id="cab35-244">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="cab35-245">Bei [herkömmlichem Routing](#cr)wird bei Aktionen häufig derselbe Aktionsname verwendet, wenn Sie Teil eines anshow Formulars sind, Formular Workflow senden.</span><span class="sxs-lookup"><span data-stu-id="cab35-245">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="cab35-246">Informationen hierzu finden Sie beispielsweise [unter Überprüfen der beiden Bearbeitungs Aktionsmethoden](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span><span class="sxs-lookup"><span data-stu-id="cab35-246">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="cab35-247">Wenn das Routing keinen besten Kandidaten auswählen kann, <xref:System.Reflection.AmbiguousMatchException> wird eine ausgelöst, die mehrere übereinstimmende Endpunkte auflistet.</span><span class="sxs-lookup"><span data-stu-id="cab35-247">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="cab35-248">Herkömmliche Routennamen</span><span class="sxs-lookup"><span data-stu-id="cab35-248">Conventional route names</span></span>

<span data-ttu-id="cab35-249">Die Zeichen `"blog"` `"default"` folgen und in den folgenden Beispielen sind konventionelle Routennamen:</span><span class="sxs-lookup"><span data-stu-id="cab35-249">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="cab35-250">Mit den Routennamen wird der Route ein logischer Name zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="cab35-250">The route names give the route a logical name.</span></span> <span data-ttu-id="cab35-251">Die benannte Route kann für die URL-Generierung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-251">The named route can be used for URL generation.</span></span> <span data-ttu-id="cab35-252">Die Verwendung einer benannten Route vereinfacht die URL-Erstellung, wenn die Reihenfolge der Routen die URL-Generierung erschweren könnte.</span><span class="sxs-lookup"><span data-stu-id="cab35-252">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="cab35-253">Routennamen müssen eine eindeutige Anwendungs weite sein.</span><span class="sxs-lookup"><span data-stu-id="cab35-253">Route names must be unique application wide.</span></span>

<span data-ttu-id="cab35-254">Routennamen:</span><span class="sxs-lookup"><span data-stu-id="cab35-254">Route names:</span></span>

* <span data-ttu-id="cab35-255">Hat keine Auswirkung auf die URL-Übereinstimmung oder-Verarbeitung von Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="cab35-255">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="cab35-256">Werden nur für die URL-Generierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="cab35-256">Are used only for URL generation.</span></span>

<span data-ttu-id="cab35-257">Das Routing Namenskonzept wird als " [iendpointnamemetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata)" dargestellt.</span><span class="sxs-lookup"><span data-stu-id="cab35-257">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="cab35-258">Die Begriffe **Routen Name** und **Endpunkt Name**:</span><span class="sxs-lookup"><span data-stu-id="cab35-258">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="cab35-259">Sind austauschbar.</span><span class="sxs-lookup"><span data-stu-id="cab35-259">Are interchangeable.</span></span>
* <span data-ttu-id="cab35-260">Welche Informationen in der Dokumentation und im Code verwendet werden, hängt von der API ab, die beschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-260">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="cab35-261">Attribut Routing für Rest-APIs</span><span class="sxs-lookup"><span data-stu-id="cab35-261">Attribute routing for REST APIs</span></span>

<span data-ttu-id="cab35-262">Rest-APIs sollten das Attribut Routing verwenden, um die Funktionalität der App als Satz von Ressourcen zu modellieren, in denen Vorgänge durch [HTTP-Verben](#verb)dargestellt werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-262">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="cab35-263">Beim Attributrouting werden Aktionen mithilfe von Attributen direkt Routenvorlagen zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="cab35-263">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="cab35-264">Der folgende `StartUp.Configure` Code ist für eine Rest-API typisch und wird im nächsten Beispiel verwendet:</span><span class="sxs-lookup"><span data-stu-id="cab35-264">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupAPI.cs?name=snippet)]

<span data-ttu-id="cab35-265">Im vorangehenden Code <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> wird innerhalb von aufgerufen, `UseEndpoints` um Attribut Routing Controller zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="cab35-265">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="cab35-266">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cab35-266">In the following example:</span></span>

* <span data-ttu-id="cab35-267">Die vorangehende `Configure` Methode wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="cab35-267">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="cab35-268">`HomeController`entspricht einem Satz von URLs, die mit der herkömmlichen Standardroute `{controller=Home}/{action=Index}/{id?}` übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="cab35-268">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="cab35-269">Die `HomeController.Index` Aktion wird für alle URL-Pfade `/` , `/Home` , oder ausgeführt `/Home/Index` `/Home/Index/3` .</span><span class="sxs-lookup"><span data-stu-id="cab35-269">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="cab35-270">In diesem Beispiel wird ein wichtiger Programmier Unterschied zwischen Attribut Routing und [herkömmlichem Routing](#cr)hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="cab35-270">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="cab35-271">Das Attribut Routing erfordert mehr Eingaben, um eine Route anzugeben.</span><span class="sxs-lookup"><span data-stu-id="cab35-271">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="cab35-272">Bei der herkömmlichen Standardroute werden Routen genauer behandelt.</span><span class="sxs-lookup"><span data-stu-id="cab35-272">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="cab35-273">Das Attribut Routing ermöglicht und erfordert jedoch eine genaue Kontrolle darüber, welche Routen Vorlagen auf die einzelnen [Aktionen](#action)angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-273">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="cab35-274">Beim Attribut Routing spielen die Controller-und Aktions Namen keinen Teil, in dem die Aktion abgeglichen wird, es sei denn, es wird eine [Tokenersetzung](#routing-token-replacement-templates-ref-label) verwendet.</span><span class="sxs-lookup"><span data-stu-id="cab35-274">With attribute routing, the controller and action names play no part in which action is matched, unless [token replacement](#routing-token-replacement-templates-ref-label) is used.</span></span> <span data-ttu-id="cab35-275">Das folgende Beispiel entspricht den gleichen URLs wie im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cab35-275">The following example matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="cab35-276">Der folgende Code verwendet die Tokenersetzung für `action` und `controller` :</span><span class="sxs-lookup"><span data-stu-id="cab35-276">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="cab35-277">Der folgende Code gilt `[Route("[controller]/[action]")]` für den Controller:</span><span class="sxs-lookup"><span data-stu-id="cab35-277">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="cab35-278">Im vorangehenden Code müssen die `Index` Methoden Vorlagen `/` oder `~/` den Routen Vorlagen vorangestellt werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-278">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="cab35-279">Routenvorlagen, die auf eine Aktion angewendet werden, die mit einem `/` oder `~/` beginnen, können nicht mit Routenvorlagen kombiniert werden, die auf den Controller angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-279">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="cab35-280">Weitere Informationen zur Routen Vorlagen Auswahl finden Sie unter [Routen Vorlagen Rangfolge](xref:fundamentals/routing#rtp) .</span><span class="sxs-lookup"><span data-stu-id="cab35-280">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="cab35-281">Reservierte Routingnamen</span><span class="sxs-lookup"><span data-stu-id="cab35-281">Reserved routing names</span></span>

<span data-ttu-id="cab35-282">Die folgenden Schlüsselwörter sind reservierte Routen Parameternamen bei der Verwendung von Controllern oder Razor Seiten:</span><span class="sxs-lookup"><span data-stu-id="cab35-282">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="cab35-283">Die Verwendung `page` von als Routen Parameter mit Attribut Routing ist ein häufiger Fehler.</span><span class="sxs-lookup"><span data-stu-id="cab35-283">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="cab35-284">Dies führt zu inkonsistentem und verwirdem Verhalten bei der URL-Generierung.</span><span class="sxs-lookup"><span data-stu-id="cab35-284">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="cab35-285">Die speziellen Parameternamen werden von der URL-Generierung verwendet, um zu bestimmen, ob ein URL-Generierungs Vorgang auf eine Razor Seite oder auf einen Controller verweist.</span><span class="sxs-lookup"><span data-stu-id="cab35-285">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="cab35-286">HTTP-Verb Vorlagen</span><span class="sxs-lookup"><span data-stu-id="cab35-286">HTTP verb templates</span></span>

<span data-ttu-id="cab35-287">ASP.net Core verfügt über die folgenden HTTP-Verb Vorlagen:</span><span class="sxs-lookup"><span data-stu-id="cab35-287">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="cab35-288">[HttpGet](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="cab35-288">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="cab35-289">[HttpPost](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="cab35-289">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="cab35-290">[HttpPut](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="cab35-290">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="cab35-291">[HttpDelete](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="cab35-291">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="cab35-292">[[Httphead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="cab35-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="cab35-293">[[Httppatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="cab35-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="cab35-294">Routenvorlagen</span><span class="sxs-lookup"><span data-stu-id="cab35-294">Route templates</span></span>

<span data-ttu-id="cab35-295">ASP.net Core verfügt über die folgenden Routen Vorlagen:</span><span class="sxs-lookup"><span data-stu-id="cab35-295">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="cab35-296">Alle [http-Verb Vorlagen](#verb) sind Routen Vorlagen.</span><span class="sxs-lookup"><span data-stu-id="cab35-296">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="cab35-297">[Seeweg](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="cab35-297">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="cab35-298">Attribut Routing mit HTTP-Verb Attributen</span><span class="sxs-lookup"><span data-stu-id="cab35-298">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="cab35-299">Beachten Sie den folgenden Controller:</span><span class="sxs-lookup"><span data-stu-id="cab35-299">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="cab35-300">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="cab35-300">In the preceding code:</span></span>

* <span data-ttu-id="cab35-301">Jede Aktion enthält das- `[HttpGet]` Attribut, das die Übereinstimmung von HTTP-GET-Anforderungen einschränkt.</span><span class="sxs-lookup"><span data-stu-id="cab35-301">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="cab35-302">Die `GetProduct` Aktion enthält die `"{id}"` Vorlage und `id` wird daher an die `"api/[controller]"` Vorlage auf dem Controller angehängt.</span><span class="sxs-lookup"><span data-stu-id="cab35-302">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="cab35-303">Die Methoden Vorlage ist `"api/[controller]/"{id}""` .</span><span class="sxs-lookup"><span data-stu-id="cab35-303">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="cab35-304">Daher entspricht diese Aktion nur Get-Anforderungen von für das Formular `/api/test2/xyz` , `/api/test2/123` , `/api/test2/{any string}` usw.</span><span class="sxs-lookup"><span data-stu-id="cab35-304">Therefore this action only matches GET requests of for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="cab35-305">Die `GetIntProduct` Aktion enthält die `"int/{id:int}")` Vorlage.</span><span class="sxs-lookup"><span data-stu-id="cab35-305">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="cab35-306">Der `:int` Teil der Vorlage schränkt die `id` Routen Werte auf Zeichen folgen ein, die in eine ganze Zahl konvertiert werden können.</span><span class="sxs-lookup"><span data-stu-id="cab35-306">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="cab35-307">Eine GET-Anforderung für `/api/test2/int/abc` :</span><span class="sxs-lookup"><span data-stu-id="cab35-307">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="cab35-308">Entspricht dieser Aktion nicht.</span><span class="sxs-lookup"><span data-stu-id="cab35-308">Doesn't match this action.</span></span>
  * <span data-ttu-id="cab35-309">Gibt den Fehler " [404 nicht gefunden](https://developer.mozilla.org/docs/Web/HTTP/Status/404) " zurück.</span><span class="sxs-lookup"><span data-stu-id="cab35-309">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="cab35-310">Die `GetInt2Product` Aktion enthält `{id}` in der Vorlage, schränkt jedoch nicht `id` auf Werte ein, die in eine ganze Zahl konvertiert werden können.</span><span class="sxs-lookup"><span data-stu-id="cab35-310">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="cab35-311">Eine GET-Anforderung für `/api/test2/int2/abc` :</span><span class="sxs-lookup"><span data-stu-id="cab35-311">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="cab35-312">Entspricht dieser Route.</span><span class="sxs-lookup"><span data-stu-id="cab35-312">Matches this route.</span></span>
  * <span data-ttu-id="cab35-313">Die Modell Bindung kann nicht `abc` in eine ganze Zahl konvertiert werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-313">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="cab35-314">Der- `id` Parameter der-Methode ist Integer.</span><span class="sxs-lookup"><span data-stu-id="cab35-314">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="cab35-315">Gibt eine ungültige [400-Anforderung](https://developer.mozilla.org/docs/Web/HTTP/Status/400) zurück, da die Modell Bindung nicht in eine ganze Zahl konvertiert werden konnte `abc` .</span><span class="sxs-lookup"><span data-stu-id="cab35-315">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="cab35-316">Beim Attribut Routing können <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> Attribute wie <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute> und verwendet werden <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute> .</span><span class="sxs-lookup"><span data-stu-id="cab35-316">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="cab35-317">Alle HTTP- [Verb](#verb) Attribute akzeptieren eine Routen Vorlage.</span><span class="sxs-lookup"><span data-stu-id="cab35-317">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="cab35-318">Das folgende Beispiel zeigt zwei Aktionen, die derselben Routen Vorlage entsprechen:</span><span class="sxs-lookup"><span data-stu-id="cab35-318">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="cab35-319">Verwenden des URL-Pfads `/products3` :</span><span class="sxs-lookup"><span data-stu-id="cab35-319">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="cab35-320">Die `MyProductsController.ListProducts` Aktion wird ausgeführt, wenn das [http-Verb](#verb) den Wert hat `GET` .</span><span class="sxs-lookup"><span data-stu-id="cab35-320">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="cab35-321">Die `MyProductsController.CreateProduct` Aktion wird ausgeführt, wenn das [http-Verb](#verb) den Wert hat `POST` .</span><span class="sxs-lookup"><span data-stu-id="cab35-321">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="cab35-322">Beim Aufbau einer Rest-API müssen Sie `[Route(...)]` nur selten eine Aktionsmethode verwenden, da die Aktion alle HTTP-Methoden akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="cab35-322">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="cab35-323">Es ist besser, das spezifischere [http-Verb-Attribut](#verb) zu verwenden, um genau zu erfahren, was Ihre API unterstützt.</span><span class="sxs-lookup"><span data-stu-id="cab35-323">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="cab35-324">REST-API-Clients sollten wissen, welche Pfade und HTTP-Verben bestimmten logischen Operationen entsprechen.</span><span class="sxs-lookup"><span data-stu-id="cab35-324">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="cab35-325">Rest-APIs sollten das Attribut Routing verwenden, um die Funktionalität der App als Satz von Ressourcen zu modellieren, in denen Vorgänge durch HTTP-Verben dargestellt werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-325">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="cab35-326">Dies bedeutet, dass viele Vorgänge, z. b. Get und Post, für dieselbe logische Ressource dieselbe URL verwenden.</span><span class="sxs-lookup"><span data-stu-id="cab35-326">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="cab35-327">Das Attributrouting bietet eine Ebene der Steuerung, die für einen sorgfältigen Entwurf des öffentlichen Endpunktlayouts einer API erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="cab35-327">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="cab35-328">Da eine Attributroute für eine bestimmte Aktion gilt, ist es einfach, Parameter als Teil der Routenvorlagendefinition erforderlich festzulegen.</span><span class="sxs-lookup"><span data-stu-id="cab35-328">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="cab35-329">Im folgenden Beispiel `id` ist als Teil des URL-Pfads erforderlich:</span><span class="sxs-lookup"><span data-stu-id="cab35-329">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="cab35-330">Die `Products2ApiController.GetProduct(int)` Aktion:</span><span class="sxs-lookup"><span data-stu-id="cab35-330">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="cab35-331">Wird mit URL-Pfad wie`/products2/3`</span><span class="sxs-lookup"><span data-stu-id="cab35-331">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="cab35-332">Wird nicht mit dem URL-Pfad ausgeführt `/products2` .</span><span class="sxs-lookup"><span data-stu-id="cab35-332">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="cab35-333">Das [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)-Attribut ermöglicht es einer Aktion, die unterstützten Anforderungsinhaltstypen einzuschränken.</span><span class="sxs-lookup"><span data-stu-id="cab35-333">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="cab35-334">Weitere Informationen finden Sie [unter Definieren unterstützter Anforderungs Inhaltstypen mit dem Attribut "Verbrauchs](xref:web-api/index#consumes)".</span><span class="sxs-lookup"><span data-stu-id="cab35-334">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="cab35-335">Eine vollständige Beschreibung und Routenvorlagen und dazugehörige Optionen finden Sie unter [Routing in ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="cab35-335">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="cab35-336">Weitere Informationen zu `[ApiController]` finden Sie unter [apicontroller-Attribut](xref:web-api/index##apicontroller-attribute).</span><span class="sxs-lookup"><span data-stu-id="cab35-336">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="cab35-337">Routenname</span><span class="sxs-lookup"><span data-stu-id="cab35-337">Route name</span></span>

<span data-ttu-id="cab35-338">Im folgenden Code wird ein Routenname von `Products_List` definiert:</span><span class="sxs-lookup"><span data-stu-id="cab35-338">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="cab35-339">Routennamen können verwendet werden, um basierend auf einer bestimmten Route eine URL zu generieren.</span><span class="sxs-lookup"><span data-stu-id="cab35-339">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="cab35-340">Routennamen:</span><span class="sxs-lookup"><span data-stu-id="cab35-340">Route names:</span></span>

* <span data-ttu-id="cab35-341">Hat keine Auswirkung auf das URL-Vergleichs Verhalten des Routings.</span><span class="sxs-lookup"><span data-stu-id="cab35-341">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="cab35-342">Werden nur für die URL-Generierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="cab35-342">Are only used for URL generation.</span></span>

<span data-ttu-id="cab35-343">Routennamen müssen anwendungsweit eindeutig sein.</span><span class="sxs-lookup"><span data-stu-id="cab35-343">Route names must be unique application-wide.</span></span>

<span data-ttu-id="cab35-344">Vergleichen Sie den vorangehenden Code mit der herkömmlichen Standardroute, die den `id` Parameter als optional definiert ( `{id?}` ).</span><span class="sxs-lookup"><span data-stu-id="cab35-344">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="cab35-345">Die Möglichkeit, APIs genau anzugeben, bietet Vorteile, z. b. das zulassen `/products` und verteilen `/products/5` an verschiedene Aktionen.</span><span class="sxs-lookup"><span data-stu-id="cab35-345">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="cab35-346">Kombinieren von Attribut Routen</span><span class="sxs-lookup"><span data-stu-id="cab35-346">Combining attribute routes</span></span>

<span data-ttu-id="cab35-347">Um Attributrouting weniger repetitiv zu gestalten, werden Routenattribute auf dem Controller mit Routenattributen auf den einzelnen Aktionen kombiniert.</span><span class="sxs-lookup"><span data-stu-id="cab35-347">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="cab35-348">Alle auf dem Controller definierten Routenvorlagen werden den Routenvorlagen auf den Aktionen vorangestellt.</span><span class="sxs-lookup"><span data-stu-id="cab35-348">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="cab35-349">Wenn Routenattribute auf dem Controller platziert werden, verwenden **alle** Aktionen im Controller Attributrouting.</span><span class="sxs-lookup"><span data-stu-id="cab35-349">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="cab35-350">Im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cab35-350">In the preceding example:</span></span>

* <span data-ttu-id="cab35-351">Der URL-Pfad `/products` kann Stimmen.`ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="cab35-351">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="cab35-352">Der URL-Pfad kann abgeglichen werden `/products/5` `ProductsApi.GetProduct(int)` .</span><span class="sxs-lookup"><span data-stu-id="cab35-352">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="cab35-353">Beide Aktionen entsprechen nur http, `GET` da Sie mit dem-Attribut markiert sind `[HttpGet]` .</span><span class="sxs-lookup"><span data-stu-id="cab35-353">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="cab35-354">Routenvorlagen, die auf eine Aktion angewendet werden, die mit einem `/` oder `~/` beginnen, können nicht mit Routenvorlagen kombiniert werden, die auf den Controller angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-354">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="cab35-355">Das folgende Beispiel entspricht einem Satz von URL-Pfaden, die der Standardroute ähneln.</span><span class="sxs-lookup"><span data-stu-id="cab35-355">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="cab35-356">In der folgenden Tabelle werden die `[Route]` Attribute im vorangehenden Code erläutert:</span><span class="sxs-lookup"><span data-stu-id="cab35-356">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="cab35-357">Attribut</span><span class="sxs-lookup"><span data-stu-id="cab35-357">Attribute</span></span>               | <span data-ttu-id="cab35-358">Kombiniert mit`[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="cab35-358">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="cab35-359">Definiert die Routen Vorlage.</span><span class="sxs-lookup"><span data-stu-id="cab35-359">Defines route template</span></span> |
| ----------------- | ------------ | --------- |
| `[Route("")]` | <span data-ttu-id="cab35-360">Ja</span><span class="sxs-lookup"><span data-stu-id="cab35-360">Yes</span></span> | `"Home"` |
| `[Route("Index")]` | <span data-ttu-id="cab35-361">Ja</span><span class="sxs-lookup"><span data-stu-id="cab35-361">Yes</span></span> | `"Home/Index"` |
| `[Route("/")]` | <span data-ttu-id="cab35-362">**No**</span><span class="sxs-lookup"><span data-stu-id="cab35-362">**No**</span></span> | `""` |
| `[Route("About")]` | <span data-ttu-id="cab35-363">Ja</span><span class="sxs-lookup"><span data-stu-id="cab35-363">Yes</span></span> | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="cab35-364">Attribut Routen Reihenfolge</span><span class="sxs-lookup"><span data-stu-id="cab35-364">Attribute route order</span></span>

<span data-ttu-id="cab35-365">Routing erstellt eine Struktur und gleicht alle Endpunkte gleichzeitig ab:</span><span class="sxs-lookup"><span data-stu-id="cab35-365">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="cab35-366">Die Routen Einträge Verhalten sich so, als ob Sie in einer idealen Reihenfolge platziert werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-366">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="cab35-367">Die spezifischsten Routen können vor den allgemeineren Routen ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-367">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="cab35-368">Beispielsweise ist eine Attribut Route wie `blog/search/{topic}` spezifischer als eine Attribut Route wie `blog/{*article}` .</span><span class="sxs-lookup"><span data-stu-id="cab35-368">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="cab35-369">Die `blog/search/{topic}` Route hat standardmäßig eine höhere Priorität, da Sie spezifischer ist.</span><span class="sxs-lookup"><span data-stu-id="cab35-369">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="cab35-370">Mit [herkömmlichem Routing](#cr)ist der Entwickler für das Platzieren von Routen in der gewünschten Reihenfolge verantwortlich.</span><span class="sxs-lookup"><span data-stu-id="cab35-370">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="cab35-371">Attribut Routen können mithilfe der-Eigenschaft eine Bestellung konfigurieren <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> .</span><span class="sxs-lookup"><span data-stu-id="cab35-371">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="cab35-372">Alle vom Framework bereitgestellten [Routen Attribute](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) umfassen `Order` .</span><span class="sxs-lookup"><span data-stu-id="cab35-372">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="cab35-373">Routen werden entsprechend einer aufsteigenden Reihenfolge der `Order`-Eigenschaft verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="cab35-373">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="cab35-374">Die Standardreihenfolge ist `0`.</span><span class="sxs-lookup"><span data-stu-id="cab35-374">The default order is `0`.</span></span> <span data-ttu-id="cab35-375">Das Festlegen einer Route mithilfe von wird `Order = -1` vor Routen ausgeführt, die keine Reihenfolge festlegen.</span><span class="sxs-lookup"><span data-stu-id="cab35-375">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="cab35-376">Das Festlegen einer Route mithilfe von wird `Order = 1` nach der Standardrouten Reihenfolge ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="cab35-376">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="cab35-377">**Vermeiden** Sie die Abhängigkeit von `Order` .</span><span class="sxs-lookup"><span data-stu-id="cab35-377">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="cab35-378">Wenn für den URL-Speicher einer APP explizite Auftrags Werte erforderlich sind, um ordnungsgemäß weiterzuleiten, ist es wahrscheinlich auch für Clients verwirrend.</span><span class="sxs-lookup"><span data-stu-id="cab35-378">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="cab35-379">Im Allgemeinen wählt das Attribut Routing die richtige Route mit URL-Übereinstimmung aus.</span><span class="sxs-lookup"><span data-stu-id="cab35-379">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="cab35-380">Wenn die für die URL-Generierung verwendete Standard Reihenfolge nicht funktioniert, ist die Verwendung eines Routen namens als außer Kraft Setzung in der Regel einfacher als das Anwenden der `Order` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="cab35-380">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="cab35-381">Beachten Sie die beiden folgenden Controller, die beide den Routen Abgleich definieren `/home` :</span><span class="sxs-lookup"><span data-stu-id="cab35-381">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="cab35-382">Wenn `/home` Sie mit dem vorangehenden Code anfordern, wird eine Ausnahme ausgelöst, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="cab35-382">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="cab35-383">Durch das Hinzufügen `Order` eines der Routen Attribute wird die Mehrdeutigkeit aufgelöst:</span><span class="sxs-lookup"><span data-stu-id="cab35-383">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="cab35-384">Mit dem vorangehenden Code `/home` führt den `HomeController.Index` Endpunkt aus.</span><span class="sxs-lookup"><span data-stu-id="cab35-384">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="cab35-385">Um die `MyDemoController.MyIndex` anzufordern, fordern Sie an `/home/MyIndex` .</span><span class="sxs-lookup"><span data-stu-id="cab35-385">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="cab35-386">**Hinweis**:</span><span class="sxs-lookup"><span data-stu-id="cab35-386">**Note**:</span></span>

* <span data-ttu-id="cab35-387">Der vorangehende Code ist ein Beispiel oder ein schlechtes Routing Design.</span><span class="sxs-lookup"><span data-stu-id="cab35-387">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="cab35-388">Es wurde verwendet, um die-Eigenschaft zu veranschaulichen `Order` .</span><span class="sxs-lookup"><span data-stu-id="cab35-388">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="cab35-389">Die- `Order` Eigenschaft löst nur die Mehrdeutigkeit auf, diese Vorlage kann nicht abgeglichen werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-389">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="cab35-390">Es wäre besser, die Vorlage zu entfernen `[Route("Home")]` .</span><span class="sxs-lookup"><span data-stu-id="cab35-390">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="cab35-391">Siehe [ Razor Seiten Routen und App-Konventionen: Routen Reihenfolge](xref:razor-pages/razor-pages-conventions#route-order) für Informationen zur Routen Reihenfolge mit Razor Seiten.</span><span class="sxs-lookup"><span data-stu-id="cab35-391">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="cab35-392">In einigen Fällen wird ein HTTP 500-Fehler mit mehrdeutigen Routen zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="cab35-392">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="cab35-393">Verwenden Sie die [Protokollierung](xref:fundamentals/logging/index) , um anzuzeigen, welche Endpunkte `AmbiguousMatchException` den verursacht haben</span><span class="sxs-lookup"><span data-stu-id="cab35-393">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="cab35-394">Ersetzung von Token in Routen Vorlagen [Controller], [Aktion], [Bereich]</span><span class="sxs-lookup"><span data-stu-id="cab35-394">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="cab35-395">Zur einfacheren Unterstützung unterstützen Attribut Routen die Tokenersetzung für reservierte Routen Parameter durch das Einschließen eines Tokens in einer der folgenden Werte:</span><span class="sxs-lookup"><span data-stu-id="cab35-395">For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:</span></span>

* <span data-ttu-id="cab35-396">Eckige Klammern:`[]`</span><span class="sxs-lookup"><span data-stu-id="cab35-396">Square brackets: `[]`</span></span>
* <span data-ttu-id="cab35-397">Geschweifte Klammern:`{}`</span><span class="sxs-lookup"><span data-stu-id="cab35-397">Curly braces: `{}`</span></span>

<span data-ttu-id="cab35-398">Die Token `[action]` , `[area]` und `[controller]` werden durch die Werte für Aktionsname, Bereichs Name und Controller Name aus der Aktion ersetzt, in der die Route definiert ist:</span><span class="sxs-lookup"><span data-stu-id="cab35-398">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="cab35-399">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="cab35-399">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="cab35-400">Match`/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="cab35-400">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="cab35-401">Match`/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="cab35-401">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="cab35-402">Die Tokenersetzung tritt im letzten Schritt der Erstellung von Attributrouten auf.</span><span class="sxs-lookup"><span data-stu-id="cab35-402">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="cab35-403">Das vorherige Beispiel verhält sich wie der folgende Code:</span><span class="sxs-lookup"><span data-stu-id="cab35-403">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="cab35-404">Attributrouten können auch mit Vererbung kombiniert werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-404">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="cab35-405">Dies ist in Kombination mit der Tokenersetzung stark.</span><span class="sxs-lookup"><span data-stu-id="cab35-405">This is powerful combined with token replacement.</span></span> <span data-ttu-id="cab35-406">Tokenersetzung gilt auch für Routennamen, die durch Attributrouten definiert werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-406">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="cab35-407">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generiert für jede Aktion einen eindeutigen Routennamen:</span><span class="sxs-lookup"><span data-stu-id="cab35-407">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="cab35-408">Tokenersetzung gilt auch für Routennamen, die durch Attributrouten definiert werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-408">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="cab35-409"> generiert für jede Aktion einen eindeutigen Routennamen.</span><span class="sxs-lookup"><span data-stu-id="cab35-409">generates a unique route name for each action.</span></span>

<span data-ttu-id="cab35-410">Damit das Trennzeichen `[` oder `]` der Tokenersetzungs-Literalzeichenfolge bei einem Abgleich gefunden wird, muss es doppelt vorhanden sein (`[[` oder `]]`), was einem Escapezeichen entspricht.</span><span class="sxs-lookup"><span data-stu-id="cab35-410">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="cab35-411">Verwenden eines Parametertransformators zum Anpassen der Tokenersetzung</span><span class="sxs-lookup"><span data-stu-id="cab35-411">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="cab35-412">Die Tokenersetzung kann mit einem Parametertransformator angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-412">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="cab35-413">Ein Parametertransformator implementiert <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> und wandelt den Wert der Parameter um.</span><span class="sxs-lookup"><span data-stu-id="cab35-413">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="cab35-414">Beispielsweise ändert ein benutzerdefinierter `SlugifyParameterTransformer` parametertransformator den `SubscriptionManagement` Routen Wert in `subscription-management` :</span><span class="sxs-lookup"><span data-stu-id="cab35-414">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="cab35-415">Die <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> ist eine Anwendungsmodellkonvention, die Folgendes ausführt:</span><span class="sxs-lookup"><span data-stu-id="cab35-415">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="cab35-416">Wendet einen angegebenen Parametertransformator auf alle Attributrouten in der App an.</span><span class="sxs-lookup"><span data-stu-id="cab35-416">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="cab35-417">Passt die Tokenwerte für Attributrouten bei ihrer Ersetzung an.</span><span class="sxs-lookup"><span data-stu-id="cab35-417">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="cab35-418">Die vorangehende `ListAll` Methode stimmt überein `/subscription-management/list-all` .</span><span class="sxs-lookup"><span data-stu-id="cab35-418">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="cab35-419">Die `RouteTokenTransformerConvention` wird als Option in `ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="cab35-419">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="cab35-420">Die Definition von Slug finden Sie unter [MDN-Webdokumentation zu Slug](https://developer.mozilla.org/docs/Glossary/Slug) .</span><span class="sxs-lookup"><span data-stu-id="cab35-420">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="cab35-421">Mehrere Attribut Routen</span><span class="sxs-lookup"><span data-stu-id="cab35-421">Multiple attribute routes</span></span>

<span data-ttu-id="cab35-422">Attributrouting unterstützt das Definieren mehrerer Routen, die zu derselben Aktion führen.</span><span class="sxs-lookup"><span data-stu-id="cab35-422">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="cab35-423">Dies wird am häufigsten beim Imitieren des Verhaltens der herkömmlichen Standardroute verwendet. Im folgenden Beispiel wird dies gezeigt:</span><span class="sxs-lookup"><span data-stu-id="cab35-423">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="cab35-424">Wenn Sie mehrere Routen Attribute auf dem Controller platzieren, bedeutet dies, dass jede mit den Routen Attributen der Aktionsmethoden kombiniert wird:</span><span class="sxs-lookup"><span data-stu-id="cab35-424">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="cab35-425">Alle [http-Verb](#verb) Routen Einschränkungen implementieren `IActionConstraint` .</span><span class="sxs-lookup"><span data-stu-id="cab35-425">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="cab35-426">Wenn mehrere Routen Attribute, die implementieren, <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> in eine Aktion eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="cab35-426">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="cab35-427">Jede Aktions Einschränkung kombiniert mit der auf den Controller angewendeten Routen Vorlage.</span><span class="sxs-lookup"><span data-stu-id="cab35-427">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="cab35-428">Die Verwendung mehrerer Routen für Aktionen mag hilfreich und leistungsstark sein. es ist besser, den URL-Bereich ihrer App als "Basic" und "klar" festzuhalten.</span><span class="sxs-lookup"><span data-stu-id="cab35-428">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="cab35-429">Verwenden Sie mehrere Routen für Aktionen **nur** bei Bedarf, z. b. zur Unterstützung vorhandener Clients.</span><span class="sxs-lookup"><span data-stu-id="cab35-429">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="cab35-430">Angeben von optionalen Attributroutenparametern, Standardwerten und Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="cab35-430">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="cab35-431">Attributrouten unterstützen dieselbe Inline-Syntax wie herkömmliche Routen, um optionale Parameter, Standardwerte und Einschränkungen anzugeben.</span><span class="sxs-lookup"><span data-stu-id="cab35-431">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="cab35-432">Im vorangehenden Code wird `[HttpPost("product/{id:int}")]` eine Routen Einschränkung angewendet.</span><span class="sxs-lookup"><span data-stu-id="cab35-432">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="cab35-433">Die `ProductsController.ShowProduct` Aktion wird nur durch URL-Pfade wie abgeglichen `/product/3` .</span><span class="sxs-lookup"><span data-stu-id="cab35-433">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="cab35-434">Der Weiterleitungs Vorlagen Teil `{id:int}` schränkt dieses Segment auf ganze Zahlen ein.</span><span class="sxs-lookup"><span data-stu-id="cab35-434">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

<span data-ttu-id="cab35-435">Eine ausführliche Beschreibung der Syntax der Routenvorlage finden Sie unter [Routenvorlagenreferenz](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="cab35-435">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="cab35-436">Benutzerdefinierte Routen Attribute mit iroutetemplateprovider</span><span class="sxs-lookup"><span data-stu-id="cab35-436">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="cab35-437">Alle [Routen Attribute](#rt) implementieren <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider> .</span><span class="sxs-lookup"><span data-stu-id="cab35-437">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="cab35-438">Die ASP.net Core Laufzeit:</span><span class="sxs-lookup"><span data-stu-id="cab35-438">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="cab35-439">Sucht nach Attributen in Controller Klassen und Aktionsmethoden, wenn die APP gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-439">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="cab35-440">Verwendet die Attribute, die implementieren, `IRouteTemplateProvider` um den anfänglichen Satz von Routen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="cab35-440">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="cab35-441">Implementieren `IRouteTemplateProvider` Sie, um benutzerdefinierte Routen Attribute zu definieren.</span><span class="sxs-lookup"><span data-stu-id="cab35-441">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="cab35-442">Jeder `IRouteTemplateProvider` lässt Sie eine einzelne Route mit einer benutzerdefinierten Routenvorlage, Reihenfolge und einem benutzerdefinierten Namen definieren:</span><span class="sxs-lookup"><span data-stu-id="cab35-442">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="cab35-443">Die vorherige `Get` Methode gibt zurück `Order = 2, Template = api/MyTestApi` .</span><span class="sxs-lookup"><span data-stu-id="cab35-443">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="cab35-444">Verwenden des Anwendungs Modells zum Anpassen von Attribut Routen</span><span class="sxs-lookup"><span data-stu-id="cab35-444">Use application model to customize attribute routes</span></span>

<span data-ttu-id="cab35-445">Das Anwendungsmodell:</span><span class="sxs-lookup"><span data-stu-id="cab35-445">The application model:</span></span>

* <span data-ttu-id="cab35-446">Ist ein Objektmodell, das beim Start erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-446">Is an object model created at startup.</span></span>
* <span data-ttu-id="cab35-447">Enthält alle Metadaten, die von ASP.net Core verwendet werden, um die Aktionen in einer APP weiterzuleiten und auszuführen.</span><span class="sxs-lookup"><span data-stu-id="cab35-447">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="cab35-448">Das Anwendungsmodell umfasst alle Daten, die von Routen Attributen gesammelt werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-448">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="cab35-449">Die Daten aus Routen Attributen werden von der- `IRouteTemplateProvider` Implementierung bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="cab35-449">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="cab35-450">Geregelt</span><span class="sxs-lookup"><span data-stu-id="cab35-450">Conventions:</span></span>

* <span data-ttu-id="cab35-451">Kann geschrieben werden, um das Anwendungsmodell so zu ändern, dass das Routing Verhalten angepasst wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-451">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="cab35-452">Werden beim APP-Start gelesen.</span><span class="sxs-lookup"><span data-stu-id="cab35-452">Are read at app startup.</span></span>

<span data-ttu-id="cab35-453">Dieser Abschnitt zeigt ein einfaches Beispiel für das Anpassen des Routings mithilfe des Anwendungs Modells.</span><span class="sxs-lookup"><span data-stu-id="cab35-453">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="cab35-454">Der folgende Code bewirkt, dass Routen ungefähr mit der Ordnerstruktur des Projekts übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="cab35-454">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="cab35-455">Der folgende Code verhindert, `namespace` dass die Konvention auf Controller angewendet wird, die Attribut weitergeleitet werden:</span><span class="sxs-lookup"><span data-stu-id="cab35-455">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="cab35-456">Der folgende Controller verwendet beispielsweise nicht `NamespaceRoutingConvention` :</span><span class="sxs-lookup"><span data-stu-id="cab35-456">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="cab35-457">Die `NamespaceRoutingConvention.Apply`-Methode:</span><span class="sxs-lookup"><span data-stu-id="cab35-457">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="cab35-458">Führt keine Aktion aus, wenn der Controller Attribut weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-458">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="cab35-459">Legt die Controller Vorlage auf Grundlage von fest `namespace` , wobei die Basis `namespace` entfernt wurde.</span><span class="sxs-lookup"><span data-stu-id="cab35-459">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="cab35-460">`NamespaceRoutingConvention`Kann in angewendet werden `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="cab35-460">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="cab35-461">Sehen Sie sich beispielsweise den folgenden Controller an:</span><span class="sxs-lookup"><span data-stu-id="cab35-461">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="cab35-462">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="cab35-462">In the preceding code:</span></span>

* <span data-ttu-id="cab35-463">Die Basis `namespace` ist `My.Application` .</span><span class="sxs-lookup"><span data-stu-id="cab35-463">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="cab35-464">Der vollständige Name des vorangehenden Controllers ist `My.Application.Admin.Controllers.UsersController` .</span><span class="sxs-lookup"><span data-stu-id="cab35-464">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="cab35-465">Der `NamespaceRoutingConvention` legt die Controller Vorlage auf fest `Admin/Controllers/Users/[action]/{id?` .</span><span class="sxs-lookup"><span data-stu-id="cab35-465">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="cab35-466">`NamespaceRoutingConvention`Kann auch als Attribut auf einem Controller angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="cab35-466">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="cab35-467">Gemischtes Routing: Attributrouting vs. herkömmliches Routing</span><span class="sxs-lookup"><span data-stu-id="cab35-467">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="cab35-468">ASP.net Core-Apps können die Verwendung von herkömmlichem Routing und Attribut Routing vermischen.</span><span class="sxs-lookup"><span data-stu-id="cab35-468">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="cab35-469">In der Regel werden herkömmliche Routen für Controller verwendet, die für Browser-HTML-Seiten gedacht sind, und das Attributrouting für Controller für REST-APIs.</span><span class="sxs-lookup"><span data-stu-id="cab35-469">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="cab35-470">Aktionen werden entweder herkömmlich oder über Attribute zugeordnet,</span><span class="sxs-lookup"><span data-stu-id="cab35-470">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="cab35-471">d.h., dass eine Route auf dem Controller oder der Aktion platziert wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-471">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="cab35-472">Aktionen, die Attributrouten definieren, können nicht über die herkömmliche Routen und umgekehrt erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-472">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="cab35-473">Alle **Routen Attribute** auf dem Controller machen **alle** Aktionen im Controller Attribut weiter.</span><span class="sxs-lookup"><span data-stu-id="cab35-473">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="cab35-474">Bei Attribut Routing und herkömmlichem Routing wird dieselbe Routing-Engine verwendet.</span><span class="sxs-lookup"><span data-stu-id="cab35-474">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="cab35-475">URL-Generierung und Ambient-Werte</span><span class="sxs-lookup"><span data-stu-id="cab35-475">URL Generation and ambient values</span></span>

<span data-ttu-id="cab35-476">Apps können Routing-URL-Generierungs Features verwenden, um URL-Links zu Aktionen zu generieren.</span><span class="sxs-lookup"><span data-stu-id="cab35-476">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="cab35-477">Durch das Erstellen von URLs werden hart codierte URLs eliminiert, sodass der Code stabiler und verwallegbar wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-477">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="cab35-478">Dieser Abschnitt konzentriert sich auf die Funktionen der URL-Generierung, die von MVC bereitgestellt werden</span><span class="sxs-lookup"><span data-stu-id="cab35-478">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="cab35-479">Eine detaillierte Beschreibung der URL-Generierung finden Sie unter [Routing in ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="cab35-479">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="cab35-480">Die <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> Schnittstelle ist das zugrunde liegende Element der Infrastruktur zwischen MVC und dem Routing für die URL-Generierung.</span><span class="sxs-lookup"><span data-stu-id="cab35-480">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="cab35-481">Eine Instanz von `IUrlHelper` ist über die- `Url` Eigenschaft in Controllern, Ansichten und Ansichts Komponenten verfügbar.</span><span class="sxs-lookup"><span data-stu-id="cab35-481">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="cab35-482">Im folgenden Beispiel wird die- `IUrlHelper` Schnittstelle mithilfe der- `Controller.Url` Eigenschaft verwendet, um eine URL zu einer anderen Aktion zu generieren.</span><span class="sxs-lookup"><span data-stu-id="cab35-482">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="cab35-483">Wenn die APP die herkömmliche Standardroute verwendet, ist der Wert der `url` Variablen die URL-Pfad Zeichenfolge `/UrlGeneration/Destination` .</span><span class="sxs-lookup"><span data-stu-id="cab35-483">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="cab35-484">Dieser URL-Pfad wird durch das Routing erstellt, indem Folgendes kombiniert wird:</span><span class="sxs-lookup"><span data-stu-id="cab35-484">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="cab35-485">Die Routen Werte aus der aktuellen Anforderung, die als **Umgebungs Werte**bezeichnet werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-485">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="cab35-486">Die Werte, die an weitergegeben werden, `Url.Action` und ersetzen diese Werte in der Routen Vorlage:</span><span class="sxs-lookup"><span data-stu-id="cab35-486">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="cab35-487">Der Wert eines jeden Routenparameters wird in der Routenvorlage durch die entsprechenden Namen mit den Werten und Umgebungswerten ersetzt.</span><span class="sxs-lookup"><span data-stu-id="cab35-487">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="cab35-488">Ein Routen Parameter, der keinen Wert hat, kann folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="cab35-488">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="cab35-489">Verwenden Sie ggf. einen Standardwert.</span><span class="sxs-lookup"><span data-stu-id="cab35-489">Use a default value if it has one.</span></span>
* <span data-ttu-id="cab35-490">Sie sollten übersprungen werden, wenn Sie optional ist.</span><span class="sxs-lookup"><span data-stu-id="cab35-490">Be skipped if it's optional.</span></span> <span data-ttu-id="cab35-491">Beispielsweise die `id` aus der Routen Vorlage `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="cab35-491">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="cab35-492">Die URL-Generierung schlägt fehl, wenn für einen erforderlichen Routen Parameter kein entsprechender Wert vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="cab35-492">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="cab35-493">Wenn die URL-Generierung für eine Route fehlschlägt, wird die nächste Route ausprobiert, bis alle Routen getestet wurden oder eine Übereinstimmung gefunden wurde.</span><span class="sxs-lookup"><span data-stu-id="cab35-493">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="cab35-494">Im vorherigen Beispiel von wird von `Url.Action` [herkömmlichem Routing](#cr)ausgegangen.</span><span class="sxs-lookup"><span data-stu-id="cab35-494">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="cab35-495">Die URL-Generierung funktioniert ähnlich wie das [Attribut Routing](#ar), obwohl sich die Konzepte unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="cab35-495">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="cab35-496">Mit herkömmlichem Routing:</span><span class="sxs-lookup"><span data-stu-id="cab35-496">With conventional routing:</span></span>

* <span data-ttu-id="cab35-497">Die Routen Werte werden zum Erweitern einer Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="cab35-497">The route values are used to expand a template.</span></span>
* <span data-ttu-id="cab35-498">Die Routen Werte für `controller` und werden `action` in der Regel in dieser Vorlage angezeigt.</span><span class="sxs-lookup"><span data-stu-id="cab35-498">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="cab35-499">Dies funktioniert, da die mit dem Routing übereinstimmenden URLs einer Konvention entsprechen.</span><span class="sxs-lookup"><span data-stu-id="cab35-499">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="cab35-500">Im folgenden Beispiel wird das Attribut Routing verwendet:</span><span class="sxs-lookup"><span data-stu-id="cab35-500">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="cab35-501">Durch die `Source` Aktion im vorangehenden Code wird generiert `custom/url/to/destination` .</span><span class="sxs-lookup"><span data-stu-id="cab35-501">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="cab35-502"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>wurde in ASP.net Core 3,0 als Alternative zu hinzugefügt `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="cab35-502"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="cab35-503">`LinkGenerator`bietet ähnliche, aber flexiblere Funktionen.</span><span class="sxs-lookup"><span data-stu-id="cab35-503">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="cab35-504">Jede Methode `IUrlHelper` von verfügt auch über eine entsprechende Familie von-Methoden `LinkGenerator` .</span><span class="sxs-lookup"><span data-stu-id="cab35-504">Each method on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="cab35-505">Generieren von URLs nach Aktionsnamen</span><span class="sxs-lookup"><span data-stu-id="cab35-505">Generating URLs by action name</span></span>

<span data-ttu-id="cab35-506">" [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)", " [Linkgenerator. getpathbyaction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)" und alle zugehörigen über Ladungen sind so konzipiert, dass der Ziel Endpunkt durch Angabe eines Controller namens und Aktions namens generiert wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-506">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="cab35-507">Bei Verwendung `Url.Action` von werden die aktuellen Routen Werte für `controller` und `action` von der Laufzeit bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="cab35-507">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="cab35-508">Der Wert von `controller` und `action` ist Teil der [Ambient-Werte](#ambient) und-Werte.</span><span class="sxs-lookup"><span data-stu-id="cab35-508">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="cab35-509">Die `Url.Action` -Methode verwendet immer die aktuellen Werte von `action` und `controller` und generiert einen URL-Pfad, der zur aktuellen Aktion weiterleitet.</span><span class="sxs-lookup"><span data-stu-id="cab35-509">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="cab35-510">Beim Routing werden die Werte in Ambient-Werten verwendet, um Informationen einzugeben, die beim Erstellen einer URL nicht bereitgestellt wurden.</span><span class="sxs-lookup"><span data-stu-id="cab35-510">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="cab35-511">Stellen Sie sich eine Route wie `{a}/{b}/{c}/{d}` mit Ambient-Werten vor `{ a = Alice, b = Bob, c = Carol, d = David }` :</span><span class="sxs-lookup"><span data-stu-id="cab35-511">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="cab35-512">Das Routing verfügt über genügend Informationen, um eine URL ohne zusätzliche Werte zu generieren.</span><span class="sxs-lookup"><span data-stu-id="cab35-512">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="cab35-513">Das Routing verfügt über genügend Informationen, da alle Routen Parameter über einen Wert verfügen.</span><span class="sxs-lookup"><span data-stu-id="cab35-513">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="cab35-514">Wenn der Wert `{ d = Donovan }` hinzugefügt wird:</span><span class="sxs-lookup"><span data-stu-id="cab35-514">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="cab35-515">Der Wert `{ d = David }` wird ignoriert.</span><span class="sxs-lookup"><span data-stu-id="cab35-515">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="cab35-516">Der generierte URL-Pfad ist `Alice/Bob/Carol/Donovan` .</span><span class="sxs-lookup"><span data-stu-id="cab35-516">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="cab35-517">**Warnung**: URL-Pfade sind hierarchisch.</span><span class="sxs-lookup"><span data-stu-id="cab35-517">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="cab35-518">Im vorherigen Beispiel, wenn der Wert `{ c = Cheryl }` hinzugefügt wird:</span><span class="sxs-lookup"><span data-stu-id="cab35-518">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="cab35-519">Beide Werte `{ c = Carol, d = David }` werden ignoriert.</span><span class="sxs-lookup"><span data-stu-id="cab35-519">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="cab35-520">Für ist kein Wert mehr vorhanden, `d` und die URL-Generierung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="cab35-520">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="cab35-521">Die gewünschten Werte von `c` und `d` müssen angegeben werden, um eine URL zu generieren.</span><span class="sxs-lookup"><span data-stu-id="cab35-521">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="cab35-522">Möglicherweise erwarten Sie dieses Problem mit der Standardroute `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="cab35-522">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="cab35-523">Dieses Problem tritt in der Praxis selten auf, da `Url.Action` explizit einen `controller` -Wert und einen- `action` Wert angibt.</span><span class="sxs-lookup"><span data-stu-id="cab35-523">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="cab35-524">Mehrere über Ladungen von [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) nehmen ein Routen Werte Objekt an, um Werte für andere Routen Parameter als und bereitzustellen `controller` `action` .</span><span class="sxs-lookup"><span data-stu-id="cab35-524">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="cab35-525">Das Routen Werte Objekt wird häufig mit verwendet `id` .</span><span class="sxs-lookup"><span data-stu-id="cab35-525">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="cab35-526">Beispiel: `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="cab35-526">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="cab35-527">Das Routen Werte Objekt:</span><span class="sxs-lookup"><span data-stu-id="cab35-527">The route values object:</span></span>

* <span data-ttu-id="cab35-528">Gemäß der Konvention ist in der Regel ein Objekt des anonymen Typs.</span><span class="sxs-lookup"><span data-stu-id="cab35-528">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="cab35-529">Kann ein `IDictionary<>` oder ein [poco](https://wikipedia.org/wiki/Plain_old_CLR_object)sein).</span><span class="sxs-lookup"><span data-stu-id="cab35-529">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="cab35-530">Alle zusätzlichen Routenwerte, die keinen Routenparametern zugeordnet sind, werden in der Abfragezeichenfolge platziert.</span><span class="sxs-lookup"><span data-stu-id="cab35-530">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="cab35-531">Der vorangehende Code generiert `/Products/Buy/17?color=red` .</span><span class="sxs-lookup"><span data-stu-id="cab35-531">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="cab35-532">Mit dem folgenden Code wird eine absolute URL generiert:</span><span class="sxs-lookup"><span data-stu-id="cab35-532">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="cab35-533">Um einen absolute URL zu erstellen, verwenden Sie eine der folgenden Aktionen:</span><span class="sxs-lookup"><span data-stu-id="cab35-533">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="cab35-534">Eine Überladung, die eine akzeptiert `protocol` .</span><span class="sxs-lookup"><span data-stu-id="cab35-534">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="cab35-535">Beispielsweise der vorangehende Code.</span><span class="sxs-lookup"><span data-stu-id="cab35-535">For example, the preceding code.</span></span>
* <span data-ttu-id="cab35-536">[Linkgenerator. geturibyaction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), wodurch standardmäßig absolute URIs generiert werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-536">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="cab35-537">Generieren von URLs nach Route</span><span class="sxs-lookup"><span data-stu-id="cab35-537">Generate URLs by route</span></span>

<span data-ttu-id="cab35-538">Der vorangehende Code hat das Erzeugen einer URL durch Übergeben des Controllers und des Aktions namens veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="cab35-538">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="cab35-539">`IUrlHelper`stellt auch die [URL. RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) -Familie der Methoden bereit.</span><span class="sxs-lookup"><span data-stu-id="cab35-539">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="cab35-540">Diese Methoden ähneln [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), Sie kopieren jedoch nicht die aktuellen Werte von `action` und `controller` in die Routen Werte.</span><span class="sxs-lookup"><span data-stu-id="cab35-540">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="cab35-541">Die häufigste Verwendung von `Url.RouteUrl` :</span><span class="sxs-lookup"><span data-stu-id="cab35-541">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="cab35-542">Gibt einen Routennamen zum Generieren der URL an.</span><span class="sxs-lookup"><span data-stu-id="cab35-542">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="cab35-543">In der Regel wird kein Controller-oder Aktionsname angegeben.</span><span class="sxs-lookup"><span data-stu-id="cab35-543">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="cab35-544">Die folgende Razor Datei generiert einen HTML-Link zum `Destination_Route` :</span><span class="sxs-lookup"><span data-stu-id="cab35-544">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a><span data-ttu-id="cab35-545">Generieren von URLs in HTML undRazor</span><span class="sxs-lookup"><span data-stu-id="cab35-545">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="cab35-546"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper>stellt die <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> Methoden [HTML. BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) und [HTML. Action Link](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) zum Generieren von `<form>` - `<a>` bzw.-Elementen bereit.</span><span class="sxs-lookup"><span data-stu-id="cab35-546"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="cab35-547">Diese Methoden verwenden die [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) -Methode, um eine URL zu generieren, und Sie akzeptieren ähnliche Argumente.</span><span class="sxs-lookup"><span data-stu-id="cab35-547">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="cab35-548">Die `Url.RouteUrl`-Begleiter für `HtmlHelper` sind `Html.BeginRouteForm` und `Html.RouteLink`, die ähnliche Funktionen aufweisen.</span><span class="sxs-lookup"><span data-stu-id="cab35-548">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="cab35-549">Taghilfsprogramme generieren URLs mit den Taghilfsprogrammen `form` und `<a>`.</span><span class="sxs-lookup"><span data-stu-id="cab35-549">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="cab35-550">Beide implementieren mit `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="cab35-550">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="cab35-551">Weitere Informationen finden Sie [unter taghilfsprogramme in Formularen](xref:mvc/views/working-with-forms) .</span><span class="sxs-lookup"><span data-stu-id="cab35-551">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="cab35-552">In Ansichten ist `IUrlHelper` über die `Url`-Eigenschaft für jede Ad-hoc-URL-Generierung verfügbar, die keine der oben genannten ist.</span><span class="sxs-lookup"><span data-stu-id="cab35-552">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="cab35-553">URL-Generierung in Aktions Ergebnissen</span><span class="sxs-lookup"><span data-stu-id="cab35-553">URL generation in Action Results</span></span>

<span data-ttu-id="cab35-554">In den vorangehenden Beispielen wurde die Verwendung von `IUrlHelper` in einem Controller gezeigt.</span><span class="sxs-lookup"><span data-stu-id="cab35-554">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="cab35-555">Die häufigste Verwendung eines Controllers ist das Generieren einer URL als Teil eines Aktions Ergebnisses.</span><span class="sxs-lookup"><span data-stu-id="cab35-555">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="cab35-556">Die Basisklassen <xref:Microsoft.AspNetCore.Mvc.ControllerBase> und <xref:Microsoft.AspNetCore.Mvc.Controller> stellen Hilfsmethoden für Aktionsergebnisse bereit, die auf eine andere Aktionen verweisen.</span><span class="sxs-lookup"><span data-stu-id="cab35-556">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="cab35-557">Eine typische Verwendung besteht darin, nach Annahme der Benutzereingabe eine Umleitung durchzusetzen:</span><span class="sxs-lookup"><span data-stu-id="cab35-557">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="cab35-558">Die Aktions Ergebnisse Factorymethoden wie <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> und <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> folgen einem ähnlichen Muster wie die Methoden in `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="cab35-558">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="cab35-559">Sonderfall für dedizierte herkömmliche Routen</span><span class="sxs-lookup"><span data-stu-id="cab35-559">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="cab35-560">[Herkömmliches Routing](#cr) kann eine besondere Art von Routen Definition verwenden, die als [dedizierte konventionelle Route](#dcr)bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-560">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="cab35-561">Im folgenden Beispiel ist die Route mit dem Namen `blog` eine dedizierte konventionelle Route:</span><span class="sxs-lookup"><span data-stu-id="cab35-561">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="cab35-562">Wenn Sie die vorangehenden Routen Definitionen verwenden, `Url.Action("Index", "Home")` generiert den URL-Pfad `/` mithilfe der `default` Route, aber warum?</span><span class="sxs-lookup"><span data-stu-id="cab35-562">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="cab35-563">Man könnte meinen, dass die Routenwerte `{ controller = Home, action = Index }` ausreichen würden, um eine URL mithilfe von `blog` zu generieren, und das Ergebnis wäre `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="cab35-563">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="cab35-564">[Dedizierte herkömmliche Routen](#dcr) basieren auf einem besonderen Verhalten von Standardwerten, die über keinen entsprechenden Routen Parameter verfügen, der verhindert, dass die Route mit der URL-Generierung zu [gierig](xref:fundamentals/routing#greedy) wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-564">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="cab35-565">In diesem Fall sind die Standardwerte `{ controller = Blog, action = Article }`, und weder `controller` noch `action` werden als Routenparameter verwendet.</span><span class="sxs-lookup"><span data-stu-id="cab35-565">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="cab35-566">Wenn das Routing die URL-Generierung ausführt, müssen die angegebenen Werte mit den Standardwerten übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="cab35-566">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="cab35-567">Die URL-Generierung mithilfe von `blog` schlägt fehl, da die Werte `{ controller = Home, action = Index }` nicht stimmen `{ controller = Blog, action = Article }` .</span><span class="sxs-lookup"><span data-stu-id="cab35-567">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="cab35-568">Routing greift dann wieder auf `default` zurück, was erfolgreich ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-568">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="cab35-569">Bereiche</span><span class="sxs-lookup"><span data-stu-id="cab35-569">Areas</span></span>

<span data-ttu-id="cab35-570">[Bereiche](xref:mvc/controllers/areas) sind ein MVC-Feature, das verwendet wird, um verwandte Funktionen in einer Gruppe als separate zu organisieren:</span><span class="sxs-lookup"><span data-stu-id="cab35-570">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="cab35-571">Routing Namespace für Controller Aktionen.</span><span class="sxs-lookup"><span data-stu-id="cab35-571">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="cab35-572">Ordnerstruktur für Ansichten.</span><span class="sxs-lookup"><span data-stu-id="cab35-572">Folder structure for views.</span></span>

<span data-ttu-id="cab35-573">Die Verwendung von Bereichen ermöglicht es einer APP, mehrere Controller mit dem gleichen Namen zu verwenden, solange Sie über unterschiedliche Bereiche verfügen.</span><span class="sxs-lookup"><span data-stu-id="cab35-573">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="cab35-574">Mithilfe von Bereichen wird außerdem eine Hierarchie erstellt, damit das Routing durch Hinzufügen eines anderen Routenparameters ausgeführt werden kann: `area` zu `controller` und `action`.</span><span class="sxs-lookup"><span data-stu-id="cab35-574">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="cab35-575">In diesem Abschnitt wird erläutert, wie das Routing mit Bereichen interagiert.</span><span class="sxs-lookup"><span data-stu-id="cab35-575">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="cab35-576">Ausführliche Informationen zur Verwendung von Bereichen mit Ansichten finden Sie unter [Bereiche](xref:mvc/controllers/areas) .</span><span class="sxs-lookup"><span data-stu-id="cab35-576">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="cab35-577">Im folgenden Beispiel wird MVC so konfiguriert, dass die herkömmliche Standardroute und eine `area` Route für einen mit dem Namen verwendet werden `area` `Blog` :</span><span class="sxs-lookup"><span data-stu-id="cab35-577">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="cab35-578">Im vorangehenden Code <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> wird aufgerufen, um zu erstellen `"blog_route"` .</span><span class="sxs-lookup"><span data-stu-id="cab35-578">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="cab35-579">Der zweite Parameter, `"Blog"` , ist der Bereichs Name.</span><span class="sxs-lookup"><span data-stu-id="cab35-579">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="cab35-580">Beim Abgleichen eines URL-Pfads wie `/Manage/Users/AddUser` `"blog_route"` generiert die Route die Routen Werte `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="cab35-580">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="cab35-581">Der `area` Routen Wert wird durch einen Standardwert für erstellt `area` .</span><span class="sxs-lookup"><span data-stu-id="cab35-581">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="cab35-582">Die von erstellte Route `MapAreaControllerRoute` entspricht Folgendem:</span><span class="sxs-lookup"><span data-stu-id="cab35-582">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="cab35-583">`MapAreaControllerRoute` erstellt mit einem Standardwert und einer Einschränkung für `area` sowie mit dem bereitgestellten Bereichsnamen (in diesem Fall `Blog`) eine Route.</span><span class="sxs-lookup"><span data-stu-id="cab35-583">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="cab35-584">Der Standardwert stellt sicher, dass die Route immer `{ area = Blog, ... }` erzeugt, die Einschränkung erfordert den Wert `{ area = Blog, ... }` für URL-Generierung.</span><span class="sxs-lookup"><span data-stu-id="cab35-584">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="cab35-585">Beim herkömmlichen Routing ist die Reihenfolge wichtig.</span><span class="sxs-lookup"><span data-stu-id="cab35-585">Conventional routing is order-dependent.</span></span> <span data-ttu-id="cab35-586">Im Allgemeinen sollten Routen mit Bereichen früher platziert werden, da Sie spezifischer als Routen ohne Bereich sind.</span><span class="sxs-lookup"><span data-stu-id="cab35-586">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="cab35-587">Im vorherigen Beispiel entsprechen die Routen Werte `{ area = Blog, controller = Users, action = AddUser }` der folgenden Aktion:</span><span class="sxs-lookup"><span data-stu-id="cab35-587">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="cab35-588">Das [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) -Attribut bezeichnet einen Controller als Teil eines Bereichs.</span><span class="sxs-lookup"><span data-stu-id="cab35-588">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="cab35-589">Dieser Controller befindet sich im `Blog` Bereich.</span><span class="sxs-lookup"><span data-stu-id="cab35-589">This controller is in the `Blog` area.</span></span> <span data-ttu-id="cab35-590">Controller ohne ein `[Area]` -Attribut sind keine Elemente eines Bereichs und Stimmen **nicht** mit einem Wert identisch, wenn der `area` Routen Wert durch Routing bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-590">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="cab35-591">Im folgenden Beispiel kann nur der erste aufgelistete Controller die Routenwerte `{ area = Blog, controller = Users, action = AddUser }` abgleichen.</span><span class="sxs-lookup"><span data-stu-id="cab35-591">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="cab35-592">Der Namespace der einzelnen Controller wird hier aus Gründen der Vollständigkeit angezeigt.</span><span class="sxs-lookup"><span data-stu-id="cab35-592">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="cab35-593">Wenn die vorangehenden Controller denselben Namespace verwenden, wird ein Compilerfehler generiert.</span><span class="sxs-lookup"><span data-stu-id="cab35-593">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="cab35-594">Klassennamespaces haben keine Auswirkungen auf das MVC-Routing.</span><span class="sxs-lookup"><span data-stu-id="cab35-594">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="cab35-595">Die ersten beiden Controller gehören zu Bereichen und werden können nur abgleichen, wenn ihr jeweiliger Bereichsname vom `area`-Routenwert bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-595">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="cab35-596">Der dritte Controller gehört keinem Bereich an und kann nur abgleichen, wenn vom Routing kein Wert für `area` bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-596">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="cab35-597">Im Hinblick auf das Erkennen *keines Werts* hat die Abwesenheit des `area`-Werts dieselben Auswirkungen, wie wenn der Wert für `area` 0 (null) oder eine leere Zeichenfolge wäre.</span><span class="sxs-lookup"><span data-stu-id="cab35-597">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="cab35-598">Wenn eine Aktion in einem Bereich ausgeführt wird, ist der Routen Wert für `area` als [Ambient-Wert](#ambient) für das Routing zur URL-Generierung verfügbar.</span><span class="sxs-lookup"><span data-stu-id="cab35-598">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="cab35-599">Das bedeutet, dass Bereiche bei der URL-Generierung wie im folgenden Beispiel dargestellt standardmäßig *beständig* sind.</span><span class="sxs-lookup"><span data-stu-id="cab35-599">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="cab35-600">Der folgende Code generiert eine URL zu `/Zebra/Users/AddUser` :</span><span class="sxs-lookup"><span data-stu-id="cab35-600">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="cab35-601">Aktions Definition</span><span class="sxs-lookup"><span data-stu-id="cab35-601">Action definition</span></span>

<span data-ttu-id="cab35-602">Öffentliche Methoden auf einem Controller, außer denen mit dem [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) -Attribut, sind Aktionen.</span><span class="sxs-lookup"><span data-stu-id="cab35-602">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="cab35-603">Beispielcode</span><span class="sxs-lookup"><span data-stu-id="cab35-603">Sample code</span></span>

 * <span data-ttu-id="cab35-604">Die [mydisplayrouteinfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) -Methode ist im [Beispiel Download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) enthalten und wird zum Anzeigen von Routing Informationen verwendet.</span><span class="sxs-lookup"><span data-stu-id="cab35-604">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>
* <span data-ttu-id="cab35-605">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cab35-605">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cab35-606">ASP.NET Core MVC verwendet [Routing-Middleware](xref:fundamentals/middleware/index), um die URLs der eingehenden Anforderungen abzugleichen und sie Aktionen zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="cab35-606">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="cab35-607">Routen werden im Startcode oder in Attributen definiert</span><span class="sxs-lookup"><span data-stu-id="cab35-607">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="cab35-608">und beschreiben, wie URL-Pfade Aktionen zugeordnet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="cab35-608">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="cab35-609">Sie werden auch dazu verwendet, um URLs (für Links) zu generieren, die in Antworten gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-609">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="cab35-610">Aktionen werden entweder herkömmlich oder über Attribute zugeordnet,</span><span class="sxs-lookup"><span data-stu-id="cab35-610">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="cab35-611">d.h., dass eine Route auf dem Controller oder der Aktion platziert wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-611">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="cab35-612">Weitere Informationen finden Sie im Abschnitt [Gemischtes Routing](#routing-mixed-ref-label).</span><span class="sxs-lookup"><span data-stu-id="cab35-612">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="cab35-613">In diesem Artikel werden die Schnittstellen zwischen MVC und Routing und die Art und Weise erläutert, wie normale MVC-Apps Routingfeatures verwenden.</span><span class="sxs-lookup"><span data-stu-id="cab35-613">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="cab35-614">Informationen zum erweiterten Routing finden Sie unter [Routing in ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="cab35-614">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="cab35-615">Einrichten der Routing-Middleware</span><span class="sxs-lookup"><span data-stu-id="cab35-615">Setting up Routing Middleware</span></span>

<span data-ttu-id="cab35-616">Ihre *Configure*-Methode kann Code wie diesen enthalten:</span><span class="sxs-lookup"><span data-stu-id="cab35-616">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="cab35-617">Innerhalb des Aufrufs von `UseMvc` wird mit `MapRoute` eine einzelne Route erstellt, die wir `default`-Route nennen.</span><span class="sxs-lookup"><span data-stu-id="cab35-617">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="cab35-618">Die meisten MVC-Apps verwenden eine Route mit einer Vorlage, die der `default`-Route ähnelt.</span><span class="sxs-lookup"><span data-stu-id="cab35-618">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="cab35-619">TDie Routenvorlage `"{controller=Home}/{action=Index}/{id?}"` kann einem URL-Pfad wie `/Products/Details/5` abgleichen und extrahiert die Routenwerte `{ controller = Products, action = Details, id = 5 }`, indem ein Token für den Pfad erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-619">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="cab35-620">MVC versucht, einen Controller namens `ProductsController` zu suchen und die Aktion `Details` auszuführen:</span><span class="sxs-lookup"><span data-stu-id="cab35-620">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="cab35-621">In diesem Beispiel hat die Modellbindung den `id`-Parameter mit dem Wert `id = 5` beim Aufrufen dieser Aktion auf `5` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="cab35-621">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="cab35-622">Weitere Informationen finden Sie unter [Modellbindung](../models/model-binding.md).</span><span class="sxs-lookup"><span data-stu-id="cab35-622">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="cab35-623">Verwenden der `default`-Route:</span><span class="sxs-lookup"><span data-stu-id="cab35-623">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="cab35-624">Die Routenvorlage:</span><span class="sxs-lookup"><span data-stu-id="cab35-624">The route template:</span></span>

* <span data-ttu-id="cab35-625">`{controller=Home}` definiert `Home` als Standard-`controller`.</span><span class="sxs-lookup"><span data-stu-id="cab35-625">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="cab35-626">`{action=Index}` definiert `Index` als Standard-`action`.</span><span class="sxs-lookup"><span data-stu-id="cab35-626">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="cab35-627">`{id?}` definiert `id` als optional.</span><span class="sxs-lookup"><span data-stu-id="cab35-627">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="cab35-628">Standardmäßige und optionale Routenparameter müssen nicht im URL-Pfad vorhanden sein, damit es eine Übereinstimmung gibt.</span><span class="sxs-lookup"><span data-stu-id="cab35-628">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="cab35-629">Eine ausführliche Beschreibung der Syntax der Routenvorlage finden Sie unter [Routenvorlagenreferenz](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="cab35-629">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="cab35-630">`"{controller=Home}/{action=Index}/{id?}"` gleicht den URL-Pfad `/` ab und erzeugt die Routenwerte `{ controller = Home, action = Index }`.</span><span class="sxs-lookup"><span data-stu-id="cab35-630">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="cab35-631">Die Werte für `controller` und `action` verwenden die Standardwerte. `id` erzeugt keine Werte, da kein entsprechendes Segment im URL-Pfad vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="cab35-631">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="cab35-632">MVC nutzt diese Routenwerte, um die `HomeController`- und `Index`-Aktion auszuwählen:</span><span class="sxs-lookup"><span data-stu-id="cab35-632">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="cab35-633">Mit dieser Controllerdefinition und dieser Routenvorlage wird die `HomeController.Index`-Aktion für jeden der folgenden URL-Pfade ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="cab35-633">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="cab35-634">Mit der Hilfsmethode `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="cab35-634">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="cab35-635">kann Folgendes ersetzt werden:</span><span class="sxs-lookup"><span data-stu-id="cab35-635">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="cab35-636">`UseMvc` und `UseMvcWithDefaultRoute` fügen der Middleware-Pipeline eine Instanz von `RouterMiddleware` hinzu.</span><span class="sxs-lookup"><span data-stu-id="cab35-636">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="cab35-637">MVC interagiert nicht direkt mit der Middleware und verarbeitet Anforderungen mithilfe von Routing.</span><span class="sxs-lookup"><span data-stu-id="cab35-637">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="cab35-638">MVC ist über eine Instanz von `MvcRouteHandler` mit den Routen verbunden.</span><span class="sxs-lookup"><span data-stu-id="cab35-638">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="cab35-639">Der Code in `UseMvc` ähnelt Folgendem:</span><span class="sxs-lookup"><span data-stu-id="cab35-639">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="cab35-640">`UseMvc` definiert keine Routen direkt, sondern fügt der Routenauflistung einen Platzhalter für die `attribute`-Route hinzu.</span><span class="sxs-lookup"><span data-stu-id="cab35-640">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="cab35-641">Die Überladung `UseMvc(Action<IRouteBuilder>)` ermöglicht es Ihnen, Ihre eigenen Routen hinzuzufügen und unterstützt darüber hinaus das Routingattribut.</span><span class="sxs-lookup"><span data-stu-id="cab35-641">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="cab35-642">`UseMvc` und alle Varianten fügen einen Platzhalter für die Attributroute hinzu. Attributrouting ist immer verfügbar, unabhängig von der Konfiguration von `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="cab35-642">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="cab35-643">`UseMvcWithDefaultRoute` definiert eine Standardroute und unterstützt Attributrouting.</span><span class="sxs-lookup"><span data-stu-id="cab35-643">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="cab35-644">Der Abschnitt [Attributrouting](#attribute-routing-ref-label) enthält weitere Informationen zu dem Thema.</span><span class="sxs-lookup"><span data-stu-id="cab35-644">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="cab35-645">Herkömmliches Routing</span><span class="sxs-lookup"><span data-stu-id="cab35-645">Conventional routing</span></span>

<span data-ttu-id="cab35-646">Die `default`-Route:</span><span class="sxs-lookup"><span data-stu-id="cab35-646">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="cab35-647">Der vorangehende Code ist ein Beispiel für ein herkömmliches Routing.</span><span class="sxs-lookup"><span data-stu-id="cab35-647">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="cab35-648">Dieser Stil wird als herkömmliches Routing bezeichnet, da er eine *Konvention* für URL-Pfade festlegt:</span><span class="sxs-lookup"><span data-stu-id="cab35-648">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="cab35-649">Das erste Pfad Segment wird dem Controller Namen zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="cab35-649">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="cab35-650">Die zweite wird dem Aktions Namen zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="cab35-650">The second maps to the action name.</span></span>
* <span data-ttu-id="cab35-651">Das dritte Segment wird für eine optionale verwendet `id` .</span><span class="sxs-lookup"><span data-stu-id="cab35-651">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="cab35-652">`id`wird einer Modell Entität zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="cab35-652">`id` maps to a model entity.</span></span>

<span data-ttu-id="cab35-653">Mit dieser `default`-Route wir der URL-Pfad `/Products/List` der `ProductsController.List`-Aktion und `/Blog/Article/17``BlogController.Article` zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="cab35-653">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="cab35-654">Diese Zuordnung basiert **ausschließlich** auf den Controller- und Aktionsnamen und nicht auf Namespaces, Speicherorten für Quelldateien oder Methodenparametern.</span><span class="sxs-lookup"><span data-stu-id="cab35-654">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="cab35-655">Die Kombination aus herkömmlichem Routing und Standardrouting ermöglicht es Ihnen, Anwendungen schnell zu erstellen, ohne für jede definierte Aktion ein neues URL-Muster entwerfen zu müssen.</span><span class="sxs-lookup"><span data-stu-id="cab35-655">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="cab35-656">Bei Anwendungen mit Aktionen im CRUD-Stil können einheitliche URLs auf allen Controllern dabei helfen, den Code zu vereinfachen und die UI vorhersehbarer zu machen.</span><span class="sxs-lookup"><span data-stu-id="cab35-656">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="cab35-657">`id` wird von der Routenvorlage als optional definiert, was bedeutet, dass Ihre Aktionen ausgeführt werden, ohne dass die ID als Teil der URL bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-657">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="cab35-658">Wenn `id` in der URL ausgelassen wird, wird es üblicherweise von der Modellbindung auf `0` festgelegt, und daher wird in der Datenbank keine Entität gefunden, die `id == 0` entspricht.</span><span class="sxs-lookup"><span data-stu-id="cab35-658">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="cab35-659">Mit dem Attributrouting können Sie präzise steuern, für welche Aktionen die ID erforderlich ist und für welche nicht.</span><span class="sxs-lookup"><span data-stu-id="cab35-659">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="cab35-660">Gemäß der Konvention enthält die Dokumentation optionale Parameter wie `id`, wenn sie wahrscheinlich in der richtigen Verwendung angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-660">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="cab35-661">Mehrere Routen</span><span class="sxs-lookup"><span data-stu-id="cab35-661">Multiple routes</span></span>

<span data-ttu-id="cab35-662">Sie können mehrere Routen innerhalb von `UseMvc` hinzufügen, indem Sie weitere Aufrufe von `MapRoute` hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="cab35-662">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="cab35-663">So können Sie mehrere Konventionen definieren oder herkömmlichen Routen hinzufügen, die einer bestimmten Aktion zugeordnet sind, z.B.:</span><span class="sxs-lookup"><span data-stu-id="cab35-663">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="cab35-664">Die `blog`-Route hier ist eine *dedizierte herkömmliche Route*, d.h., dass das herkömmliche Routingsystem verwendet wird, aber einer bestimmten Aktion zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="cab35-664">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="cab35-665">Da `controller` und `action` nicht als Parameter in der Routenvorlage vorkommen, können sie nur die Standardwerte haben. Daher wird diese Route immer der Aktion `BlogController.Article` zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="cab35-665">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="cab35-666">Die Routen in der Routenauflistung sind geordnet und werden in der Reihenfolge verarbeitet, in der sie hinzugefügt wurden.</span><span class="sxs-lookup"><span data-stu-id="cab35-666">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="cab35-667">In diesem Beispiel wird daher die Route `blog` vor der Route `default` überprüft.</span><span class="sxs-lookup"><span data-stu-id="cab35-667">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="cab35-668">*Dedizierte herkömmliche Routen* verwenden häufig **catch-all-** Routen Parameter wie `{*article}` , um den verbleibenden Teil des URL-Pfads zu erfassen.</span><span class="sxs-lookup"><span data-stu-id="cab35-668">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="cab35-669">Dies kann eine Route „zu gierig“ machen, d.h., dass sie alle URLs abgleicht, die eigentlich von anderen Routen abgeglichen werden sollten.</span><span class="sxs-lookup"><span data-stu-id="cab35-669">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="cab35-670">Fügen Sie die „gierigen“ Routen weiter unten in die Routingtabelle ein, um dieses Problem zu beheben.</span><span class="sxs-lookup"><span data-stu-id="cab35-670">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="cab35-671">Fallback</span><span class="sxs-lookup"><span data-stu-id="cab35-671">Fallback</span></span>

<span data-ttu-id="cab35-672">Im Rahmen der Anforderungsverarbeitung überprüft MVC, ob mit den Routenwerten ein Controller und eine Aktion in Ihrer Anwendung gefunden werden können.</span><span class="sxs-lookup"><span data-stu-id="cab35-672">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="cab35-673">Falls die Routenwerte mit keiner Aktion übereinstimmen, gilt die Route nicht als Übereinstimmung, und die nächste Route wird überprüft.</span><span class="sxs-lookup"><span data-stu-id="cab35-673">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="cab35-674">Dabei spricht man von einem *Fallback*. Dieser Vorgang soll Szenarios vereinfachen, bei denen sich herkömmliche Routen überschneiden.</span><span class="sxs-lookup"><span data-stu-id="cab35-674">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="cab35-675">Aktionen eindeutig zuordnen</span><span class="sxs-lookup"><span data-stu-id="cab35-675">Disambiguating actions</span></span>

<span data-ttu-id="cab35-676">Wenn zwei Aktionen beim Routing übereinstimmen, muss MVC beide analysieren und die beste auswählen oder eine Ausnahme auslösen.</span><span class="sxs-lookup"><span data-stu-id="cab35-676">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="cab35-677">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cab35-677">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="cab35-678">Dieser Controller definiert zwei Aktionen, die mit dem URL-Pfad `/Products/Edit/17` übereinstimmen und die Daten `{ controller = Products, action = Edit, id = 17 }` weiterleiten.</span><span class="sxs-lookup"><span data-stu-id="cab35-678">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="cab35-679">Dies ist ein typisches Muster für MVC-Controller, in dem `Edit(int)` ein Formular zum Bearbeiten eines Produkts anzeigt und `Edit(int, Product)` das bereitgestellte Formular verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="cab35-679">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="cab35-680">Um dies zu ermöglichen, muss MVC `Edit(int, Product)` auswählen, wenn die Anforderung HTTP-`POST` ist, und `Edit(int)`, wenn das HTTP-Verb ein anderes ist.</span><span class="sxs-lookup"><span data-stu-id="cab35-680">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="cab35-681">`HttpPostAttribute` (`[HttpPost]`) ist eine Implementierung von `IActionConstraint`, bei der die Aktion nur ausgewählt werden darf, wenn das HTTP-Verb `POST` ist.</span><span class="sxs-lookup"><span data-stu-id="cab35-681">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="cab35-682">Aufgrund des Vorhandenseins von `IActionConstraint` ist `Edit(int, Product)` die „bessere“ Übereinstimmung als `Edit(int)`, sodass `Edit(int, Product)` zuerst überprüft wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-682">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="cab35-683">Benutzerdefinierte `IActionConstraint`-Implementierungen müssen Sie nur in speziellen Szenarios schreiben. Es ist jedoch wichtig, die Rolle von Attributen wie `HttpPostAttribute` zu kennen, denn ähnliche Attribute sind auch für andere HTTP-Verben definiert.</span><span class="sxs-lookup"><span data-stu-id="cab35-683">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="cab35-684">Beim herkömmlichen Routing nutzen Aktionen oft denselben Aktionsnamen, wenn sie Teil eines `show form -> submit form`-Workflows sind.</span><span class="sxs-lookup"><span data-stu-id="cab35-684">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="cab35-685">Die Vorteile dessen werden deutlicher, wenn Sie den Abschnitt [Verstehen von IActionConstraint](#understanding-iactionconstraint) gelesen haben.</span><span class="sxs-lookup"><span data-stu-id="cab35-685">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="cab35-686">Wenn mehrere Routen übereinstimmen und MVC die „beste“ nicht bestimmen kann, löst es eine `AmbiguousActionException` aus.</span><span class="sxs-lookup"><span data-stu-id="cab35-686">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="cab35-687">Routennamen</span><span class="sxs-lookup"><span data-stu-id="cab35-687">Route names</span></span>

<span data-ttu-id="cab35-688">Die Zeichenfolgen `"blog"` und `"default"` in den folgenden Beispielen sind Routennamen:</span><span class="sxs-lookup"><span data-stu-id="cab35-688">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="cab35-689">Der Routenname gibt der Route einen logischen Namen, damit die benannte Route bei der URL-Generierung verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="cab35-689">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="cab35-690">Dadurch wird die URL-Erstellung erheblich vereinfacht, obwohl die Reihenfolge der Routen die URL-Generierung verkomplizieren sollte.</span><span class="sxs-lookup"><span data-stu-id="cab35-690">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="cab35-691">Routennamen müssen anwendungsweit eindeutig sein.</span><span class="sxs-lookup"><span data-stu-id="cab35-691">Route names must be unique application-wide.</span></span>

<span data-ttu-id="cab35-692">Routennamen haben keine Auswirkung auf die URL-Zuordnung oder die Verarbeitung von Anforderungen. Sie dienen nur der URL-Generierung.</span><span class="sxs-lookup"><span data-stu-id="cab35-692">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="cab35-693">Unter [Routing](xref:fundamentals/routing) finden Sie weitere ausführliche Informationen zur URL-Generierung, einschließlich der Generierung in MVC-spezifischen Hilfsprogrammen.</span><span class="sxs-lookup"><span data-stu-id="cab35-693">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="cab35-694">Attributrouting</span><span class="sxs-lookup"><span data-stu-id="cab35-694">Attribute routing</span></span>

<span data-ttu-id="cab35-695">Beim Attributrouting werden Aktionen mithilfe von Attributen direkt Routenvorlagen zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="cab35-695">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="cab35-696">Im folgenden Beispiel wird `app.UseMvc();` in der `Configure`-Methode verwendet, und es wird keine Route übergeben.</span><span class="sxs-lookup"><span data-stu-id="cab35-696">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="cab35-697">`HomeController` entspricht URLs, die denen ähneln, denen die Standardroute `{controller=Home}/{action=Index}/{id?}` entsprechen würde:</span><span class="sxs-lookup"><span data-stu-id="cab35-697">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

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

<span data-ttu-id="cab35-698">Die `HomeController.Index()`-Aktion wird für die URL-Pfade `/`, `/Home` und `/Home/Index` jeweils ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="cab35-698">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="cab35-699">In diesem Beispiel wird ein wichtiger Unterschied beim Programmieren zwischen dem Attributrouting und dem herkömmlichen Routing hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="cab35-699">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="cab35-700">Attributrouting erfordert mehr Eingabe, um eine Route anzugeben. Die herkömmliche Standardroute verarbeitet auch kürzere Routen.</span><span class="sxs-lookup"><span data-stu-id="cab35-700">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="cab35-701">Attributrouting ermöglicht jedoch (und erfordert auch) die präzise Kontrolle der Routenvorlagen, die für die einzelnen Aktionen gelten.</span><span class="sxs-lookup"><span data-stu-id="cab35-701">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="cab35-702">Beim Attributrouting haben der Controller- und der Aktionsname **keine** Auswirkung darauf, welche Aktion ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-702">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="cab35-703">In diesem Beispiel werden dieselben URLs wie im vorherigen Beispiel abgeglichen.</span><span class="sxs-lookup"><span data-stu-id="cab35-703">This example will match the same URLs as the previous example.</span></span>

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
> <span data-ttu-id="cab35-704">Die oben genannten Routenvorlagen definieren keine Routenparameter für `action`, `area` und `controller`.</span><span class="sxs-lookup"><span data-stu-id="cab35-704">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="cab35-705">Diese Routenparameter sind in Attributrouten nicht zulässig.</span><span class="sxs-lookup"><span data-stu-id="cab35-705">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="cab35-706">Da die Routenvorlage bereits einer Aktion zugeordnet ist, wäre es nicht sinnvoll, den Aktionsnamen aus der URL zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="cab35-706">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="cab35-707">Attributrouting mit Http[Verb]-Attributen</span><span class="sxs-lookup"><span data-stu-id="cab35-707">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="cab35-708">Beim Attributrouting können auch `Http[Verb]`-Attribute wie `HttpPostAttribute` verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-708">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="cab35-709">Alle diese Attribute können eine Routenvorlage akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="cab35-709">All of these attributes can accept a route template.</span></span> <span data-ttu-id="cab35-710">In diesem Beispiel werden zwei Aktionen gezeigt, die derselben Routenvorlage zugeordnet sind:</span><span class="sxs-lookup"><span data-stu-id="cab35-710">This example shows two actions that match the same route template:</span></span>

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

<span data-ttu-id="cab35-711">Für einen URL-Pfad wie `/products` wird die Aktion `ProductsApi.ListProducts` ausgeführt, wenn das HTTP-Verb `GET` ist, und `ProductsApi.CreateProduct` wird ausgeführt, wenn das HTTP-Verb `POST` entspricht.</span><span class="sxs-lookup"><span data-stu-id="cab35-711">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="cab35-712">Attributrouting gleicht zuerst die URL gegen die Routenvorlagen ab, die von den Routenattributen definiert werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-712">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="cab35-713">Sobald eine Routenvorlage übereinstimmt, werden `IActionConstraint`-Einschränkungen angewendet, um zu bestimmen, welche Aktionen ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="cab35-713">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="cab35-714">Beim Erstellen einer REST-API wird `[Route(...)]` selten für eine Aktionsmethode verwendet, die Aktion alle HTTP-Methoden akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="cab35-714">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="cab35-715">Es ist besser, das spezifischere `Http*Verb*Attributes` zu nutzen, um präzise anzugeben, was Ihre API unterstützt.</span><span class="sxs-lookup"><span data-stu-id="cab35-715">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="cab35-716">REST-API-Clients sollten wissen, welche Pfade und HTTP-Verben bestimmten logischen Operationen entsprechen.</span><span class="sxs-lookup"><span data-stu-id="cab35-716">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="cab35-717">Da eine Attributroute für eine bestimmte Aktion gilt, ist es einfach, Parameter als Teil der Routenvorlagendefinition erforderlich festzulegen.</span><span class="sxs-lookup"><span data-stu-id="cab35-717">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="cab35-718">In diesem Beispiel ist `id` als Teil des URL-Pfads erforderlich.</span><span class="sxs-lookup"><span data-stu-id="cab35-718">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="cab35-719">Die `ProductsApi.GetProduct(int)`-Aktion wird für einen URL-Pfad wie `/products/3` ausgeführt, jedoch nicht für einen URL-Pfad wie `/products`.</span><span class="sxs-lookup"><span data-stu-id="cab35-719">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="cab35-720">Eine vollständige Beschreibung und Routenvorlagen und dazugehörige Optionen finden Sie unter [Routing in ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="cab35-720">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="cab35-721">Routenname</span><span class="sxs-lookup"><span data-stu-id="cab35-721">Route Name</span></span>

<span data-ttu-id="cab35-722">Im folgenden Code wird ein *Routenname* von `Products_List` definiert:</span><span class="sxs-lookup"><span data-stu-id="cab35-722">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="cab35-723">Routennamen können verwendet werden, um basierend auf einer bestimmten Route eine URL zu generieren.</span><span class="sxs-lookup"><span data-stu-id="cab35-723">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="cab35-724">Routennamen haben keine Auswirkung auf das URL-Zuordnungsverhalten des Routings und dienen nur zur URL-Generierung.</span><span class="sxs-lookup"><span data-stu-id="cab35-724">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="cab35-725">Routennamen müssen anwendungsweit eindeutig sein.</span><span class="sxs-lookup"><span data-stu-id="cab35-725">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="cab35-726">Vergleichen Sie dies mit der herkömmlichen *Standardroute*, die den `id`-Parameter als optional definiert (`{id?}`).</span><span class="sxs-lookup"><span data-stu-id="cab35-726">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="cab35-727">APIs präzise angeben zu können, hat Vorteile, z.B. können `/products` und `/products/5` an unterschiedliche Aktionen gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-727">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="cab35-728">Kombinieren von Routen</span><span class="sxs-lookup"><span data-stu-id="cab35-728">Combining routes</span></span>

<span data-ttu-id="cab35-729">Um Attributrouting weniger repetitiv zu gestalten, werden Routenattribute auf dem Controller mit Routenattributen auf den einzelnen Aktionen kombiniert.</span><span class="sxs-lookup"><span data-stu-id="cab35-729">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="cab35-730">Alle auf dem Controller definierten Routenvorlagen werden den Routenvorlagen auf den Aktionen vorangestellt.</span><span class="sxs-lookup"><span data-stu-id="cab35-730">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="cab35-731">Wenn Routenattribute auf dem Controller platziert werden, verwenden **alle** Aktionen im Controller Attributrouting.</span><span class="sxs-lookup"><span data-stu-id="cab35-731">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

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

<span data-ttu-id="cab35-732">In diesem Beispiel kann der URL-Pfad `/products``ProductsApi.ListProducts` und der URL-Pfad `/products/5``ProductsApi.GetProduct(int)` zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-732">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="cab35-733">Beide Aktionen gleichen nur „HTTP `GET`“ ab, weil sie mit `HttpGetAttribute` markiert sind.</span><span class="sxs-lookup"><span data-stu-id="cab35-733">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="cab35-734">Routenvorlagen, die auf eine Aktion angewendet werden, die mit einem `/` oder `~/` beginnen, können nicht mit Routenvorlagen kombiniert werden, die auf den Controller angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-734">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="cab35-735">In diesem Beispiel werden mehrere URL-Pfade zugeordnet, die der *Standardroute* ähneln.</span><span class="sxs-lookup"><span data-stu-id="cab35-735">This example matches a set of URL paths similar to the *default route*.</span></span>

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

### <a name="ordering-attribute-routes"></a><span data-ttu-id="cab35-736">Ordnen der Attributrouten</span><span class="sxs-lookup"><span data-stu-id="cab35-736">Ordering attribute routes</span></span>

<span data-ttu-id="cab35-737">Im Gegensatz zu herkömmlichen Routen, die in einer definierten Reihenfolge ausgeführt werden, erstellt das Attribut Routing eine Struktur und gleicht alle Routen gleichzeitig ab.</span><span class="sxs-lookup"><span data-stu-id="cab35-737">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="cab35-738">Der Vorgang wird also ausgeführt, als ob die Routeneinträge in der idealen Reihenfolge platziert worden wären: Die spezifischsten Routen können also vor den allgemeineren ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-738">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="cab35-739">Eine Route wie `blog/search/{topic}` ist z.B. spezifischer als eine Route wie `blog/{*article}`.</span><span class="sxs-lookup"><span data-stu-id="cab35-739">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="cab35-740">Logisch gesehen wird die Route `blog/search/{topic}` standardmäßig zuerst ausgeführt, da dies die einzig sinnvolle Reihenfolge ist.</span><span class="sxs-lookup"><span data-stu-id="cab35-740">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="cab35-741">Beim herkömmlichen Routing ist der Entwickler verantwortlich dafür, die Routen in die gewünschte Reihenfolge zu bringen.</span><span class="sxs-lookup"><span data-stu-id="cab35-741">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="cab35-742">Attributrouten können mithilfe der `Order`-Eigenschaft aller vom Framework bereitgestellten Routenattribute eine Reihenfolge konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="cab35-742">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="cab35-743">Routen werden entsprechend einer aufsteigenden Reihenfolge der `Order`-Eigenschaft verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="cab35-743">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="cab35-744">Die Standardreihenfolge ist `0`.</span><span class="sxs-lookup"><span data-stu-id="cab35-744">The default order is `0`.</span></span> <span data-ttu-id="cab35-745">Wird für eine Route `Order = -1` festgelegt, wird sie vor denjenigen Routen ausgeführt, für die keine Reihenfolge festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="cab35-745">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="cab35-746">Wird für eine Route `Order = 1` festgelegt, wird sie ausgeführt, nachdem die Standardroutenreihenfolge ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="cab35-746">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="cab35-747">Vermeiden Sie eine Abhängigkeit von `Order`.</span><span class="sxs-lookup"><span data-stu-id="cab35-747">Avoid depending on `Order`.</span></span> <span data-ttu-id="cab35-748">Wenn der URL-Raum explizite Reihenfolgenwerte erfordert, um korrekt weiterzuleiten, ist es wahrscheinlich auch für Clients verwirrend.</span><span class="sxs-lookup"><span data-stu-id="cab35-748">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="cab35-749">Beim Attributrouting wird im Allgemeinen mithilfe der URL-Zuordnung die richtige Route ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="cab35-749">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="cab35-750">Wenn die für die URL-Generierung verwendete Standardreihenfolge nicht funktioniert, ist es meist einfacher, Routennamen als Außerkraftsetzung zu verwenden, statt die `Order`-Eigenschaft anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="cab35-750">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

Razor<span data-ttu-id="cab35-751"> Pages-Routing und MVC Controller-Routing verwenden eine gemeinsame Implementierung.</span><span class="sxs-lookup"><span data-stu-id="cab35-751"> Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="cab35-752">Informationen zur Routen Reihenfolge in den Razor Seiten Themen finden Sie unter [ Razor pages Route und App Conventions: Route Order](xref:razor-pages/razor-pages-conventions#route-order).</span><span class="sxs-lookup"><span data-stu-id="cab35-752">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="cab35-753">Ersetzen von Token in Routenvorlagen ([controller], [action] [area])</span><span class="sxs-lookup"><span data-stu-id="cab35-753">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="cab35-754">Zur einfacheren Unterstützung unterstützen Attribut Routen die *Tokenersetzung* durch Einschließen eines Tokens in eckige Klammern ( `[` , `]` ).</span><span class="sxs-lookup"><span data-stu-id="cab35-754">For convenience, attribute routes support *token replacement* by enclosing a token in square-brackets (`[`, `]`).</span></span> <span data-ttu-id="cab35-755">Die Token `[action]`, `[area]` und `[controller]` werden durch die Werte der Aktionsnamen, den Namen des Bereichs und des Controllers der Aktion ersetzt, in dem die Route definiert ist.</span><span class="sxs-lookup"><span data-stu-id="cab35-755">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="cab35-756">In dem folgenden Beispiel entsprechen die Aktionen wie in den Kommentaren beschrieben URL-Pfaden:</span><span class="sxs-lookup"><span data-stu-id="cab35-756">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="cab35-757">Die Tokenersetzung tritt im letzten Schritt der Erstellung von Attributrouten auf.</span><span class="sxs-lookup"><span data-stu-id="cab35-757">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="cab35-758">Der folgende Code verhält sich genauso wie der aus dem obigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cab35-758">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="cab35-759">Attributrouten können auch mit Vererbung kombiniert werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-759">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="cab35-760">Dies ist besonders effektiv in Kombination mit der Tokenersetzung.</span><span class="sxs-lookup"><span data-stu-id="cab35-760">This is particularly powerful combined with token replacement.</span></span>

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

<span data-ttu-id="cab35-761">Tokenersetzung gilt auch für Routennamen, die durch Attributrouten definiert werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-761">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="cab35-762">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generiert für jede Aktion einen eindeutigen Routennamen.</span><span class="sxs-lookup"><span data-stu-id="cab35-762">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="cab35-763">Damit das Trennzeichen `[` oder `]` der Tokenersetzungs-Literalzeichenfolge bei einem Abgleich gefunden wird, muss es doppelt vorhanden sein (`[[` oder `]]`), was einem Escapezeichen entspricht.</span><span class="sxs-lookup"><span data-stu-id="cab35-763">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="cab35-764">Verwenden eines Parametertransformators zum Anpassen der Tokenersetzung</span><span class="sxs-lookup"><span data-stu-id="cab35-764">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="cab35-765">Die Tokenersetzung kann mit einem Parametertransformator angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-765">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="cab35-766">Ein Parametertransformator implementiert `IOutboundParameterTransformer` und wandelt den Wert der Parameter um.</span><span class="sxs-lookup"><span data-stu-id="cab35-766">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="cab35-767">Beispielsweise ändert ein benutzerdefinierter `SlugifyParameterTransformer`-Parametertransformator den Routenwert `SubscriptionManagement` in `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="cab35-767">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="cab35-768">Die `RouteTokenTransformerConvention` ist eine Anwendungsmodellkonvention, die Folgendes ausführt:</span><span class="sxs-lookup"><span data-stu-id="cab35-768">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="cab35-769">Wendet einen angegebenen Parametertransformator auf alle Attributrouten in der App an.</span><span class="sxs-lookup"><span data-stu-id="cab35-769">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="cab35-770">Passt die Tokenwerte für Attributrouten bei ihrer Ersetzung an.</span><span class="sxs-lookup"><span data-stu-id="cab35-770">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="cab35-771">Die `RouteTokenTransformerConvention` wird als Option in `ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="cab35-771">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

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

### <a name="multiple-routes"></a><span data-ttu-id="cab35-772">Mehrere Routen</span><span class="sxs-lookup"><span data-stu-id="cab35-772">Multiple Routes</span></span>

<span data-ttu-id="cab35-773">Attributrouting unterstützt das Definieren mehrerer Routen, die zu derselben Aktion führen.</span><span class="sxs-lookup"><span data-stu-id="cab35-773">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="cab35-774">Dies wird am häufigsten beim Imitieren des Verhaltens der *herkömmlichen Standardroute* verwendet. Im folgenden Beispiel wird dies gezeigt:</span><span class="sxs-lookup"><span data-stu-id="cab35-774">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="cab35-775">Wenn mehrere Routenattribute auf dem Controller platziert werden, wird jedes mit jedem der Routenattribute auf den Aktionsmethoden kombiniert.</span><span class="sxs-lookup"><span data-stu-id="cab35-775">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

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

<span data-ttu-id="cab35-776">Werden mehrere Routenattribute (die `IActionConstraint` implementieren) auf einer Aktion platziert, wird jede Aktionseinschränkung mit der Routenvorlage aus dem Attribut kombiniert, das sie definiert.</span><span class="sxs-lookup"><span data-stu-id="cab35-776">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

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
> <span data-ttu-id="cab35-777">Obwohl das Verwenden mehrerer Routen für Aktionen sehr hilfreich scheint, ist es empfehlenswerter, den URL-Raum der Anwendung einfach und gut definiert zu halten.</span><span class="sxs-lookup"><span data-stu-id="cab35-777">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="cab35-778">Verwenden Sie nur mehrere Routen für Aktionen, wenn dies notwendig ist, z.B. um vorhandene Clients zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="cab35-778">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="cab35-779">Angeben von optionalen Attributroutenparametern, Standardwerten und Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="cab35-779">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="cab35-780">Attributrouten unterstützen dieselbe Inline-Syntax wie herkömmliche Routen, um optionale Parameter, Standardwerte und Einschränkungen anzugeben.</span><span class="sxs-lookup"><span data-stu-id="cab35-780">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="cab35-781">Eine ausführliche Beschreibung der Syntax der Routenvorlage finden Sie unter [Routenvorlagenreferenz](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="cab35-781">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="cab35-782">Benutzerdefinierte Routenattribute mithilfe von `IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="cab35-782">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="cab35-783">Alle im Framework bereitgestellten Routenattribute (`[Route(...)]`, `[HttpGet(...)]` usw.) implementieren die `IRouteTemplateProvider`-Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="cab35-783">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="cab35-784">MVC sucht in Controllerklassen und Aktionsmethoden nach Attributen, wenn die Anwendung gestartet wird, und verwendet diejenigen, die `IRouteTemplateProvider` implementieren, um die anfänglichen Routen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="cab35-784">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="cab35-785">Sie können `IRouteTemplateProvider` implementieren, um eigene Routenattribute zu definieren.</span><span class="sxs-lookup"><span data-stu-id="cab35-785">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="cab35-786">Jeder `IRouteTemplateProvider` lässt Sie eine einzelne Route mit einer benutzerdefinierten Routenvorlage, Reihenfolge und einem benutzerdefinierten Namen definieren:</span><span class="sxs-lookup"><span data-stu-id="cab35-786">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="cab35-787">Das Attribut aus dem obigen Beispiel legt `Template` automatisch auf `"api/[controller]"` fest, wenn `[MyApiController]` angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-787">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="cab35-788">Anpassen von Attributrouten mithilfe des Anwendungsmodells</span><span class="sxs-lookup"><span data-stu-id="cab35-788">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="cab35-789">Das *Anwendungsmodell* ist ein Objektmodell, das beim Starten aus allen Metadaten von MVC erstellt wird, um Ihre Aktionen weiterzuleiten und auszuführen.</span><span class="sxs-lookup"><span data-stu-id="cab35-789">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="cab35-790">Das *Anwendungsmodell* enthält alle Daten, die aus Routenattributen (über `IRouteTemplateProvider`) erfasst wurden.</span><span class="sxs-lookup"><span data-stu-id="cab35-790">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="cab35-791">Sie können *Konventionen* schreiben, damit das Anwendungsmodell beim Startzeitpunkt das Routingverhalten anpasst.</span><span class="sxs-lookup"><span data-stu-id="cab35-791">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="cab35-792">Dieser Abschnitt enthält ein einfaches Beispiel für das Anpassen des Routings mithilfe des Anwendungsmodells.</span><span class="sxs-lookup"><span data-stu-id="cab35-792">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="cab35-793">Gemischtes Routing: Attributrouting vs. herkömmliches Routing</span><span class="sxs-lookup"><span data-stu-id="cab35-793">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="cab35-794">MVC-Anwendungen können herkömmliches Routing und Attributrouting kombinieren.</span><span class="sxs-lookup"><span data-stu-id="cab35-794">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="cab35-795">In der Regel werden herkömmliche Routen für Controller verwendet, die für Browser-HTML-Seiten gedacht sind, und das Attributrouting für Controller für REST-APIs.</span><span class="sxs-lookup"><span data-stu-id="cab35-795">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="cab35-796">Aktionen werden entweder herkömmlich oder über Attribute zugeordnet,</span><span class="sxs-lookup"><span data-stu-id="cab35-796">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="cab35-797">d.h., dass eine Route auf dem Controller oder der Aktion platziert wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-797">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="cab35-798">Aktionen, die Attributrouten definieren, können nicht über die herkömmliche Routen und umgekehrt erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-798">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="cab35-799">Wenn nur **ein** Routenattribut auf dem Controller platziert wird, werden alle Aktionen im Controller über Attribute zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="cab35-799">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="cab35-800">Die beiden Routingmethoden unterscheiden sich in dem Prozess, der angewendet wird, nachdem eine URL einer Routenvorlage zugeordnet wurde.</span><span class="sxs-lookup"><span data-stu-id="cab35-800">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="cab35-801">Beim herkömmlichen Routing dienen die Routenwerte aus der Zuordnung dazu, aus einer Nachschlagetabelle aller herkömmlich zugeordneten Aktionen die Aktion und den Controller auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="cab35-801">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="cab35-802">Beim Attributrouting ist jede Vorlage bereits einer Aktion zugeordnet, und keine weitere Suche ist erforderlich.</span><span class="sxs-lookup"><span data-stu-id="cab35-802">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="cab35-803">Komplexe Segmente</span><span class="sxs-lookup"><span data-stu-id="cab35-803">Complex segments</span></span>

<span data-ttu-id="cab35-804">Komplexe Segmente (z.B. `[Route("/dog{token}cat")]`) werden von rechts nach links auf eine nicht gierige Weise durch entsprechende Literale verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="cab35-804">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="cab35-805">Eine entsprechende Beschreibung finden Sie im [Quellcode](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296).</span><span class="sxs-lookup"><span data-stu-id="cab35-805">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="cab35-806">Weitere Informationen finden Sie in [diesem Thema](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span><span class="sxs-lookup"><span data-stu-id="cab35-806">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="cab35-807">URL-Generierung</span><span class="sxs-lookup"><span data-stu-id="cab35-807">URL Generation</span></span>

<span data-ttu-id="cab35-808">MVC-Anwendungen können Routingfeatures zur URL-Generierung verwenden, um URL-Links zu Aktionen zu generieren.</span><span class="sxs-lookup"><span data-stu-id="cab35-808">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="cab35-809">Durch das Generieren von URLs müssen URLs nicht mehr hartcodiert werden, sodass der Code robuster und leichter verwaltbar wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-809">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="cab35-810">Dieser Abschnitt konzentriert sich auf die von MVC bereitgestellte URL-Generierung und befasst sich nur kurz mit der Funktionsweise.</span><span class="sxs-lookup"><span data-stu-id="cab35-810">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="cab35-811">Eine detaillierte Beschreibung der URL-Generierung finden Sie unter [Routing in ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="cab35-811">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="cab35-812">Die `IUrlHelper`-Schnittstelle ist die zugrunde liegende Infrastruktur zwischen MVC und dem Routing zur URL-Generierung.</span><span class="sxs-lookup"><span data-stu-id="cab35-812">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="cab35-813">Eine Instanz von `IUrlHelper` ist über die `Url`-Eigenschaft in Controllern, Ansichten und Ansichtskomponenten verfügbar.</span><span class="sxs-lookup"><span data-stu-id="cab35-813">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="cab35-814">In diesem Beispiel wird die `IUrlHelper`-Schnittstelle von der `Controller.Url`-Eigenschaft dazu verwendet, eine URL in einer anderen Aktion zu generieren.</span><span class="sxs-lookup"><span data-stu-id="cab35-814">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="cab35-815">Wenn die Anwendung die herkömmliche Standardroute verwendet, ist der Wert der `url`-Variable die URL-Pfadzeichenfolge `/UrlGeneration/Destination`.</span><span class="sxs-lookup"><span data-stu-id="cab35-815">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="cab35-816">Dieser URL-Pfad wird vom Routing erstellt, indem Routenwerte aus der aktuellen Anforderung (Umgebungswerte) mit den an `Url.Action` übergebenen Werten kombiniert und anschließend in die Routenvorlage eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="cab35-816">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="cab35-817">Der Wert eines jeden Routenparameters wird in der Routenvorlage durch die entsprechenden Namen mit den Werten und Umgebungswerten ersetzt.</span><span class="sxs-lookup"><span data-stu-id="cab35-817">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="cab35-818">Ein Routenparameter ohne Wert kann einen Standardwert verwenden, wenn er über einen verfügt, oder übersprungen werden, wenn er optional ist (wie im Fall von `id` in diesem Beispiel).</span><span class="sxs-lookup"><span data-stu-id="cab35-818">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="cab35-819">Die URL-Generierung schlägt fehl, wenn ein erforderlicher Routenparameter keinen entsprechenden Wert besitzt.</span><span class="sxs-lookup"><span data-stu-id="cab35-819">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="cab35-820">Wenn die URL-Generierung für eine Route fehlschlägt, wird die nächste Route ausprobiert, bis alle Routen getestet wurden oder eine Übereinstimmung gefunden wurde.</span><span class="sxs-lookup"><span data-stu-id="cab35-820">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="cab35-821">In dem Beispiel mit `Url.Action` oben wird von herkömmlichem Routing ausgegangen. Die URL-Generierung funktioniert ähnlich wie das Attributrouting, obwohl sich die Konzepte unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="cab35-821">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="cab35-822">Bei herkömmlichem Routing wird mit den Routenwerten eine Vorlage erweitert, und die Routenwerte für `controller` und `action` kommen in der Regel in dieser Vorlage vor. Das funktioniert, weil sich die mit dem Routing zugeordneten URLs an eine *Konvention* halten.</span><span class="sxs-lookup"><span data-stu-id="cab35-822">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="cab35-823">Beim Attributrouting dürfen die Routenwerte für `controller` und `action` nicht in der Vorlage vorkommen. Sie werden stattdessen verwendet, um nachzuschlagen, welche Vorlage verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="cab35-823">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="cab35-824">In diesem Beispiel wird das Attributrouting verwendet:</span><span class="sxs-lookup"><span data-stu-id="cab35-824">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="cab35-825">MVC erstellt eine Nachschlagetabelle aller über Attribute zugeordneten Aktionen und ordnet die `controller`- und `action`-Werte zu, um die Routenvorlage auszuwählen, die für die URL-Generierung verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="cab35-825">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="cab35-826">Im obigen Beispiel wird `custom/url/to/destination` generiert.</span><span class="sxs-lookup"><span data-stu-id="cab35-826">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="cab35-827">Generieren von URLs nach Aktionsnamen</span><span class="sxs-lookup"><span data-stu-id="cab35-827">Generating URLs by action name</span></span>

<span data-ttu-id="cab35-828">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="cab35-828">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="cab35-829">`Action`) und alle zugehörigen Überladungen bauen alle auf der Idee auf, dass Sie angeben, was Sie verknüpfen möchten, indem Sie einen Controllernamen und einen Aktionsnamen angeben.</span><span class="sxs-lookup"><span data-stu-id="cab35-829">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="cab35-830">Bei Verwendung von `Url.Action` werden die aktuellen Routenwerte für `controller` und `action` für Sie festgelegt. Die Werte von `controller` und `action` bestehen sowohl aus *Umgebungswerten* \*\*als auch aus \*\* *Werten*.</span><span class="sxs-lookup"><span data-stu-id="cab35-830">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="cab35-831">Die Methode `Url.Action` verwendet immer die aktuellen Werte von `action` und `controller` und generiert einen URL-Pfad, der zur aktuellen Aktion weiterleitet.</span><span class="sxs-lookup"><span data-stu-id="cab35-831">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="cab35-832">Beim Routing wird versucht, mit den Werten in den Umgebungswerten Informationen auszufüllen, die Sie beim Generieren einer URL nicht bereitgestellt haben.</span><span class="sxs-lookup"><span data-stu-id="cab35-832">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="cab35-833">Mit Routen wie `{a}/{b}/{c}/{d}` und Umgebungswerten wie `{ a = Alice, b = Bob, c = Carol, d = David }` hat das Routing genügend Informationen, um eine URL ohne zusätzliche Werte zu generieren, da alle Routenparameter einen Wert aufweisen.</span><span class="sxs-lookup"><span data-stu-id="cab35-833">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="cab35-834">Wenn Sie den Wert `{ d = Donovan }` hinzufügen, wird der Wert `{ d = David }` ignoriert, und der generierte URL-Pfad wäre `Alice/Bob/Carol/Donovan`.</span><span class="sxs-lookup"><span data-stu-id="cab35-834">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="cab35-835">URL-Pfade sind hierarchisch.</span><span class="sxs-lookup"><span data-stu-id="cab35-835">URL paths are hierarchical.</span></span> <span data-ttu-id="cab35-836">Wenn Sie im obigen Beispiel den Wert `{ c = Cheryl }` hinzufügen, werden die beiden Werte `{ c = Carol, d = David }` ignoriert.</span><span class="sxs-lookup"><span data-stu-id="cab35-836">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="cab35-837">In diesem Fall haben wir keinen Wert für `d` mehr, und die URL-Generierung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="cab35-837">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="cab35-838">Sie müssten dann den gewünschten Wert von `c` und `d` angeben.</span><span class="sxs-lookup"><span data-stu-id="cab35-838">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="cab35-839">Man könnte annehmen, dass dieses Problem bei der Standardroute auftritt (`{controller}/{action}/{id?}`). Tatsächlich passiert es in der Praxis jedoch selten, das `Url.Action` immer explizit einen `controller`- und `action`-Wert angibt.</span><span class="sxs-lookup"><span data-stu-id="cab35-839">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="cab35-840">Längere Überladungen von `Url.Action` akzeptieren auch ein zusätzliches *route values*-Objekt, um andere Werte für Routenparameter als `controller` und `action` bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="cab35-840">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="cab35-841">Es wird in der Regel mit `id` verwendet, z.B. in `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="cab35-841">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="cab35-842">Gemäß der Konvention ist das *Routenwerte*-Objekt eines des anonymen Typs, es kann aber auch ein `IDictionary<>`-Objekt oder ein *Plain Old .NET Object* sein.</span><span class="sxs-lookup"><span data-stu-id="cab35-842">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="cab35-843">Alle zusätzlichen Routenwerte, die keinen Routenparametern zugeordnet sind, werden in der Abfragezeichenfolge platziert.</span><span class="sxs-lookup"><span data-stu-id="cab35-843">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="cab35-844">Um eine absolute URL zu erstellen, verwenden Sie eine Überladung, die eine `protocol` akzeptiert: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="cab35-844">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="cab35-845">Generieren von URLs nach Routen</span><span class="sxs-lookup"><span data-stu-id="cab35-845">Generating URLs by route</span></span>

<span data-ttu-id="cab35-846">Im obigen Code wurde das Generieren einer URL durch das Übergeben des Controller- und Aktionsnamens veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="cab35-846">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="cab35-847">`IUrlHelper` stellt außerdem die `Url.RouteUrl`-Methodenfamilie bereit.</span><span class="sxs-lookup"><span data-stu-id="cab35-847">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="cab35-848">Diese Methoden ähneln `Url.Action`, kopieren jedoch nicht die aktuellen Werte `action` und `controller` in die Routenwerte.</span><span class="sxs-lookup"><span data-stu-id="cab35-848">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="cab35-849">Oftmals wird damit ein Routenname angegeben, um mit einer bestimmten Route die URL zu generieren, in der Regel *ohne* Angabe eines Controller- oder Aktionsnamens.</span><span class="sxs-lookup"><span data-stu-id="cab35-849">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="cab35-850">Generieren von URLs in HTML</span><span class="sxs-lookup"><span data-stu-id="cab35-850">Generating URLs in HTML</span></span>

<span data-ttu-id="cab35-851">`IHtmlHelper` stellt die `HtmlHelper`-Methoden `Html.BeginForm` und `Html.ActionLink` bereit, um jeweils `<form>`- und `<a>`-Elemente zu generieren.</span><span class="sxs-lookup"><span data-stu-id="cab35-851">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="cab35-852">Diese Methoden verwenden die `Url.Action`-Methode, um eine URL zu generieren, und akzeptieren ähnliche Argumente.</span><span class="sxs-lookup"><span data-stu-id="cab35-852">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="cab35-853">Die `Url.RouteUrl`-Begleiter für `HtmlHelper` sind `Html.BeginRouteForm` und `Html.RouteLink`, die ähnliche Funktionen aufweisen.</span><span class="sxs-lookup"><span data-stu-id="cab35-853">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="cab35-854">Taghilfsprogramme generieren URLs mit den Taghilfsprogrammen `form` und `<a>`.</span><span class="sxs-lookup"><span data-stu-id="cab35-854">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="cab35-855">Beide implementieren mit `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="cab35-855">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="cab35-856">Weitere Informationen finden Sie unter [Einführung in die Verwendung von Taghilfsprogrammen in Formularen in ASP.NET Core](../views/working-with-forms.md).</span><span class="sxs-lookup"><span data-stu-id="cab35-856">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="cab35-857">In Ansichten ist `IUrlHelper` über die `Url`-Eigenschaft für jede Ad-hoc-URL-Generierung verfügbar, die keine der oben genannten ist.</span><span class="sxs-lookup"><span data-stu-id="cab35-857">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="cab35-858">Generieren von URLs in Aktionsergebnissen</span><span class="sxs-lookup"><span data-stu-id="cab35-858">Generating URLS in Action Results</span></span>

<span data-ttu-id="cab35-859">In den obigen Beispielen wurde gezeigt, wie `IUrlHelper` in einem Controller verwendet wird und dass es üblicherweise dazu dient, eine URL im Rahmen eines Aktionsergebnisses zu generieren.</span><span class="sxs-lookup"><span data-stu-id="cab35-859">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="cab35-860">Die Basisklassen `ControllerBase` und `Controller` stellen Hilfsmethoden für Aktionsergebnisse bereit, die auf eine andere Aktionen verweisen.</span><span class="sxs-lookup"><span data-stu-id="cab35-860">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="cab35-861">Eine typische Verwendung besteht darin, nach dem Akzeptieren einer Benutzereingabe weiterzuleiten.</span><span class="sxs-lookup"><span data-stu-id="cab35-861">One typical usage is to redirect after accepting user input.</span></span>

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

<span data-ttu-id="cab35-862">Die Factorymethoden der Aktionsergebnisse folgen einem ähnlichen Muster wie die Methoden für `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="cab35-862">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="cab35-863">Sonderfall für dedizierte herkömmliche Routen</span><span class="sxs-lookup"><span data-stu-id="cab35-863">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="cab35-864">Beim herkömmlichen Routing können Sie eine bestimmte Art von Routendefinition verwenden, die als *dedizierte herkömmliche Route* bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-864">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="cab35-865">Die Route namens `blog` im folgenden Beispiel ist eine dedizierte herkömmliche Route.</span><span class="sxs-lookup"><span data-stu-id="cab35-865">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="cab35-866">Wenn Sie diese Routendefinitionen verwenden, generiert `Url.Action("Index", "Home")` den URL-Pfad `/` mit der `default`-Route. Die Frage ist nur warum?</span><span class="sxs-lookup"><span data-stu-id="cab35-866">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="cab35-867">Man könnte meinen, dass die Routenwerte `{ controller = Home, action = Index }` ausreichen würden, um eine URL mithilfe von `blog` zu generieren, und das Ergebnis wäre `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="cab35-867">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="cab35-868">Dedizierte herkömmliche Routen nutzen ein spezielles Verhalten von Standardwerten, die keinen entsprechenden Routenparameter besitzen, der verhindert, dass die Route bei der URL-Generierung „zu gierig“ wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-868">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="cab35-869">In diesem Fall sind die Standardwerte `{ controller = Blog, action = Article }`, und weder `controller` noch `action` werden als Routenparameter verwendet.</span><span class="sxs-lookup"><span data-stu-id="cab35-869">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="cab35-870">Wenn das Routing die URL-Generierung ausführt, müssen die angegebenen Werte mit den Standardwerten übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="cab35-870">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="cab35-871">Die URL-Generierung mithilfe von `blog` schlägt fehl, da die Werte `{ controller = Home, action = Index }` nicht mit `{ controller = Blog, action = Article }` übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="cab35-871">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="cab35-872">Routing greift dann wieder auf `default` zurück, was erfolgreich ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-872">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="cab35-873">Bereiche</span><span class="sxs-lookup"><span data-stu-id="cab35-873">Areas</span></span>

<span data-ttu-id="cab35-874">[Bereiche](areas.md) sind ein MVC-Feature, mit dem verwandte Funktionen in einer Gruppe als separater Routing-Namespace (für Controlleraktionen) und als Ordnerstruktur (für Ansichten) organisiert werden können.</span><span class="sxs-lookup"><span data-stu-id="cab35-874">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="cab35-875">Mithilfe von Bereichen kann eine Anwendung mehrere Controller mit demselben Namen haben – solange sie verschiedene *Bereiche* haben.</span><span class="sxs-lookup"><span data-stu-id="cab35-875">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="cab35-876">Mithilfe von Bereichen wird außerdem eine Hierarchie erstellt, damit das Routing durch Hinzufügen eines anderen Routenparameters ausgeführt werden kann: `area` zu `controller` und `action`.</span><span class="sxs-lookup"><span data-stu-id="cab35-876">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="cab35-877">In diesem Abschnitt wird erläutert, wie Routing mit Bereichen interagiert. Weitere Informationen zur Verwendung von Bereichen mit Ansichten finden Sie unter [Bereiche](areas.md).</span><span class="sxs-lookup"><span data-stu-id="cab35-877">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="cab35-878">Im folgenden Beispiel wird MVC konfiguriert, sodass es die standardmäßige herkömmliche Route verwendet und eine *Bereichsroute* für einen Bereich namens `Blog`:</span><span class="sxs-lookup"><span data-stu-id="cab35-878">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="cab35-879">Beim Abgleich eines URL-Pfads wie `/Manage/Users/AddUser` erzeugt die erste Route die Routenwerte `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="cab35-879">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="cab35-880">Der `area`-Routenwert wird von einem Standardwert für `area` erzeugt. Die von `MapAreaRoute` erstellte Route entspricht in der Tat der folgenden:</span><span class="sxs-lookup"><span data-stu-id="cab35-880">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="cab35-881">`MapAreaRoute` erstellt mit einem Standardwert und einer Einschränkung für `area` sowie mit dem bereitgestellten Bereichsnamen (in diesem Fall `Blog`) eine Route.</span><span class="sxs-lookup"><span data-stu-id="cab35-881">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="cab35-882">Der Standardwert stellt sicher, dass die Route immer `{ area = Blog, ... }` erzeugt, die Einschränkung erfordert den Wert `{ area = Blog, ... }` für URL-Generierung.</span><span class="sxs-lookup"><span data-stu-id="cab35-882">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="cab35-883">Beim herkömmlichen Routing ist die Reihenfolge wichtig.</span><span class="sxs-lookup"><span data-stu-id="cab35-883">Conventional routing is order-dependent.</span></span> <span data-ttu-id="cab35-884">Routen mit Bereichen werden im Allgemeinen früher in der Routentabelle aufgeführt als die spezifischeren Routen ohne Bereich.</span><span class="sxs-lookup"><span data-stu-id="cab35-884">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="cab35-885">Die Routenwerte im oben genannten Beispiel werden der folgenden Aktion zugeordnet:</span><span class="sxs-lookup"><span data-stu-id="cab35-885">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="cab35-886">`AreaAttribute` kennzeichnet einen Controller als Teil eines Bereichs, z.B. des Bereichs `Blog`.</span><span class="sxs-lookup"><span data-stu-id="cab35-886">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="cab35-887">Controller ohne `[Area]`-Attribut gehören demnach zu keinem Bereich und stimmen **nicht** überein, wenn die `area`-Route wird vom Routing bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-887">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="cab35-888">Im folgenden Beispiel kann nur der erste aufgelistete Controller die Routenwerte `{ area = Blog, controller = Users, action = AddUser }` abgleichen.</span><span class="sxs-lookup"><span data-stu-id="cab35-888">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="cab35-889">Aus Gründen der Vollständigkeit wird hier der Namespace jedes Controllers dargestellt. Andernfalls gäbe es einen Namenskonflikt zwischen den Controllern, und ein Compilerfehler würde generiert.</span><span class="sxs-lookup"><span data-stu-id="cab35-889">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="cab35-890">Klassennamespaces haben keine Auswirkungen auf das MVC-Routing.</span><span class="sxs-lookup"><span data-stu-id="cab35-890">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="cab35-891">Die ersten beiden Controller gehören zu Bereichen und werden können nur abgleichen, wenn ihr jeweiliger Bereichsname vom `area`-Routenwert bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-891">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="cab35-892">Der dritte Controller gehört keinem Bereich an und kann nur abgleichen, wenn vom Routing kein Wert für `area` bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-892">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="cab35-893">Im Hinblick auf das Erkennen *keines Werts* hat die Abwesenheit des `area`-Werts dieselben Auswirkungen, wie wenn der Wert für `area` 0 (null) oder eine leere Zeichenfolge wäre.</span><span class="sxs-lookup"><span data-stu-id="cab35-893">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="cab35-894">Beim Ausführen einer Aktion innerhalb eines Bereichs ist der Routenwert für `area` als *Umgebungswert* für das Routing verfügbar und kann für die URL-Generierung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-894">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="cab35-895">Das bedeutet, dass Bereiche bei der URL-Generierung wie im folgenden Beispiel dargestellt standardmäßig *beständig* sind.</span><span class="sxs-lookup"><span data-stu-id="cab35-895">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="cab35-896">Verstehen von IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="cab35-896">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="cab35-897">Dieser Abschnitt enthält Details zu Framework-Mechanismen und dazu, wie eine auszuführende Aktion in MVC auswählt wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-897">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="cab35-898">Eine typische Anwendung benötigt kein benutzerdefiniertes `IActionConstraint`.</span><span class="sxs-lookup"><span data-stu-id="cab35-898">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="cab35-899">Sie haben `IActionConstraint` wahrscheinlich bereits verwendet, auch wenn Sie mit der Oberfläche noch nicht vertraut sind.</span><span class="sxs-lookup"><span data-stu-id="cab35-899">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="cab35-900">Das `[HttpGet]`-Attribut und ähnliche `[Http-VERB]`-Attribute implementieren `IActionConstraint`, um die Ausführung einer Aktionsmethode einzuschränken.</span><span class="sxs-lookup"><span data-stu-id="cab35-900">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="cab35-901">Unter Annahme der herkömmlichen Standardroute würde der URL-Pfad `/Products/Edit` die Werte `{ controller = Products, action = Edit }` erzeugen, die **beiden** hier gezeigten Aktionen entsprechen.</span><span class="sxs-lookup"><span data-stu-id="cab35-901">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="cab35-902">In `IActionConstraint`-Terminologie würde man sagen, dass es sich bei beiden genannten Aktionen um Kandidaten handelt, da beide den Routendaten entsprechen.</span><span class="sxs-lookup"><span data-stu-id="cab35-902">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="cab35-903">Wenn `HttpGetAttribute` ausgeführt wird, wird angegeben, dass *Edit()* mit *GET* übereinstimmt und mit keinem anderen HTTP-Verb.</span><span class="sxs-lookup"><span data-stu-id="cab35-903">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="cab35-904">Für die `Edit(...)`-Aktion wurde keine Einschränkungen definiert, und daher stimmt sie mit allen HTTP-Verben überein.</span><span class="sxs-lookup"><span data-stu-id="cab35-904">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="cab35-905">Wenn wir von `POST` ausgehen, stimmt nur `Edit(...)` überein.</span><span class="sxs-lookup"><span data-stu-id="cab35-905">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="cab35-906">Für `GET` können jedoch beide Aktionen übereinstimmen. Eine Aktion mit `IActionConstraint` wird einer Aktion ohne jedoch immer *vorgezogen*.</span><span class="sxs-lookup"><span data-stu-id="cab35-906">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="cab35-907">Da `Edit()` über `[HttpGet]` verfügt, gilt die Methode als spezifischer und wird ausgewählt, wenn beide Aktionen das Verb erkennen.</span><span class="sxs-lookup"><span data-stu-id="cab35-907">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="cab35-908">Im Prinzip ist `IActionConstraint` eine Form der *Überladung*. Anstatt jedoch Methoden mit demselben Namen zu überladen, führt es zu einer Überladung zwischen Aktionen, die dieselbe URL erkennen.</span><span class="sxs-lookup"><span data-stu-id="cab35-908">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="cab35-909">Beim Attributrouting wird auch `IActionConstraint` verwendet. Aus diesem Grund kann es zu Aktionen aus verschiedenen Controllern führen, die beide als Kandidaten gelten.</span><span class="sxs-lookup"><span data-stu-id="cab35-909">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="cab35-910">Implementieren von IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="cab35-910">Implementing IActionConstraint</span></span>

<span data-ttu-id="cab35-911">`IActionConstraint` lässt sich am einfachsten erstellen, indem Sie eine abgeleitete Klasse von `System.Attribute` erstellen und sie auf Ihren Aktionen und Controllern platzieren.</span><span class="sxs-lookup"><span data-stu-id="cab35-911">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="cab35-912">MVC erkennt automatisch jedes `IActionConstraint`, das als Attribut verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-912">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="cab35-913">Sie können mithilfe des Anwendungsmodells Einschränkungen anwenden. Dies ist wahrscheinlich die flexibelste Herangehensweise, da Sie über Metaprogrammierung festlegen können, wie die Einschränkungen angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="cab35-913">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="cab35-914">Im folgenden Beispiel wählt eine Einschränkung eine Aktion aus, die auf einem *Ländercode* aus den Routendaten basiert.</span><span class="sxs-lookup"><span data-stu-id="cab35-914">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="cab35-915">[Das vollständige Beispiel finden Sie auf GitHub.](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs)</span><span class="sxs-lookup"><span data-stu-id="cab35-915">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

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

<span data-ttu-id="cab35-916">Sie sind für die Implementierung der `Accept`-Methode und die Festlegung einer Reihenfolge (Order) verantwortlich, in der die Einschränkung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-916">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="cab35-917">In diesem Fall gibt die `Accept`-Methode `true` zurück, um zu kennzeichnen, dass die Aktion eine Übereinstimmung ist, wenn die `country`-Routenwerte übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="cab35-917">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="cab35-918">Dies unterscheidet sich von `RouteValueAttribute` dahingehend, dass ein Fallback auf eine Aktion ohne Attribute zulässig ist.</span><span class="sxs-lookup"><span data-stu-id="cab35-918">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="cab35-919">In dem Beispiel wird gezeigt, dass wenn Sie eine `en-US`-Aktion definieren, ein Ländercode wie `fr-FR` auf einen generischeren Controller zurückgreift, für den `[CountrySpecific(...)]` nicht angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="cab35-919">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="cab35-920">Die `Order`-Eigenschaft entscheidet, zu welcher *Phase* die Einschränkung gehört.</span><span class="sxs-lookup"><span data-stu-id="cab35-920">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="cab35-921">Aktionseinschränkungen werden auf Grundlage von `Order` in Gruppen ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="cab35-921">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="cab35-922">Beispiel: Alle vom Framework bereitgestellten HTTP-Methodenattribute verwenden denselben `Order`-Wert, sodass sie in derselben Phase ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="cab35-922">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="cab35-923">Es kann so viele Phasen geben, wie zur Implementierung der gewünschten Richtlinien notwendig.</span><span class="sxs-lookup"><span data-stu-id="cab35-923">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="cab35-924">Bei der Entscheidung über einen Wert für `Order` sollten Sie berücksichtigen, ob die Einschränkung vor HTTP-Methoden angewendet werden soll oder nicht.</span><span class="sxs-lookup"><span data-stu-id="cab35-924">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="cab35-925">Niedrigere Zahlen werden zuerst ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="cab35-925">Lower numbers run first.</span></span>

::: moniker-end

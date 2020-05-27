---
<span data-ttu-id="37602-101">Title: Autor: Beschreibung: ms. Author: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="37602-101">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="37602-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="37602-102">'Blazor'</span></span>
- <span data-ttu-id="37602-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="37602-103">'Identity'</span></span>
- <span data-ttu-id="37602-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="37602-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="37602-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="37602-105">'Razor'</span></span>
- <span data-ttu-id="37602-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="37602-106">'SignalR' uid:</span></span> 

---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="37602-107">Routing zu Controlleraktionen in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="37602-107">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="37602-108">Von [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="37602-108">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="37602-109">ASP.net Core Controller verwenden die Routing [Middleware](xref:fundamentals/middleware/index) , um die URLs eingehender Anforderungen abzugleichen und Sie [Aktionen](#action)zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="37602-109">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="37602-110">Routen Vorlagen:</span><span class="sxs-lookup"><span data-stu-id="37602-110">Routes templates:</span></span>

* <span data-ttu-id="37602-111">Werden in Startcode oder Attributen definiert.</span><span class="sxs-lookup"><span data-stu-id="37602-111">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="37602-112">Beschreiben Sie, wie URL-Pfade mit [Aktionen](#action)abgeglichen werden.</span><span class="sxs-lookup"><span data-stu-id="37602-112">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="37602-113">Werden verwendet, um URLs für Verknüpfungen zu generieren.</span><span class="sxs-lookup"><span data-stu-id="37602-113">Are used to generate URLs for links.</span></span> <span data-ttu-id="37602-114">Die generierten Verknüpfungen werden in der Regel in Antworten zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="37602-114">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="37602-115">Aktionen werden entweder [konventionell](#cr) oder [Attribut](#ar)weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="37602-115">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="37602-116">Durch das Platzieren einer Route auf dem Controller oder der [Aktion](#action) wird das Attribut geroutet.</span><span class="sxs-lookup"><span data-stu-id="37602-116">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="37602-117">Weitere Informationen finden Sie im Abschnitt [Gemischtes Routing](#routing-mixed-ref-label).</span><span class="sxs-lookup"><span data-stu-id="37602-117">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="37602-118">Dieses Dokument:</span><span class="sxs-lookup"><span data-stu-id="37602-118">This document:</span></span>

* <span data-ttu-id="37602-119">Erläutert die Interaktionen zwischen MVC und Routing:</span><span class="sxs-lookup"><span data-stu-id="37602-119">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="37602-120">Verwendung von Routing Features durch typische MVC-apps</span><span class="sxs-lookup"><span data-stu-id="37602-120">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="37602-121">Umfasst beides:</span><span class="sxs-lookup"><span data-stu-id="37602-121">Covers both:</span></span>
    * <span data-ttu-id="37602-122">[Konventionell Routing](#cr) , das in der Regel für Controller und Ansichten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="37602-122">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="37602-123">*Attribut Routing* , das mit Rest-APIs verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="37602-123">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="37602-124">Wenn Sie hauptsächlich an der Weiterleitung von Rest-APIs interessiert sind, springen Sie zum Abschnitt [Attribut Routing für Rest-APIs](#ar) .</span><span class="sxs-lookup"><span data-stu-id="37602-124">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="37602-125">Weitere Informationen finden Sie unter [Routing](xref:fundamentals/routing) für erweiterte Routing Details.</span><span class="sxs-lookup"><span data-stu-id="37602-125">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="37602-126">Bezieht sich auf das Standard Routing System, das in ASP.net Core 3,0 hinzugefügt wurde, dem sogenannten Endpunkt Routing.</span><span class="sxs-lookup"><span data-stu-id="37602-126">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="37602-127">Es ist möglich, Controller mit der vorherigen Version des Routings zu Kompatibilitätszwecken zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="37602-127">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="37602-128">Anweisungen hierzu finden Sie im [2.2-3.0-Migrations Handbuch](xref:migration/22-to-30) .</span><span class="sxs-lookup"><span data-stu-id="37602-128">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="37602-129">Das Referenzmaterial zum Legacy Routing System finden Sie in der [Version 2,2 dieses Dokuments](xref:mvc/controllers/routing?view=aspnetcore-2.2) .</span><span class="sxs-lookup"><span data-stu-id="37602-129">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="37602-130">Einrichten der herkömmlichen Route</span><span class="sxs-lookup"><span data-stu-id="37602-130">Set up conventional route</span></span>

<span data-ttu-id="37602-131">`Startup.Configure`in der Regel ist Code ähnlich dem folgenden, wenn [herkömmliches Routing](#crd)verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="37602-131">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="37602-132">Innerhalb des <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> Aufrufes wird zum Erstellen einer einzelnen Route verwendet.</span><span class="sxs-lookup"><span data-stu-id="37602-132">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> is used to create a single route.</span></span> <span data-ttu-id="37602-133">Die einzelne Route heißt `default` Route.</span><span class="sxs-lookup"><span data-stu-id="37602-133">The single route is named `default` route.</span></span> <span data-ttu-id="37602-134">Die meisten apps mit Controllern und Ansichten verwenden eine Routen Vorlage ähnlich der `default` Route.</span><span class="sxs-lookup"><span data-stu-id="37602-134">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="37602-135">Rest-APIs sollten [Attribut Routing](#ar)verwenden.</span><span class="sxs-lookup"><span data-stu-id="37602-135">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="37602-136">Die Routen Vorlage `"{controller=Home}/{action=Index}/{id?}"` :</span><span class="sxs-lookup"><span data-stu-id="37602-136">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="37602-137">Entspricht einem URL-Pfad wie`/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="37602-137">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="37602-138">Extrahiert die Routen Werte, `{ controller = Products, action = Details, id = 5 }` indem der Pfad mit einem Token versehen wird.</span><span class="sxs-lookup"><span data-stu-id="37602-138">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="37602-139">Die Extraktion von Routen Werten führt zu einer Abstimmung, wenn die APP einen Controller mit dem Namen `ProductsController` und eine `Details` Aktion aufweist:</span><span class="sxs-lookup"><span data-stu-id="37602-139">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* <span data-ttu-id="37602-140">`/Products/Details/5`Modell bindet den Wert von `id = 5` , um den- `id` Parameter auf festzulegen `5` .</span><span class="sxs-lookup"><span data-stu-id="37602-140">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="37602-141">Weitere Informationen finden Sie unter [Modell Bindung](xref:mvc/models/model-binding) .</span><span class="sxs-lookup"><span data-stu-id="37602-141">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="37602-142">`{controller=Home}`definiert `Home` als Standard `controller` .</span><span class="sxs-lookup"><span data-stu-id="37602-142">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="37602-143">`{action=Index}`definiert `Index` als Standard `action` .</span><span class="sxs-lookup"><span data-stu-id="37602-143">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="37602-144">Das `?` Zeichen in `{id?}` definiert `id` als optional.</span><span class="sxs-lookup"><span data-stu-id="37602-144">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="37602-145">Standardmäßige und optionale Routenparameter müssen nicht im URL-Pfad vorhanden sein, damit es eine Übereinstimmung gibt.</span><span class="sxs-lookup"><span data-stu-id="37602-145">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="37602-146">Eine ausführliche Beschreibung der Syntax der Routenvorlage finden Sie unter [Routenvorlagenreferenz](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="37602-146">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="37602-147">Entspricht dem URL-Pfad `/` .</span><span class="sxs-lookup"><span data-stu-id="37602-147">Matches the URL path `/`.</span></span>
* <span data-ttu-id="37602-148">Erzeugt die Routen Werte `{ controller = Home, action = Index }` .</span><span class="sxs-lookup"><span data-stu-id="37602-148">Produces the route values `{ controller = Home, action = Index }`.</span></span>

<span data-ttu-id="37602-149">Die Werte für `controller` und `action` verwenden die Standardwerte.</span><span class="sxs-lookup"><span data-stu-id="37602-149">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="37602-150">`id`erzeugt keinen Wert, da kein entsprechendes Segment im URL-Pfad vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="37602-150">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="37602-151">`/`stimmt nur überein, wenn eine `HomeController` and-Aktion vorhanden ist `Index` :</span><span class="sxs-lookup"><span data-stu-id="37602-151">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="37602-152">Mithilfe der vorangehenden Controller Definition und Routen Vorlage `HomeController.Index` wird die Aktion für die folgenden URL-Pfade ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="37602-152">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="37602-153">Der URL `/` -Pfad verwendet die Routen Vorlagen `Home` -Standard Controller und- `Index` Aktion.</span><span class="sxs-lookup"><span data-stu-id="37602-153">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="37602-154">Der URL-Pfad `/Home` verwendet die Standardaktion der Routen Vorlage `Index` .</span><span class="sxs-lookup"><span data-stu-id="37602-154">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="37602-155">Mit der Hilfsmethode <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span><span class="sxs-lookup"><span data-stu-id="37602-155">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="37602-156">Arbeits</span><span class="sxs-lookup"><span data-stu-id="37602-156">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> <span data-ttu-id="37602-157">Das Routing wird mithilfe der <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> Middleware und konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="37602-157">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> middleware.</span></span> <span data-ttu-id="37602-158">So verwenden Sie Controller:</span><span class="sxs-lookup"><span data-stu-id="37602-158">To use controllers:</span></span>
>
> * <span data-ttu-id="37602-159">Ruft <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> in `UseEndpoints` auf, um [Attribut](#ar) Routing Controller zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="37602-159">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
> * <span data-ttu-id="37602-160"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>Wird oder aufgerufen <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> , um [konventionell weitergeleitete](#cr) Controller zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="37602-160">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>, to map [conventionally routed](#cr) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="37602-161">Herkömmliches Routing</span><span class="sxs-lookup"><span data-stu-id="37602-161">Conventional routing</span></span>

<span data-ttu-id="37602-162">Herkömmliches Routing wird mit Controllern und Ansichten verwendet.</span><span class="sxs-lookup"><span data-stu-id="37602-162">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="37602-163">Die `default`-Route:</span><span class="sxs-lookup"><span data-stu-id="37602-163">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="37602-164">ist ein Beispiel für *herkömmliches Routing*.</span><span class="sxs-lookup"><span data-stu-id="37602-164">is an example of a *conventional routing*.</span></span> <span data-ttu-id="37602-165">Es wird als *herkömmliches Routing* bezeichnet, da es eine *Konvention* für URL-Pfade festlegt:</span><span class="sxs-lookup"><span data-stu-id="37602-165">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="37602-166">Das erste Pfad Segment, `{controller=Home}` , wird dem Controller Namen zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="37602-166">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="37602-167">Das zweite Segment, `{action=Index}` , wird dem [Aktions](#action) Namen zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="37602-167">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="37602-168">Das dritte Segment `{id?}` wird für eine optionale verwendet `id` .</span><span class="sxs-lookup"><span data-stu-id="37602-168">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="37602-169">Die `?` in `{id?}` macht Sie optional.</span><span class="sxs-lookup"><span data-stu-id="37602-169">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="37602-170">`id`wird verwendet, um einer Modell Entität zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="37602-170">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="37602-171">Verwenden Sie diese `default` Route, den URL-Pfad:</span><span class="sxs-lookup"><span data-stu-id="37602-171">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="37602-172">`/Products/List`wird der- `ProductsController.List` Aktion zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="37602-172">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="37602-173">`/Blog/Article/17`wird zugeordnet, und in der `BlogController.Article` Regel bindet Model den- `id` Parameter an 17.</span><span class="sxs-lookup"><span data-stu-id="37602-173">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="37602-174">Diese Zuordnung:</span><span class="sxs-lookup"><span data-stu-id="37602-174">This mapping:</span></span>

* <span data-ttu-id="37602-175">Basiert **nur**auf den Controller-und [Aktions](#action) Namen.</span><span class="sxs-lookup"><span data-stu-id="37602-175">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="37602-176">Basiert nicht auf Namespaces, Quelldatei Speicherorten oder Methoden Parametern.</span><span class="sxs-lookup"><span data-stu-id="37602-176">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="37602-177">Die Verwendung von herkömmlichem Routing mit der Standardroute ermöglicht das Erstellen der APP, ohne für jede Aktion ein neues URL-Muster zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="37602-177">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="37602-178">Für eine APP mit [CRUD-](https://wikipedia.org/wiki/Create,_read,_update_and_delete) Stil Aktionen mit Konsistenz für die URLs über Controller hinweg:</span><span class="sxs-lookup"><span data-stu-id="37602-178">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="37602-179">Erleichtert das Vereinfachen des Codes.</span><span class="sxs-lookup"><span data-stu-id="37602-179">Helps simplify the code.</span></span>
* <span data-ttu-id="37602-180">Macht die Benutzeroberfläche berechenbarer.</span><span class="sxs-lookup"><span data-stu-id="37602-180">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="37602-181">Der `id` im vorangehenden Code wird von der Routen Vorlage als optional definiert.</span><span class="sxs-lookup"><span data-stu-id="37602-181">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="37602-182">Aktionen können ausgeführt werden, ohne dass die optionale ID als Teil der URL bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="37602-182">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="37602-183">Im Allgemeinen gilt, wenn in `id` der URL weggelassen wird:</span><span class="sxs-lookup"><span data-stu-id="37602-183">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="37602-184">`id`wird `0` von der Modell Bindung auf festgelegt.</span><span class="sxs-lookup"><span data-stu-id="37602-184">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="37602-185">Es wurde keine Entität in der Daten Bank Übereinstimmung gefunden `id == 0` .</span><span class="sxs-lookup"><span data-stu-id="37602-185">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="37602-186">Das [Attribut Routing](#ar) ermöglicht eine differenzierte Steuerung, um die erforderliche ID für einige Aktionen und nicht für andere zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="37602-186">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="37602-187">Gemäß der Konvention enthält die Dokumentation optionale Parameter, z `id` . b., wenn Sie wahrscheinlich in der richtigen Verwendung angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="37602-187">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="37602-188">Für die meisten Apps sollte eine grundlegendes und beschreibendes Routingschema ausgewählt werden, um lesbare und aussagekräftige URLs zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="37602-188">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="37602-189">Für die konventionelle Standardroute `{controller=Home}/{action=Index}/{id?}` gilt:</span><span class="sxs-lookup"><span data-stu-id="37602-189">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="37602-190">Sie unterstützt ein grundlegendes und beschreibendes Routingschema.</span><span class="sxs-lookup"><span data-stu-id="37602-190">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="37602-191">Sie stellt einen nützlichen Startpunkt für benutzeroberflächenbasierte Apps dar.</span><span class="sxs-lookup"><span data-stu-id="37602-191">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="37602-192">Ist die einzige Routen Vorlage, die für viele Web-UI-apps benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="37602-192">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="37602-193">Bei größeren Webanwendungs-Apps ist eine weitere Route, die [Bereiche](#areas) verwendet, wenn diese häufig benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="37602-193">For larger web UI apps, another route using [Areas](#areas) if frequently all that's needed.</span></span>

<span data-ttu-id="37602-194"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>und <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span><span class="sxs-lookup"><span data-stu-id="37602-194"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span></span>

* <span data-ttu-id="37602-195">Weisen Sie Ihren Endpunkten automatisch einen **Bestell** Wert basierend auf der Reihenfolge zu, in der Sie aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="37602-195">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="37602-196">Endpunktrouting in ASP.NET Core 3.0 und höher:</span><span class="sxs-lookup"><span data-stu-id="37602-196">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="37602-197">Weist kein Konzept für Routen auf.</span><span class="sxs-lookup"><span data-stu-id="37602-197">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="37602-198">Bietet keine Bestell Garantien für die Ausführung der Erweiterbarkeit. alle Endpunkte werden gleichzeitig verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="37602-198">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="37602-199">Wenn Sie die [Protokollierung](xref:fundamentals/logging/index) aktivieren, erfahren Sie, wie die integrierten Routingimplementierungen (z.B. <xref:Microsoft.AspNetCore.Routing.Route>) Zuordnungen für Anforderungen ermitteln.</span><span class="sxs-lookup"><span data-stu-id="37602-199">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="37602-200">Das [Attribut Routing](#ar) wird weiter unten in diesem Dokument erläutert.</span><span class="sxs-lookup"><span data-stu-id="37602-200">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="37602-201">Mehrere herkömmliche Routen</span><span class="sxs-lookup"><span data-stu-id="37602-201">Multiple conventional routes</span></span>

<span data-ttu-id="37602-202">Mehrere [konventionelle Routen](#cr) können in hinzugefügt werden `UseEndpoints` , indem weitere Aufrufe von und hinzugefügt werden <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> .</span><span class="sxs-lookup"><span data-stu-id="37602-202">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span></span> <span data-ttu-id="37602-203">Dies ermöglicht das Definieren mehrerer Konventionen oder das Hinzufügen herkömmlicher Routen, die einer bestimmten [Aktion](#action)zugeordnet sind, wie z. b.:</span><span class="sxs-lookup"><span data-stu-id="37602-203">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="37602-204">Die `blog` Route im vorangehenden Code ist eine **dedizierte konventionelle Route**.</span><span class="sxs-lookup"><span data-stu-id="37602-204">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="37602-205">Dies wird als dedizierte konventionelle Route bezeichnet:</span><span class="sxs-lookup"><span data-stu-id="37602-205">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="37602-206">Es verwendet [herkömmliches Routing](#cr).</span><span class="sxs-lookup"><span data-stu-id="37602-206">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="37602-207">Es ist für eine bestimmte [Aktion](#action)vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="37602-207">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="37602-208">Da `controller` und `action` nicht in der Routen Vorlage `"blog/{*article}"` als Parameter angezeigt werden:</span><span class="sxs-lookup"><span data-stu-id="37602-208">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="37602-209">Sie können nur die Standardwerte aufweisen `{ controller = "Blog", action = "Article" }` .</span><span class="sxs-lookup"><span data-stu-id="37602-209">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="37602-210">Diese Route wird immer der Aktion zugeordnet `BlogController.Article` .</span><span class="sxs-lookup"><span data-stu-id="37602-210">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="37602-211">`/Blog`, `/Blog/Article` und `/Blog/{any-string}` sind die einzigen URL-Pfade, die der Blog Route entsprechen.</span><span class="sxs-lookup"><span data-stu-id="37602-211">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="37602-212">Das vorherige Beispiel:</span><span class="sxs-lookup"><span data-stu-id="37602-212">The preceding example:</span></span>

* <span data-ttu-id="37602-213">`blog`Route hat eine höhere Priorität als die Route, `default` da Sie zuerst hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="37602-213">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="37602-214">Ist ein Beispiel für das Routing im [Slug](https://developer.mozilla.org/docs/Glossary/Slug) -Format, in dem normalerweise ein Artikelname als Teil der URL vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="37602-214">Is and example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="37602-215">In ASP.net Core 3,0 und höher ist das Routing nicht:</span><span class="sxs-lookup"><span data-stu-id="37602-215">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="37602-216">Definieren Sie ein Konzept, das als *Route*bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="37602-216">Define a concept called a *route*.</span></span> <span data-ttu-id="37602-217">`UseRouting` fügt der Middlewarepipeline einen Routenabgleich hinzu.</span><span class="sxs-lookup"><span data-stu-id="37602-217">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="37602-218">Die `UseRouting` Middleware prüft den Satz von Endpunkten, die in der APP definiert sind, und wählt basierend auf der Anforderung die beste Endpunkt Übereinstimmung aus.</span><span class="sxs-lookup"><span data-stu-id="37602-218">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="37602-219">Stellen Sie Garantien für die Ausführungsreihenfolge der Erweiterbarkeit wie <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> oder bereit <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> .</span><span class="sxs-lookup"><span data-stu-id="37602-219">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="37602-220">Referenzmaterial zum Routing finden Sie unter [Routing](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="37602-220">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="37602-221">Konventionelle Routing Reihenfolge</span><span class="sxs-lookup"><span data-stu-id="37602-221">Conventional routing order</span></span>

<span data-ttu-id="37602-222">Herkömmliches Routing stimmt nur mit einer Kombination aus Aktion und Controller überein, die von der APP definiert werden.</span><span class="sxs-lookup"><span data-stu-id="37602-222">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="37602-223">Dies dient zur Vereinfachung der Fälle, in denen herkömmliche Routen sich überlappen.</span><span class="sxs-lookup"><span data-stu-id="37602-223">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="37602-224">Durch das Hinzufügen von Routen mithilfe von <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> , <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*> und wird <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> ihren Endpunkten automatisch ein Bestellwert entsprechend der Reihenfolge zugewiesen, in der Sie aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="37602-224">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="37602-225">Übereinstimmungen aus einer zuvor angezeigten Route haben eine höhere Priorität.</span><span class="sxs-lookup"><span data-stu-id="37602-225">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="37602-226">Beim herkömmlichen Routing ist die Reihenfolge wichtig.</span><span class="sxs-lookup"><span data-stu-id="37602-226">Conventional routing is order-dependent.</span></span> <span data-ttu-id="37602-227">Im Allgemeinen sollten Routen mit Bereichen früher platziert werden, da Sie spezifischer als Routen ohne Bereich sind.</span><span class="sxs-lookup"><span data-stu-id="37602-227">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="37602-228">[Dedizierte herkömmliche Routen](#dcr) mit "Catch-All"-Routen Parametern wie `{*article}` können eine Route zu [gierig](xref:fundamentals/routing#greedy)machen, was bedeutet, dass Sie mit den URLs übereinstimmt, die Sie mit anderen Routen vergleichen wollten.</span><span class="sxs-lookup"><span data-stu-id="37602-228">[Dedicated conventional routes](#dcr) with catch-all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="37602-229">Platzieren Sie die gierigen Routen später in der Routing Tabelle, um gierige Übereinstimmungen zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="37602-229">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

[!INCLUDE[](~/includes/catchall.md)]

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="37602-230">Auflösen mehrdeutiger Aktionen</span><span class="sxs-lookup"><span data-stu-id="37602-230">Resolving ambiguous actions</span></span>

<span data-ttu-id="37602-231">Wenn zwei Endpunkte über das Routing abgleichen, muss das Routing einen der folgenden Schritte ausführen:</span><span class="sxs-lookup"><span data-stu-id="37602-231">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="37602-232">Wählen Sie den besten Kandidaten aus.</span><span class="sxs-lookup"><span data-stu-id="37602-232">Choose the best candidate.</span></span>
* <span data-ttu-id="37602-233">Löst eine Ausnahme aus.</span><span class="sxs-lookup"><span data-stu-id="37602-233">Throw an exception.</span></span>

<span data-ttu-id="37602-234">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="37602-234">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="37602-235">Der vorangehende Controller definiert zwei Aktionen, die mit identisch sind:</span><span class="sxs-lookup"><span data-stu-id="37602-235">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="37602-236">Der URL-Pfad.`/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="37602-236">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="37602-237">Weiterleiten von Daten `{ controller = Products33, action = Edit, id = 17 }` .</span><span class="sxs-lookup"><span data-stu-id="37602-237">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="37602-238">Dies ist ein typisches Muster für MVC-Controller:</span><span class="sxs-lookup"><span data-stu-id="37602-238">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="37602-239">`Edit(int)`zeigt ein Formular zum Bearbeiten eines Produkts an.</span><span class="sxs-lookup"><span data-stu-id="37602-239">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="37602-240">`Edit(int, Product)`verarbeitet das gepostete Formular.</span><span class="sxs-lookup"><span data-stu-id="37602-240">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="37602-241">So beheben Sie die korrekte Route:</span><span class="sxs-lookup"><span data-stu-id="37602-241">To resolve the correct route:</span></span>

* <span data-ttu-id="37602-242">`Edit(int, Product)`wird ausgewählt, wenn die Anforderung ein HTTP ist `POST` .</span><span class="sxs-lookup"><span data-stu-id="37602-242">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="37602-243">`Edit(int)`wird ausgewählt, wenn das [http-Verb](#verb) etwas anderes ist.</span><span class="sxs-lookup"><span data-stu-id="37602-243">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="37602-244">`Edit(int)`wird im Allgemeinen über aufgerufen `GET` .</span><span class="sxs-lookup"><span data-stu-id="37602-244">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="37602-245">Die <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , `[HttpPost]` , wird für das Routing bereitgestellt, sodass Sie basierend auf der HTTP-Methode der Anforderung ausgewählt werden kann.</span><span class="sxs-lookup"><span data-stu-id="37602-245">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="37602-246">Der `HttpPostAttribute` ist `Edit(int, Product)` besser geeignet als `Edit(int)` .</span><span class="sxs-lookup"><span data-stu-id="37602-246">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="37602-247">Es ist wichtig, die Rolle von Attributen wie zu verstehen `HttpPostAttribute` .</span><span class="sxs-lookup"><span data-stu-id="37602-247">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="37602-248">Ähnliche Attribute werden für andere [HTTP-Verben](#verb)definiert.</span><span class="sxs-lookup"><span data-stu-id="37602-248">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="37602-249">Bei [herkömmlichem Routing](#cr)wird bei Aktionen häufig derselbe Aktionsname verwendet, wenn Sie Teil eines anshow Formulars sind, Formular Workflow senden.</span><span class="sxs-lookup"><span data-stu-id="37602-249">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="37602-250">Informationen hierzu finden Sie beispielsweise [unter Überprüfen der beiden Bearbeitungs Aktionsmethoden](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span><span class="sxs-lookup"><span data-stu-id="37602-250">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="37602-251">Wenn das Routing keinen besten Kandidaten auswählen kann, <xref:System.Reflection.AmbiguousMatchException> wird eine ausgelöst, die mehrere übereinstimmende Endpunkte auflistet.</span><span class="sxs-lookup"><span data-stu-id="37602-251">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="37602-252">Herkömmliche Routennamen</span><span class="sxs-lookup"><span data-stu-id="37602-252">Conventional route names</span></span>

<span data-ttu-id="37602-253">Die Zeichen `"blog"` `"default"` folgen und in den folgenden Beispielen sind konventionelle Routennamen:</span><span class="sxs-lookup"><span data-stu-id="37602-253">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="37602-254">Mit den Routennamen wird der Route ein logischer Name zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="37602-254">The route names give the route a logical name.</span></span> <span data-ttu-id="37602-255">Die benannte Route kann für die URL-Generierung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="37602-255">The named route can be used for URL generation.</span></span> <span data-ttu-id="37602-256">Die Verwendung einer benannten Route vereinfacht die URL-Erstellung, wenn die Reihenfolge der Routen die URL-Generierung erschweren könnte.</span><span class="sxs-lookup"><span data-stu-id="37602-256">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="37602-257">Routennamen müssen eine eindeutige Anwendungs weite sein.</span><span class="sxs-lookup"><span data-stu-id="37602-257">Route names must be unique application wide.</span></span>

<span data-ttu-id="37602-258">Routennamen:</span><span class="sxs-lookup"><span data-stu-id="37602-258">Route names:</span></span>

* <span data-ttu-id="37602-259">Hat keine Auswirkung auf die URL-Übereinstimmung oder-Verarbeitung von Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="37602-259">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="37602-260">Werden nur für die URL-Generierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="37602-260">Are used only for URL generation.</span></span>

<span data-ttu-id="37602-261">Das Routing Namenskonzept wird als " [iendpointnamemetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata)" dargestellt.</span><span class="sxs-lookup"><span data-stu-id="37602-261">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="37602-262">Die Begriffe **Routen Name** und **Endpunkt Name**:</span><span class="sxs-lookup"><span data-stu-id="37602-262">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="37602-263">Sind austauschbar.</span><span class="sxs-lookup"><span data-stu-id="37602-263">Are interchangeable.</span></span>
* <span data-ttu-id="37602-264">Welche Informationen in der Dokumentation und im Code verwendet werden, hängt von der API ab, die beschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="37602-264">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="37602-265">Attribut Routing für Rest-APIs</span><span class="sxs-lookup"><span data-stu-id="37602-265">Attribute routing for REST APIs</span></span>

<span data-ttu-id="37602-266">Rest-APIs sollten das Attribut Routing verwenden, um die Funktionalität der App als Satz von Ressourcen zu modellieren, in denen Vorgänge durch [HTTP-Verben](#verb)dargestellt werden.</span><span class="sxs-lookup"><span data-stu-id="37602-266">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="37602-267">Beim Attributrouting werden Aktionen mithilfe von Attributen direkt Routenvorlagen zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="37602-267">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="37602-268">Der folgende `StartUp.Configure` Code ist für eine Rest-API typisch und wird im nächsten Beispiel verwendet:</span><span class="sxs-lookup"><span data-stu-id="37602-268">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

<span data-ttu-id="37602-269">Im vorangehenden Code <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> wird innerhalb von aufgerufen, `UseEndpoints` um Attribut Routing Controller zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="37602-269">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="37602-270">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="37602-270">In the following example:</span></span>

* <span data-ttu-id="37602-271">Die vorangehende `Configure` Methode wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="37602-271">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="37602-272">`HomeController`entspricht einem Satz von URLs, die mit der herkömmlichen Standardroute `{controller=Home}/{action=Index}/{id?}` übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="37602-272">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="37602-273">Die `HomeController.Index` Aktion wird für alle URL-Pfade `/` , `/Home` , oder ausgeführt `/Home/Index` `/Home/Index/3` .</span><span class="sxs-lookup"><span data-stu-id="37602-273">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="37602-274">In diesem Beispiel wird ein wichtiger Programmier Unterschied zwischen Attribut Routing und [herkömmlichem Routing](#cr)hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="37602-274">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="37602-275">Das Attribut Routing erfordert mehr Eingaben, um eine Route anzugeben.</span><span class="sxs-lookup"><span data-stu-id="37602-275">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="37602-276">Bei der herkömmlichen Standardroute werden Routen genauer behandelt.</span><span class="sxs-lookup"><span data-stu-id="37602-276">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="37602-277">Das Attribut Routing ermöglicht und erfordert jedoch eine genaue Kontrolle darüber, welche Routen Vorlagen auf die einzelnen [Aktionen](#action)angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="37602-277">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="37602-278">Im folgenden Code wird Folgendes ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="37602-278">In the following code:</span></span>

* <span data-ttu-id="37602-279">Der Controller Name und die Aktions Namen spielen **keine** Rolle, in welcher Aktion abgeglichen wird.</span><span class="sxs-lookup"><span data-stu-id="37602-279">The controller name and action names play **no** role in which action is matched.</span></span>
* <span data-ttu-id="37602-280">Entspricht denselben URLs wie im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="37602-280">Matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="37602-281">Der folgende Code verwendet die Tokenersetzung für `action` und `controller` :</span><span class="sxs-lookup"><span data-stu-id="37602-281">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="37602-282">Der folgende Code gilt `[Route("[controller]/[action]")]` für den Controller:</span><span class="sxs-lookup"><span data-stu-id="37602-282">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="37602-283">Im vorangehenden Code müssen die `Index` Methoden Vorlagen `/` oder `~/` den Routen Vorlagen vorangestellt werden.</span><span class="sxs-lookup"><span data-stu-id="37602-283">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="37602-284">Routenvorlagen, die auf eine Aktion angewendet werden, die mit einem `/` oder `~/` beginnen, können nicht mit Routenvorlagen kombiniert werden, die auf den Controller angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="37602-284">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="37602-285">Weitere Informationen zur Routen Vorlagen Auswahl finden Sie unter [Routen Vorlagen Rangfolge](xref:fundamentals/routing#rtp) .</span><span class="sxs-lookup"><span data-stu-id="37602-285">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="37602-286">Reservierte Routingnamen</span><span class="sxs-lookup"><span data-stu-id="37602-286">Reserved routing names</span></span>

<span data-ttu-id="37602-287">Die folgenden Schlüsselwörter sind reservierte Routen Parameternamen bei der Verwendung von Controllern oder Razor Seiten:</span><span class="sxs-lookup"><span data-stu-id="37602-287">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="37602-288">Die Verwendung `page` von als Routen Parameter mit Attribut Routing ist ein häufiger Fehler.</span><span class="sxs-lookup"><span data-stu-id="37602-288">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="37602-289">Dies führt zu inkonsistentem und verwirdem Verhalten bei der URL-Generierung.</span><span class="sxs-lookup"><span data-stu-id="37602-289">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="37602-290">Die speziellen Parameternamen werden von der URL-Generierung verwendet, um zu bestimmen, ob ein URL-Generierungs Vorgang auf eine Razor Seite oder auf einen Controller verweist.</span><span class="sxs-lookup"><span data-stu-id="37602-290">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="37602-291">HTTP-Verb Vorlagen</span><span class="sxs-lookup"><span data-stu-id="37602-291">HTTP verb templates</span></span>

<span data-ttu-id="37602-292">ASP.net Core verfügt über die folgenden HTTP-Verb Vorlagen:</span><span class="sxs-lookup"><span data-stu-id="37602-292">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="37602-293">[HttpGet](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="37602-293">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="37602-294">[HttpPost](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="37602-294">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="37602-295">[HttpPut](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="37602-295">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="37602-296">[HttpDelete](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="37602-296">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="37602-297">[[Httphead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="37602-297">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="37602-298">[[Httppatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="37602-298">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="37602-299">Routenvorlagen</span><span class="sxs-lookup"><span data-stu-id="37602-299">Route templates</span></span>

<span data-ttu-id="37602-300">ASP.net Core verfügt über die folgenden Routen Vorlagen:</span><span class="sxs-lookup"><span data-stu-id="37602-300">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="37602-301">Alle [http-Verb Vorlagen](#verb) sind Routen Vorlagen.</span><span class="sxs-lookup"><span data-stu-id="37602-301">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="37602-302">[Seeweg](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="37602-302">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="37602-303">Attribut Routing mit HTTP-Verb Attributen</span><span class="sxs-lookup"><span data-stu-id="37602-303">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="37602-304">Beachten Sie den folgenden Controller:</span><span class="sxs-lookup"><span data-stu-id="37602-304">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="37602-305">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="37602-305">In the preceding code:</span></span>

* <span data-ttu-id="37602-306">Jede Aktion enthält das- `[HttpGet]` Attribut, das die Übereinstimmung von HTTP-GET-Anforderungen einschränkt.</span><span class="sxs-lookup"><span data-stu-id="37602-306">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="37602-307">Die `GetProduct` Aktion enthält die `"{id}"` Vorlage und `id` wird daher an die `"api/[controller]"` Vorlage auf dem Controller angehängt.</span><span class="sxs-lookup"><span data-stu-id="37602-307">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="37602-308">Die Methoden Vorlage ist `"api/[controller]/"{id}""` .</span><span class="sxs-lookup"><span data-stu-id="37602-308">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="37602-309">Daher entspricht diese Aktion nur Get-Anforderungen von für das Formular `/api/test2/xyz` , `/api/test2/123` , `/api/test2/{any string}` usw.</span><span class="sxs-lookup"><span data-stu-id="37602-309">Therefore this action only matches GET requests of for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="37602-310">Die `GetIntProduct` Aktion enthält die `"int/{id:int}")` Vorlage.</span><span class="sxs-lookup"><span data-stu-id="37602-310">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="37602-311">Der `:int` Teil der Vorlage schränkt die `id` Routen Werte auf Zeichen folgen ein, die in eine ganze Zahl konvertiert werden können.</span><span class="sxs-lookup"><span data-stu-id="37602-311">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="37602-312">Eine GET-Anforderung für `/api/test2/int/abc` :</span><span class="sxs-lookup"><span data-stu-id="37602-312">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="37602-313">Entspricht dieser Aktion nicht.</span><span class="sxs-lookup"><span data-stu-id="37602-313">Doesn't match this action.</span></span>
  * <span data-ttu-id="37602-314">Gibt den Fehler " [404 nicht gefunden](https://developer.mozilla.org/docs/Web/HTTP/Status/404) " zurück.</span><span class="sxs-lookup"><span data-stu-id="37602-314">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="37602-315">Die `GetInt2Product` Aktion enthält `{id}` in der Vorlage, schränkt jedoch nicht `id` auf Werte ein, die in eine ganze Zahl konvertiert werden können.</span><span class="sxs-lookup"><span data-stu-id="37602-315">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="37602-316">Eine GET-Anforderung für `/api/test2/int2/abc` :</span><span class="sxs-lookup"><span data-stu-id="37602-316">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="37602-317">Entspricht dieser Route.</span><span class="sxs-lookup"><span data-stu-id="37602-317">Matches this route.</span></span>
  * <span data-ttu-id="37602-318">Die Modell Bindung kann nicht `abc` in eine ganze Zahl konvertiert werden.</span><span class="sxs-lookup"><span data-stu-id="37602-318">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="37602-319">Der- `id` Parameter der-Methode ist Integer.</span><span class="sxs-lookup"><span data-stu-id="37602-319">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="37602-320">Gibt eine ungültige [400-Anforderung](https://developer.mozilla.org/docs/Web/HTTP/Status/400) zurück, da die Modell Bindung nicht in eine ganze Zahl konvertiert werden konnte `abc` .</span><span class="sxs-lookup"><span data-stu-id="37602-320">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="37602-321">Beim Attribut Routing können <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> Attribute wie <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute> und verwendet werden <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute> .</span><span class="sxs-lookup"><span data-stu-id="37602-321">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="37602-322">Alle HTTP- [Verb](#verb) Attribute akzeptieren eine Routen Vorlage.</span><span class="sxs-lookup"><span data-stu-id="37602-322">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="37602-323">Das folgende Beispiel zeigt zwei Aktionen, die derselben Routen Vorlage entsprechen:</span><span class="sxs-lookup"><span data-stu-id="37602-323">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="37602-324">Verwenden des URL-Pfads `/products3` :</span><span class="sxs-lookup"><span data-stu-id="37602-324">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="37602-325">Die `MyProductsController.ListProducts` Aktion wird ausgeführt, wenn das [http-Verb](#verb) den Wert hat `GET` .</span><span class="sxs-lookup"><span data-stu-id="37602-325">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="37602-326">Die `MyProductsController.CreateProduct` Aktion wird ausgeführt, wenn das [http-Verb](#verb) den Wert hat `POST` .</span><span class="sxs-lookup"><span data-stu-id="37602-326">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="37602-327">Beim Aufbau einer Rest-API müssen Sie `[Route(...)]` nur selten eine Aktionsmethode verwenden, da die Aktion alle HTTP-Methoden akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="37602-327">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="37602-328">Es ist besser, das spezifischere [http-Verb-Attribut](#verb) zu verwenden, um genau zu erfahren, was Ihre API unterstützt.</span><span class="sxs-lookup"><span data-stu-id="37602-328">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="37602-329">REST-API-Clients sollten wissen, welche Pfade und HTTP-Verben bestimmten logischen Operationen entsprechen.</span><span class="sxs-lookup"><span data-stu-id="37602-329">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="37602-330">Rest-APIs sollten das Attribut Routing verwenden, um die Funktionalität der App als Satz von Ressourcen zu modellieren, in denen Vorgänge durch HTTP-Verben dargestellt werden.</span><span class="sxs-lookup"><span data-stu-id="37602-330">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="37602-331">Dies bedeutet, dass viele Vorgänge, z. b. Get und Post, für dieselbe logische Ressource dieselbe URL verwenden.</span><span class="sxs-lookup"><span data-stu-id="37602-331">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="37602-332">Das Attributrouting bietet eine Ebene der Steuerung, die für einen sorgfältigen Entwurf des öffentlichen Endpunktlayouts einer API erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="37602-332">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="37602-333">Da eine Attributroute für eine bestimmte Aktion gilt, ist es einfach, Parameter als Teil der Routenvorlagendefinition erforderlich festzulegen.</span><span class="sxs-lookup"><span data-stu-id="37602-333">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="37602-334">Im folgenden Beispiel `id` ist als Teil des URL-Pfads erforderlich:</span><span class="sxs-lookup"><span data-stu-id="37602-334">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="37602-335">Die `Products2ApiController.GetProduct(int)` Aktion:</span><span class="sxs-lookup"><span data-stu-id="37602-335">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="37602-336">Wird mit URL-Pfad wie`/products2/3`</span><span class="sxs-lookup"><span data-stu-id="37602-336">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="37602-337">Wird nicht mit dem URL-Pfad ausgeführt `/products2` .</span><span class="sxs-lookup"><span data-stu-id="37602-337">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="37602-338">Das [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)-Attribut ermöglicht es einer Aktion, die unterstützten Anforderungsinhaltstypen einzuschränken.</span><span class="sxs-lookup"><span data-stu-id="37602-338">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="37602-339">Weitere Informationen finden Sie [unter Definieren unterstützter Anforderungs Inhaltstypen mit dem Attribut "Verbrauchs](xref:web-api/index#consumes)".</span><span class="sxs-lookup"><span data-stu-id="37602-339">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="37602-340">Eine vollständige Beschreibung und Routenvorlagen und dazugehörige Optionen finden Sie unter [Routing in ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="37602-340">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="37602-341">Weitere Informationen zu `[ApiController]` finden Sie unter [apicontroller-Attribut](xref:web-api/index##apicontroller-attribute).</span><span class="sxs-lookup"><span data-stu-id="37602-341">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="37602-342">Routenname</span><span class="sxs-lookup"><span data-stu-id="37602-342">Route name</span></span>

<span data-ttu-id="37602-343">Im folgenden Code wird ein Routenname von `Products_List` definiert:</span><span class="sxs-lookup"><span data-stu-id="37602-343">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="37602-344">Routennamen können verwendet werden, um basierend auf einer bestimmten Route eine URL zu generieren.</span><span class="sxs-lookup"><span data-stu-id="37602-344">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="37602-345">Routennamen:</span><span class="sxs-lookup"><span data-stu-id="37602-345">Route names:</span></span>

* <span data-ttu-id="37602-346">Hat keine Auswirkung auf das URL-Vergleichs Verhalten des Routings.</span><span class="sxs-lookup"><span data-stu-id="37602-346">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="37602-347">Werden nur für die URL-Generierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="37602-347">Are only used for URL generation.</span></span>

<span data-ttu-id="37602-348">Routennamen müssen anwendungsweit eindeutig sein.</span><span class="sxs-lookup"><span data-stu-id="37602-348">Route names must be unique application-wide.</span></span>

<span data-ttu-id="37602-349">Vergleichen Sie den vorangehenden Code mit der herkömmlichen Standardroute, die den `id` Parameter als optional definiert ( `{id?}` ).</span><span class="sxs-lookup"><span data-stu-id="37602-349">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="37602-350">Die Möglichkeit, APIs genau anzugeben, bietet Vorteile, z. b. das zulassen `/products` und verteilen `/products/5` an verschiedene Aktionen.</span><span class="sxs-lookup"><span data-stu-id="37602-350">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="37602-351">Kombinieren von Attribut Routen</span><span class="sxs-lookup"><span data-stu-id="37602-351">Combining attribute routes</span></span>

<span data-ttu-id="37602-352">Um Attributrouting weniger repetitiv zu gestalten, werden Routenattribute auf dem Controller mit Routenattributen auf den einzelnen Aktionen kombiniert.</span><span class="sxs-lookup"><span data-stu-id="37602-352">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="37602-353">Alle auf dem Controller definierten Routenvorlagen werden den Routenvorlagen auf den Aktionen vorangestellt.</span><span class="sxs-lookup"><span data-stu-id="37602-353">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="37602-354">Wenn Routenattribute auf dem Controller platziert werden, verwenden **alle** Aktionen im Controller Attributrouting.</span><span class="sxs-lookup"><span data-stu-id="37602-354">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="37602-355">Im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="37602-355">In the preceding example:</span></span>

* <span data-ttu-id="37602-356">Der URL-Pfad `/products` kann Stimmen.`ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="37602-356">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="37602-357">Der URL-Pfad kann abgeglichen werden `/products/5` `ProductsApi.GetProduct(int)` .</span><span class="sxs-lookup"><span data-stu-id="37602-357">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="37602-358">Beide Aktionen entsprechen nur http, `GET` da Sie mit dem-Attribut markiert sind `[HttpGet]` .</span><span class="sxs-lookup"><span data-stu-id="37602-358">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="37602-359">Routenvorlagen, die auf eine Aktion angewendet werden, die mit einem `/` oder `~/` beginnen, können nicht mit Routenvorlagen kombiniert werden, die auf den Controller angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="37602-359">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="37602-360">Das folgende Beispiel entspricht einem Satz von URL-Pfaden, die der Standardroute ähneln.</span><span class="sxs-lookup"><span data-stu-id="37602-360">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="37602-361">In der folgenden Tabelle werden die `[Route]` Attribute im vorangehenden Code erläutert:</span><span class="sxs-lookup"><span data-stu-id="37602-361">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="37602-362">attribute</span><span class="sxs-lookup"><span data-stu-id="37602-362">Attribute</span></span>               | <span data-ttu-id="37602-363">Kombiniert mit`[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="37602-363">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="37602-364">Definiert die Routen Vorlage.</span><span class="sxs-lookup"><span data-stu-id="37602-364">Defines route template</span></span> |
| ---
<span data-ttu-id="37602-365">Title: Autor: Beschreibung: ms. Author: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="37602-365">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="37602-366">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="37602-366">'Blazor'</span></span>
- <span data-ttu-id="37602-367">'Identity'</span><span class="sxs-lookup"><span data-stu-id="37602-367">'Identity'</span></span>
- <span data-ttu-id="37602-368">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="37602-368">'Let's Encrypt'</span></span>
- <span data-ttu-id="37602-369">'Razor'</span><span class="sxs-lookup"><span data-stu-id="37602-369">'Razor'</span></span>
- <span data-ttu-id="37602-370">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="37602-370">'SignalR' uid:</span></span> 

-
<span data-ttu-id="37602-371">Title: Autor: Beschreibung: ms. Author: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="37602-371">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="37602-372">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="37602-372">'Blazor'</span></span>
- <span data-ttu-id="37602-373">'Identity'</span><span class="sxs-lookup"><span data-stu-id="37602-373">'Identity'</span></span>
- <span data-ttu-id="37602-374">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="37602-374">'Let's Encrypt'</span></span>
- <span data-ttu-id="37602-375">'Razor'</span><span class="sxs-lookup"><span data-stu-id="37602-375">'Razor'</span></span>
- <span data-ttu-id="37602-376">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="37602-376">'SignalR' uid:</span></span> 

-
<span data-ttu-id="37602-377">Title: Autor: Beschreibung: ms. Author: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="37602-377">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="37602-378">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="37602-378">'Blazor'</span></span>
- <span data-ttu-id="37602-379">'Identity'</span><span class="sxs-lookup"><span data-stu-id="37602-379">'Identity'</span></span>
- <span data-ttu-id="37602-380">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="37602-380">'Let's Encrypt'</span></span>
- <span data-ttu-id="37602-381">'Razor'</span><span class="sxs-lookup"><span data-stu-id="37602-381">'Razor'</span></span>
- <span data-ttu-id="37602-382">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="37602-382">'SignalR' uid:</span></span> 

-
<span data-ttu-id="37602-383">Title: Autor: Beschreibung: ms. Author: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="37602-383">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="37602-384">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="37602-384">'Blazor'</span></span>
- <span data-ttu-id="37602-385">'Identity'</span><span class="sxs-lookup"><span data-stu-id="37602-385">'Identity'</span></span>
- <span data-ttu-id="37602-386">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="37602-386">'Let's Encrypt'</span></span>
- <span data-ttu-id="37602-387">'Razor'</span><span class="sxs-lookup"><span data-stu-id="37602-387">'Razor'</span></span>
- <span data-ttu-id="37602-388">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="37602-388">'SignalR' uid:</span></span> 

-
<span data-ttu-id="37602-389">Title: Autor: Beschreibung: ms. Author: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="37602-389">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="37602-390">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="37602-390">'Blazor'</span></span>
- <span data-ttu-id="37602-391">'Identity'</span><span class="sxs-lookup"><span data-stu-id="37602-391">'Identity'</span></span>
- <span data-ttu-id="37602-392">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="37602-392">'Let's Encrypt'</span></span>
- <span data-ttu-id="37602-393">'Razor'</span><span class="sxs-lookup"><span data-stu-id="37602-393">'Razor'</span></span>
- <span data-ttu-id="37602-394">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="37602-394">'SignalR' uid:</span></span> 

-
<span data-ttu-id="37602-395">Title: Autor: Beschreibung: ms. Author: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="37602-395">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="37602-396">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="37602-396">'Blazor'</span></span>
- <span data-ttu-id="37602-397">'Identity'</span><span class="sxs-lookup"><span data-stu-id="37602-397">'Identity'</span></span>
- <span data-ttu-id="37602-398">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="37602-398">'Let's Encrypt'</span></span>
- <span data-ttu-id="37602-399">'Razor'</span><span class="sxs-lookup"><span data-stu-id="37602-399">'Razor'</span></span>
- <span data-ttu-id="37602-400">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="37602-400">'SignalR' uid:</span></span> 

<span data-ttu-id="37602-401">--------- | ---Titel: Author: Description: ms. Author: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="37602-401">--------- | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="37602-402">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="37602-402">'Blazor'</span></span>
- <span data-ttu-id="37602-403">'Identity'</span><span class="sxs-lookup"><span data-stu-id="37602-403">'Identity'</span></span>
- <span data-ttu-id="37602-404">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="37602-404">'Let's Encrypt'</span></span>
- <span data-ttu-id="37602-405">'Razor'</span><span class="sxs-lookup"><span data-stu-id="37602-405">'Razor'</span></span>
- <span data-ttu-id="37602-406">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="37602-406">'SignalR' uid:</span></span> 

-
<span data-ttu-id="37602-407">Title: Autor: Beschreibung: ms. Author: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="37602-407">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="37602-408">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="37602-408">'Blazor'</span></span>
- <span data-ttu-id="37602-409">'Identity'</span><span class="sxs-lookup"><span data-stu-id="37602-409">'Identity'</span></span>
- <span data-ttu-id="37602-410">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="37602-410">'Let's Encrypt'</span></span>
- <span data-ttu-id="37602-411">'Razor'</span><span class="sxs-lookup"><span data-stu-id="37602-411">'Razor'</span></span>
- <span data-ttu-id="37602-412">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="37602-412">'SignalR' uid:</span></span> 

-
<span data-ttu-id="37602-413">Title: Autor: Beschreibung: ms. Author: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="37602-413">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="37602-414">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="37602-414">'Blazor'</span></span>
- <span data-ttu-id="37602-415">'Identity'</span><span class="sxs-lookup"><span data-stu-id="37602-415">'Identity'</span></span>
- <span data-ttu-id="37602-416">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="37602-416">'Let's Encrypt'</span></span>
- <span data-ttu-id="37602-417">'Razor'</span><span class="sxs-lookup"><span data-stu-id="37602-417">'Razor'</span></span>
- <span data-ttu-id="37602-418">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="37602-418">'SignalR' uid:</span></span> 

-
<span data-ttu-id="37602-419">Title: Autor: Beschreibung: ms. Author: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="37602-419">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="37602-420">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="37602-420">'Blazor'</span></span>
- <span data-ttu-id="37602-421">'Identity'</span><span class="sxs-lookup"><span data-stu-id="37602-421">'Identity'</span></span>
- <span data-ttu-id="37602-422">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="37602-422">'Let's Encrypt'</span></span>
- <span data-ttu-id="37602-423">'Razor'</span><span class="sxs-lookup"><span data-stu-id="37602-423">'Razor'</span></span>
- <span data-ttu-id="37602-424">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="37602-424">'SignalR' uid:</span></span> 

<span data-ttu-id="37602-425">------ | ---Titel: Author: Description: ms. Author: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="37602-425">------ | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="37602-426">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="37602-426">'Blazor'</span></span>
- <span data-ttu-id="37602-427">'Identity'</span><span class="sxs-lookup"><span data-stu-id="37602-427">'Identity'</span></span>
- <span data-ttu-id="37602-428">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="37602-428">'Let's Encrypt'</span></span>
- <span data-ttu-id="37602-429">'Razor'</span><span class="sxs-lookup"><span data-stu-id="37602-429">'Razor'</span></span>
- <span data-ttu-id="37602-430">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="37602-430">'SignalR' uid:</span></span> 

-
<span data-ttu-id="37602-431">Title: Autor: Beschreibung: ms. Author: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="37602-431">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="37602-432">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="37602-432">'Blazor'</span></span>
- <span data-ttu-id="37602-433">'Identity'</span><span class="sxs-lookup"><span data-stu-id="37602-433">'Identity'</span></span>
- <span data-ttu-id="37602-434">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="37602-434">'Let's Encrypt'</span></span>
- <span data-ttu-id="37602-435">'Razor'</span><span class="sxs-lookup"><span data-stu-id="37602-435">'Razor'</span></span>
- <span data-ttu-id="37602-436">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="37602-436">'SignalR' uid:</span></span> 

<span data-ttu-id="37602-437">----- | | `[Route("")]` | Ja | `"Home"` |
| `[Route("Index")]` | Ja | `"Home/Index"` |
| `[Route("/")]` | **Nein** | `""` |
 | `[Route("About")]` | Ja | `"Home/About"`|</span><span class="sxs-lookup"><span data-stu-id="37602-437">----- | | `[Route("")]` | Yes | `"Home"` |
| `[Route("Index")]` | Yes | `"Home/Index"` |
| `[Route("/")]` | **No** | `""` |
| `[Route("About")]` | Yes | `"Home/About"` |</span></span>

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="37602-438">Attribut Routen Reihenfolge</span><span class="sxs-lookup"><span data-stu-id="37602-438">Attribute route order</span></span>

<span data-ttu-id="37602-439">Routing erstellt eine Struktur und gleicht alle Endpunkte gleichzeitig ab:</span><span class="sxs-lookup"><span data-stu-id="37602-439">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="37602-440">Die Routen Einträge Verhalten sich so, als ob Sie in einer idealen Reihenfolge platziert werden.</span><span class="sxs-lookup"><span data-stu-id="37602-440">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="37602-441">Die spezifischsten Routen können vor den allgemeineren Routen ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="37602-441">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="37602-442">Beispielsweise ist eine Attribut Route wie `blog/search/{topic}` spezifischer als eine Attribut Route wie `blog/{*article}` .</span><span class="sxs-lookup"><span data-stu-id="37602-442">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="37602-443">Die `blog/search/{topic}` Route hat standardmäßig eine höhere Priorität, da Sie spezifischer ist.</span><span class="sxs-lookup"><span data-stu-id="37602-443">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="37602-444">Mit [herkömmlichem Routing](#cr)ist der Entwickler für das Platzieren von Routen in der gewünschten Reihenfolge verantwortlich.</span><span class="sxs-lookup"><span data-stu-id="37602-444">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="37602-445">Attribut Routen können mithilfe der-Eigenschaft eine Bestellung konfigurieren <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> .</span><span class="sxs-lookup"><span data-stu-id="37602-445">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="37602-446">Alle vom Framework bereitgestellten [Routen Attribute](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) umfassen `Order` .</span><span class="sxs-lookup"><span data-stu-id="37602-446">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="37602-447">Routen werden entsprechend einer aufsteigenden Reihenfolge der `Order`-Eigenschaft verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="37602-447">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="37602-448">Die Standardreihenfolge ist `0`.</span><span class="sxs-lookup"><span data-stu-id="37602-448">The default order is `0`.</span></span> <span data-ttu-id="37602-449">Das Festlegen einer Route mithilfe von wird `Order = -1` vor Routen ausgeführt, die keine Reihenfolge festlegen.</span><span class="sxs-lookup"><span data-stu-id="37602-449">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="37602-450">Das Festlegen einer Route mithilfe von wird `Order = 1` nach der Standardrouten Reihenfolge ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="37602-450">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="37602-451">**Vermeiden** Sie die Abhängigkeit von `Order` .</span><span class="sxs-lookup"><span data-stu-id="37602-451">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="37602-452">Wenn für den URL-Speicher einer APP explizite Auftrags Werte erforderlich sind, um ordnungsgemäß weiterzuleiten, ist es wahrscheinlich auch für Clients verwirrend.</span><span class="sxs-lookup"><span data-stu-id="37602-452">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="37602-453">Im Allgemeinen wählt das Attribut Routing die richtige Route mit URL-Übereinstimmung aus.</span><span class="sxs-lookup"><span data-stu-id="37602-453">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="37602-454">Wenn die für die URL-Generierung verwendete Standard Reihenfolge nicht funktioniert, ist die Verwendung eines Routen namens als außer Kraft Setzung in der Regel einfacher als das Anwenden der `Order` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="37602-454">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="37602-455">Beachten Sie die beiden folgenden Controller, die beide den Routen Abgleich definieren `/home` :</span><span class="sxs-lookup"><span data-stu-id="37602-455">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="37602-456">Wenn `/home` Sie mit dem vorangehenden Code anfordern, wird eine Ausnahme ausgelöst, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="37602-456">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="37602-457">Durch das Hinzufügen `Order` eines der Routen Attribute wird die Mehrdeutigkeit aufgelöst:</span><span class="sxs-lookup"><span data-stu-id="37602-457">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="37602-458">Mit dem vorangehenden Code `/home` führt den `HomeController.Index` Endpunkt aus.</span><span class="sxs-lookup"><span data-stu-id="37602-458">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="37602-459">Um die `MyDemoController.MyIndex` anzufordern, fordern Sie an `/home/MyIndex` .</span><span class="sxs-lookup"><span data-stu-id="37602-459">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="37602-460">**Hinweis**:</span><span class="sxs-lookup"><span data-stu-id="37602-460">**Note**:</span></span>

* <span data-ttu-id="37602-461">Der vorangehende Code ist ein Beispiel oder ein schlechtes Routing Design.</span><span class="sxs-lookup"><span data-stu-id="37602-461">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="37602-462">Es wurde verwendet, um die-Eigenschaft zu veranschaulichen `Order` .</span><span class="sxs-lookup"><span data-stu-id="37602-462">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="37602-463">Die- `Order` Eigenschaft löst nur die Mehrdeutigkeit auf, diese Vorlage kann nicht abgeglichen werden.</span><span class="sxs-lookup"><span data-stu-id="37602-463">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="37602-464">Es wäre besser, die Vorlage zu entfernen `[Route("Home")]` .</span><span class="sxs-lookup"><span data-stu-id="37602-464">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="37602-465">Siehe [ Razor Seiten Routen und App-Konventionen: Routen Reihenfolge](xref:razor-pages/razor-pages-conventions#route-order) für Informationen zur Routen Reihenfolge mit Razor Seiten.</span><span class="sxs-lookup"><span data-stu-id="37602-465">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="37602-466">In einigen Fällen wird ein HTTP 500-Fehler mit mehrdeutigen Routen zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="37602-466">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="37602-467">Verwenden Sie die [Protokollierung](xref:fundamentals/logging/index) , um anzuzeigen, welche Endpunkte `AmbiguousMatchException` den verursacht haben</span><span class="sxs-lookup"><span data-stu-id="37602-467">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="37602-468">Ersetzung von Token in Routen Vorlagen [Controller], [Aktion], [Bereich]</span><span class="sxs-lookup"><span data-stu-id="37602-468">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="37602-469">Zur einfacheren Unterstützung unterstützen Attribut Routen die Tokenersetzung für reservierte Routen Parameter durch das Einschließen eines Tokens in einer der folgenden Werte:</span><span class="sxs-lookup"><span data-stu-id="37602-469">For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:</span></span>

* <span data-ttu-id="37602-470">Eckige Klammern:`[]`</span><span class="sxs-lookup"><span data-stu-id="37602-470">Square brackets: `[]`</span></span>
* <span data-ttu-id="37602-471">Geschweifte Klammern:`{}`</span><span class="sxs-lookup"><span data-stu-id="37602-471">Curly braces: `{}`</span></span>

<span data-ttu-id="37602-472">Die Token `[action]` , `[area]` und `[controller]` werden durch die Werte für Aktionsname, Bereichs Name und Controller Name aus der Aktion ersetzt, in der die Route definiert ist:</span><span class="sxs-lookup"><span data-stu-id="37602-472">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="37602-473">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="37602-473">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="37602-474">Match`/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="37602-474">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="37602-475">Match`/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="37602-475">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="37602-476">Die Tokenersetzung tritt im letzten Schritt der Erstellung von Attributrouten auf.</span><span class="sxs-lookup"><span data-stu-id="37602-476">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="37602-477">Das vorherige Beispiel verhält sich wie der folgende Code:</span><span class="sxs-lookup"><span data-stu-id="37602-477">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="37602-478">Attributrouten können auch mit Vererbung kombiniert werden.</span><span class="sxs-lookup"><span data-stu-id="37602-478">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="37602-479">Dies ist in Kombination mit der Tokenersetzung stark.</span><span class="sxs-lookup"><span data-stu-id="37602-479">This is powerful combined with token replacement.</span></span> <span data-ttu-id="37602-480">Tokenersetzung gilt auch für Routennamen, die durch Attributrouten definiert werden.</span><span class="sxs-lookup"><span data-stu-id="37602-480">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="37602-481">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generiert für jede Aktion einen eindeutigen Routennamen:</span><span class="sxs-lookup"><span data-stu-id="37602-481">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="37602-482">Tokenersetzung gilt auch für Routennamen, die durch Attributrouten definiert werden.</span><span class="sxs-lookup"><span data-stu-id="37602-482">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="37602-483"> generiert für jede Aktion einen eindeutigen Routennamen.</span><span class="sxs-lookup"><span data-stu-id="37602-483">generates a unique route name for each action.</span></span>

<span data-ttu-id="37602-484">Damit das Trennzeichen `[` oder `]` der Tokenersetzungs-Literalzeichenfolge bei einem Abgleich gefunden wird, muss es doppelt vorhanden sein (`[[` oder `]]`), was einem Escapezeichen entspricht.</span><span class="sxs-lookup"><span data-stu-id="37602-484">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="37602-485">Verwenden eines Parametertransformators zum Anpassen der Tokenersetzung</span><span class="sxs-lookup"><span data-stu-id="37602-485">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="37602-486">Die Tokenersetzung kann mit einem Parametertransformator angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="37602-486">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="37602-487">Ein Parametertransformator implementiert <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> und wandelt den Wert der Parameter um.</span><span class="sxs-lookup"><span data-stu-id="37602-487">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="37602-488">Beispielsweise ändert ein benutzerdefinierter `SlugifyParameterTransformer` parametertransformator den `SubscriptionManagement` Routen Wert in `subscription-management` :</span><span class="sxs-lookup"><span data-stu-id="37602-488">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="37602-489">Die <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> ist eine Anwendungsmodellkonvention, die Folgendes ausführt:</span><span class="sxs-lookup"><span data-stu-id="37602-489">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="37602-490">Wendet einen angegebenen Parametertransformator auf alle Attributrouten in der App an.</span><span class="sxs-lookup"><span data-stu-id="37602-490">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="37602-491">Passt die Tokenwerte für Attributrouten bei ihrer Ersetzung an.</span><span class="sxs-lookup"><span data-stu-id="37602-491">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="37602-492">Die vorangehende `ListAll` Methode stimmt überein `/subscription-management/list-all` .</span><span class="sxs-lookup"><span data-stu-id="37602-492">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="37602-493">Die `RouteTokenTransformerConvention` wird als Option in `ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="37602-493">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="37602-494">Die Definition von Slug finden Sie unter [MDN-Webdokumentation zu Slug](https://developer.mozilla.org/docs/Glossary/Slug) .</span><span class="sxs-lookup"><span data-stu-id="37602-494">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="37602-495">Mehrere Attribut Routen</span><span class="sxs-lookup"><span data-stu-id="37602-495">Multiple attribute routes</span></span>

<span data-ttu-id="37602-496">Attributrouting unterstützt das Definieren mehrerer Routen, die zu derselben Aktion führen.</span><span class="sxs-lookup"><span data-stu-id="37602-496">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="37602-497">Dies wird am häufigsten beim Imitieren des Verhaltens der herkömmlichen Standardroute verwendet. Im folgenden Beispiel wird dies gezeigt:</span><span class="sxs-lookup"><span data-stu-id="37602-497">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="37602-498">Wenn Sie mehrere Routen Attribute auf dem Controller platzieren, bedeutet dies, dass jede mit den Routen Attributen der Aktionsmethoden kombiniert wird:</span><span class="sxs-lookup"><span data-stu-id="37602-498">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="37602-499">Alle [http-Verb](#verb) Routen Einschränkungen implementieren `IActionConstraint` .</span><span class="sxs-lookup"><span data-stu-id="37602-499">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="37602-500">Wenn mehrere Routen Attribute, die implementieren, <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> in eine Aktion eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="37602-500">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="37602-501">Jede Aktions Einschränkung kombiniert mit der auf den Controller angewendeten Routen Vorlage.</span><span class="sxs-lookup"><span data-stu-id="37602-501">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="37602-502">Die Verwendung mehrerer Routen für Aktionen mag hilfreich und leistungsstark sein. es ist besser, den URL-Bereich ihrer App als "Basic" und "klar" festzuhalten.</span><span class="sxs-lookup"><span data-stu-id="37602-502">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="37602-503">Verwenden Sie mehrere Routen für Aktionen **nur** bei Bedarf, z. b. zur Unterstützung vorhandener Clients.</span><span class="sxs-lookup"><span data-stu-id="37602-503">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="37602-504">Angeben von optionalen Attributroutenparametern, Standardwerten und Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="37602-504">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="37602-505">Attributrouten unterstützen dieselbe Inline-Syntax wie herkömmliche Routen, um optionale Parameter, Standardwerte und Einschränkungen anzugeben.</span><span class="sxs-lookup"><span data-stu-id="37602-505">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="37602-506">Im vorangehenden Code wird `[HttpPost("product/{id:int}")]` eine Routen Einschränkung angewendet.</span><span class="sxs-lookup"><span data-stu-id="37602-506">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="37602-507">Die `ProductsController.ShowProduct` Aktion wird nur durch URL-Pfade wie abgeglichen `/product/3` .</span><span class="sxs-lookup"><span data-stu-id="37602-507">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="37602-508">Der Weiterleitungs Vorlagen Teil `{id:int}` schränkt dieses Segment auf ganze Zahlen ein.</span><span class="sxs-lookup"><span data-stu-id="37602-508">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="37602-509">Eine ausführliche Beschreibung der Syntax der Routenvorlage finden Sie unter [Routenvorlagenreferenz](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="37602-509">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="37602-510">Benutzerdefinierte Routen Attribute mit iroutetemplateprovider</span><span class="sxs-lookup"><span data-stu-id="37602-510">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="37602-511">Alle [Routen Attribute](#rt) implementieren <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider> .</span><span class="sxs-lookup"><span data-stu-id="37602-511">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="37602-512">Die ASP.net Core Laufzeit:</span><span class="sxs-lookup"><span data-stu-id="37602-512">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="37602-513">Sucht nach Attributen in Controller Klassen und Aktionsmethoden, wenn die APP gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="37602-513">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="37602-514">Verwendet die Attribute, die implementieren, `IRouteTemplateProvider` um den anfänglichen Satz von Routen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="37602-514">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="37602-515">Implementieren `IRouteTemplateProvider` Sie, um benutzerdefinierte Routen Attribute zu definieren.</span><span class="sxs-lookup"><span data-stu-id="37602-515">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="37602-516">Jeder `IRouteTemplateProvider` lässt Sie eine einzelne Route mit einer benutzerdefinierten Routenvorlage, Reihenfolge und einem benutzerdefinierten Namen definieren:</span><span class="sxs-lookup"><span data-stu-id="37602-516">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="37602-517">Die vorherige `Get` Methode gibt zurück `Order = 2, Template = api/MyTestApi` .</span><span class="sxs-lookup"><span data-stu-id="37602-517">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="37602-518">Verwenden des Anwendungs Modells zum Anpassen von Attribut Routen</span><span class="sxs-lookup"><span data-stu-id="37602-518">Use application model to customize attribute routes</span></span>

<span data-ttu-id="37602-519">Das Anwendungsmodell:</span><span class="sxs-lookup"><span data-stu-id="37602-519">The application model:</span></span>

* <span data-ttu-id="37602-520">Ist ein Objektmodell, das beim Start erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="37602-520">Is an object model created at startup.</span></span>
* <span data-ttu-id="37602-521">Enthält alle Metadaten, die von ASP.net Core verwendet werden, um die Aktionen in einer APP weiterzuleiten und auszuführen.</span><span class="sxs-lookup"><span data-stu-id="37602-521">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="37602-522">Das Anwendungsmodell umfasst alle Daten, die von Routen Attributen gesammelt werden.</span><span class="sxs-lookup"><span data-stu-id="37602-522">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="37602-523">Die Daten aus Routen Attributen werden von der- `IRouteTemplateProvider` Implementierung bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="37602-523">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="37602-524">Geregelt</span><span class="sxs-lookup"><span data-stu-id="37602-524">Conventions:</span></span>

* <span data-ttu-id="37602-525">Kann geschrieben werden, um das Anwendungsmodell so zu ändern, dass das Routing Verhalten angepasst wird.</span><span class="sxs-lookup"><span data-stu-id="37602-525">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="37602-526">Werden beim APP-Start gelesen.</span><span class="sxs-lookup"><span data-stu-id="37602-526">Are read at app startup.</span></span>

<span data-ttu-id="37602-527">Dieser Abschnitt zeigt ein einfaches Beispiel für das Anpassen des Routings mithilfe des Anwendungs Modells.</span><span class="sxs-lookup"><span data-stu-id="37602-527">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="37602-528">Der folgende Code bewirkt, dass Routen ungefähr mit der Ordnerstruktur des Projekts übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="37602-528">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="37602-529">Der folgende Code verhindert, `namespace` dass die Konvention auf Controller angewendet wird, die Attribut weitergeleitet werden:</span><span class="sxs-lookup"><span data-stu-id="37602-529">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="37602-530">Der folgende Controller verwendet beispielsweise nicht `NamespaceRoutingConvention` :</span><span class="sxs-lookup"><span data-stu-id="37602-530">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="37602-531">Die `NamespaceRoutingConvention.Apply`-Methode:</span><span class="sxs-lookup"><span data-stu-id="37602-531">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="37602-532">Führt keine Aktion aus, wenn der Controller Attribut weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="37602-532">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="37602-533">Legt die Controller Vorlage auf Grundlage von fest `namespace` , wobei die Basis `namespace` entfernt wurde.</span><span class="sxs-lookup"><span data-stu-id="37602-533">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="37602-534">`NamespaceRoutingConvention`Kann in angewendet werden `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="37602-534">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="37602-535">Sehen Sie sich beispielsweise den folgenden Controller an:</span><span class="sxs-lookup"><span data-stu-id="37602-535">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="37602-536">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="37602-536">In the preceding code:</span></span>

* <span data-ttu-id="37602-537">Die Basis `namespace` ist `My.Application` .</span><span class="sxs-lookup"><span data-stu-id="37602-537">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="37602-538">Der vollständige Name des vorangehenden Controllers ist `My.Application.Admin.Controllers.UsersController` .</span><span class="sxs-lookup"><span data-stu-id="37602-538">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="37602-539">Der `NamespaceRoutingConvention` legt die Controller Vorlage auf fest `Admin/Controllers/Users/[action]/{id?` .</span><span class="sxs-lookup"><span data-stu-id="37602-539">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="37602-540">`NamespaceRoutingConvention`Kann auch als Attribut auf einem Controller angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="37602-540">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="37602-541">Gemischtes Routing: Attributrouting vs. herkömmliches Routing</span><span class="sxs-lookup"><span data-stu-id="37602-541">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="37602-542">ASP.net Core-Apps können die Verwendung von herkömmlichem Routing und Attribut Routing vermischen.</span><span class="sxs-lookup"><span data-stu-id="37602-542">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="37602-543">In der Regel werden herkömmliche Routen für Controller verwendet, die für Browser-HTML-Seiten gedacht sind, und das Attributrouting für Controller für REST-APIs.</span><span class="sxs-lookup"><span data-stu-id="37602-543">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="37602-544">Aktionen werden entweder herkömmlich oder über Attribute zugeordnet,</span><span class="sxs-lookup"><span data-stu-id="37602-544">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="37602-545">d.h., dass eine Route auf dem Controller oder der Aktion platziert wird.</span><span class="sxs-lookup"><span data-stu-id="37602-545">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="37602-546">Aktionen, die Attributrouten definieren, können nicht über die herkömmliche Routen und umgekehrt erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="37602-546">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="37602-547">Alle **Routen Attribute** auf dem Controller machen **alle** Aktionen im Controller Attribut weiter.</span><span class="sxs-lookup"><span data-stu-id="37602-547">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="37602-548">Bei Attribut Routing und herkömmlichem Routing wird dieselbe Routing-Engine verwendet.</span><span class="sxs-lookup"><span data-stu-id="37602-548">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="37602-549">URL-Generierung und Ambient-Werte</span><span class="sxs-lookup"><span data-stu-id="37602-549">URL Generation and ambient values</span></span>

<span data-ttu-id="37602-550">Apps können Routing-URL-Generierungs Features verwenden, um URL-Links zu Aktionen zu generieren.</span><span class="sxs-lookup"><span data-stu-id="37602-550">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="37602-551">Durch das Erstellen von URLs werden hart codierte URLs eliminiert, sodass der Code stabiler und verwallegbar wird.</span><span class="sxs-lookup"><span data-stu-id="37602-551">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="37602-552">Dieser Abschnitt konzentriert sich auf die Funktionen der URL-Generierung, die von MVC bereitgestellt werden</span><span class="sxs-lookup"><span data-stu-id="37602-552">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="37602-553">Eine detaillierte Beschreibung der URL-Generierung finden Sie unter [Routing in ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="37602-553">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="37602-554">Die <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> Schnittstelle ist das zugrunde liegende Element der Infrastruktur zwischen MVC und dem Routing für die URL-Generierung.</span><span class="sxs-lookup"><span data-stu-id="37602-554">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="37602-555">Eine Instanz von `IUrlHelper` ist über die- `Url` Eigenschaft in Controllern, Ansichten und Ansichts Komponenten verfügbar.</span><span class="sxs-lookup"><span data-stu-id="37602-555">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="37602-556">Im folgenden Beispiel wird die- `IUrlHelper` Schnittstelle mithilfe der- `Controller.Url` Eigenschaft verwendet, um eine URL zu einer anderen Aktion zu generieren.</span><span class="sxs-lookup"><span data-stu-id="37602-556">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="37602-557">Wenn die APP die herkömmliche Standardroute verwendet, ist der Wert der `url` Variablen die URL-Pfad Zeichenfolge `/UrlGeneration/Destination` .</span><span class="sxs-lookup"><span data-stu-id="37602-557">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="37602-558">Dieser URL-Pfad wird durch das Routing erstellt, indem Folgendes kombiniert wird:</span><span class="sxs-lookup"><span data-stu-id="37602-558">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="37602-559">Die Routen Werte aus der aktuellen Anforderung, die als **Umgebungs Werte**bezeichnet werden.</span><span class="sxs-lookup"><span data-stu-id="37602-559">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="37602-560">Die Werte, die an weitergegeben werden, `Url.Action` und ersetzen diese Werte in der Routen Vorlage:</span><span class="sxs-lookup"><span data-stu-id="37602-560">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="37602-561">Der Wert eines jeden Routenparameters wird in der Routenvorlage durch die entsprechenden Namen mit den Werten und Umgebungswerten ersetzt.</span><span class="sxs-lookup"><span data-stu-id="37602-561">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="37602-562">Ein Routen Parameter, der keinen Wert hat, kann folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="37602-562">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="37602-563">Verwenden Sie ggf. einen Standardwert.</span><span class="sxs-lookup"><span data-stu-id="37602-563">Use a default value if it has one.</span></span>
* <span data-ttu-id="37602-564">Sie sollten übersprungen werden, wenn Sie optional ist.</span><span class="sxs-lookup"><span data-stu-id="37602-564">Be skipped if it's optional.</span></span> <span data-ttu-id="37602-565">Beispielsweise die `id` aus der Routen Vorlage `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="37602-565">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="37602-566">Die URL-Generierung schlägt fehl, wenn für einen erforderlichen Routen Parameter kein entsprechender Wert vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="37602-566">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="37602-567">Wenn die URL-Generierung für eine Route fehlschlägt, wird die nächste Route ausprobiert, bis alle Routen getestet wurden oder eine Übereinstimmung gefunden wurde.</span><span class="sxs-lookup"><span data-stu-id="37602-567">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="37602-568">Im vorherigen Beispiel von wird von `Url.Action` [herkömmlichem Routing](#cr)ausgegangen.</span><span class="sxs-lookup"><span data-stu-id="37602-568">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="37602-569">Die URL-Generierung funktioniert ähnlich wie das [Attribut Routing](#ar), obwohl sich die Konzepte unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="37602-569">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="37602-570">Mit herkömmlichem Routing:</span><span class="sxs-lookup"><span data-stu-id="37602-570">With conventional routing:</span></span>

* <span data-ttu-id="37602-571">Die Routen Werte werden zum Erweitern einer Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="37602-571">The route values are used to expand a template.</span></span>
* <span data-ttu-id="37602-572">Die Routen Werte für `controller` und werden `action` in der Regel in dieser Vorlage angezeigt.</span><span class="sxs-lookup"><span data-stu-id="37602-572">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="37602-573">Dies funktioniert, da die mit dem Routing übereinstimmenden URLs einer Konvention entsprechen.</span><span class="sxs-lookup"><span data-stu-id="37602-573">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="37602-574">Im folgenden Beispiel wird das Attribut Routing verwendet:</span><span class="sxs-lookup"><span data-stu-id="37602-574">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="37602-575">Durch die `Source` Aktion im vorangehenden Code wird generiert `custom/url/to/destination` .</span><span class="sxs-lookup"><span data-stu-id="37602-575">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="37602-576"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>wurde in ASP.net Core 3,0 als Alternative zu hinzugefügt `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="37602-576"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="37602-577">`LinkGenerator`bietet ähnliche, aber flexiblere Funktionen.</span><span class="sxs-lookup"><span data-stu-id="37602-577">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="37602-578">Jede Methode `IUrlHelper` von verfügt auch über eine entsprechende Familie von-Methoden `LinkGenerator` .</span><span class="sxs-lookup"><span data-stu-id="37602-578">Each method on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="37602-579">Generieren von URLs nach Aktionsnamen</span><span class="sxs-lookup"><span data-stu-id="37602-579">Generating URLs by action name</span></span>

<span data-ttu-id="37602-580">" [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)", " [Linkgenerator. getpathbyaction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)" und alle zugehörigen über Ladungen sind so konzipiert, dass der Ziel Endpunkt durch Angabe eines Controller namens und Aktions namens generiert wird.</span><span class="sxs-lookup"><span data-stu-id="37602-580">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="37602-581">Bei Verwendung `Url.Action` von werden die aktuellen Routen Werte für `controller` und `action` von der Laufzeit bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="37602-581">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="37602-582">Der Wert von `controller` und `action` ist Teil der [Ambient-Werte](#ambient) und-Werte.</span><span class="sxs-lookup"><span data-stu-id="37602-582">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="37602-583">Die `Url.Action` -Methode verwendet immer die aktuellen Werte von `action` und `controller` und generiert einen URL-Pfad, der zur aktuellen Aktion weiterleitet.</span><span class="sxs-lookup"><span data-stu-id="37602-583">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="37602-584">Beim Routing werden die Werte in Ambient-Werten verwendet, um Informationen einzugeben, die beim Erstellen einer URL nicht bereitgestellt wurden.</span><span class="sxs-lookup"><span data-stu-id="37602-584">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="37602-585">Stellen Sie sich eine Route wie `{a}/{b}/{c}/{d}` mit Ambient-Werten vor `{ a = Alice, b = Bob, c = Carol, d = David }` :</span><span class="sxs-lookup"><span data-stu-id="37602-585">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="37602-586">Das Routing verfügt über genügend Informationen, um eine URL ohne zusätzliche Werte zu generieren.</span><span class="sxs-lookup"><span data-stu-id="37602-586">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="37602-587">Das Routing verfügt über genügend Informationen, da alle Routen Parameter über einen Wert verfügen.</span><span class="sxs-lookup"><span data-stu-id="37602-587">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="37602-588">Wenn der Wert `{ d = Donovan }` hinzugefügt wird:</span><span class="sxs-lookup"><span data-stu-id="37602-588">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="37602-589">Der Wert `{ d = David }` wird ignoriert.</span><span class="sxs-lookup"><span data-stu-id="37602-589">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="37602-590">Der generierte URL-Pfad ist `Alice/Bob/Carol/Donovan` .</span><span class="sxs-lookup"><span data-stu-id="37602-590">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="37602-591">**Warnung**: URL-Pfade sind hierarchisch.</span><span class="sxs-lookup"><span data-stu-id="37602-591">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="37602-592">Im vorherigen Beispiel, wenn der Wert `{ c = Cheryl }` hinzugefügt wird:</span><span class="sxs-lookup"><span data-stu-id="37602-592">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="37602-593">Beide Werte `{ c = Carol, d = David }` werden ignoriert.</span><span class="sxs-lookup"><span data-stu-id="37602-593">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="37602-594">Für ist kein Wert mehr vorhanden, `d` und die URL-Generierung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="37602-594">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="37602-595">Die gewünschten Werte von `c` und `d` müssen angegeben werden, um eine URL zu generieren.</span><span class="sxs-lookup"><span data-stu-id="37602-595">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="37602-596">Möglicherweise erwarten Sie dieses Problem mit der Standardroute `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="37602-596">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="37602-597">Dieses Problem tritt in der Praxis selten auf, da `Url.Action` explizit einen `controller` -Wert und einen- `action` Wert angibt.</span><span class="sxs-lookup"><span data-stu-id="37602-597">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="37602-598">Mehrere über Ladungen von [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) nehmen ein Routen Werte Objekt an, um Werte für andere Routen Parameter als und bereitzustellen `controller` `action` .</span><span class="sxs-lookup"><span data-stu-id="37602-598">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="37602-599">Das Routen Werte Objekt wird häufig mit verwendet `id` .</span><span class="sxs-lookup"><span data-stu-id="37602-599">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="37602-600">Beispiel: `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="37602-600">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="37602-601">Das Routen Werte Objekt:</span><span class="sxs-lookup"><span data-stu-id="37602-601">The route values object:</span></span>

* <span data-ttu-id="37602-602">Gemäß der Konvention ist in der Regel ein Objekt des anonymen Typs.</span><span class="sxs-lookup"><span data-stu-id="37602-602">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="37602-603">Kann ein `IDictionary<>` oder ein [poco](https://wikipedia.org/wiki/Plain_old_CLR_object)sein).</span><span class="sxs-lookup"><span data-stu-id="37602-603">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="37602-604">Alle zusätzlichen Routenwerte, die keinen Routenparametern zugeordnet sind, werden in der Abfragezeichenfolge platziert.</span><span class="sxs-lookup"><span data-stu-id="37602-604">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="37602-605">Der vorangehende Code generiert `/Products/Buy/17?color=red` .</span><span class="sxs-lookup"><span data-stu-id="37602-605">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="37602-606">Mit dem folgenden Code wird eine absolute URL generiert:</span><span class="sxs-lookup"><span data-stu-id="37602-606">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="37602-607">Um einen absolute URL zu erstellen, verwenden Sie eine der folgenden Aktionen:</span><span class="sxs-lookup"><span data-stu-id="37602-607">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="37602-608">Eine Überladung, die eine akzeptiert `protocol` .</span><span class="sxs-lookup"><span data-stu-id="37602-608">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="37602-609">Beispielsweise der vorangehende Code.</span><span class="sxs-lookup"><span data-stu-id="37602-609">For example, the preceding code.</span></span>
* <span data-ttu-id="37602-610">[Linkgenerator. geturibyaction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), wodurch standardmäßig absolute URIs generiert werden.</span><span class="sxs-lookup"><span data-stu-id="37602-610">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="37602-611">Generieren von URLs nach Route</span><span class="sxs-lookup"><span data-stu-id="37602-611">Generate URLs by route</span></span>

<span data-ttu-id="37602-612">Der vorangehende Code hat das Erzeugen einer URL durch Übergeben des Controllers und des Aktions namens veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="37602-612">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="37602-613">`IUrlHelper`stellt auch die [URL. RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) -Familie der Methoden bereit.</span><span class="sxs-lookup"><span data-stu-id="37602-613">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="37602-614">Diese Methoden ähneln [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), Sie kopieren jedoch nicht die aktuellen Werte von `action` und `controller` in die Routen Werte.</span><span class="sxs-lookup"><span data-stu-id="37602-614">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="37602-615">Die häufigste Verwendung von `Url.RouteUrl` :</span><span class="sxs-lookup"><span data-stu-id="37602-615">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="37602-616">Gibt einen Routennamen zum Generieren der URL an.</span><span class="sxs-lookup"><span data-stu-id="37602-616">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="37602-617">In der Regel wird kein Controller-oder Aktionsname angegeben.</span><span class="sxs-lookup"><span data-stu-id="37602-617">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="37602-618">Die folgende Razor Datei generiert einen HTML-Link zum `Destination_Route` :</span><span class="sxs-lookup"><span data-stu-id="37602-618">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a><span data-ttu-id="37602-619">Generieren von URLs in HTML undRazor</span><span class="sxs-lookup"><span data-stu-id="37602-619">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="37602-620"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper>stellt die <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> Methoden [HTML. BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) und [HTML. Action Link](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) zum Generieren von `<form>` - `<a>` bzw.-Elementen bereit.</span><span class="sxs-lookup"><span data-stu-id="37602-620"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="37602-621">Diese Methoden verwenden die [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) -Methode, um eine URL zu generieren, und Sie akzeptieren ähnliche Argumente.</span><span class="sxs-lookup"><span data-stu-id="37602-621">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="37602-622">Die `Url.RouteUrl`-Begleiter für `HtmlHelper` sind `Html.BeginRouteForm` und `Html.RouteLink`, die ähnliche Funktionen aufweisen.</span><span class="sxs-lookup"><span data-stu-id="37602-622">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="37602-623">Taghilfsprogramme generieren URLs mit den Taghilfsprogrammen `form` und `<a>`.</span><span class="sxs-lookup"><span data-stu-id="37602-623">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="37602-624">Beide implementieren mit `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="37602-624">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="37602-625">Weitere Informationen finden Sie [unter taghilfsprogramme in Formularen](xref:mvc/views/working-with-forms) .</span><span class="sxs-lookup"><span data-stu-id="37602-625">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="37602-626">In Ansichten ist `IUrlHelper` über die `Url`-Eigenschaft für jede Ad-hoc-URL-Generierung verfügbar, die keine der oben genannten ist.</span><span class="sxs-lookup"><span data-stu-id="37602-626">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="37602-627">URL-Generierung in Aktions Ergebnissen</span><span class="sxs-lookup"><span data-stu-id="37602-627">URL generation in Action Results</span></span>

<span data-ttu-id="37602-628">In den vorangehenden Beispielen wurde die Verwendung von `IUrlHelper` in einem Controller gezeigt.</span><span class="sxs-lookup"><span data-stu-id="37602-628">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="37602-629">Die häufigste Verwendung eines Controllers ist das Generieren einer URL als Teil eines Aktions Ergebnisses.</span><span class="sxs-lookup"><span data-stu-id="37602-629">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="37602-630">Die Basisklassen <xref:Microsoft.AspNetCore.Mvc.ControllerBase> und <xref:Microsoft.AspNetCore.Mvc.Controller> stellen Hilfsmethoden für Aktionsergebnisse bereit, die auf eine andere Aktionen verweisen.</span><span class="sxs-lookup"><span data-stu-id="37602-630">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="37602-631">Eine typische Verwendung besteht darin, nach Annahme der Benutzereingabe eine Umleitung durchzusetzen:</span><span class="sxs-lookup"><span data-stu-id="37602-631">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="37602-632">Die Aktions Ergebnisse Factorymethoden wie <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> und <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> folgen einem ähnlichen Muster wie die Methoden in `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="37602-632">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="37602-633">Sonderfall für dedizierte herkömmliche Routen</span><span class="sxs-lookup"><span data-stu-id="37602-633">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="37602-634">[Herkömmliches Routing](#cr) kann eine besondere Art von Routen Definition verwenden, die als [dedizierte konventionelle Route](#dcr)bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="37602-634">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="37602-635">Im folgenden Beispiel ist die Route mit dem Namen `blog` eine dedizierte konventionelle Route:</span><span class="sxs-lookup"><span data-stu-id="37602-635">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="37602-636">Wenn Sie die vorangehenden Routen Definitionen verwenden, `Url.Action("Index", "Home")` generiert den URL-Pfad `/` mithilfe der `default` Route, aber warum?</span><span class="sxs-lookup"><span data-stu-id="37602-636">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="37602-637">Man könnte meinen, dass die Routenwerte `{ controller = Home, action = Index }` ausreichen würden, um eine URL mithilfe von `blog` zu generieren, und das Ergebnis wäre `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="37602-637">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="37602-638">[Dedizierte herkömmliche Routen](#dcr) basieren auf einem besonderen Verhalten von Standardwerten, die über keinen entsprechenden Routen Parameter verfügen, der verhindert, dass die Route mit der URL-Generierung zu [gierig](xref:fundamentals/routing#greedy) wird.</span><span class="sxs-lookup"><span data-stu-id="37602-638">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="37602-639">In diesem Fall sind die Standardwerte `{ controller = Blog, action = Article }`, und weder `controller` noch `action` werden als Routenparameter verwendet.</span><span class="sxs-lookup"><span data-stu-id="37602-639">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="37602-640">Wenn das Routing die URL-Generierung ausführt, müssen die angegebenen Werte mit den Standardwerten übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="37602-640">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="37602-641">Die URL-Generierung mithilfe von `blog` schlägt fehl, da die Werte `{ controller = Home, action = Index }` nicht stimmen `{ controller = Blog, action = Article }` .</span><span class="sxs-lookup"><span data-stu-id="37602-641">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="37602-642">Routing greift dann wieder auf `default` zurück, was erfolgreich ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="37602-642">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="37602-643">Bereiche</span><span class="sxs-lookup"><span data-stu-id="37602-643">Areas</span></span>

<span data-ttu-id="37602-644">[Bereiche](xref:mvc/controllers/areas) sind ein MVC-Feature, das verwendet wird, um verwandte Funktionen in einer Gruppe als separate zu organisieren:</span><span class="sxs-lookup"><span data-stu-id="37602-644">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="37602-645">Routing Namespace für Controller Aktionen.</span><span class="sxs-lookup"><span data-stu-id="37602-645">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="37602-646">Ordnerstruktur für Ansichten.</span><span class="sxs-lookup"><span data-stu-id="37602-646">Folder structure for views.</span></span>

<span data-ttu-id="37602-647">Die Verwendung von Bereichen ermöglicht es einer APP, mehrere Controller mit dem gleichen Namen zu verwenden, solange Sie über unterschiedliche Bereiche verfügen.</span><span class="sxs-lookup"><span data-stu-id="37602-647">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="37602-648">Mithilfe von Bereichen wird außerdem eine Hierarchie erstellt, damit das Routing durch Hinzufügen eines anderen Routenparameters ausgeführt werden kann: `area` zu `controller` und `action`.</span><span class="sxs-lookup"><span data-stu-id="37602-648">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="37602-649">In diesem Abschnitt wird erläutert, wie das Routing mit Bereichen interagiert.</span><span class="sxs-lookup"><span data-stu-id="37602-649">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="37602-650">Ausführliche Informationen zur Verwendung von Bereichen mit Ansichten finden Sie unter [Bereiche](xref:mvc/controllers/areas) .</span><span class="sxs-lookup"><span data-stu-id="37602-650">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="37602-651">Im folgenden Beispiel wird MVC so konfiguriert, dass die herkömmliche Standardroute und eine `area` Route für einen mit dem Namen verwendet werden `area` `Blog` :</span><span class="sxs-lookup"><span data-stu-id="37602-651">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="37602-652">Im vorangehenden Code <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> wird aufgerufen, um zu erstellen `"blog_route"` .</span><span class="sxs-lookup"><span data-stu-id="37602-652">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="37602-653">Der zweite Parameter, `"Blog"` , ist der Bereichs Name.</span><span class="sxs-lookup"><span data-stu-id="37602-653">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="37602-654">Beim Abgleichen eines URL-Pfads wie `/Manage/Users/AddUser` `"blog_route"` generiert die Route die Routen Werte `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="37602-654">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="37602-655">Der `area` Routen Wert wird durch einen Standardwert für erstellt `area` .</span><span class="sxs-lookup"><span data-stu-id="37602-655">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="37602-656">Die von erstellte Route `MapAreaControllerRoute` entspricht Folgendem:</span><span class="sxs-lookup"><span data-stu-id="37602-656">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="37602-657">`MapAreaControllerRoute` erstellt mit einem Standardwert und einer Einschränkung für `area` sowie mit dem bereitgestellten Bereichsnamen (in diesem Fall `Blog`) eine Route.</span><span class="sxs-lookup"><span data-stu-id="37602-657">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="37602-658">Der Standardwert stellt sicher, dass die Route immer `{ area = Blog, ... }` erzeugt, die Einschränkung erfordert den Wert `{ area = Blog, ... }` für URL-Generierung.</span><span class="sxs-lookup"><span data-stu-id="37602-658">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="37602-659">Beim herkömmlichen Routing ist die Reihenfolge wichtig.</span><span class="sxs-lookup"><span data-stu-id="37602-659">Conventional routing is order-dependent.</span></span> <span data-ttu-id="37602-660">Im Allgemeinen sollten Routen mit Bereichen früher platziert werden, da Sie spezifischer als Routen ohne Bereich sind.</span><span class="sxs-lookup"><span data-stu-id="37602-660">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="37602-661">Im vorherigen Beispiel entsprechen die Routen Werte `{ area = Blog, controller = Users, action = AddUser }` der folgenden Aktion:</span><span class="sxs-lookup"><span data-stu-id="37602-661">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="37602-662">Das [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) -Attribut bezeichnet einen Controller als Teil eines Bereichs.</span><span class="sxs-lookup"><span data-stu-id="37602-662">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="37602-663">Dieser Controller befindet sich im `Blog` Bereich.</span><span class="sxs-lookup"><span data-stu-id="37602-663">This controller is in the `Blog` area.</span></span> <span data-ttu-id="37602-664">Controller ohne ein `[Area]` -Attribut sind keine Elemente eines Bereichs und Stimmen **nicht** mit einem Wert identisch, wenn der `area` Routen Wert durch Routing bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="37602-664">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="37602-665">Im folgenden Beispiel kann nur der erste aufgelistete Controller die Routenwerte `{ area = Blog, controller = Users, action = AddUser }` abgleichen.</span><span class="sxs-lookup"><span data-stu-id="37602-665">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="37602-666">Der Namespace der einzelnen Controller wird hier aus Gründen der Vollständigkeit angezeigt.</span><span class="sxs-lookup"><span data-stu-id="37602-666">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="37602-667">Wenn die vorangehenden Controller denselben Namespace verwenden, wird ein Compilerfehler generiert.</span><span class="sxs-lookup"><span data-stu-id="37602-667">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="37602-668">Klassennamespaces haben keine Auswirkungen auf das MVC-Routing.</span><span class="sxs-lookup"><span data-stu-id="37602-668">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="37602-669">Die ersten beiden Controller gehören zu Bereichen und werden können nur abgleichen, wenn ihr jeweiliger Bereichsname vom `area`-Routenwert bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="37602-669">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="37602-670">Der dritte Controller gehört keinem Bereich an und kann nur abgleichen, wenn vom Routing kein Wert für `area` bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="37602-670">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="37602-671">Im Hinblick auf das Erkennen *keines Werts* hat die Abwesenheit des `area`-Werts dieselben Auswirkungen, wie wenn der Wert für `area` 0 (null) oder eine leere Zeichenfolge wäre.</span><span class="sxs-lookup"><span data-stu-id="37602-671">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="37602-672">Wenn eine Aktion in einem Bereich ausgeführt wird, ist der Routen Wert für `area` als [Ambient-Wert](#ambient) für das Routing zur URL-Generierung verfügbar.</span><span class="sxs-lookup"><span data-stu-id="37602-672">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="37602-673">Das bedeutet, dass Bereiche bei der URL-Generierung wie im folgenden Beispiel dargestellt standardmäßig *beständig* sind.</span><span class="sxs-lookup"><span data-stu-id="37602-673">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="37602-674">Der folgende Code generiert eine URL zu `/Zebra/Users/AddUser` :</span><span class="sxs-lookup"><span data-stu-id="37602-674">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="37602-675">Aktions Definition</span><span class="sxs-lookup"><span data-stu-id="37602-675">Action definition</span></span>

<span data-ttu-id="37602-676">Öffentliche Methoden auf einem Controller, außer denen mit dem [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) -Attribut, sind Aktionen.</span><span class="sxs-lookup"><span data-stu-id="37602-676">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="37602-677">Beispielcode</span><span class="sxs-lookup"><span data-stu-id="37602-677">Sample code</span></span>

 * <span data-ttu-id="37602-678">Die [mydisplayrouteinfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) -Methode ist im [Beispiel Download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) enthalten und wird zum Anzeigen von Routing Informationen verwendet.</span><span class="sxs-lookup"><span data-stu-id="37602-678">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>
* <span data-ttu-id="37602-679">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="37602-679">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="37602-680">ASP.NET Core MVC verwendet [Routing-Middleware](xref:fundamentals/middleware/index), um die URLs der eingehenden Anforderungen abzugleichen und sie Aktionen zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="37602-680">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="37602-681">Routen werden im Startcode oder in Attributen definiert</span><span class="sxs-lookup"><span data-stu-id="37602-681">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="37602-682">und beschreiben, wie URL-Pfade Aktionen zugeordnet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="37602-682">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="37602-683">Sie werden auch dazu verwendet, um URLs (für Links) zu generieren, die in Antworten gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="37602-683">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="37602-684">Aktionen werden entweder herkömmlich oder über Attribute zugeordnet,</span><span class="sxs-lookup"><span data-stu-id="37602-684">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="37602-685">d.h., dass eine Route auf dem Controller oder der Aktion platziert wird.</span><span class="sxs-lookup"><span data-stu-id="37602-685">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="37602-686">Weitere Informationen finden Sie im Abschnitt [Gemischtes Routing](#routing-mixed-ref-label).</span><span class="sxs-lookup"><span data-stu-id="37602-686">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="37602-687">In diesem Artikel werden die Schnittstellen zwischen MVC und Routing und die Art und Weise erläutert, wie normale MVC-Apps Routingfeatures verwenden.</span><span class="sxs-lookup"><span data-stu-id="37602-687">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="37602-688">Informationen zum erweiterten Routing finden Sie unter [Routing in ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="37602-688">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="37602-689">Einrichten der Routing-Middleware</span><span class="sxs-lookup"><span data-stu-id="37602-689">Setting up Routing Middleware</span></span>

<span data-ttu-id="37602-690">Ihre *Configure*-Methode kann Code wie diesen enthalten:</span><span class="sxs-lookup"><span data-stu-id="37602-690">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="37602-691">Innerhalb des Aufrufs von `UseMvc` wird mit `MapRoute` eine einzelne Route erstellt, die wir `default`-Route nennen.</span><span class="sxs-lookup"><span data-stu-id="37602-691">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="37602-692">Die meisten MVC-Apps verwenden eine Route mit einer Vorlage, die der `default`-Route ähnelt.</span><span class="sxs-lookup"><span data-stu-id="37602-692">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="37602-693">TDie Routenvorlage `"{controller=Home}/{action=Index}/{id?}"` kann einem URL-Pfad wie `/Products/Details/5` abgleichen und extrahiert die Routenwerte `{ controller = Products, action = Details, id = 5 }`, indem ein Token für den Pfad erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="37602-693">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="37602-694">MVC versucht, einen Controller namens `ProductsController` zu suchen und die Aktion `Details` auszuführen:</span><span class="sxs-lookup"><span data-stu-id="37602-694">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="37602-695">In diesem Beispiel hat die Modellbindung den `id`-Parameter mit dem Wert `id = 5` beim Aufrufen dieser Aktion auf `5` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="37602-695">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="37602-696">Weitere Informationen finden Sie unter [Modellbindung](../models/model-binding.md).</span><span class="sxs-lookup"><span data-stu-id="37602-696">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="37602-697">Verwenden der `default`-Route:</span><span class="sxs-lookup"><span data-stu-id="37602-697">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="37602-698">Die Routenvorlage:</span><span class="sxs-lookup"><span data-stu-id="37602-698">The route template:</span></span>

* <span data-ttu-id="37602-699">`{controller=Home}` definiert `Home` als Standard-`controller`.</span><span class="sxs-lookup"><span data-stu-id="37602-699">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="37602-700">`{action=Index}` definiert `Index` als Standard-`action`.</span><span class="sxs-lookup"><span data-stu-id="37602-700">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="37602-701">`{id?}` definiert `id` als optional.</span><span class="sxs-lookup"><span data-stu-id="37602-701">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="37602-702">Standardmäßige und optionale Routenparameter müssen nicht im URL-Pfad vorhanden sein, damit es eine Übereinstimmung gibt.</span><span class="sxs-lookup"><span data-stu-id="37602-702">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="37602-703">Eine ausführliche Beschreibung der Syntax der Routenvorlage finden Sie unter [Routenvorlagenreferenz](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="37602-703">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="37602-704">`"{controller=Home}/{action=Index}/{id?}"` gleicht den URL-Pfad `/` ab und erzeugt die Routenwerte `{ controller = Home, action = Index }`.</span><span class="sxs-lookup"><span data-stu-id="37602-704">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="37602-705">Die Werte für `controller` und `action` verwenden die Standardwerte. `id` erzeugt keine Werte, da kein entsprechendes Segment im URL-Pfad vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="37602-705">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="37602-706">MVC nutzt diese Routenwerte, um die `HomeController`- und `Index`-Aktion auszuwählen:</span><span class="sxs-lookup"><span data-stu-id="37602-706">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="37602-707">Mit dieser Controllerdefinition und dieser Routenvorlage wird die `HomeController.Index`-Aktion für jeden der folgenden URL-Pfade ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="37602-707">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="37602-708">Mit der Hilfsmethode `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="37602-708">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="37602-709">kann Folgendes ersetzt werden:</span><span class="sxs-lookup"><span data-stu-id="37602-709">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="37602-710">`UseMvc` und `UseMvcWithDefaultRoute` fügen der Middleware-Pipeline eine Instanz von `RouterMiddleware` hinzu.</span><span class="sxs-lookup"><span data-stu-id="37602-710">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="37602-711">MVC interagiert nicht direkt mit der Middleware und verarbeitet Anforderungen mithilfe von Routing.</span><span class="sxs-lookup"><span data-stu-id="37602-711">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="37602-712">MVC ist über eine Instanz von `MvcRouteHandler` mit den Routen verbunden.</span><span class="sxs-lookup"><span data-stu-id="37602-712">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="37602-713">Der Code in `UseMvc` ähnelt Folgendem:</span><span class="sxs-lookup"><span data-stu-id="37602-713">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="37602-714">`UseMvc` definiert keine Routen direkt, sondern fügt der Routenauflistung einen Platzhalter für die `attribute`-Route hinzu.</span><span class="sxs-lookup"><span data-stu-id="37602-714">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="37602-715">Die Überladung `UseMvc(Action<IRouteBuilder>)` ermöglicht es Ihnen, Ihre eigenen Routen hinzuzufügen und unterstützt darüber hinaus das Routingattribut.</span><span class="sxs-lookup"><span data-stu-id="37602-715">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="37602-716">`UseMvc` und alle Varianten fügen einen Platzhalter für die Attributroute hinzu. Attributrouting ist immer verfügbar, unabhängig von der Konfiguration von `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="37602-716">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="37602-717">`UseMvcWithDefaultRoute` definiert eine Standardroute und unterstützt Attributrouting.</span><span class="sxs-lookup"><span data-stu-id="37602-717">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="37602-718">Der Abschnitt [Attributrouting](#attribute-routing-ref-label) enthält weitere Informationen zu dem Thema.</span><span class="sxs-lookup"><span data-stu-id="37602-718">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="37602-719">Herkömmliches Routing</span><span class="sxs-lookup"><span data-stu-id="37602-719">Conventional routing</span></span>

<span data-ttu-id="37602-720">Die `default`-Route:</span><span class="sxs-lookup"><span data-stu-id="37602-720">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="37602-721">Der vorangehende Code ist ein Beispiel für ein herkömmliches Routing.</span><span class="sxs-lookup"><span data-stu-id="37602-721">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="37602-722">Dieser Stil wird als herkömmliches Routing bezeichnet, da er eine *Konvention* für URL-Pfade festlegt:</span><span class="sxs-lookup"><span data-stu-id="37602-722">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="37602-723">Das erste Pfad Segment wird dem Controller Namen zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="37602-723">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="37602-724">Die zweite wird dem Aktions Namen zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="37602-724">The second maps to the action name.</span></span>
* <span data-ttu-id="37602-725">Das dritte Segment wird für eine optionale verwendet `id` .</span><span class="sxs-lookup"><span data-stu-id="37602-725">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="37602-726">`id`wird einer Modell Entität zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="37602-726">`id` maps to a model entity.</span></span>

<span data-ttu-id="37602-727">Mit dieser `default`-Route wir der URL-Pfad `/Products/List` der `ProductsController.List`-Aktion und `/Blog/Article/17``BlogController.Article` zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="37602-727">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="37602-728">Diese Zuordnung basiert **ausschließlich** auf den Controller- und Aktionsnamen und nicht auf Namespaces, Speicherorten für Quelldateien oder Methodenparametern.</span><span class="sxs-lookup"><span data-stu-id="37602-728">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="37602-729">Die Kombination aus herkömmlichem Routing und Standardrouting ermöglicht es Ihnen, Anwendungen schnell zu erstellen, ohne für jede definierte Aktion ein neues URL-Muster entwerfen zu müssen.</span><span class="sxs-lookup"><span data-stu-id="37602-729">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="37602-730">Bei Anwendungen mit Aktionen im CRUD-Stil können einheitliche URLs auf allen Controllern dabei helfen, den Code zu vereinfachen und die UI vorhersehbarer zu machen.</span><span class="sxs-lookup"><span data-stu-id="37602-730">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="37602-731">`id` wird von der Routenvorlage als optional definiert, was bedeutet, dass Ihre Aktionen ausgeführt werden, ohne dass die ID als Teil der URL bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="37602-731">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="37602-732">Wenn `id` in der URL ausgelassen wird, wird es üblicherweise von der Modellbindung auf `0` festgelegt, und daher wird in der Datenbank keine Entität gefunden, die `id == 0` entspricht.</span><span class="sxs-lookup"><span data-stu-id="37602-732">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="37602-733">Mit dem Attributrouting können Sie präzise steuern, für welche Aktionen die ID erforderlich ist und für welche nicht.</span><span class="sxs-lookup"><span data-stu-id="37602-733">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="37602-734">Gemäß der Konvention enthält die Dokumentation optionale Parameter wie `id`, wenn sie wahrscheinlich in der richtigen Verwendung angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="37602-734">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="37602-735">Mehrere Routen</span><span class="sxs-lookup"><span data-stu-id="37602-735">Multiple routes</span></span>

<span data-ttu-id="37602-736">Sie können mehrere Routen innerhalb von `UseMvc` hinzufügen, indem Sie weitere Aufrufe von `MapRoute` hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="37602-736">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="37602-737">So können Sie mehrere Konventionen definieren oder herkömmlichen Routen hinzufügen, die einer bestimmten Aktion zugeordnet sind, z.B.:</span><span class="sxs-lookup"><span data-stu-id="37602-737">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="37602-738">Die `blog`-Route hier ist eine *dedizierte herkömmliche Route*, d.h., dass das herkömmliche Routingsystem verwendet wird, aber einer bestimmten Aktion zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="37602-738">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="37602-739">Da `controller` und `action` nicht als Parameter in der Routenvorlage vorkommen, können sie nur die Standardwerte haben. Daher wird diese Route immer der Aktion `BlogController.Article` zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="37602-739">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="37602-740">Die Routen in der Routenauflistung sind geordnet und werden in der Reihenfolge verarbeitet, in der sie hinzugefügt wurden.</span><span class="sxs-lookup"><span data-stu-id="37602-740">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="37602-741">In diesem Beispiel wird daher die Route `blog` vor der Route `default` überprüft.</span><span class="sxs-lookup"><span data-stu-id="37602-741">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="37602-742">*Dedizierte herkömmliche Routen* verwenden häufig **catch-all-** Routen Parameter wie `{*article}` , um den verbleibenden Teil des URL-Pfads zu erfassen.</span><span class="sxs-lookup"><span data-stu-id="37602-742">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="37602-743">Dies kann eine Route „zu gierig“ machen, d.h., dass sie alle URLs abgleicht, die eigentlich von anderen Routen abgeglichen werden sollten.</span><span class="sxs-lookup"><span data-stu-id="37602-743">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="37602-744">Fügen Sie die „gierigen“ Routen weiter unten in die Routingtabelle ein, um dieses Problem zu beheben.</span><span class="sxs-lookup"><span data-stu-id="37602-744">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="37602-745">Fallback</span><span class="sxs-lookup"><span data-stu-id="37602-745">Fallback</span></span>

<span data-ttu-id="37602-746">Im Rahmen der Anforderungsverarbeitung überprüft MVC, ob mit den Routenwerten ein Controller und eine Aktion in Ihrer Anwendung gefunden werden können.</span><span class="sxs-lookup"><span data-stu-id="37602-746">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="37602-747">Falls die Routenwerte mit keiner Aktion übereinstimmen, gilt die Route nicht als Übereinstimmung, und die nächste Route wird überprüft.</span><span class="sxs-lookup"><span data-stu-id="37602-747">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="37602-748">Dabei spricht man von einem *Fallback*. Dieser Vorgang soll Szenarios vereinfachen, bei denen sich herkömmliche Routen überschneiden.</span><span class="sxs-lookup"><span data-stu-id="37602-748">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="37602-749">Aktionen eindeutig zuordnen</span><span class="sxs-lookup"><span data-stu-id="37602-749">Disambiguating actions</span></span>

<span data-ttu-id="37602-750">Wenn zwei Aktionen beim Routing übereinstimmen, muss MVC beide analysieren und die beste auswählen oder eine Ausnahme auslösen.</span><span class="sxs-lookup"><span data-stu-id="37602-750">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="37602-751">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="37602-751">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="37602-752">Dieser Controller definiert zwei Aktionen, die mit dem URL-Pfad `/Products/Edit/17` übereinstimmen und die Daten `{ controller = Products, action = Edit, id = 17 }` weiterleiten.</span><span class="sxs-lookup"><span data-stu-id="37602-752">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="37602-753">Dies ist ein typisches Muster für MVC-Controller, in dem `Edit(int)` ein Formular zum Bearbeiten eines Produkts anzeigt und `Edit(int, Product)` das bereitgestellte Formular verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="37602-753">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="37602-754">Um dies zu ermöglichen, muss MVC `Edit(int, Product)` auswählen, wenn die Anforderung HTTP-`POST` ist, und `Edit(int)`, wenn das HTTP-Verb ein anderes ist.</span><span class="sxs-lookup"><span data-stu-id="37602-754">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="37602-755">`HttpPostAttribute` (`[HttpPost]`) ist eine Implementierung von `IActionConstraint`, bei der die Aktion nur ausgewählt werden darf, wenn das HTTP-Verb `POST` ist.</span><span class="sxs-lookup"><span data-stu-id="37602-755">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="37602-756">Aufgrund des Vorhandenseins von `IActionConstraint` ist `Edit(int, Product)` die „bessere“ Übereinstimmung als `Edit(int)`, sodass `Edit(int, Product)` zuerst überprüft wird.</span><span class="sxs-lookup"><span data-stu-id="37602-756">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="37602-757">Benutzerdefinierte `IActionConstraint`-Implementierungen müssen Sie nur in speziellen Szenarios schreiben. Es ist jedoch wichtig, die Rolle von Attributen wie `HttpPostAttribute` zu kennen, denn ähnliche Attribute sind auch für andere HTTP-Verben definiert.</span><span class="sxs-lookup"><span data-stu-id="37602-757">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="37602-758">Beim herkömmlichen Routing nutzen Aktionen oft denselben Aktionsnamen, wenn sie Teil eines `show form -> submit form`-Workflows sind.</span><span class="sxs-lookup"><span data-stu-id="37602-758">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="37602-759">Die Vorteile dessen werden deutlicher, wenn Sie den Abschnitt [Verstehen von IActionConstraint](#understanding-iactionconstraint) gelesen haben.</span><span class="sxs-lookup"><span data-stu-id="37602-759">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="37602-760">Wenn mehrere Routen übereinstimmen und MVC die „beste“ nicht bestimmen kann, löst es eine `AmbiguousActionException` aus.</span><span class="sxs-lookup"><span data-stu-id="37602-760">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="37602-761">Routennamen</span><span class="sxs-lookup"><span data-stu-id="37602-761">Route names</span></span>

<span data-ttu-id="37602-762">Die Zeichenfolgen `"blog"` und `"default"` in den folgenden Beispielen sind Routennamen:</span><span class="sxs-lookup"><span data-stu-id="37602-762">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="37602-763">Der Routenname gibt der Route einen logischen Namen, damit die benannte Route bei der URL-Generierung verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="37602-763">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="37602-764">Dadurch wird die URL-Erstellung erheblich vereinfacht, obwohl die Reihenfolge der Routen die URL-Generierung verkomplizieren sollte.</span><span class="sxs-lookup"><span data-stu-id="37602-764">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="37602-765">Routennamen müssen anwendungsweit eindeutig sein.</span><span class="sxs-lookup"><span data-stu-id="37602-765">Route names must be unique application-wide.</span></span>

<span data-ttu-id="37602-766">Routennamen haben keine Auswirkung auf die URL-Zuordnung oder die Verarbeitung von Anforderungen. Sie dienen nur der URL-Generierung.</span><span class="sxs-lookup"><span data-stu-id="37602-766">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="37602-767">Unter [Routing](xref:fundamentals/routing) finden Sie weitere ausführliche Informationen zur URL-Generierung, einschließlich der Generierung in MVC-spezifischen Hilfsprogrammen.</span><span class="sxs-lookup"><span data-stu-id="37602-767">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="37602-768">Attributrouting</span><span class="sxs-lookup"><span data-stu-id="37602-768">Attribute routing</span></span>

<span data-ttu-id="37602-769">Beim Attributrouting werden Aktionen mithilfe von Attributen direkt Routenvorlagen zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="37602-769">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="37602-770">Im folgenden Beispiel wird `app.UseMvc();` in der `Configure`-Methode verwendet, und es wird keine Route übergeben.</span><span class="sxs-lookup"><span data-stu-id="37602-770">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="37602-771">`HomeController` entspricht URLs, die denen ähneln, denen die Standardroute `{controller=Home}/{action=Index}/{id?}` entsprechen würde:</span><span class="sxs-lookup"><span data-stu-id="37602-771">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

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

<span data-ttu-id="37602-772">Die `HomeController.Index()`-Aktion wird für die URL-Pfade `/`, `/Home` und `/Home/Index` jeweils ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="37602-772">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="37602-773">In diesem Beispiel wird ein wichtiger Unterschied beim Programmieren zwischen dem Attributrouting und dem herkömmlichen Routing hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="37602-773">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="37602-774">Attributrouting erfordert mehr Eingabe, um eine Route anzugeben. Die herkömmliche Standardroute verarbeitet auch kürzere Routen.</span><span class="sxs-lookup"><span data-stu-id="37602-774">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="37602-775">Attributrouting ermöglicht jedoch (und erfordert auch) die präzise Kontrolle der Routenvorlagen, die für die einzelnen Aktionen gelten.</span><span class="sxs-lookup"><span data-stu-id="37602-775">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="37602-776">Beim Attributrouting haben der Controller- und der Aktionsname **keine** Auswirkung darauf, welche Aktion ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="37602-776">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="37602-777">In diesem Beispiel werden dieselben URLs wie im vorherigen Beispiel abgeglichen.</span><span class="sxs-lookup"><span data-stu-id="37602-777">This example will match the same URLs as the previous example.</span></span>

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
> <span data-ttu-id="37602-778">Die oben genannten Routenvorlagen definieren keine Routenparameter für `action`, `area` und `controller`.</span><span class="sxs-lookup"><span data-stu-id="37602-778">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="37602-779">Diese Routenparameter sind in Attributrouten nicht zulässig.</span><span class="sxs-lookup"><span data-stu-id="37602-779">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="37602-780">Da die Routenvorlage bereits einer Aktion zugeordnet ist, wäre es nicht sinnvoll, den Aktionsnamen aus der URL zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="37602-780">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="37602-781">Attributrouting mit Http[Verb]-Attributen</span><span class="sxs-lookup"><span data-stu-id="37602-781">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="37602-782">Beim Attributrouting können auch `Http[Verb]`-Attribute wie `HttpPostAttribute` verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="37602-782">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="37602-783">Alle diese Attribute können eine Routenvorlage akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="37602-783">All of these attributes can accept a route template.</span></span> <span data-ttu-id="37602-784">In diesem Beispiel werden zwei Aktionen gezeigt, die derselben Routenvorlage zugeordnet sind:</span><span class="sxs-lookup"><span data-stu-id="37602-784">This example shows two actions that match the same route template:</span></span>

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

<span data-ttu-id="37602-785">Für einen URL-Pfad wie `/products` wird die Aktion `ProductsApi.ListProducts` ausgeführt, wenn das HTTP-Verb `GET` ist, und `ProductsApi.CreateProduct` wird ausgeführt, wenn das HTTP-Verb `POST` entspricht.</span><span class="sxs-lookup"><span data-stu-id="37602-785">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="37602-786">Attributrouting gleicht zuerst die URL gegen die Routenvorlagen ab, die von den Routenattributen definiert werden.</span><span class="sxs-lookup"><span data-stu-id="37602-786">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="37602-787">Sobald eine Routenvorlage übereinstimmt, werden `IActionConstraint`-Einschränkungen angewendet, um zu bestimmen, welche Aktionen ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="37602-787">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="37602-788">Beim Erstellen einer REST-API wird `[Route(...)]` selten für eine Aktionsmethode verwendet, die Aktion alle HTTP-Methoden akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="37602-788">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="37602-789">Es ist besser, das spezifischere `Http*Verb*Attributes` zu nutzen, um präzise anzugeben, was Ihre API unterstützt.</span><span class="sxs-lookup"><span data-stu-id="37602-789">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="37602-790">REST-API-Clients sollten wissen, welche Pfade und HTTP-Verben bestimmten logischen Operationen entsprechen.</span><span class="sxs-lookup"><span data-stu-id="37602-790">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="37602-791">Da eine Attributroute für eine bestimmte Aktion gilt, ist es einfach, Parameter als Teil der Routenvorlagendefinition erforderlich festzulegen.</span><span class="sxs-lookup"><span data-stu-id="37602-791">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="37602-792">In diesem Beispiel ist `id` als Teil des URL-Pfads erforderlich.</span><span class="sxs-lookup"><span data-stu-id="37602-792">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="37602-793">Die `ProductsApi.GetProduct(int)`-Aktion wird für einen URL-Pfad wie `/products/3` ausgeführt, jedoch nicht für einen URL-Pfad wie `/products`.</span><span class="sxs-lookup"><span data-stu-id="37602-793">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="37602-794">Eine vollständige Beschreibung und Routenvorlagen und dazugehörige Optionen finden Sie unter [Routing in ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="37602-794">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="37602-795">Routenname</span><span class="sxs-lookup"><span data-stu-id="37602-795">Route Name</span></span>

<span data-ttu-id="37602-796">Im folgenden Code wird ein *Routenname* von `Products_List` definiert:</span><span class="sxs-lookup"><span data-stu-id="37602-796">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="37602-797">Routennamen können verwendet werden, um basierend auf einer bestimmten Route eine URL zu generieren.</span><span class="sxs-lookup"><span data-stu-id="37602-797">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="37602-798">Routennamen haben keine Auswirkung auf das URL-Zuordnungsverhalten des Routings und dienen nur zur URL-Generierung.</span><span class="sxs-lookup"><span data-stu-id="37602-798">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="37602-799">Routennamen müssen anwendungsweit eindeutig sein.</span><span class="sxs-lookup"><span data-stu-id="37602-799">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="37602-800">Vergleichen Sie dies mit der herkömmlichen *Standardroute*, die den `id`-Parameter als optional definiert (`{id?}`).</span><span class="sxs-lookup"><span data-stu-id="37602-800">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="37602-801">APIs präzise angeben zu können, hat Vorteile, z.B. können `/products` und `/products/5` an unterschiedliche Aktionen gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="37602-801">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="37602-802">Kombinieren von Routen</span><span class="sxs-lookup"><span data-stu-id="37602-802">Combining routes</span></span>

<span data-ttu-id="37602-803">Um Attributrouting weniger repetitiv zu gestalten, werden Routenattribute auf dem Controller mit Routenattributen auf den einzelnen Aktionen kombiniert.</span><span class="sxs-lookup"><span data-stu-id="37602-803">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="37602-804">Alle auf dem Controller definierten Routenvorlagen werden den Routenvorlagen auf den Aktionen vorangestellt.</span><span class="sxs-lookup"><span data-stu-id="37602-804">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="37602-805">Wenn Routenattribute auf dem Controller platziert werden, verwenden **alle** Aktionen im Controller Attributrouting.</span><span class="sxs-lookup"><span data-stu-id="37602-805">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

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

<span data-ttu-id="37602-806">In diesem Beispiel kann der URL-Pfad `/products``ProductsApi.ListProducts` und der URL-Pfad `/products/5``ProductsApi.GetProduct(int)` zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="37602-806">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="37602-807">Beide Aktionen gleichen nur „HTTP `GET`“ ab, weil sie mit `HttpGetAttribute` markiert sind.</span><span class="sxs-lookup"><span data-stu-id="37602-807">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="37602-808">Routenvorlagen, die auf eine Aktion angewendet werden, die mit einem `/` oder `~/` beginnen, können nicht mit Routenvorlagen kombiniert werden, die auf den Controller angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="37602-808">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="37602-809">In diesem Beispiel werden mehrere URL-Pfade zugeordnet, die der *Standardroute* ähneln.</span><span class="sxs-lookup"><span data-stu-id="37602-809">This example matches a set of URL paths similar to the *default route*.</span></span>

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

### <a name="ordering-attribute-routes"></a><span data-ttu-id="37602-810">Ordnen der Attributrouten</span><span class="sxs-lookup"><span data-stu-id="37602-810">Ordering attribute routes</span></span>

<span data-ttu-id="37602-811">Im Gegensatz zu herkömmlichen Routen, die in einer definierten Reihenfolge ausgeführt werden, erstellt das Attribut Routing eine Struktur und gleicht alle Routen gleichzeitig ab.</span><span class="sxs-lookup"><span data-stu-id="37602-811">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="37602-812">Der Vorgang wird also ausgeführt, als ob die Routeneinträge in der idealen Reihenfolge platziert worden wären: Die spezifischsten Routen können also vor den allgemeineren ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="37602-812">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="37602-813">Eine Route wie `blog/search/{topic}` ist z.B. spezifischer als eine Route wie `blog/{*article}`.</span><span class="sxs-lookup"><span data-stu-id="37602-813">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="37602-814">Logisch gesehen wird die Route `blog/search/{topic}` standardmäßig zuerst ausgeführt, da dies die einzig sinnvolle Reihenfolge ist.</span><span class="sxs-lookup"><span data-stu-id="37602-814">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="37602-815">Beim herkömmlichen Routing ist der Entwickler verantwortlich dafür, die Routen in die gewünschte Reihenfolge zu bringen.</span><span class="sxs-lookup"><span data-stu-id="37602-815">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="37602-816">Attributrouten können mithilfe der `Order`-Eigenschaft aller vom Framework bereitgestellten Routenattribute eine Reihenfolge konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="37602-816">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="37602-817">Routen werden entsprechend einer aufsteigenden Reihenfolge der `Order`-Eigenschaft verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="37602-817">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="37602-818">Die Standardreihenfolge ist `0`.</span><span class="sxs-lookup"><span data-stu-id="37602-818">The default order is `0`.</span></span> <span data-ttu-id="37602-819">Wird für eine Route `Order = -1` festgelegt, wird sie vor denjenigen Routen ausgeführt, für die keine Reihenfolge festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="37602-819">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="37602-820">Wird für eine Route `Order = 1` festgelegt, wird sie ausgeführt, nachdem die Standardroutenreihenfolge ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="37602-820">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="37602-821">Vermeiden Sie eine Abhängigkeit von `Order`.</span><span class="sxs-lookup"><span data-stu-id="37602-821">Avoid depending on `Order`.</span></span> <span data-ttu-id="37602-822">Wenn der URL-Raum explizite Reihenfolgenwerte erfordert, um korrekt weiterzuleiten, ist es wahrscheinlich auch für Clients verwirrend.</span><span class="sxs-lookup"><span data-stu-id="37602-822">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="37602-823">Beim Attributrouting wird im Allgemeinen mithilfe der URL-Zuordnung die richtige Route ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="37602-823">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="37602-824">Wenn die für die URL-Generierung verwendete Standardreihenfolge nicht funktioniert, ist es meist einfacher, Routennamen als Außerkraftsetzung zu verwenden, statt die `Order`-Eigenschaft anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="37602-824">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

Razor<span data-ttu-id="37602-825"> Pages-Routing und MVC Controller-Routing verwenden eine gemeinsame Implementierung.</span><span class="sxs-lookup"><span data-stu-id="37602-825"> Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="37602-826">Informationen zur Routen Reihenfolge in den Razor Seiten Themen finden Sie unter [ Razor pages Route und App Conventions: Route Order](xref:razor-pages/razor-pages-conventions#route-order).</span><span class="sxs-lookup"><span data-stu-id="37602-826">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="37602-827">Ersetzen von Token in Routenvorlagen ([controller], [action] [area])</span><span class="sxs-lookup"><span data-stu-id="37602-827">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="37602-828">Zur einfacheren Unterstützung unterstützen Attribut Routen die *Tokenersetzung* durch Einschließen eines Tokens in eckige Klammern ( `[` , `]` ).</span><span class="sxs-lookup"><span data-stu-id="37602-828">For convenience, attribute routes support *token replacement* by enclosing a token in square-brackets (`[`, `]`).</span></span> <span data-ttu-id="37602-829">Die Token `[action]`, `[area]` und `[controller]` werden durch die Werte der Aktionsnamen, den Namen des Bereichs und des Controllers der Aktion ersetzt, in dem die Route definiert ist.</span><span class="sxs-lookup"><span data-stu-id="37602-829">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="37602-830">In dem folgenden Beispiel entsprechen die Aktionen wie in den Kommentaren beschrieben URL-Pfaden:</span><span class="sxs-lookup"><span data-stu-id="37602-830">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="37602-831">Die Tokenersetzung tritt im letzten Schritt der Erstellung von Attributrouten auf.</span><span class="sxs-lookup"><span data-stu-id="37602-831">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="37602-832">Der folgende Code verhält sich genauso wie der aus dem obigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="37602-832">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="37602-833">Attributrouten können auch mit Vererbung kombiniert werden.</span><span class="sxs-lookup"><span data-stu-id="37602-833">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="37602-834">Dies ist besonders effektiv in Kombination mit der Tokenersetzung.</span><span class="sxs-lookup"><span data-stu-id="37602-834">This is particularly powerful combined with token replacement.</span></span>

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

<span data-ttu-id="37602-835">Tokenersetzung gilt auch für Routennamen, die durch Attributrouten definiert werden.</span><span class="sxs-lookup"><span data-stu-id="37602-835">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="37602-836">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generiert für jede Aktion einen eindeutigen Routennamen.</span><span class="sxs-lookup"><span data-stu-id="37602-836">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="37602-837">Damit das Trennzeichen `[` oder `]` der Tokenersetzungs-Literalzeichenfolge bei einem Abgleich gefunden wird, muss es doppelt vorhanden sein (`[[` oder `]]`), was einem Escapezeichen entspricht.</span><span class="sxs-lookup"><span data-stu-id="37602-837">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="37602-838">Verwenden eines Parametertransformators zum Anpassen der Tokenersetzung</span><span class="sxs-lookup"><span data-stu-id="37602-838">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="37602-839">Die Tokenersetzung kann mit einem Parametertransformator angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="37602-839">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="37602-840">Ein Parametertransformator implementiert `IOutboundParameterTransformer` und wandelt den Wert der Parameter um.</span><span class="sxs-lookup"><span data-stu-id="37602-840">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="37602-841">Beispielsweise ändert ein benutzerdefinierter `SlugifyParameterTransformer`-Parametertransformator den Routenwert `SubscriptionManagement` in `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="37602-841">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="37602-842">Die `RouteTokenTransformerConvention` ist eine Anwendungsmodellkonvention, die Folgendes ausführt:</span><span class="sxs-lookup"><span data-stu-id="37602-842">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="37602-843">Wendet einen angegebenen Parametertransformator auf alle Attributrouten in der App an.</span><span class="sxs-lookup"><span data-stu-id="37602-843">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="37602-844">Passt die Tokenwerte für Attributrouten bei ihrer Ersetzung an.</span><span class="sxs-lookup"><span data-stu-id="37602-844">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="37602-845">Die `RouteTokenTransformerConvention` wird als Option in `ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="37602-845">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

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

### <a name="multiple-routes"></a><span data-ttu-id="37602-846">Mehrere Routen</span><span class="sxs-lookup"><span data-stu-id="37602-846">Multiple Routes</span></span>

<span data-ttu-id="37602-847">Attributrouting unterstützt das Definieren mehrerer Routen, die zu derselben Aktion führen.</span><span class="sxs-lookup"><span data-stu-id="37602-847">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="37602-848">Dies wird am häufigsten beim Imitieren des Verhaltens der *herkömmlichen Standardroute* verwendet. Im folgenden Beispiel wird dies gezeigt:</span><span class="sxs-lookup"><span data-stu-id="37602-848">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="37602-849">Wenn mehrere Routenattribute auf dem Controller platziert werden, wird jedes mit jedem der Routenattribute auf den Aktionsmethoden kombiniert.</span><span class="sxs-lookup"><span data-stu-id="37602-849">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

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

<span data-ttu-id="37602-850">Werden mehrere Routenattribute (die `IActionConstraint` implementieren) auf einer Aktion platziert, wird jede Aktionseinschränkung mit der Routenvorlage aus dem Attribut kombiniert, das sie definiert.</span><span class="sxs-lookup"><span data-stu-id="37602-850">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

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
> <span data-ttu-id="37602-851">Obwohl das Verwenden mehrerer Routen für Aktionen sehr hilfreich scheint, ist es empfehlenswerter, den URL-Raum der Anwendung einfach und gut definiert zu halten.</span><span class="sxs-lookup"><span data-stu-id="37602-851">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="37602-852">Verwenden Sie nur mehrere Routen für Aktionen, wenn dies notwendig ist, z.B. um vorhandene Clients zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="37602-852">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="37602-853">Angeben von optionalen Attributroutenparametern, Standardwerten und Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="37602-853">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="37602-854">Attributrouten unterstützen dieselbe Inline-Syntax wie herkömmliche Routen, um optionale Parameter, Standardwerte und Einschränkungen anzugeben.</span><span class="sxs-lookup"><span data-stu-id="37602-854">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="37602-855">Eine ausführliche Beschreibung der Syntax der Routenvorlage finden Sie unter [Routenvorlagenreferenz](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="37602-855">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="37602-856">Benutzerdefinierte Routenattribute mithilfe von `IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="37602-856">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="37602-857">Alle im Framework bereitgestellten Routenattribute (`[Route(...)]`, `[HttpGet(...)]` usw.) implementieren die `IRouteTemplateProvider`-Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="37602-857">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="37602-858">MVC sucht in Controllerklassen und Aktionsmethoden nach Attributen, wenn die Anwendung gestartet wird, und verwendet diejenigen, die `IRouteTemplateProvider` implementieren, um die anfänglichen Routen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="37602-858">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="37602-859">Sie können `IRouteTemplateProvider` implementieren, um eigene Routenattribute zu definieren.</span><span class="sxs-lookup"><span data-stu-id="37602-859">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="37602-860">Jeder `IRouteTemplateProvider` lässt Sie eine einzelne Route mit einer benutzerdefinierten Routenvorlage, Reihenfolge und einem benutzerdefinierten Namen definieren:</span><span class="sxs-lookup"><span data-stu-id="37602-860">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="37602-861">Das Attribut aus dem obigen Beispiel legt `Template` automatisch auf `"api/[controller]"` fest, wenn `[MyApiController]` angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="37602-861">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="37602-862">Anpassen von Attributrouten mithilfe des Anwendungsmodells</span><span class="sxs-lookup"><span data-stu-id="37602-862">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="37602-863">Das *Anwendungsmodell* ist ein Objektmodell, das beim Starten aus allen Metadaten von MVC erstellt wird, um Ihre Aktionen weiterzuleiten und auszuführen.</span><span class="sxs-lookup"><span data-stu-id="37602-863">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="37602-864">Das *Anwendungsmodell* enthält alle Daten, die aus Routenattributen (über `IRouteTemplateProvider`) erfasst wurden.</span><span class="sxs-lookup"><span data-stu-id="37602-864">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="37602-865">Sie können *Konventionen* schreiben, damit das Anwendungsmodell beim Startzeitpunkt das Routingverhalten anpasst.</span><span class="sxs-lookup"><span data-stu-id="37602-865">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="37602-866">Dieser Abschnitt enthält ein einfaches Beispiel für das Anpassen des Routings mithilfe des Anwendungsmodells.</span><span class="sxs-lookup"><span data-stu-id="37602-866">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="37602-867">Gemischtes Routing: Attributrouting vs. herkömmliches Routing</span><span class="sxs-lookup"><span data-stu-id="37602-867">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="37602-868">MVC-Anwendungen können herkömmliches Routing und Attributrouting kombinieren.</span><span class="sxs-lookup"><span data-stu-id="37602-868">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="37602-869">In der Regel werden herkömmliche Routen für Controller verwendet, die für Browser-HTML-Seiten gedacht sind, und das Attributrouting für Controller für REST-APIs.</span><span class="sxs-lookup"><span data-stu-id="37602-869">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="37602-870">Aktionen werden entweder herkömmlich oder über Attribute zugeordnet,</span><span class="sxs-lookup"><span data-stu-id="37602-870">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="37602-871">d.h., dass eine Route auf dem Controller oder der Aktion platziert wird.</span><span class="sxs-lookup"><span data-stu-id="37602-871">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="37602-872">Aktionen, die Attributrouten definieren, können nicht über die herkömmliche Routen und umgekehrt erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="37602-872">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="37602-873">Wenn nur **ein** Routenattribut auf dem Controller platziert wird, werden alle Aktionen im Controller über Attribute zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="37602-873">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="37602-874">Die beiden Routingmethoden unterscheiden sich in dem Prozess, der angewendet wird, nachdem eine URL einer Routenvorlage zugeordnet wurde.</span><span class="sxs-lookup"><span data-stu-id="37602-874">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="37602-875">Beim herkömmlichen Routing dienen die Routenwerte aus der Zuordnung dazu, aus einer Nachschlagetabelle aller herkömmlich zugeordneten Aktionen die Aktion und den Controller auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="37602-875">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="37602-876">Beim Attributrouting ist jede Vorlage bereits einer Aktion zugeordnet, und keine weitere Suche ist erforderlich.</span><span class="sxs-lookup"><span data-stu-id="37602-876">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="37602-877">Komplexe Segmente</span><span class="sxs-lookup"><span data-stu-id="37602-877">Complex segments</span></span>

<span data-ttu-id="37602-878">Komplexe Segmente (z.B. `[Route("/dog{token}cat")]`) werden von rechts nach links auf eine nicht gierige Weise durch entsprechende Literale verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="37602-878">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="37602-879">Eine entsprechende Beschreibung finden Sie im [Quellcode](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296).</span><span class="sxs-lookup"><span data-stu-id="37602-879">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="37602-880">Weitere Informationen finden Sie in [diesem Thema](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span><span class="sxs-lookup"><span data-stu-id="37602-880">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="37602-881">URL-Generierung</span><span class="sxs-lookup"><span data-stu-id="37602-881">URL Generation</span></span>

<span data-ttu-id="37602-882">MVC-Anwendungen können Routingfeatures zur URL-Generierung verwenden, um URL-Links zu Aktionen zu generieren.</span><span class="sxs-lookup"><span data-stu-id="37602-882">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="37602-883">Durch das Generieren von URLs müssen URLs nicht mehr hartcodiert werden, sodass der Code robuster und leichter verwaltbar wird.</span><span class="sxs-lookup"><span data-stu-id="37602-883">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="37602-884">Dieser Abschnitt konzentriert sich auf die von MVC bereitgestellte URL-Generierung und befasst sich nur kurz mit der Funktionsweise.</span><span class="sxs-lookup"><span data-stu-id="37602-884">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="37602-885">Eine detaillierte Beschreibung der URL-Generierung finden Sie unter [Routing in ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="37602-885">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="37602-886">Die `IUrlHelper`-Schnittstelle ist die zugrunde liegende Infrastruktur zwischen MVC und dem Routing zur URL-Generierung.</span><span class="sxs-lookup"><span data-stu-id="37602-886">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="37602-887">Eine Instanz von `IUrlHelper` ist über die `Url`-Eigenschaft in Controllern, Ansichten und Ansichtskomponenten verfügbar.</span><span class="sxs-lookup"><span data-stu-id="37602-887">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="37602-888">In diesem Beispiel wird die `IUrlHelper`-Schnittstelle von der `Controller.Url`-Eigenschaft dazu verwendet, eine URL in einer anderen Aktion zu generieren.</span><span class="sxs-lookup"><span data-stu-id="37602-888">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="37602-889">Wenn die Anwendung die herkömmliche Standardroute verwendet, ist der Wert der `url`-Variable die URL-Pfadzeichenfolge `/UrlGeneration/Destination`.</span><span class="sxs-lookup"><span data-stu-id="37602-889">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="37602-890">Dieser URL-Pfad wird vom Routing erstellt, indem Routenwerte aus der aktuellen Anforderung (Umgebungswerte) mit den an `Url.Action` übergebenen Werten kombiniert und anschließend in die Routenvorlage eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="37602-890">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="37602-891">Der Wert eines jeden Routenparameters wird in der Routenvorlage durch die entsprechenden Namen mit den Werten und Umgebungswerten ersetzt.</span><span class="sxs-lookup"><span data-stu-id="37602-891">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="37602-892">Ein Routenparameter ohne Wert kann einen Standardwert verwenden, wenn er über einen verfügt, oder übersprungen werden, wenn er optional ist (wie im Fall von `id` in diesem Beispiel).</span><span class="sxs-lookup"><span data-stu-id="37602-892">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="37602-893">Die URL-Generierung schlägt fehl, wenn ein erforderlicher Routenparameter keinen entsprechenden Wert besitzt.</span><span class="sxs-lookup"><span data-stu-id="37602-893">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="37602-894">Wenn die URL-Generierung für eine Route fehlschlägt, wird die nächste Route ausprobiert, bis alle Routen getestet wurden oder eine Übereinstimmung gefunden wurde.</span><span class="sxs-lookup"><span data-stu-id="37602-894">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="37602-895">In dem Beispiel mit `Url.Action` oben wird von herkömmlichem Routing ausgegangen. Die URL-Generierung funktioniert ähnlich wie das Attributrouting, obwohl sich die Konzepte unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="37602-895">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="37602-896">Bei herkömmlichem Routing wird mit den Routenwerten eine Vorlage erweitert, und die Routenwerte für `controller` und `action` kommen in der Regel in dieser Vorlage vor. Das funktioniert, weil sich die mit dem Routing zugeordneten URLs an eine *Konvention* halten.</span><span class="sxs-lookup"><span data-stu-id="37602-896">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="37602-897">Beim Attributrouting dürfen die Routenwerte für `controller` und `action` nicht in der Vorlage vorkommen. Sie werden stattdessen verwendet, um nachzuschlagen, welche Vorlage verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="37602-897">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="37602-898">In diesem Beispiel wird das Attributrouting verwendet:</span><span class="sxs-lookup"><span data-stu-id="37602-898">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="37602-899">MVC erstellt eine Nachschlagetabelle aller über Attribute zugeordneten Aktionen und ordnet die `controller`- und `action`-Werte zu, um die Routenvorlage auszuwählen, die für die URL-Generierung verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="37602-899">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="37602-900">Im obigen Beispiel wird `custom/url/to/destination` generiert.</span><span class="sxs-lookup"><span data-stu-id="37602-900">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="37602-901">Generieren von URLs nach Aktionsnamen</span><span class="sxs-lookup"><span data-stu-id="37602-901">Generating URLs by action name</span></span>

<span data-ttu-id="37602-902">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="37602-902">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="37602-903">`Action`) und alle zugehörigen Überladungen bauen alle auf der Idee auf, dass Sie angeben, was Sie verknüpfen möchten, indem Sie einen Controllernamen und einen Aktionsnamen angeben.</span><span class="sxs-lookup"><span data-stu-id="37602-903">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="37602-904">Bei Verwendung von `Url.Action` werden die aktuellen Routenwerte für `controller` und `action` für Sie festgelegt. Die Werte von `controller` und `action` bestehen sowohl aus *Umgebungswerten* \*\*als auch aus \*\* *Werten*.</span><span class="sxs-lookup"><span data-stu-id="37602-904">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="37602-905">Die Methode `Url.Action` verwendet immer die aktuellen Werte von `action` und `controller` und generiert einen URL-Pfad, der zur aktuellen Aktion weiterleitet.</span><span class="sxs-lookup"><span data-stu-id="37602-905">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="37602-906">Beim Routing wird versucht, mit den Werten in den Umgebungswerten Informationen auszufüllen, die Sie beim Generieren einer URL nicht bereitgestellt haben.</span><span class="sxs-lookup"><span data-stu-id="37602-906">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="37602-907">Mit Routen wie `{a}/{b}/{c}/{d}` und Umgebungswerten wie `{ a = Alice, b = Bob, c = Carol, d = David }` hat das Routing genügend Informationen, um eine URL ohne zusätzliche Werte zu generieren, da alle Routenparameter einen Wert aufweisen.</span><span class="sxs-lookup"><span data-stu-id="37602-907">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="37602-908">Wenn Sie den Wert `{ d = Donovan }` hinzufügen, wird der Wert `{ d = David }` ignoriert, und der generierte URL-Pfad wäre `Alice/Bob/Carol/Donovan`.</span><span class="sxs-lookup"><span data-stu-id="37602-908">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="37602-909">URL-Pfade sind hierarchisch.</span><span class="sxs-lookup"><span data-stu-id="37602-909">URL paths are hierarchical.</span></span> <span data-ttu-id="37602-910">Wenn Sie im obigen Beispiel den Wert `{ c = Cheryl }` hinzufügen, werden die beiden Werte `{ c = Carol, d = David }` ignoriert.</span><span class="sxs-lookup"><span data-stu-id="37602-910">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="37602-911">In diesem Fall haben wir keinen Wert für `d` mehr, und die URL-Generierung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="37602-911">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="37602-912">Sie müssten dann den gewünschten Wert von `c` und `d` angeben.</span><span class="sxs-lookup"><span data-stu-id="37602-912">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="37602-913">Man könnte annehmen, dass dieses Problem bei der Standardroute auftritt (`{controller}/{action}/{id?}`). Tatsächlich passiert es in der Praxis jedoch selten, das `Url.Action` immer explizit einen `controller`- und `action`-Wert angibt.</span><span class="sxs-lookup"><span data-stu-id="37602-913">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="37602-914">Längere Überladungen von `Url.Action` akzeptieren auch ein zusätzliches *route values*-Objekt, um andere Werte für Routenparameter als `controller` und `action` bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="37602-914">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="37602-915">Es wird in der Regel mit `id` verwendet, z.B. in `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="37602-915">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="37602-916">Gemäß der Konvention ist das *Routenwerte*-Objekt eines des anonymen Typs, es kann aber auch ein `IDictionary<>`-Objekt oder ein *Plain Old .NET Object* sein.</span><span class="sxs-lookup"><span data-stu-id="37602-916">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="37602-917">Alle zusätzlichen Routenwerte, die keinen Routenparametern zugeordnet sind, werden in der Abfragezeichenfolge platziert.</span><span class="sxs-lookup"><span data-stu-id="37602-917">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="37602-918">Um eine absolute URL zu erstellen, verwenden Sie eine Überladung, die eine `protocol` akzeptiert: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="37602-918">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="37602-919">Generieren von URLs nach Routen</span><span class="sxs-lookup"><span data-stu-id="37602-919">Generating URLs by route</span></span>

<span data-ttu-id="37602-920">Im obigen Code wurde das Generieren einer URL durch das Übergeben des Controller- und Aktionsnamens veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="37602-920">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="37602-921">`IUrlHelper` stellt außerdem die `Url.RouteUrl`-Methodenfamilie bereit.</span><span class="sxs-lookup"><span data-stu-id="37602-921">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="37602-922">Diese Methoden ähneln `Url.Action`, kopieren jedoch nicht die aktuellen Werte `action` und `controller` in die Routenwerte.</span><span class="sxs-lookup"><span data-stu-id="37602-922">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="37602-923">Oftmals wird damit ein Routenname angegeben, um mit einer bestimmten Route die URL zu generieren, in der Regel *ohne* Angabe eines Controller- oder Aktionsnamens.</span><span class="sxs-lookup"><span data-stu-id="37602-923">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="37602-924">Generieren von URLs in HTML</span><span class="sxs-lookup"><span data-stu-id="37602-924">Generating URLs in HTML</span></span>

<span data-ttu-id="37602-925">`IHtmlHelper` stellt die `HtmlHelper`-Methoden `Html.BeginForm` und `Html.ActionLink` bereit, um jeweils `<form>`- und `<a>`-Elemente zu generieren.</span><span class="sxs-lookup"><span data-stu-id="37602-925">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="37602-926">Diese Methoden verwenden die `Url.Action`-Methode, um eine URL zu generieren, und akzeptieren ähnliche Argumente.</span><span class="sxs-lookup"><span data-stu-id="37602-926">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="37602-927">Die `Url.RouteUrl`-Begleiter für `HtmlHelper` sind `Html.BeginRouteForm` und `Html.RouteLink`, die ähnliche Funktionen aufweisen.</span><span class="sxs-lookup"><span data-stu-id="37602-927">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="37602-928">Taghilfsprogramme generieren URLs mit den Taghilfsprogrammen `form` und `<a>`.</span><span class="sxs-lookup"><span data-stu-id="37602-928">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="37602-929">Beide implementieren mit `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="37602-929">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="37602-930">Weitere Informationen finden Sie unter [Einführung in die Verwendung von Taghilfsprogrammen in Formularen in ASP.NET Core](../views/working-with-forms.md).</span><span class="sxs-lookup"><span data-stu-id="37602-930">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="37602-931">In Ansichten ist `IUrlHelper` über die `Url`-Eigenschaft für jede Ad-hoc-URL-Generierung verfügbar, die keine der oben genannten ist.</span><span class="sxs-lookup"><span data-stu-id="37602-931">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="37602-932">Generieren von URLs in Aktionsergebnissen</span><span class="sxs-lookup"><span data-stu-id="37602-932">Generating URLS in Action Results</span></span>

<span data-ttu-id="37602-933">In den obigen Beispielen wurde gezeigt, wie `IUrlHelper` in einem Controller verwendet wird und dass es üblicherweise dazu dient, eine URL im Rahmen eines Aktionsergebnisses zu generieren.</span><span class="sxs-lookup"><span data-stu-id="37602-933">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="37602-934">Die Basisklassen `ControllerBase` und `Controller` stellen Hilfsmethoden für Aktionsergebnisse bereit, die auf eine andere Aktionen verweisen.</span><span class="sxs-lookup"><span data-stu-id="37602-934">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="37602-935">Eine typische Verwendung besteht darin, nach dem Akzeptieren einer Benutzereingabe weiterzuleiten.</span><span class="sxs-lookup"><span data-stu-id="37602-935">One typical usage is to redirect after accepting user input.</span></span>

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

<span data-ttu-id="37602-936">Die Factorymethoden der Aktionsergebnisse folgen einem ähnlichen Muster wie die Methoden für `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="37602-936">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="37602-937">Sonderfall für dedizierte herkömmliche Routen</span><span class="sxs-lookup"><span data-stu-id="37602-937">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="37602-938">Beim herkömmlichen Routing können Sie eine bestimmte Art von Routendefinition verwenden, die als *dedizierte herkömmliche Route* bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="37602-938">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="37602-939">Die Route namens `blog` im folgenden Beispiel ist eine dedizierte herkömmliche Route.</span><span class="sxs-lookup"><span data-stu-id="37602-939">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="37602-940">Wenn Sie diese Routendefinitionen verwenden, generiert `Url.Action("Index", "Home")` den URL-Pfad `/` mit der `default`-Route. Die Frage ist nur warum?</span><span class="sxs-lookup"><span data-stu-id="37602-940">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="37602-941">Man könnte meinen, dass die Routenwerte `{ controller = Home, action = Index }` ausreichen würden, um eine URL mithilfe von `blog` zu generieren, und das Ergebnis wäre `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="37602-941">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="37602-942">Dedizierte herkömmliche Routen nutzen ein spezielles Verhalten von Standardwerten, die keinen entsprechenden Routenparameter besitzen, der verhindert, dass die Route bei der URL-Generierung „zu gierig“ wird.</span><span class="sxs-lookup"><span data-stu-id="37602-942">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="37602-943">In diesem Fall sind die Standardwerte `{ controller = Blog, action = Article }`, und weder `controller` noch `action` werden als Routenparameter verwendet.</span><span class="sxs-lookup"><span data-stu-id="37602-943">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="37602-944">Wenn das Routing die URL-Generierung ausführt, müssen die angegebenen Werte mit den Standardwerten übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="37602-944">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="37602-945">Die URL-Generierung mithilfe von `blog` schlägt fehl, da die Werte `{ controller = Home, action = Index }` nicht mit `{ controller = Blog, action = Article }` übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="37602-945">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="37602-946">Routing greift dann wieder auf `default` zurück, was erfolgreich ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="37602-946">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="37602-947">Bereiche</span><span class="sxs-lookup"><span data-stu-id="37602-947">Areas</span></span>

<span data-ttu-id="37602-948">[Bereiche](areas.md) sind ein MVC-Feature, mit dem verwandte Funktionen in einer Gruppe als separater Routing-Namespace (für Controlleraktionen) und als Ordnerstruktur (für Ansichten) organisiert werden können.</span><span class="sxs-lookup"><span data-stu-id="37602-948">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="37602-949">Mithilfe von Bereichen kann eine Anwendung mehrere Controller mit demselben Namen haben – solange sie verschiedene *Bereiche* haben.</span><span class="sxs-lookup"><span data-stu-id="37602-949">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="37602-950">Mithilfe von Bereichen wird außerdem eine Hierarchie erstellt, damit das Routing durch Hinzufügen eines anderen Routenparameters ausgeführt werden kann: `area` zu `controller` und `action`.</span><span class="sxs-lookup"><span data-stu-id="37602-950">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="37602-951">In diesem Abschnitt wird erläutert, wie Routing mit Bereichen interagiert. Weitere Informationen zur Verwendung von Bereichen mit Ansichten finden Sie unter [Bereiche](areas.md).</span><span class="sxs-lookup"><span data-stu-id="37602-951">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="37602-952">Im folgenden Beispiel wird MVC konfiguriert, sodass es die standardmäßige herkömmliche Route verwendet und eine *Bereichsroute* für einen Bereich namens `Blog`:</span><span class="sxs-lookup"><span data-stu-id="37602-952">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="37602-953">Beim Abgleich eines URL-Pfads wie `/Manage/Users/AddUser` erzeugt die erste Route die Routenwerte `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="37602-953">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="37602-954">Der `area`-Routenwert wird von einem Standardwert für `area` erzeugt. Die von `MapAreaRoute` erstellte Route entspricht in der Tat der folgenden:</span><span class="sxs-lookup"><span data-stu-id="37602-954">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="37602-955">`MapAreaRoute` erstellt mit einem Standardwert und einer Einschränkung für `area` sowie mit dem bereitgestellten Bereichsnamen (in diesem Fall `Blog`) eine Route.</span><span class="sxs-lookup"><span data-stu-id="37602-955">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="37602-956">Der Standardwert stellt sicher, dass die Route immer `{ area = Blog, ... }` erzeugt, die Einschränkung erfordert den Wert `{ area = Blog, ... }` für URL-Generierung.</span><span class="sxs-lookup"><span data-stu-id="37602-956">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="37602-957">Beim herkömmlichen Routing ist die Reihenfolge wichtig.</span><span class="sxs-lookup"><span data-stu-id="37602-957">Conventional routing is order-dependent.</span></span> <span data-ttu-id="37602-958">Routen mit Bereichen werden im Allgemeinen früher in der Routentabelle aufgeführt als die spezifischeren Routen ohne Bereich.</span><span class="sxs-lookup"><span data-stu-id="37602-958">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="37602-959">Die Routenwerte im oben genannten Beispiel werden der folgenden Aktion zugeordnet:</span><span class="sxs-lookup"><span data-stu-id="37602-959">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="37602-960">`AreaAttribute` kennzeichnet einen Controller als Teil eines Bereichs, z.B. des Bereichs `Blog`.</span><span class="sxs-lookup"><span data-stu-id="37602-960">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="37602-961">Controller ohne `[Area]`-Attribut gehören demnach zu keinem Bereich und stimmen **nicht** überein, wenn die `area`-Route wird vom Routing bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="37602-961">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="37602-962">Im folgenden Beispiel kann nur der erste aufgelistete Controller die Routenwerte `{ area = Blog, controller = Users, action = AddUser }` abgleichen.</span><span class="sxs-lookup"><span data-stu-id="37602-962">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="37602-963">Aus Gründen der Vollständigkeit wird hier der Namespace jedes Controllers dargestellt. Andernfalls gäbe es einen Namenskonflikt zwischen den Controllern, und ein Compilerfehler würde generiert.</span><span class="sxs-lookup"><span data-stu-id="37602-963">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="37602-964">Klassennamespaces haben keine Auswirkungen auf das MVC-Routing.</span><span class="sxs-lookup"><span data-stu-id="37602-964">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="37602-965">Die ersten beiden Controller gehören zu Bereichen und werden können nur abgleichen, wenn ihr jeweiliger Bereichsname vom `area`-Routenwert bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="37602-965">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="37602-966">Der dritte Controller gehört keinem Bereich an und kann nur abgleichen, wenn vom Routing kein Wert für `area` bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="37602-966">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="37602-967">Im Hinblick auf das Erkennen *keines Werts* hat die Abwesenheit des `area`-Werts dieselben Auswirkungen, wie wenn der Wert für `area` 0 (null) oder eine leere Zeichenfolge wäre.</span><span class="sxs-lookup"><span data-stu-id="37602-967">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="37602-968">Beim Ausführen einer Aktion innerhalb eines Bereichs ist der Routenwert für `area` als *Umgebungswert* für das Routing verfügbar und kann für die URL-Generierung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="37602-968">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="37602-969">Das bedeutet, dass Bereiche bei der URL-Generierung wie im folgenden Beispiel dargestellt standardmäßig *beständig* sind.</span><span class="sxs-lookup"><span data-stu-id="37602-969">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="37602-970">Verstehen von IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="37602-970">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="37602-971">Dieser Abschnitt enthält Details zu Framework-Mechanismen und dazu, wie eine auszuführende Aktion in MVC auswählt wird.</span><span class="sxs-lookup"><span data-stu-id="37602-971">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="37602-972">Eine typische Anwendung benötigt kein benutzerdefiniertes `IActionConstraint`.</span><span class="sxs-lookup"><span data-stu-id="37602-972">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="37602-973">Sie haben `IActionConstraint` wahrscheinlich bereits verwendet, auch wenn Sie mit der Oberfläche noch nicht vertraut sind.</span><span class="sxs-lookup"><span data-stu-id="37602-973">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="37602-974">Das `[HttpGet]`-Attribut und ähnliche `[Http-VERB]`-Attribute implementieren `IActionConstraint`, um die Ausführung einer Aktionsmethode einzuschränken.</span><span class="sxs-lookup"><span data-stu-id="37602-974">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="37602-975">Unter Annahme der herkömmlichen Standardroute würde der URL-Pfad `/Products/Edit` die Werte `{ controller = Products, action = Edit }` erzeugen, die **beiden** hier gezeigten Aktionen entsprechen.</span><span class="sxs-lookup"><span data-stu-id="37602-975">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="37602-976">In `IActionConstraint`-Terminologie würde man sagen, dass es sich bei beiden genannten Aktionen um Kandidaten handelt, da beide den Routendaten entsprechen.</span><span class="sxs-lookup"><span data-stu-id="37602-976">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="37602-977">Wenn `HttpGetAttribute` ausgeführt wird, wird angegeben, dass *Edit()* mit *GET* übereinstimmt und mit keinem anderen HTTP-Verb.</span><span class="sxs-lookup"><span data-stu-id="37602-977">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="37602-978">Für die `Edit(...)`-Aktion wurde keine Einschränkungen definiert, und daher stimmt sie mit allen HTTP-Verben überein.</span><span class="sxs-lookup"><span data-stu-id="37602-978">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="37602-979">Wenn wir von `POST` ausgehen, stimmt nur `Edit(...)` überein.</span><span class="sxs-lookup"><span data-stu-id="37602-979">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="37602-980">Für `GET` können jedoch beide Aktionen übereinstimmen. Eine Aktion mit `IActionConstraint` wird einer Aktion ohne jedoch immer *vorgezogen*.</span><span class="sxs-lookup"><span data-stu-id="37602-980">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="37602-981">Da `Edit()` über `[HttpGet]` verfügt, gilt die Methode als spezifischer und wird ausgewählt, wenn beide Aktionen das Verb erkennen.</span><span class="sxs-lookup"><span data-stu-id="37602-981">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="37602-982">Im Prinzip ist `IActionConstraint` eine Form der *Überladung*. Anstatt jedoch Methoden mit demselben Namen zu überladen, führt es zu einer Überladung zwischen Aktionen, die dieselbe URL erkennen.</span><span class="sxs-lookup"><span data-stu-id="37602-982">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="37602-983">Beim Attributrouting wird auch `IActionConstraint` verwendet. Aus diesem Grund kann es zu Aktionen aus verschiedenen Controllern führen, die beide als Kandidaten gelten.</span><span class="sxs-lookup"><span data-stu-id="37602-983">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="37602-984">Implementieren von IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="37602-984">Implementing IActionConstraint</span></span>

<span data-ttu-id="37602-985">`IActionConstraint` lässt sich am einfachsten erstellen, indem Sie eine abgeleitete Klasse von `System.Attribute` erstellen und sie auf Ihren Aktionen und Controllern platzieren.</span><span class="sxs-lookup"><span data-stu-id="37602-985">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="37602-986">MVC erkennt automatisch jedes `IActionConstraint`, das als Attribut verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="37602-986">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="37602-987">Sie können mithilfe des Anwendungsmodells Einschränkungen anwenden. Dies ist wahrscheinlich die flexibelste Herangehensweise, da Sie über Metaprogrammierung festlegen können, wie die Einschränkungen angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="37602-987">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="37602-988">Im folgenden Beispiel wählt eine Einschränkung eine Aktion aus, die auf einem *Ländercode* aus den Routendaten basiert.</span><span class="sxs-lookup"><span data-stu-id="37602-988">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="37602-989">[Das vollständige Beispiel finden Sie auf GitHub.](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs)</span><span class="sxs-lookup"><span data-stu-id="37602-989">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

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

<span data-ttu-id="37602-990">Sie sind für die Implementierung der `Accept`-Methode und die Festlegung einer Reihenfolge (Order) verantwortlich, in der die Einschränkung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="37602-990">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="37602-991">In diesem Fall gibt die `Accept`-Methode `true` zurück, um zu kennzeichnen, dass die Aktion eine Übereinstimmung ist, wenn die `country`-Routenwerte übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="37602-991">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="37602-992">Dies unterscheidet sich von `RouteValueAttribute` dahingehend, dass ein Fallback auf eine Aktion ohne Attribute zulässig ist.</span><span class="sxs-lookup"><span data-stu-id="37602-992">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="37602-993">In dem Beispiel wird gezeigt, dass wenn Sie eine `en-US`-Aktion definieren, ein Ländercode wie `fr-FR` auf einen generischeren Controller zurückgreift, für den `[CountrySpecific(...)]` nicht angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="37602-993">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="37602-994">Die `Order`-Eigenschaft entscheidet, zu welcher *Phase* die Einschränkung gehört.</span><span class="sxs-lookup"><span data-stu-id="37602-994">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="37602-995">Aktionseinschränkungen werden auf Grundlage von `Order` in Gruppen ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="37602-995">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="37602-996">Beispiel: Alle vom Framework bereitgestellten HTTP-Methodenattribute verwenden denselben `Order`-Wert, sodass sie in derselben Phase ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="37602-996">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="37602-997">Es kann so viele Phasen geben, wie zur Implementierung der gewünschten Richtlinien notwendig.</span><span class="sxs-lookup"><span data-stu-id="37602-997">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="37602-998">Bei der Entscheidung über einen Wert für `Order` sollten Sie berücksichtigen, ob die Einschränkung vor HTTP-Methoden angewendet werden soll oder nicht.</span><span class="sxs-lookup"><span data-stu-id="37602-998">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="37602-999">Niedrigere Zahlen werden zuerst ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="37602-999">Lower numbers run first.</span></span>

::: moniker-end

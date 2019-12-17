---
title: Routing zu Controlleraktionen in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie ASP.NET Core MVC Routingmiddleware verwendet, um die URLs der eingehenden Anforderungen abzugleichen und sie Aktionen zuzuordnen.
ms.author: riande
ms.date: 12/05/2019
uid: mvc/controllers/routing
ms.openlocfilehash: b0cd3df6eb0efa90fc76d206413016d6c624285c
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74881076"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="e0a2a-103">Routing zu Controlleraktionen in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e0a2a-103">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="e0a2a-104">Von [Ryan Nowak](https://github.com/rynowak) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e0a2a-104">By [Ryan Nowak](https://github.com/rynowak) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e0a2a-105">ASP.NET Core MVC verwendet [Routing-Middleware](xref:fundamentals/middleware/index), um die URLs der eingehenden Anforderungen abzugleichen und sie Aktionen zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-105">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="e0a2a-106">Routen werden im Startcode oder in Attributen definiert</span><span class="sxs-lookup"><span data-stu-id="e0a2a-106">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="e0a2a-107">und beschreiben, wie URL-Pfade Aktionen zugeordnet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-107">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="e0a2a-108">Sie werden auch dazu verwendet, um URLs (für Links) zu generieren, die in Antworten gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-108">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="e0a2a-109">Aktionen werden entweder herkömmlich oder über Attribute zugeordnet,</span><span class="sxs-lookup"><span data-stu-id="e0a2a-109">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="e0a2a-110">d.h., dass eine Route auf dem Controller oder der Aktion platziert wird.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-110">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="e0a2a-111">Weitere Informationen finden Sie im Abschnitt [Gemischtes Routing](#routing-mixed-ref-label).</span><span class="sxs-lookup"><span data-stu-id="e0a2a-111">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="e0a2a-112">In diesem Artikel werden die Schnittstellen zwischen MVC und Routing und die Art und Weise erläutert, wie normale MVC-Apps Routingfeatures verwenden.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-112">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="e0a2a-113">Informationen zum erweiterten Routing finden Sie unter [Routing in ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="e0a2a-113">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="e0a2a-114">Einrichten der Routing-Middleware</span><span class="sxs-lookup"><span data-stu-id="e0a2a-114">Setting up Routing Middleware</span></span>

<span data-ttu-id="e0a2a-115">Ihre *Configure*-Methode kann Code wie diesen enthalten:</span><span class="sxs-lookup"><span data-stu-id="e0a2a-115">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="e0a2a-116">Innerhalb des Aufrufs von `UseMvc` wird mit `MapRoute` eine einzelne Route erstellt, die wir `default`-Route nennen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-116">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="e0a2a-117">Die meisten MVC-Apps verwenden eine Route mit einer Vorlage, die der `default`-Route ähnelt.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-117">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="e0a2a-118">TDie Routenvorlage `"{controller=Home}/{action=Index}/{id?}"` kann einem URL-Pfad wie `/Products/Details/5` abgleichen und extrahiert die Routenwerte `{ controller = Products, action = Details, id = 5 }`, indem ein Token für den Pfad erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-118">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="e0a2a-119">MVC versucht, einen Controller namens `ProductsController` zu suchen und die Aktion `Details` auszuführen:</span><span class="sxs-lookup"><span data-stu-id="e0a2a-119">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="e0a2a-120">In diesem Beispiel hat die Modellbindung den `id`-Parameter mit dem Wert `id = 5` beim Aufrufen dieser Aktion auf `5` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-120">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="e0a2a-121">Weitere Informationen finden Sie unter [Modellbindung](../models/model-binding.md).</span><span class="sxs-lookup"><span data-stu-id="e0a2a-121">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="e0a2a-122">Verwenden der `default`-Route:</span><span class="sxs-lookup"><span data-stu-id="e0a2a-122">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="e0a2a-123">Die Routenvorlage:</span><span class="sxs-lookup"><span data-stu-id="e0a2a-123">The route template:</span></span>

* <span data-ttu-id="e0a2a-124">`{controller=Home}` definiert `Home` als Standard-`controller`.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-124">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="e0a2a-125">`{action=Index}` definiert `Index` als Standard-`action`.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-125">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="e0a2a-126">`{id?}` definiert `id` als optional.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-126">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="e0a2a-127">Standardmäßige und optionale Routenparameter müssen nicht im URL-Pfad vorhanden sein, damit es eine Übereinstimmung gibt.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-127">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="e0a2a-128">Eine ausführliche Beschreibung der Syntax der Routenvorlage finden Sie unter [Routenvorlagenreferenz](../../fundamentals/routing.md#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="e0a2a-128">See [Route Template Reference](../../fundamentals/routing.md#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="e0a2a-129">`"{controller=Home}/{action=Index}/{id?}"` gleicht den URL-Pfad `/` ab und erzeugt die Routenwerte `{ controller = Home, action = Index }`.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-129">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="e0a2a-130">Die Werte für `controller` und `action` verwenden die Standardwerte. `id` erzeugt keine Werte, da kein entsprechendes Segment im URL-Pfad vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-130">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="e0a2a-131">MVC nutzt diese Routenwerte, um die `HomeController`- und `Index`-Aktion auszuwählen:</span><span class="sxs-lookup"><span data-stu-id="e0a2a-131">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="e0a2a-132">Mit dieser Controllerdefinition und dieser Routenvorlage wird die `HomeController.Index`-Aktion für jeden der folgenden URL-Pfade ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="e0a2a-132">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="e0a2a-133">Mit der Hilfsmethode `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="e0a2a-133">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="e0a2a-134">kann Folgendes ersetzt werden:</span><span class="sxs-lookup"><span data-stu-id="e0a2a-134">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="e0a2a-135">`UseMvc` und `UseMvcWithDefaultRoute` fügen der Middleware-Pipeline eine Instanz von `RouterMiddleware` hinzu.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-135">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="e0a2a-136">MVC interagiert nicht direkt mit der Middleware und verarbeitet Anforderungen mithilfe von Routing.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-136">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="e0a2a-137">MVC ist über eine Instanz von `MvcRouteHandler` mit den Routen verbunden.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-137">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="e0a2a-138">Der Code in `UseMvc` ähnelt Folgendem:</span><span class="sxs-lookup"><span data-stu-id="e0a2a-138">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="e0a2a-139">`UseMvc` definiert keine Routen direkt, sondern fügt der Routenauflistung einen Platzhalter für die `attribute`-Route hinzu.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-139">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="e0a2a-140">Die Überladung `UseMvc(Action<IRouteBuilder>)` ermöglicht es Ihnen, Ihre eigenen Routen hinzuzufügen und unterstützt darüber hinaus das Routingattribut.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-140">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="e0a2a-141">`UseMvc` und alle seine Varianten fügen einen Platzhalter für die Attributroute hinzu. Attributrouting ist immer verfügbar, unabhängig von der Konfiguration von `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-141">`UseMvc` and all of its variations adds a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="e0a2a-142">`UseMvcWithDefaultRoute` definiert eine Standardroute und unterstützt Attributrouting.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-142">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="e0a2a-143">Der Abschnitt [Attributrouting](#attribute-routing-ref-label) enthält weitere Informationen zu dem Thema.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-143">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="e0a2a-144">Herkömmliches Routing</span><span class="sxs-lookup"><span data-stu-id="e0a2a-144">Conventional routing</span></span>

<span data-ttu-id="e0a2a-145">Die `default`-Route:</span><span class="sxs-lookup"><span data-stu-id="e0a2a-145">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="e0a2a-146">ist ein Beispiel für *herkömmliches Routing*.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-146">is an example of a *conventional routing*.</span></span> <span data-ttu-id="e0a2a-147">Das *herkömmliche Routing* heißt so, weil dabei eine *Konvention* für URL-Pfade erstellt wird:</span><span class="sxs-lookup"><span data-stu-id="e0a2a-147">We call this style *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="e0a2a-148">Das erste Pfadsegment entspricht dem Namen des Controllers.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-148">the first path segment maps to the controller name</span></span>

* <span data-ttu-id="e0a2a-149">Das zweite entspricht dem Aktionsnamen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-149">the second maps to the action name.</span></span>

* <span data-ttu-id="e0a2a-150">Das dritte Segment wird für eine optionale `id` verwendet, mit der eine Zuordnung zu einer Modellentität vorgenommen wird.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-150">the third segment is used for an optional `id` used to map to a model entity</span></span>

<span data-ttu-id="e0a2a-151">Mit dieser `default`-Route wir der URL-Pfad `/Products/List` der `ProductsController.List`-Aktion und `/Blog/Article/17` `BlogController.Article` zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-151">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="e0a2a-152">Diese Zuordnung basiert **ausschließlich** auf den Controller- und Aktionsnamen und nicht auf Namespaces, Speicherorten für Quelldateien oder Methodenparametern.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-152">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="e0a2a-153">Die Kombination aus herkömmlichem Routing und Standardrouting ermöglicht es Ihnen, Anwendungen schnell zu erstellen, ohne für jede definierte Aktion ein neues URL-Muster entwerfen zu müssen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-153">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="e0a2a-154">Bei Anwendungen mit Aktionen im CRUD-Stil können einheitliche URLs auf allen Controllern dabei helfen, den Code zu vereinfachen und die UI vorhersehbarer zu machen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-154">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="e0a2a-155">`id` wird von der Routenvorlage als optional definiert, was bedeutet, dass Ihre Aktionen ausgeführt werden, ohne dass die ID als Teil der URL bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-155">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="e0a2a-156">Wenn `id` in der URL ausgelassen wird, wird es üblicherweise von der Modellbindung auf `0` festgelegt, und daher wird in der Datenbank keine Entität gefunden, die `id == 0` entspricht.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-156">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="e0a2a-157">Mit dem Attributrouting können Sie präzise steuern, für welche Aktionen die ID erforderlich ist und für welche nicht.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-157">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="e0a2a-158">Gemäß der Konvention enthält die Dokumentation optionale Parameter wie `id`, wenn sie wahrscheinlich in der richtigen Verwendung angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-158">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="e0a2a-159">Mehrere Routen</span><span class="sxs-lookup"><span data-stu-id="e0a2a-159">Multiple routes</span></span>

<span data-ttu-id="e0a2a-160">Sie können mehrere Routen innerhalb von `UseMvc` hinzufügen, indem Sie weitere Aufrufe von `MapRoute` hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-160">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="e0a2a-161">So können Sie mehrere Konventionen definieren oder herkömmlichen Routen hinzufügen, die einer bestimmten Aktion zugeordnet sind, z.B.:</span><span class="sxs-lookup"><span data-stu-id="e0a2a-161">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="e0a2a-162">Die `blog`-Route hier ist eine *dedizierte herkömmliche Route*, d.h., dass das herkömmliche Routingsystem verwendet wird, aber einer bestimmten Aktion zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-162">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="e0a2a-163">Da `controller` und `action` nicht als Parameter in der Routenvorlage vorkommen, können sie nur die Standardwerte haben. Daher wird diese Route immer der Aktion `BlogController.Article` zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-163">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="e0a2a-164">Die Routen in der Routenauflistung sind geordnet und werden in der Reihenfolge verarbeitet, in der sie hinzugefügt wurden.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-164">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="e0a2a-165">In diesem Beispiel wird daher die Route `blog` vor der Route `default` überprüft.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-165">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="e0a2a-166">*Dedizierte herkömmliche Routen* verwenden oft Catchall-Routenparameter wie `{*article}`, um den verbleibenden Teil des URL-Pfads zu erfassen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-166">*Dedicated conventional routes* often use catch-all route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="e0a2a-167">Dies kann eine Route „zu gierig“ machen, d.h., dass sie alle URLs abgleicht, die eigentlich von anderen Routen abgeglichen werden sollten.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-167">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="e0a2a-168">Fügen Sie die „gierigen“ Routen weiter unten in die Routingtabelle ein, um dieses Problem zu beheben.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-168">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="e0a2a-169">Fallback</span><span class="sxs-lookup"><span data-stu-id="e0a2a-169">Fallback</span></span>

<span data-ttu-id="e0a2a-170">Im Rahmen der Anforderungsverarbeitung überprüft MVC, ob mit den Routenwerten ein Controller und eine Aktion in Ihrer Anwendung gefunden werden können.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-170">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="e0a2a-171">Falls die Routenwerte mit keiner Aktion übereinstimmen, gilt die Route nicht als Übereinstimmung, und die nächste Route wird überprüft.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-171">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="e0a2a-172">Dabei spricht man von einem *Fallback*. Dieser Vorgang soll Szenarios vereinfachen, bei denen sich herkömmliche Routen überschneiden.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-172">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="e0a2a-173">Aktionen eindeutig zuordnen</span><span class="sxs-lookup"><span data-stu-id="e0a2a-173">Disambiguating actions</span></span>

<span data-ttu-id="e0a2a-174">Wenn zwei Aktionen beim Routing übereinstimmen, muss MVC beide analysieren und die beste auswählen oder eine Ausnahme auslösen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-174">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="e0a2a-175">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e0a2a-175">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="e0a2a-176">Dieser Controller definiert zwei Aktionen, die mit dem URL-Pfad `/Products/Edit/17` übereinstimmen und die Daten `{ controller = Products, action = Edit, id = 17 }` weiterleiten.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-176">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="e0a2a-177">Dies ist ein typisches Muster für MVC-Controller, in dem `Edit(int)` ein Formular zum Bearbeiten eines Produkts anzeigt und `Edit(int, Product)` das bereitgestellte Formular verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-177">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="e0a2a-178">Um dies zu ermöglichen, muss MVC `Edit(int, Product)` auswählen, wenn die Anforderung HTTP-`POST` ist, und `Edit(int)`, wenn das HTTP-Verb ein anderes ist.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-178">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="e0a2a-179">`HttpPostAttribute` (`[HttpPost]`) ist eine Implementierung von `IActionConstraint`, bei der die Aktion nur ausgewählt werden darf, wenn das HTTP-Verb `POST` ist.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-179">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="e0a2a-180">Aufgrund des Vorhandenseins von `IActionConstraint` ist `Edit(int, Product)` die „bessere“ Übereinstimmung als `Edit(int)`, sodass `Edit(int, Product)` zuerst überprüft wird.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-180">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="e0a2a-181">Benutzerdefinierte `IActionConstraint`-Implementierungen müssen Sie nur in speziellen Szenarios schreiben. Es ist jedoch wichtig, die Rolle von Attributen wie `HttpPostAttribute` zu kennen, denn ähnliche Attribute sind auch für andere HTTP-Verben definiert.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-181">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="e0a2a-182">Beim herkömmlichen Routing nutzen Aktionen oft denselben Aktionsnamen, wenn sie Teil eines `show form -> submit form`-Workflows sind.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-182">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="e0a2a-183">Die Vorteile dessen werden deutlicher, wenn Sie den Abschnitt [Verstehen von IActionConstraint](#understanding-iactionconstraint) gelesen haben.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-183">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="e0a2a-184">Wenn mehrere Routen übereinstimmen und MVC die „beste“ nicht bestimmen kann, löst es eine `AmbiguousActionException` aus.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-184">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="e0a2a-185">Routennamen</span><span class="sxs-lookup"><span data-stu-id="e0a2a-185">Route names</span></span>

<span data-ttu-id="e0a2a-186">Die Zeichenfolgen `"blog"` und `"default"` in den folgenden Beispielen sind Routennamen:</span><span class="sxs-lookup"><span data-stu-id="e0a2a-186">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="e0a2a-187">Der Routenname gibt der Route einen logischen Namen, damit die benannte Route bei der URL-Generierung verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-187">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="e0a2a-188">Dadurch wird die URL-Erstellung erheblich vereinfacht, obwohl die Reihenfolge der Routen die URL-Generierung verkomplizieren sollte.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-188">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="e0a2a-189">Routennamen müssen anwendungsweit eindeutig sein.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-189">Route names must be unique application-wide.</span></span>

<span data-ttu-id="e0a2a-190">Routennamen haben keine Auswirkung auf die URL-Zuordnung oder die Verarbeitung von Anforderungen. Sie dienen nur der URL-Generierung.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-190">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="e0a2a-191">Unter [Routing](xref:fundamentals/routing) finden Sie weitere ausführliche Informationen zur URL-Generierung, einschließlich der Generierung in MVC-spezifischen Hilfsprogrammen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-191">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="e0a2a-192">Attributrouting</span><span class="sxs-lookup"><span data-stu-id="e0a2a-192">Attribute routing</span></span>

<span data-ttu-id="e0a2a-193">Beim Attributrouting werden Aktionen mithilfe von Attributen direkt Routenvorlagen zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-193">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="e0a2a-194">Im folgenden Beispiel wird `app.UseMvc();` in der `Configure`-Methode verwendet, und es wird keine Route übergeben.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-194">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="e0a2a-195">`HomeController` entspricht URLs, die denen ähneln, denen die Standardroute `{controller=Home}/{action=Index}/{id?}` entsprechen würde:</span><span class="sxs-lookup"><span data-stu-id="e0a2a-195">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

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

<span data-ttu-id="e0a2a-196">Die `HomeController.Index()`-Aktion wird für die URL-Pfade `/`, `/Home` und `/Home/Index` jeweils ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-196">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="e0a2a-197">In diesem Beispiel wird ein wichtiger Unterschied beim Programmieren zwischen dem Attributrouting und dem herkömmlichen Routing hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-197">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="e0a2a-198">Attributrouting erfordert mehr Eingabe, um eine Route anzugeben. Die herkömmliche Standardroute verarbeitet auch kürzere Routen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-198">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="e0a2a-199">Attributrouting ermöglicht jedoch (und erfordert auch) die präzise Kontrolle der Routenvorlagen, die für die einzelnen Aktionen gelten.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-199">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="e0a2a-200">Beim Attributrouting haben der Controller- und der Aktionsname **keine** Auswirkung darauf, welche Aktion ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-200">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="e0a2a-201">In diesem Beispiel werden dieselben URLs wie im vorherigen Beispiel abgeglichen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-201">This example will match the same URLs as the previous example.</span></span>

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
> <span data-ttu-id="e0a2a-202">Die oben genannten Routenvorlagen definieren keine Routenparameter für `action`, `area` und `controller`.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-202">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="e0a2a-203">Diese Routenparameter sind in Attributrouten nicht zulässig.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-203">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="e0a2a-204">Da die Routenvorlage bereits einer Aktion zugeordnet ist, wäre es nicht sinnvoll, den Aktionsnamen aus der URL zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-204">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="e0a2a-205">Attributrouting mit Http[Verb]-Attributen</span><span class="sxs-lookup"><span data-stu-id="e0a2a-205">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="e0a2a-206">Beim Attributrouting können auch `Http[Verb]`-Attribute wie `HttpPostAttribute` verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-206">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="e0a2a-207">Alle diese Attribute können eine Routenvorlage akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-207">All of these attributes can accept a route template.</span></span> <span data-ttu-id="e0a2a-208">In diesem Beispiel werden zwei Aktionen gezeigt, die derselben Routenvorlage zugeordnet sind:</span><span class="sxs-lookup"><span data-stu-id="e0a2a-208">This example shows two actions that match the same route template:</span></span>

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

<span data-ttu-id="e0a2a-209">Für einen URL-Pfad wie `/products` wird die Aktion `ProductsApi.ListProducts` ausgeführt, wenn das HTTP-Verb `GET` ist, und `ProductsApi.CreateProduct` wird ausgeführt, wenn das HTTP-Verb `POST` entspricht.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-209">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="e0a2a-210">Attributrouting gleicht zuerst die URL gegen die Routenvorlagen ab, die von den Routenattributen definiert werden.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-210">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="e0a2a-211">Sobald eine Routenvorlage übereinstimmt, werden `IActionConstraint`-Einschränkungen angewendet, um zu bestimmen, welche Aktionen ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-211">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="e0a2a-212">Beim Erstellen einer REST-API wird `[Route(...)]` selten für eine Aktionsmethode verwendet, die Aktion alle HTTP-Methoden akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-212">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="e0a2a-213">Es ist besser, das spezifischere `Http*Verb*Attributes` zu nutzen, um präzise anzugeben, was Ihre API unterstützt.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-213">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="e0a2a-214">REST-API-Clients sollten wissen, welche Pfade und HTTP-Verben bestimmten logischen Operationen entsprechen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-214">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="e0a2a-215">Da eine Attributroute für eine bestimmte Aktion gilt, ist es einfach, Parameter als Teil der Routenvorlagendefinition erforderlich festzulegen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-215">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="e0a2a-216">In diesem Beispiel ist `id` als Teil des URL-Pfads erforderlich.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-216">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="e0a2a-217">Die `ProductsApi.GetProduct(int)`-Aktion wird für einen URL-Pfad wie `/products/3` ausgeführt, jedoch nicht für einen URL-Pfad wie `/products`.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-217">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="e0a2a-218">Eine vollständige Beschreibung und Routenvorlagen und dazugehörige Optionen finden Sie unter [Routing in ASP.NET Core](../../fundamentals/routing.md).</span><span class="sxs-lookup"><span data-stu-id="e0a2a-218">See [Routing](../../fundamentals/routing.md) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="e0a2a-219">Routenname</span><span class="sxs-lookup"><span data-stu-id="e0a2a-219">Route Name</span></span>

<span data-ttu-id="e0a2a-220">Im folgenden Code wird ein *Routenname* von `Products_List` definiert:</span><span class="sxs-lookup"><span data-stu-id="e0a2a-220">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="e0a2a-221">Routennamen können verwendet werden, um basierend auf einer bestimmten Route eine URL zu generieren.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-221">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="e0a2a-222">Routennamen haben keine Auswirkung auf das URL-Zuordnungsverhalten des Routings und dienen nur zur URL-Generierung.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-222">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="e0a2a-223">Routennamen müssen anwendungsweit eindeutig sein.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-223">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="e0a2a-224">Vergleichen Sie dies mit der herkömmlichen *Standardroute*, die den `id`-Parameter als optional definiert (`{id?}`).</span><span class="sxs-lookup"><span data-stu-id="e0a2a-224">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="e0a2a-225">APIs präzise angeben zu können, hat Vorteile, z.B. können `/products` und `/products/5` an unterschiedliche Aktionen gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-225">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="e0a2a-226">Kombinieren von Routen</span><span class="sxs-lookup"><span data-stu-id="e0a2a-226">Combining routes</span></span>

<span data-ttu-id="e0a2a-227">Um Attributrouting weniger repetitiv zu gestalten, werden Routenattribute auf dem Controller mit Routenattributen auf den einzelnen Aktionen kombiniert.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-227">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="e0a2a-228">Alle auf dem Controller definierten Routenvorlagen werden den Routenvorlagen auf den Aktionen vorangestellt.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-228">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="e0a2a-229">Wenn Routenattribute auf dem Controller platziert werden, verwenden **alle** Aktionen im Controller Attributrouting.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-229">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

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

<span data-ttu-id="e0a2a-230">In diesem Beispiel kann der URL-Pfad `/products` `ProductsApi.ListProducts` und der URL-Pfad `/products/5` `ProductsApi.GetProduct(int)` zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-230">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="e0a2a-231">Beide Aktionen gleichen nur „HTTP `GET`“ ab, weil sie mit `HttpGetAttribute` markiert sind.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-231">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="e0a2a-232">Routenvorlagen, die auf eine Aktion angewendet werden, die mit einem `/` oder `~/` beginnen, können nicht mit Routenvorlagen kombiniert werden, die auf den Controller angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-232">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="e0a2a-233">In diesem Beispiel werden mehrere URL-Pfade zugeordnet, die der *Standardroute* ähneln.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-233">This example matches a set of URL paths similar to the *default route*.</span></span>

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

### <a name="ordering-attribute-routes"></a><span data-ttu-id="e0a2a-234">Ordnen der Attributrouten</span><span class="sxs-lookup"><span data-stu-id="e0a2a-234">Ordering attribute routes</span></span>

<span data-ttu-id="e0a2a-235">Im Gegensatz zu herkömmlichen Routen, die in einer definierten Reihenfolge ausgeführt werden, wird beim Attributrouting eine Struktur erstellt, und alle Routen werden gleichzeitig zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-235">In contrast to conventional routes which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="e0a2a-236">Der Vorgang wird also ausgeführt, als ob die Routeneinträge in der idealen Reihenfolge platziert worden wären: Die spezifischsten Routen können also vor den allgemeineren ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-236">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="e0a2a-237">Eine Route wie `blog/search/{topic}` ist z.B. spezifischer als eine Route wie `blog/{*article}`.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-237">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="e0a2a-238">Logisch gesehen wird die Route `blog/search/{topic}` standardmäßig zuerst ausgeführt, da dies die einzig sinnvolle Reihenfolge ist.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-238">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="e0a2a-239">Beim herkömmlichen Routing ist der Entwickler verantwortlich dafür, die Routen in die gewünschte Reihenfolge zu bringen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-239">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="e0a2a-240">Attributrouten können mithilfe der `Order`-Eigenschaft aller vom Framework bereitgestellten Routenattribute eine Reihenfolge konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-240">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="e0a2a-241">Routen werden entsprechend einer aufsteigenden Reihenfolge der `Order`-Eigenschaft verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-241">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="e0a2a-242">Die Standardreihenfolge ist `0`.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-242">The default order is `0`.</span></span> <span data-ttu-id="e0a2a-243">Wird für eine Route `Order = -1` festgelegt, wird sie vor denjenigen Routen ausgeführt, für die keine Reihenfolge festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-243">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="e0a2a-244">Wird für eine Route `Order = 1` festgelegt, wird sie ausgeführt, nachdem die Standardroutenreihenfolge ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-244">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="e0a2a-245">Vermeiden Sie eine Abhängigkeit von `Order`.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-245">Avoid depending on `Order`.</span></span> <span data-ttu-id="e0a2a-246">Wenn der URL-Raum explizite Reihenfolgenwerte erfordert, um korrekt weiterzuleiten, ist es wahrscheinlich auch für Clients verwirrend.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-246">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="e0a2a-247">Beim Attributrouting wird im Allgemeinen mithilfe der URL-Zuordnung die richtige Route ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-247">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="e0a2a-248">Wenn die für die URL-Generierung verwendete Standardreihenfolge nicht funktioniert, ist es meist einfacher, Routennamen als Außerkraftsetzung zu verwenden, statt die `Order`-Eigenschaft anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-248">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="e0a2a-249">Razor Pages-Routing und MVC Controller-Routing verwenden eine gemeinsame Implementierung.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-249">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="e0a2a-250">Informationen zur Routenreihenfolge in den Razor Pages-Themen finden Sie unter [Razor Pages-Route und App-Konventionen: Routenreihenfolge](xref:razor-pages/razor-pages-conventions#route-order).</span><span class="sxs-lookup"><span data-stu-id="e0a2a-250">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="e0a2a-251">Ersetzen von Token in Routenvorlagen ([controller], [action] [area])</span><span class="sxs-lookup"><span data-stu-id="e0a2a-251">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="e0a2a-252">Der Einfachheit halber unterstützen Attributrouten die *Tokenersetzung*, indem ein Token in eckige Klammern eingeschlossen wird (`[`, `]`).</span><span class="sxs-lookup"><span data-stu-id="e0a2a-252">For convenience, attribute routes support *token replacement* by enclosing a token in square-braces (`[`, `]`).</span></span> <span data-ttu-id="e0a2a-253">Die Token `[action]`, `[area]` und `[controller]` werden durch die Werte der Aktionsnamen, den Namen des Bereichs und des Controllers der Aktion ersetzt, in dem die Route definiert ist.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-253">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="e0a2a-254">In dem folgenden Beispiel entsprechen die Aktionen wie in den Kommentaren beschrieben URL-Pfaden:</span><span class="sxs-lookup"><span data-stu-id="e0a2a-254">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/sample/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="e0a2a-255">Die Tokenersetzung tritt im letzten Schritt der Erstellung von Attributrouten auf.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-255">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="e0a2a-256">Der folgende Code verhält sich genauso wie der aus dem obigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e0a2a-256">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/sample/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="e0a2a-257">Attributrouten können auch mit Vererbung kombiniert werden.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-257">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="e0a2a-258">Dies ist besonders effektiv in Kombination mit der Tokenersetzung.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-258">This is particularly powerful combined with token replacement.</span></span>

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

<span data-ttu-id="e0a2a-259">Tokenersetzung gilt auch für Routennamen, die durch Attributrouten definiert werden.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-259">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="e0a2a-260">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generiert für jede Aktion einen eindeutigen Routennamen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-260">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="e0a2a-261">Damit das Trennzeichen `[` oder `]` der Tokenersetzungs-Literalzeichenfolge bei einem Abgleich gefunden wird, muss es doppelt vorhanden sein (`[[` oder `]]`), was einem Escapezeichen entspricht.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-261">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker range=">= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="e0a2a-262">Verwenden eines Parametertransformators zum Anpassen der Tokenersetzung</span><span class="sxs-lookup"><span data-stu-id="e0a2a-262">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="e0a2a-263">Die Tokenersetzung kann mit einem Parametertransformator angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-263">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="e0a2a-264">Ein Parametertransformator implementiert `IOutboundParameterTransformer` und wandelt den Wert der Parameter um.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-264">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="e0a2a-265">Beispielsweise ändert ein benutzerdefinierter `SlugifyParameterTransformer`-Parametertransformator den Routenwert `SubscriptionManagement` in `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-265">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="e0a2a-266">Die `RouteTokenTransformerConvention` ist eine Anwendungsmodellkonvention, die Folgendes ausführt:</span><span class="sxs-lookup"><span data-stu-id="e0a2a-266">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="e0a2a-267">Wendet einen angegebenen Parametertransformator auf alle Attributrouten in der App an.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-267">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="e0a2a-268">Passt die Tokenwerte für Attributrouten bei ihrer Ersetzung an.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-268">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="e0a2a-269">Die `RouteTokenTransformerConvention` wird als Option in `ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-269">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

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

<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a><span data-ttu-id="e0a2a-270">Mehrere Routen</span><span class="sxs-lookup"><span data-stu-id="e0a2a-270">Multiple Routes</span></span>

<span data-ttu-id="e0a2a-271">Attributrouting unterstützt das Definieren mehrerer Routen, die zu derselben Aktion führen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-271">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="e0a2a-272">Dies wird am häufigsten beim Imitieren des Verhaltens der *herkömmlichen Standardroute* verwendet. Im folgenden Beispiel wird dies gezeigt:</span><span class="sxs-lookup"><span data-stu-id="e0a2a-272">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="e0a2a-273">Wenn mehrere Routenattribute auf dem Controller platziert werden, wird jedes mit jedem der Routenattribute auf den Aktionsmethoden kombiniert.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-273">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

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

<span data-ttu-id="e0a2a-274">Werden mehrere Routenattribute (die `IActionConstraint` implementieren) auf einer Aktion platziert, wird jede Aktionseinschränkung mit der Routenvorlage aus dem Attribut kombiniert, das sie definiert.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-274">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

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
> <span data-ttu-id="e0a2a-275">Obwohl das Verwenden mehrerer Routen für Aktionen sehr hilfreich scheint, ist es empfehlenswerter, den URL-Raum der Anwendung einfach und gut definiert zu halten.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-275">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="e0a2a-276">Verwenden Sie nur mehrere Routen für Aktionen, wenn dies notwendig ist, z.B. um vorhandene Clients zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-276">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="e0a2a-277">Angeben von optionalen Attributroutenparametern, Standardwerten und Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="e0a2a-277">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="e0a2a-278">Attributrouten unterstützen dieselbe Inline-Syntax wie herkömmliche Routen, um optionale Parameter, Standardwerte und Einschränkungen anzugeben.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-278">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="e0a2a-279">Eine ausführliche Beschreibung der Syntax der Routenvorlage finden Sie unter [Routenvorlagenreferenz](../../fundamentals/routing.md#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="e0a2a-279">See [Route Template Reference](../../fundamentals/routing.md#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="e0a2a-280">Benutzerdefinierte Routenattribute mithilfe von `IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="e0a2a-280">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="e0a2a-281">Alle im Framework bereitgestellten Routenattribute (`[Route(...)]`, `[HttpGet(...)]` usw.) implementieren die `IRouteTemplateProvider`-Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-281">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="e0a2a-282">MVC sucht in Controllerklassen und Aktionsmethoden nach Attributen, wenn die Anwendung gestartet wird, und verwendet diejenigen, die `IRouteTemplateProvider` implementieren, um die anfänglichen Routen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-282">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="e0a2a-283">Sie können `IRouteTemplateProvider` implementieren, um eigene Routenattribute zu definieren.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-283">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="e0a2a-284">Jeder `IRouteTemplateProvider` lässt Sie eine einzelne Route mit einer benutzerdefinierten Routenvorlage, Reihenfolge und einem benutzerdefinierten Namen definieren:</span><span class="sxs-lookup"><span data-stu-id="e0a2a-284">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="e0a2a-285">Das Attribut aus dem obigen Beispiel legt `Template` automatisch auf `"api/[controller]"` fest, wenn `[MyApiController]` angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-285">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="e0a2a-286">Anpassen von Attributrouten mithilfe des Anwendungsmodells</span><span class="sxs-lookup"><span data-stu-id="e0a2a-286">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="e0a2a-287">Das *Anwendungsmodell* ist ein Objektmodell, das beim Starten aus allen Metadaten von MVC erstellt wird, um Ihre Aktionen weiterzuleiten und auszuführen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-287">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="e0a2a-288">Das *Anwendungsmodell* enthält alle Daten, die aus Routenattributen (über `IRouteTemplateProvider`) erfasst wurden.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-288">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="e0a2a-289">Sie können *Konventionen* schreiben, damit das Anwendungsmodell beim Startzeitpunkt das Routingverhalten anpasst.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-289">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="e0a2a-290">Dieser Abschnitt enthält ein einfaches Beispiel für das Anpassen des Routings mithilfe des Anwendungsmodells.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-290">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/sample/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="e0a2a-291">Gemischtes Routing: Attributrouting vs. herkömmliches Routing</span><span class="sxs-lookup"><span data-stu-id="e0a2a-291">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="e0a2a-292">MVC-Anwendungen können herkömmliches Routing und Attributrouting kombinieren.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-292">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="e0a2a-293">In der Regel werden herkömmliche Routen für Controller verwendet, die für Browser-HTML-Seiten gedacht sind, und das Attributrouting für Controller für REST-APIs.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-293">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="e0a2a-294">Aktionen werden entweder herkömmlich oder über Attribute zugeordnet,</span><span class="sxs-lookup"><span data-stu-id="e0a2a-294">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="e0a2a-295">d.h., dass eine Route auf dem Controller oder der Aktion platziert wird.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-295">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="e0a2a-296">Aktionen, die Attributrouten definieren, können nicht über die herkömmliche Routen und umgekehrt erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-296">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="e0a2a-297">Wenn nur **ein** Routenattribut auf dem Controller platziert wird, werden alle Aktionen im Controller über Attribute zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-297">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="e0a2a-298">Die beiden Routingmethoden unterscheiden sich in dem Prozess, der angewendet wird, nachdem eine URL einer Routenvorlage zugeordnet wurde.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-298">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="e0a2a-299">Beim herkömmlichen Routing dienen die Routenwerte aus der Zuordnung dazu, aus einer Nachschlagetabelle aller herkömmlich zugeordneten Aktionen die Aktion und den Controller auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-299">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="e0a2a-300">Beim Attributrouting ist jede Vorlage bereits einer Aktion zugeordnet, und keine weitere Suche ist erforderlich.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-300">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="e0a2a-301">Komplexe Segmente</span><span class="sxs-lookup"><span data-stu-id="e0a2a-301">Complex segments</span></span>

<span data-ttu-id="e0a2a-302">Komplexe Segmente (z.B. `[Route("/dog{token}cat")]`) werden von rechts nach links auf eine nicht gierige Weise durch entsprechende Literale verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-302">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="e0a2a-303">Eine entsprechende Beschreibung finden Sie im [Quellcode](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296).</span><span class="sxs-lookup"><span data-stu-id="e0a2a-303">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="e0a2a-304">Weitere Informationen finden Sie in [diesem Thema](https://github.com/aspnet/AspNetCore.Docs/issues/8197).</span><span class="sxs-lookup"><span data-stu-id="e0a2a-304">For more information, see [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="e0a2a-305">URL-Generierung</span><span class="sxs-lookup"><span data-stu-id="e0a2a-305">URL Generation</span></span>

<span data-ttu-id="e0a2a-306">MVC-Anwendungen können Routingfeatures zur URL-Generierung verwenden, um URL-Links zu Aktionen zu generieren.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-306">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="e0a2a-307">Durch das Generieren von URLs müssen URLs nicht mehr hartcodiert werden, sodass der Code robuster und leichter verwaltbar wird.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-307">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="e0a2a-308">Dieser Abschnitt konzentriert sich auf die von MVC bereitgestellte URL-Generierung und befasst sich nur kurz mit der Funktionsweise.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-308">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="e0a2a-309">Eine detaillierte Beschreibung der URL-Generierung finden Sie unter [Routing in ASP.NET Core](../../fundamentals/routing.md).</span><span class="sxs-lookup"><span data-stu-id="e0a2a-309">See [Routing](../../fundamentals/routing.md) for a detailed description of URL generation.</span></span>

<span data-ttu-id="e0a2a-310">Die `IUrlHelper`-Schnittstelle ist die zugrunde liegende Infrastruktur zwischen MVC und dem Routing zur URL-Generierung.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-310">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="e0a2a-311">Eine Instanz von `IUrlHelper` ist über die `Url`-Eigenschaft in Controllern, Ansichten und Ansichtskomponenten verfügbar.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-311">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="e0a2a-312">In diesem Beispiel wird die `IUrlHelper`-Schnittstelle von der `Controller.Url`-Eigenschaft dazu verwendet, eine URL in einer anderen Aktion zu generieren.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-312">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/sample/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="e0a2a-313">Wenn die Anwendung die herkömmliche Standardroute verwendet, ist der Wert der `url`-Variable die URL-Pfadzeichenfolge `/UrlGeneration/Destination`.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-313">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="e0a2a-314">Dieser URL-Pfad wird vom Routing erstellt, indem Routenwerte aus der aktuellen Anforderung (Umgebungswerte) mit den an `Url.Action` übergebenen Werten kombiniert und anschließend in die Routenvorlage eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="e0a2a-314">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="e0a2a-315">Der Wert eines jeden Routenparameters wird in der Routenvorlage durch die entsprechenden Namen mit den Werten und Umgebungswerten ersetzt.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-315">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="e0a2a-316">Ein Routenparameter ohne Wert kann einen Standardwert verwenden, wenn er über einen verfügt, oder übersprungen werden, wenn er optional ist (wie im Fall von `id` in diesem Beispiel).</span><span class="sxs-lookup"><span data-stu-id="e0a2a-316">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="e0a2a-317">Die URL-Generierung schlägt fehl, wenn ein erforderlicher Routenparameter keinen entsprechenden Wert besitzt.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-317">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="e0a2a-318">Wenn die URL-Generierung für eine Route fehlschlägt, wird die nächste Route ausprobiert, bis alle Routen getestet wurden oder eine Übereinstimmung gefunden wurde.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-318">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="e0a2a-319">In dem Beispiel mit `Url.Action` oben wird von herkömmlichem Routing ausgegangen. Die URL-Generierung funktioniert ähnlich wie das Attributrouting, obwohl sich die Konzepte unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-319">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="e0a2a-320">Bei herkömmlichem Routing wird mit den Routenwerten eine Vorlage erweitert, und die Routenwerte für `controller` und `action` kommen in der Regel in dieser Vorlage vor. Das funktioniert, weil sich die mit dem Routing zugeordneten URLs an eine *Konvention* halten.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-320">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="e0a2a-321">Beim Attributrouting dürfen die Routenwerte für `controller` und `action` nicht in der Vorlage vorkommen. Sie werden stattdessen verwendet, um nachzuschlagen, welche Vorlage verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-321">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="e0a2a-322">In diesem Beispiel wird das Attributrouting verwendet:</span><span class="sxs-lookup"><span data-stu-id="e0a2a-322">This example uses attribute routing:</span></span>

[!code-csharp[](routing/sample/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/sample/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="e0a2a-323">MVC erstellt eine Nachschlagetabelle aller über Attribute zugeordneten Aktionen und ordnet die `controller`- und `action`-Werte zu, um die Routenvorlage auszuwählen, die für die URL-Generierung verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-323">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="e0a2a-324">Im obigen Beispiel wird `custom/url/to/destination` generiert.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-324">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="e0a2a-325">Generieren von URLs nach Aktionsnamen</span><span class="sxs-lookup"><span data-stu-id="e0a2a-325">Generating URLs by action name</span></span>

<span data-ttu-id="e0a2a-326">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="e0a2a-326">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="e0a2a-327">`Action`) und alle zugehörigen Überladungen bauen alle auf der Idee auf, dass Sie angeben, was Sie verknüpfen möchten, indem Sie einen Controllernamen und einen Aktionsnamen angeben.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-327">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="e0a2a-328">Bei Verwendung von `Url.Action` sind die aktuellen Routenwerte für `controller` und `action` für Sie angegeben. Die Werte von `controller` und `action` bestehen sowohl aus *Umgebungswerten* **als auch** aus *Werten*.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-328">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="e0a2a-329">Die Methode `Url.Action` verwendet immer die aktuellen Werte von `action` und `controller` und generiert einen URL-Pfad, der zur aktuellen Aktion weiterleitet.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-329">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="e0a2a-330">Beim Routing wird versucht, mit den Werten in den Umgebungswerten Informationen auszufüllen, die Sie beim Generieren einer URL nicht bereitgestellt haben.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-330">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="e0a2a-331">Mit Routen wie `{a}/{b}/{c}/{d}` und Umgebungswerten wie `{ a = Alice, b = Bob, c = Carol, d = David }` hat das Routing genügend Informationen, um eine URL ohne zusätzliche Werte zu generieren, da alle Routenparameter einen Wert aufweisen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-331">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="e0a2a-332">Wenn Sie den Wert `{ d = Donovan }` hinzufügen, wird der Wert `{ d = David }` ignoriert, und der generierte URL-Pfad wäre `Alice/Bob/Carol/Donovan`.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-332">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="e0a2a-333">URL-Pfade sind hierarchisch.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-333">URL paths are hierarchical.</span></span> <span data-ttu-id="e0a2a-334">Wenn Sie im obigen Beispiel den Wert `{ c = Cheryl }` hinzufügen, werden die beiden Werte `{ c = Carol, d = David }` ignoriert.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-334">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="e0a2a-335">In diesem Fall haben wir keinen Wert für `d` mehr, und die URL-Generierung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-335">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="e0a2a-336">Sie müssten dann den gewünschten Wert von `c` und `d` angeben.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-336">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="e0a2a-337">Man könnte annehmen, dass dieses Problem bei der Standardroute auftritt (`{controller}/{action}/{id?}`). Tatsächlich passiert es in der Praxis jedoch selten, das `Url.Action` immer explizit einen `controller`- und `action`-Wert angibt.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-337">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="e0a2a-338">Längere Überladungen von `Url.Action` akzeptieren auch ein zusätzliches *route values*-Objekt, um andere Werte für Routenparameter als `controller` und `action` bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-338">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="e0a2a-339">Es wird in der Regel mit `id` verwendet, z.B. in `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-339">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="e0a2a-340">Gemäß der Konvention ist das *Routenwerte*-Objekt eines des anonymen Typs, es kann aber auch ein `IDictionary<>`-Objekt oder ein *Plain Old .NET Object* sein.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-340">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="e0a2a-341">Alle zusätzlichen Routenwerte, die keinen Routenparametern zugeordnet sind, werden in der Abfragezeichenfolge platziert.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-341">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/sample/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="e0a2a-342">Um eine absolute URL zu erstellen, verwenden Sie eine Überladung, die eine `protocol` akzeptiert: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="e0a2a-342">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="e0a2a-343">Generieren von URLs nach Routen</span><span class="sxs-lookup"><span data-stu-id="e0a2a-343">Generating URLs by route</span></span>

<span data-ttu-id="e0a2a-344">Im obigen Code wurde das Generieren einer URL durch das Übergeben des Controller- und Aktionsnamens veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-344">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="e0a2a-345">`IUrlHelper` stellt außerdem die `Url.RouteUrl`-Methodenfamilie bereit.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-345">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="e0a2a-346">Diese Methoden ähneln `Url.Action`, kopieren jedoch nicht die aktuellen Werte `action` und `controller` in die Routenwerte.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-346">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="e0a2a-347">Oftmals wird damit ein Routenname angegeben, um mit einer bestimmten Route die URL zu generieren, in der Regel *ohne* Angabe eines Controller- oder Aktionsnamens.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-347">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/sample/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="e0a2a-348">Generieren von URLs in HTML</span><span class="sxs-lookup"><span data-stu-id="e0a2a-348">Generating URLs in HTML</span></span>

<span data-ttu-id="e0a2a-349">`IHtmlHelper` stellt die `HtmlHelper`-Methoden `Html.BeginForm` und `Html.ActionLink` bereit, um jeweils `<form>`- und `<a>`-Elemente zu generieren.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-349">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="e0a2a-350">Diese Methoden verwenden die `Url.Action`-Methode, um eine URL zu generieren, und akzeptieren ähnliche Argumente.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-350">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="e0a2a-351">Die `Url.RouteUrl`-Begleiter für `HtmlHelper` sind `Html.BeginRouteForm` und `Html.RouteLink`, die ähnliche Funktionen aufweisen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-351">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="e0a2a-352">Taghilfsprogramme generieren URLs mit den Taghilfsprogrammen `form` und `<a>`.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-352">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="e0a2a-353">Beide implementieren mit `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-353">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="e0a2a-354">Weitere Informationen finden Sie unter [Einführung in die Verwendung von Taghilfsprogrammen in Formularen in ASP.NET Core](../views/working-with-forms.md).</span><span class="sxs-lookup"><span data-stu-id="e0a2a-354">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="e0a2a-355">In Ansichten ist `IUrlHelper` über die `Url`-Eigenschaft für jede Ad-hoc-URL-Generierung verfügbar, die keine der oben genannten ist.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-355">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="e0a2a-356">Generieren von URLs in Aktionsergebnissen</span><span class="sxs-lookup"><span data-stu-id="e0a2a-356">Generating URLS in Action Results</span></span>

<span data-ttu-id="e0a2a-357">In den obigen Beispielen wurde gezeigt, wie `IUrlHelper` in einem Controller verwendet wird und dass es üblicherweise dazu dient, eine URL im Rahmen eines Aktionsergebnisses zu generieren.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-357">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="e0a2a-358">Die Basisklassen `ControllerBase` und `Controller` stellen Hilfsmethoden für Aktionsergebnisse bereit, die auf eine andere Aktionen verweisen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-358">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="e0a2a-359">Eine typische Verwendung besteht darin, nach dem Akzeptieren einer Benutzereingabe weiterzuleiten.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-359">One typical usage is to redirect after accepting user input.</span></span>

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

<span data-ttu-id="e0a2a-360">Die Factorymethoden der Aktionsergebnisse folgen einem ähnlichen Muster wie die Methoden für `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-360">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="e0a2a-361">Sonderfall für dedizierte herkömmliche Routen</span><span class="sxs-lookup"><span data-stu-id="e0a2a-361">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="e0a2a-362">Beim herkömmlichen Routing können Sie eine bestimmte Art von Routendefinition verwenden, die als *dedizierte herkömmliche Route* bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-362">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="e0a2a-363">Die Route namens `blog` im folgenden Beispiel ist eine dedizierte herkömmliche Route.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-363">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="e0a2a-364">Wenn Sie diese Routendefinitionen verwenden, generiert `Url.Action("Index", "Home")` den URL-Pfad `/` mit der `default`-Route. Die Frage ist nur warum?</span><span class="sxs-lookup"><span data-stu-id="e0a2a-364">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="e0a2a-365">Man könnte meinen, dass die Routenwerte `{ controller = Home, action = Index }` ausreichen würden, um eine URL mithilfe von `blog` zu generieren, und das Ergebnis wäre `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-365">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="e0a2a-366">Dedizierte herkömmliche Routen nutzen ein spezielles Verhalten von Standardwerten, die keinen entsprechenden Routenparameter besitzen, der verhindert, dass die Route bei der URL-Generierung „zu gierig“ wird.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-366">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="e0a2a-367">In diesem Fall sind die Standardwerte `{ controller = Blog, action = Article }`, und weder `controller` noch `action` werden als Routenparameter verwendet.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-367">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="e0a2a-368">Wenn das Routing die URL-Generierung ausführt, müssen die angegebenen Werte mit den Standardwerten übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-368">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="e0a2a-369">Die URL-Generierung mithilfe von `blog` schlägt fehl, da die Werte `{ controller = Home, action = Index }` nicht mit `{ controller = Blog, action = Article }` übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-369">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="e0a2a-370">Routing greift dann wieder auf `default` zurück, was erfolgreich ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-370">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="e0a2a-371">Bereiche</span><span class="sxs-lookup"><span data-stu-id="e0a2a-371">Areas</span></span>

<span data-ttu-id="e0a2a-372">[Bereiche](areas.md) sind ein MVC-Feature, mit dem verwandte Funktionen in einer Gruppe als separater Routing-Namespace (für Controlleraktionen) und als Ordnerstruktur (für Ansichten) organisiert werden können.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-372">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="e0a2a-373">Mithilfe von Bereichen kann eine Anwendung mehrere Controller mit demselben Namen haben – solange sie verschiedene *Bereiche* haben.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-373">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="e0a2a-374">Mithilfe von Bereichen wird außerdem eine Hierarchie erstellt, damit das Routing durch Hinzufügen eines anderen Routenparameters ausgeführt werden kann: `area` zu `controller` und `action`.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-374">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="e0a2a-375">In diesem Abschnitt wird erläutert, wie Routing mit Bereichen interagiert. Weitere Informationen zur Verwendung von Bereichen mit Ansichten finden Sie unter [Bereiche](areas.md).</span><span class="sxs-lookup"><span data-stu-id="e0a2a-375">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="e0a2a-376">Im folgenden Beispiel wird MVC konfiguriert, sodass es die standardmäßige herkömmliche Route verwendet und eine *Bereichsroute* für einen Bereich namens `Blog`:</span><span class="sxs-lookup"><span data-stu-id="e0a2a-376">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/sample/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="e0a2a-377">Beim Abgleich eines URL-Pfads wie `/Manage/Users/AddUser` erzeugt die erste Route die Routenwerte `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-377">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="e0a2a-378">Der `area`-Routenwert wird von einem Standardwert für `area` erzeugt. Die von `MapAreaRoute` erstellte Route entspricht in der Tat der folgenden:</span><span class="sxs-lookup"><span data-stu-id="e0a2a-378">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/sample/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="e0a2a-379">`MapAreaRoute` erstellt mit einem Standardwert und einer Einschränkung für `area` sowie mit dem bereitgestellten Bereichsnamen (in diesem Fall `Blog`) eine Route.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-379">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="e0a2a-380">Der Standardwert stellt sicher, dass die Route immer `{ area = Blog, ... }` erzeugt, die Einschränkung erfordert den Wert `{ area = Blog, ... }` für URL-Generierung.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-380">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="e0a2a-381">Beim herkömmlichen Routing ist die Reihenfolge wichtig.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-381">Conventional routing is order-dependent.</span></span> <span data-ttu-id="e0a2a-382">Routen mit Bereichen werden im Allgemeinen früher in der Routentabelle aufgeführt als die spezifischeren Routen ohne Bereich.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-382">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="e0a2a-383">Die Routenwerte im oben genannten Beispiel werden der folgenden Aktion zugeordnet:</span><span class="sxs-lookup"><span data-stu-id="e0a2a-383">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="e0a2a-384">`AreaAttribute` kennzeichnet einen Controller als Teil eines Bereichs, z.B. des Bereichs `Blog`.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-384">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="e0a2a-385">Controller ohne `[Area]`-Attribut gehören demnach zu keinem Bereich und stimmen **nicht** überein, wenn die `area`-Route wird vom Routing bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-385">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="e0a2a-386">Im folgenden Beispiel kann nur der erste aufgelistete Controller die Routenwerte `{ area = Blog, controller = Users, action = AddUser }` abgleichen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-386">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/sample/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/sample/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="e0a2a-387">Aus Gründen der Vollständigkeit wird hier der Namespace jedes Controllers dargestellt. Andernfalls gäbe es einen Namenskonflikt zwischen den Controllern, und ein Compilerfehler würde generiert.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-387">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="e0a2a-388">Klassennamespaces haben keine Auswirkungen auf das MVC-Routing.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-388">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="e0a2a-389">Die ersten beiden Controller gehören zu Bereichen und werden können nur abgleichen, wenn ihr jeweiliger Bereichsname vom `area`-Routenwert bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-389">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="e0a2a-390">Der dritte Controller gehört keinem Bereich an und kann nur abgleichen, wenn vom Routing kein Wert für `area` bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-390">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="e0a2a-391">Im Hinblick auf das Erkennen *keines Werts* hat die Abwesenheit des `area`-Werts dieselben Auswirkungen, wie wenn der Wert für `area` 0 (null) oder eine leere Zeichenfolge wäre.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-391">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="e0a2a-392">Beim Ausführen einer Aktion innerhalb eines Bereichs ist der Routenwert für `area` als *Umgebungswert* für das Routing verfügbar und kann für die URL-Generierung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-392">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="e0a2a-393">Das bedeutet, dass Bereiche bei der URL-Generierung wie im folgenden Beispiel dargestellt standardmäßig *beständig* sind.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-393">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/sample/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/sample/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="e0a2a-394">Verstehen von IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="e0a2a-394">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="e0a2a-395">Dieser Abschnitt enthält Details zu Framework-Mechanismen und dazu, wie eine auszuführende Aktion in MVC auswählt wird.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-395">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="e0a2a-396">Eine typische Anwendung benötigt kein benutzerdefiniertes `IActionConstraint`.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-396">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="e0a2a-397">Sie haben `IActionConstraint` wahrscheinlich bereits verwendet, auch wenn Sie mit der Oberfläche noch nicht vertraut sind.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-397">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="e0a2a-398">Das `[HttpGet]`-Attribut und ähnliche `[Http-VERB]`-Attribute implementieren `IActionConstraint`, um die Ausführung einer Aktionsmethode einzuschränken.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-398">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="e0a2a-399">Unter Annahme der herkömmlichen Standardroute würde der URL-Pfad `/Products/Edit` die Werte `{ controller = Products, action = Edit }` erzeugen, die **beiden** hier gezeigten Aktionen entsprechen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-399">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="e0a2a-400">In `IActionConstraint`-Terminologie würde man sagen, dass es sich bei beiden genannten Aktionen um Kandidaten handelt, da beide den Routendaten entsprechen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-400">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="e0a2a-401">Wenn `HttpGetAttribute` ausgeführt wird, wird angegeben, dass *Edit()* mit *GET* übereinstimmt und mit keinem anderen HTTP-Verb.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-401">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="e0a2a-402">Für die `Edit(...)`-Aktion wurde keine Einschränkungen definiert, und daher stimmt sie mit allen HTTP-Verben überein.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-402">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="e0a2a-403">Wenn wir von `POST` ausgehen, stimmt nur `Edit(...)` überein.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-403">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="e0a2a-404">Für `GET` können jedoch beide Aktionen übereinstimmen. Eine Aktion mit `IActionConstraint` wird einer Aktion ohne jedoch immer *vorgezogen*.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-404">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="e0a2a-405">Da `Edit()` über `[HttpGet]` verfügt, gilt die Methode als spezifischer und wird ausgewählt, wenn beide Aktionen das Verb erkennen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-405">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="e0a2a-406">Im Prinzip ist `IActionConstraint` eine Form der *Überladung*. Anstatt jedoch Methoden mit demselben Namen zu überladen, führt es zu einer Überladung zwischen Aktionen, die dieselbe URL erkennen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-406">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="e0a2a-407">Beim Attributrouting wird auch `IActionConstraint` verwendet. Aus diesem Grund kann es zu Aktionen aus verschiedenen Controllern führen, die beide als Kandidaten gelten.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-407">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="e0a2a-408">Implementieren von IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="e0a2a-408">Implementing IActionConstraint</span></span>

<span data-ttu-id="e0a2a-409">`IActionConstraint` lässt sich am einfachsten erstellen, indem Sie eine abgeleitete Klasse von `System.Attribute` erstellen und sie auf Ihren Aktionen und Controllern platzieren.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-409">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="e0a2a-410">MVC erkennt automatisch jedes `IActionConstraint`, das als Attribut verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-410">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="e0a2a-411">Sie können mithilfe des Anwendungsmodells Einschränkungen anwenden. Dies ist wahrscheinlich die flexibelste Herangehensweise, da Sie über Metaprogrammierung festlegen können, wie die Einschränkungen angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-411">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="e0a2a-412">Im folgenden Beispiel wählt eine Einschränkung eine Aktion auf Grundlage einer *Landeskennzahl* aus den Routendaten aus.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-412">In the following example a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="e0a2a-413">[Das vollständige Beispiel finden Sie auf GitHub.](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs)</span><span class="sxs-lookup"><span data-stu-id="e0a2a-413">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

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

<span data-ttu-id="e0a2a-414">Sie sind für die Implementierung der `Accept`-Methode und die Festlegung einer Reihenfolge (Order) verantwortlich, in der die Einschränkung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-414">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="e0a2a-415">In diesem Fall gibt die `Accept`-Methode `true` zurück, um zu kennzeichnen, dass die Aktion eine Übereinstimmung ist, wenn die `country`-Routenwerte übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-415">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="e0a2a-416">Dies unterscheidet sich von `RouteValueAttribute` dahingehend, dass ein Fallback auf eine Aktion ohne Attribute zulässig ist.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-416">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="e0a2a-417">In dem Beispiel wird gezeigt, dass wenn Sie eine `en-US`-Aktion definieren, ein Ländercode wie `fr-FR` auf einen generischeren Controller zurückgreift, für den `[CountrySpecific(...)]` nicht angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-417">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="e0a2a-418">Die `Order`-Eigenschaft entscheidet, zu welcher *Phase* die Einschränkung gehört.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-418">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="e0a2a-419">Aktionseinschränkungen werden auf Grundlage von `Order` in Gruppen ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-419">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="e0a2a-420">Beispiel: Alle vom Framework bereitgestellten HTTP-Methodenattribute verwenden denselben `Order`-Wert, sodass sie in derselben Phase ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-420">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="e0a2a-421">Es kann so viele Phasen geben, wie zur Implementierung der gewünschten Richtlinien notwendig.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-421">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="e0a2a-422">Bei der Entscheidung über einen Wert für `Order` sollten Sie berücksichtigen, ob die Einschränkung vor HTTP-Methoden angewendet werden soll oder nicht.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-422">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="e0a2a-423">Niedrigere Zahlen werden zuerst ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="e0a2a-423">Lower numbers run first.</span></span>

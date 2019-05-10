---
title: 'Razor-Seiten: Routen- und App-Konventionen in ASP.NET Core'
author: guardrex
description: Erfahren Sie, wie Konventionen für Routen- und App-Modellanbieter Sie beim Steuern von Seitenrouting, Ermittlung und Verarbeitung unterstützen können.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/07/2019
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: 4e07b5803adbce94982584212fa65afbfd427b64
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64893507"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a><span data-ttu-id="6a587-103">Razor-Seiten: Routen- und App-Konventionen in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6a587-103">Razor Pages route and app conventions in ASP.NET Core</span></span>

<span data-ttu-id="6a587-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6a587-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6a587-105">Erfahren Sie, wie Sie in Apps für Razor-Seiten mithilfe von [Konventionen für Seitenrouten und App-Modellanbieter](xref:mvc/controllers/application-model#conventions) das Seitenrouting, die Ermittlung und die Verarbeitung steuern können.</span><span class="sxs-lookup"><span data-stu-id="6a587-105">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="6a587-106">Wenn Sie für einzelne Seiten benutzerdefinierte Seitenrouten konfigurieren müssen, sollten Sie das Routing zu den Seiten mithilfe der [AddPageRoute-Konvention](#configure-a-page-route) konfigurieren, die weiter unten in diesem Artikel beschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="6a587-106">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="6a587-107">Verwenden Sie zum Angeben einer seitenroute, Hinzufügen von routensegmenten oder Hinzufügen von Parametern für eine Route, die der Seite `@page` Richtlinie.</span><span class="sxs-lookup"><span data-stu-id="6a587-107">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="6a587-108">Weitere Informationen finden Sie unter [benutzerdefinierte Routen](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="6a587-108">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="6a587-109">Es sind reservierte Wörter, die als routensegmenten oder Parameternamen verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="6a587-109">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="6a587-110">Weitere Informationen finden Sie unter [Routing: Reservierte routing Namen](xref:fundamentals/routing#reserved-routing-names).</span><span class="sxs-lookup"><span data-stu-id="6a587-110">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="6a587-111">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6a587-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="6a587-112">Szenario</span><span class="sxs-lookup"><span data-stu-id="6a587-112">Scenario</span></span> | <span data-ttu-id="6a587-113">Dieses Beispiel veranschaulicht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6a587-113">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="6a587-114">Model conventions (Modellkonventionen)</span><span class="sxs-lookup"><span data-stu-id="6a587-114">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="6a587-115">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="6a587-115">Conventions.Add</span></span><ul><li><span data-ttu-id="6a587-116">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="6a587-116">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="6a587-117">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="6a587-117">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="6a587-118">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="6a587-118">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="6a587-119">Das Hinzufügen einer Routenvorlage und eines Headers zu den Seiten einer App</span><span class="sxs-lookup"><span data-stu-id="6a587-119">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="6a587-120">Konventionen für Seitenroutenaktionen</span><span class="sxs-lookup"><span data-stu-id="6a587-120">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="6a587-121">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="6a587-121">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="6a587-122">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="6a587-122">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="6a587-123">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="6a587-123">AddPageRoute</span></span></li></ul> | <span data-ttu-id="6a587-124">Das Hinzufügen einer Routenvorlage zu Seiten in einem Ordner und zu einer Einzelseite</span><span class="sxs-lookup"><span data-stu-id="6a587-124">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="6a587-125">Konventionen für Seitenmodellaktionen</span><span class="sxs-lookup"><span data-stu-id="6a587-125">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="6a587-126">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="6a587-126">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="6a587-127">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="6a587-127">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="6a587-128">ConfigureFilter (Filterklasse, Lambdaausdruck oder Filterzuordnungsinstanz)</span><span class="sxs-lookup"><span data-stu-id="6a587-128">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="6a587-129">Das Hinzufügen eines Headers zu Seiten in einem Ordner, das Hinzufügen eines Headers zu einer einzelnen Seite und das Konfigurieren einer [Filter-Factoy](xref:mvc/controllers/filters#ifilterfactory) zum Hinzufügen eines Headers zu den Seiten einer App.</span><span class="sxs-lookup"><span data-stu-id="6a587-129">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="6a587-130">Konventionen für Razor-Seiten werden hinzugefügt und konfiguriert die <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Erweiterungsmethode <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> auf die Sammlung von Diensten in der `Startup` Klasse.</span><span class="sxs-lookup"><span data-stu-id="6a587-130">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="6a587-131">Die folgenden Beispiele der Konvention werden später in diesem Thema erläutert:</span><span class="sxs-lookup"><span data-stu-id="6a587-131">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
            {
                options.Conventions.Add( ... );
                options.Conventions.AddFolderRouteModelConvention("/OtherPages", model => { ... });
                options.Conventions.AddPageRouteModelConvention("/About", model => { ... });
                options.Conventions.AddPageRoute("/Contact", "TheContactPage/{text?}");
                options.Conventions.AddFolderApplicationModelConvention("/OtherPages", model => { ... });
                options.Conventions.AddPageApplicationModelConvention("/About", model => { ... });
                options.Conventions.ConfigureFilter(model => { ... });
                options.Conventions.ConfigureFilter( ... );
            });
}
```

## <a name="route-order"></a><span data-ttu-id="6a587-132">Reihenfolge</span><span class="sxs-lookup"><span data-stu-id="6a587-132">Route order</span></span>

<span data-ttu-id="6a587-133">Routen geben eine <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> für die Verarbeitung (Route übereinstimmend).</span><span class="sxs-lookup"><span data-stu-id="6a587-133">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="6a587-134">Auftrag</span><span class="sxs-lookup"><span data-stu-id="6a587-134">Order</span></span>            | <span data-ttu-id="6a587-135">Verhalten</span><span class="sxs-lookup"><span data-stu-id="6a587-135">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="6a587-136">-1</span><span class="sxs-lookup"><span data-stu-id="6a587-136">-1</span></span>               | <span data-ttu-id="6a587-137">Die Route wird verarbeitet, bevor andere Routen verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="6a587-137">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="6a587-138">0</span><span class="sxs-lookup"><span data-stu-id="6a587-138">0</span></span>                | <span data-ttu-id="6a587-139">Reihenfolge nicht angegeben ist (Standardwert).</span><span class="sxs-lookup"><span data-stu-id="6a587-139">Order isn't specified (default value).</span></span> <span data-ttu-id="6a587-140">Kein Zuweisen von `Order` (`Order = null`) wird standardmäßig die Route `Order` auf 0 (null) für die Verarbeitung.</span><span class="sxs-lookup"><span data-stu-id="6a587-140">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="6a587-141">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="6a587-141">1, 2, &hellip; n</span></span> | <span data-ttu-id="6a587-142">Gibt die Verarbeitungsreihenfolge für die Route an.</span><span class="sxs-lookup"><span data-stu-id="6a587-142">Specifies the route processing order.</span></span> |

<span data-ttu-id="6a587-143">Die Verarbeitung Route wird gemäß der Konvention hergestellt:</span><span class="sxs-lookup"><span data-stu-id="6a587-143">Route processing is established by convention:</span></span>

* <span data-ttu-id="6a587-144">Routen werden in sequenzieller Reihenfolge verarbeitet (-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="6a587-144">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="6a587-145">Wenn die Routen haben die gleiche `Order`, wird die bestimmte Route zuerst gefolgt von weniger spezifische Routen abgeglichen wird.</span><span class="sxs-lookup"><span data-stu-id="6a587-145">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="6a587-146">Wenn Routen mit dem gleichen `Order` und die gleiche Anzahl von Parametern mit eine Anforderungs-URL übereinstimmen, Routen werden in der Reihenfolge verarbeitet, die hinzugefügt werden die <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span><span class="sxs-lookup"><span data-stu-id="6a587-146">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="6a587-147">Vermeiden Sie wenn möglich, je nach einem etablierten Route Verarbeitungsreihenfolge.</span><span class="sxs-lookup"><span data-stu-id="6a587-147">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="6a587-148">Im Allgemeinen wird beim routing mit URL-Zuordnung die richtige Route ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="6a587-148">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="6a587-149">Wenn Sie, Route festlegen müssen `Order` Eigenschaften zum Weiterleiten von Anforderungen ordnungsgemäß routing der app-Schema ist wahrscheinlich für Clients verwirrend und fehleranfälliger zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="6a587-149">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="6a587-150">Suchen Sie zur Vereinfachung der app-routing-Schema.</span><span class="sxs-lookup"><span data-stu-id="6a587-150">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="6a587-151">Die Beispiel-app erfordert eine explizite Route Verarbeitungsreihenfolge mehrere Routingszenarien verwenden eine einzelne app veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="6a587-151">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="6a587-152">Allerdings sollten Sie versuchen, vermeiden Sie die Methode der Einstellung Route `Order` in Produktions-apps.</span><span class="sxs-lookup"><span data-stu-id="6a587-152">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="6a587-153">Razor Pages-Routing und MVC Controller-Routing verwenden eine gemeinsame Implementierung.</span><span class="sxs-lookup"><span data-stu-id="6a587-153">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="6a587-154">Informationen zur Reihenfolge der MVC-Themen finden Sie unter [Routing zu Controlleraktionen: Ordnen der attributrouten](xref:mvc/controllers/routing#ordering-attribute-routes).</span><span class="sxs-lookup"><span data-stu-id="6a587-154">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="6a587-155">Modellkonventionen</span><span class="sxs-lookup"><span data-stu-id="6a587-155">Model conventions</span></span>

<span data-ttu-id="6a587-156">Fügen Sie einen Delegaten für <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> hinzuzufügende [modellieren Konventionen](xref:mvc/controllers/application-model#conventions) gilt für Razor-Seiten.</span><span class="sxs-lookup"><span data-stu-id="6a587-156">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="6a587-157">Hinzufügen einer routenmodellkonvention zu allen Seiten</span><span class="sxs-lookup"><span data-stu-id="6a587-157">Add a route model convention to all pages</span></span>

<span data-ttu-id="6a587-158">Verwendung <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> erstellen und Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> zur Auflistung der <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> model Konstruktion-Instanzen, die während der seitenroute angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="6a587-158">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="6a587-159">Die Beispielanwendung fügt dann zu allen Seiten der App eine `{globalTemplate?}`-Routenvorlage hinzu:</span><span class="sxs-lookup"><span data-stu-id="6a587-159">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="6a587-160">Die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> ist für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> auf `1` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="6a587-160">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="6a587-161">Dadurch wird die folgende Route Vergleichsverhalten in der Beispiel-app:</span><span class="sxs-lookup"><span data-stu-id="6a587-161">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="6a587-162">Eine routenvorlage für `TheContactPage/{text?}` wird später im Thema hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="6a587-162">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="6a587-163">Die Seite "Kontakt"-Route hat eine Standardreihenfolge der `null` (`Order = 0`), bevor Sie entsprechend der `{globalTemplate?}` routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="6a587-163">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="6a587-164">Ein `{aboutTemplate?}` routenvorlage wird später im Thema hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="6a587-164">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="6a587-165">Die Vorlage `{aboutTemplate?}` erhält den `Order` von `2`.</span><span class="sxs-lookup"><span data-stu-id="6a587-165">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="6a587-166">Wenn die Seite „Info“ unter `/About/RouteDataValue` angefordert wird, wird „RouteDataValue“ in die Vorlage `RouteData.Values["globalTemplate"]` geladen (`Order = 1`) und nicht in `RouteData.Values["aboutTemplate"]` (`Order = 2`), da die Eigenschaft `Order` auf null festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="6a587-166">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="6a587-167">Ein `{otherPagesTemplate?}` routenvorlage wird später im Thema hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="6a587-167">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="6a587-168">Die Vorlage `{otherPagesTemplate?}` erhält den `Order` von `2`.</span><span class="sxs-lookup"><span data-stu-id="6a587-168">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="6a587-169">Wenn eine Seite der *Seiten/OtherPages* Ordner mit einem Routenparameter angefordert wird (z. B. `/OtherPages/Page1/RouteDataValue`), wird "RouteDataValue" in den geladen `RouteData.Values["globalTemplate"]` (`Order = 1`) und nicht `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) aufgrund der Einstellung der `Order` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="6a587-169">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="6a587-170">Wo immer dies möglich ist, legen Sie nicht die `Order`, was dazu führt `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="6a587-170">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="6a587-171">Wählen Sie die richtige Route routing nutzen.</span><span class="sxs-lookup"><span data-stu-id="6a587-171">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="6a587-172">Razor-Seiten-Optionen, z. B. das Hinzufügen <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, werden hinzugefügt, wenn die Sammlung von Diensten in MVC hinzugefügt wird `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6a587-172">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6a587-173">In der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) finden Sie ein Beispiel hierfür.</span><span class="sxs-lookup"><span data-stu-id="6a587-173">For an example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="6a587-174">Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About/GlobalRouteValue` an, und prüfen Sie das Ergebnis:</span><span class="sxs-lookup"><span data-stu-id="6a587-174">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Die Seite „Info“ wird mit einem Routensegment von GlobalRouteValue angefordert.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="6a587-177">Hinzufügen einer app-modellkonvention zu allen Seiten</span><span class="sxs-lookup"><span data-stu-id="6a587-177">Add an app model convention to all pages</span></span>

<span data-ttu-id="6a587-178">Verwenden <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> erstellen und Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> zur Auflistung der <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> -Instanzen, die während der angewendet werden Seite Modellentwicklung app.</span><span class="sxs-lookup"><span data-stu-id="6a587-178">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="6a587-179">Die Beispielanwendung enthält eine `AddHeaderAttribute`-Klasse, damit das Verwenden dieser und anderer Konventionen weiter unten in diesem Thema gezeigt werden kann.</span><span class="sxs-lookup"><span data-stu-id="6a587-179">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="6a587-180">Der Klassenkonstruktor akzeptiert eine `name`-Zeichenfolge und ein `values`-Zeichenfolgenarray.</span><span class="sxs-lookup"><span data-stu-id="6a587-180">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="6a587-181">Diese Werte werden in seiner `OnResultExecuting`-Methode verwendet, um einen Antwortheader einzurichten.</span><span class="sxs-lookup"><span data-stu-id="6a587-181">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="6a587-182">Die Klasse wird im Abschnitt [Seitenmodellaktionskonventionen](#page-model-action-conventions) weiter unten in diesem Artikel erläutert.</span><span class="sxs-lookup"><span data-stu-id="6a587-182">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="6a587-183">Die Beispielanwendung verwendet die Klasse `AddHeaderAttribute`, um den Header `GlobalHeader` zu allen Seiten der Anwendung hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="6a587-183">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="6a587-184">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="6a587-184">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="6a587-185">Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About` an, und überprüfen Sie die Header, um das Ergebnis zu sehen:</span><span class="sxs-lookup"><span data-stu-id="6a587-185">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Antwortheader der Seite „Info“ zeigen an, dass „GlobalHeader“ hinzugefügt wurde.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="6a587-187">Hinzufügen einer Ereignishandler-modellkonvention zu allen Seiten</span><span class="sxs-lookup"><span data-stu-id="6a587-187">Add a handler model convention to all pages</span></span>

<span data-ttu-id="6a587-188">Verwendung <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> erstellen und Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> auf die Auflistung von <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> -Instanzen, die während der angewendet werden Seite Modellentwicklung Handler.</span><span class="sxs-lookup"><span data-stu-id="6a587-188">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="6a587-189">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="6a587-189">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="6a587-190">Konventionen für Seitenroutenaktionen</span><span class="sxs-lookup"><span data-stu-id="6a587-190">Page route action conventions</span></span>

<span data-ttu-id="6a587-191">Der Standardwert für routenmodelle, die von abgeleitet <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> ruft Konventionen auf, die entwickelt wurden, um Erweiterungspunkte zum Konfigurieren von seitenrouten bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="6a587-191">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="6a587-192">Ordnerroutenmodellkonvention</span><span class="sxs-lookup"><span data-stu-id="6a587-192">Folder route model convention</span></span>

<span data-ttu-id="6a587-193">Verwenden <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> erstellen und Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> , das eine Aktion aufruft, auf die <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> für alle Seiten in dem angegebenen Ordner.</span><span class="sxs-lookup"><span data-stu-id="6a587-193">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="6a587-194">Die Beispielanwendung verwendet <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>, um eine `{otherPagesTemplate?}`-Routenvorlage zu den Seiten im Ordner *OtherPages* hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="6a587-194">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="6a587-195">Die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> ist für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> auf `2` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="6a587-195">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="6a587-196">Dadurch wird sichergestellt, dass die Vorlage für `{globalTemplate?}` (Legen Sie weiter oben in das Thema `1`) Bezug auf die erste Routendaten Position Wert, wenn ein einziger routenwert angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="6a587-196">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="6a587-197">Wenn eine Seite in der *Seiten/OtherPages* Ordner mit einem routenparameterwert angefordert wird (z. B. `/OtherPages/Page1/RouteDataValue`), wird "RouteDataValue" in den geladen `RouteData.Values["globalTemplate"]` (`Order = 1`) und nicht `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) aufgrund der Einstellung der `Order` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="6a587-197">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="6a587-198">Wo immer dies möglich ist, legen Sie nicht die `Order`, was dazu führt `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="6a587-198">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="6a587-199">Wählen Sie die richtige Route routing nutzen.</span><span class="sxs-lookup"><span data-stu-id="6a587-199">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="6a587-200">Fordern Sie die Seite „Seite1“ der Beispielanwendung unter `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` an, und prüfen Sie das Ergebnis:</span><span class="sxs-lookup"><span data-stu-id="6a587-200">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![Seite1 aus dem Ordner „OtherPages“ wird mit einem Routensegment von GlobalRouteValue und OtherPagesRouteValue angefordert.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="6a587-203">Seitenroutenmodellkonvention</span><span class="sxs-lookup"><span data-stu-id="6a587-203">Page route model convention</span></span>

<span data-ttu-id="6a587-204">Verwenden <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> erstellen und Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> , das eine Aktion aufruft, auf die <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> für die Seite mit dem angegebenen Namen.</span><span class="sxs-lookup"><span data-stu-id="6a587-204">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="6a587-205">Die Beispielanwendung verwendet `AddPageRouteModelConvention`, um eine `{aboutTemplate?}`-Routenvorlage zu der Seite „Info“ hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="6a587-205">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="6a587-206">Die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> ist für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> auf `2` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="6a587-206">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="6a587-207">Dadurch wird sichergestellt, dass die Vorlage für `{globalTemplate?}` (Legen Sie weiter oben in das Thema `1`) Bezug auf die erste Routendaten Position Wert, wenn ein einziger routenwert angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="6a587-207">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="6a587-208">Wenn die Seite "Info" angefordert wird, mit dem Route-Parameterwert an `/About/RouteDataValue`, wird "RouteDataValue" in den geladen `RouteData.Values["globalTemplate"]` (`Order = 1`) und nicht `RouteData.Values["aboutTemplate"]` (`Order = 2`) aufgrund der Einstellung der `Order` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="6a587-208">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="6a587-209">Wo immer dies möglich ist, legen Sie nicht die `Order`, was dazu führt `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="6a587-209">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="6a587-210">Wählen Sie die richtige Route routing nutzen.</span><span class="sxs-lookup"><span data-stu-id="6a587-210">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="6a587-211">Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About/GlobalRouteValue/AboutRouteValue` an, und prüfen Sie das Ergebnis:</span><span class="sxs-lookup"><span data-stu-id="6a587-211">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![Die Seite „Info“ wird mit Routensegmenten für GlobalRouteValue und AboutRouteValue angefordert.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

::: moniker range=">= aspnetcore-2.2"

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="6a587-214">Verwenden Sie einen Transformator Parameter zum Anpassen von seitenrouten</span><span class="sxs-lookup"><span data-stu-id="6a587-214">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="6a587-215">Von ASP.NET Core generierte seitenrouten können mit der ein Transformator Parameter angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="6a587-215">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="6a587-216">Ein Parametertransformator implementiert `IOutboundParameterTransformer` und wandelt den Wert der Parameter um.</span><span class="sxs-lookup"><span data-stu-id="6a587-216">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="6a587-217">Beispielsweise ändert ein benutzerdefinierter `SlugifyParameterTransformer`-Parametertransformator den Routenwert `SubscriptionManagement` in `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="6a587-217">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="6a587-218">Die `PageRouteTransformerConvention` seitenroutenmodellkonvention gilt einen Transformator Parameter für die namenssegmente Ordner- und von automatisch generierten Seitenprofilklasse Routen in einer app.</span><span class="sxs-lookup"><span data-stu-id="6a587-218">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="6a587-219">Z. B. die Razor-Seiten, die auf Dateien */Pages/SubscriptionManagement/ViewAll.cshtml* müsste der Route aus umgeschrieben `/SubscriptionManagement/ViewAll` zu `/subscription-management/view-all`.</span><span class="sxs-lookup"><span data-stu-id="6a587-219">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="6a587-220">`PageRouteTransformerConvention` nur transformiert die automatisch generierten Segmenten einer seitenroute, die von den Razor-Seiten-Ordner und Dateinamen stammen.</span><span class="sxs-lookup"><span data-stu-id="6a587-220">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="6a587-221">Keine Transformation routensegmenten hinzugefügt, mit der `@page` Richtlinie.</span><span class="sxs-lookup"><span data-stu-id="6a587-221">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="6a587-222">Die Konvention nicht transformiert und außerdem hinzugefügten Routen <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span><span class="sxs-lookup"><span data-stu-id="6a587-222">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="6a587-223">Die `PageRouteTransformerConvention` registriert, ist als eine Option in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6a587-223">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
            {
                options.Conventions.Add(
                    new PageRouteTransformerConvention(
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

## <a name="configure-a-page-route"></a><span data-ttu-id="6a587-224">Konfigurieren einer Seitenroute</span><span class="sxs-lookup"><span data-stu-id="6a587-224">Configure a page route</span></span>

<span data-ttu-id="6a587-225">Verwendung <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> so konfigurieren Sie eine Route zu einer Seite am angegebenen Seitenpfad.</span><span class="sxs-lookup"><span data-stu-id="6a587-225">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="6a587-226">Generierte Links, die auf die Seite verweisen, verwenden die von Ihnen angegebene Route.</span><span class="sxs-lookup"><span data-stu-id="6a587-226">Generated links to the page use your specified route.</span></span> <span data-ttu-id="6a587-227">`AddPageRoute` verwendet die `AddPageRouteModelConvention` zum Aufbauen der Route.</span><span class="sxs-lookup"><span data-stu-id="6a587-227">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="6a587-228">Die Beispielanwendung erstellt für *Contact.cshtml* eine Route zur Seite `/TheContactPage`:</span><span class="sxs-lookup"><span data-stu-id="6a587-228">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="6a587-229">Die Seite „Kontakt“ kann auch unter `/Contact` über ihre Standardroute erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="6a587-229">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="6a587-230">Die benutzerdefinierte Route der Beispielanwendung, die zur Seite „Kontakt“ führt, ermöglicht das Verwenden eines optionalen `text`-Routensegments (`{text?}`).</span><span class="sxs-lookup"><span data-stu-id="6a587-230">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="6a587-231">Die Seite enthält dieses optionale Segment auch in ihrer `@page`-Anweisung, für den Fall, dass der Besucher über die `/Contact`-Route auf die Seite zugreift:</span><span class="sxs-lookup"><span data-stu-id="6a587-231">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="6a587-232">Beachten Sie, dass die URL, die für den Link **Kontakt** in der gerenderten Seite generiert wurde, die aktualisierte Route widerspiegelt:</span><span class="sxs-lookup"><span data-stu-id="6a587-232">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Kontaktlink der Beispielanwendung in der Navigationsleiste](razor-pages-conventions/_static/contact-link.png)

![Beim Überprüfen des Kontaktlinks in der gerenderten HTML zeigt sich, dass das HREF-Attribut auf „/TheContactPage“ festgelegt wurde.](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="6a587-235">Sie können die Kontaktseite entweder über deren übliche Route, `/Contact`, oder über die benutzerdefinierte Route, `/TheContactPage`, besuchen.</span><span class="sxs-lookup"><span data-stu-id="6a587-235">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="6a587-236">Wenn Sie ein zusätzliches `text`-Routensegment bereitstellen, wird dieses HTML-codierte Segment auf der Seite angezeigt:</span><span class="sxs-lookup"><span data-stu-id="6a587-236">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![Beispiel für das Bereitstellen eines optionalen „Text“-Routensegments mit „TextValue“ in der URL in der Ansicht des Microsoft Edge-Browsers](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="6a587-239">Konventionen für Seitenmodellaktionen</span><span class="sxs-lookup"><span data-stu-id="6a587-239">Page model action conventions</span></span>

<span data-ttu-id="6a587-240">Der Standardanbieter für seitenmodelle, die implementiert <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> ruft Konventionen auf, die entwickelt wurden, um Erweiterungspunkte zum Konfigurieren von seitenmodellen bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="6a587-240">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="6a587-241">Diese Konventionen sind beim Erstellen und Ändern von Seitenermittlungs- und Verarbeitungsszenarios hilfreich.</span><span class="sxs-lookup"><span data-stu-id="6a587-241">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="6a587-242">Für die Beispiele in diesem Abschnitt, die Beispiel-app verwendet eine `AddHeaderAttribute` Klasse, die eine <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, die für einen Antwortheader gilt:</span><span class="sxs-lookup"><span data-stu-id="6a587-242">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="6a587-243">Das Beispiel veranschaulicht mithilfe von Konventionen, wie das Attribut auf alle Seiten in einem Ordner und auf eine Einzelseite angewendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="6a587-243">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="6a587-244">**Ordner-App-Modellkonvention**</span><span class="sxs-lookup"><span data-stu-id="6a587-244">**Folder app model convention**</span></span>

<span data-ttu-id="6a587-245">Verwendung <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> erstellen und Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> , die auf eine Aktion aufruft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> Instanzen für alle Seiten in den angegebenen Ordner.</span><span class="sxs-lookup"><span data-stu-id="6a587-245">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="6a587-246">Im Beispiel wird die Verwendung der `AddFolderApplicationModelConvention` durch Hinzufügen eines Headers, `OtherPagesHeader`, zu den Seiten im Ordner *OtherPages* der Anwendung veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="6a587-246">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="6a587-247">Fordern Sie die Seite „Seite1“ der Beispielanwendung unter `localhost:5000/OtherPages/Page1` an, und prüfen Sie die Header, um das Ergebnis zu sehen:</span><span class="sxs-lookup"><span data-stu-id="6a587-247">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Antwortheader der Seite „OtherPages/Seite1“ zeigt, dass der OtherPagesHeader hinzugefügt wurde.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="6a587-249">**Seiten-App-Modellkonvention**</span><span class="sxs-lookup"><span data-stu-id="6a587-249">**Page app model convention**</span></span>

<span data-ttu-id="6a587-250">Verwenden <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> erstellen und Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> , das eine Aktion aufruft, auf die <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> für die Seite mit dem angegebenen Namen.</span><span class="sxs-lookup"><span data-stu-id="6a587-250">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="6a587-251">Im Beispiel wird das Verwenden von `AddPageApplicationModelConvention` durch Hinzufügen eines Headers, `AboutHeader`, auf der Seite „Info“ veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="6a587-251">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="6a587-252">Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About` an, und überprüfen Sie die Header, um das Ergebnis zu sehen:</span><span class="sxs-lookup"><span data-stu-id="6a587-252">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Antwortheader der Seite „Info“ zeigen, dass AboutHeader hinzugefügt wurde.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="6a587-254">**Konfigurieren eines Filters**</span><span class="sxs-lookup"><span data-stu-id="6a587-254">**Configure a filter**</span></span>

<span data-ttu-id="6a587-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> konfiguriert den angegebenen Filter anwenden.</span><span class="sxs-lookup"><span data-stu-id="6a587-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="6a587-256">Sie können Filterklassen selbst implementieren. In der Beispielanwendung wird jedoch gezeigt, wie Sie einen Filter in einen Lambdaausdruck implementieren. Dieser wird dann im Hintergrund als Zuordnungsinstanz implementiert, die einen Filter zurückgibt:</span><span class="sxs-lookup"><span data-stu-id="6a587-256">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="6a587-257">Das Seiten-App-Modell wird verwendet, um den relativen Pfad für Segmente zu überprüfen, die zur Seite „Seite2“ im Ordner *OtherPages* führen.</span><span class="sxs-lookup"><span data-stu-id="6a587-257">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="6a587-258">Wenn die Bedingung erfüllt ist, wird ein Header hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="6a587-258">If the condition passes, a header is added.</span></span> <span data-ttu-id="6a587-259">Wenn dies nicht der Fall ist, wird der `EmptyFilter` angewendet.</span><span class="sxs-lookup"><span data-stu-id="6a587-259">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="6a587-260">`EmptyFilter` ist ein [Aktionsfilter](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="6a587-260">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="6a587-261">Wenn der Pfad `OtherPages/Page2` nicht enthält, hat der `EmptyFilter`, wie vorgesehen, keine Funktion, da Aktionsfilter von Razor Pages ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="6a587-261">Since Action filters are ignored by Razor Pages, the `EmptyFilter` no-ops as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="6a587-262">Fordern Sie die Seite „Seite2“ der Beispielanwendung unter `localhost:5000/OtherPages/Page2` an, und prüfen Sie die Header, um das Ergebnis zu sehen:</span><span class="sxs-lookup"><span data-stu-id="6a587-262">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![Der Header „OtherPagesPage2Header“ wird zur Antwort für „Seite2“ hinzugefügt.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="6a587-264">**Konfigurieren einer Filterzuordnungsinstanz**</span><span class="sxs-lookup"><span data-stu-id="6a587-264">**Configure a filter factory**</span></span>

<span data-ttu-id="6a587-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> konfiguriert die angegebene Zuordnungsinstanz so gelten [Filter](xref:mvc/controllers/filters) für alle Razor-Seiten.</span><span class="sxs-lookup"><span data-stu-id="6a587-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="6a587-266">Die Beispielanwendung bietet Ihnen die Möglichkeit des beispielhaften Verwendens einer [Filterzuordnungsinstanz](xref:mvc/controllers/filters#ifilterfactory) durch Hinzufügen des Headers `FilterFactoryHeader` zu den Seiten der Anwendung, der zwei Werte besitzt:</span><span class="sxs-lookup"><span data-stu-id="6a587-266">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="6a587-267">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="6a587-267">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="6a587-268">Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About` an, und überprüfen Sie die Header, um das Ergebnis zu sehen:</span><span class="sxs-lookup"><span data-stu-id="6a587-268">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Die Antwortheader der Seite „Info“ zeigen, dass zwei Header des Typs „FilterFactoryHeader“ hinzugefügt wurden.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="6a587-270">Die MVC-Filter und der Seitenfilter (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="6a587-270">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="6a587-271">MVC-[Aktionsfilter](xref:mvc/controllers/filters#action-filters) werden von Razor Pages ignoriert, da diese Handlermethoden verwenden.</span><span class="sxs-lookup"><span data-stu-id="6a587-271">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="6a587-272">Andere Arten von MVC-Filter sind verfügbar, Sie verwenden: [Autorisierung](xref:mvc/controllers/filters#authorization-filters), [Ausnahme](xref:mvc/controllers/filters#exception-filters), [Ressource](xref:mvc/controllers/filters#resource-filters), und [Ergebnis](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="6a587-272">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="6a587-273">Weitere Informationen finden Sie im Thema [Filter](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="6a587-273">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="6a587-274">Der Seitenfilter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) ist ein Filter, die auf Razor Pages anwendbar.</span><span class="sxs-lookup"><span data-stu-id="6a587-274">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="6a587-275">Weitere Informationen finden Sie unter [Filtermethoden für Razor-Seiten](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="6a587-275">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6a587-276">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="6a587-276">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

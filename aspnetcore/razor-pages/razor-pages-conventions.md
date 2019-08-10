---
title: 'Razor-Seiten: Routen- und App-Konventionen in ASP.NET Core'
author: guardrex
description: Erfahren Sie, wie Konventionen für Routen- und App-Modellanbieter Sie beim Steuern von Seitenrouting, Ermittlung und Verarbeitung unterstützen können.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/08/2019
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: c93f169c422d260f738faba4812861521f383e51
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68914975"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a><span data-ttu-id="5ca1b-103">Razor-Seiten: Routen- und App-Konventionen in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5ca1b-103">Razor Pages route and app conventions in ASP.NET Core</span></span>

<span data-ttu-id="5ca1b-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5ca1b-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5ca1b-105">Erfahren Sie, wie Sie in Apps für Razor-Seiten mithilfe von [Konventionen für Seitenrouten und App-Modellanbieter](xref:mvc/controllers/application-model#conventions) das Seitenrouting, die Ermittlung und die Verarbeitung steuern können.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-105">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="5ca1b-106">Wenn Sie für einzelne Seiten benutzerdefinierte Seitenrouten konfigurieren müssen, sollten Sie das Routing zu den Seiten mithilfe der [AddPageRoute-Konvention](#configure-a-page-route) konfigurieren, die weiter unten in diesem Artikel beschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-106">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="5ca1b-107">Verwenden Sie die- `@page` Direktive der Seite, um eine Seiten Route anzugeben, Routen Segmente hinzuzufügen oder einer Route Parameter hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-107">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="5ca1b-108">Weitere Informationen finden Sie unter [benutzerdefinierte Routen](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="5ca1b-108">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="5ca1b-109">Es gibt reservierte Wörter, die nicht als Routen Segmente oder Parameternamen verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-109">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="5ca1b-110">Weitere Informationen finden [Sie unter Routing: Reservierte Routing](xref:fundamentals/routing#reserved-routing-names)Namen.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-110">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="5ca1b-111">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5ca1b-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="5ca1b-112">Szenario</span><span class="sxs-lookup"><span data-stu-id="5ca1b-112">Scenario</span></span> | <span data-ttu-id="5ca1b-113">Dieses Beispiel veranschaulicht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="5ca1b-113">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="5ca1b-114">Model conventions (Modellkonventionen)</span><span class="sxs-lookup"><span data-stu-id="5ca1b-114">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="5ca1b-115">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="5ca1b-115">Conventions.Add</span></span><ul><li><span data-ttu-id="5ca1b-116">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="5ca1b-116">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="5ca1b-117">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="5ca1b-117">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="5ca1b-118">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="5ca1b-118">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="5ca1b-119">Das Hinzufügen einer Routenvorlage und eines Headers zu den Seiten einer App</span><span class="sxs-lookup"><span data-stu-id="5ca1b-119">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="5ca1b-120">Konventionen für Seitenroutenaktionen</span><span class="sxs-lookup"><span data-stu-id="5ca1b-120">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="5ca1b-121">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="5ca1b-121">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="5ca1b-122">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="5ca1b-122">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="5ca1b-123">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="5ca1b-123">AddPageRoute</span></span></li></ul> | <span data-ttu-id="5ca1b-124">Das Hinzufügen einer Routenvorlage zu Seiten in einem Ordner und zu einer Einzelseite</span><span class="sxs-lookup"><span data-stu-id="5ca1b-124">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="5ca1b-125">Konventionen für Seitenmodellaktionen</span><span class="sxs-lookup"><span data-stu-id="5ca1b-125">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="5ca1b-126">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="5ca1b-126">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="5ca1b-127">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="5ca1b-127">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="5ca1b-128">ConfigureFilter (Filterklasse, Lambdaausdruck oder Filterzuordnungsinstanz)</span><span class="sxs-lookup"><span data-stu-id="5ca1b-128">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="5ca1b-129">Das Hinzufügen eines Headers zu Seiten in einem Ordner, das Hinzufügen eines Headers zu einer einzelnen Seite und das Konfigurieren einer [Filter-Factoy](xref:mvc/controllers/filters#ifilterfactory) zum Hinzufügen eines Headers zu den Seiten einer App.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-129">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="5ca1b-130">Razor Pages Konventionen werden mithilfe der <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> -Erweiterungsmethode für <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> die Dienst Auflistung in der `Startup` -Klasse hinzugefügt und konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-130">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="5ca1b-131">Die folgenden Beispiele der Konvention werden später in diesem Thema erläutert:</span><span class="sxs-lookup"><span data-stu-id="5ca1b-131">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
            {
                options.Conventions.Add( ... );
                options.Conventions.AddFolderRouteModelConvention(
                    "/OtherPages", model => { ... });
                options.Conventions.AddPageRouteModelConvention(
                    "/About", model => { ... });
                options.Conventions.AddPageRoute(
                    "/Contact", "TheContactPage/{text?}");
                options.Conventions.AddFolderApplicationModelConvention(
                    "/OtherPages", model => { ... });
                options.Conventions.AddPageApplicationModelConvention(
                    "/About", model => { ... });
                options.Conventions.ConfigureFilter(model => { ... });
                options.Conventions.ConfigureFilter( ... );
            });
}
```

## <a name="route-order"></a><span data-ttu-id="5ca1b-132">Routen Reihenfolge</span><span class="sxs-lookup"><span data-stu-id="5ca1b-132">Route order</span></span>

<span data-ttu-id="5ca1b-133">Routen geben einen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> für die Verarbeitung an (Routen Abgleich).</span><span class="sxs-lookup"><span data-stu-id="5ca1b-133">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="5ca1b-134">Auftrag</span><span class="sxs-lookup"><span data-stu-id="5ca1b-134">Order</span></span>            | <span data-ttu-id="5ca1b-135">Verhalten</span><span class="sxs-lookup"><span data-stu-id="5ca1b-135">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="5ca1b-136">-1</span><span class="sxs-lookup"><span data-stu-id="5ca1b-136">-1</span></span>               | <span data-ttu-id="5ca1b-137">Die Route wird verarbeitet, bevor andere Routen verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-137">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="5ca1b-138">0</span><span class="sxs-lookup"><span data-stu-id="5ca1b-138">0</span></span>                | <span data-ttu-id="5ca1b-139">Die Reihenfolge wurde nicht angegeben (Standardwert).</span><span class="sxs-lookup"><span data-stu-id="5ca1b-139">Order isn't specified (default value).</span></span> <span data-ttu-id="5ca1b-140">Wenn Sie `Order` (`Order = null`) nicht zuweisen, `Order` wird die Route für die Verarbeitung standardmäßig auf 0 (null) gesetzt.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-140">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="5ca1b-141">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="5ca1b-141">1, 2, &hellip; n</span></span> | <span data-ttu-id="5ca1b-142">Gibt die Verarbeitungsreihenfolge der Route an.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-142">Specifies the route processing order.</span></span> |

<span data-ttu-id="5ca1b-143">Die Routen Verarbeitung wird gemäß der Konvention eingerichtet:</span><span class="sxs-lookup"><span data-stu-id="5ca1b-143">Route processing is established by convention:</span></span>

* <span data-ttu-id="5ca1b-144">Routen werden in sequenzieller Reihenfolge verarbeitet (-1, 0, 1 &hellip; , 2, n).</span><span class="sxs-lookup"><span data-stu-id="5ca1b-144">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="5ca1b-145">Wenn Routen identisch `Order`sind, wird die spezifischere Route zuerst abgeglichen, gefolgt von weniger spezifischen Routen.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-145">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="5ca1b-146">Wenn Routen mit der gleichen `Order` Anzahl von Parametern mit einer Anforderungs-URL übereinstimmen, werden Routen in der Reihenfolge verarbeitet, in der Sie <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>der hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-146">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="5ca1b-147">Vermeiden Sie nach Möglichkeit die Abhängigkeit von einer festgelegten Auftrags Verarbeitungsreihenfolge.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-147">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="5ca1b-148">Im Allgemeinen wählt das Routing die richtige Route mit URL-Übereinstimmung aus.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-148">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="5ca1b-149">Wenn Sie Routen `Order` Eigenschaften festlegen müssen, um Anforderungen ordnungsgemäß weiterzuleiten, ist das Routing Schema der APP wahrscheinlich verwirrend für Clients und anfällig für die Wartung.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-149">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="5ca1b-150">Versuchen Sie, das Routing Schema der APP zu vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-150">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="5ca1b-151">Die Beispiel-App erfordert eine explizite Routen Verarbeitungsreihenfolge, um verschiedene Routing Szenarien mithilfe einer einzelnen APP zu veranschaulichen.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-151">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="5ca1b-152">Sie sollten jedoch versuchen, die Vorgehensweise beim Festlegen von Route `Order` in Produktions-apps zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-152">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="5ca1b-153">Razor Pages-Routing und MVC Controller-Routing verwenden eine gemeinsame Implementierung.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-153">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="5ca1b-154">Informationen zur Routen Reihenfolge in den MVC-Themen [finden Sie unter Routing zu Controller Aktionen: Sortieren von Attribut](xref:mvc/controllers/routing#ordering-attribute-routes)Routen.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-154">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="5ca1b-155">Modellkonventionen</span><span class="sxs-lookup"><span data-stu-id="5ca1b-155">Model conventions</span></span>

<span data-ttu-id="5ca1b-156">Fügen Sie einen Delegaten für <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> hinzu, um [Modell Konventionen](xref:mvc/controllers/application-model#conventions) hinzuzufügen, die für Razor Pages gelten.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-156">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="5ca1b-157">Hinzufügen einer Routen Modell Konvention zu allen Seiten</span><span class="sxs-lookup"><span data-stu-id="5ca1b-157">Add a route model convention to all pages</span></span>

<span data-ttu-id="5ca1b-158">Verwenden <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> Sie, um der Auflistung <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> von-Instanzen, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> die während der Erstellung von Seiten Weiterleitungs Modellen angewendet werden, ein zu erstellen und hinzuzufügen</span><span class="sxs-lookup"><span data-stu-id="5ca1b-158">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="5ca1b-159">Die Beispielanwendung fügt dann zu allen Seiten der App eine `{globalTemplate?}`-Routenvorlage hinzu:</span><span class="sxs-lookup"><span data-stu-id="5ca1b-159">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="5ca1b-160">Die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> ist für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> auf `1` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-160">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="5ca1b-161">Dadurch wird das folgende Weiterleitungs Übereinstimmungs Verhalten in der Beispiel-App sichergestellt:</span><span class="sxs-lookup"><span data-stu-id="5ca1b-161">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="5ca1b-162">Eine Routen Vorlage für `TheContactPage/{text?}` wird später in diesem Thema hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-162">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="5ca1b-163">Die Route der Kontaktseite hat eine Standard Reihenfolge von `null` (`Order = 0`), sodass Sie `{globalTemplate?}` vor der Routen Vorlage übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-163">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="5ca1b-164">Eine `{aboutTemplate?}` Routen Vorlage wird später in diesem Thema hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-164">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="5ca1b-165">Die Vorlage `{aboutTemplate?}` erhält den `Order` von `2`.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-165">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="5ca1b-166">Wenn die Seite „Info“ unter `/About/RouteDataValue` angefordert wird, wird „RouteDataValue“ in die Vorlage `RouteData.Values["globalTemplate"]` geladen (`Order = 1`) und nicht in `RouteData.Values["aboutTemplate"]` (`Order = 2`), da die Eigenschaft `Order` auf null festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-166">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="5ca1b-167">Eine `{otherPagesTemplate?}` Routen Vorlage wird später in diesem Thema hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-167">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="5ca1b-168">Die Vorlage `{otherPagesTemplate?}` erhält den `Order` von `2`.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-168">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="5ca1b-169">Wenn eine Seite im Ordner *pages/otherpages* `/OtherPages/Page1/RouteDataValue`mit einem Routen Parameter (z. b.) angefordert wird, wird "routedatavalue" in`Order = 1` `RouteData.Values["globalTemplate"]` () und nicht `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) geladen, weil das `Order` -Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-169">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="5ca1b-170">Legen Sie, sofern möglich, `Order`nicht den fest, `Order = 0`der ergibt.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-170">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="5ca1b-171">Verlassen Sie sich auf das Routing, um die richtige Route auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-171">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="5ca1b-172">Razor Pages Optionen (z. b <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>. hinzufügen) werden hinzugefügt, wenn MVC der Dienst `Startup.ConfigureServices`Auflistung in hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-172">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5ca1b-173">In der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) finden Sie ein Beispiel hierfür.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-173">For an example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="5ca1b-174">Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About/GlobalRouteValue` an, und prüfen Sie das Ergebnis:</span><span class="sxs-lookup"><span data-stu-id="5ca1b-174">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![Die Seite „Info“ wird mit einem Routensegment von GlobalRouteValue angefordert.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="5ca1b-177">Hinzufügen einer APP-Modell Konvention zu allen Seiten</span><span class="sxs-lookup"><span data-stu-id="5ca1b-177">Add an app model convention to all pages</span></span>

<span data-ttu-id="5ca1b-178">Verwenden <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> Sie, um der Auflistung <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> von-Instanzen, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> die während der Erstellung von Seiten-App-Modellen angewendet werden, eine hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-178">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="5ca1b-179">Die Beispielanwendung enthält eine `AddHeaderAttribute`-Klasse, damit das Verwenden dieser und anderer Konventionen weiter unten in diesem Thema gezeigt werden kann.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-179">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="5ca1b-180">Der Klassenkonstruktor akzeptiert eine `name`-Zeichenfolge und ein `values`-Zeichenfolgenarray.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-180">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="5ca1b-181">Diese Werte werden in seiner `OnResultExecuting`-Methode verwendet, um einen Antwortheader einzurichten.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-181">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="5ca1b-182">Die Klasse wird im Abschnitt [Seitenmodellaktionskonventionen](#page-model-action-conventions) weiter unten in diesem Artikel erläutert.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-182">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="5ca1b-183">Die Beispielanwendung verwendet die Klasse `AddHeaderAttribute`, um den Header `GlobalHeader` zu allen Seiten der Anwendung hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="5ca1b-183">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="5ca1b-184">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="5ca1b-184">*Startup.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

::: moniker-end

<span data-ttu-id="5ca1b-185">Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About` an, und überprüfen Sie die Header, um das Ergebnis zu sehen:</span><span class="sxs-lookup"><span data-stu-id="5ca1b-185">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Antwortheader der Seite „Info“ zeigen an, dass „GlobalHeader“ hinzugefügt wurde.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="5ca1b-187">Hinzufügen einer handlermodellkonvention zu allen Seiten</span><span class="sxs-lookup"><span data-stu-id="5ca1b-187">Add a handler model convention to all pages</span></span>

<span data-ttu-id="5ca1b-188">Verwenden <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> Sie, um der Auflistung <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> von-Instanzen, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> die während der Erstellung des seitenhandlermodells angewendet werden, zu erstellen und hinzuzufügen</span><span class="sxs-lookup"><span data-stu-id="5ca1b-188">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="5ca1b-189">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="5ca1b-189">*Startup.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

::: moniker-end

## <a name="page-route-action-conventions"></a><span data-ttu-id="5ca1b-190">Konventionen für Seitenroutenaktionen</span><span class="sxs-lookup"><span data-stu-id="5ca1b-190">Page route action conventions</span></span>

<span data-ttu-id="5ca1b-191">Der von abgeleitete Standardrouten Modell Anbieter <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> ruft Konventionen auf, die zum Bereitstellen von Erweiterungs Punkten zum Konfigurieren von Seiten Routen entworfen wurden.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-191">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="5ca1b-192">Ordner Routen-Modell Konvention</span><span class="sxs-lookup"><span data-stu-id="5ca1b-192">Folder route model convention</span></span>

<span data-ttu-id="5ca1b-193">Verwenden <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> Sie, um einen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> zu erstellen und hinzuzufügen, der <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> eine Aktion für alle Seiten im angegebenen Ordner aufruft.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-193">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="5ca1b-194">Die Beispielanwendung verwendet <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>, um eine `{otherPagesTemplate?}`-Routenvorlage zu den Seiten im Ordner *OtherPages* hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="5ca1b-194">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

::: moniker-end

<span data-ttu-id="5ca1b-195">Die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> ist für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> auf `2` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-195">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="5ca1b-196">Dadurch wird sichergestellt, dass `{globalTemplate?}` die Vorlage für (die zuvor im `1`Thema auf festgelegt wurde) Priorität für die erste Route-Daten Wert Position erhält, wenn ein einzelner Routen Wert bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-196">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="5ca1b-197">Wenn eine Seite im Ordner *pages/otherpages* mit einem `/OtherPages/Page1/RouteDataValue`Routen Parameterwert (z. b.) angefordert wird, wird "routedatavalue" in`Order = 1` `RouteData.Values["globalTemplate"]` () und nicht `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) geladen, weil der `Order` -Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-197">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="5ca1b-198">Legen Sie, sofern möglich, `Order`nicht den fest, `Order = 0`der ergibt.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-198">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="5ca1b-199">Verlassen Sie sich auf das Routing, um die richtige Route auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-199">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="5ca1b-200">Fordern Sie die Seite „Seite1“ der Beispielanwendung unter `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` an, und prüfen Sie das Ergebnis:</span><span class="sxs-lookup"><span data-stu-id="5ca1b-200">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![Seite1 aus dem Ordner „OtherPages“ wird mit einem Routensegment von GlobalRouteValue und OtherPagesRouteValue angefordert.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="5ca1b-203">Seiten Weiterleitungs Modell Konvention</span><span class="sxs-lookup"><span data-stu-id="5ca1b-203">Page route model convention</span></span>

<span data-ttu-id="5ca1b-204">Verwenden <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> Sie, um eine <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> zu erstellen und hinzuzufügen, die <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> eine Aktion für die für die Seite mit dem angegebenen Namen aufruft.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-204">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="5ca1b-205">Die Beispielanwendung verwendet `AddPageRouteModelConvention`, um eine `{aboutTemplate?}`-Routenvorlage zu der Seite „Info“ hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="5ca1b-205">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

::: moniker-end

<span data-ttu-id="5ca1b-206">Die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> ist für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> auf `2` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-206">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="5ca1b-207">Dadurch wird sichergestellt, dass `{globalTemplate?}` die Vorlage für (die zuvor im `1`Thema auf festgelegt wurde) Priorität für die erste Route-Daten Wert Position erhält, wenn ein einzelner Routen Wert bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-207">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="5ca1b-208">Wenn die Info-Seite mit einem Routen Parameter `/About/RouteDataValue`Wert unter angefordert wird, wird "routedatavalue" in `RouteData.Values["globalTemplate"]` (`Order = 1`) und nicht `RouteData.Values["aboutTemplate"]` (`Order = 2`) geladen, weil die `Order` -Eigenschaft festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-208">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="5ca1b-209">Legen Sie, sofern möglich, `Order`nicht den fest, `Order = 0`der ergibt.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-209">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="5ca1b-210">Verlassen Sie sich auf das Routing, um die richtige Route auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-210">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="5ca1b-211">Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About/GlobalRouteValue/AboutRouteValue` an, und prüfen Sie das Ergebnis:</span><span class="sxs-lookup"><span data-stu-id="5ca1b-211">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![Die Seite „Info“ wird mit Routensegmenten für GlobalRouteValue und AboutRouteValue angefordert.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

::: moniker range=">= aspnetcore-2.2"

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="5ca1b-214">Verwenden eines parametertransformer zum Anpassen von Seiten Routen</span><span class="sxs-lookup"><span data-stu-id="5ca1b-214">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="5ca1b-215">Die von ASP.net Core generierten Seiten Routen können mit einem Parameter Transformator angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-215">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="5ca1b-216">Ein Parametertransformator implementiert `IOutboundParameterTransformer` und wandelt den Wert der Parameter um.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-216">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="5ca1b-217">Beispielsweise ändert ein benutzerdefinierter `SlugifyParameterTransformer`-Parametertransformator den Routenwert `SubscriptionManagement` in `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-217">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="5ca1b-218">Die `PageRouteTransformerConvention` Page Route-Modell Konvention wendet einen Parameter Transformator auf die Ordner-und Dateinamen Segmente von automatisch generierten Seiten Routen in einer APP an.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-218">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="5ca1b-219">Beispielsweise wird in der Razor Pages-Datei unter */pages/SubscriptionManagement/ViewAll.cshtml* von `/SubscriptionManagement/ViewAll` in `/subscription-management/view-all`die Route umgeschrieben.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-219">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="5ca1b-220">`PageRouteTransformerConvention`transformiert nur die automatisch generierten Segmente einer Seiten Route, die aus dem Razor Pages Ordner und dem Dateinamen stammen.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-220">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="5ca1b-221">Mit der `@page` -Direktive hinzugefügte Routen Segmente werden nicht transformiert.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-221">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="5ca1b-222">Außerdem werden von hinzugefügte Routen von <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>der Konvention nicht transformiert.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-222">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="5ca1b-223">Die `PageRouteTransformerConvention` ist als Option in `Startup.ConfigureServices`registriert:</span><span class="sxs-lookup"><span data-stu-id="5ca1b-223">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

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

## <a name="configure-a-page-route"></a><span data-ttu-id="5ca1b-224">Konfigurieren einer Seitenroute</span><span class="sxs-lookup"><span data-stu-id="5ca1b-224">Configure a page route</span></span>

<span data-ttu-id="5ca1b-225">Verwenden <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> Sie, um eine Route zu einer Seite am angegebenen Seiten Pfad zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-225">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="5ca1b-226">Generierte Links, die auf die Seite verweisen, verwenden die von Ihnen angegebene Route.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-226">Generated links to the page use your specified route.</span></span> <span data-ttu-id="5ca1b-227">`AddPageRoute` verwendet die `AddPageRouteModelConvention` zum Aufbauen der Route.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-227">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="5ca1b-228">Die Beispielanwendung erstellt für *Contact.cshtml* eine Route zur Seite `/TheContactPage`:</span><span class="sxs-lookup"><span data-stu-id="5ca1b-228">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

::: moniker-end

<span data-ttu-id="5ca1b-229">Die Seite „Kontakt“ kann auch unter `/Contact` über ihre Standardroute erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-229">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="5ca1b-230">Die benutzerdefinierte Route der Beispielanwendung, die zur Seite „Kontakt“ führt, ermöglicht das Verwenden eines optionalen `text`-Routensegments (`{text?}`).</span><span class="sxs-lookup"><span data-stu-id="5ca1b-230">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="5ca1b-231">Die Seite enthält dieses optionale Segment auch in ihrer `@page`-Anweisung, für den Fall, dass der Besucher über die `/Contact`-Route auf die Seite zugreift:</span><span class="sxs-lookup"><span data-stu-id="5ca1b-231">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

::: moniker-end

<span data-ttu-id="5ca1b-232">Beachten Sie, dass die URL, die für den Link **Kontakt** in der gerenderten Seite generiert wurde, die aktualisierte Route widerspiegelt:</span><span class="sxs-lookup"><span data-stu-id="5ca1b-232">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Kontaktlink der Beispielanwendung in der Navigationsleiste](razor-pages-conventions/_static/contact-link.png)

![Beim Überprüfen des Kontaktlinks in der gerenderten HTML zeigt sich, dass das HREF-Attribut auf „/TheContactPage“ festgelegt wurde.](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="5ca1b-235">Sie können die Kontaktseite entweder über deren übliche Route, `/Contact`, oder über die benutzerdefinierte Route, `/TheContactPage`, besuchen.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-235">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="5ca1b-236">Wenn Sie ein zusätzliches `text`-Routensegment bereitstellen, wird dieses HTML-codierte Segment auf der Seite angezeigt:</span><span class="sxs-lookup"><span data-stu-id="5ca1b-236">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![Beispiel für das Bereitstellen eines optionalen „Text“-Routensegments mit „TextValue“ in der URL in der Ansicht des Microsoft Edge-Browsers](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="5ca1b-239">Konventionen für Seitenmodellaktionen</span><span class="sxs-lookup"><span data-stu-id="5ca1b-239">Page model action conventions</span></span>

<span data-ttu-id="5ca1b-240">Der Standardanbieter für Seiten Modelle, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> der implementiert, ruft Konventionen auf, die entwickelt wurden, um Erweiterungs Punkte zum Konfigurieren von Seiten Modellen bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-240">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="5ca1b-241">Diese Konventionen sind beim Erstellen und Ändern von Seitenermittlungs- und Verarbeitungsszenarios hilfreich.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-241">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="5ca1b-242">In den Beispielen in diesem Abschnitt verwendet `AddHeaderAttribute` die Beispiel-App eine-Klasse, bei der <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>es sich um einen handelt, der einen Antwortheader anwendet:</span><span class="sxs-lookup"><span data-stu-id="5ca1b-242">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="5ca1b-243">Das Beispiel veranschaulicht mithilfe von Konventionen, wie das Attribut auf alle Seiten in einem Ordner und auf eine Einzelseite angewendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-243">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="5ca1b-244">**Ordner-App-Modellkonvention**</span><span class="sxs-lookup"><span data-stu-id="5ca1b-244">**Folder app model convention**</span></span>

<span data-ttu-id="5ca1b-245">Verwenden <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> Sie, um einen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> zu erstellen und hinzuzufügen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> , der eine Aktion für-Instanzen für alle Seiten unter dem angegebenen Ordner aufruft.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-245">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="5ca1b-246">Im Beispiel wird die Verwendung der `AddFolderApplicationModelConvention` durch Hinzufügen eines Headers, `OtherPagesHeader`, zu den Seiten im Ordner *OtherPages* der Anwendung veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="5ca1b-246">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

::: moniker-end

<span data-ttu-id="5ca1b-247">Fordern Sie die Seite „Seite1“ der Beispielanwendung unter `localhost:5000/OtherPages/Page1` an, und prüfen Sie die Header, um das Ergebnis zu sehen:</span><span class="sxs-lookup"><span data-stu-id="5ca1b-247">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Antwortheader der Seite „OtherPages/Seite1“ zeigt, dass der OtherPagesHeader hinzugefügt wurde.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="5ca1b-249">**Seiten-App-Modellkonvention**</span><span class="sxs-lookup"><span data-stu-id="5ca1b-249">**Page app model convention**</span></span>

<span data-ttu-id="5ca1b-250">Verwenden <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> Sie, um eine <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> zu erstellen und hinzuzufügen, die <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> eine Aktion für die für die Seite mit dem angegebenen Namen aufruft.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-250">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="5ca1b-251">Im Beispiel wird das Verwenden von `AddPageApplicationModelConvention` durch Hinzufügen eines Headers, `AboutHeader`, auf der Seite „Info“ veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="5ca1b-251">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

::: moniker-end

<span data-ttu-id="5ca1b-252">Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About` an, und überprüfen Sie die Header, um das Ergebnis zu sehen:</span><span class="sxs-lookup"><span data-stu-id="5ca1b-252">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Antwortheader der Seite „Info“ zeigen, dass AboutHeader hinzugefügt wurde.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="5ca1b-254">**Konfigurieren eines Filters**</span><span class="sxs-lookup"><span data-stu-id="5ca1b-254">**Configure a filter**</span></span>

<span data-ttu-id="5ca1b-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>konfiguriert den angegebenen Filter, der angewendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="5ca1b-256">Sie können Filterklassen selbst implementieren. In der Beispielanwendung wird jedoch gezeigt, wie Sie einen Filter in einen Lambdaausdruck implementieren. Dieser wird dann im Hintergrund als Zuordnungsinstanz implementiert, die einen Filter zurückgibt:</span><span class="sxs-lookup"><span data-stu-id="5ca1b-256">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

::: moniker-end

<span data-ttu-id="5ca1b-257">Das Seiten-App-Modell wird verwendet, um den relativen Pfad für Segmente zu überprüfen, die zur Seite „Seite2“ im Ordner *OtherPages* führen.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-257">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="5ca1b-258">Wenn die Bedingung erfüllt ist, wird ein Header hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-258">If the condition passes, a header is added.</span></span> <span data-ttu-id="5ca1b-259">Wenn dies nicht der Fall ist, wird der `EmptyFilter` angewendet.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-259">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="5ca1b-260">`EmptyFilter` ist ein [Aktionsfilter](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="5ca1b-260">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="5ca1b-261">Da Aktionsfilter von Razor Pages ignoriert werden, wirkt `EmptyFilter` sich das nicht wie vorgesehen aus, wenn der Pfad `OtherPages/Page2`nicht enthält.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-261">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="5ca1b-262">Fordern Sie die Seite „Seite2“ der Beispielanwendung unter `localhost:5000/OtherPages/Page2` an, und prüfen Sie die Header, um das Ergebnis zu sehen:</span><span class="sxs-lookup"><span data-stu-id="5ca1b-262">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![Der Header „OtherPagesPage2Header“ wird zur Antwort für „Seite2“ hinzugefügt.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="5ca1b-264">**Konfigurieren einer Filterzuordnungsinstanz**</span><span class="sxs-lookup"><span data-stu-id="5ca1b-264">**Configure a filter factory**</span></span>

<span data-ttu-id="5ca1b-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>konfiguriert die angegebene Factory so, dass [Filter](xref:mvc/controllers/filters) auf alle Razor Pages angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="5ca1b-266">Die Beispielanwendung bietet Ihnen die Möglichkeit des beispielhaften Verwendens einer [Filterzuordnungsinstanz](xref:mvc/controllers/filters#ifilterfactory) durch Hinzufügen des Headers `FilterFactoryHeader` zu den Seiten der Anwendung, der zwei Werte besitzt:</span><span class="sxs-lookup"><span data-stu-id="5ca1b-266">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

::: moniker-end

<span data-ttu-id="5ca1b-267">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="5ca1b-267">*AddHeaderWithFactory.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="5ca1b-268">Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About` an, und überprüfen Sie die Header, um das Ergebnis zu sehen:</span><span class="sxs-lookup"><span data-stu-id="5ca1b-268">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Die Antwortheader der Seite „Info“ zeigen, dass zwei Header des Typs „FilterFactoryHeader“ hinzugefügt wurden.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="5ca1b-270">Die MVC-Filter und der Seitenfilter (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="5ca1b-270">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="5ca1b-271">MVC-[Aktionsfilter](xref:mvc/controllers/filters#action-filters) werden von Razor Pages ignoriert, da diese Handlermethoden verwenden.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-271">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="5ca1b-272">Andere Typen von MVC-Filtern stehen zur Verwendung zur Verfügung: [Autorisierung](xref:mvc/controllers/filters#authorization-filters), [Ausnahme](xref:mvc/controllers/filters#exception-filters), [Ressource](xref:mvc/controllers/filters#resource-filters)und [Ergebnis](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="5ca1b-272">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="5ca1b-273">Weitere Informationen finden Sie im Thema [Filter](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="5ca1b-273">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="5ca1b-274">Der Seiten Filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) ist ein Filter, der für Razor Pages gilt.</span><span class="sxs-lookup"><span data-stu-id="5ca1b-274">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="5ca1b-275">Weitere Informationen finden Sie unter [Filtermethoden für Razor-Seiten](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="5ca1b-275">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5ca1b-276">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5ca1b-276">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

---
title: Bereiche in ASP.NET Core
author: rick-anderson
description: Erfahren Sie mehr über Bereiche, ein Feature von ASP.NET MVC, das für die Organisation von verwandten Funktionalitäten in einer Gruppe als separater Namespace (für Routing) und Ordnerstruktur (für Ansichten) verwendet wird.
ms.author: riande
ms.date: 12/05/2019
uid: mvc/controllers/areas
ms.openlocfilehash: 1066f4ce104e507abe63302fd3523a3a7a8dfde9
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828242"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="43b22-103">Bereiche in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="43b22-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="43b22-104">Von [Dhananjay Kumar](https://twitter.com/debug_mode) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="43b22-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="43b22-105">Bereiche sind ein Feature von ASP.NET, das für die Organisation von verwandten Funktionalitäten in eine Gruppe als separater Namespace (für Routing) und Ordnerstruktur (für Ansichten) verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="43b22-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="43b22-106">Mithilfe von Bereichen wird außerdem eine Hierarchie erstellt, damit das Routing durch Hinzufügen eines anderen Routenparameters ausgeführt werden kann: `area` zu `controller` und `action` oder einer Razor Page `page`.</span><span class="sxs-lookup"><span data-stu-id="43b22-106">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="43b22-107">Bereiche ermöglichen es, eine ASP.NET Core-Web-App in kleinere funktionale Gruppen zu partitionieren. Jede dieser Gruppen hat dabei ihre eigene Menge an Razor Pages, Controller, Ansichten und Modellen.</span><span class="sxs-lookup"><span data-stu-id="43b22-107">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="43b22-108">Ein Bereich ist im Grunde genommen eine Struktur in einer App.</span><span class="sxs-lookup"><span data-stu-id="43b22-108">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="43b22-109">In einem ASP.NET Core-Webprojekt werden logische Komponenten wie Seiten, Modelle, Controller und Ansichten in verschiedenen Ordner aufbewahrt.</span><span class="sxs-lookup"><span data-stu-id="43b22-109">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="43b22-110">Die ASP.NET Core-Runtime verwendet Namenskonventionen, um die Beziehung zwischen diesen Komponenten zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="43b22-110">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="43b22-111">Bei einer großen App kann es von Vorteil sein, die App in mehrere Bereiche mit hoher Funktionalität aufzuteilen.</span><span class="sxs-lookup"><span data-stu-id="43b22-111">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="43b22-112">Dies gilt z.B. für eine E-Commerce-App mit mehreren Geschäftseinheiten, wie Auftragsabschluss, Abrechnung und Suche.</span><span class="sxs-lookup"><span data-stu-id="43b22-112">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="43b22-113">Jede dieser Einheiten hat ihre eigenen Bereiche, die Ansichten, Controller, Razor Pages und Modelle enthalten.</span><span class="sxs-lookup"><span data-stu-id="43b22-113">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="43b22-114">Die Verwendung von Bereichen in einem Projekt ist erwägenswert, wenn:</span><span class="sxs-lookup"><span data-stu-id="43b22-114">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="43b22-115">Ihre App aus mehreren funktionalen Komponenten auf hoher Ebene besteht, die logisch getrennt sein können.</span><span class="sxs-lookup"><span data-stu-id="43b22-115">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="43b22-116">Sie Ihre App partitionieren möchten, damit jeder funktionale Bereich unabhängig voneinander bearbeitet werden kann.</span><span class="sxs-lookup"><span data-stu-id="43b22-116">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="43b22-117">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="43b22-117">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="43b22-118">Das Downloadbeispiel stellt eine einfache App für Testbereiche zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="43b22-118">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="43b22-119">Wenn Sie Razor-Seiten verwenden, lesen Sie [Bereiche mit Razor-Seiten](#areas-with-razor-pages) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="43b22-119">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="43b22-120">Bereiche für Controller mit Ansichten</span><span class="sxs-lookup"><span data-stu-id="43b22-120">Areas for controllers with views</span></span>

<span data-ttu-id="43b22-121">Eine typische ASP.NET Core-Web-App, die Bereiche, Controller und Ansichten verwendet, beinhaltet Folgendes:</span><span class="sxs-lookup"><span data-stu-id="43b22-121">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="43b22-122">Eine [Bereichsordnerstruktur](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="43b22-122">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="43b22-123">Controller werden mit dem Attribut [`[Area]`](#attribute) versehen, um den Controller mit dem Bereich zu verknüpfen:</span><span class="sxs-lookup"><span data-stu-id="43b22-123">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="43b22-124">Die [Bereichsroute, die dem Startup hinzugefügt wurde](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="43b22-124">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="43b22-125">Bereichsordnerstruktur</span><span class="sxs-lookup"><span data-stu-id="43b22-125">Area folder structure</span></span>

<span data-ttu-id="43b22-126">Stellen Sie sich eine App vor, die zwei logische Gruppen hat, *Produkte* und *Dienste*.</span><span class="sxs-lookup"><span data-stu-id="43b22-126">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="43b22-127">Wenn Bereiche verwendet werden, würde die Ordnerstruktur ähnlich dem Folgenden aussehen:</span><span class="sxs-lookup"><span data-stu-id="43b22-127">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="43b22-128">Projektname</span><span class="sxs-lookup"><span data-stu-id="43b22-128">Project name</span></span>
  * <span data-ttu-id="43b22-129">Bereiche</span><span class="sxs-lookup"><span data-stu-id="43b22-129">Areas</span></span>
    * <span data-ttu-id="43b22-130">Products</span><span class="sxs-lookup"><span data-stu-id="43b22-130">Products</span></span>
      * <span data-ttu-id="43b22-131">Controller</span><span class="sxs-lookup"><span data-stu-id="43b22-131">Controllers</span></span>
        * <span data-ttu-id="43b22-132">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="43b22-132">HomeController.cs</span></span>
        * <span data-ttu-id="43b22-133">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="43b22-133">ManageController.cs</span></span>
      * <span data-ttu-id="43b22-134">Ansichten</span><span class="sxs-lookup"><span data-stu-id="43b22-134">Views</span></span>
        * <span data-ttu-id="43b22-135">Startseite</span><span class="sxs-lookup"><span data-stu-id="43b22-135">Home</span></span>
          * <span data-ttu-id="43b22-136">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="43b22-136">Index.cshtml</span></span>
        * <span data-ttu-id="43b22-137">Verwalten</span><span class="sxs-lookup"><span data-stu-id="43b22-137">Manage</span></span>
          * <span data-ttu-id="43b22-138">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="43b22-138">Index.cshtml</span></span>
          * <span data-ttu-id="43b22-139">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="43b22-139">About.cshtml</span></span>
    * <span data-ttu-id="43b22-140">Dienste</span><span class="sxs-lookup"><span data-stu-id="43b22-140">Services</span></span>
      * <span data-ttu-id="43b22-141">Controller</span><span class="sxs-lookup"><span data-stu-id="43b22-141">Controllers</span></span>
        * <span data-ttu-id="43b22-142">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="43b22-142">HomeController.cs</span></span>
      * <span data-ttu-id="43b22-143">Ansichten</span><span class="sxs-lookup"><span data-stu-id="43b22-143">Views</span></span>
        * <span data-ttu-id="43b22-144">Startseite</span><span class="sxs-lookup"><span data-stu-id="43b22-144">Home</span></span>
          * <span data-ttu-id="43b22-145">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="43b22-145">Index.cshtml</span></span>

<span data-ttu-id="43b22-146">Während das vorherige Layout typisch ist, wenn Bereiche verwendet werden, müssen nur die Ansichtsdateien diese Ordnerstruktur verwenden.</span><span class="sxs-lookup"><span data-stu-id="43b22-146">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="43b22-147">Die Ansichtsermittlung sucht nach einer passenden Bereichsansichtsdatei im folgenden Ordner:</span><span class="sxs-lookup"><span data-stu-id="43b22-147">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="43b22-148">Zuordnen eines Controllers zu einem Bereich</span><span class="sxs-lookup"><span data-stu-id="43b22-148">Associate the controller with an Area</span></span>

<span data-ttu-id="43b22-149">Bereichscontroller werden mit dem Attribut [&lbrack;Bereich&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) versehen:</span><span class="sxs-lookup"><span data-stu-id="43b22-149">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="43b22-150">Hinzufügen einer Bereichsroute</span><span class="sxs-lookup"><span data-stu-id="43b22-150">Add Area route</span></span>

<span data-ttu-id="43b22-151">Bereichsrouten verwenden normalerweise konventionelles Routing anstatt Attributrouting.</span><span class="sxs-lookup"><span data-stu-id="43b22-151">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="43b22-152">Beim herkömmlichen Routing ist die Reihenfolge wichtig.</span><span class="sxs-lookup"><span data-stu-id="43b22-152">Conventional routing is order-dependent.</span></span> <span data-ttu-id="43b22-153">Routen mit Bereichen werden im Allgemeinen früher in der Routentabelle aufgeführt als die spezifischeren Routen ohne Bereich.</span><span class="sxs-lookup"><span data-stu-id="43b22-153">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="43b22-154">`{area:...}` kann als Token in Routenvorlagen verwendet werden, wenn der URL-Raum in allen Bereichen einheitlich ist:</span><span class="sxs-lookup"><span data-stu-id="43b22-154">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="43b22-155">Im vorherigen Code wendet `exists` eine Einschränkung an: Die Route muss mit einem Bereich übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="43b22-155">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="43b22-156">`{area:...}` zu verwenden ist die unkomplizierteste Methode, um Bereichen Routing hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="43b22-156">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="43b22-157">Im folgenden Code wird <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> verwendet, um zwei benannte Bereichsrouten zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="43b22-157">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="43b22-158">Wenn `MapAreaRoute` mit ASP.NET Core 2.2 verwendet werden soll, sehen Sie sich diesen [GitHub-Artikel](https://github.com/dotnet/AspNetCore/issues/7772) an.</span><span class="sxs-lookup"><span data-stu-id="43b22-158">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="43b22-159">Weitere Informationen finden Sie im Artikel [Routing zu Controlleraktionen in ASP.NET Core](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="43b22-159">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="43b22-160">Erstellen von Links mit MVC-Bereichen</span><span class="sxs-lookup"><span data-stu-id="43b22-160">Link generation with MVC areas</span></span>

<span data-ttu-id="43b22-161">Im folgenden Code aus dem [Beispieldownload](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) sehen Sie die Erstellung eines Links mit dem angegebenen Bereich:</span><span class="sxs-lookup"><span data-stu-id="43b22-161">The following code from the [sample download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="43b22-162">Links, die mit dem vorherigen Code erstellt wurden, gelten überall in der App.</span><span class="sxs-lookup"><span data-stu-id="43b22-162">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="43b22-163">Im Beispieldownload ist eine [partielle Ansicht](xref:mvc/views/partial) enthalten, die die vorherigen Links und dieselben Links beinhaltet, ohne dass der Bereich angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="43b22-163">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="43b22-164">In der [Layoutdatei](xref:mvc/views/layout) wird auf die partielle Ansicht verwiesen. Jede Seite in der App stellt also die erstellen Links dar.</span><span class="sxs-lookup"><span data-stu-id="43b22-164">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="43b22-165">Die Links, die ohne Angabe eines Bereichs erstellt werden, sind nur gültig, wenn auf sie von einer Seite im selben Bereich und Controller verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="43b22-165">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="43b22-166">Wenn der Bereich oder der Kontroller nicht angegeben werden, hängt das Routing von den *Umgebungswerten* ab.</span><span class="sxs-lookup"><span data-stu-id="43b22-166">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="43b22-167">Die aktuellen Routenwerte der aktuellen Anforderung werden bei der Linkgenerierung als Umgebungswerte behandelt.</span><span class="sxs-lookup"><span data-stu-id="43b22-167">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="43b22-168">Oft werden ungültige Links erstellt, wenn für die Beispiel-App die Umgebungswerte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="43b22-168">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="43b22-169">Weitere Informationen finden Sie unter [Routing zu Controlleraktionen in ASP.NET Core](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="43b22-169">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="43b22-170">Freigegebenes Layout für Bereiche unter Verwendung der _ViewStart.cshtml-Datei</span><span class="sxs-lookup"><span data-stu-id="43b22-170">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="43b22-171">Um ein gemeinsames Layout für die gesamte App freizugeben, verschieben Sie *_ViewStart.cshtml* in den Stammordner der Anwendung.</span><span class="sxs-lookup"><span data-stu-id="43b22-171">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="43b22-172">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="43b22-172">_ViewImports.cshtml</span></span>

<span data-ttu-id="43b22-173">An ihrem Standardspeicherort gilt die Datei */Views/_ViewImports.cshtml* nicht für Bereiche.</span><span class="sxs-lookup"><span data-stu-id="43b22-173">In its standard location, */Views/_ViewImports.cshtml* doesn't apply to areas.</span></span> <span data-ttu-id="43b22-174">Um allgemeine [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro), `@using` oder `@inject` in Ihrem Bereich zu verwenden, stellen Sie sicher, dass eine ordnungsgemäße Datei *_ViewImports.cshtml*[für Ihre Bereichsansichten gilt](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="43b22-174">To use common [Tag Helpers](xref:mvc/views/tag-helpers/intro), `@using`, or `@inject` in your area, ensure a proper *_ViewImports.cshtml* file [applies to your area views](xref:mvc/views/layout#importing-shared-directives).</span></span> <span data-ttu-id="43b22-175">Wenn Sie das gleiche Verhalten in allen Ansichten wünschen, verschieben Sie */Views/_ViewImports.cshtml* in den Anwendungsstamm.</span><span class="sxs-lookup"><span data-stu-id="43b22-175">If you want the same behavior in all your views, move */Views/_ViewImports.cshtml* to the application root.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="43b22-176">Ändern des Standardbereichsordners, in dem Ansichten gespeichert sind</span><span class="sxs-lookup"><span data-stu-id="43b22-176">Change default area folder where views are stored</span></span>

<span data-ttu-id="43b22-177">Der folgende Code ändert den Standardbereichsordner von `"Areas"` in `"MyAreas"`:</span><span class="sxs-lookup"><span data-stu-id="43b22-177">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="43b22-178">Bereiche mit Razor-Seiten</span><span class="sxs-lookup"><span data-stu-id="43b22-178">Areas with Razor Pages</span></span>

<span data-ttu-id="43b22-179">Bereiche mit Razor-Seiten erfordern einen Ordner *Areas/<area name>/Pages* im Stammverzeichnis der App.</span><span class="sxs-lookup"><span data-stu-id="43b22-179">Areas with Razor Pages require an *Areas/<area name>/Pages* folder in the root of the app.</span></span> <span data-ttu-id="43b22-180">Die folgende Ordnerstruktur wird mit dem [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) verwendet.</span><span class="sxs-lookup"><span data-stu-id="43b22-180">The following folder structure is used with the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span></span>

* <span data-ttu-id="43b22-181">Projektname</span><span class="sxs-lookup"><span data-stu-id="43b22-181">Project name</span></span>
  * <span data-ttu-id="43b22-182">Bereiche</span><span class="sxs-lookup"><span data-stu-id="43b22-182">Areas</span></span>
    * <span data-ttu-id="43b22-183">Products</span><span class="sxs-lookup"><span data-stu-id="43b22-183">Products</span></span>
      * <span data-ttu-id="43b22-184">Seiten</span><span class="sxs-lookup"><span data-stu-id="43b22-184">Pages</span></span>
        * <span data-ttu-id="43b22-185">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="43b22-185">_ViewImports</span></span>
        * <span data-ttu-id="43b22-186">Info</span><span class="sxs-lookup"><span data-stu-id="43b22-186">About</span></span>
        * <span data-ttu-id="43b22-187">Index</span><span class="sxs-lookup"><span data-stu-id="43b22-187">Index</span></span>
    * <span data-ttu-id="43b22-188">Dienste</span><span class="sxs-lookup"><span data-stu-id="43b22-188">Services</span></span>
      * <span data-ttu-id="43b22-189">Seiten</span><span class="sxs-lookup"><span data-stu-id="43b22-189">Pages</span></span>
        * <span data-ttu-id="43b22-190">Verwalten</span><span class="sxs-lookup"><span data-stu-id="43b22-190">Manage</span></span>
          * <span data-ttu-id="43b22-191">Info</span><span class="sxs-lookup"><span data-stu-id="43b22-191">About</span></span>
          * <span data-ttu-id="43b22-192">Index</span><span class="sxs-lookup"><span data-stu-id="43b22-192">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="43b22-193">Erstellen von Links mit Razor-Seiten und Bereichen</span><span class="sxs-lookup"><span data-stu-id="43b22-193">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="43b22-194">Im folgenden Code aus dem [Beispieldownload](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) sehen Sie die Erstellung eines Links mit dem angegebenen Bereich (z.B. `asp-area="Products"`):</span><span class="sxs-lookup"><span data-stu-id="43b22-194">The following code from the [sample download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="43b22-195">Links, die mit dem vorherigen Code erstellt wurden, gelten überall in der App.</span><span class="sxs-lookup"><span data-stu-id="43b22-195">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="43b22-196">Im Beispieldownload ist eine [partielle Ansicht](xref:mvc/views/partial) enthalten, die die vorherigen Links und dieselben Links beinhaltet, ohne dass der Bereich angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="43b22-196">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="43b22-197">In der [Layoutdatei](xref:mvc/views/layout) wird auf die partielle Ansicht verwiesen. Jede Seite in der App stellt also die erstellen Links dar.</span><span class="sxs-lookup"><span data-stu-id="43b22-197">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="43b22-198">Die Links, die ohne Angabe eines Bereichs erstellt werden, sind nur gültig, wenn auf sie von einer Seite im selben Bereich verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="43b22-198">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="43b22-199">Wenn der Bereich nicht angegeben wird, hängt das Routing von den *Umgebungswerten* ab.</span><span class="sxs-lookup"><span data-stu-id="43b22-199">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="43b22-200">Die aktuellen Routenwerte der aktuellen Anforderung werden bei der Linkgenerierung als Umgebungswerte behandelt.</span><span class="sxs-lookup"><span data-stu-id="43b22-200">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="43b22-201">Oft werden ungültige Links erstellt, wenn für die Beispiel-App die Umgebungswerte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="43b22-201">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="43b22-202">Betrachten Sie hierzu die aus dem folgenden Code generierten Links:</span><span class="sxs-lookup"><span data-stu-id="43b22-202">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="43b22-203">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="43b22-203">For the preceding code:</span></span>

* <span data-ttu-id="43b22-204">Der aus `<a asp-page="/Manage/About">` generierte Link stimmt nur, wenn die letzte Anforderung für eine Seite im `Services`-Bereich erfolgte.</span><span class="sxs-lookup"><span data-stu-id="43b22-204">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="43b22-205">Beispielsweise `/Services/Manage/`, `/Services/Manage/Index` oder `/Services/Manage/About`.</span><span class="sxs-lookup"><span data-stu-id="43b22-205">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="43b22-206">Der aus `<a asp-page="/About">` generierte Link stimmt nur, wenn die letzte Anforderung für eine Seite in `/Home` erfolgte.</span><span class="sxs-lookup"><span data-stu-id="43b22-206">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="43b22-207">Der Code stammt aus dem [Beispieldownload](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="43b22-207">The code is from the [sample download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="43b22-208">Importieren von Namespace und Taghilfsprogrammen mit der _ViewImports-Datei</span><span class="sxs-lookup"><span data-stu-id="43b22-208">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="43b22-209">Eine *_ViewImports.cshtml*-Datei kann dem *Pages*-Ordner jedes Bereichs hinzugefügt werden, um Namespace und Taghilfsprogramme für jede Razor-Seite im Ordner zu importieren.</span><span class="sxs-lookup"><span data-stu-id="43b22-209">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="43b22-210">Betrachten Sie den Bereich *Services* des Beispielcodes, der keine *_ViewImports.cshtml*-Datei enthält.</span><span class="sxs-lookup"><span data-stu-id="43b22-210">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="43b22-211">Das folgende Markup zeigt die Razor-Seite */Services/Manage/About*:</span><span class="sxs-lookup"><span data-stu-id="43b22-211">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="43b22-212">Im obenstehenden Markup:</span><span class="sxs-lookup"><span data-stu-id="43b22-212">In the preceding markup:</span></span>

* <span data-ttu-id="43b22-213">Der vollqualifizierte Domänenname muss verwendet werden, um das Modell anzugeben (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span><span class="sxs-lookup"><span data-stu-id="43b22-213">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="43b22-214">[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) werden durch `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` aktiviert.</span><span class="sxs-lookup"><span data-stu-id="43b22-214">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="43b22-215">Im Beispieldownload enthält der Bereich „Products“ die folgende *_ViewImports.cshtml*-Datei:</span><span class="sxs-lookup"><span data-stu-id="43b22-215">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="43b22-216">Das folgende Markup zeigt die Razor-Seite */Products/About*:</span><span class="sxs-lookup"><span data-stu-id="43b22-216">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="43b22-217">In der vorherigen Datei werden Namespace und `@addTagHelper`-Anweisung von der Datei *Areas/Products/Pages/_ViewImports.cshtml* in die Datei importiert.</span><span class="sxs-lookup"><span data-stu-id="43b22-217">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="43b22-218">Weitere Informationen finden Sie unter [Verwalten des Taghilfsprogrammbereichs](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) und [Importieren gemeinsam verwendeter Anweisungen](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="43b22-218">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="43b22-219">Gemeinsam genutztes Layout für Razor-Seiten-Bereiche</span><span class="sxs-lookup"><span data-stu-id="43b22-219">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="43b22-220">Um ein gemeinsames Layout für die gesamte App freizugeben, verschieben Sie *_ViewStart.cshtml* in den Stammordner der Anwendung.</span><span class="sxs-lookup"><span data-stu-id="43b22-220">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="43b22-221">Veröffentlichen von Bereichen</span><span class="sxs-lookup"><span data-stu-id="43b22-221">Publishing Areas</span></span>

<span data-ttu-id="43b22-222">Alle \*.cshtml-Dateien und Dateien im *wwwroot*-Verzeichnis werden in der Ausgabe veröffentlicht, wenn `<Project Sdk="Microsoft.NET.Sdk.Web">` in der \*.csproj-Datei enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="43b22-222">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>

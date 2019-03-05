---
title: Bereiche in ASP.NET Core
author: rick-anderson
description: Erfahren Sie mehr über Bereiche, ein Feature von ASP.NET MVC, das für die Organisation von verwandten Funktionalitäten in einer Gruppe als separater Namespace (für Routing) und Ordnerstruktur (für Ansichten) verwendet wird.
ms.author: riande
ms.date: 02/14/2019
uid: mvc/controllers/areas
ms.openlocfilehash: c21eed04ea68512515da262b6b6895dc1a821039
ms.sourcegitcommit: 2c7ffe349eabdccf2ed748dd303ffd0ba6e1cfe3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56833526"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="4a51d-103">Bereiche in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4a51d-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="4a51d-104">Von [Dhananjay Kumar](https://twitter.com/debug_mode) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4a51d-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4a51d-105">Bereiche sind ein Feature von ASP.NET, das für die Organisation von verwandten Funktionalitäten in eine Gruppe als separater Namespace (für Routing) und Ordnerstruktur (für Ansichten) verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="4a51d-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="4a51d-106">Mithilfe von Bereichen wird außerdem eine Hierarchie erstellt, damit das Routing durch Hinzufügen eines anderen Routenparameters ausgeführt werden kann: `area` zu `controller` und `action` oder einer Razor Page `page`.</span><span class="sxs-lookup"><span data-stu-id="4a51d-106">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="4a51d-107">Bereiche ermöglichen es, eine ASP.NET Core-Web-App in kleinere funktionale Gruppen zu partitionieren. Jede dieser Gruppen hat dabei ihre eigene Menge an Razor Pages, Controller, Ansichten und Modellen.</span><span class="sxs-lookup"><span data-stu-id="4a51d-107">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="4a51d-108">Ein Bereich ist im Grunde genommen eine Struktur in einer App.</span><span class="sxs-lookup"><span data-stu-id="4a51d-108">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="4a51d-109">In einem ASP.NET Core-Webprojekt werden logische Komponenten wie Seiten, Modelle, Controller und Ansichten in verschiedenen Ordner aufbewahrt.</span><span class="sxs-lookup"><span data-stu-id="4a51d-109">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="4a51d-110">Die ASP.NET Core-Runtime verwendet Namenskonventionen, um die Beziehung zwischen diesen Komponenten zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="4a51d-110">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="4a51d-111">Bei einer großen App kann es von Vorteil sein, die App in mehrere Bereiche mit hoher Funktionalität aufzuteilen.</span><span class="sxs-lookup"><span data-stu-id="4a51d-111">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="4a51d-112">Dies gilt z.B. für eine E-Commerce-App mit mehreren Geschäftseinheiten, wie Auftragsabschluss, Abrechnung und Suche.</span><span class="sxs-lookup"><span data-stu-id="4a51d-112">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="4a51d-113">Jede dieser Einheiten hat ihre eigenen Bereiche, die Ansichten, Controller, Razor Pages und Modelle enthalten.</span><span class="sxs-lookup"><span data-stu-id="4a51d-113">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="4a51d-114">Die Verwendung von Bereichen in einem Projekt ist erwägenswert, wenn:</span><span class="sxs-lookup"><span data-stu-id="4a51d-114">Consider using Areas in an project when:</span></span>

* <span data-ttu-id="4a51d-115">Ihre App aus mehreren funktionalen Komponenten auf hoher Ebene besteht, die logisch getrennt sein können.</span><span class="sxs-lookup"><span data-stu-id="4a51d-115">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="4a51d-116">Sie Ihre App partitionieren möchten, damit jeder funktionale Bereich unabhängig voneinander bearbeitet werden kann.</span><span class="sxs-lookup"><span data-stu-id="4a51d-116">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="4a51d-117">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="4a51d-117">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="4a51d-118">Das Downloadbeispiel stellt eine einfache App für Testbereiche zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="4a51d-118">The download sample provides a basic app for testing areas.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="4a51d-119">Bereiche für Controller mit Ansichten</span><span class="sxs-lookup"><span data-stu-id="4a51d-119">Areas for controllers with views</span></span>

<span data-ttu-id="4a51d-120">Eine typische ASP.NET Core-Web-App, die Bereiche, Controller und Ansichten verwendet, beinhaltet Folgendes:</span><span class="sxs-lookup"><span data-stu-id="4a51d-120">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="4a51d-121">Eine [Bereichsordnerstruktur](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="4a51d-121">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="4a51d-122">Controller, die mit dem Attribut [&lbrack;Bereich&rbrack;](#attribute) versehen wurden, um dem Controller den Bereich zuzuordnen: [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]</span><span class="sxs-lookup"><span data-stu-id="4a51d-122">Controllers decorated with the [&lbrack;Area&rbrack;](#attribute) attribute to associate the controller with the area: [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]</span></span>
* <span data-ttu-id="4a51d-123">Die [Bereichsroute, die dem Startup hinzugefügt wurde](#add-area-route): [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]</span><span class="sxs-lookup"><span data-stu-id="4a51d-123">The [area route added to startup](#add-area-route): [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]</span></span>

## <a name="area-folder-structure"></a><span data-ttu-id="4a51d-124">Bereichsordnerstruktur</span><span class="sxs-lookup"><span data-stu-id="4a51d-124">Area folder structure</span></span>
<span data-ttu-id="4a51d-125">Stellen Sie sich eine App vor, die zwei logische Gruppen hat, *Produkte* und *Dienste*.</span><span class="sxs-lookup"><span data-stu-id="4a51d-125">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="4a51d-126">Wenn Bereiche verwendet werden, würde die Ordnerstruktur ähnlich dem Folgenden aussehen:</span><span class="sxs-lookup"><span data-stu-id="4a51d-126">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="4a51d-127">Projektname</span><span class="sxs-lookup"><span data-stu-id="4a51d-127">Project name</span></span>
  * <span data-ttu-id="4a51d-128">Bereiche</span><span class="sxs-lookup"><span data-stu-id="4a51d-128">Areas</span></span>
    * <span data-ttu-id="4a51d-129">Products</span><span class="sxs-lookup"><span data-stu-id="4a51d-129">Products</span></span>
      * <span data-ttu-id="4a51d-130">Controller</span><span class="sxs-lookup"><span data-stu-id="4a51d-130">Controllers</span></span>
        * <span data-ttu-id="4a51d-131">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="4a51d-131">HomeController.cs</span></span>
        * <span data-ttu-id="4a51d-132">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="4a51d-132">ManageController.cs</span></span>
      * <span data-ttu-id="4a51d-133">Ansichten</span><span class="sxs-lookup"><span data-stu-id="4a51d-133">Views</span></span>
        * <span data-ttu-id="4a51d-134">Startseite</span><span class="sxs-lookup"><span data-stu-id="4a51d-134">Home</span></span>
          * <span data-ttu-id="4a51d-135">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="4a51d-135">Index.cshtml</span></span>
        * <span data-ttu-id="4a51d-136">Verwalten</span><span class="sxs-lookup"><span data-stu-id="4a51d-136">Manage</span></span>
          * <span data-ttu-id="4a51d-137">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="4a51d-137">Index.cshtml</span></span>
          * <span data-ttu-id="4a51d-138">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="4a51d-138">About.cshtml</span></span>
    * <span data-ttu-id="4a51d-139">Dienste</span><span class="sxs-lookup"><span data-stu-id="4a51d-139">Services</span></span>
      * <span data-ttu-id="4a51d-140">Controller</span><span class="sxs-lookup"><span data-stu-id="4a51d-140">Controllers</span></span>
        * <span data-ttu-id="4a51d-141">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="4a51d-141">HomeController.cs</span></span>
      * <span data-ttu-id="4a51d-142">Ansichten</span><span class="sxs-lookup"><span data-stu-id="4a51d-142">Views</span></span>
        * <span data-ttu-id="4a51d-143">Startseite</span><span class="sxs-lookup"><span data-stu-id="4a51d-143">Home</span></span>
          * <span data-ttu-id="4a51d-144">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="4a51d-144">Index.cshtml</span></span>

<span data-ttu-id="4a51d-145">Während das vorherige Layout typisch ist, wenn Bereiche verwendet werden, müssen nur die Ansichtsdateien diese Ordnerstruktur verwenden.</span><span class="sxs-lookup"><span data-stu-id="4a51d-145">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="4a51d-146">Die Ansichtsermittlung sucht nach einer passenden Bereichsansichtsdatei im folgenden Ordner:</span><span class="sxs-lookup"><span data-stu-id="4a51d-146">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
   ```

<span data-ttu-id="4a51d-147">Der Speicherort von Ordnern, die sich nicht auf die Ansicht beziehen, wie *Controller* und *Modelle* spielt **keine** Rolle.</span><span class="sxs-lookup"><span data-stu-id="4a51d-147">The location of non-view folders like *Controllers* and *Models* does **not** matter.</span></span> <span data-ttu-id="4a51d-148">Die Ordner *Controller* und *Modelle* werden z.B. nicht benötigt.</span><span class="sxs-lookup"><span data-stu-id="4a51d-148">For example, the *Controllers* and *Models* folder are not required.</span></span> <span data-ttu-id="4a51d-149">Der Inhalt von *Controller* und *Modelle* ist Code, der in eine .dll-Datei kompiliert wird.</span><span class="sxs-lookup"><span data-stu-id="4a51d-149">The content of *Controllers* and *Models* is code which gets compiled into a .dll.</span></span> <span data-ttu-id="4a51d-150">Der Inhalt von *Ansichten* wird nicht kompiliert, bis eine Anforderung an diese Ansicht gestellt wird.</span><span class="sxs-lookup"><span data-stu-id="4a51d-150">The content of the *Views* isn't compiled until a request to that view has been made.</span></span>

<!-- TODO review:
The content of the *Views* isn't compiled until a request to that view has been made.

What about precompiled views? 
 -->
<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="4a51d-151">Zuordnen eines Controllers zu einem Bereich</span><span class="sxs-lookup"><span data-stu-id="4a51d-151">Associate the controller with an Area</span></span>

<span data-ttu-id="4a51d-152">Bereichscontroller werden mit dem Attribut [&lbrack;Bereich&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) versehen:</span><span class="sxs-lookup"><span data-stu-id="4a51d-152">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="4a51d-153">Hinzufügen einer Bereichsroute</span><span class="sxs-lookup"><span data-stu-id="4a51d-153">Add Area route</span></span>

<span data-ttu-id="4a51d-154">Bereichsrouten verwenden normalerweise konventionelles Routing anstatt Attributrouting.</span><span class="sxs-lookup"><span data-stu-id="4a51d-154">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="4a51d-155">Beim herkömmlichen Routing ist die Reihenfolge wichtig.</span><span class="sxs-lookup"><span data-stu-id="4a51d-155">Conventional routing is order-dependent.</span></span> <span data-ttu-id="4a51d-156">Routen mit Bereichen werden im Allgemeinen früher in der Routentabelle aufgeführt als die spezifischeren Routen ohne Bereich.</span><span class="sxs-lookup"><span data-stu-id="4a51d-156">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="4a51d-157">`{area:...}` kann als Token in Routenvorlagen verwendet werden, wenn der URL-Raum in allen Bereichen einheitlich ist:</span><span class="sxs-lookup"><span data-stu-id="4a51d-157">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="4a51d-158">Im vorherigen Code wendet `exists` eine Einschränkung an: Die Route muss mit einem Bereich übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="4a51d-158">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="4a51d-159">`{area:...}` zu verwenden ist die unkomplizierteste Methode, um Bereichen Routing hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="4a51d-159">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="4a51d-160">Im folgenden Code wird <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> verwendet, um zwei benannte Bereichsrouten zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="4a51d-160">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="4a51d-161">Wenn `MapAreaRoute` mit ASP.NET Core 2.2 verwendet werden soll, sehen Sie sich diesen [GitHub-Artikel](https://github.com/aspnet/AspNetCore/issues/7772) an.</span><span class="sxs-lookup"><span data-stu-id="4a51d-161">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="4a51d-162">Weitere Informationen finden Sie im Artikel [Routing zu Controlleraktionen in ASP.NET Core](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="4a51d-162">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-areas"></a><span data-ttu-id="4a51d-163">Erstellen von Links mit Bereichen</span><span class="sxs-lookup"><span data-stu-id="4a51d-163">Link Generation with Areas</span></span>

<span data-ttu-id="4a51d-164">Im folgenden Code aus dem [Beispieldownload](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) sehen Sie die Erstellung eines Links mit dem angegebenen Bereich:</span><span class="sxs-lookup"><span data-stu-id="4a51d-164">The following code from the [sample download](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="4a51d-165">Links, die mit dem vorherigen Code erstellt wurden, gelten überall in der App.</span><span class="sxs-lookup"><span data-stu-id="4a51d-165">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="4a51d-166">Im Beispieldownload ist eine [partielle Ansicht](xref:mvc/views/partial) enthalten, die die vorherigen Links und dieselben Links beinhaltet, ohne dass der Bereich angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="4a51d-166">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="4a51d-167">In der [Layoutdatei]() wird auf die partielle Ansicht verwiesen. Jede Seite in der App stellt also die erstellen Links dar.</span><span class="sxs-lookup"><span data-stu-id="4a51d-167">The partial view is referenced in the [layout file](), so every page in the app displays the generated links.</span></span> <span data-ttu-id="4a51d-168">Die Links, die ohne Angabe eines Bereichs erstellt werden, sind nur gültig, wenn auf sie von einer Seite im selben Bereich und Controller verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="4a51d-168">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="4a51d-169">Wenn der Bereich oder der Kontroller nicht angegeben werden, hängt das Routing von den *Umgebungswerten* ab.</span><span class="sxs-lookup"><span data-stu-id="4a51d-169">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="4a51d-170">Die aktuellen Routenwerte der aktuellen Anforderung werden bei der Linkgenerierung als Umgebungswerte behandelt.</span><span class="sxs-lookup"><span data-stu-id="4a51d-170">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="4a51d-171">Oft werden ungültige Links erstellt, wenn für die Beispiel-App die Umgebungswerte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="4a51d-171">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="4a51d-172">Weitere Informationen finden Sie unter [Routing zu Controlleraktionen in ASP.NET Core](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="4a51d-172">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-viewstartcshtml-file"></a><span data-ttu-id="4a51d-173">Freigegebenes Layout für Bereiche unter Verwendung der _ViewStart.cshtml-Datei</span><span class="sxs-lookup"><span data-stu-id="4a51d-173">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="4a51d-174">Um ein gemeinsames Layout für die gesamte App freizugeben, verschieben Sie *_ViewStart.cshtml* in den Stammordner der Anwendung.</span><span class="sxs-lookup"><span data-stu-id="4a51d-174">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

<!-- This section will be completed after https://github.com/aspnet/Docs/pull/10978 is merged.
<a name="arp"></a>

## Areas for Razor Pages
-->
<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="4a51d-175">Ändern des Standardbereichsordners, in dem Ansichten gespeichert sind</span><span class="sxs-lookup"><span data-stu-id="4a51d-175">Change default area folder where views are stored</span></span>

<span data-ttu-id="4a51d-176">Der folgende Code ändert den Standardbereichsordner von `"Areas"` in `"MyAreas"`:</span><span class="sxs-lookup"><span data-stu-id="4a51d-176">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<!-- TODO review - can we delete this. Areas doesn't change publishing - right? -->
### <a name="publishing-areas"></a><span data-ttu-id="4a51d-177">Veröffentlichen von Bereichen</span><span class="sxs-lookup"><span data-stu-id="4a51d-177">Publishing Areas</span></span>

<span data-ttu-id="4a51d-178">Alle `*.cshtml`- und `wwwroot/**`-Dateien werden für die Ausgabe veröffentlicht, wenn `<Project Sdk="Microsoft.NET.Sdk.Web">` in der *CSPROJ*-Datei enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="4a51d-178">All `*.cshtml` and `wwwroot/**` files are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the *.csproj* file.</span></span>

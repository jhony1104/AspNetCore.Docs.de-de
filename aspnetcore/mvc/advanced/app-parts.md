---
title: Freigeben von Controllern, Ansichten, Razor Pages und vielem mehr mit Anwendungsparts in ASP.NET Core
author: rick-anderson
description: Freigeben von Controllern, Ansichten, Razor Pages und vielem mehr mit Anwendungsparts in ASP.NET Core
ms.author: riande
ms.date: 11/11/2019
uid: mvc/extensibility/app-parts
ms.openlocfilehash: a95c344410db0651b9f8f1c1eb7551029f084c25
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829074"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts"></a><span data-ttu-id="335e3-103">Freigeben von Controllern, Ansichten, Razor Pages und vielem mehr mit Anwendungsparts</span><span class="sxs-lookup"><span data-stu-id="335e3-103">Share controllers, views, Razor Pages and more with Application Parts</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="335e3-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="335e3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="335e3-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="335e3-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="335e3-106">Ein *Anwendungspart* ist eine Abstraktion der Ressourcen einer App.</span><span class="sxs-lookup"><span data-stu-id="335e3-106">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="335e3-107">Anwendungsparts ermöglichen es ASP.NET Core, Controller, Ansichtskomponenten, Taghilfsprogramme, Razor Pages, Razor-Kompilierungsquellen und vieles mehr zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="335e3-107">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="335e3-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> ist ein Anwendungspart.</span><span class="sxs-lookup"><span data-stu-id="335e3-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> is an Application part.</span></span> <span data-ttu-id="335e3-109">`AssemblyPart` kapselt einen Assemblyverweis und macht Typen und Kompilierungsverweise verfügbar.</span><span class="sxs-lookup"><span data-stu-id="335e3-109">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="335e3-110">[Featureanbieter](#fp) arbeiten mit Anwendungsparts, um die Features einer ASP.NET Core-Anwendung aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="335e3-110">[Feature providers](#fp) work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="335e3-111">Anwendungsparts werden hauptsächlich dafür eingesetzt, eine App so zu konfigurieren, dass sie die ASP.NET Core-Features in einer Assembly ermittelt (oder das Laden solcher Features vermeidet).</span><span class="sxs-lookup"><span data-stu-id="335e3-111">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="335e3-112">Ein Beispiel: Sie möchten einige gängige Funktionen in mehreren Apps gemeinsam nutzen.</span><span class="sxs-lookup"><span data-stu-id="335e3-112">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="335e3-113">Mithilfe von Anwendungsparts können Sie eine Assembly (DLL) für mehrere Apps freigeben, die Controller, Ansichten, Razor Pages, Razor-Kompilierungsquellen, Taghilfsprogramme und vieles mehr enthält.</span><span class="sxs-lookup"><span data-stu-id="335e3-113">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="335e3-114">Die Freigabe einer Assembly ist dem Duplizieren von Code in mehreren Projekten vorzuziehen.</span><span class="sxs-lookup"><span data-stu-id="335e3-114">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="335e3-115">ASP.NET Core-Apps laden Features aus <xref:System.Web.WebPages.ApplicationPart>.</span><span class="sxs-lookup"><span data-stu-id="335e3-115">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="335e3-116">Die <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>-Klasse stellt ein Anwendungspart dar, das durch eine Assembly gesichert wird.</span><span class="sxs-lookup"><span data-stu-id="335e3-116">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="335e3-117">Laden von ASP.NET Core-Features</span><span class="sxs-lookup"><span data-stu-id="335e3-117">Load ASP.NET Core features</span></span>

<span data-ttu-id="335e3-118">Verwenden Sie die Klassen <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> und <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>, um ASP.NET Core-Features (Controller, Ansichtskomponenten usw.) zu ermitteln und zu laden.</span><span class="sxs-lookup"><span data-stu-id="335e3-118">Use the <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> and <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="335e3-119">Der <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> verfolgt die verfügbaren Anwendungsparts und Featureanbieter nach.</span><span class="sxs-lookup"><span data-stu-id="335e3-119">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="335e3-120">Der `ApplicationPartManager` wird in `Startup.ConfigureServices` konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="335e3-120">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="335e3-121">Der folgende Code bietet einen alternativen Ansatz, um den `ApplicationPartManager` mithilfe von `AssemblyPart` zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="335e3-121">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="335e3-122">Die beiden oben gezeigten Codebeispiele laden den `SharedController` aus einer Assembly.</span><span class="sxs-lookup"><span data-stu-id="335e3-122">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="335e3-123">Der `SharedController` befindet sich nicht im Projekt der App.</span><span class="sxs-lookup"><span data-stu-id="335e3-123">The `SharedController` is not in the app's project.</span></span> <span data-ttu-id="335e3-124">Weitere Informationen finden Sie im Beispieldownload für eine [WebAppParts-Lösung](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts).</span><span class="sxs-lookup"><span data-stu-id="335e3-124">See the [WebAppParts solution](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="335e3-125">Einschließen von Ansichten</span><span class="sxs-lookup"><span data-stu-id="335e3-125">Include views</span></span>

<span data-ttu-id="335e3-126">Verwenden Sie eine [Razor-Klassenbibliothek](xref:razor-pages/ui-class), um Ansichten in die Assembly einzuschließen.</span><span class="sxs-lookup"><span data-stu-id="335e3-126">Use a [Razor class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="335e3-127">Vermeiden des Ladens von Ressourcen</span><span class="sxs-lookup"><span data-stu-id="335e3-127">Prevent loading resources</span></span>

<span data-ttu-id="335e3-128">Anwendungsparts können verwendet werden, um das Laden von Ressourcen in einer bestimmten Assembly oder einem bestimmten Speicherort zu *vermeiden*.</span><span class="sxs-lookup"><span data-stu-id="335e3-128">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="335e3-129">Fügen Sie der Sammlung <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> Elemente hinzu, oder entfernen Sie Elemente aus der Sammlung, um Ressourcen auszublenden oder verfügbar zu machen.</span><span class="sxs-lookup"><span data-stu-id="335e3-129">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="335e3-130">Die Reihenfolge der Einträge in der `ApplicationParts`-Sammlung ist nicht wichtig.</span><span class="sxs-lookup"><span data-stu-id="335e3-130">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="335e3-131">Konfigurieren Sie den `ApplicationPartManager`, bevor er zur Konfiguration von Diensten im Container verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="335e3-131">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="335e3-132">Beispielsweise sollten Sie den `ApplicationPartManager` vor dem Aufrufen von `AddControllersAsServices` konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="335e3-132">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="335e3-133">Rufen Sie `Remove` in der `ApplicationParts`-Sammlung auf, um eine Ressource zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="335e3-133">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="335e3-134">Der `ApplicationPartManager` enthält Parts für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="335e3-134">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="335e3-135">Die Assembly der App und die abhängigen Assemblys.</span><span class="sxs-lookup"><span data-stu-id="335e3-135">The app's assembly and dependent assemblies.</span></span>
* `Microsoft.AspNetCore.Mvc.ApplicationParts.CompiledRazorAssemblyPart`
* `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`
* <span data-ttu-id="335e3-136">`Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="335e3-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="335e3-137">`Microsoft.AspNetCore.Mvc.Razor`</span><span class="sxs-lookup"><span data-stu-id="335e3-137">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

<a name="fp"></a>

## <a name="feature-providers"></a><span data-ttu-id="335e3-138">Featureanbieter</span><span class="sxs-lookup"><span data-stu-id="335e3-138">Feature providers</span></span>

<span data-ttu-id="335e3-139">Anwendungsfeatureanbieter untersuchen Anwendungsparts und bieten Features für diese.</span><span class="sxs-lookup"><span data-stu-id="335e3-139">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="335e3-140">Es gibt integrierte Featureanbieter für die folgenden ASP.NET Core-Features:</span><span class="sxs-lookup"><span data-stu-id="335e3-140">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Controllers.ControllerFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.MetadataReferenceFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.ViewsFeatureProvider>
* <span data-ttu-id="335e3-141">`internal class` [RazorCompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)</span><span class="sxs-lookup"><span data-stu-id="335e3-141">`internal class` [RazorCompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)</span></span>

<span data-ttu-id="335e3-142">Featureanbieter erben von <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, wobei `T` der Typ des Features ist.</span><span class="sxs-lookup"><span data-stu-id="335e3-142">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="335e3-143">Featureanbieter können für jeden der oben aufgeführten Featuretypen implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="335e3-143">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="335e3-144">Die Reihenfolge der Featureanbieter in `ApplicationPartManager.FeatureProviders` kann sich auf das Laufzeitverhalten auswirken.</span><span class="sxs-lookup"><span data-stu-id="335e3-144">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="335e3-145">Später hinzugefügte Anbieter können auf Aktionen reagieren, die von früher hinzugefügten Anbietern ausgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="335e3-145">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="335e3-146">Anzeigen verfügbarer Features</span><span class="sxs-lookup"><span data-stu-id="335e3-146">Display available features</span></span>

<span data-ttu-id="335e3-147">Die für eine App verfügbaren Features können aufgelistet werden, indem durch [Abhängigkeitsinjektion](../../fundamentals/dependency-injection.md) ein `ApplicationPartManager` angefordert wird:</span><span class="sxs-lookup"><span data-stu-id="335e3-147">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="335e3-148">Das [Downloadbeispiel](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) verwendet den oben stehenden Code, um die App-Features anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="335e3-148">The [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a><span data-ttu-id="335e3-149">Ermittlung in Anwendungsparts</span><span class="sxs-lookup"><span data-stu-id="335e3-149">Discovery in application parts</span></span>

<span data-ttu-id="335e3-150">Bei der Entwicklung mit Anwendungsparts sind HTTP-404-Fehler nicht ungewöhnlich.</span><span class="sxs-lookup"><span data-stu-id="335e3-150">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="335e3-151">Diese Fehler werden in der Regel dadurch verursacht, dass eine essenzielle Anforderung an die Ermittlung von Anwendungsparts nicht erfüllt ist.</span><span class="sxs-lookup"><span data-stu-id="335e3-151">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="335e3-152">Wenn Ihre App einen HTTP-404-Fehler zurückgibt, überprüfen Sie, ob die folgenden Anforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="335e3-152">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="335e3-153">Die `applicationName`-Einstellungen müssen auf die Stammassembly festgelegt sein, die für die Ermittlung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="335e3-153">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="335e3-154">Die für die Ermittlung verwendete Stammassembly ist normalerweise die Einstiegspunktassembly.</span><span class="sxs-lookup"><span data-stu-id="335e3-154">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="335e3-155">Die Stammassembly muss einen Verweis auf das für die Ermittlung verwendete Part enthalten.</span><span class="sxs-lookup"><span data-stu-id="335e3-155">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="335e3-156">Der Verweis kann direkt oder transitiv sein.</span><span class="sxs-lookup"><span data-stu-id="335e3-156">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="335e3-157">Die Stammassembly muss auf das Web-SDK verweisen.</span><span class="sxs-lookup"><span data-stu-id="335e3-157">The root assembly needs to reference the Web SDK.</span></span> <span data-ttu-id="335e3-158">Das Framework verfügt über eine Logik, die Attribute in die Stammassembly einfügt, die für die Ermittlung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="335e3-158">The framework has logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="335e3-159">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="335e3-159">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="335e3-160">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="335e3-160">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="335e3-161">Ein *Anwendungspart* ist eine Abstraktion der Ressourcen einer App.</span><span class="sxs-lookup"><span data-stu-id="335e3-161">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="335e3-162">Anwendungsparts ermöglichen es ASP.NET Core, Controller, Ansichtskomponenten, Taghilfsprogramme, Razor Pages, Razor-Kompilierungsquellen und vieles mehr zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="335e3-162">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="335e3-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) ist ein Anwendungspart.</span><span class="sxs-lookup"><span data-stu-id="335e3-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="335e3-164">`AssemblyPart` kapselt einen Assemblyverweis und macht Typen und Kompilierungsverweise verfügbar.</span><span class="sxs-lookup"><span data-stu-id="335e3-164">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="335e3-165">*Featureanbieter* arbeiten mit Anwendungsparts, um die Features einer ASP.NET Core-Anwendung aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="335e3-165">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="335e3-166">Anwendungsparts werden hauptsächlich dafür eingesetzt, eine App so zu konfigurieren, dass sie die ASP.NET Core-Features in einer Assembly ermittelt (oder das Laden solcher Features vermeidet).</span><span class="sxs-lookup"><span data-stu-id="335e3-166">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="335e3-167">Ein Beispiel: Sie möchten einige gängige Funktionen in mehreren Apps gemeinsam nutzen.</span><span class="sxs-lookup"><span data-stu-id="335e3-167">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="335e3-168">Mithilfe von Anwendungsparts können Sie eine Assembly (DLL) für mehrere Apps freigeben, die Controller, Ansichten, Razor Pages, Razor-Kompilierungsquellen, Taghilfsprogramme und vieles mehr enthält.</span><span class="sxs-lookup"><span data-stu-id="335e3-168">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="335e3-169">Die Freigabe einer Assembly ist dem Duplizieren von Code in mehreren Projekten vorzuziehen.</span><span class="sxs-lookup"><span data-stu-id="335e3-169">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="335e3-170">ASP.NET Core-Apps laden Features aus <xref:System.Web.WebPages.ApplicationPart>.</span><span class="sxs-lookup"><span data-stu-id="335e3-170">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="335e3-171">Die <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>-Klasse stellt ein Anwendungspart dar, das durch eine Assembly gesichert wird.</span><span class="sxs-lookup"><span data-stu-id="335e3-171">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="335e3-172">Laden von ASP.NET Core-Features</span><span class="sxs-lookup"><span data-stu-id="335e3-172">Load ASP.NET Core features</span></span>

<span data-ttu-id="335e3-173">Verwenden Sie die Klassen `ApplicationPart` und `AssemblyPart`, um ASP.NET Core-Features (Controller, Ansichtskomponenten usw.) zu ermitteln und zu laden.</span><span class="sxs-lookup"><span data-stu-id="335e3-173">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="335e3-174">Der <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> verfolgt die verfügbaren Anwendungsparts und Featureanbieter nach.</span><span class="sxs-lookup"><span data-stu-id="335e3-174">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="335e3-175">Der `ApplicationPartManager` wird in `Startup.ConfigureServices` konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="335e3-175">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="335e3-176">Der folgende Code bietet einen alternativen Ansatz, um den `ApplicationPartManager` mithilfe von `AssemblyPart` zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="335e3-176">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="335e3-177">Die beiden oben gezeigten Codebeispiele laden den `SharedController` aus einer Assembly.</span><span class="sxs-lookup"><span data-stu-id="335e3-177">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="335e3-178">Der `SharedController` befindet sich nicht im Projekt der Anwendung.</span><span class="sxs-lookup"><span data-stu-id="335e3-178">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="335e3-179">Weitere Informationen finden Sie im Beispieldownload für eine [WebAppParts-Lösung](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts).</span><span class="sxs-lookup"><span data-stu-id="335e3-179">See the [WebAppParts solution](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="335e3-180">Einschließen von Ansichten</span><span class="sxs-lookup"><span data-stu-id="335e3-180">Include views</span></span>

<span data-ttu-id="335e3-181">Verwenden Sie eine [Razor-Klassenbibliothek](xref:razor-pages/ui-class), um Ansichten in die Assembly einzuschließen.</span><span class="sxs-lookup"><span data-stu-id="335e3-181">Use a [Razor class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="335e3-182">Vermeiden des Ladens von Ressourcen</span><span class="sxs-lookup"><span data-stu-id="335e3-182">Prevent loading resources</span></span>

<span data-ttu-id="335e3-183">Anwendungsparts können verwendet werden, um das Laden von Ressourcen in einer bestimmten Assembly oder einem bestimmten Speicherort zu *vermeiden*.</span><span class="sxs-lookup"><span data-stu-id="335e3-183">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="335e3-184">Fügen Sie der Sammlung <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> Elemente hinzu, oder entfernen Sie Elemente aus der Sammlung, um Ressourcen auszublenden oder verfügbar zu machen.</span><span class="sxs-lookup"><span data-stu-id="335e3-184">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="335e3-185">Die Reihenfolge der Einträge in der `ApplicationParts`-Sammlung ist nicht wichtig.</span><span class="sxs-lookup"><span data-stu-id="335e3-185">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="335e3-186">Konfigurieren Sie den `ApplicationPartManager`, bevor er zur Konfiguration von Diensten im Container verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="335e3-186">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="335e3-187">Beispielsweise sollten Sie den `ApplicationPartManager` vor dem Aufrufen von `AddControllersAsServices` konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="335e3-187">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="335e3-188">Rufen Sie `Remove` in der `ApplicationParts`-Sammlung auf, um eine Ressource zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="335e3-188">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="335e3-189">Der folgende Code verwendet <xref:Microsoft.AspNetCore.Mvc.ApplicationParts>, um `MyDependentLibrary` aus der App zu entfernen: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="335e3-189">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="335e3-190">Der `ApplicationPartManager` enthält Parts für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="335e3-190">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="335e3-191">Die Assembly der App und die abhängigen Assemblys.</span><span class="sxs-lookup"><span data-stu-id="335e3-191">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="335e3-192">`Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="335e3-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="335e3-193">`Microsoft.AspNetCore.Mvc.Razor`</span><span class="sxs-lookup"><span data-stu-id="335e3-193">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="335e3-194">Anwendungsfeatureanbieter</span><span class="sxs-lookup"><span data-stu-id="335e3-194">Application feature providers</span></span>

<span data-ttu-id="335e3-195">Anwendungsfeatureanbieter untersuchen Anwendungsparts und bieten Features für diese.</span><span class="sxs-lookup"><span data-stu-id="335e3-195">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="335e3-196">Es gibt integrierte Featureanbieter für die folgenden ASP.NET Core-Features:</span><span class="sxs-lookup"><span data-stu-id="335e3-196">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="335e3-197">Controller</span><span class="sxs-lookup"><span data-stu-id="335e3-197">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="335e3-198">Taghilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="335e3-198">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="335e3-199">Ansichtskomponenten</span><span class="sxs-lookup"><span data-stu-id="335e3-199">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="335e3-200">Featureanbieter erben von <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, wobei `T` der Typ des Features ist.</span><span class="sxs-lookup"><span data-stu-id="335e3-200">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="335e3-201">Featureanbieter können für jeden der oben aufgeführten Featuretypen implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="335e3-201">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="335e3-202">Die Reihenfolge der Featureanbieter in `ApplicationPartManager.FeatureProviders` kann sich auf das Laufzeitverhalten auswirken.</span><span class="sxs-lookup"><span data-stu-id="335e3-202">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="335e3-203">Später hinzugefügte Anbieter können auf Aktionen reagieren, die von früher hinzugefügten Anbietern ausgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="335e3-203">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="335e3-204">Anzeigen verfügbarer Features</span><span class="sxs-lookup"><span data-stu-id="335e3-204">Display available features</span></span>

<span data-ttu-id="335e3-205">Die für eine App verfügbaren Features können aufgelistet werden, indem durch [Abhängigkeitsinjektion](../../fundamentals/dependency-injection.md) ein `ApplicationPartManager` angefordert wird:</span><span class="sxs-lookup"><span data-stu-id="335e3-205">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="335e3-206">Das [Downloadbeispiel](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) verwendet den oben stehenden Code, um die App-Features anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="335e3-206">The [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a><span data-ttu-id="335e3-207">Ermittlung in Anwendungsparts</span><span class="sxs-lookup"><span data-stu-id="335e3-207">Discovery in application parts</span></span>

<span data-ttu-id="335e3-208">Bei der Entwicklung mit Anwendungsparts sind HTTP-404-Fehler nicht ungewöhnlich.</span><span class="sxs-lookup"><span data-stu-id="335e3-208">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="335e3-209">Diese Fehler werden in der Regel dadurch verursacht, dass eine essenzielle Anforderung an die Ermittlung von Anwendungsparts nicht erfüllt ist.</span><span class="sxs-lookup"><span data-stu-id="335e3-209">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="335e3-210">Wenn Ihre App einen HTTP-404-Fehler zurückgibt, überprüfen Sie, ob die folgenden Anforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="335e3-210">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="335e3-211">Die `applicationName`-Einstellungen müssen auf die Stammassembly festgelegt sein, die für die Ermittlung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="335e3-211">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="335e3-212">Die für die Ermittlung verwendete Stammassembly ist normalerweise die Einstiegspunktassembly.</span><span class="sxs-lookup"><span data-stu-id="335e3-212">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="335e3-213">Die Stammassembly muss einen Verweis auf das für die Ermittlung verwendete Part enthalten.</span><span class="sxs-lookup"><span data-stu-id="335e3-213">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="335e3-214">Der Verweis kann direkt oder transitiv sein.</span><span class="sxs-lookup"><span data-stu-id="335e3-214">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="335e3-215">Die Stammassembly muss auf das Web-SDK verweisen.</span><span class="sxs-lookup"><span data-stu-id="335e3-215">The root assembly needs to reference the Web SDK.</span></span>
  * <span data-ttu-id="335e3-216">Das ASP.NET Core-Framework verfügt über eine benutzerdefinierte Logik, die Attribute in die Stammassembly einfügt, die für die Ermittlung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="335e3-216">The ASP.NET Core framework has custom build logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end

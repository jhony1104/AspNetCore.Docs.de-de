---
title: Anwendungsparts in ASP.NET Core
author: rick-anderson
description: Freigeben von Controllern, Ansichten, Razor Pages und vielem mehr mit Anwendungsparts in ASP.NET Core
ms.author: riande
ms.date: 05/14/2019
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 4b4c8c554a7045a180b56cf9998ab1a8496cde1b
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71207353"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts-in-aspnet-core"></a><span data-ttu-id="03311-103">Freigeben von Controllern, Ansichten, Razor Pages und vielem mehr mit Anwendungsparts in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="03311-103">Share controllers, views, Razor Pages and more with Application Parts in ASP.NET Core</span></span>

<span data-ttu-id="03311-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="03311-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="03311-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="03311-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="03311-106">Ein *Anwendungspart* ist eine Abstraktion der Ressourcen einer App.</span><span class="sxs-lookup"><span data-stu-id="03311-106">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="03311-107">Anwendungsparts ermöglichen es ASP.NET Core, Controller, Ansichtskomponenten, Taghilfsprogramme, Razor Pages, Razor-Kompilierungsquellen und vieles mehr zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="03311-107">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="03311-108">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) ist ein Anwendungspart.</span><span class="sxs-lookup"><span data-stu-id="03311-108">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="03311-109">`AssemblyPart` kapselt einen Assemblyverweis und macht Typen und Kompilierungsverweise verfügbar.</span><span class="sxs-lookup"><span data-stu-id="03311-109">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="03311-110">*Featureanbieter* arbeiten mit Anwendungsparts, um die Features einer ASP.NET Core-Anwendung aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="03311-110">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="03311-111">Anwendungsparts werden hauptsächlich dafür eingesetzt, eine App so zu konfigurieren, dass sie die ASP.NET Core-Features in einer Assembly ermittelt (oder das Laden solcher Features vermeidet).</span><span class="sxs-lookup"><span data-stu-id="03311-111">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="03311-112">Ein Beispiel: Sie möchten einige gängige Funktionen in mehreren Apps gemeinsam nutzen.</span><span class="sxs-lookup"><span data-stu-id="03311-112">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="03311-113">Mithilfe von Anwendungsparts können Sie eine Assembly (DLL) für mehrere Apps freigeben, die Controller, Ansichten, Razor Pages, Razor-Kompilierungsquellen, Taghilfsprogramme und vieles mehr enthält.</span><span class="sxs-lookup"><span data-stu-id="03311-113">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="03311-114">Die Freigabe einer Assembly ist dem Duplizieren von Code in mehreren Projekten vorzuziehen.</span><span class="sxs-lookup"><span data-stu-id="03311-114">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="03311-115">ASP.NET Core-Apps laden Features aus <xref:System.Web.WebPages.ApplicationPart>.</span><span class="sxs-lookup"><span data-stu-id="03311-115">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="03311-116">Die <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>-Klasse stellt ein Anwendungspart dar, das durch eine Assembly gesichert wird.</span><span class="sxs-lookup"><span data-stu-id="03311-116">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="03311-117">Laden von ASP.NET Core-Features</span><span class="sxs-lookup"><span data-stu-id="03311-117">Load ASP.NET Core features</span></span>

<span data-ttu-id="03311-118">Verwenden Sie die Klassen `ApplicationPart` und `AssemblyPart`, um ASP.NET Core-Features (Controller, Ansichtskomponenten usw.) zu ermitteln und zu laden.</span><span class="sxs-lookup"><span data-stu-id="03311-118">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="03311-119">Der <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> verfolgt die verfügbaren Anwendungsparts und Featureanbieter nach.</span><span class="sxs-lookup"><span data-stu-id="03311-119">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="03311-120">Der `ApplicationPartManager` wird in `Startup.ConfigureServices` konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="03311-120">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="03311-121">Der folgende Code bietet einen alternativen Ansatz, um den `ApplicationPartManager` mithilfe von `AssemblyPart` zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="03311-121">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="03311-122">Die beiden oben gezeigten Codebeispiele laden den `SharedController` aus einer Assembly.</span><span class="sxs-lookup"><span data-stu-id="03311-122">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="03311-123">Der `SharedController` befindet sich nicht im Projekt der Anwendung.</span><span class="sxs-lookup"><span data-stu-id="03311-123">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="03311-124">Weitere Informationen finden Sie im Beispieldownload für eine [WebAppParts-Lösung](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts).</span><span class="sxs-lookup"><span data-stu-id="03311-124">See the [WebAppParts solution](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="03311-125">Einschließen von Ansichten</span><span class="sxs-lookup"><span data-stu-id="03311-125">Include views</span></span>

<span data-ttu-id="03311-126">So schließen Sie Ansichten in der Assembly ein:</span><span class="sxs-lookup"><span data-stu-id="03311-126">To include views in the assembly:</span></span>

* <span data-ttu-id="03311-127">Fügen Sie der freigegebenen Projektdatei das folgende Markup hinzu:</span><span class="sxs-lookup"><span data-stu-id="03311-127">Add the following markup to the shared project file:</span></span>

  ```csproj
  <ItemGroup>
      <EmbeddedResource Include="Views\**\*.cshtml" />
  </ItemGroup>
  ```

* <span data-ttu-id="03311-128">Fügen Sie den <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> zur <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine> hinzu:</span><span class="sxs-lookup"><span data-stu-id="03311-128">Add the <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> to the <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>:</span></span>

  [!code-csharp[](./app-parts/sample1/WebAppParts/StartupViews.cs?name=snippet&highlight=3-7)]

### <a name="prevent-loading-resources"></a><span data-ttu-id="03311-129">Vermeiden des Ladens von Ressourcen</span><span class="sxs-lookup"><span data-stu-id="03311-129">Prevent loading resources</span></span>

<span data-ttu-id="03311-130">Anwendungsparts können verwendet werden, um das Laden von Ressourcen in einer bestimmten Assembly oder einem bestimmten Speicherort zu *vermeiden*.</span><span class="sxs-lookup"><span data-stu-id="03311-130">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="03311-131">Fügen Sie der Sammlung <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> Elemente hinzu, oder entfernen Sie Elemente aus der Sammlung, um Ressourcen auszublenden oder verfügbar zu machen.</span><span class="sxs-lookup"><span data-stu-id="03311-131">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="03311-132">Die Reihenfolge der Einträge in der `ApplicationParts`-Sammlung ist nicht wichtig.</span><span class="sxs-lookup"><span data-stu-id="03311-132">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="03311-133">Konfigurieren Sie den `ApplicationPartManager`, bevor er zur Konfiguration von Diensten im Container verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="03311-133">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="03311-134">Beispielsweise sollten Sie den `ApplicationPartManager` vor dem Aufrufen von `AddControllersAsServices` konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="03311-134">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="03311-135">Rufen Sie `Remove` in der `ApplicationParts`-Sammlung auf, um eine Ressource zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="03311-135">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="03311-136">Der folgende Code verwendet <xref:Microsoft.AspNetCore.Mvc.ApplicationParts>, um `MyDependentLibrary` aus der App zu entfernen: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="03311-136">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="03311-137">Der `ApplicationPartManager` enthält Parts für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="03311-137">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="03311-138">Die Assembly der App und die abhängigen Assemblys.</span><span class="sxs-lookup"><span data-stu-id="03311-138">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="03311-139">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="03311-139">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="03311-140">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="03311-140">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="03311-141">Anwendungsfeatureanbieter</span><span class="sxs-lookup"><span data-stu-id="03311-141">Application feature providers</span></span>

<span data-ttu-id="03311-142">Anwendungsfeatureanbieter untersuchen Anwendungsparts und bieten Features für diese.</span><span class="sxs-lookup"><span data-stu-id="03311-142">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="03311-143">Es gibt integrierte Featureanbieter für die folgenden ASP.NET Core-Features:</span><span class="sxs-lookup"><span data-stu-id="03311-143">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="03311-144">Controller</span><span class="sxs-lookup"><span data-stu-id="03311-144">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="03311-145">Taghilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="03311-145">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="03311-146">Ansichtskomponenten</span><span class="sxs-lookup"><span data-stu-id="03311-146">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="03311-147">Featureanbieter erben von <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, wobei `T` der Typ des Features ist.</span><span class="sxs-lookup"><span data-stu-id="03311-147">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="03311-148">Featureanbieter können für jeden der oben aufgeführten Featuretypen implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="03311-148">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="03311-149">Die Reihenfolge der Featureanbieter in `ApplicationPartManager.FeatureProviders` kann sich auf das Laufzeitverhalten auswirken.</span><span class="sxs-lookup"><span data-stu-id="03311-149">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="03311-150">Später hinzugefügte Anbieter können auf Aktionen reagieren, die von früher hinzugefügten Anbietern ausgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="03311-150">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="generic-controller-feature"></a><span data-ttu-id="03311-151">Generisches Controllerfeature</span><span class="sxs-lookup"><span data-stu-id="03311-151">Generic controller feature</span></span>

<span data-ttu-id="03311-152">ASP.NET Core ignoriert [generische Controller](/dotnet/csharp/programming-guide/generics/generic-classes).</span><span class="sxs-lookup"><span data-stu-id="03311-152">ASP.NET Core ignores [generic controllers](/dotnet/csharp/programming-guide/generics/generic-classes).</span></span> <span data-ttu-id="03311-153">Ein generischer Controller weist einen Typparameter auf (z. B. `MyController<T>`).</span><span class="sxs-lookup"><span data-stu-id="03311-153">A generic controller has a type parameter (for example, `MyController<T>`).</span></span> <span data-ttu-id="03311-154">Das folgende Beispiel fügt generische Controllerinstanzen für eine angegebene Liste von Typen hinzu:</span><span class="sxs-lookup"><span data-stu-id="03311-154">The following sample adds generic controller instances for a specified list of types:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerFeatureProvider.cs?name=snippet)]

<span data-ttu-id="03311-155">Diese Typen werden in `EntityTypes.Types` definiert:</span><span class="sxs-lookup"><span data-stu-id="03311-155">The types are defined in `EntityTypes.Types`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Models/EntityTypes.cs?name=snippet)]

<span data-ttu-id="03311-156">Der Featureanbieter wird in `Startup` hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="03311-156">The feature provider is added in `Startup`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Startup.cs?name=snippet)]

<span data-ttu-id="03311-157">Namen generischer Controller für das Routing weisen das Format *GenericController'1 [Widget]* anstelle von *Widget* auf.</span><span class="sxs-lookup"><span data-stu-id="03311-157">Generic controller names used for routing are of the form *GenericController\`1[Widget]* rather than *Widget*.</span></span> <span data-ttu-id="03311-158">Das folgende Attribut ändert den Namen, damit dieser dem vom Controller verwendeten generischen Typ entspricht:</span><span class="sxs-lookup"><span data-stu-id="03311-158">The following attribute modifies the name to correspond to the generic type used by the controller:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerNameConvention.cs)]

<span data-ttu-id="03311-159">Die `GenericController`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="03311-159">The `GenericController` class:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericController.cs)]

<span data-ttu-id="03311-160">Die Anforderung der URL `https://localhost:5001/Sprocket` beispielsweise führt zu folgender Antwort:</span><span class="sxs-lookup"><span data-stu-id="03311-160">For example, requesting a URL of `https://localhost:5001/Sprocket` results in the following response:</span></span>

```text
Hello from a generic Sprocket controller.
```

### <a name="display-available-features"></a><span data-ttu-id="03311-161">Anzeigen verfügbarer Features</span><span class="sxs-lookup"><span data-stu-id="03311-161">Display available features</span></span>

<span data-ttu-id="03311-162">Die für eine App verfügbaren Features können aufgelistet werden, indem durch [Abhängigkeitsinjektion](../../fundamentals/dependency-injection.md) ein `ApplicationPartManager` angefordert wird:</span><span class="sxs-lookup"><span data-stu-id="03311-162">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="03311-163">Das [Downloadbeispiel](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) verwendet den oben stehenden Code, um die App-Features anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="03311-163">The [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

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

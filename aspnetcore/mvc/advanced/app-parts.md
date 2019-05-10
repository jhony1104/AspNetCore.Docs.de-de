---
title: Anwendungsparts in ASP.NET Core
author: ardalis
description: Dieser Artikel enthält Informationen zur Verwendung von Anwendungsparts, die Abstraktionen der Ressourcen einer Anwendung sind, damit Sie Features ermitteln oder das Laden von Features aus einer Assembly verhindern können.
ms.author: riande
ms.date: 01/04/2017
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 5d5e79d3afe0eee6d0b126d4642ccd5f61bff409
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64888135"
---
# <a name="application-parts-in-aspnet-core"></a><span data-ttu-id="3ef9f-103">Anwendungsparts in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3ef9f-103">Application Parts in ASP.NET Core</span></span>

<span data-ttu-id="3ef9f-104">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3ef9f-104">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="3ef9f-105">Ein *Anwendungspart* ist eine Abstraktion der Ressourcen einer Anwendung, aus der MVC-Features wie Controller, Komponentenansichten oder Taghilfsprogramme ermittelt werden können.</span><span class="sxs-lookup"><span data-stu-id="3ef9f-105">An *Application Part* is an abstraction over the resources of an application, from which MVC features like controllers, view components, or tag helpers may be discovered.</span></span> <span data-ttu-id="3ef9f-106">Ein Beispiel für einen Anwendungspart ist ein AssemblyPart, der einen Assemblyverweis kapselt und Typen und Kompilierungsverweise verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="3ef9f-106">One example of an application part is an AssemblyPart, which encapsulates an assembly reference and exposes types and compilation references.</span></span> <span data-ttu-id="3ef9f-107">*Featureanbieter* arbeiten mit Anwendungsparts, um die Features einer ASP.NET Core MVC-Anwendung aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="3ef9f-107">*Feature providers* work with application parts to populate the features of an ASP.NET Core MVC app.</span></span> <span data-ttu-id="3ef9f-108">Anwendungsparts ermöglichen Ihnen hauptsächlich das Konfigurieren Ihrer App zum Ermitteln (oder zur Vermeidung des Ladens) von MVC-Features aus einer Assembly.</span><span class="sxs-lookup"><span data-stu-id="3ef9f-108">The main use case for application parts is to allow you to configure your app to discover (or avoid loading) MVC features from an assembly.</span></span>

## <a name="introducing-application-parts"></a><span data-ttu-id="3ef9f-109">Einführung in Anwendungsparts</span><span class="sxs-lookup"><span data-stu-id="3ef9f-109">Introducing Application Parts</span></span>

<span data-ttu-id="3ef9f-110">MVC-Anwendungen laden ihre Features aus [Anwendungsparts](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpart).</span><span class="sxs-lookup"><span data-stu-id="3ef9f-110">MVC apps load their features from [application parts](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpart).</span></span> <span data-ttu-id="3ef9f-111">Insbesondere die [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart)-Klasse stellt einen Anwendungspart dar, der von einer Assembly gesichert wird.</span><span class="sxs-lookup"><span data-stu-id="3ef9f-111">In particular, the [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) class represents an application part that's backed by an assembly.</span></span> <span data-ttu-id="3ef9f-112">Sie können diese Klassen verwenden, um MVC-Features, z.B. Controller, Ansichtskomponenten, Taghilfsprogramme und Razor-Kompilierungsquellen zu entdecken und zu laden.</span><span class="sxs-lookup"><span data-stu-id="3ef9f-112">You can use these classes to discover and load MVC features, such as controllers, view components, tag helpers, and razor compilation sources.</span></span> <span data-ttu-id="3ef9f-113">Der [ApplicationPartManager](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpartmanager) ist verantwortlich für das Nachverfolgen von Anwendungsparts und Featureanbietern, die für die MVC-App verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="3ef9f-113">The [ApplicationPartManager](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpartmanager) is responsible for tracking the application parts and feature providers available to the MVC app.</span></span> <span data-ttu-id="3ef9f-114">Sie können bei der Konfiguration von MVC mit `ApplicationPartManager` in `Startup` interagieren:</span><span class="sxs-lookup"><span data-stu-id="3ef9f-114">You can interact with the `ApplicationPartManager` in `Startup` when you configure MVC:</span></span>

```csharp
// create an assembly part from a class's assembly
var assembly = typeof(Startup).GetTypeInfo().Assembly;
services.AddMvc()
    .AddApplicationPart(assembly);

// OR
var assembly = typeof(Startup).GetTypeInfo().Assembly;
var part = new AssemblyPart(assembly);
services.AddMvc()
    .ConfigureApplicationPartManager(apm => apm.ApplicationParts.Add(part));
```

<span data-ttu-id="3ef9f-115">Standardmäßig durchsucht MVC die Abhängigkeitsstruktur und findet Controller (auch in anderen Assemblys).</span><span class="sxs-lookup"><span data-stu-id="3ef9f-115">By default MVC will search the dependency tree and find controllers (even in other assemblies).</span></span> <span data-ttu-id="3ef9f-116">Um eine beliebige Assembly (z.B. in einem Plug-in, auf das zum Zeitpunkt der Kompilierung nicht verwiesen wird) zu laden, können Sie einen Anwendungspart verwenden.</span><span class="sxs-lookup"><span data-stu-id="3ef9f-116">To load an arbitrary assembly (for instance, from a plugin that isn't referenced at compile time), you can use an application part.</span></span>

<span data-ttu-id="3ef9f-117">Sie können Anwendungsparts verwenden, um die Suche nach Controllern in einer bestimmten Assembly oder an einem bestimmten Speicherort zu *vermeiden*.</span><span class="sxs-lookup"><span data-stu-id="3ef9f-117">You can use application parts to *avoid* looking for controllers in a particular assembly or location.</span></span> <span data-ttu-id="3ef9f-118">Sie können steuern, welche Parts (oder Assemblys) für die Anwendung verfügbar sind, indem Sie die `ApplicationParts`-Sammlung des `ApplicationPartManager` ändern.</span><span class="sxs-lookup"><span data-stu-id="3ef9f-118">You can control which parts (or assemblies) are available to the app by modifying the `ApplicationParts` collection of the `ApplicationPartManager`.</span></span> <span data-ttu-id="3ef9f-119">Die Reihenfolge der Einträge in der `ApplicationParts`-Sammlung ist nicht wichtig.</span><span class="sxs-lookup"><span data-stu-id="3ef9f-119">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="3ef9f-120">Es ist wichtig, `ApplicationPartManager` vollständig zu konfigurieren, bevor er zur Konfiguration von Diensten im Container verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="3ef9f-120">It's important to fully configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="3ef9f-121">Beispielsweise sollten Sie `ApplicationPartManager` vor dem Aufrufen von `AddControllersAsServices` vollständig konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="3ef9f-121">For example, you should fully configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="3ef9f-122">Sollte dies fehlschlagen, sind die Controller in den Anwendungsparts, die nach dem Methodenaufruf hinzugefügt wurden, nicht betroffen (werden nicht als Dienste registriert). Ihre Anwendung könnte sich aus diesem Grund fehlerhaft verhalten.</span><span class="sxs-lookup"><span data-stu-id="3ef9f-122">Failing to do so, will mean that controllers in application parts added after that method call won't be affected (won't get registered as services) which might result in incorrect behavior of your application.</span></span>

<span data-ttu-id="3ef9f-123">Wenn Sie über eine Assembly mit Controllern verfügen, die Sie nicht verwendet möchten, entfernen Sie sie aus dem `ApplicationPartManager`:</span><span class="sxs-lookup"><span data-stu-id="3ef9f-123">If you have an assembly that contains controllers you don't want to be used, remove it from the `ApplicationPartManager`:</span></span>

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(apm =>
    {
        var dependentLibrary = apm.ApplicationParts
            .FirstOrDefault(part => part.Name == "DependentLibrary");

        if (dependentLibrary != null)
        {
           apm.ApplicationParts.Remove(dependentLibrary);
        }
    })
```

<span data-ttu-id="3ef9f-124">Neben der Assembly Ihres Projekts und deren abhängigen Assemblys enthält `ApplicationPartManager` standardmäßig auch Elemente für `Microsoft.AspNetCore.Mvc.TagHelpers` und `Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="3ef9f-124">In addition to your project's assembly and its dependent assemblies, the `ApplicationPartManager` will include parts for `Microsoft.AspNetCore.Mvc.TagHelpers` and `Microsoft.AspNetCore.Mvc.Razor` by default.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="3ef9f-125">Anwendungsfeatureanbieter</span><span class="sxs-lookup"><span data-stu-id="3ef9f-125">Application Feature Providers</span></span>

<span data-ttu-id="3ef9f-126">Anwendungsfeatureanbieter untersuchen Anwendungsparts und bieten Features für diese.</span><span class="sxs-lookup"><span data-stu-id="3ef9f-126">Application Feature Providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="3ef9f-127">Es gibt integrierte Featureanbieter für die folgenden MVC-Features:</span><span class="sxs-lookup"><span data-stu-id="3ef9f-127">There are built-in feature providers for the following MVC features:</span></span>

* [<span data-ttu-id="3ef9f-128">Controller</span><span class="sxs-lookup"><span data-stu-id="3ef9f-128">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="3ef9f-129">Metadatenverweis</span><span class="sxs-lookup"><span data-stu-id="3ef9f-129">Metadata Reference</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.compilation.metadatareferencefeatureprovider)
* [<span data-ttu-id="3ef9f-130">Taghilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="3ef9f-130">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="3ef9f-131">Ansichtskomponenten</span><span class="sxs-lookup"><span data-stu-id="3ef9f-131">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="3ef9f-132">Featureanbieter erben von `IApplicationFeatureProvider<T>`, wobei `T` der Typ des Features ist.</span><span class="sxs-lookup"><span data-stu-id="3ef9f-132">Feature providers inherit from `IApplicationFeatureProvider<T>`, where `T` is the type of the feature.</span></span> <span data-ttu-id="3ef9f-133">Sie können Ihren eigenen Featureanbieter für alle oben aufgelisteten MVC-Featuretypen implementieren.</span><span class="sxs-lookup"><span data-stu-id="3ef9f-133">You can implement your own feature providers for any of MVC's feature types listed above.</span></span> <span data-ttu-id="3ef9f-134">Die Reihenfolge der Featureanbieter in der `ApplicationPartManager.FeatureProviders`-Sammlung kann wichtig sein, da Anbieter später auf Aktionen von vorherigen Anbietern reagieren können.</span><span class="sxs-lookup"><span data-stu-id="3ef9f-134">The order of feature providers in the `ApplicationPartManager.FeatureProviders` collection can be important, since later providers can react to actions taken by previous providers.</span></span>

### <a name="sample-generic-controller-feature"></a><span data-ttu-id="3ef9f-135">Beispiel: Generisches Controllerfeature</span><span class="sxs-lookup"><span data-stu-id="3ef9f-135">Sample: Generic controller feature</span></span>

<span data-ttu-id="3ef9f-136">Standardmäßig ignoriert ASP.NET Core MVC generische Controller (z.B. `SomeController<T>`).</span><span class="sxs-lookup"><span data-stu-id="3ef9f-136">By default, ASP.NET Core MVC ignores generic controllers (for example, `SomeController<T>`).</span></span> <span data-ttu-id="3ef9f-137">Dieses Beispiel verwendet einen Controllerfeatureanbieter, der nach dem Standardanbieter ausgeführt wird und generische Controllerinstanzen für eine angegebene Liste von Typen (definiert in `EntityTypes.Types`) hinzufügt:</span><span class="sxs-lookup"><span data-stu-id="3ef9f-137">This sample uses a controller feature provider that runs after the default provider and adds generic controller instances for a specified list of types (defined in `EntityTypes.Types`):</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericControllerFeatureProvider.cs?highlight=13&range=18-36)]

<span data-ttu-id="3ef9f-138">Die Entitätstypen:</span><span class="sxs-lookup"><span data-stu-id="3ef9f-138">The entity types:</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/Model/EntityTypes.cs?range=6-16)]

<span data-ttu-id="3ef9f-139">Der Featureanbieter wird in `Startup` hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="3ef9f-139">The feature provider is added in `Startup`:</span></span>

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(apm => 
        apm.FeatureProviders.Add(new GenericControllerFeatureProvider()));
```

<span data-ttu-id="3ef9f-140">Standardmäßig sind die Namen der generischen Controller für das Routing *GenericController'1 [Widget]* anstelle von *Widget*.</span><span class="sxs-lookup"><span data-stu-id="3ef9f-140">By default, the generic controller names used for routing would be of the form *GenericController\`1[Widget]* instead of *Widget*.</span></span> <span data-ttu-id="3ef9f-141">Das folgende Attribut wird zu Änderung des Namens verwendet, damit dieser dem vom Controller verwendeten generischen Typ entspricht:</span><span class="sxs-lookup"><span data-stu-id="3ef9f-141">The following attribute is used to modify the name to correspond to the generic type used by the controller:</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericControllerNameConvention.cs)]

<span data-ttu-id="3ef9f-142">Die `GenericController`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="3ef9f-142">The `GenericController` class:</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericController.cs?highlight=5-6)]

<span data-ttu-id="3ef9f-143">Das Ergebnis, wenn eine übereinstimmende Route angefordert wird:</span><span class="sxs-lookup"><span data-stu-id="3ef9f-143">The result, when a matching route is requested:</span></span>

![Beispielausgabe aus der Beispielanwendung: „Hello from a generic Sprocket controller.“](app-parts/_static/generic-controller.png)

### <a name="sample-display-available-features"></a><span data-ttu-id="3ef9f-145">Beispiel: Anzeigen verfügbarer Features</span><span class="sxs-lookup"><span data-stu-id="3ef9f-145">Sample: Display available features</span></span>

<span data-ttu-id="3ef9f-146">Sie können die verfügbaren ausgefüllten Features Ihrer Anwendung durchlaufen, indem Sie durch eine [Dependency Injection](../../fundamentals/dependency-injection.md) einen `ApplicationPartManager` anfordern und ihn dazu verwenden, die Instanzen der entsprechenden Features aufzufüllen:</span><span class="sxs-lookup"><span data-stu-id="3ef9f-146">You can iterate through the populated features available to your app by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md) and using it to populate instances of the appropriate features:</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="3ef9f-147">Beispielausgabe:</span><span class="sxs-lookup"><span data-stu-id="3ef9f-147">Example output:</span></span>

![Beispielausgabe aus der Beispielanwendung](app-parts/_static/available-features.png)

---
title: Anwendungsparts in ASP.NET Core
author: ardalis
description: Dieser Artikel enthält Informationen zur Verwendung von Anwendungsparts, die Abstraktionen der Ressourcen einer Anwendung sind, damit Sie Features ermitteln oder das Laden von Features aus einer Assembly verhindern können.
ms.author: riande
ms.date: 01/04/2017
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 4900ccf5589500db076f8cecd9da198c6a7ceea4
ms.sourcegitcommit: 41f2c1a6b316e6e368a4fd27a8b18d157cef91e1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69886461"
---
<!-- DO NOT MAKE CHANGES BEFORE https://github.com/aspnet/AspNetCore.Docs/pull/12376 Merges -->

# <a name="application-parts-in-aspnet-core"></a>Anwendungsparts in ASP.NET Core

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Ein *Anwendungspart* ist eine Abstraktion der Ressourcen einer Anwendung, aus der MVC-Features wie Controller, Komponentenansichten oder Taghilfsprogramme ermittelt werden können. Ein Beispiel für einen Anwendungspart ist ein AssemblyPart, der einen Assemblyverweis kapselt und Typen und Kompilierungsverweise verfügbar macht. *Featureanbieter* arbeiten mit Anwendungsparts, um die Features einer ASP.NET Core MVC-Anwendung aufzufüllen. Anwendungsparts ermöglichen Ihnen hauptsächlich das Konfigurieren Ihrer App zum Ermitteln (oder zur Vermeidung des Ladens) von MVC-Features aus einer Assembly.

## <a name="introducing-application-parts"></a>Einführung in Anwendungsparts

MVC-Anwendungen laden ihre Features aus [Anwendungsparts](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpart). Insbesondere die [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart)-Klasse stellt einen Anwendungspart dar, der von einer Assembly gesichert wird. Sie können diese Klassen verwenden, um MVC-Features, z.B. Controller, Ansichtskomponenten, Taghilfsprogramme und Razor-Kompilierungsquellen zu entdecken und zu laden. Der [ApplicationPartManager](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpartmanager) ist verantwortlich für das Nachverfolgen von Anwendungsparts und Featureanbietern, die für die MVC-App verfügbar sind. Sie können bei der Konfiguration von MVC mit `ApplicationPartManager` in `Startup` interagieren:

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

Standardmäßig durchsucht MVC die Abhängigkeitsstruktur und findet Controller (auch in anderen Assemblys). Um eine beliebige Assembly (z.B. in einem Plug-in, auf das zum Zeitpunkt der Kompilierung nicht verwiesen wird) zu laden, können Sie einen Anwendungspart verwenden.

Sie können Anwendungsparts verwenden, um die Suche nach Controllern in einer bestimmten Assembly oder an einem bestimmten Speicherort zu *vermeiden*. Sie können steuern, welche Parts (oder Assemblys) für die Anwendung verfügbar sind, indem Sie die `ApplicationParts`-Sammlung des `ApplicationPartManager` ändern. Die Reihenfolge der Einträge in der `ApplicationParts`-Sammlung ist nicht wichtig. Es ist wichtig, `ApplicationPartManager` vollständig zu konfigurieren, bevor er zur Konfiguration von Diensten im Container verwendet wird. Beispielsweise sollten Sie `ApplicationPartManager` vor dem Aufrufen von `AddControllersAsServices` vollständig konfigurieren. Sollte dies fehlschlagen, sind die Controller in den Anwendungsparts, die nach dem Methodenaufruf hinzugefügt wurden, nicht betroffen (werden nicht als Dienste registriert). Ihre Anwendung könnte sich aus diesem Grund fehlerhaft verhalten.

Wenn Sie über eine Assembly mit Controllern verfügen, die Sie nicht verwendet möchten, entfernen Sie sie aus dem `ApplicationPartManager`:

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

Neben der Assembly Ihres Projekts und deren abhängigen Assemblys enthält `ApplicationPartManager` standardmäßig auch Elemente für `Microsoft.AspNetCore.Mvc.TagHelpers` und `Microsoft.AspNetCore.Mvc.Razor`.

## <a name="application-feature-providers"></a>Anwendungsfeatureanbieter

Anwendungsfeatureanbieter untersuchen Anwendungsparts und bieten Features für diese. Es gibt integrierte Featureanbieter für die folgenden MVC-Features:

* [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [Metadatenverweis](/dotnet/api/microsoft.aspnetcore.mvc.razor.compilation.metadatareferencefeatureprovider)
* [Taghilfsprogramme](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [Ansichtskomponenten](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

Featureanbieter erben von `IApplicationFeatureProvider<T>`, wobei `T` der Typ des Features ist. Sie können Ihren eigenen Featureanbieter für alle oben aufgelisteten MVC-Featuretypen implementieren. Die Reihenfolge der Featureanbieter in der `ApplicationPartManager.FeatureProviders`-Sammlung kann wichtig sein, da Anbieter später auf Aktionen von vorherigen Anbietern reagieren können.

### <a name="sample-generic-controller-feature"></a>Beispiel: Generisches Controllerfeature

Standardmäßig ignoriert ASP.NET Core MVC generische Controller (z.B. `SomeController<T>`). Dieses Beispiel verwendet einen Controllerfeatureanbieter, der nach dem Standardanbieter ausgeführt wird und generische Controllerinstanzen für eine angegebene Liste von Typen (definiert in `EntityTypes.Types`) hinzufügt:

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericControllerFeatureProvider.cs?highlight=13&range=18-36)]

Die Entitätstypen:

[!code-csharp[](./app-parts/sample/AppPartsSample/Model/EntityTypes.cs?range=6-16)]

Der Featureanbieter wird in `Startup` hinzugefügt:

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(apm => 
        apm.FeatureProviders.Add(new GenericControllerFeatureProvider()));
```

Standardmäßig sind die Namen der generischen Controller für das Routing *GenericController'1 [Widget]* anstelle von *Widget*. Das folgende Attribut wird zu Änderung des Namens verwendet, damit dieser dem vom Controller verwendeten generischen Typ entspricht:

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericControllerNameConvention.cs)]

Die `GenericController`-Klasse:

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericController.cs?highlight=5-6)]

Das Ergebnis, wenn eine übereinstimmende Route angefordert wird:

![Beispielausgabe aus der Beispielanwendung: „Hello from a generic Sprocket controller.“](app-parts/_static/generic-controller.png)

### <a name="sample-display-available-features"></a>Beispiel: Anzeigen verfügbarer Features

Sie können die verfügbaren ausgefüllten Features Ihrer Anwendung durchlaufen, indem Sie durch eine [Dependency Injection](../../fundamentals/dependency-injection.md) einen `ApplicationPartManager` anfordern und ihn dazu verwenden, die Instanzen der entsprechenden Features aufzufüllen:

[!code-csharp[](./app-parts/sample/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

Beispielausgabe:

![Beispielausgabe aus der Beispielanwendung](app-parts/_static/available-features.png)

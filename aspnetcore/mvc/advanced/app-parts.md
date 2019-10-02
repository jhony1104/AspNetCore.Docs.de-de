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
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts-in-aspnet-core"></a>Freigeben von Controllern, Ansichten, Razor Pages und vielem mehr mit Anwendungsparts in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Ein *Anwendungspart* ist eine Abstraktion der Ressourcen einer App. Anwendungsparts ermöglichen es ASP.NET Core, Controller, Ansichtskomponenten, Taghilfsprogramme, Razor Pages, Razor-Kompilierungsquellen und vieles mehr zu ermitteln. [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) ist ein Anwendungspart. `AssemblyPart` kapselt einen Assemblyverweis und macht Typen und Kompilierungsverweise verfügbar.

*Featureanbieter* arbeiten mit Anwendungsparts, um die Features einer ASP.NET Core-Anwendung aufzufüllen. Anwendungsparts werden hauptsächlich dafür eingesetzt, eine App so zu konfigurieren, dass sie die ASP.NET Core-Features in einer Assembly ermittelt (oder das Laden solcher Features vermeidet). Ein Beispiel: Sie möchten einige gängige Funktionen in mehreren Apps gemeinsam nutzen. Mithilfe von Anwendungsparts können Sie eine Assembly (DLL) für mehrere Apps freigeben, die Controller, Ansichten, Razor Pages, Razor-Kompilierungsquellen, Taghilfsprogramme und vieles mehr enthält. Die Freigabe einer Assembly ist dem Duplizieren von Code in mehreren Projekten vorzuziehen.

ASP.NET Core-Apps laden Features aus <xref:System.Web.WebPages.ApplicationPart>. Die <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>-Klasse stellt ein Anwendungspart dar, das durch eine Assembly gesichert wird.

## <a name="load-aspnet-core-features"></a>Laden von ASP.NET Core-Features

Verwenden Sie die Klassen `ApplicationPart` und `AssemblyPart`, um ASP.NET Core-Features (Controller, Ansichtskomponenten usw.) zu ermitteln und zu laden. Der <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> verfolgt die verfügbaren Anwendungsparts und Featureanbieter nach. Der `ApplicationPartManager` wird in `Startup.ConfigureServices` konfiguriert:

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

Der folgende Code bietet einen alternativen Ansatz, um den `ApplicationPartManager` mithilfe von `AssemblyPart` zu konfigurieren:

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

Die beiden oben gezeigten Codebeispiele laden den `SharedController` aus einer Assembly. Der `SharedController` befindet sich nicht im Projekt der Anwendung. Weitere Informationen finden Sie im Beispieldownload für eine [WebAppParts-Lösung](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts).

### <a name="include-views"></a>Einschließen von Ansichten

So schließen Sie Ansichten in der Assembly ein:

* Fügen Sie der freigegebenen Projektdatei das folgende Markup hinzu:

  ```csproj
  <ItemGroup>
      <EmbeddedResource Include="Views\**\*.cshtml" />
  </ItemGroup>
  ```

* Fügen Sie den <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> zur <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine> hinzu:

  [!code-csharp[](./app-parts/sample1/WebAppParts/StartupViews.cs?name=snippet&highlight=3-7)]

### <a name="prevent-loading-resources"></a>Vermeiden des Ladens von Ressourcen

Anwendungsparts können verwendet werden, um das Laden von Ressourcen in einer bestimmten Assembly oder einem bestimmten Speicherort zu *vermeiden*. Fügen Sie der Sammlung <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> Elemente hinzu, oder entfernen Sie Elemente aus der Sammlung, um Ressourcen auszublenden oder verfügbar zu machen. Die Reihenfolge der Einträge in der `ApplicationParts`-Sammlung ist nicht wichtig. Konfigurieren Sie den `ApplicationPartManager`, bevor er zur Konfiguration von Diensten im Container verwendet wird. Beispielsweise sollten Sie den `ApplicationPartManager` vor dem Aufrufen von `AddControllersAsServices` konfigurieren. Rufen Sie `Remove` in der `ApplicationParts`-Sammlung auf, um eine Ressource zu entfernen.

Der folgende Code verwendet <xref:Microsoft.AspNetCore.Mvc.ApplicationParts>, um `MyDependentLibrary` aus der App zu entfernen: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]

Der `ApplicationPartManager` enthält Parts für Folgendes:

* Die Assembly der App und die abhängigen Assemblys.
* `Microsoft.AspNetCore.Mvc.TagHelpers`.
* `Microsoft.AspNetCore.Mvc.Razor`.

## <a name="application-feature-providers"></a>Anwendungsfeatureanbieter

Anwendungsfeatureanbieter untersuchen Anwendungsparts und bieten Features für diese. Es gibt integrierte Featureanbieter für die folgenden ASP.NET Core-Features:

* [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [Taghilfsprogramme](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [Ansichtskomponenten](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

Featureanbieter erben von <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, wobei `T` der Typ des Features ist. Featureanbieter können für jeden der oben aufgeführten Featuretypen implementiert werden. Die Reihenfolge der Featureanbieter in `ApplicationPartManager.FeatureProviders` kann sich auf das Laufzeitverhalten auswirken. Später hinzugefügte Anbieter können auf Aktionen reagieren, die von früher hinzugefügten Anbietern ausgeführt wurden.

### <a name="generic-controller-feature"></a>Generisches Controllerfeature

ASP.NET Core ignoriert [generische Controller](/dotnet/csharp/programming-guide/generics/generic-classes). Ein generischer Controller weist einen Typparameter auf (z. B. `MyController<T>`). Das folgende Beispiel fügt generische Controllerinstanzen für eine angegebene Liste von Typen hinzu:

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerFeatureProvider.cs?name=snippet)]

Diese Typen werden in `EntityTypes.Types` definiert:

[!code-csharp[](./app-parts/sample2/AppPartsSample/Models/EntityTypes.cs?name=snippet)]

Der Featureanbieter wird in `Startup` hinzugefügt:

[!code-csharp[](./app-parts/sample2/AppPartsSample/Startup.cs?name=snippet)]

Namen generischer Controller für das Routing weisen das Format *GenericController'1 [Widget]* anstelle von *Widget* auf. Das folgende Attribut ändert den Namen, damit dieser dem vom Controller verwendeten generischen Typ entspricht:

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerNameConvention.cs)]

Die `GenericController`-Klasse:

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericController.cs)]

Die Anforderung der URL `https://localhost:5001/Sprocket` beispielsweise führt zu folgender Antwort:

```text
Hello from a generic Sprocket controller.
```

### <a name="display-available-features"></a>Anzeigen verfügbarer Features

Die für eine App verfügbaren Features können aufgelistet werden, indem durch [Abhängigkeitsinjektion](../../fundamentals/dependency-injection.md) ein `ApplicationPartManager` angefordert wird:

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

Das [Downloadbeispiel](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) verwendet den oben stehenden Code, um die App-Features anzuzeigen:

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

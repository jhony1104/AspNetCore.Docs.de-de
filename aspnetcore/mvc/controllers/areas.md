---
title: Bereiche in ASP.NET Core
author: rick-anderson
description: Erfahren Sie mehr über Bereiche, ein Feature von ASP.NET MVC, das für die Organisation von verwandten Funktionalitäten in einer Gruppe als separater Namespace (für Routing) und Ordnerstruktur (für Ansichten) verwendet wird.
ms.author: riande
ms.date: 02/14/2017
uid: mvc/controllers/areas
ms.openlocfilehash: 19e818fa198936ea1bee0da8039e88a3c0abbf6b
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410611"
---
# <a name="areas-in-aspnet-core"></a>Bereiche in ASP.NET Core

Von [Dhananjay Kumar](https://twitter.com/debug_mode) und [Rick Anderson](https://twitter.com/RickAndMSFT)

Bereiche sind ein Feature von ASP.NET MVC, das für die Organisation von verwandten Funktionalitäten in eine Gruppe als separater Namespace (für Routing) und Ordnerstruktur (für Ansichten) verwendet wird. Mithilfe von Bereichen wird eine Hierarchie erstellt, damit das Routing durch Hinzufügen eines anderen Routenparameters ausgeführt werden kann: `area` zu `controller` und `action`.

Bereiche sind eine Möglichkeit, eine große ASP.NET Core MVC-Web-App in kleinere funktionale Gruppierungen aufzuteilen. Ein Bereich ist im Grunde genommen eine MVC-Struktur in einer Anwendung. In einem MVC-Projekt sind logische Komponenten wie Modell, Controller und Ansicht in verschiedenen Ordnern gespeichert. MVC nutzt Namenskonventionen zum Erstellen einer Beziehung zwischen diesen Komponenten. Bei einer großen App kann es von Vorteil sein, die App in mehrere Bereiche auf hoher Funktionalitätsebene zu unterteilen, Dies gilt z.B. für eine E-Commerce-App mit mehreren Geschäftseinheiten, wie Auftragsabschluss, Abrechnung und Suche usw. Jede dieser Einheiten hat ihre eigenen logischen Komponentenansichten, Controller und Modelle. In diesem Szenario können Sie Bereiche verwenden, um die Geschäftskomponenten im selben Projekt physisch zu partitionieren.

Ein Bereich kann als kleinere funktionale Einheiten mit eigenen Controllern, Ansichten und Modellen in einem ASP.NET Core MVC-Projekt definiert werden.

Die Verwendung von Bereichen in einem MVC-Projekt ist erwägenswert, wenn:

* Ihre Anwendung aus mehreren funktionalen Komponenten auf hoher Ebene besteht, die logisch getrennt sein sollten

* Sie Ihr MVC-Projekt partitionieren möchten, damit jeder funktionale Bereich unabhängig voneinander bearbeitet werden kann

Features von Bereichen:

* Eine ASP.NET Core MVC-App kann über eine beliebige Anzahl von Bereichen verfügen.

* Jeder Bereich verfügt über seine eigenen Controller, Modelle und Ansichten.

* Die Bereiche ermöglichen Ihnen das Organisieren großer MVC-Projekte in mehrere allgemeine Komponenten, die unabhängig voneinander bearbeitet werden können.

* Die Bereiche unterstützen mehrere Controller mit demselben Namen, solange diese über verschiedene *Bereiche* verfügen.

Im Folgenden finden Sie ein Beispiel, das darstellt, wie Bereiche erstellt und verwendet werden. Angenommen, Sie besitzen eine Store-App mit zwei unterschiedlichen Gruppierungen von Controllern und Ansichten: Produkte und Dienste.  Eine übliche Ordnerstruktur hierfür, die MVC-Bereiche verwendet, sieht folgendermaßen aus:

* Projektname
  * Bereiche
    * Products
      * Controller
        * HomeController.cs
        * ManageController.cs
      * Ansichten
        * Startseite
          * Index.cshtml
        * Verwalten
          * Index.cshtml
    * Dienste
      * Controller
        * HomeController.cs
      * Ansichten
        * Startseite
          * Index.cshtml

Wenn MVC versucht, eine Ansicht in einem Bereich zu rendern, wird standardgemäß in den folgenden Speicherorten gesucht:

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
   /Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
   /Views/Shared/<Action-Name>.cshtml
   ```

Dies sind die Standardspeicherorte, die mithilfe von `AreaViewLocationFormats` unter `Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions` geändert werden können.

Im unten gezeigten Code wurde der Ordner z.B. von „Areas“ in „Categories“ umbenannt.

```csharp
services.Configure<RazorViewEngineOptions>(options =>
   {
       options.AreaViewLocationFormats.Clear();
       options.AreaViewLocationFormats.Add("/Categories/{2}/Views/{1}/{0}.cshtml");
       options.AreaViewLocationFormats.Add("/Categories/{2}/Views/Shared/{0}.cshtml");
       options.AreaViewLocationFormats.Add("/Views/Shared/{0}.cshtml");
   });
   ```

Beachten Sie, dass nur die Struktur des Ordners *Ansichten* in diesem Fall wichtig ist, und dass die Inhalte der restlichen Ordner, wie *Controller* und *Modelle*, **nicht** wichtig sind. Sie benötigen z.B. gar keine Ordner für *Controller* und *Modelle*. Dies funktioniert, weil der Inhalt von *Controller* und *Modelle* nur aus Code besteht, der in eine DLL-Datei kompiliert wird, der Inhalt von *Ansichten* jedoch erst kompiliert wird, wenn die Ansicht angefordert wurde.

Sobald Sie eine Ordnerhierarchie definiert haben, müssen Sie MVC mitteilen, dass jeder Controller einem Bereich zugeordnet ist. Dazu versehen Sie den Controllernamen mit dem Attribut `[Area]`.

```csharp
...
   namespace MyStore.Areas.Products.Controllers
   {
       [Area("Products")]
       public class HomeController : Controller
       {
           // GET: /Products/Home/Index
           public IActionResult Index()
           {
               return View();
           }

           // GET: /Products/Home/Create
           public IActionResult Create()
           {
               return View();
           }
       }
   }
   ```

Richten Sie eine Routendefinition ein, die mit Ihren neu erstellten Bereichen funktioniert. Der Artikel [Routing to Controller Actions (Routing zu Controlleraktionen)](routing.md) enthält weitere Details dazu, wie Sie Routendefinitionen erstellen, darunter die Verwendung konventioneller Routen im Vergleich zu Attributrouten. In diesem Beispiel wird eine konventionelle Route verwendet. Öffnen Sie hierzu die Datei *Startup.cs* und ändern Sie sie, indem Sie die unten genannte Routendefinition `areaRoute` hinzufügen.

```csharp
...
   app.UseMvc(routes =>
   {
     routes.MapRoute(
         name: "areaRoute",
         template: "{area:exists}/{controller=Home}/{action=Index}/{id?}");

     routes.MapRoute(
         name: "default",
         template: "{controller=Home}/{action=Index}/{id?}");
   });
   ```

Beim Navigieren zu `http://<yourApp>/products` wird die Aktionsmethode `Index` von der `HomeController`-Klasse im Bereich`Products` aufgerufen.

## <a name="link-generation"></a>Erstellen von Links

* Erstellen von Links von einer Aktion in einem bereichsbasierten Controller zu einer anderen Aktion im selben Controller

  Angenommen, der Pfad der aktuellen Anforderung ähnelt `/Products/Home/Create`.

  HtmlHelper-Syntax: `@Html.ActionLink("Go to Product's Home Page", "Index")`

  TagHelper-Syntax: `<a asp-action="Index">Go to Product's Home Page</a>`

  Beachten Sie, dass die Werte für „area“ und „controller“ hier nicht angegeben sind, weil sie bereits über den Kontext der aktuellen Anforderung verfügbar sind. Diese Werte werden als `ambient`-Werte bezeichnet.

* Erstellen von Links von einer Aktion in einem bereichsbasierten Controller zu einer anderen Aktion auf einem anderen Controller

  Angenommen, der Pfad der aktuellen Anforderung ähnelt `/Products/Home/Create`.

  HtmlHelper-Syntax: `@Html.ActionLink("Go to Manage Products Home Page", "Index", "Manage")`

  TagHelper-Syntax: `<a asp-controller="Manage" asp-action="Index">Go to Manage Products Home Page</a>`

  Beachten Sie, dass der ambient-Wert für „area“ verwendet wird, der Wert für „controller“ wird jedoch explizit oben angegeben.

* Erstellen von Links von einer Aktion in einem bereichsbasierten Controller zu einer anderen Aktion auf einem anderen Controller und einem anderen Bereich

  Angenommen, der Pfad der aktuellen Anforderung ähnelt `/Products/Home/Create`.

  HtmlHelper-Syntax: `@Html.ActionLink("Go to Services Home Page", "Index", "Home", new { area = "Services" })`

  TagHelper-Syntax: `<a asp-area="Services" asp-controller="Home" asp-action="Index">Go to Services Home Page</a>`

  Beachten Sie, dass hier keine ambient-Werte verwendet werden.

* Erstellen von Links von einer Aktion in einem bereichsbasierten Controller zu einer anderen Aktion auf einem anderen Controller und **nicht** in einem Bereich.

  HtmlHelper-Syntax: `@Html.ActionLink("Go to Manage Products  Home Page", "Index", "Home", new { area = "" })`

  TagHelper-Syntax: `<a asp-area="" asp-controller="Manage" asp-action="Index">Go to Manage Products Home Page</a>`

  Da Links zu einer nicht bereichsbasierten Controlleraktion erstellt werden sollen, wird der ambient-Wert für „area“ hier entfernt.

## <a name="publishing-areas"></a>Veröffentlichen von Bereichen

Alle `*.cshtml`- und `wwwroot/**`-Dateien werden für die Ausgabe veröffentlicht, wenn `<Project Sdk="Microsoft.NET.Sdk.Web">` in der *CSPROJ*-Datei enthalten ist.

---
title: Bereiche in ASP.NET Core
author: rick-anderson
description: Erfahren Sie mehr über Bereiche, ein Feature von ASP.NET MVC, das für die Organisation von verwandten Funktionalitäten in einer Gruppe als separater Namespace (für Routing) und Ordnerstruktur (für Ansichten) verwendet wird.
ms.author: riande
ms.date: 12/05/2019
uid: mvc/controllers/areas
ms.openlocfilehash: 7e02a21361e0e2148b29a3ae0f1ba25e68239e13
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74881117"
---
# <a name="areas-in-aspnet-core"></a>Bereiche in ASP.NET Core

Von [Dhananjay Kumar](https://twitter.com/debug_mode) und [Rick Anderson](https://twitter.com/RickAndMSFT)

Bereiche sind ein Feature von ASP.NET, das für die Organisation von verwandten Funktionalitäten in eine Gruppe als separater Namespace (für Routing) und Ordnerstruktur (für Ansichten) verwendet wird. Mithilfe von Bereichen wird außerdem eine Hierarchie erstellt, damit das Routing durch Hinzufügen eines anderen Routenparameters ausgeführt werden kann: `area` zu `controller` und `action` oder einer Razor Page `page`.

Bereiche ermöglichen es, eine ASP.NET Core-Web-App in kleinere funktionale Gruppen zu partitionieren. Jede dieser Gruppen hat dabei ihre eigene Menge an Razor Pages, Controller, Ansichten und Modellen. Ein Bereich ist im Grunde genommen eine Struktur in einer App. In einem ASP.NET Core-Webprojekt werden logische Komponenten wie Seiten, Modelle, Controller und Ansichten in verschiedenen Ordner aufbewahrt. Die ASP.NET Core-Runtime verwendet Namenskonventionen, um die Beziehung zwischen diesen Komponenten zu erstellen. Bei einer großen App kann es von Vorteil sein, die App in mehrere Bereiche mit hoher Funktionalität aufzuteilen. Dies gilt z.B. für eine E-Commerce-App mit mehreren Geschäftseinheiten, wie Auftragsabschluss, Abrechnung und Suche. Jede dieser Einheiten hat ihre eigenen Bereiche, die Ansichten, Controller, Razor Pages und Modelle enthalten.

Die Verwendung von Bereichen in einem Projekt ist erwägenswert, wenn:

* Ihre App aus mehreren funktionalen Komponenten auf hoher Ebene besteht, die logisch getrennt sein können.
* Sie Ihre App partitionieren möchten, damit jeder funktionale Bereich unabhängig voneinander bearbeitet werden kann.

[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)). Das Downloadbeispiel stellt eine einfache App für Testbereiche zur Verfügung.

Wenn Sie Razor-Seiten verwenden, lesen Sie [Bereiche mit Razor-Seiten](#areas-with-razor-pages) in diesem Dokument.

## <a name="areas-for-controllers-with-views"></a>Bereiche für Controller mit Ansichten

Eine typische ASP.NET Core-Web-App, die Bereiche, Controller und Ansichten verwendet, beinhaltet Folgendes:

* Eine [Bereichsordnerstruktur](#area-folder-structure).
* Controller werden mit dem Attribut [`[Area]`](#attribute) versehen, um den Controller mit dem Bereich zu verknüpfen:

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* Die [Bereichsroute, die dem Startup hinzugefügt wurde](#add-area-route):

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a>Bereichsordnerstruktur

Stellen Sie sich eine App vor, die zwei logische Gruppen hat, *Produkte* und *Dienste*. Wenn Bereiche verwendet werden, würde die Ordnerstruktur ähnlich dem Folgenden aussehen:

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
          * About.cshtml
    * Dienste
      * Controller
        * HomeController.cs
      * Ansichten
        * Startseite
          * Index.cshtml

Während das vorherige Layout typisch ist, wenn Bereiche verwendet werden, müssen nur die Ansichtsdateien diese Ordnerstruktur verwenden. Die Ansichtsermittlung sucht nach einer passenden Bereichsansichtsdatei im folgenden Ordner:

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a>Zuordnen eines Controllers zu einem Bereich

Bereichscontroller werden mit dem Attribut [&lbrack;Bereich&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) versehen:

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a>Hinzufügen einer Bereichsroute

Bereichsrouten verwenden normalerweise konventionelles Routing anstatt Attributrouting. Beim herkömmlichen Routing ist die Reihenfolge wichtig. Routen mit Bereichen werden im Allgemeinen früher in der Routentabelle aufgeführt als die spezifischeren Routen ohne Bereich.

`{area:...}` kann als Token in Routenvorlagen verwendet werden, wenn der URL-Raum in allen Bereichen einheitlich ist:

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

Im vorherigen Code wendet `exists` eine Einschränkung an: Die Route muss mit einem Bereich übereinstimmen. `{area:...}` zu verwenden ist die unkomplizierteste Methode, um Bereichen Routing hinzuzufügen.

Im folgenden Code wird <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> verwendet, um zwei benannte Bereichsrouten zu erstellen:

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

Wenn `MapAreaRoute` mit ASP.NET Core 2.2 verwendet werden soll, sehen Sie sich diesen [GitHub-Artikel](https://github.com/aspnet/AspNetCore/issues/7772) an.

Weitere Informationen finden Sie im Artikel [Routing zu Controlleraktionen in ASP.NET Core](xref:mvc/controllers/routing#areas).

### <a name="link-generation-with-mvc-areas"></a>Erstellen von Links mit MVC-Bereichen

Im folgenden Code aus dem [Beispieldownload](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) sehen Sie die Erstellung eines Links mit dem angegebenen Bereich:

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

Links, die mit dem vorherigen Code erstellt wurden, gelten überall in der App.

Im Beispieldownload ist eine [partielle Ansicht](xref:mvc/views/partial) enthalten, die die vorherigen Links und dieselben Links beinhaltet, ohne dass der Bereich angegeben wird. In der [Layoutdatei](xref:mvc/views/layout) wird auf die partielle Ansicht verwiesen. Jede Seite in der App stellt also die erstellen Links dar. Die Links, die ohne Angabe eines Bereichs erstellt werden, sind nur gültig, wenn auf sie von einer Seite im selben Bereich und Controller verwiesen wird.

Wenn der Bereich oder der Kontroller nicht angegeben werden, hängt das Routing von den *Umgebungswerten* ab. Die aktuellen Routenwerte der aktuellen Anforderung werden bei der Linkgenerierung als Umgebungswerte behandelt. Oft werden ungültige Links erstellt, wenn für die Beispiel-App die Umgebungswerte verwendet werden.

Weitere Informationen finden Sie unter [Routing zu Controlleraktionen in ASP.NET Core](xref:mvc/controllers/routing).

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a>Freigegebenes Layout für Bereiche unter Verwendung der _ViewStart.cshtml-Datei

Um ein gemeinsames Layout für die gesamte App freizugeben, verschieben Sie *_ViewStart.cshtml* in den Stammordner der Anwendung.

### <a name="_viewimportscshtml"></a>_ViewImports.cshtml

An ihrem Standardspeicherort gilt die Datei */Views/_ViewImports.cshtml* nicht für Bereiche. Um allgemeine [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro), `@using` oder `@inject` in Ihrem Bereich zu verwenden, stellen Sie sicher, dass eine ordnungsgemäße Datei *_ViewImports.cshtml* [für Ihre Bereichsansichten gilt](xref:mvc/views/layout#importing-shared-directives). Wenn Sie das gleiche Verhalten in allen Ansichten wünschen, verschieben Sie */Views/_ViewImports.cshtml* in den Anwendungsstamm.

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a>Ändern des Standardbereichsordners, in dem Ansichten gespeichert sind

Der folgende Code ändert den Standardbereichsordner von `"Areas"` in `"MyAreas"`:

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a>Bereiche mit Razor-Seiten

Bereiche mit Razor-Seiten erfordern einen Ordner *Areas/<area name>/Pages* im Stammverzeichnis der App. Die folgende Ordnerstruktur wird mit dem [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) verwendet.

* Projektname
  * Bereiche
    * Products
      * Seiten
        * _ViewImports
        * Info
        * Index
    * Dienste
      * Seiten
        * Verwalten
          * Info
          * Index

### <a name="link-generation-with-razor-pages-and-areas"></a>Erstellen von Links mit Razor-Seiten und Bereichen

Im folgenden Code aus dem [Beispieldownload](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) sehen Sie die Erstellung eines Links mit dem angegebenen Bereich (z.B. `asp-area="Products"`):

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

Links, die mit dem vorherigen Code erstellt wurden, gelten überall in der App.

Im Beispieldownload ist eine [partielle Ansicht](xref:mvc/views/partial) enthalten, die die vorherigen Links und dieselben Links beinhaltet, ohne dass der Bereich angegeben wird. In der [Layoutdatei](xref:mvc/views/layout) wird auf die partielle Ansicht verwiesen. Jede Seite in der App stellt also die erstellen Links dar. Die Links, die ohne Angabe eines Bereichs erstellt werden, sind nur gültig, wenn auf sie von einer Seite im selben Bereich verwiesen wird.

Wenn der Bereich nicht angegeben wird, hängt das Routing von den *Umgebungswerten* ab. Die aktuellen Routenwerte der aktuellen Anforderung werden bei der Linkgenerierung als Umgebungswerte behandelt. Oft werden ungültige Links erstellt, wenn für die Beispiel-App die Umgebungswerte verwendet werden. Betrachten Sie hierzu die aus dem folgenden Code generierten Links:

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

Für den Code oben gilt:

* Der aus `<a asp-page="/Manage/About">` generierte Link stimmt nur, wenn die letzte Anforderung für eine Seite im `Services`-Bereich erfolgte. Beispielsweise `/Services/Manage/`, `/Services/Manage/Index` oder `/Services/Manage/About`.
* Der aus `<a asp-page="/About">` generierte Link stimmt nur, wenn die letzte Anforderung für eine Seite in `/Home` erfolgte.
* Der Code stammt aus dem [Beispieldownload](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a>Importieren von Namespace und Taghilfsprogrammen mit der _ViewImports-Datei

Eine *_ViewImports.cshtml*-Datei kann dem *Pages*-Ordner jedes Bereichs hinzugefügt werden, um Namespace und Taghilfsprogramme für jede Razor-Seite im Ordner zu importieren.

Betrachten Sie den Bereich *Services* des Beispielcodes, der keine *_ViewImports.cshtml*-Datei enthält. Das folgende Markup zeigt die Razor-Seite */Services/Manage/About*:

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

Im obenstehenden Markup:

* Der vollqualifizierte Domänenname muss verwendet werden, um das Modell anzugeben (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).
* [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) werden durch `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` aktiviert.

Im Beispieldownload enthält der Bereich „Products“ die folgende *_ViewImports.cshtml*-Datei:

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

Das folgende Markup zeigt die Razor-Seite */Products/About*:

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

In der vorherigen Datei werden Namespace und `@addTagHelper`-Anweisung von der Datei *Areas/Products/Pages/_ViewImports.cshtml* in die Datei importiert.

Weitere Informationen finden Sie unter [Verwalten des Taghilfsprogrammbereichs](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) und [Importieren gemeinsam verwendeter Anweisungen](xref:mvc/views/layout#importing-shared-directives).

### <a name="shared-layout-for-razor-pages-areas"></a>Gemeinsam genutztes Layout für Razor-Seiten-Bereiche

Um ein gemeinsames Layout für die gesamte App freizugeben, verschieben Sie *_ViewStart.cshtml* in den Stammordner der Anwendung.

### <a name="publishing-areas"></a>Veröffentlichen von Bereichen

Alle *.cshtml-Dateien und Dateien im *wwwroot*-Verzeichnis werden in der Ausgabe veröffentlicht, wenn `<Project Sdk="Microsoft.NET.Sdk.Web">` in der *.csproj-Datei enthalten ist.

---
title: Routing zu Controlleraktionen in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie ASP.NET Core MVC Routingmiddleware verwendet, um die URLs der eingehenden Anforderungen abzugleichen und sie Aktionen zuzuordnen.
ms.author: riande
ms.date: 3/25/2020
uid: mvc/controllers/routing
ms.openlocfilehash: 9f7a26a482cb115697a0a3d7439c14a062677c92
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277131"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a>Routing zu Controlleraktionen in ASP.NET Core

Von [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5)und [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core-Controller verwenden die [Routing-Middleware,](xref:fundamentals/middleware/index) um die URLs eingehender Anforderungen abzugleichen und [sie Aktionen](#action)zuzuordnen.  Routenvorlagen:

* Werden im Startcode oder in Attributen definiert.
* Beschreiben, wie URL-Pfade [mit Aktionen](#action)abgeglichen werden .
* Werden verwendet, um URLs für Links zu generieren. Die generierten Verknüpfungen werden in der Regel in Antworten zurückgegeben.

Aktionen werden entweder [konventionell oder](#cr) [attributgeroutet.](#ar) Wenn Sie eine Route auf dem Controller oder der [Aktion](#action) platzieren, wird sie mit Attributrouten versehen. Weitere Informationen finden Sie im Abschnitt [Gemischtes Routing](#routing-mixed-ref-label).

Dieses Dokument:

* Erläutert die Interaktionen zwischen MVC und Routing:
  * Wie typische MVC-Apps Routingfunktionen nutzen.
  * Umfasst beide:
    * [Konventionell Routing in](#cr) der Regel mit Controllern und Ansichten verwendet.
    * *Attributrouting,* das mit REST-APIs verwendet wird. Wenn Sie hauptsächlich am Routing für REST-APIs interessiert sind, wechseln Sie zum Abschnitt [Attributrouting für REST-APIs.](#ar)
  * Weitere Routingdetails finden Sie unter [Routing.](xref:fundamentals/routing)
* Bezieht sich auf das Standardroutingsystem, das in ASP.NET Core 3.0 hinzugefügt wurde, dem so genannten Endpunktrouting. Es ist möglich, Controller mit der vorherigen Version des Routings aus Kompatibilitätsgründen zu verwenden. Anweisungen finden Sie im [Migrationsleitfaden 2.2-3.0.](xref:migration/22-to-30) Referenzmaterial auf dem Legacy-Routingsystem finden Sie in der [Version 2.2 dieses Dokuments.](xref:mvc/controllers/routing?view=aspnetcore-2.2)

<a name="cr"></a>

## <a name="set-up-conventional-route"></a>Einrichten einer konventionellen Route

`Startup.Configure`In der Regel hat Code ähnlich dem folgenden, wenn [herkömmliches Routing](#crd)verwendet wird:

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

Innerhalb des <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>Aufrufs von <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> wird verwendet, um eine einzelne Route zu erstellen. Die einzelne Route `default` wird Route genannt. Die meisten Apps mit Controllern und `default` Ansichten verwenden eine Routenvorlage, die der Route ähnelt. REST-APIs sollten [Attributrouting](#ar)verwenden.

Die Routenvorlage `"{controller=Home}/{action=Index}/{id?}"`:

* Entspricht einem URL-Pfad wie`/Products/Details/5`
* Extrahiert die Routenwerte, `{ controller = Products, action = Details, id = 5 }` indem der Pfad tokenisiert wird. Die Extraktion von Routenwerten führt zu einer Übereinstimmung, wenn die App einen Controller nament `ProductsController` und eine `Details` Aktion hat:

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* `/Products/Details/5`Modell bindet den `id = 5` Wert von `id` , `5`um den Parameter auf festzulegen. Weitere Informationen finden Sie unter [Modellbindung.](xref:mvc/models/model-binding)
* `{controller=Home}`definiert `Home` als Standard `controller`.
* `{action=Index}`definiert `Index` als Standard `action`.
*  Das `?` Zeichen `{id?}` in `id` definiert als optional.
  * Standardmäßige und optionale Routenparameter müssen nicht im URL-Pfad vorhanden sein, damit es eine Übereinstimmung gibt. Eine ausführliche Beschreibung der Syntax der Routenvorlage finden Sie unter [Routenvorlagenreferenz](xref:fundamentals/routing#route-template-reference).
* Entspricht dem `/`URL-Pfad .
* Erzeugt die `{ controller = Home, action = Index }`Arbeitsplanwerte .

Die Werte `controller` `action` für und die Verwendung der Standardwerte. `id`erzeugt keinen Wert, da es kein entsprechendes Segment im URL-Pfad gibt. `/`entspricht nur, wenn `HomeController` `Index` eine Aktion vorhanden ist:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Mithilfe der vorherigen Controllerdefinition und `HomeController.Index` Routenvorlage wird die Aktion für die folgenden URL-Pfade ausgeführt:

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

Der URL-Pfad `/` verwendet `Home` die `Index` Standardcontroller und die Aktion der Routenvorlage. Der URL-Pfad `/Home` verwendet `Index` die Standardaktion der Routenvorlage.

Mit der Hilfsmethode <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:

```csharp
endpoints.MapDefaultControllerRoute();
```

Ersetzt:

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> Routing wird mit <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> der <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> und Middleware konfiguriert. So verwenden Sie Controller:
>
> * Rufen <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> `UseEndpoints` Sie innerhalb, um [Attribut routende](#ar) Controller zuzuordnen.
> * Rufen <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>Sie oder , um [konventionell geroutete](#cr) Controller zu kartieren.

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a>Herkömmliches Routing

Herkömmliches Routing wird mit Controllern und Ansichten verwendet. Die `default`-Route:

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

ist ein Beispiel für *herkömmliches Routing*. Es wird *als konventionelles Routing* bezeichnet, da es eine *Konvention* für URL-Pfade festlegt:

* Das erste Pfadsegment, `{controller=Home}`, wird dem Controllernamen zugeordnet.
* Das zweite `{action=Index}`Segment, , wird dem [Aktionsnamen](#action) zugeordnet.
* Das dritte `{id?}` Segment wird für `id`eine optionale verwendet. Das `?` `{id?}` in macht es optional. `id`wird verwendet, um einer Modellentität zuzuordnen.

Mit `default` dieser Route wird der URL-Pfad:

* `/Products/List`karten der `ProductsController.List` Aktion.
* `/Blog/Article/17`Zuordnungen `BlogController.Article` zu und in `id` der Regel Modell bindet den Parameter an 17.

Diese Zuordnung:

* Basiert nur auf dem Controller und [den Aktionsnamen](#action) **.**
* Basiert nicht auf Namespaces, Quelldateispeicherorten oder Methodenparametern.

Die Verwendung des herkömmlichen Routings mit der Standardroute ermöglicht das Erstellen der App, ohne für jede Aktion ein neues URL-Muster erstellen zu müssen. Für eine App mit AKTIONEN im [CRUD-Stil](https://wikipedia.org/wiki/Create,_read,_update_and_delete) mit Konsistenz für die URLs über Controller hinweg:

* Vereinfacht den Code.
* Macht die Benutzeroberfläche vorhersehbarer.

> [!WARNING]
> Der `id` im vorherigen Code wird von der Routenvorlage als optional definiert. Aktionen können ohne die optionale ID ausgeführt werden, die als Teil der URL angegeben ist. Im Allgemeinen, wenn`id` in der URL weggelassen wird:
>
> * `id`wird auf `0` durch Modellbindung festgelegt.
> * In der Datenbank wurde `id == 0`keine Entität gefunden, die abstimmt.
>
> [Das Attributrouting](#ar) bietet eine detaillierte Steuerung, um die für einige Aktionen erforderliche ID und nicht für andere zu erstellen. Gemäß der Konvention enthält die `id` Dokumentation optionale Parameter, z. B. wenn sie wahrscheinlich in korrekter Verwendung angezeigt werden.

Für die meisten Apps sollte eine grundlegendes und beschreibendes Routingschema ausgewählt werden, um lesbare und aussagekräftige URLs zu erhalten. Für die konventionelle Standardroute `{controller=Home}/{action=Index}/{id?}` gilt:

* Sie unterstützt ein grundlegendes und beschreibendes Routingschema.
* Sie stellt einen nützlichen Startpunkt für benutzeroberflächenbasierte Apps dar.
* Die einzige Routenvorlage, die für viele Web-UI-Apps erforderlich ist. Bei größeren Web-UI-Apps eine weitere Route, die [Bereiche](#areas) verwendet, wenn häufig alles benötigt wird.

<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>und <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :

* Weisen Sie ihren Endpunkten automatisch einen **Auftragswert** zu, basierend auf der Reihenfolge, in der sie aufgerufen werden.

Endpunktrouting in ASP.NET Core 3.0 und höher:

* Hat kein Konzept von Routen.
* Bietet keine Bestellgarantien für die Ausführung der Erweiterbarkeit, alle Endpunkte werden auf einmal verarbeitet.

Wenn Sie die [Protokollierung](xref:fundamentals/logging/index) aktivieren, erfahren Sie, wie die integrierten Routingimplementierungen (z.B. <xref:Microsoft.AspNetCore.Routing.Route>) Zuordnungen für Anforderungen ermitteln.

[Das Attributrouting](#ar) wird weiter unten in diesem Dokument erläutert.

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a>Mehrere konventionelle Routen

Mehrere [konventionelle Routen](#cr) können `UseEndpoints` im Inneren <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> hinzugefügt <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>werden, indem weitere Aufrufe zu und hinzugefügt werden. Auf diese Weise können mehrere Konventionen definiert oder herkömmliche Routen hinzugefügt werden, die einer bestimmten [Aktion](#action)gewidmet sind, z. B.:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

Die `blog` Route im vorherigen Code ist eine **dedizierte konventionelle Route**. Es wird eine dedizierte konventionelle Route genannt, weil:

* Es verwendet [konventionelles Routing](#cr).
* Es ist einer bestimmten [Aktion](#action)gewidmet.

Da `controller` `action` und nicht in der `"blog/{*article}"` Routenvorlage als Parameter angezeigt:

* Sie können nur die `{ controller = "Blog", action = "Article" }`Standardwerte haben.
* Diese Route wird immer `BlogController.Article`der Aktion zugeordnet.

`/Blog`, `/Blog/Article`und `/Blog/{any-string}` sind die einzigen URL-Pfade, die mit der Blogroute übereinstimmen.

Das vorangegangene Beispiel:

* `blog`Route hat eine höhere Priorität `default` für Übereinstimmungen als die Route, da sie zuerst hinzugefügt wird.
* Ist und [Slug](https://developer.mozilla.org/docs/Glossary/Slug) Beispiel für Slug-Stilrouting, bei dem es typisch ist, einen Artikelnamen als Teil der URL zu haben.

> [!WARNING]
> In ASP.NET Core 3.0 und höher funktioniert routing nicht:
> * Definieren Sie ein Konzept, das als *Route*bezeichnet wird. `UseRouting`fügt der Middleware-Pipeline den Routenabgleich hinzu. Die `UseRouting` Middleware betrachtet den in der App definierten Satz von Endpunkten und wählt basierend auf der Anforderung die beste Endpunktübereinstimmung aus.
> * Stellen Sie Garantien für die Ausführungsreihenfolge der Erweiterbarkeit wie <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> oder <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>bereit.
>
>Siehe [Routing](xref:fundamentals/routing) für Referenzmaterial zum Routing.

<a name="cro"></a>

### <a name="conventional-routing-order"></a>Herkömmlicher Routingauftrag

Herkömmliches Routing entspricht nur einer Kombination aus Aktion und Controller, die von der App definiert werden. Damit sollen Fälle vereinfacht werden, in denen sich herkömmliche Routen überschneiden.
Hinzufügen von <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>Routen <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> mithilfe von , und automatischeZuweisung eines Auftragswerts zu ihren Endpunkten basierend auf der Reihenfolge, in der sie aufgerufen werden. Übereinstimmungen von einer Route, die früher angezeigt wurde, haben eine höhere Priorität. Beim herkömmlichen Routing ist die Reihenfolge wichtig. Im Allgemeinen sollten Routen mit Flächen früher platziert werden, da sie spezifischer sind als Routen ohne Fläche. [Dedizierte konventionelle Routen](#dcr) mit catch `{*article}` alle Routenparameter wie kann eine Route zu [gierig](xref:fundamentals/routing#greedy)machen, was bedeutet, dass es URLs entspricht, die Sie mit anderen Routen übereinstimmen wollten. Setzen Sie die gierigen Routen später in die Routentabelle, um gierige Übereinstimmungen zu verhindern.

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a>Auflösen mehrdeutiger Aktionen

Wenn zwei Endpunkte über das Routing übereinstimmen, muss das Routing eine der folgenden Schritte ausführen:

* Wählen Sie den besten Kandidaten.
* Löst eine Ausnahme aus.

Beispiel:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

Der vorherige Controller definiert zwei Aktionen, die übereinstimmen:

* Der URL-Pfad`/Products33/Edit/17`
* Routendaten `{ controller = Products33, action = Edit, id = 17 }`.

Dies ist ein typisches Muster für MVC-Controller:

* `Edit(int)`zeigt ein Formular zum Bearbeiten eines Produkts an.
* `Edit(int, Product)`verarbeitet das gebuchte Formular.

So lösen Sie die richtige Route auf:

* `Edit(int, Product)`wird ausgewählt, wenn es `POST`sich bei der Anforderung um ein HTTP handelt.
* `Edit(int)`wird ausgewählt, wenn das [HTTP-Verb](#verb) etwas anderes ist. `Edit(int)`wird in `GET`der Regel über aufgerufen.

Die <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> `[HttpPost]`, wird dem Routing zur Verfügung gestellt, sodass sie basierend auf der HTTP-Methode der Anforderung auswählen kann. Der `HttpPostAttribute` `Edit(int, Product)` macht eine `Edit(int)`bessere Übereinstimmung als .

Es ist wichtig, die Rolle von `HttpPostAttribute`Attributen wie zu verstehen. Ähnliche Attribute sind für andere [HTTP-Verben](#verb)definiert. Im [herkömmlichen Routing](#cr)ist es üblich, dass Aktionen denselben Aktionsnamen verwenden, wenn sie Teil eines Showformulars sind, um formularworkflow zu senden. Siehe z. [B. Untersuchen der beiden Edit-Aktionsmethoden](xref:tutorials/first-mvc-app/controller-methods-views#get-post).

Wenn Routing keinen besten Kandidaten auswählen <xref:System.Reflection.AmbiguousMatchException> kann, wird ein ausgelöst, in dem die mehreren übereinstimmenden Endpunkte aufgelistet werden.

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a>Konventionelle Routennamen

Die `"blog"` Zeichenfolgen und `"default"` in den folgenden Beispielen sind herkömmliche Routennamen:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Die Routennamen geben der Route einen logischen Namen. Die benannte Route kann für die URL-Generierung verwendet werden. Die Verwendung einer benannten Route vereinfacht die URL-Erstellung, wenn die Reihenfolge von Routen die URL-Generierung erschweren könnte. Routennamen müssen eindeutige Anwendungsbreite sein.

Routennamen:

* Haben keine Auswirkungen auf den URL-Abgleich oder die Behandlung von Anforderungen.
* Werden nur für die URL-Generierung verwendet.

Das Routennamenkonzept wird im Routing als [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata)dargestellt. Die Begriffe **Routenname** und **Endpunktname**:

* Sind austauschbar.
* Welche in Dokumentation und Code verwendet wird, hängt von der beschriebenen API ab.

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a>Attributrouting für REST-APIs

REST-APIs sollten Attributrouting verwenden, um die Funktionalität der App als eine Gruppe von Ressourcen zu modellieren, in denen Vorgänge durch [HTTP-Verben](#verb)dargestellt werden.

Beim Attributrouting werden Aktionen mithilfe von Attributen direkt Routenvorlagen zugeordnet. Der `StartUp.Configure` folgende Code ist typisch für eine REST-API und wird im nächsten Beispiel verwendet:

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

Im vorherigen Code <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> wird `UseEndpoints` innerhalb aufgerufen, um Attribut routenierte Controller zuzuordnen.

Siehe folgendes Beispiel:

* Die `Configure` vorangehende Methode wird verwendet.
* `HomeController`entspricht einer Reihe von URLs, die `{controller=Home}/{action=Index}/{id?}` der Standardroute für konventionelle Routen ähneln.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

Die `HomeController.Index` Aktion wird für alle `/`URL-Pfade , `/Home`, `/Home/Index`oder `/Home/Index/3`ausgeführt.

In diesem Beispiel wird ein wichtiger Programmierunterschied zwischen Attributrouting und [herkömmlichem Routing](#cr)hervorgehoben. Das Attributrouting erfordert mehr Eingabe, um eine Route anzugeben. Die herkömmliche Standardroute behandelt Routen prägnanter. Das Attributrouting ermöglicht und erfordert jedoch eine genaue Steuerung, welche Routenvorlagen für jede [Aktion](#action)gelten.

Beim Attributrouting spielen der Controllername und die Aktionsnamen **keine** Rolle, in der die Aktion abgeglichen wird. Das folgende Beispiel entspricht den gleichen URLs wie im vorherigen Beispiel:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

Der folgende Code verwendet `action` `controller`Tokenersatz für und:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

Der folgende `[Route("[controller]/[action]")]` Code gilt für den Controller:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

Im vorherigen Code `Index` müssen die Methodenvorlagen `/` `~/` den Routenvorlagen vorangestellt werden. Routenvorlagen, die auf eine Aktion angewendet werden, die mit einem `/` oder `~/` beginnen, können nicht mit Routenvorlagen kombiniert werden, die auf den Controller angewendet werden.

Informationen zur Routenvorlagenauswahl finden Sie [unter Routenvorlagenpriorität.](xref:fundamentals/routing#rtp)

## <a name="reserved-routing-names"></a>Reservierte Routingnamen

Die folgenden Schlüsselwörter sind reservierte Routenparameternamen bei Verwendung von Controllern oder Razor-Seiten:

* `action`
* `area`
* `controller`
* `handler`
* `page`

Die `page` Verwendung als Routenparameter mit Attributrouting ist ein häufiger Fehler. Dies führt zu inkonsistentem und verwirrendem Verhalten mit der URL-Generierung.

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

Die speziellen Parameternamen werden von der URL-Generierung verwendet, um zu bestimmen, ob ein URL-Generierungsvorgang auf eine Razor-Seite oder auf einen Controller verweist.

<a name="verb"></a>

## <a name="http-verb-templates"></a>HTTP-Verbvorlagen

ASP.NET Core verfügt über die folgenden HTTP-Verbvorlagen:

* [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)
* [[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)
* [[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)
* [[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)
* [[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)
* [[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)

<a name="rt"></a>

### <a name="route-templates"></a>Routenvorlagen

ASP.NET Core verfügt über die folgenden Routenvorlagen:

* Alle [HTTP-Verbvorlagen](#verb) sind Routenvorlagen.
* [[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a>Attributrouting mit Http-Verbattributen

Betrachten Sie den folgenden Controller:

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

Für den Code oben gilt:

* Jede Aktion `[HttpGet]` enthält das Attribut, das die Zuordnung nur zu HTTP GET-Anforderungen einschränkt.
* Die `GetProduct` Aktion `"{id}"` enthält die `id` Vorlage und `"api/[controller]"` wird daher an die Vorlage auf dem Controller angehängt. Die Methodenvorlage `"api/[controller]/"{id}""`ist . Daher entspricht diese Aktion nur GET-Anforderungen für das Formular `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, usw.
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* Die `GetIntProduct` Aktion `"int/{id:int}")` enthält die Vorlage. Der `:int` Teil der Vorlage beschränkt `id` die Routenwerte auf Zeichenfolgen, die in eine ganze Zahl konvertiert werden können. Eine GET-Anforderung an: `/api/test2/int/abc`
  * Entspricht dieser Aktion nicht.
  * Gibt einen [404 Not Found-Fehler](https://developer.mozilla.org/docs/Web/HTTP/Status/404) zurück.
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* Die `GetInt2Product` Aktion `{id}` enthält in der Vorlage, `id` beschränkt sich jedoch nicht auf Werte, die in eine ganze Zahl konvertiert werden können. Eine GET-Anforderung an: `/api/test2/int2/abc`
  * Entspricht dieser Route.
  * Die Modellbindung `abc` kann nicht in eine ganze Zahl konvertiert werden. Der `id` Parameter der Methode ist ganzzahlig.
  * Gibt eine [400 ungültige Anforderung](https://developer.mozilla.org/docs/Web/HTTP/Status/400) `abc` zurück, da die Modellbindung nicht in eine ganze Zahl konvertiert werden konnte.
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

Das Attributrouting <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> kann Attribute <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>wie <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>verwenden, z. B. , und . Alle [HTTP-Verbattribute](#verb) akzeptieren eine Routenvorlage. Das folgende Beispiel zeigt zwei Aktionen, die mit derselben Routenvorlage übereinstimmen:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

Verwenden des `/products3`URL-Pfads :

* Die `MyProductsController.ListProducts` Aktion wird ausgeführt, `GET`wenn das [HTTP-Verb](#verb) lautet.
* Die `MyProductsController.CreateProduct` Aktion wird ausgeführt, `POST`wenn das [HTTP-Verb](#verb) lautet.

Beim Erstellen einer REST-API ist es selten, `[Route(...)]` dass Sie eine Aktionsmethode verwenden müssen, da die Aktion alle HTTP-Methoden akzeptiert. Es ist besser, das spezifischere [HTTP-Verbattribut](#verb) zu verwenden, um genau zu sein, was Ihre API unterstützt. REST-API-Clients sollten wissen, welche Pfade und HTTP-Verben bestimmten logischen Operationen entsprechen.

REST-APIs sollten Attributrouting verwenden, um die Funktionalität der App als eine Gruppe von Ressourcen zu modellieren, in denen Vorgänge durch HTTP-Verben dargestellt werden. Dies bedeutet, dass viele Vorgänge, z. B. GET und POST für dieselbe logische Ressource, dieselbe URL verwenden. Das Attributrouting bietet eine Ebene der Steuerung, die für einen sorgfältigen Entwurf des öffentlichen Endpunktlayouts einer API erforderlich ist.

Da eine Attributroute für eine bestimmte Aktion gilt, ist es einfach, Parameter als Teil der Routenvorlagendefinition erforderlich festzulegen. Im folgenden Beispiel `id` ist als Teil des URL-Pfads erforderlich:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

Die `Products2ApiController.GetProduct(int)` Aktion:

* Wird mit URL-Pfad wie`/products2/3`
* Wird nicht mit dem URL-Pfad `/products2`ausgeführt.

Das [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)-Attribut ermöglicht es einer Aktion, die unterstützten Anforderungsinhaltstypen einzuschränken. Weitere Informationen finden Sie unter [Definieren unterstützter Anforderungsinhaltstypen mit dem Attribut Consumes](xref:web-api/index#consumes).

 Eine vollständige Beschreibung und Routenvorlagen und dazugehörige Optionen finden Sie unter [Routing in ASP.NET Core](xref:fundamentals/routing).

Weitere Informationen `[ApiController]`zu finden Sie unter [ApiController-Attribut](xref:web-api/index##apicontroller-attribute).

## <a name="route-name"></a>Routenname

Im folgenden Code wird ein Routenname von `Products_List` definiert:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

Routennamen können verwendet werden, um basierend auf einer bestimmten Route eine URL zu generieren. Routennamen:

* Haben sie keine Auswirkungen auf das URL-Abgleichsverhalten des Routings.
* Werden nur für die URL-Generierung verwendet.

Routennamen müssen anwendungsweit eindeutig sein.

Vergleichen Sie den vorhergehenden Code mit `id` der herkömmlichen`{id?}`Standardroute, die den Parameter als optional definiert ( ). Die Möglichkeit, APIs präzise zu spezifizieren, hat Vorteile, z. B. das Zulassen `/products` und `/products/5` Anteilen an verschiedene Aktionen.

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a>Kombinieren von Attributrouten

Um Attributrouting weniger repetitiv zu gestalten, werden Routenattribute auf dem Controller mit Routenattributen auf den einzelnen Aktionen kombiniert. Alle auf dem Controller definierten Routenvorlagen werden den Routenvorlagen auf den Aktionen vorangestellt. Wenn Routenattribute auf dem Controller platziert werden, verwenden **alle** Aktionen im Controller Attributrouting.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

Im vorherigen Beispiel:

* Der URL-Pfad `/products` kann mit`ProductsApi.ListProducts`
* Der `/products/5` URL-Pfad `ProductsApi.GetProduct(int)`kann übereinstimmen.

Beide Aktionen stimmen nur `GET` mit HTTP überein, da sie mit dem `[HttpGet]` Attribut gekennzeichnet sind.

Routenvorlagen, die auf eine Aktion angewendet werden, die mit einem `/` oder `~/` beginnen, können nicht mit Routenvorlagen kombiniert werden, die auf den Controller angewendet werden. Im folgenden Beispiel wird eine Reihe von URL-Pfaden ähnlich der Standardroute abgeschlagen.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

In der folgenden `[Route]` Tabelle werden die Attribute im vorherigen Code erläutert:

| attribute               | Kombiniert mit`[Route("Home")]` | Definiert die Routenvorlage |
| ----------------- | ------------ | --------- |
| `[Route("")]` | Ja | `"Home"` |
| `[Route("Index")]` | Ja | `"Home/Index"` |
| `[Route("/")]` | **Nein** | `""` |
| `[Route("About")]` | Ja | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a>Attributroutenreihenfolge

Routing erstellt eine Struktur und stimmt alle Endpunkte gleichzeitig ab:

* Die Routeneinträge verhalten sich wie in einer idealen Reihenfolge.
* Die spezifischsten Routen haben die Möglichkeit, vor den allgemeineren Routen auszuführen.

Eine Attributroute wie `blog/search/{topic}` z. B. ist `blog/{*article}`spezifischer als eine Attributroute wie . Die `blog/search/{topic}` Route hat standardmäßig eine höhere Priorität, da sie spezifischer ist. Mit [konventionellem Routing](#cr)ist der Entwickler dafür verantwortlich, Routen in der gewünschten Reihenfolge zu platzieren.

Attributrouten können einen Auftrag <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> mithilfe der Eigenschaft konfigurieren. Alle [bereitgestellten Routenattribute](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) des `Order` Frameworks enthalten . Routen werden entsprechend einer aufsteigenden Reihenfolge der `Order`-Eigenschaft verarbeitet. Die Standardreihenfolge ist `0`. Festlegen einer `Order = -1` Route mithilfe von Durchläufen vor Routen, die keine Reihenfolge festlegen. Festlegen einer `Order = 1` Route mithilfe von Runs nach der Standardroutenreihenfolge.

**Vermeiden** Sie `Order`die Abhängigkeit von . Wenn der URL-Bereich einer App explizite Auftragswerte erfordert, um sie korrekt weiterzuleiten, ist dies wahrscheinlich auch für Clients verwirrend. Im Allgemeinen wählt das Attributrouting die richtige Route mit URL-Abgleich aus. Wenn die für die URL-Generierung verwendete Standardreihenfolge nicht funktioniert, ist die Verwendung `Order` eines Routennamens als Außerkraftsetzung in der Regel einfacher als das Anwenden der Eigenschaft.

Betrachten Sie die folgenden beiden Controller, die beide den Routenabgleich `/home`definieren:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

Beim `/home` Anfordern mit dem vorherigen Code wird eine Ausnahme ähnlich der folgenden ausgelöst:

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

Durch `Order` Hinzufügen eines der Routenattribute wird die Mehrdeutigkeit behoben:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

Führt mit dem `/home` vorherigen `HomeController.Index` Code den Endpunkt aus. Um zum `MyDemoController.MyIndex`zu `/home/MyIndex`gelangen, fordern Sie an. **Hinweis**:

* Der vorangehende Code ist ein Beispiel oder ein schlechter Routingentwurf. Es wurde verwendet, `Order` um die Eigenschaft zu veranschaulichen.
* Die `Order` Eigenschaft löst nur die Mehrdeutigkeit auf, da diese Vorlage nicht zugeordnet werden kann. Es wäre besser, `[Route("Home")]` die Vorlage zu entfernen.

Weitere Informationen zur Routenreihenfolge finden Sie unter [Razor Pages-Route und App-Konventionen: Routenreihenfolge](xref:razor-pages/razor-pages-conventions#route-order) mit Razor Pages.

In einigen Fällen wird ein HTTP 500-Fehler mit mehrdeutigen Routen zurückgegeben. Verwenden Sie [die Protokollierung,](xref:fundamentals/logging/index) um zu sehen, welche Endpunkte die `AmbiguousMatchException`verursacht haben.

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Token-Ersatz in Routenvorlagen [Controller], [Aktion], [Bereich]

Der Einfachheit halber unterstützen Attributrouten den Tokenersatz für reservierte Routenparameter, indem ein Token in einem der folgenden Punkte eingeschlossen wird:

* Quadratische Klammern:`[]`
* Lockige Klammern:`{}`

Die Token `[action]` `[area]`, `[controller]` und werden durch die Werte des Aktionsnamens, des Bereichsnamens und des Controllernamens aus der Aktion ersetzt, in der die Route definiert ist:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

Für den Code oben gilt:

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * Entspricht`/Products0/List`

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * Entspricht`/Products0/Edit/{id}`

Die Tokenersetzung tritt im letzten Schritt der Erstellung von Attributrouten auf. Das vorangegangene Beispiel verhält sich wie der folgende Code:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

Attributrouten können auch mit Vererbung kombiniert werden. Dies ist leistungsstark kombiniert mit Token-Ersatz. Tokenersetzung gilt auch für Routennamen, die durch Attributrouten definiert werden.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`generiert einen eindeutigen Routennamen für jede Aktion:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

Tokenersetzung gilt auch für Routennamen, die durch Attributrouten definiert werden.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
 generiert für jede Aktion einen eindeutigen Routennamen.

Damit das Trennzeichen `[` oder `]` der Tokenersetzungs-Literalzeichenfolge bei einem Abgleich gefunden wird, muss es doppelt vorhanden sein (`[[` oder `]]`), was einem Escapezeichen entspricht.

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Verwenden eines Parametertransformators zum Anpassen der Tokenersetzung

Die Tokenersetzung kann mit einem Parametertransformator angepasst werden. Ein Parametertransformator implementiert <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> und wandelt den Wert der Parameter um. Ein benutzerdefinierter `SlugifyParameterTransformer` Parametertransformator `SubscriptionManagement` ändert z. B. den Routenwert in: `subscription-management`

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

Die <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> ist eine Anwendungsmodellkonvention, die Folgendes ausführt:

* Wendet einen angegebenen Parametertransformator auf alle Attributrouten in der App an.
* Passt die Tokenwerte für Attributrouten bei ihrer Ersetzung an.

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

Die `ListAll` vorherige `/subscription-management/list-all`Methode stimmt mit über.

Die `RouteTokenTransformerConvention` wird als Option in `ConfigureServices` registriert.

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

Siehe [MDN Web-Docs auf Slug](https://developer.mozilla.org/docs/Glossary/Slug) für die Definition von Slug.

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a>Mehrere Attributrouten

Attributrouting unterstützt das Definieren mehrerer Routen, die zu derselben Aktion führen. Dies wird am häufigsten beim Imitieren des Verhaltens der herkömmlichen Standardroute verwendet. Im folgenden Beispiel wird dies gezeigt:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

Wenn Sie mehrere Routenattribute auf dem Controller festlegen, wird jeweils jedes mit den einzelnen Routenattributen für die Aktionsmethoden kombiniert:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

Alle [HTTP-Verbrouteneinschränkungen](#verb) `IActionConstraint`implementieren .

Wenn mehrere Routenattribute, <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> die implementiert werden, für eine Aktion platziert werden:

* Jede Aktionseinschränkung wird mit der Routenvorlage kombiniert, die auf den Controller angewendet wird.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

Die Verwendung mehrerer Routen für Aktionen mag nützlich und leistungsfähig erscheinen, es ist besser, den URL-Speicherplatz Ihrer App einfach und gut definiert zu halten. Verwenden Sie mehrere Routen für Aktionen **nur** dort, wo dies erforderlich ist, z. B. um vorhandene Clients zu unterstützen.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Angeben von optionalen Attributroutenparametern, Standardwerten und Einschränkungen

Attributrouten unterstützen dieselbe Inline-Syntax wie herkömmliche Routen, um optionale Parameter, Standardwerte und Einschränkungen anzugeben.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

`[HttpPost("product/{id:int}")]` Wendet im vorherigen Code eine Routeneinschränkung an. Die `ProductsController.ShowProduct` Aktion wird nur durch `/product/3`URL-Pfade wie abgeglichen. Der Abschnitt `{id:int}` der Routenvorlage beschränkt dieses Segment auf ganze Zahlen.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

Eine ausführliche Beschreibung der Syntax der Routenvorlage finden Sie unter [Routenvorlagenreferenz](xref:fundamentals/routing#route-template-reference).

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Benutzerdefinierte Routenattribute mit IRouteTemplateProvider

Alle [Routenattribute](#rt) implementieren <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>. Die ASP.NET Core-Laufzeit:

* Sucht beim Starten der App nach Attributen für Controllerklassen und Aktionsmethoden.
* Verwendet die Attribute, `IRouteTemplateProvider` die implementiert werden, um den anfänglichen Satz von Routen zu erstellen.

Implementieren `IRouteTemplateProvider` Sie, um benutzerdefinierte Routenattribute zu definieren. Jeder `IRouteTemplateProvider` lässt Sie eine einzelne Route mit einer benutzerdefinierten Routenvorlage, Reihenfolge und einem benutzerdefinierten Namen definieren:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

Die `Get` vorherige `Order = 2, Template = api/MyTestApi`Methode gibt zurück.

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a>Verwenden des Anwendungsmodells zum Anpassen von Attributrouten

Das Anwendungsmodell:

* Ein Objektmodell, das beim Start erstellt wurde.
* Enthält alle Metadaten, die von ASP.NET Core zum Weiterleiten und Ausführen der Aktionen in einer App verwendet werden.

Das Anwendungsmodell enthält alle Daten, die aus Routenattributen gesammelt wurden. Die Daten aus routenattributen `IRouteTemplateProvider` werden von der Implementierung bereitgestellt. Konventionen:

* Kann geschrieben werden, um das Anwendungsmodell zu ändern, um das Verhalten des Routings anzupassen.
* Werden beim App-Start gelesen.

Dieser Abschnitt zeigt ein grundlegendes Beispiel für das Anpassen von Routing mithilfe des Anwendungsmodells. Der folgende Code führt dazu, dass Routen grob mit der Ordnerstruktur des Projekts in Einklang stehen.

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

Der folgende Code `namespace` verhindert, dass die Konvention auf Controller angewendet wird, die mit Attributen weitergeleitet werden:

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

Der folgende Controller verwendet `NamespaceRoutingConvention`z. B. nicht:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

Die `NamespaceRoutingConvention.Apply`-Methode:

* Tut nichts, wenn der Controller geroutet ist.
* Legt die Controllervorlage `namespace`basierend auf `namespace` der fest, wobei die Basis entfernt wird.

Die `NamespaceRoutingConvention` kann angewendet `Startup.ConfigureServices`werden in:

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

Betrachten Sie z. B. den folgenden Controller:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

Für den Code oben gilt:

* Die `namespace` Basis `My.Application`ist .
* Der vollständige Name des `My.Application.Admin.Controllers.UsersController`vorherigen Controllers lautet .
* Die `NamespaceRoutingConvention` legt die `Admin/Controllers/Users/[action]/{id?`Controllervorlage auf fest.

Der `NamespaceRoutingConvention` kann auch als Attribut auf einem Controller angewendet werden:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Gemischtes Routing: Attributrouting vs. herkömmliches Routing

ASP.NET Core-Apps können die Verwendung von herkömmlichem Routing und Attributrouting kombinieren. In der Regel werden herkömmliche Routen für Controller verwendet, die für Browser-HTML-Seiten gedacht sind, und das Attributrouting für Controller für REST-APIs.

Aktionen werden entweder herkömmlich oder über Attribute zugeordnet, d.h., dass eine Route auf dem Controller oder der Aktion platziert wird. Aktionen, die Attributrouten definieren, können nicht über die herkömmliche Routen und umgekehrt erreicht werden. **Jedes** Routenattribut auf dem Controller führt **dazu,** dass alle Aktionen im Controllerattribut weitergeleitet werden.

Attributrouting und konventionelles Routing verwenden dasselbe Routingmodul.

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a>URL-Generierung und Umgebungswerte

Apps können Routing-URL-Generierungsfunktionen verwenden, um URL-Links zu Aktionen zu generieren. Durch das Generieren von URLs werden Hardcodierungs-URLs eliminiert, wodurch Code robuster und wartungsfähiger wird. Dieser Abschnitt konzentriert sich auf die von MVC bereitgestellten URL-Generierungsfunktionen und behandelt nur die Grundlagen der Funktionsweise der URL-Generierung. Eine detaillierte Beschreibung der URL-Generierung finden Sie unter [Routing in ASP.NET Core](xref:fundamentals/routing).

Die <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> Schnittstelle ist das zugrunde liegende Element der Infrastruktur zwischen MVC und Routing für die URL-Generierung. Eine Instanz `IUrlHelper` von ist `Url` über die Eigenschaft in Controllern, Ansichten und Ansichtskomponenten verfügbar.

Im folgenden Beispiel `IUrlHelper` wird die Schnittstelle `Controller.Url` über die Eigenschaft verwendet, um eine URL für eine andere Aktion zu generieren.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Wenn die App die herkömmliche Standardroute verwendet, ist der Wert der `url` Variablen die URL-Pfadzeichenfolge `/UrlGeneration/Destination`. Dieser URL-Pfad wird durch Routing erstellt, indem:

* Die Routenwerte aus der aktuellen Anforderung, die **als Umgebungswerte**bezeichnet werden.
* Die Werte, `Url.Action` die an die Werte übergeben und diese Werte in der Routenvorlage ersetzt werden:

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Der Wert eines jeden Routenparameters wird in der Routenvorlage durch die entsprechenden Namen mit den Werten und Umgebungswerten ersetzt. Ein Routenparameter, der keinen Wert hat, kann:

* Verwenden Sie einen Standardwert, wenn dieser einen Wert hat.
* Wenn dies optional ist, sollten Sie übersprungen werden. Zum Beispiel `id` die aus `{controller}/{action}/{id?}`der Routenvorlage .

Die URL-Generierung schlägt fehl, wenn ein erforderlicher Routenparameter keinen entsprechenden Wert hat. Wenn die URL-Generierung für eine Route fehlschlägt, wird die nächste Route ausprobiert, bis alle Routen getestet wurden oder eine Übereinstimmung gefunden wurde.

Das vorangegangene Beispiel `Url.Action` für [konventionelles Routing](#cr). Die URL-Generierung funktioniert ähnlich mit [attributrouting](#ar), obwohl die Konzepte unterschiedlich sind. Mit herkömmlichem Routing:

* Die Routenwerte werden zum Erweitern einer Vorlage verwendet.
* Die Routenwerte `controller` `action` für und werden in der Regel in dieser Vorlage angezeigt. Dies funktioniert, weil die URLs, die mit routing übereinstimmen, einer Konvention entsprechen.

Im folgenden Beispiel wird Attributrouting verwendet:

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

Die `Source` Aktion im vorherigen `custom/url/to/destination`Code generiert .

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>wurde in ASP.NET Core 3.0 `IUrlHelper`als Alternative zu hinzugefügt. `LinkGenerator`bietet ähnliche, aber flexiblere Funktionen. Jede Methode `IUrlHelper` auf hat eine `LinkGenerator` entsprechende Familie von Methoden auf als auch.

### <a name="generating-urls-by-action-name"></a>Generieren von URLs nach Aktionsnamen

[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)und alle zugehörigen Überladungen sind alle so konzipiert, dass der Zielendpunkt generiert wird, indem ein Controllername und ein Aktionsname angegeben werden.

Bei `Url.Action`Verwendung von werden `controller` die `action` aktuellen Routenwerte für und werden von der Laufzeit bereitgestellt:

* Der Wert `controller` `action` von und ist Teil von [Umgebungswerten](#ambient) und Werten. Die `Url.Action` Methode verwendet immer `action` die `controller` aktuellen Werte von und und generiert einen URL-Pfad, der zur aktuellen Aktion weiterleitet.

Routing versucht, die Werte in Umgebungswerten zu verwenden, um Informationen einzugeben, die beim Generieren einer URL nicht angegeben wurden. Betrachten Sie `{a}/{b}/{c}/{d}` eine Route `{ a = Alice, b = Bob, c = Carol, d = David }`wie mit Umgebungswerten:

* Routing verfügt über genügend Informationen, um eine URL ohne zusätzliche Werte zu generieren.
* Routing verfügt über genügend Informationen, da alle Routenparameter einen Wert haben.

Wenn der `{ d = Donovan }` Wert addiert wird:

* Der `{ d = David }` Wert wird ignoriert.
* Der generierte `Alice/Bob/Carol/Donovan`URL-Pfad ist .

**Warnung**: URL-Pfade sind hierarchisch. Wenn im vorherigen Beispiel `{ c = Cheryl }` der Wert hinzugefügt wird:

* Beide Werte `{ c = Carol, d = David }` werden ignoriert.
* Es gibt keinen Wert `d` mehr für und die URL-Generierung schlägt fehl.
* Die gewünschten `c` Werte `d` von und müssen angegeben werden, um eine URL zu generieren.  

Sie können erwarten, dass dieses `{controller}/{action}/{id?}`Problem mit der Standardroute behoben wird. Dieses Problem ist in `Url.Action` der Praxis `controller` selten, da immer explizit ein und `action` ein Wert angegeben wird.

Mehrere Überladungen von [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) verwenden ein Routenwertobjekt, `controller` `action`um Werte für andere Routenparameter als und bereitzustellen. Das Routenwertobjekt wird `id`häufig mit verwendet. Beispiel: `Url.Action("Buy", "Products", new { id = 17 })`. Das Routenwerte-Objekt:

* Nach Konvention ist in der Regel ein Objekt des anonymen Typs.
* Kann ein `IDictionary<>` oder ein [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)sein.

Alle zusätzlichen Routenwerte, die keinen Routenparametern zugeordnet sind, werden in der Abfragezeichenfolge platziert.

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

Der vorherige `/Products/Buy/17?color=red`Code generiert .

Der folgende Code generiert eine absolute URL:

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

Um eine absolute URL zu erstellen, verwenden Sie eine der folgenden Optionen:

* Eine Überladung, `protocol`die eine akzeptiert. Zum Beispiel der vorherige Code.
* [LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), die standardmäßig absolute URIs generiert.

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a>Generieren von URLs nach Route

Der vorhergehende Code zeigte das Generieren einer URL durch Übergeben des Controllers und des Aktionsnamens. `IUrlHelper`bietet auch die [Url.RouteUrl-Methodenfamilie](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) an. Diese Methoden ähneln [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), aber sie kopieren nicht `action` `controller` die aktuellen Werte von und in die Routenwerte. Die häufigste Verwendung `Url.RouteUrl`von:

* Gibt einen Routennamen an, um die URL zu generieren.
* Im Allgemeinen wird kein Controller oder Aktionsname angegeben.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

Die folgende Razor-Datei generiert `Destination_Route`einen HTML-Link zu:

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a>Generieren von URLs in HTML und Razor

<xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper>stellt <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> die Zuerzeugungsmethoden `<form>` [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) und `<a>` [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) bzw. Elemente bereit. Diese Methoden verwenden die [Url.Action-Methode,](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) um eine URL zu generieren, und sie akzeptieren ähnliche Argumente. Die `Url.RouteUrl`-Begleiter für `HtmlHelper` sind `Html.BeginRouteForm` und `Html.RouteLink`, die ähnliche Funktionen aufweisen.

Taghilfsprogramme generieren URLs mit den Taghilfsprogrammen `form` und `<a>`. Beide implementieren mit `IUrlHelper`. Weitere Informationen finden Sie [unter Tag Helpers in Formularen.](xref:mvc/views/working-with-forms)

In Ansichten ist `IUrlHelper` über die `Url`-Eigenschaft für jede Ad-hoc-URL-Generierung verfügbar, die keine der oben genannten ist.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a>URL-Generierung in Aktionsergebnissen

Die vorhergehenden `IUrlHelper` Beispiele zeigten die Verwendung in einem Controller. Die häufigste Verwendung in einem Controller besteht darin, eine URL als Teil eines Aktionsergebnisses zu generieren.

Die Basisklassen <xref:Microsoft.AspNetCore.Mvc.ControllerBase> und <xref:Microsoft.AspNetCore.Mvc.Controller> stellen Hilfsmethoden für Aktionsergebnisse bereit, die auf eine andere Aktionen verweisen. Eine typische Verwendung ist die Umleitung nach der Annahme von Benutzereingaben:

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

Die Aktionsergebnisse Factorymethoden <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> wie und folgen einem `IUrlHelper`ähnlichen Muster wie die Methoden auf .

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Sonderfall für dedizierte herkömmliche Routen

[Herkömmliches Routing](#cr) kann eine spezielle Art von Routendefinition verwenden, die als [dedizierte konventionelle Route](#dcr)bezeichnet wird. Im folgenden Beispiel handelt `blog` es sich bei der benannten Route um eine dedizierte konventionelle Route:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Generiert mithilfe der vorherigen `Url.Action("Index", "Home")` Routendefinitionen `/` den `default` URL-Pfad mithilfe der Route, aber warum? Man könnte meinen, dass die Routenwerte `{ controller = Home, action = Index }` ausreichen würden, um eine URL mithilfe von `blog` zu generieren, und das Ergebnis wäre `/blog?action=Index&controller=Home`.

[Dedizierte konventionelle Routen](#dcr) basieren auf einem speziellen Verhalten von Standardwerten, die keinen entsprechenden Routenparameter haben, der verhindert, dass die Route bei der URL-Generierung zu [gierig](xref:fundamentals/routing#greedy) ist. In diesem Fall sind die Standardwerte `{ controller = Blog, action = Article }`, und weder `controller` noch `action` werden als Routenparameter verwendet. Wenn das Routing die URL-Generierung ausführt, müssen die angegebenen Werte mit den Standardwerten übereinstimmen. Die URL-Generierung `blog` mit `{ controller = Home, action = Index }` Fehler schlägt `{ controller = Blog, action = Article }`fehl, da die Werte nicht übereinstimmen. Routing greift dann wieder auf `default` zurück, was erfolgreich ausgeführt wird.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Bereiche

[Bereiche](xref:mvc/controllers/areas) sind eine MVC-Funktion, die verwendet wird, um verwandte Funktionen in einer Gruppe als separate zu organisieren:

* Routing-Namespace für Controlleraktionen.
* Ordnerstruktur für Ansichten.

Durch die Verwendung von Bereichen kann eine App über mehrere Controller mit demselben Namen verfügen, sofern sie unterschiedliche Bereiche haben. Mithilfe von Bereichen wird außerdem eine Hierarchie erstellt, damit das Routing durch Hinzufügen eines anderen Routenparameters ausgeführt werden kann: `area` zu `controller` und `action`. In diesem Abschnitt wird erläutert, wie Routing mit Bereichen interagiert. Weitere Informationen zur Verwendung von Bereichen mit Ansichten finden Sie unter [Bereiche.](xref:mvc/controllers/areas)

Im folgenden Beispiel wird MVC so konfiguriert, `area` dass `area` die `Blog`herkömmliche Standardroute und eine Route für eine benannte Route verwendet wird:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

Im vorherigen Code <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> wird aufgerufen, `"blog_route"`um die zu erstellen. Der zweite `"Blog"`Parameter , ist der Flächenname.

Beim Abgleichen eines `/Manage/Users/AddUser`URL-Pfads wie generiert die `"blog_route"` Route die Routenwerte `{ area = Blog, controller = Users, action = AddUser }`. Der `area` Routenwert wird durch einen `area`Standardwert für erzeugt. Die von `MapAreaControllerRoute` erstellte Route entspricht der folgenden:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

`MapAreaControllerRoute` erstellt mit einem Standardwert und einer Einschränkung für `area` sowie mit dem bereitgestellten Bereichsnamen (in diesem Fall `Blog`) eine Route. Der Standardwert stellt sicher, dass die Route immer `{ area = Blog, ... }` erzeugt, die Einschränkung erfordert den Wert `{ area = Blog, ... }` für URL-Generierung.

Beim herkömmlichen Routing ist die Reihenfolge wichtig. Im Allgemeinen sollten Routen mit Flächen früher platziert werden, da sie spezifischer sind als Routen ohne Fläche.

Im vorherigen Beispiel stimmen `{ area = Blog, controller = Users, action = AddUser }` die Routenwerte mit der folgenden Aktion überein:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

Das [[Area]-Attribut](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) bezeichnet einen Controller als Teil eines Bereichs. Dieser Controller befindet `Blog` sich in der Umgebung. Controller ohne `[Area]` Attribut sind keine Mitglieder eines Bereichs und `area` stimmen **nicht** überein, wenn der Routenwert vom Routing bereitgestellt wird. Im folgenden Beispiel kann nur der erste aufgelistete Controller die Routenwerte `{ area = Blog, controller = Users, action = AddUser }` abgleichen.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

Der Namespace jedes Controllers wird hier zur Vollständigkeit angezeigt. Wenn die vorherigen Controller denselben Namespace verwenden, wird ein Compilerfehler generiert. Klassennamespaces haben keine Auswirkungen auf das MVC-Routing.

Die ersten beiden Controller gehören zu Bereichen und werden können nur abgleichen, wenn ihr jeweiliger Bereichsname vom `area`-Routenwert bereitgestellt wird. Der dritte Controller gehört keinem Bereich an und kann nur abgleichen, wenn vom Routing kein Wert für `area` bereitgestellt wird.

<a name="aa"></a>

Im Hinblick auf das Erkennen *keines Werts* hat die Abwesenheit des `area`-Werts dieselben Auswirkungen, wie wenn der Wert für `area` 0 (null) oder eine leere Zeichenfolge wäre.

Beim Ausführen einer Aktion innerhalb eines Bereichs `area` ist der Routenwert für als [Umgebungswert](#ambient) für das Routing verfügbar, das für die URL-Generierung verwendet werden soll. Das bedeutet, dass Bereiche bei der URL-Generierung wie im folgenden Beispiel dargestellt standardmäßig *beständig* sind.

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

Der folgende Code generiert `/Zebra/Users/AddUser`eine URL zu:

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a>Aktionsdefinition

Öffentliche Methoden auf einem Controller, mit Ausnahme derjenigen mit dem [NonAction-Attribut,](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) sind Aktionen.

## <a name="sample-code"></a>Beispielcode

 * Die [MyDisplayRouteInfo-Methode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) ist im [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) enthalten und wird zum Anzeigen von Routinginformationen verwendet.
* [Beispielcode anzeigen oder herunterladen](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([downloaden](xref:index#how-to-download-a-sample))

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core MVC verwendet [Routing-Middleware](xref:fundamentals/middleware/index), um die URLs der eingehenden Anforderungen abzugleichen und sie Aktionen zuzuordnen. Routen werden im Startcode oder in Attributen definiert und beschreiben, wie URL-Pfade Aktionen zugeordnet werden sollen. Sie werden auch dazu verwendet, um URLs (für Links) zu generieren, die in Antworten gesendet werden.

Aktionen werden entweder herkömmlich oder über Attribute zugeordnet, d.h., dass eine Route auf dem Controller oder der Aktion platziert wird. Weitere Informationen finden Sie im Abschnitt [Gemischtes Routing](#routing-mixed-ref-label).

In diesem Artikel werden die Schnittstellen zwischen MVC und Routing und die Art und Weise erläutert, wie normale MVC-Apps Routingfeatures verwenden. Informationen zum erweiterten Routing finden Sie unter [Routing in ASP.NET Core](xref:fundamentals/routing).

## <a name="setting-up-routing-middleware"></a>Einrichten der Routing-Middleware

Ihre *Configure*-Methode kann Code wie diesen enthalten:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Innerhalb des Aufrufs von `UseMvc` wird mit `MapRoute` eine einzelne Route erstellt, die wir `default`-Route nennen. Die meisten MVC-Apps verwenden eine Route mit einer Vorlage, die der `default`-Route ähnelt.

TDie Routenvorlage `"{controller=Home}/{action=Index}/{id?}"` kann einem URL-Pfad wie `/Products/Details/5` abgleichen und extrahiert die Routenwerte `{ controller = Products, action = Details, id = 5 }`, indem ein Token für den Pfad erstellt wird. MVC versucht, einen Controller namens `ProductsController` zu suchen und die Aktion `Details` auszuführen:

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

In diesem Beispiel hat die Modellbindung den `id`-Parameter mit dem Wert `id = 5` beim Aufrufen dieser Aktion auf `5` festgelegt. Weitere Informationen finden Sie unter [Modellbindung](../models/model-binding.md).

Verwenden der `default`-Route:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Die Routenvorlage:

* `{controller=Home}` definiert `Home` als Standard-`controller`.

* `{action=Index}` definiert `Index` als Standard-`action`.

* `{id?}` definiert `id` als optional.

Standardmäßige und optionale Routenparameter müssen nicht im URL-Pfad vorhanden sein, damit es eine Übereinstimmung gibt. Eine ausführliche Beschreibung der Syntax der Routenvorlage finden Sie unter [Routenvorlagenreferenz](xref:fundamentals/routing#route-template-reference).

`"{controller=Home}/{action=Index}/{id?}"` gleicht den URL-Pfad `/` ab und erzeugt die Routenwerte `{ controller = Home, action = Index }`. Die Werte für `controller` und `action` verwenden die Standardwerte. `id` erzeugt keine Werte, da kein entsprechendes Segment im URL-Pfad vorhanden ist. MVC nutzt diese Routenwerte, um die `HomeController`- und `Index`-Aktion auszuwählen:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Mit dieser Controllerdefinition und dieser Routenvorlage wird die `HomeController.Index`-Aktion für jeden der folgenden URL-Pfade ausgeführt:

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

Mit der Hilfsmethode `UseMvcWithDefaultRoute`:

```csharp
app.UseMvcWithDefaultRoute();
```

kann Folgendes ersetzt werden:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`UseMvc` und `UseMvcWithDefaultRoute` fügen der Middleware-Pipeline eine Instanz von `RouterMiddleware` hinzu. MVC interagiert nicht direkt mit der Middleware und verarbeitet Anforderungen mithilfe von Routing. MVC ist über eine Instanz von `MvcRouteHandler` mit den Routen verbunden. Der Code in `UseMvc` ähnelt Folgendem:

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

`UseMvc` definiert keine Routen direkt, sondern fügt der Routenauflistung einen Platzhalter für die `attribute`-Route hinzu. Die Überladung `UseMvc(Action<IRouteBuilder>)` ermöglicht es Ihnen, Ihre eigenen Routen hinzuzufügen und unterstützt darüber hinaus das Routingattribut.  `UseMvc` und alle Varianten fügen einen Platzhalter für die Attributroute hinzu. Attributrouting ist immer verfügbar, unabhängig von der Konfiguration von `UseMvc`. `UseMvcWithDefaultRoute` definiert eine Standardroute und unterstützt Attributrouting. Der Abschnitt [Attributrouting](#attribute-routing-ref-label) enthält weitere Informationen zu dem Thema.

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a>Herkömmliches Routing

Die `default`-Route:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Der vorangehende Code ist ein Beispiel für ein herkömmliches Routing. Dieser Stil wird als herkömmliches Routing bezeichnet, da er eine *Konvention* für URL-Pfade festlegt:

* Das erste Pfadsegment wird dem Controllernamen zugeordnet.
* Die zweite Zuordnung zum Aktionsnamen.
* Das dritte Segment wird `id`für eine optionale verwendet. `id`einer Modellentität zugeordnet.

Mit dieser `default`-Route wir der URL-Pfad `/Products/List` der `ProductsController.List`-Aktion und `/Blog/Article/17``BlogController.Article` zugeordnet. Diese Zuordnung basiert **ausschließlich** auf den Controller- und Aktionsnamen und nicht auf Namespaces, Speicherorten für Quelldateien oder Methodenparametern.

> [!TIP]
> Die Kombination aus herkömmlichem Routing und Standardrouting ermöglicht es Ihnen, Anwendungen schnell zu erstellen, ohne für jede definierte Aktion ein neues URL-Muster entwerfen zu müssen. Bei Anwendungen mit Aktionen im CRUD-Stil können einheitliche URLs auf allen Controllern dabei helfen, den Code zu vereinfachen und die UI vorhersehbarer zu machen.

> [!WARNING]
> `id` wird von der Routenvorlage als optional definiert, was bedeutet, dass Ihre Aktionen ausgeführt werden, ohne dass die ID als Teil der URL bereitgestellt wird. Wenn `id` in der URL ausgelassen wird, wird es üblicherweise von der Modellbindung auf `0` festgelegt, und daher wird in der Datenbank keine Entität gefunden, die `id == 0` entspricht. Mit dem Attributrouting können Sie präzise steuern, für welche Aktionen die ID erforderlich ist und für welche nicht. Gemäß der Konvention enthält die Dokumentation optionale Parameter wie `id`, wenn sie wahrscheinlich in der richtigen Verwendung angezeigt werden.

## <a name="multiple-routes"></a>Mehrere Routen

Sie können mehrere Routen innerhalb von `UseMvc` hinzufügen, indem Sie weitere Aufrufe von `MapRoute` hinzufügen. So können Sie mehrere Konventionen definieren oder herkömmlichen Routen hinzufügen, die einer bestimmten Aktion zugeordnet sind, z.B.:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Die `blog`-Route hier ist eine *dedizierte herkömmliche Route*, d.h., dass das herkömmliche Routingsystem verwendet wird, aber einer bestimmten Aktion zugeordnet ist. Da `controller` und `action` nicht als Parameter in der Routenvorlage vorkommen, können sie nur die Standardwerte haben. Daher wird diese Route immer der Aktion `BlogController.Article` zugeordnet.

Die Routen in der Routenauflistung sind geordnet und werden in der Reihenfolge verarbeitet, in der sie hinzugefügt wurden. In diesem Beispiel wird daher die Route `blog` vor der Route `default` überprüft.

> [!NOTE]
> *Dedizierte konventionelle Routen* verwenden häufig **catch-all-Routenparameter,** z. `{*article}` B. um den verbleibenden Teil des URL-Pfads zu erfassen. Dies kann eine Route „zu gierig“ machen, d.h., dass sie alle URLs abgleicht, die eigentlich von anderen Routen abgeglichen werden sollten. Fügen Sie die „gierigen“ Routen weiter unten in die Routingtabelle ein, um dieses Problem zu beheben.

### <a name="fallback"></a>Fallback

Im Rahmen der Anforderungsverarbeitung überprüft MVC, ob mit den Routenwerten ein Controller und eine Aktion in Ihrer Anwendung gefunden werden können. Falls die Routenwerte mit keiner Aktion übereinstimmen, gilt die Route nicht als Übereinstimmung, und die nächste Route wird überprüft. Dabei spricht man von einem *Fallback*. Dieser Vorgang soll Szenarios vereinfachen, bei denen sich herkömmliche Routen überschneiden.

### <a name="disambiguating-actions"></a>Aktionen eindeutig zuordnen

Wenn zwei Aktionen beim Routing übereinstimmen, muss MVC beide analysieren und die beste auswählen oder eine Ausnahme auslösen. Beispiel:

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

Dieser Controller definiert zwei Aktionen, die mit dem URL-Pfad `/Products/Edit/17` übereinstimmen und die Daten `{ controller = Products, action = Edit, id = 17 }` weiterleiten. Dies ist ein typisches Muster für MVC-Controller, in dem `Edit(int)` ein Formular zum Bearbeiten eines Produkts anzeigt und `Edit(int, Product)` das bereitgestellte Formular verarbeitet. Um dies zu ermöglichen, muss MVC `Edit(int, Product)` auswählen, wenn die Anforderung HTTP-`POST` ist, und `Edit(int)`, wenn das HTTP-Verb ein anderes ist.

`HttpPostAttribute` (`[HttpPost]`) ist eine Implementierung von `IActionConstraint`, bei der die Aktion nur ausgewählt werden darf, wenn das HTTP-Verb `POST` ist. Aufgrund des Vorhandenseins von `IActionConstraint` ist `Edit(int, Product)` die „bessere“ Übereinstimmung als `Edit(int)`, sodass `Edit(int, Product)` zuerst überprüft wird.

Benutzerdefinierte `IActionConstraint`-Implementierungen müssen Sie nur in speziellen Szenarios schreiben. Es ist jedoch wichtig, die Rolle von Attributen wie `HttpPostAttribute` zu kennen, denn ähnliche Attribute sind auch für andere HTTP-Verben definiert. Beim herkömmlichen Routing nutzen Aktionen oft denselben Aktionsnamen, wenn sie Teil eines `show form -> submit form`-Workflows sind. Die Vorteile dessen werden deutlicher, wenn Sie den Abschnitt [Verstehen von IActionConstraint](#understanding-iactionconstraint) gelesen haben.

Wenn mehrere Routen übereinstimmen und MVC die „beste“ nicht bestimmen kann, löst es eine `AmbiguousActionException` aus.

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a>Routennamen

Die Zeichenfolgen `"blog"` und `"default"` in den folgenden Beispielen sind Routennamen:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Der Routenname gibt der Route einen logischen Namen, damit die benannte Route bei der URL-Generierung verwendet werden kann. Dadurch wird die URL-Erstellung erheblich vereinfacht, obwohl die Reihenfolge der Routen die URL-Generierung verkomplizieren sollte. Routennamen müssen anwendungsweit eindeutig sein.

Routennamen haben keine Auswirkung auf die URL-Zuordnung oder die Verarbeitung von Anforderungen. Sie dienen nur der URL-Generierung. Unter [Routing](xref:fundamentals/routing) finden Sie weitere ausführliche Informationen zur URL-Generierung, einschließlich der Generierung in MVC-spezifischen Hilfsprogrammen.

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a>Attributrouting

Beim Attributrouting werden Aktionen mithilfe von Attributen direkt Routenvorlagen zugeordnet. Im folgenden Beispiel wird `app.UseMvc();` in der `Configure`-Methode verwendet, und es wird keine Route übergeben. `HomeController` entspricht URLs, die denen ähneln, denen die Standardroute `{controller=Home}/{action=Index}/{id?}` entsprechen würde:

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

Die `HomeController.Index()`-Aktion wird für die URL-Pfade `/`, `/Home` und `/Home/Index` jeweils ausgeführt.

> [!NOTE]
> In diesem Beispiel wird ein wichtiger Unterschied beim Programmieren zwischen dem Attributrouting und dem herkömmlichen Routing hervorgehoben. Attributrouting erfordert mehr Eingabe, um eine Route anzugeben. Die herkömmliche Standardroute verarbeitet auch kürzere Routen. Attributrouting ermöglicht jedoch (und erfordert auch) die präzise Kontrolle der Routenvorlagen, die für die einzelnen Aktionen gelten.

Beim Attributrouting haben der Controller- und der Aktionsname **keine** Auswirkung darauf, welche Aktion ausgewählt wird. In diesem Beispiel werden dieselben URLs wie im vorherigen Beispiel abgeglichen.

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> Die oben genannten Routenvorlagen definieren keine Routenparameter für `action`, `area` und `controller`. Diese Routenparameter sind in Attributrouten nicht zulässig. Da die Routenvorlage bereits einer Aktion zugeordnet ist, wäre es nicht sinnvoll, den Aktionsnamen aus der URL zu analysieren.

## <a name="attribute-routing-with-httpverb-attributes"></a>Attributrouting mit Http[Verb]-Attributen

Beim Attributrouting können auch `Http[Verb]`-Attribute wie `HttpPostAttribute` verwendet werden. Alle diese Attribute können eine Routenvorlage akzeptieren. In diesem Beispiel werden zwei Aktionen gezeigt, die derselben Routenvorlage zugeordnet sind:

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

Für einen URL-Pfad wie `/products` wird die Aktion `ProductsApi.ListProducts` ausgeführt, wenn das HTTP-Verb `GET` ist, und `ProductsApi.CreateProduct` wird ausgeführt, wenn das HTTP-Verb `POST` entspricht. Attributrouting gleicht zuerst die URL gegen die Routenvorlagen ab, die von den Routenattributen definiert werden. Sobald eine Routenvorlage übereinstimmt, werden `IActionConstraint`-Einschränkungen angewendet, um zu bestimmen, welche Aktionen ausgeführt werden können.

> [!TIP]
> Beim Erstellen einer REST-API wird `[Route(...)]` selten für eine Aktionsmethode verwendet, die Aktion alle HTTP-Methoden akzeptiert. Es ist besser, das spezifischere `Http*Verb*Attributes` zu nutzen, um präzise anzugeben, was Ihre API unterstützt. REST-API-Clients sollten wissen, welche Pfade und HTTP-Verben bestimmten logischen Operationen entsprechen.

Da eine Attributroute für eine bestimmte Aktion gilt, ist es einfach, Parameter als Teil der Routenvorlagendefinition erforderlich festzulegen. In diesem Beispiel ist `id` als Teil des URL-Pfads erforderlich.

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Die `ProductsApi.GetProduct(int)`-Aktion wird für einen URL-Pfad wie `/products/3` ausgeführt, jedoch nicht für einen URL-Pfad wie `/products`. Eine vollständige Beschreibung und Routenvorlagen und dazugehörige Optionen finden Sie unter [Routing in ASP.NET Core](xref:fundamentals/routing).

## <a name="route-name"></a>Routenname

Im folgenden Code wird ein *Routenname* von `Products_List` definiert:

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Routennamen können verwendet werden, um basierend auf einer bestimmten Route eine URL zu generieren. Routennamen haben keine Auswirkung auf das URL-Zuordnungsverhalten des Routings und dienen nur zur URL-Generierung. Routennamen müssen anwendungsweit eindeutig sein.

> [!NOTE]
> Vergleichen Sie dies mit der herkömmlichen *Standardroute*, die den `id`-Parameter als optional definiert (`{id?}`). APIs präzise angeben zu können, hat Vorteile, z.B. können `/products` und `/products/5` an unterschiedliche Aktionen gesendet werden.

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a>Kombinieren von Routen

Um Attributrouting weniger repetitiv zu gestalten, werden Routenattribute auf dem Controller mit Routenattributen auf den einzelnen Aktionen kombiniert. Alle auf dem Controller definierten Routenvorlagen werden den Routenvorlagen auf den Aktionen vorangestellt. Wenn Routenattribute auf dem Controller platziert werden, verwenden **alle** Aktionen im Controller Attributrouting.

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

In diesem Beispiel kann der URL-Pfad `/products``ProductsApi.ListProducts` und der URL-Pfad `/products/5``ProductsApi.GetProduct(int)` zugeordnet werden. Beide Aktionen gleichen nur „HTTP `GET`“ ab, weil sie mit `HttpGetAttribute` markiert sind.

Routenvorlagen, die auf eine Aktion angewendet werden, die mit einem `/` oder `~/` beginnen, können nicht mit Routenvorlagen kombiniert werden, die auf den Controller angewendet werden. In diesem Beispiel werden mehrere URL-Pfade zugeordnet, die der *Standardroute* ähneln.

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a>Ordnen der Attributrouten

Im Gegensatz zu herkömmlichen Routen, die in einer definierten Reihenfolge ausgeführt werden, erstellt das Attributrouting eine Struktur und entspricht allen Routen gleichzeitig. Der Vorgang wird also ausgeführt, als ob die Routeneinträge in der idealen Reihenfolge platziert worden wären: Die spezifischsten Routen können also vor den allgemeineren ausgeführt werden.

Eine Route wie `blog/search/{topic}` ist z.B. spezifischer als eine Route wie `blog/{*article}`. Logisch gesehen wird die Route `blog/search/{topic}` standardmäßig zuerst ausgeführt, da dies die einzig sinnvolle Reihenfolge ist. Beim herkömmlichen Routing ist der Entwickler verantwortlich dafür, die Routen in die gewünschte Reihenfolge zu bringen.

Attributrouten können mithilfe der `Order`-Eigenschaft aller vom Framework bereitgestellten Routenattribute eine Reihenfolge konfigurieren. Routen werden entsprechend einer aufsteigenden Reihenfolge der `Order`-Eigenschaft verarbeitet. Die Standardreihenfolge ist `0`. Wird für eine Route `Order = -1` festgelegt, wird sie vor denjenigen Routen ausgeführt, für die keine Reihenfolge festgelegt wurde. Wird für eine Route `Order = 1` festgelegt, wird sie ausgeführt, nachdem die Standardroutenreihenfolge ausgeführt wurde.

> [!TIP]
> Vermeiden Sie eine Abhängigkeit von `Order`. Wenn der URL-Raum explizite Reihenfolgenwerte erfordert, um korrekt weiterzuleiten, ist es wahrscheinlich auch für Clients verwirrend. Beim Attributrouting wird im Allgemeinen mithilfe der URL-Zuordnung die richtige Route ausgewählt. Wenn die für die URL-Generierung verwendete Standardreihenfolge nicht funktioniert, ist es meist einfacher, Routennamen als Außerkraftsetzung zu verwenden, statt die `Order`-Eigenschaft anzuwenden.

Razor Pages-Routing und MVC Controller-Routing verwenden eine gemeinsame Implementierung. Informationen zur Routenreihenfolge in den Razor Pages-Themen finden Sie unter [Razor Pages-Route and App-Konventionen: Routenreihenfolge](xref:razor-pages/razor-pages-conventions#route-order).

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Ersetzen von Token in Routenvorlagen ([controller], [action] [area])

Aus Gründen der Einfachheit unterstützen Attributrouten den *Tokenersatz,* `[`indem `]`ein Token in quadratisch-braces ( , ) eingeschlossen wird. Die Token `[action]`, `[area]` und `[controller]` werden durch die Werte der Aktionsnamen, den Namen des Bereichs und des Controllers der Aktion ersetzt, in dem die Route definiert ist. In dem folgenden Beispiel entsprechen die Aktionen wie in den Kommentaren beschrieben URL-Pfaden:

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

Die Tokenersetzung tritt im letzten Schritt der Erstellung von Attributrouten auf. Der folgende Code verhält sich genauso wie der aus dem obigen Beispiel:

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

Attributrouten können auch mit Vererbung kombiniert werden. Dies ist besonders effektiv in Kombination mit der Tokenersetzung.

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

Tokenersetzung gilt auch für Routennamen, die durch Attributrouten definiert werden. `[Route("[controller]/[action]", Name="[controller]_[action]")]` generiert für jede Aktion einen eindeutigen Routennamen.

Damit das Trennzeichen `[` oder `]` der Tokenersetzungs-Literalzeichenfolge bei einem Abgleich gefunden wird, muss es doppelt vorhanden sein (`[[` oder `]]`), was einem Escapezeichen entspricht.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Verwenden eines Parametertransformators zum Anpassen der Tokenersetzung

Die Tokenersetzung kann mit einem Parametertransformator angepasst werden. Ein Parametertransformator implementiert `IOutboundParameterTransformer` und wandelt den Wert der Parameter um. Beispielsweise ändert ein benutzerdefinierter `SlugifyParameterTransformer`-Parametertransformator den Routenwert `SubscriptionManagement` in `subscription-management`.

Die `RouteTokenTransformerConvention` ist eine Anwendungsmodellkonvention, die Folgendes ausführt:

* Wendet einen angegebenen Parametertransformator auf alle Attributrouten in der App an.
* Passt die Tokenwerte für Attributrouten bei ihrer Ersetzung an.

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

Die `RouteTokenTransformerConvention` wird als Option in `ConfigureServices` registriert.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
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


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a>Mehrere Routen

Attributrouting unterstützt das Definieren mehrerer Routen, die zu derselben Aktion führen. Dies wird am häufigsten beim Imitieren des Verhaltens der *herkömmlichen Standardroute* verwendet. Im folgenden Beispiel wird dies gezeigt:

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

Wenn mehrere Routenattribute auf dem Controller platziert werden, wird jedes mit jedem der Routenattribute auf den Aktionsmethoden kombiniert.

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

Werden mehrere Routenattribute (die `IActionConstraint` implementieren) auf einer Aktion platziert, wird jede Aktionseinschränkung mit der Routenvorlage aus dem Attribut kombiniert, das sie definiert.

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> Obwohl das Verwenden mehrerer Routen für Aktionen sehr hilfreich scheint, ist es empfehlenswerter, den URL-Raum der Anwendung einfach und gut definiert zu halten. Verwenden Sie nur mehrere Routen für Aktionen, wenn dies notwendig ist, z.B. um vorhandene Clients zu unterstützen.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Angeben von optionalen Attributroutenparametern, Standardwerten und Einschränkungen

Attributrouten unterstützen dieselbe Inline-Syntax wie herkömmliche Routen, um optionale Parameter, Standardwerte und Einschränkungen anzugeben.

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

Eine ausführliche Beschreibung der Syntax der Routenvorlage finden Sie unter [Routenvorlagenreferenz](xref:fundamentals/routing#route-template-reference).

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Benutzerdefinierte Routenattribute mithilfe von `IRouteTemplateProvider`

Alle im Framework bereitgestellten Routenattribute (`[Route(...)]`, `[HttpGet(...)]` usw.) implementieren die `IRouteTemplateProvider`-Schnittstelle. MVC sucht in Controllerklassen und Aktionsmethoden nach Attributen, wenn die Anwendung gestartet wird, und verwendet diejenigen, die `IRouteTemplateProvider` implementieren, um die anfänglichen Routen zu erstellen.

Sie können `IRouteTemplateProvider` implementieren, um eigene Routenattribute zu definieren. Jeder `IRouteTemplateProvider` lässt Sie eine einzelne Route mit einer benutzerdefinierten Routenvorlage, Reihenfolge und einem benutzerdefinierten Namen definieren:

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

Das Attribut aus dem obigen Beispiel legt `Template` automatisch auf `"api/[controller]"` fest, wenn `[MyApiController]` angewendet wird.

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a>Anpassen von Attributrouten mithilfe des Anwendungsmodells

Das *Anwendungsmodell* ist ein Objektmodell, das beim Starten aus allen Metadaten von MVC erstellt wird, um Ihre Aktionen weiterzuleiten und auszuführen. Das *Anwendungsmodell* enthält alle Daten, die aus Routenattributen (über `IRouteTemplateProvider`) erfasst wurden. Sie können *Konventionen* schreiben, damit das Anwendungsmodell beim Startzeitpunkt das Routingverhalten anpasst. Dieser Abschnitt enthält ein einfaches Beispiel für das Anpassen des Routings mithilfe des Anwendungsmodells.

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Gemischtes Routing: Attributrouting vs. herkömmliches Routing

MVC-Anwendungen können herkömmliches Routing und Attributrouting kombinieren. In der Regel werden herkömmliche Routen für Controller verwendet, die für Browser-HTML-Seiten gedacht sind, und das Attributrouting für Controller für REST-APIs.

Aktionen werden entweder herkömmlich oder über Attribute zugeordnet, d.h., dass eine Route auf dem Controller oder der Aktion platziert wird. Aktionen, die Attributrouten definieren, können nicht über die herkömmliche Routen und umgekehrt erreicht werden. Wenn nur **ein** Routenattribut auf dem Controller platziert wird, werden alle Aktionen im Controller über Attribute zugeordnet.

> [!NOTE]
> Die beiden Routingmethoden unterscheiden sich in dem Prozess, der angewendet wird, nachdem eine URL einer Routenvorlage zugeordnet wurde. Beim herkömmlichen Routing dienen die Routenwerte aus der Zuordnung dazu, aus einer Nachschlagetabelle aller herkömmlich zugeordneten Aktionen die Aktion und den Controller auszuwählen. Beim Attributrouting ist jede Vorlage bereits einer Aktion zugeordnet, und keine weitere Suche ist erforderlich.

## <a name="complex-segments"></a>Komplexe Segmente

Komplexe Segmente (z.B. `[Route("/dog{token}cat")]`) werden von rechts nach links auf eine nicht gierige Weise durch entsprechende Literale verarbeitet. Eine entsprechende Beschreibung finden Sie im [Quellcode](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296). Weitere Informationen finden Sie in [diesem Thema](https://github.com/dotnet/AspNetCore.Docs/issues/8197).

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a>URL-Generierung

MVC-Anwendungen können Routingfeatures zur URL-Generierung verwenden, um URL-Links zu Aktionen zu generieren. Durch das Generieren von URLs müssen URLs nicht mehr hartcodiert werden, sodass der Code robuster und leichter verwaltbar wird. Dieser Abschnitt konzentriert sich auf die von MVC bereitgestellte URL-Generierung und befasst sich nur kurz mit der Funktionsweise. Eine detaillierte Beschreibung der URL-Generierung finden Sie unter [Routing in ASP.NET Core](xref:fundamentals/routing).

Die `IUrlHelper`-Schnittstelle ist die zugrunde liegende Infrastruktur zwischen MVC und dem Routing zur URL-Generierung. Eine Instanz von `IUrlHelper` ist über die `Url`-Eigenschaft in Controllern, Ansichten und Ansichtskomponenten verfügbar.

In diesem Beispiel wird die `IUrlHelper`-Schnittstelle von der `Controller.Url`-Eigenschaft dazu verwendet, eine URL in einer anderen Aktion zu generieren.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Wenn die Anwendung die herkömmliche Standardroute verwendet, ist der Wert der `url`-Variable die URL-Pfadzeichenfolge `/UrlGeneration/Destination`. Dieser URL-Pfad wird vom Routing erstellt, indem Routenwerte aus der aktuellen Anforderung (Umgebungswerte) mit den an `Url.Action` übergebenen Werten kombiniert und anschließend in die Routenvorlage eingefügt werden:

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Der Wert eines jeden Routenparameters wird in der Routenvorlage durch die entsprechenden Namen mit den Werten und Umgebungswerten ersetzt. Ein Routenparameter ohne Wert kann einen Standardwert verwenden, wenn er über einen verfügt, oder übersprungen werden, wenn er optional ist (wie im Fall von `id` in diesem Beispiel). Die URL-Generierung schlägt fehl, wenn ein erforderlicher Routenparameter keinen entsprechenden Wert besitzt. Wenn die URL-Generierung für eine Route fehlschlägt, wird die nächste Route ausprobiert, bis alle Routen getestet wurden oder eine Übereinstimmung gefunden wurde.

In dem Beispiel mit `Url.Action` oben wird von herkömmlichem Routing ausgegangen. Die URL-Generierung funktioniert ähnlich wie das Attributrouting, obwohl sich die Konzepte unterscheiden. Bei herkömmlichem Routing wird mit den Routenwerten eine Vorlage erweitert, und die Routenwerte für `controller` und `action` kommen in der Regel in dieser Vorlage vor. Das funktioniert, weil sich die mit dem Routing zugeordneten URLs an eine *Konvention* halten. Beim Attributrouting dürfen die Routenwerte für `controller` und `action` nicht in der Vorlage vorkommen. Sie werden stattdessen verwendet, um nachzuschlagen, welche Vorlage verwendet werden soll.

In diesem Beispiel wird das Attributrouting verwendet:

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

MVC erstellt eine Nachschlagetabelle aller über Attribute zugeordneten Aktionen und ordnet die `controller`- und `action`-Werte zu, um die Routenvorlage auszuwählen, die für die URL-Generierung verwendet werden soll. Im obigen Beispiel wird `custom/url/to/destination` generiert.

### <a name="generating-urls-by-action-name"></a>Generieren von URLs nach Aktionsnamen

`Url.Action` (`IUrlHelper` . `Action`) und alle zugehörigen Überladungen bauen alle auf der Idee auf, dass Sie angeben, was Sie verknüpfen möchten, indem Sie einen Controllernamen und einen Aktionsnamen angeben.

> [!NOTE]
> Bei Verwendung von `Url.Action` werden die aktuellen Routenwerte für `controller` und `action` für Sie festgelegt. Die Werte von `controller` und `action` bestehen sowohl aus *Umgebungswerten* **als auch aus ** *Werten*. Die Methode `Url.Action` verwendet immer die aktuellen Werte von `action` und `controller` und generiert einen URL-Pfad, der zur aktuellen Aktion weiterleitet.

Beim Routing wird versucht, mit den Werten in den Umgebungswerten Informationen auszufüllen, die Sie beim Generieren einer URL nicht bereitgestellt haben. Mit Routen wie `{a}/{b}/{c}/{d}` und Umgebungswerten wie `{ a = Alice, b = Bob, c = Carol, d = David }` hat das Routing genügend Informationen, um eine URL ohne zusätzliche Werte zu generieren, da alle Routenparameter einen Wert aufweisen. Wenn Sie den Wert `{ d = Donovan }` hinzufügen, wird der Wert `{ d = David }` ignoriert, und der generierte URL-Pfad wäre `Alice/Bob/Carol/Donovan`.

> [!WARNING]
> URL-Pfade sind hierarchisch. Wenn Sie im obigen Beispiel den Wert `{ c = Cheryl }` hinzufügen, werden die beiden Werte `{ c = Carol, d = David }` ignoriert. In diesem Fall haben wir keinen Wert für `d` mehr, und die URL-Generierung schlägt fehl. Sie müssten dann den gewünschten Wert von `c` und `d` angeben.  Man könnte annehmen, dass dieses Problem bei der Standardroute auftritt (`{controller}/{action}/{id?}`). Tatsächlich passiert es in der Praxis jedoch selten, das `Url.Action` immer explizit einen `controller`- und `action`-Wert angibt.

Längere Überladungen von `Url.Action` akzeptieren auch ein zusätzliches *route values*-Objekt, um andere Werte für Routenparameter als `controller` und `action` bereitzustellen. Es wird in der Regel mit `id` verwendet, z.B. in `Url.Action("Buy", "Products", new { id = 17 })`. Gemäß der Konvention ist das *Routenwerte*-Objekt eines des anonymen Typs, es kann aber auch ein `IDictionary<>`-Objekt oder ein *Plain Old .NET Object* sein. Alle zusätzlichen Routenwerte, die keinen Routenparametern zugeordnet sind, werden in der Abfragezeichenfolge platziert.

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> Um eine absolute URL zu erstellen, verwenden Sie eine Überladung, die eine `protocol` akzeptiert: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a>Generieren von URLs nach Routen

Im obigen Code wurde das Generieren einer URL durch das Übergeben des Controller- und Aktionsnamens veranschaulicht. `IUrlHelper` stellt außerdem die `Url.RouteUrl`-Methodenfamilie bereit. Diese Methoden ähneln `Url.Action`, kopieren jedoch nicht die aktuellen Werte `action` und `controller` in die Routenwerte. Oftmals wird damit ein Routenname angegeben, um mit einer bestimmten Route die URL zu generieren, in der Regel *ohne* Angabe eines Controller- oder Aktionsnamens.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a>Generieren von URLs in HTML

`IHtmlHelper` stellt die `HtmlHelper`-Methoden `Html.BeginForm` und `Html.ActionLink` bereit, um jeweils `<form>`- und `<a>`-Elemente zu generieren. Diese Methoden verwenden die `Url.Action`-Methode, um eine URL zu generieren, und akzeptieren ähnliche Argumente. Die `Url.RouteUrl`-Begleiter für `HtmlHelper` sind `Html.BeginRouteForm` und `Html.RouteLink`, die ähnliche Funktionen aufweisen.

Taghilfsprogramme generieren URLs mit den Taghilfsprogrammen `form` und `<a>`. Beide implementieren mit `IUrlHelper`. Weitere Informationen finden Sie unter [Einführung in die Verwendung von Taghilfsprogrammen in Formularen in ASP.NET Core](../views/working-with-forms.md).

In Ansichten ist `IUrlHelper` über die `Url`-Eigenschaft für jede Ad-hoc-URL-Generierung verfügbar, die keine der oben genannten ist.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a>Generieren von URLs in Aktionsergebnissen

In den obigen Beispielen wurde gezeigt, wie `IUrlHelper` in einem Controller verwendet wird und dass es üblicherweise dazu dient, eine URL im Rahmen eines Aktionsergebnisses zu generieren.

Die Basisklassen `ControllerBase` und `Controller` stellen Hilfsmethoden für Aktionsergebnisse bereit, die auf eine andere Aktionen verweisen. Eine typische Verwendung besteht darin, nach dem Akzeptieren einer Benutzereingabe weiterzuleiten.

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

Die Factorymethoden der Aktionsergebnisse folgen einem ähnlichen Muster wie die Methoden für `IUrlHelper`.

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Sonderfall für dedizierte herkömmliche Routen

Beim herkömmlichen Routing können Sie eine bestimmte Art von Routendefinition verwenden, die als *dedizierte herkömmliche Route* bezeichnet wird. Die Route namens `blog` im folgenden Beispiel ist eine dedizierte herkömmliche Route.

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Wenn Sie diese Routendefinitionen verwenden, generiert `Url.Action("Index", "Home")` den URL-Pfad `/` mit der `default`-Route. Die Frage ist nur warum? Man könnte meinen, dass die Routenwerte `{ controller = Home, action = Index }` ausreichen würden, um eine URL mithilfe von `blog` zu generieren, und das Ergebnis wäre `/blog?action=Index&controller=Home`.

Dedizierte herkömmliche Routen nutzen ein spezielles Verhalten von Standardwerten, die keinen entsprechenden Routenparameter besitzen, der verhindert, dass die Route bei der URL-Generierung „zu gierig“ wird. In diesem Fall sind die Standardwerte `{ controller = Blog, action = Article }`, und weder `controller` noch `action` werden als Routenparameter verwendet. Wenn das Routing die URL-Generierung ausführt, müssen die angegebenen Werte mit den Standardwerten übereinstimmen. Die URL-Generierung mithilfe von `blog` schlägt fehl, da die Werte `{ controller = Home, action = Index }` nicht mit `{ controller = Blog, action = Article }` übereinstimmen. Routing greift dann wieder auf `default` zurück, was erfolgreich ausgeführt wird.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Bereiche

[Bereiche](areas.md) sind ein MVC-Feature, mit dem verwandte Funktionen in einer Gruppe als separater Routing-Namespace (für Controlleraktionen) und als Ordnerstruktur (für Ansichten) organisiert werden können. Mithilfe von Bereichen kann eine Anwendung mehrere Controller mit demselben Namen haben – solange sie verschiedene *Bereiche* haben. Mithilfe von Bereichen wird außerdem eine Hierarchie erstellt, damit das Routing durch Hinzufügen eines anderen Routenparameters ausgeführt werden kann: `area` zu `controller` und `action`. In diesem Abschnitt wird erläutert, wie Routing mit Bereichen interagiert. Weitere Informationen zur Verwendung von Bereichen mit Ansichten finden Sie unter [Bereiche](areas.md).

Im folgenden Beispiel wird MVC konfiguriert, sodass es die standardmäßige herkömmliche Route verwendet und eine *Bereichsroute* für einen Bereich namens `Blog`:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

Beim Abgleich eines URL-Pfads wie `/Manage/Users/AddUser` erzeugt die erste Route die Routenwerte `{ area = Blog, controller = Users, action = AddUser }`. Der `area`-Routenwert wird von einem Standardwert für `area` erzeugt. Die von `MapAreaRoute` erstellte Route entspricht in der Tat der folgenden:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

`MapAreaRoute` erstellt mit einem Standardwert und einer Einschränkung für `area` sowie mit dem bereitgestellten Bereichsnamen (in diesem Fall `Blog`) eine Route. Der Standardwert stellt sicher, dass die Route immer `{ area = Blog, ... }` erzeugt, die Einschränkung erfordert den Wert `{ area = Blog, ... }` für URL-Generierung.

> [!TIP]
> Beim herkömmlichen Routing ist die Reihenfolge wichtig. Routen mit Bereichen werden im Allgemeinen früher in der Routentabelle aufgeführt als die spezifischeren Routen ohne Bereich.

Die Routenwerte im oben genannten Beispiel werden der folgenden Aktion zugeordnet:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

`AreaAttribute` kennzeichnet einen Controller als Teil eines Bereichs, z.B. des Bereichs `Blog`. Controller ohne `[Area]`-Attribut gehören demnach zu keinem Bereich und stimmen **nicht** überein, wenn die `area`-Route wird vom Routing bereitgestellt wird. Im folgenden Beispiel kann nur der erste aufgelistete Controller die Routenwerte `{ area = Blog, controller = Users, action = AddUser }` abgleichen.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> Aus Gründen der Vollständigkeit wird hier der Namespace jedes Controllers dargestellt. Andernfalls gäbe es einen Namenskonflikt zwischen den Controllern, und ein Compilerfehler würde generiert. Klassennamespaces haben keine Auswirkungen auf das MVC-Routing.

Die ersten beiden Controller gehören zu Bereichen und werden können nur abgleichen, wenn ihr jeweiliger Bereichsname vom `area`-Routenwert bereitgestellt wird. Der dritte Controller gehört keinem Bereich an und kann nur abgleichen, wenn vom Routing kein Wert für `area` bereitgestellt wird.

> [!NOTE]
> Im Hinblick auf das Erkennen *keines Werts* hat die Abwesenheit des `area`-Werts dieselben Auswirkungen, wie wenn der Wert für `area` 0 (null) oder eine leere Zeichenfolge wäre.

Beim Ausführen einer Aktion innerhalb eines Bereichs ist der Routenwert für `area` als *Umgebungswert* für das Routing verfügbar und kann für die URL-Generierung verwendet werden. Das bedeutet, dass Bereiche bei der URL-Generierung wie im folgenden Beispiel dargestellt standardmäßig *beständig* sind.
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a>Verstehen von IActionConstraint

> [!NOTE]
> Dieser Abschnitt enthält Details zu Framework-Mechanismen und dazu, wie eine auszuführende Aktion in MVC auswählt wird. Eine typische Anwendung benötigt kein benutzerdefiniertes `IActionConstraint`.

Sie haben `IActionConstraint` wahrscheinlich bereits verwendet, auch wenn Sie mit der Oberfläche noch nicht vertraut sind. Das `[HttpGet]`-Attribut und ähnliche `[Http-VERB]`-Attribute implementieren `IActionConstraint`, um die Ausführung einer Aktionsmethode einzuschränken.

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

Unter Annahme der herkömmlichen Standardroute würde der URL-Pfad `/Products/Edit` die Werte `{ controller = Products, action = Edit }` erzeugen, die **beiden** hier gezeigten Aktionen entsprechen. In `IActionConstraint`-Terminologie würde man sagen, dass es sich bei beiden genannten Aktionen um Kandidaten handelt, da beide den Routendaten entsprechen.

Wenn `HttpGetAttribute` ausgeführt wird, wird angegeben, dass *Edit()* mit *GET* übereinstimmt und mit keinem anderen HTTP-Verb. Für die `Edit(...)`-Aktion wurde keine Einschränkungen definiert, und daher stimmt sie mit allen HTTP-Verben überein. Wenn wir von `POST` ausgehen, stimmt nur `Edit(...)` überein. Für `GET` können jedoch beide Aktionen übereinstimmen. Eine Aktion mit `IActionConstraint` wird einer Aktion ohne jedoch immer *vorgezogen*. Da `Edit()` über `[HttpGet]` verfügt, gilt die Methode als spezifischer und wird ausgewählt, wenn beide Aktionen das Verb erkennen.

Im Prinzip ist `IActionConstraint` eine Form der *Überladung*. Anstatt jedoch Methoden mit demselben Namen zu überladen, führt es zu einer Überladung zwischen Aktionen, die dieselbe URL erkennen. Beim Attributrouting wird auch `IActionConstraint` verwendet. Aus diesem Grund kann es zu Aktionen aus verschiedenen Controllern führen, die beide als Kandidaten gelten.

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a>Implementieren von IActionConstraint

`IActionConstraint` lässt sich am einfachsten erstellen, indem Sie eine abgeleitete Klasse von `System.Attribute` erstellen und sie auf Ihren Aktionen und Controllern platzieren. MVC erkennt automatisch jedes `IActionConstraint`, das als Attribut verwendet wird. Sie können mithilfe des Anwendungsmodells Einschränkungen anwenden. Dies ist wahrscheinlich die flexibelste Herangehensweise, da Sie über Metaprogrammierung festlegen können, wie die Einschränkungen angewendet werden.

Im folgenden Beispiel wählt eine Einschränkung eine Aktion basierend auf einem *Ländercode* aus den Routendaten aus. [Das vollständige Beispiel finden Sie auf GitHub.](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs)

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

Sie sind für die Implementierung der `Accept`-Methode und die Festlegung einer Reihenfolge (Order) verantwortlich, in der die Einschränkung ausgeführt wird. In diesem Fall gibt die `Accept`-Methode `true` zurück, um zu kennzeichnen, dass die Aktion eine Übereinstimmung ist, wenn die `country`-Routenwerte übereinstimmen. Dies unterscheidet sich von `RouteValueAttribute` dahingehend, dass ein Fallback auf eine Aktion ohne Attribute zulässig ist. In dem Beispiel wird gezeigt, dass wenn Sie eine `en-US`-Aktion definieren, ein Ländercode wie `fr-FR` auf einen generischeren Controller zurückgreift, für den `[CountrySpecific(...)]` nicht angewendet wird.

Die `Order`-Eigenschaft entscheidet, zu welcher *Phase* die Einschränkung gehört. Aktionseinschränkungen werden auf Grundlage von `Order` in Gruppen ausgeführt. Beispiel: Alle vom Framework bereitgestellten HTTP-Methodenattribute verwenden denselben `Order`-Wert, sodass sie in derselben Phase ausgeführt werden können. Es kann so viele Phasen geben, wie zur Implementierung der gewünschten Richtlinien notwendig.

> [!TIP]
> Bei der Entscheidung über einen Wert für `Order` sollten Sie berücksichtigen, ob die Einschränkung vor HTTP-Methoden angewendet werden soll oder nicht. Niedrigere Zahlen werden zuerst ausgeführt.

::: moniker-end

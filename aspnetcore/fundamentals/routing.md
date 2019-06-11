---
title: Routing in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, inwiefern das Routing mit ASP.NET Core für das Zuordnen von Anforderungs-URIs zu Endpunktselektoren und das Weiterleiten von Anforderungen an Endpunkte zuständig ist.
ms.author: riande
ms.custom: mvc
ms.date: 02/13/2019
uid: fundamentals/routing
ms.openlocfilehash: 2a7a942f43de94326e84977f09dc9a2e24dd00f0
ms.sourcegitcommit: 5dd2ce9709c9e41142771e652d1a4bd0b5248cec
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692579"
---
# <a name="routing-in-aspnet-core"></a>Routing in ASP.NET Core

Von [Ryan Nowak](https://github.com/rynowak), [Steve Smith](https://ardalis.com/) und [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range="<= aspnetcore-1.1"

Laden Sie die Version 1.1 dieses Artikel unter [Routing in ASP.NET Core (version 1.1, PDF) (Routing in ASP.NET Core (Version 1.1, PDF))](https://webpifeed.blob.core.windows.net/webpifeed/Partners/Routing_1.x.pdf) herunter.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Beim Routing werden Anforderungs-URIs Endpunktselektoren zugeordnet und Anforderungen an Endpunkte weitergeleitet. Routen werden in der App definiert und beim Start der App konfiguriert. Eine Route kann optional Werte aus der URL extrahieren, die in der Anforderung enthalten ist. Diese Werte können anschließend für die Verarbeitung der Anforderung verwendet werden. Mithilfe von Routeninformationen aus der App lassen sich durch das Routing URLs generieren, die Endpunktselektoren zugeordnet werden.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Wenn Sie die aktuellsten Routingszenarios in ASP.NET Core 2.2 verwenden möchten, geben Sie die [Kompatibilitätsversion](xref:mvc/compatibility-version) zur Registrierung der MVC-Dienste in `Startup.ConfigureServices` an:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

Die Option <xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> bestimmt, ob für das Routing intern endpunktbasierte Logik oder <xref:Microsoft.AspNetCore.Routing.IRouter>-basierte Logik von ASP.NET Core 2.1 oder früher verwendet werden soll. Wenn die Kompatibilitätsversion auf 2.2 oder höher festgelegt ist, lautet der Standardwert `true`. Legen Sie für den Wert `false` fest, um die vorherige Routinglogik zu verwenden:

```csharp
// Use the routing logic of ASP.NET Core 2.1 or earlier:
services.AddMvc(options => options.EnableEndpointRouting = false)
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

Weitere Informationen zum <xref:Microsoft.AspNetCore.Routing.IRouter>-basierten Routing finden Sie in dem [Artikel zur Version 2.1 von ASP.NET Core](/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Beim Routing werden Anforderungs-URIs Routenhandlern zugeordnet und Anforderungen weitergeleitet. Routen werden in der App definiert und beim Start der App konfiguriert. Eine Route kann optional Werte aus der URL extrahieren, die in der Anforderung enthalten ist. Diese Werte können anschließend für die Verarbeitung der Anforderung verwendet werden. Unter Verwendung von konfigurierten Routen aus der App können durch das Routing URLs erstellt werden, die Routenhandlern zugeordnet werden.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Wenn Sie die aktuellsten Routingszenarios in ASP.NET Core 2.1 verwenden möchten, geben Sie die [Kompatibilitätsversion](xref:mvc/compatibility-version) zur Registrierung der MVC-Dienste in `Startup.ConfigureServices` an:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

::: moniker-end

> [!IMPORTANT]
> In diesem Artikel wird das Low-Level-Routing in ASP.NET Core beschrieben. Weitere Informationen zum Routing mit ASP.NET Core MVC finden Sie unter <xref:mvc/controllers/routing>. Weitere Informationen zu Routingkonventionen in Razor Pages finden Sie unter <xref:razor-pages/razor-pages-conventions>.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="routing-basics"></a>Routinggrundlagen

Für die meisten Apps sollte eine grundlegendes und beschreibendes Routingschema ausgewählt werden, um lesbare und aussagekräftige URLs zu erhalten. Für die konventionelle Standardroute `{controller=Home}/{action=Index}/{id?}` gilt:

* Sie unterstützt ein grundlegendes und beschreibendes Routingschema.
* Sie stellt einen nützlichen Startpunkt für benutzeroberflächenbasierte Apps dar.

Entwickler fügen in der Regel in speziellen Situationen (z. B. bei Blog- und E-Commerce-Endpunkten) durch [Attributrouting](xref:mvc/controllers/routing#attribute-routing) oder dedizierte konventionelle Routen zusätzliche kurze Routen zu stark frequentierten Bereichen der App hinzu.

Web-APIs sollten das Attributrouting verwenden, um die Funktionalität der App als einen Satz von Ressourcen zu modellieren, bei denen Vorgänge durch HTTP-Verben dargestellt werden. Dies bedeutet, dass viele Vorgänge (z.B. GET, POST) für dieselbe logische Ressource dieselbe URL verwenden. Das Attributrouting bietet eine Ebene der Steuerung, die für einen sorgfältigen Entwurf des öffentlichen Endpunktlayouts einer API erforderlich ist.

Razor Pages-Apps verwenden konventionelles Standardrouting, um benannte Ressourcen im *Razor*-Ordner einer App bereitzustellen. Außerdem gibt es weitere Konventionen zum Anpassen des Routingverhaltens für Razor Pages. Weitere Informationen finden Sie unter <xref:razor-pages/index> und <xref:razor-pages/razor-pages-conventions>.

Die Unterstützung zum Generieren von URLs ermöglicht es, die App ohne Hartcodierung der URLs zum Verlinken der App zu entwickeln. Auf diese Weise kann mit einer grundlegenden Routingkonfiguration begonnen werden, und die Routen können geändert werden, wenn das Ressourcenlayout der App festgelegt wurde.

::: moniker range=">= aspnetcore-2.2"

Es werden *Endpunkte* (`Endpoint`) für das Routing verwendet, um logische Endpunkte in einer App darzustellen.

Ein Endpunkt definiert einen Delegaten zum Verarbeiten von Anforderungen und eine Sammlung von beliebigen Metadaten. Die Metadaten werden zur Implementierung von übergreifenden Belangen verwendet, die auf Richtlinien und der Konfiguration basieren, die den einzelnen Endpunkten angefügt sind.

Das Routingsystem weist die folgenden Eigenschaften auf:

* Die Syntax der Routenvorlage wird zum Definieren von Routen mit Routenparametern verwendet, die mit Token versehen sind.
* Sowohl die konventionelle Endpunktkonfiguration als auch die Endpunktkonfiguration mit Attributen ist zulässig.
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> wird verwendet, um zu bestimmen, ob ein URL-Parameter einen gültigen Wert für eine bestimmte Endpunkteinschränkung enthält.
* App-Modelle wie MVC bzw. Razor Pages registrieren sämtliche ihrer Endpunkte, für die die Implementierung von Routingszenarios vorhersagbar ist.
* Die Routingimplementierung trifft Routingentscheidungen, wenn diese in der Middlewarepipeline gewünscht sind.
* Middleware, die nach Routingmiddleware angezeigt wird, kann das Ergebnis der Endpunktentscheidung der Routingmiddleware untersuchen, die für einen bestimmten Anforderungs-URI getroffen wurde.
* Alle diese Endpunkte können an einer beliebigen Stelle in der Middlewarepipeline in der App aufgelistet werden.
* Apps können mithilfe von Routing URLs generieren (z. B. für Umleitungen oder Links), die auf Endpunktinformationen basieren. Dadurch wird die Erstellung von hartcodierten URLs verhindert, was wiederrum die Wartbarkeit verbessert.
* Die URL-Generierung basiert auf Adressen, die beliebige Erweiterbarkeit unterstützen:

  * Die API zur Linkgenerierung (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>) kann mithilfe von [Dependency Injection](xref:fundamentals/dependency-injection) an jeder beliebigen Stelle aufgelöst werden, um URLs zu generieren.
  * Wenn diese API nicht über Dependency Injection verfügbar ist, bietet <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> Methoden zum Erstellen von URLs.

> [!NOTE]
> Mit der Freigabe des Endpunktroutings in ASP.NET Core 2.2 wird die Endpunktverknüpfung auf MVC- bzw. Razor Pages-Aktionen und -Seiten eingeschränkt. Zukünftige Releases sollen Erweiterungen für Funktionen zur Verknüpfung von Endpunkten enthalten.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Beim Routing werden *Routen* (Implementierungen von <xref:Microsoft.AspNetCore.Routing.IRouter>) für Folgendes verwendet:

* Zuordnen von eingehenden Anforderungen zu *Routenhandlern*
* Generieren von URLs für Antworten

Eine App verfügt standardmäßig über genau eine Routensammlung. Wenn eine Anforderung eingeht, werden die Routen in der Sammlung in der Reihenfolge verarbeitet, in der sie in der Sammlung aufgelistet werden. Dann versucht das Framework, eine eingehende Anforderungs-URL einer Route in der Sammlung zuzuordnen, indem es die <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*>-Methode für jede Route in der Sammlung aufruft. Bei einer Antwort kann Routing verwendet werden, um URLs zu generieren (z.B. für Umleitungen oder Links), die auf Routeninformationen basieren. Dadurch wird die Erstellung von hartcodierten URLs verhindert, was wiederrum die Wartbarkeit verbessert.

Das Routingsystem weist die folgenden Eigenschaften auf:

* Die Syntax der Routenvorlage wird zum Definieren von Routen mit Routenparametern verwendet, die mit Token versehen sind.
* Sowohl die konventionelle Endpunktkonfiguration als auch die Endpunktkonfiguration mit Attributen ist zulässig.
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> wird verwendet, um zu bestimmen, ob ein URL-Parameter einen gültigen Wert für eine bestimmte Endpunkteinschränkung enthält.
* App-Modelle wie MVC bzw Razor Pages registrieren sämtliche ihrer Routen, für die die Implementierung von Routingszenarios vorhersagbar ist.
* Bei einer Antwort kann Routing verwendet werden, um URLs zu generieren (z.B. für Umleitungen oder Links), die auf Routeninformationen basieren. Dadurch wird die Erstellung von hartcodierten URLs verhindert, was wiederrum die Wartbarkeit verbessert.
* Die URL-Generierung basiert auf Routen, die beliebige Erweiterbarkeit unterstützen. <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> bietet Methoden zum Erstellen von URLs.

::: moniker-end

Die Routingfunktionalität wird über die Klasse <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> mit der [Middlewarepipeline](xref:fundamentals/middleware/index) verbunden. [ASP.NET Core-MVC](xref:mvc/overview) fügt im Rahmen der Konfiguration die Routingfunktionalität der Middlewarepipeline hinzu und verarbeitet das Routing in MVC- und Razor Pages-Apps. Informationen darüber, wie Sie Routing als eigenständige Komponente verwenden, finden Sie im Abschnitt [Verwenden von Routingmiddleware](#use-routing-middleware).

### <a name="url-matching"></a>URL-Zuordnung

::: moniker range=">= aspnetcore-2.2"

Bei einer URL-Zuordnung werden eingehende Anforderungen durch Routing an einen *Endpunkt* gesendet. Für diesen Prozess werden die Daten des URL-Pfads verwendet. Es können jedoch auch alle Daten der Anforderung genutzt werden. Für die Skalierung der Größe und Komplexität einer App ist das Versenden von Anforderungen an unterschiedliche Handler entscheidend.

Das Routingsystem ist beim Endpunktrouting für alle Weiterleitungsentscheidungen zuständig. Da die Middleware basierend auf dem ausgewählten Endpunkt Richtlinien anwendet, ist es wichtig, dass jede Entscheidung, die die Weiterleitung oder die Anwendung von Sicherheitsrichtlinien beeinträchtigen kann, innerhalb des Routingsystems getroffen wird.

Wenn der Endpunktdelegat ausgeführt wird, werden die Eigenschaften von [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) auf Grundlage der bisher verarbeiteten Anforderungen auf entsprechende Werte festgelegt.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Bei einer URL-Zuordnung werden eingehende Anforderungen durch Routing an einen *Handler* gesendet. Für diesen Prozess werden die Daten des URL-Pfads verwendet. Es können jedoch auch alle Daten der Anforderung genutzt werden. Für die Skalierung der Größe und Komplexität einer App ist das Versenden von Anforderungen an unterschiedliche Handler entscheidend.

Eingehende Anforderungen werden vom <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>-Objekt bearbeitet, das die Methode <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> für alle Routen nacheinander aufruft. In der <xref:Microsoft.AspNetCore.Routing.IRouter>-Instanz wird entschieden, ob die Anforderung *verarbeitet* wird, indem für den [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) ein <xref:Microsoft.AspNetCore.Http.RequestDelegate>-Delegat festgelegt wird, der nicht NULL ist. Wenn von einer Route ein Handler für die Anforderung festgelegt wird, endet die Routenverarbeitung, und der Handler wird zur Verarbeitung der Anforderung aufgerufen. Wenn kein Routenhandler gefunden wird, um die Anforderung zu verarbeiten, leitet die Middleware die Anforderung an die nächste Middleware in der Anforderungspipeline weiter.

Die Haupteingabe für <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> ist der [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*), der der aktuellen Anforderung zugeordnet ist. [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) und [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) sind Ausgaben, die festgelegt werden, nachdem eine Route zugeordnet wurde.

Eine Zuordnung, die <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> aufruft, legt außerdem die Eigenschaften von [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) auf Grundlage der bisher verarbeiteten Anforderungen auf entsprechende Werte fest.

::: moniker-end

[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) ist ein Wörterbuch mit *Routenwerten*, das aus der Route erstellt wird. Die Werte werden in der Regel durch die Tokenisierung der URL ermittelt und können so verwendet werden, dass Benutzereingaben akzeptiert oder weitere Entscheidungen in der App zum Versenden von Anforderungen getroffen werden.

[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) ist eine Eigenschaftensammlung mit zusätzlichen Daten zur zugeordneten Route. <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> werden bereitgestellt, damit sich jeder Route Zustandsdaten zuordnen lassen, sodass in der App später Entscheidungen auf Grundlage der zugeordneten Route getroffen werden können. Diese Werte werden vom Entwickler vorgegeben und beeinflussen das Routingverhalten **in keiner Weise**. Außerdem können im Gegensatz zu [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), die sich in bzw. aus Zeichenfolgen konvertieren lassen müssen, Werte in [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) einem beliebigen Typ entsprechen.

[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) ist eine Liste der Routen, die an der Zuordnung der Anforderung beteiligt waren. Routen können in anderen Routen geschachtelt werden. Die <xref:Microsoft.AspNetCore.Routing.RouteData.Routers>-Eigenschaft stellt den Pfad mithilfe der logischen Routenstruktur dar, die zu der Zuordnung geführt hat. Üblicherweise ist das erste Element in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> die Routensammlung. Dieses sollte zur URL-Generierung verwendet werden. Das letzte Element in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> ist der Routenhandler, für den eine Zuordnung vorgenommen wurde.

<a name="lg"></a>

### <a name="url-generation-with-linkgenerator"></a>URL-Generierung mit LinkGenerator

::: moniker range=">= aspnetcore-2.2"

Bei der URL-Generierung wird durch Routing ein URL-Pfad basierend auf mehreren Routenwerten erstellt. Dies ermöglicht eine logische Trennung zwischen den Endpunkten und den URLs, die auf diese zugreifen.

Das Endpunktrouting umfasst die API zur Linkgenerierung (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>). <xref:Microsoft.AspNetCore.Routing.LinkGenerator> ist ein Singletondienst, der über Dependency Injection abgerufen werden kann. Die API kann außerhalb des Kontexts einer ausgeführten Anforderung verwendet werden. <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> und Szenarios für MVC, die von <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> abhängig sind (z. B. [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro), HTML-Hilfsprogramme und [Aktionsergebnisse](xref:mvc/controllers/actions)), verwenden die API zur Linkgenerierung, um entsprechende Funktionen bereitzustellen.

Die API zur Linkgenerierung wird von Konzepten wie *Adressen* und *Adressschemas* unterstützt. Sie können mithilfe eines Adressschemas die Endpunkte bestimmen, die bei der Linkgenerierung berücksichtigt werden sollen. Beispielsweise werden Routennamen und Routenwerte als Adressschemas implementiert. Diese Szenarios kennen viele Benutzer von MVC bzw. Razor Pages.

Die API zur Linkgenerierung kann MVC- bzw. Razor Pages-Aktionen und Seiten über die folgenden Erweiterungsmethoden miteinander verknüpfen:

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

Beim Überladen dieser Methoden werden Argumente akzeptiert, die den `HttpContext` umfassen. Diese Methoden sind zwar in funktionaler Hinsicht äquivalent zu `Url.Action` und `Url.Page`, bieten aber zusätzliche Flexibilität und Optionen.

Die `GetPath*`-Methoden sind `Url.Action` und `Url.Page` in der Hinsicht ähnlich, dass sie einen URI mit einem absoluten Pfad generieren. Die `GetUri*`-Methoden generieren immer einen absoluten URI mit einem Schema und einem Host. Die Methoden, die einen `HttpContext` akzeptieren, generieren im Kontext der ausgeführten Anforderung einen URI. Die Umgebungsroutenwerte, der URL-basierte Pfad, das Schema und der Host von der ausführenden Anforderung werden so lange verwendet, bis sie außer Kraft gesetzt werden.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator> wird mit einer Adresse aufgerufen. Ein URI wird in zwei Schritten generiert:

1. Eine Adresse wird an eine Liste von Endpunkten gebunden, die der Adresse zugeordnet werden können.
1. Jedes `RoutePattern` eines Endpunkts wird bewertet, bis ein Routenmuster gefunden wird, das den angegebenen Werten zugeordnet werden kann. Die daraus resultierende Ausgabe wird mit URI-Teilen kombiniert, die für die API zur Linkgenerierung bereitgestellt wird, und zurückgegeben.

Die von <xref:Microsoft.AspNetCore.Routing.LinkGenerator> bereitgestellten Methoden unterstützen die Standardfunktionen zur Generierung von Links für jeden beliebigen Adresstypen. Am praktischsten ist es, die API zur Linkgenerierung mit Erweiterungsmethoden zu verwenden, die Vorgänge für einen bestimmten Adresstypen ausführen.

| Erweiterungsmethode   | Beschreibung                                                         |
| ------------------ | ------------------------------------------------------------------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Generiert einen URI mit einem absoluten Pfad, der auf den angegebenen Werten basiert. |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Generiert einen absoluten URI, der auf den angegebenen Werten basiert.             |

> [!WARNING]
> Beachten Sie die folgenden Implikationen zum Aufrufen von <xref:Microsoft.AspNetCore.Routing.LinkGenerator>-Methoden:
>
> * Verwenden Sie `GetUri*`-Erweiterungsmethoden in App-Konfigurationen, die den `Host`-Header von eingehenden Anforderungen nicht überprüfen, mit Bedacht. Wenn der `Host`-Header von eingehenden Anforderungen nicht überprüft wird, können nicht vertrauenswürdige Anforderungseingaben zurück an den Client in URIs einer Ansicht bzw. Seite zurückgesendet werden. Es wird empfohlen, dass alle Produktions-Apps ihren Server so konfigurieren, dass der `Host`-Header auf bekannte gültige Werte überprüft wird.
>
> * Verwenden Sie <xref:Microsoft.AspNetCore.Routing.LinkGenerator> in Kombination mit `Map` oder `MapWhen` in Middleware mit Bedacht. `Map*` ändert den Basispfad der ausgeführten Anforderung. Dies beeinflusst die Ausgabe der Linkgenerierung. Für alle <xref:Microsoft.AspNetCore.Routing.LinkGenerator>-APIs ist die Angabe eines Basispfads zulässig. Geben Sie immer einen leeren Basispfad an, um den Einfluss von `Map*` auf die Linkgenerierung rückgängig zu machen.

## <a name="differences-from-earlier-versions-of-routing"></a>Unterschiede im Vergleich zu früheren Routingversionen

Es gibt einige Unterschiede zwischen dem Endpunktrouting in ASP.NET Core 2.2 oder höher und dem Routing in früheren Versionen von ASP.NET Core:

* Das Endpunktroutingsystem unterstützt die <xref:Microsoft.AspNetCore.Routing.IRouter>-basierte Erweiterbarkeit einschließlich des Erbens von <xref:Microsoft.AspNetCore.Routing.Route> nicht.

* Das Endpunktrouting unterstützt [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) nicht. Verwenden Sie die [Kompatibilitätsversion](xref:mvc/compatibility-version) für 2.1 (`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`), um den Kompatibilitätsshim weiter zu verwenden.

* Das Verhalten des Endpunktroutings für die Schreibweise von generierten URIs ist bei der Verwendung von konventionellen Routen anders.

  Sehen Sie sich die folgende Standardvorlage für Routen an:

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  Angenommen, Sie generieren mithilfe der folgenden Route einen Link zu einer Aktion:

  ```csharp
  var link = Url.Action("ReadPost", "blog", new { id = 17, });
  ```

  Bei <xref:Microsoft.AspNetCore.Routing.IRouter>-basierten Routing generiert dieser Code einen URI von `/blog/ReadPost/17`, der die Schreibweise des angegebenen Routenwerts berücksichtigt. Durch das Endpunktrouting in ASP.NET Core 2.2 oder höher wird `/Blog/ReadPost/17` hergestellt („Blog“ wird großgeschrieben). Über das Endpunktrouting wird die `IOutboundParameterTransformer`-Schnittstelle bereitgestellt, die verwendet werden kann, um dieses Verhalten entweder global anzupassen oder auf unterschiedliche Konventionen zum Zuordnen von URLs anzuwenden.

  Weitere Informationen finden Sie im Abschnitt [Parametertransformatorreferenz](#parameter-transformer-reference).

* Das Verhalten der von MVC bzw. Razor Pages mit konventionellen Routen verwendeten Linkgenerierung unterscheidet sich, wenn versucht wird, einen Controller bzw. eine Aktion oder Seite zu verknüpfen, die nicht vorhanden ist.

  Sehen Sie sich die folgende Standardvorlage für Routen an:

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  Angenommen, Sie generieren mithilfe der Standardvorlage wie folgt einen Link zu einer Aktion:

  ```csharp
  var link = Url.Action("ReadPost", "Blog", new { id = 17, });
  ```

  Bei `IRouter`-basiertem Routing ist das Ergebnis immer `/Blog/ReadPost/17`, auch wenn `BlogController` nicht vorhanden ist oder keine Aktionsmethode für `ReadPost` aufweist. Erwartungsgemäß wird durch das Endpunktrouting in ASP.NET Core 2.2 oder höher `/Blog/ReadPost/17` erstellt, wenn die Aktionsmethode vorhanden ist. *Wenn die Aktionsmethode allerdings nicht vorhanden ist, wird beim Endpunktrouting eine leere Zeichenfolge erstellt.* Vom Konzept her wird beim Endpunktrouting nicht angenommen, dass der Endpunkt vorhanden ist, wenn es die Aktion nicht gibt.

* Das Verhalten des *Algorithmus zum Aufheben der Gültigkeit von Umgebunsgwerten* bei der Linkgenerierung ist anders, wenn dieser im Zusammenhang mit dem Endpunktrouting verwendet wird.

  Anhand des Algorithmus *zum Aufheben der Gültigkeit von Umgebungswerten* wird entschieden, welche Routenwerte der zu diesem Zeitpunkt ausgeführten Anforderung (die Umgebunsgwerte) für die Linkgenerierung verwendet werden können. Beim konventionellen Routing wurde stets die Gültigkeit von zusätzlichen Routenwerten aufgehoben, wenn eine Verknüpfung zu einer anderen Aktion hergestellt wurde. Beim Attributrouting ist dieses Verhalten vor dem Release von ASP.NET Core 2.2 nicht aufgetreten. In früheren Versionen von ASP.NET Core wurden beim Herstellen von Verknüpfungen mit einer anderen Aktion, die dieselben Namen für Routenparameter verwendete, ein Fehler bei der Linkgenerierung ausgelöst. In ASP.NET Core 2.2 oder höher wird bei beiden Arten des Routings die Gültigkeit von Werten aufgehoben, wenn eine Verknüpfung zu einer anderen Aktion hergestellt wird.

  Sehen Sie sich das folgende Beispiel in ASP.NET Core 2.1 oder früher an. Wenn eine Verknüpfung zu einer anderen Aktion (oder Seite) hergestellt wird, können Routenwerte auf unerwünschte Weise wiederverwendet werden.

  In */Pages/Store/Product.cshtml*:

  ```cshtml
  @page "{id}"
  @Url.Page("/Login")
  ```

  In */Pages/Login.cshtml*:

  ```cshtml
  @page "{id?}"
  ```

  Wenn der URI in ASP.NET Core 2.1 oder früher `/Store/Product/18` ist, ist der Link, der von `@Url.Page("/Login")` auf der Speicher- bzw. Informationsseite generiert wird, `/Login/18`. Der `id`-Wert von 18 wird wiederverwendet, obwohl das Ziel des Links ein vollkommen anderer Teil der App ist. Bei dem `id`-Routenwert im Kontext der `/Login`-Seite handelt es sich wahrscheinlich um den Wert einer Benutzer-ID und nicht um einen Wert einer Speicherprodukt-ID.

  Beim Endpunktrouting mit ASP.NET Core 2.2 oder höher lautet das Ergebnis `/Login`. Umgebungswerte werden nicht wiederverwendet, wenn das verknüpfte Ziel eine andere Aktion oder Seite ist.

* Syntax für Routenparameter für Roundtrips: Schrägstriche werden nicht codiert, wenn eine Syntax mit zwei Sternchen (`**`) für Catch-All-Parameter verwendet wird.

  Bei der Linkgenerierung codiert das Routingsystem mit Ausnahme von Schrägstrichen den Wert in einem Catch-All-Parameter, dem zwei Sternchen voranstehen (`**`), z. B. `{**myparametername}`. Der Catch-All-Parameter mit zwei Sternchen wird beim `IRouter`-basierten Routing in ASP.NET Core 2.2 oder höher unterstützt.

  Die Syntax mit einem Sternchen für Catch-All-Parameter in früheren Versionen von ASP.NET Core (`{*myparametername}`) wird weiter unterstützt, und Schrägstriche werden codiert.

  | Route              | Link generiert mit:<br>`Url.Action(new { category = "admin/products" })`&hellip; |
  | ------------------ | --------------------------------------------------------------------- |
  | `/search/{*page}`  | `/search/admin%2Fproducts` (der Schrägstrich wird codiert)             |
  | `/search/{**page}` | `/search/admin/products`                                              |

### <a name="middleware-example"></a>Middlewarebeispiel

Im folgenden Beispiel verwendet eine Middleware die <xref:Microsoft.AspNetCore.Routing.LinkGenerator>-API, um eine Verknüpfung zu einer Aktionsmethode herzustellen, die Speicherprodukte aufführt. Sie können für jede beliebige Klasse in einer App die API zur Linkgenerierung verwenden, indem Sie diese in eine Klasse einfügen und `GenerateLink` aufrufen.

```csharp
using Microsoft.AspNetCore.Routing;

public class ProductsLinkMiddleware
{
    private readonly LinkGenerator _linkGenerator;

    public ProductsLinkMiddleware(RequestDelegate next, LinkGenerator linkGenerator)
    {
        _linkGenerator = linkGenerator;
    }

    public async Task InvokeAsync(HttpContext httpContext)
    {
        var url = _linkGenerator.GetPathByAction("ListProducts", "Store");

        httpContext.Response.ContentType = "text/plain";

        await httpContext.Response.WriteAsync($"Go to {url} to see our products.");
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Bei der URL-Generierung wird durch Routing ein URL-Pfad basierend auf mehreren Routenwerten erstellt. Dies ermöglicht eine logische Trennung zwischen den Routenhandlern und den URLs, die auf diese zugreifen.

Auch für die URL-Generierung wird ein iterativer Prozess verwendet, bei dem allerdings zuerst vom Benutzer- oder Frameworkcode die <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*>-Methode der Routensammlung aufgerufen wird. Nacheinander wird für jede *Route* die zugehörige <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*>-Methode aufgerufen, bis ein <xref:Microsoft.AspNetCore.Routing.VirtualPathData>-Objekt zurückgegeben wird, dessen Wert nicht NULL ist.

Die primären Eingaben für <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> sind:

* [VirtualPathContext.HttpContext](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext)
* [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values)
* [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues)

Für Routen werden überwiegend die Routenwerte verwendet, die von <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> und <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> bereitgestellt werden. Dadurch wird ermittelt, ob eine URL generiert werden kann und welche Werte diese enthalten soll. <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> sind Routenwerte, die durch die Zuordnung zur aktuellen Anforderung erstellt wurden. Im Gegensatz dazu sind <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> die Routenwerte, die angeben, wie die gewünschte URL für den aktuellen Vorgang generiert werden soll. <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext> wird bereitgestellt, falls für eine Route Dienste oder zusätzliche Daten, die mit dem aktuellen Kontext verknüpft sind, abgerufen werden müssen.

> [!TIP]
> [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) kann als Menge von überschriebenen Eigenschaften für [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*) betrachtet werden. Bei der URL-Generierung wird versucht, Routenwerte der aktuellen Anforderung wiederzuverwenden, um so URLs für Links generieren zu können, die dieselbe Route oder dieselben Routenwerte verwenden.

Die Ausgabe von <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> ist ein <xref:Microsoft.AspNetCore.Routing.VirtualPathData>-Objekt. <xref:Microsoft.AspNetCore.Routing.VirtualPathData> verhält sich analog zu <xref:Microsoft.AspNetCore.Routing.RouteData>. <xref:Microsoft.AspNetCore.Routing.VirtualPathData> enthält das <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath>-Objekt für die Ausgabe-URL und einige zusätzlichen Eigenschaften, die von der Route festgelegt werden sollten.

Die [VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*)-Eigenschaft enthält den *virtuellen Pfad*, der von der Route erstellt wird. Je nach Anforderungen muss der Pfad eventuell noch weiter verarbeitet werden. Wenn die generierte URL in HTML gerendert werden soll, müssen Sie den App-Basispfad voranstellen.

[VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) ist ein Verweis auf die Route, mit der die URL generiert wurde.

Die [VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*)-Eigenschaften stellen ein Wörterbuch mit zusätzlichen Daten zur Route dar, mit der die URL generiert wurde. Diese Eigenschaften verhalten sich analog zu [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*).

::: moniker-end

### <a name="create-routes"></a>Erstellen von Routen

::: moniker range="< aspnetcore-2.2"

Für die Routingfunktionalität wird die <xref:Microsoft.AspNetCore.Routing.Route>-Klasse als Standardimplementierung von <xref:Microsoft.AspNetCore.Routing.IRouter> zur Verfügung gestellt. <xref:Microsoft.AspNetCore.Routing.Route> verwendet die Syntax für *Routenvorlagen*, um Muster zu definieren, die dem URL-Pfad zugeordnet werden können, wenn <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> aufgerufen wird. <xref:Microsoft.AspNetCore.Routing.Route> nutzt dieselbe Routenvorlage zum Generieren einer URL, wenn <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> aufgerufen wird.

::: moniker-end

Die meisten Apps erstellen Routen, indem sie <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> oder eine ähnliche Erweiterungsmethode aufrufen, die in <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> definiert ist. All diese <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>-Erweiterungsmethoden erstellen eine Instanz von <xref:Microsoft.AspNetCore.Routing.Route> und fügen diese der Routensammlung hinzu.

::: moniker range=">= aspnetcore-2.2"

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> akzeptiert keine Routenhandlerparameter. Durch <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> werden nur Routen hinzugefügt, die vom <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> verarbeitet werden. Weitere Informationen zum Routing in MVC finden Sie unter <xref:mvc/controllers/routing>.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> akzeptiert keine Routenhandlerparameter. Durch <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> werden nur Routen hinzugefügt, die vom <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> verarbeitet werden. Der Standardhandler ist ein `IRouter`. Möglicherweise verarbeitet der Handler die Anforderung nicht. Beispielsweise ist ASP.NET Core MVC üblicherweise als Standardhandler konfiguriert, der nur Anforderungen verarbeitet, die mit einem verfügbaren Controller und einer Aktion übereinstimmen. Weitere Informationen zum Routing in MVC finden Sie unter <xref:mvc/controllers/routing>.

::: moniker-end

Im folgenden Codebeispiel wird ein <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>-Aufruf dargestellt, der von einer typischen ASP.NET Core MVC-Routendefinition verwendet wird:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Diese Vorlage ordnet einen URL-Pfad zu und extrahiert die Routenwerte. Beispielsweise generiert der Pfad `/Products/Details/17` die folgenden Routenwerte: `{ controller = Products, action = Details, id = 17 }`.

Routenwerte werden ermittelt, indem der URL-Pfad in Segmente aufgeteilt wird und jedes Segment mit dem Namen des *Routenparameters* in der Routenvorlage abgeglichen wird. Jeder Routenparameter hat einen Namen. Die Parameter werden von Klammern `{ ... }` eingeschlossen und dadurch definiert.

Die obige Vorlage kann auch dem URL-Pfad `/` zugeordnet werden und daraus die Werte `{ controller = Home, action = Index }` generieren. Dies liegt daran, dass die Routenparameter `{controller}` und `{action}` über Standardwerte verfügen und der Routenparameter `id` optional ist. Hinter dem Namen des Routenparameters steht das Gleichheitszeichen (`=`), auf das der Wert folgt, der als Standardwert für den Parameter definiert wird. Durch ein Fragezeichen (`?`) nach dem Namen des Routenparameters wird ein optionaler Parameter definiert.

Durch Routenparameter mit einem Standardwert wird *immer* ein Routenwert erzeugt, wenn eine Übereinstimmung für die Route ermittelt wird. Bei optionalen Parametern wird kein Routenwert generiert, wenn kein entsprechendes URL-Pfadsegment vorhanden ist. Eine ausführliche Beschreibung zu den Szenarios und zur Syntax der Routenvorlage finden Sie unter [Referenz zu Routenvorlagen](#route-template-reference).

Im folgenden Beispiel definiert die Routenparameterdefinition `{id:int}` eine [Routeneinschränkung](#route-constraint-reference) für den Routenparameter `id`:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

Durch diese Vorlage wird bei einem Abgleich beispielsweise der URL-Pfad `/Products/Details/17`, aber nicht `/Products/Details/Apples` gefunden. In derartigen Einschränkungen wird <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> implementiert, und die Routenwerte werden auf Gültigkeit geprüft. Im obigen Beispiel muss sich der Routenwert `id` in einen Integer konvertieren lassen. Weitere Informationen zu Routeneinschränkungen, die vom Framework bereitgestellt werden, finden Sie in der [Referenz zu Routeneinschränkungen](#route-constraint-reference).

Bei zusätzlichen Überladungen von <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> werden Werte für `constraints`, `dataTokens` und `defaults` akzeptiert. Üblicherweise werden diese Parameter so verwendet, dass ein Objekt eines anonymen Typs übergeben wird, in dem die Eigenschaftsnamen des anonymen Typs mit den Routenparameternamen abgeglichen werden.

In den folgenden <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>-Beispielen werden identische Routen erstellt:

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> Für einfache Routen eignet sich die Inline-Syntax zum Definieren von Einschränkungen und Standardwerten. Bestimmte Szenarios wie die Verwendung von Datentoken werden von dieser allerdings nicht unterstützt.

Das folgende Beispiel veranschaulicht einige weitere Szenarios:

::: moniker range=">= aspnetcore-2.2"

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{**article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

Diese Vorlage kann einem URL-Pfad wie `/Blog/All-About-Routing/Introduction` zugeordnet werden. Sie extrahier die `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`-Werte. Die Standardroutenwerte für `controller` und `action` werden von der Route generiert, obwohl keine entsprechenden Routenparameter in der Vorlage vorhanden sind. Standardwerte können in der Routenvorlage angegeben werden. Der Routenparameter `article` wird durch zwei Sternchen (`**`) vor dem zugehörigen Namen als *Catch-All-Parameter* definiert. So wird der verbleibende Teil des URL-Pfads erfasst, und auch leere Zeichenfolgen können gefunden werden.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{*article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

Diese Vorlage kann einem URL-Pfad wie `/Blog/All-About-Routing/Introduction` zugeordnet werden. Sie extrahier die `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`-Werte. Die Standardroutenwerte für `controller` und `action` werden von der Route generiert, obwohl keine entsprechenden Routenparameter in der Vorlage vorhanden sind. Standardwerte können in der Routenvorlage angegeben werden. Der Routenparameter `article` wird durch ein Sternchen (`*`) vor dem zugehörigen Namen als *Catch-All-Parameter* definiert. So wird der verbleibende Teil des URL-Pfads erfasst, und auch leere Zeichenfolgen können gefunden werden.

::: moniker-end

Im folgenden Beispiel werden Routeneinschränkungen und Datentoken hinzugefügt:

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

Diese Vorlage kann einem URL-Pfad wie `/en-US/Products/5` zugeordnet werden, und die `{ controller = Products, action = Details, id = 5 }`-Werte und `{ locale = en-US }`-Datentoken werden extrahiert.

![Gebietsschemas, Windows-Tokens](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>Generieren der URL zu einer Routenklasse

Durch die Kombination von mehreren Routenwerten und der zugehörigen Routenvorlage kann die <xref:Microsoft.AspNetCore.Routing.Route>-Klasse auch URLs generieren. Dieser Prozess verläuft umgekehrt zum Abgleich eines URL-Pfads.

> [!TIP]
> Wenn Sie die URL-Generierung besser nachvollziehen möchten, sollten Sie sich überlegen, welche URL generiert werden soll und wie diese mithilfe der Routenvorlage abgeglichen wird. Dabei sollten Sie auch darüber nachdenken, welche Werte erstellt werden. Dieser Vorgang entspricht in etwa der URL-Generierung in der <xref:Microsoft.AspNetCore.Routing.Route>-Klasse.

Im folgenden Beispiel wird eine allgemeine Standardroute für ASP.NET Core-MVC verwendet:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Mithilfe der Routenwerte `{ controller = Products, action = List }` wird die URL `/Products/List` generiert. Die Routenwerte werden als Ersatz für die entsprechenden Routenparameter verwendet, wodurch ein URL-Pfad erstellt werden kann. Da es sich bei `id` um einen optionalen Routenparameter handelt, wird die URL erfolgreich ohne einen Wert für `id` generiert.

Mithilfe der Routenwerte `{ controller = Home, action = Index }` wird die URL `/` generiert. Die bereitgestellten Routenwerte entsprechen den Standardwerten, sodass die Segmente, die mit diesen Werten übereinstimmen, problemlos ausgelassen werden können.

Mit beiden generierten URLs (`/Home/Index` und `/`) und dieser Routendefinition wird ein Roundtrip ausgeführt, und es werden dieselben Routenwerte erstellt, die auch zur Generierung der URL verwendet wurden.

> [!NOTE]
> Eine auf ASP.NET Core MVC basierende App sollte zur Generierung von URLs die Routingfunktion nicht direkt aufrufen, sondern <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> verwenden.

Weitere Informationen zur Generierung von URLs finden Sie im Abschnitt [Referenz zur URL-Generierung](#url-generation-reference).

## <a name="use-routing-middleware"></a>Verwenden von Routingmiddleware

Legen Sie das [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) in der Projektdatei der App als Verweis fest.

Fügen Sie anschließend dem Dienstcontainer in `Startup.ConfigureServices` die Routingfunktionalität hinzu:

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

Routen müssen in der `Startup.Configure`-Methode konfiguriert werden. In der Beispiel-App werden die folgenden APIs verwendet:

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>: Nur HTTP-GET-Anforderungen werden abgeglichen.
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

In der folgenden Tabelle werden die Antworten mit den angegebenen URIs aufgelistet:

| URI                    | Antwort                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | Hallo! Route values: [operation, create], [id, 3] |
| `/package/track/-3`    | Hallo! Route values: [operation, track], [id, -3] |
| `/package/track/-3/`   | Hallo! Route values: [operation, track], [id, -3] |
| `/package/track/`      | The request falls through, no match. (Die Anforderung ist fehlgeschlagen, keine Übereinstimmung.)              |
| `GET /hello/Joe`       | Hi, Joe!                                          |
| `POST /hello/Joe`      | The request falls through, matches HTTP GET only (Die Anforderung ist fehlgeschlagen, nur für eine HTTP-GET-Anforderung wird eine Übereinstimmung ermittelt) |
| `GET /hello/Joe/Smith` | The request falls through, no match. (Die Anforderung ist fehlgeschlagen, keine Übereinstimmung.)              |

::: moniker range="< aspnetcore-2.2"

Wenn Sie eine einzelne Route konfigurieren, müssen Sie <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*> aufrufen und eine `IRouter`-Instanz übergeben. <xref:Microsoft.AspNetCore.Routing.RouteBuilder> muss nicht verwendet werden.

::: moniker-end

Das Framework stellt mehrere Erweiterungsmethoden zum Erstellen von Routen zur Verfügung (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>):

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

::: moniker range="< aspnetcore-2.2"

Einige der aufgeführten Methoden (z. B. <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>) erfordern einen <xref:Microsoft.AspNetCore.Http.RequestDelegate>. <xref:Microsoft.AspNetCore.Http.RequestDelegate> wird als *Routenhandler* verwendet, wenn der Abgleich für die Route erfolgreich ist. Für andere Erweiterungsmethoden kann eine Middlewarepipeline konfiguriert werden, die als Routenhandler verwendet wird. Wenn die `Map*`-Methode einen Handler wie <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*> nicht akzeptiert, verwendet sie <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.

::: moniker-end

In den `Map[Verb]`-Methoden werden Einschränkungen verwendet, um die Route auf das HTTP-Verb im Methodennamen zu beschränken. Beispielsweise unter <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> und <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.

## <a name="route-template-reference"></a>Referenz für Routenvorlagen

Token in geschweiften Klammern (`{ ... }`) definieren *Routenparameter*, die beim Abgleich der Route gebunden werden. Sie können in einem Routensegment mehrere Routenparameter definieren, wenn Sie sie durch einen Literalwert trennen. `{controller=Home}{action=Index}` ist z.B. keine gültige Route, da sich zwischen `{controller}` und `{action}` kein Literalwert befindet. Diese Routenparameter müssen einen Namen besitzen und können zusätzliche Attribute aufweisen.

Eine Literalzeichenfolge, die nicht den Routenparametern entspricht (z.B. `{id}`), muss zusammen mit dem Pfadtrennzeichen `/` mit dem URL-Text übereinstimmen. Beim Abgleich von Text wird nicht zwischen Groß-/Kleinbuchstaben unterschieden, und die Übereinstimmung basiert auf der decodierten Repräsentation des URL-Pfads. Damit das Trennzeichen (`{` oder `}`) der Routenparameterliteralzeichenfolge bei einem Abgleich gefunden wird, muss es doppelt vorhanden sein (`{{` oder `}}`), was einem Escapezeichen entspricht.

Bei einem URL-Muster, durch das ein Dateiname mit einer optionalen Erweiterung erfasst werden soll, sind noch weitere Aspekte zu berücksichtigen. Dies wird z.B. anhand der Vorlage `files/{filename}.{ext?}` deutlich. Wenn sowohl für `filename` als auch für `ext` Werte vorhanden sind, werden beide Werte angegeben. Wenn nur für `filename` ein Wert in der URL vorhanden ist, wird für die Route eine Zuordnung ermittelt, da der nachstehende Punkt (`.`) optional ist. Für die folgenden URLs wird eine Übereinstimmung für die Route ermittelt:

* `/files/myFile.txt`
* `/files/myFile`

::: moniker range=">= aspnetcore-2.2"

Sie können ein (`*`) oder zwei (`**`) Sternchen als Präfix für einen Routenparameter verwenden, um eine Bindung zum verbleibenden Teil des URI herzustellen. Hierbei wird von *Catch-All*-Parametern gesprochen. Durch `blog/{**slug}` wird beispielsweise jeder URI ermittelt, der mit `/blog` beginnt und dahinter einen beliebigen Wert aufweist, der dann dem `slug`-Routenwert zugeordnet wird. Durch Catch-All-Parameter können auch leere Zeichenfolgen gefunden werden.

Der Catch-All-Parameter schützt die entsprechenden Zeichen (Escaping), wenn die Route verwendet wird, um eine URL, einschließlich Pfadtrennzeichen zu generieren (`/`). Z.B. generiert die Route `foo/{*path}` mit den Routenwerten `{ path = "my/path" }` `foo/my%2Fpath`. Beachten Sie den umgekehrten Schrägstrich mit Escapezeichen. Um Trennzeichen für Roundtrips einsetzen zu können, verwenden Sie das Routenparameterpräfix `**`. Die Route `foo/{**path}` mit `{ path = "my/path" }` generiert `foo/my/path`.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Sie können das Sternchen (`*`) als Präfix für einen Routenparameter verwenden, um eine Bindung zum verbleibenden Teil des URI herzustellen. Hierbei wird von einem *Catch-All*-Parameter gesprochen. Durch `blog/{*slug}` wird beispielsweise jeder URI ermittelt, der mit `/blog` beginnt und dahinter einen beliebigen Wert aufweist, der dann dem `slug`-Routenwert zugeordnet wird. Durch Catch-All-Parameter können auch leere Zeichenfolgen gefunden werden.

Der Catch-All-Parameter schützt die entsprechenden Zeichen (Escaping), wenn die Route verwendet wird, um eine URL, einschließlich Pfadtrennzeichen zu generieren (`/`). Z.B. generiert die Route `foo/{*path}` mit den Routenwerten `{ path = "my/path" }` `foo/my%2Fpath`. Beachten Sie den umgekehrten Schrägstrich mit Escapezeichen.

::: moniker-end

Routenparameter können über mehrere *Standardwerte* verfügen, die nach dem Parameternamen angegeben werden und durch ein Gleichheitszeichen (`=`) voneinander getrennt werden. Mit `{controller=Home}` wird beispielsweise `Home` als Standardwert für `controller` definiert. Der Standardwert wird verwendet, wenn kein Wert in der Parameter-URL vorhanden ist. Routenparameter sind optional, wenn am Ende des Parameternamens ein Fragezeichen (`?`) angefügt wird, z. B. `id?`. Der Unterschied zwischen optionalen Parametern und Routenparametern mit einem Standardwert besteht darin, dass mithilfe der letzteren immer ein Wert erzeugt wird. Ein optionaler Parameter verfügt demgegenüber nur dann über einen Wert, wenn ein solcher von der Anforderungs-URL bereitgestellt wird.

Routenparameter können Einschränkungen aufweisen, die mit dem gebundenen Routenwert der URL übereinstimmen müssen. Eine *Inline-Einschränkung* für einen Routenparameter geben Sie an, indem Sie hinter dem Namen des Routenparameters einen Doppelpunkt (`:`) und einen Einschränkungsnamen hinzufügen. Wenn für die Einschränkung Argumente erforderlich sind, werden diese nach dem Einschränkungsnamen in Klammern (`(...)`) eingeschlossen. Mehrere Inline-Einschränkungen können festgelegt werden, indem ein weiterer Doppelpunkt (`:`) und Einschränkungsname hinzugefügt werden.

Der Einschränkungsname und die Argumente werden dem <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver>-Dienst übergeben, wodurch eine Instanz von <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> für die URL-Verarbeitung erstellt werden kann. In der Routenvorlage `blog/{article:minlength(10)}` wird beispielsweise die Einschränkung `minlength` mit dem Argument `10` festgelegt. Weitere Informationen zu Routeneinschränkungen und eine Liste der vom Framework bereitgestellten Einschränkungen finden Sie im Abschnitt [Referenz zu Routeneinschränkungen](#route-constraint-reference).

::: moniker range=">= aspnetcore-2.2"

Routenparameter können darüber hinaus über Parametertransformatoren verfügen, die den Wert eines Parameters beim Generieren von Links umwandeln und Aktionen und Seiten an URLs anpassen. Wie Einschränkungen können auch Parametertransformatoren einem Routenparameter inline hinzugefügt werden, indem ein Doppelpunkt (`:`) und der Name des Transformators hinter dem Namen des Routenparameters hinzugefügt werden. In der Routenvorlage `blog/{article:slugify}` wird beispielsweise der Transformator `slugify` festgelegt. Weitere Informationen zu Parametertransformatoren finden Sie im Abschnitt [Parametertransformatorreferenz](#parameter-transformer-reference).

::: moniker-end

Die folgende Tabelle enthält Beispielvorlagen für Routen und deren Verhalten.

| Routenvorlage                           | Beispiel-URI für Übereinstimmung    | Der Anforderungs-URI&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Nur für den Pfad `/hello` wird eine Übereinstimmung ermittelt.                                     |
| `{Page=Home}`                            | `/`                     | Eine Übereinstimmung wird ermittelt, und `Page` wird auf `Home` festgelegt.                                         |
| `{Page=Home}`                            | `/Contact`              | Eine Übereinstimmung wird ermittelt, und `Page` wird auf `Contact` festgelegt.                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | Stimmt mit dem `Products`-Controller und der `List`-Aktion überein.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | Stimmt mit dem `Products`-Controller und der `Details`-Aktion (`id` auf 123 festgelegt) überein. |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | Stimmt mit dem `Home`-Controller und der `Index`-Aktion überein (`id` wird ignoriert).        |

Mit Vorlagen lässt sich Routing besonders leicht durchführen. Einschränkungen und Standardwerte können auch außerhalb der Routenvorlage angegeben werden.

> [!TIP]
> Wenn Sie die [Protokollierung](xref:fundamentals/logging/index) aktivieren, erfahren Sie, wie die integrierten Routingimplementierungen (z.B. <xref:Microsoft.AspNetCore.Routing.Route>) Zuordnungen für Anforderungen ermitteln.

## <a name="reserved-routing-names"></a>Reservierte Routingnamen

Die folgenden Schlüsselwörter sind reservierte Namen und können nicht als Routennamen oder Parameter verwendet werden:

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a>Referenz für Routeneinschränkungen

Routeneinschränkungen werden angewendet, wenn eine Übereinstimmung mit der eingehenden URL gefunden wurde und der URL-Pfad in Routenwerten mit Token versehen wird. In der Regel wird mit Routeneinschränkungen der Routenwert der zugehörigen Vorlage geprüft. Dabei wird bestimmt, ob der Wert gültig ist. Für einige Routeneinschränkungen werden anstelle des Routenwerts andere Daten verwendet, um zu ermitteln, ob das Routing einer Anforderung möglich ist. <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> kann beispielsweise auf der Grundlage des HTTP-Verbs eine Anforderung entweder annehmen oder ablehnen. Einschränkungen werden in Routinganforderungen und bei der Linkgenerierung verwendet.

> [!WARNING]
> Verwenden Sie keine Einschränkungen für die **Eingabeüberprüfung**. Wenn bei der **Eingabevalidierung** Einschränkungen verwendet werden, lösen ungültige Eingaben den Fehler *404 - Not Found* (404 – Nicht gefunden) aus. Stattdessen sollte jedoch der Fehler *400 - Bad Request* (400 – Fehlerhafte Anforderung) ausgelöst und mit einer entsprechenden Fehlermeldung angezeigt werden. Verwenden Sie Routeneinschränkungen nicht, um Eingaben für eine bestimmte Route zu überprüfen, sondern um ähnlichen Routen zu **unterscheiden**.

In der folgenden Tabelle werden Beispiele für Routeneinschränkungen und deren zu erwartendes Verhalten beschriebe.

| Einschränkung | Beispiel | Beispiele für Übereinstimmungen | Hinweise |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | Für jeden Integer wird eine Übereinstimmung ermittelt. |
| `bool` | `{active:bool}` | `true`, `FALSE` | Für `true` oder `false` wird eine Übereinstimmung ermittelt (keine Unterscheidung zwischen Groß-/Kleinbuchstaben). |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Für einen gültigen `DateTime`-Wert wird eine Übereinstimmung ermittelt (in der invarianten Kultur; siehe Warnung). |
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Für einen gültigen `decimal`-Wert wird eine Übereinstimmung ermittelt (in der invarianten Kultur; siehe Warnung). |
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Für einen gültigen `double`-Wert wird eine Übereinstimmung ermittelt (in der invarianten Kultur; siehe Warnung). |
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Für einen gültigen `float`-Wert wird eine Übereinstimmung ermittelt (in der invarianten Kultur; siehe Warnung). |
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Für einen gültigen `Guid`-Wert wird eine Übereinstimmung ermittelt. |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Für einen gültigen `long`-Wert wird eine Übereinstimmung ermittelt. |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | Die Zeichenfolge muss mindestens eine Länge von 4 Zeichen aufweisen. |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | Die Zeichenfolge darf maximal eine Länge von 8 Zeichen aufweisen. |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | Die Zeichenfolge muss genau 12 Zeichen aufweisen. |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | Die Zeichenfolge muss mindestens eine Länge von 8 und darf maximal eine Länge von 16 Zeichen aufweisen. |
| `min(value)` | `{age:min(18)}` | `19` | Der Integerwert muss mindestens 18 sein. |
| `max(value)` | `{age:max(120)}` | `91` | Der Integerwert darf nicht größer als 120 sein. |
| `range(min,max)` | `{age:range(18,120)}` | `91` | Der Integerwert muss zwischen 18 und 120 liegen. |
| `alpha` | `{name:alpha}` | `Rick` | Die Zeichenfolge muss aus mindestens einem alphabetische Zeichen bestehen (`a`-`z`, keine Unterscheidung zwischen Groß-/Kleinbuchstaben). |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | Die Zeichenfolge muss dem regulären Ausdruck entsprechen (siehe Tipp zum Definieren eines regulären Ausdrucks) |
| `required` | `{name:required}` | `Rick` | Hierdurch wird erzwungen, dass ein Wert, der kein Parameter ist, für die URL-Generierung vorhanden sein muss. |

Auf einen einzelnen Parameter können mehrere durch Doppelpunkte getrennte Einschränkungen angewendet werden. Durch die folgende Einschränkung wird ein Parameter beispielsweise auf einen Integerwert größer oder gleich 1 beschränkt:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> Für Routeneinschränkungen, mit denen die URL überprüft wird und die in den CLR-Typ umgewandelt werden (beispielsweise `int` oder `DateTime`), wird immer die invariante Kultur verwendet. Diese Einschränkungen setzen voraus, dass die URL nicht lokalisierbar ist. Die vom Framework bereitgestellten Routeneinschränkungen ändern nicht die Werte, die in Routenwerten gespeichert sind. Alle Routenwerte, die aus der URL analysiert werden, werden als Zeichenfolgen gespeichert. Durch die `float`-Einschränkung wird beispielsweise versucht, den Routenwert in einen Gleitkommawert zu konvertieren. Mit dem konvertierten Wert wird allerdings nur überprüft, ob eine Umwandlung überhaupt möglich ist.

## <a name="regular-expressions"></a>Reguläre Ausdrücke

Im ASP.NET Core-Framework wird dem Konstruktor für reguläre Ausdrücke `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` hinzugefügt. Eine Beschreibung dieser Member finden Sie unter <xref:System.Text.RegularExpressions.RegexOptions>.

In regulären Ausdrücken werden Trennzeichen und Token verwendet, die auch beim Routing und in der Programmiersprache C# in ähnlicher Weise verwendet werden. Token, die reguläre Ausdrücke enthalten, müssen mit einem Escapezeichen versehen werden. Wenn Sie den regulären Ausdruck `^\d{3}-\d{2}-\d{4}$` für das Routing verwenden möchten, muss der Ausdruck `\`-Zeichen (einzelner Schrägstrich) enthalten, die in der C#-Quelldatei in der Zeichenfolge als `\\`-Zeichen (doppelter Schrägstrich) angegeben sind, damit die `\`-Zeichenfolge mit einem Escapezeichen versehen wird. Dies ist nicht erforderlich, wenn [ausführliche Zeichenfolgenliterale](/dotnet/csharp/language-reference/keywords/string) verwendet werden. Wenn Sie Trennzeichen für Routenparameter mit Escapezeichen versehen möchten (`{`, `}`, `[`, `]`), geben Sie jedes Zeichen im Ausdruck doppelt ein (`{{`, `}`, `[[`, `]]`). In der folgenden Tabelle werden reguläre Ausdrücke und Ausdrücke aufgeführt, die mit Escapezeichen versehen sind.

| Regulärer Ausdruck    | Mit Escapezeichen versehener regulärer Ausdruck     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Beim Routing verwendete reguläre Ausdrücke beginnen oft mit einem Caretzeichen (`^`) und stellen die Startposition der Zeichenfolge dar. Die Ausdrücke enden häufig mit einem Dollarzeichen (`$`) und stellen das Ende der Zeichenfolge dar. Mit den Zeichen `^` und `$` wird sichergestellt, dass der reguläre Ausdruck mit dem vollständigen Routenparameterwert übereinstimmt. Ohne die Zeichen `^` und `$` werden mit dem regulären Ausdruck alle Teilzeichenfolgen ermittelt, was häufig nicht gewünscht ist. In der folgenden Tabelle finden Sie Beispiele für reguläre Ausdrücke. Außerdem wird erklärt, warum ein Abgleich erfolgreich ist oder fehlschlägt.

| Ausdruck   | Zeichenfolge    | Match | Kommentar               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Ja   | Teilzeichenfolge stimmt überein     |
| `[a-z]{2}`   | 123abc456 | Ja   | Teilzeichenfolge stimmt überein     |
| `[a-z]{2}`   | mz        | Ja   | Ausdruck stimmt überein    |
| `[a-z]{2}`   | MZ        | Ja   | keine Unterscheidung zwischen Groß-/Kleinbuchstaben    |
| `^[a-z]{2}$` | hello     | Nein    | siehe Erläuterungen zu `^` und `$` oben |
| `^[a-z]{2}$` | 123abc456 | Nein    | siehe Erläuterungen zu `^` und `$` oben |

Weitere Informationen zur Syntax von regulären Ausdrücken finden Sie unter [Sprachelemente für reguläre Ausdrücke – Kurzübersicht](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Einen regulären Ausdruck können Sie verwenden, um einen Parameter auf zulässige Werte einzuschränken. Mit `{action:regex(^(list|get|create)$)}` werden beispielsweise für den `action`-Routenwert nur die Werte `list`, `get` oder `create` abgeglichen. Wenn die Zeichenfolge `^(list|get|create)$` dem Einschränkungswörterbuch übergeben wird, führt dies zum gleichen Ergebnis. Auch Einschränkungen, die dem zugehörigen Wörterbuch hinzugefügt werden und mit keiner vorgegebenen Einschränkung übereinstimmen , werden als reguläre Ausdrücke behandelt. Dies gilt allerdings nicht für Inline-Einschränkungen in einer Vorlage.

## <a name="custom-route-constraints"></a>Benutzerdefinierte Routeneinschränkungen

Zusätzlich zu den integrierten Routeneinschränkungen können benutzerdefinierte Routeneinschränkungen durch Implementierung der <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>-Schnittstelle erstellt werden. Die <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>-Schnittstelle enthält eine einzelne Methode, `Match`, die `true` zurückgibt, wenn die Einschränkung erfüllt wird, und andernfalls `false`.

Zum Verwenden einer benutzerdefinierten <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> muss der Routeneinschränkungstyp bei der <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> der App im Dienstcontainer der App registriert werden. Eine <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> ist ein Wörterbuch, das Routeneinschränkungsschlüssel <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>-Implementierungen zuordnet, die diese Einschränkungen überprüfen. Die <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> einer App kann in `Startup.ConfigureServices` entweder als Teil eines [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*)-Aufrufs oder durch direktes Konfigurieren von <xref:Microsoft.AspNetCore.Routing.RouteOptions> mit `services.Configure<RouteOptions>` aktualisiert werden. Beispiel:

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

Die Einschränkung kann dann auf die übliche Weise mit dem bei der Registrierung des Einschränkungstyps angegebenen Namen auf Routen angewendet werden. Beispiel:

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

::: moniker range=">= aspnetcore-2.2"

## <a name="parameter-transformer-reference"></a>Parametertransformatorreferenz

Parametertransformatoren:

* Werden beim Generieren eines Links für eine <xref:Microsoft.AspNetCore.Routing.Route> ausgeführt.
* Implementieren Sie `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`.
* Werden mithilfe von <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> konfiguriert.
* Nehmen den Routenwert des Parameters an und transformieren ihn in einen neuen Zeichenfolgenwert.
* Haben die Verwendung des transformierten Wert in einem generierten Link zur Folge.

Beispielsweise generiert ein benutzerdefinierter Parametertransformator `slugify` im Routenmuster `blog\{article:slugify}` mit `Url.Action(new { article = "MyTestArticle" })` `blog\my-test-article`.

Um einen Parametertransformator in einem Routenmuster zu verwenden, konfigurieren Sie ihn zuerst mit <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in `Startup.ConfigureServices`:

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

Parametertransformatoren werden auch von Frameworks verwendet, um den URI zu transformieren, zu dem ein Endpunkt aufgelöst wird. Beispielsweise verwendet ASP.NET Core MVC Parametertransformatoren zum Transformieren des Routenwerts, der zum Zuordnen einer `area`, eines `controller`, einer `action` und einer `page` verwendet wird.

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

Mit der vorstehenden Route wird die Aktion `SubscriptionManagementController.GetAll()` dem URI `/subscription-management/get-all` zugeordnet. Ein Parametertransformator ändert nicht die zum Generieren eines Links verwendeten Routenwerte. Beispielsweise gibt `Url.Action("GetAll", "SubscriptionManagement")` `/subscription-management/get-all` aus.

ASP.NET Core bietet API-Konventionen für die Verwendung von Parametertransformatoren mit generierten Routen:

* ASP.NET Core MVC verwendet die `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention`-API-Konvention. Diese Konvention wendet einen angegebenen Parametertransformator auf alle Attributrouten in der App an. Der Parametertransformator transformiert Attributroutentoken, wenn diese ersetzt werden. Weitere Informationen finden Sie unter [Verwenden eines Parametertransformators zum Anpassen der Tokenersetzung](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).
* Razor Pages verwendet die `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention`-API-Konvention. Diese Konvention wendet einen angegebenen Parametertransformator auf alle automatisch erkannten Razor Pages-Seiten an. Der Parametertransformator transformiert die Ordner- und Dateinamensegmente von Razor Pages-Routen. Weitere Informationen finden Sie unter [Verwenden eines Parametertransformators zum Anpassen von Seitenrouten](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).

::: moniker-end

## <a name="url-generation-reference"></a>Referenz für URL-Generierung

Im folgenden Beispiel wird gezeigt, wie Sie einen Link zu einer Route unter Berücksichtigung eines vorhandenen Wörterbuchs mit Routenwerten und einer <xref:Microsoft.AspNetCore.Routing.RouteCollection> erstellen.

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

Die <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath>-Eigenschaft, die am Ende des obigen Beispiels erstellt wird, hat den Wert `/package/create/123`. Das Wörterbuch stellt die Routenwerte von `operation` und `id` aus der Vorlage „Track Package Route“ (`package/{operation}/{id}`) bereit. Weitere Informationen finden Sie im Beispielcode im Abschnitt [Verwenden von Routingmiddleware](#use-routing-middleware) oder in der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).

Der zweite Parameter für den <xref:Microsoft.AspNetCore.Routing.VirtualPathContext>-Konstruktor ist eine Auflistung von *Umgebungswerten*. Mit diesen lässt sich leicht die Anzahl der Werte einschränken, die ein Entwickler innerhalb eines Anforderungskontexts angeben muss. Die aktuellen Routenwerte der aktuellen Anforderung werden bei der Linkgenerierung als Umgebungswerte behandelt. In einer ASP.NET Core-MVC-App müssen Sie innerhalb der `About`-Aktion von `HomeController` nicht den Controllerroutenwert angeben, um eine Verknüpfung mit der `Index`-Aktion herzustellen, da der Umgebungswert von `Home` verwendet wird.

Umgebungswerte, die mit keinem Parameter übereinstimmen, werden ignoriert. Außerdem werden Umgebungswerte ignoriert, wenn ein explizit angegebener Wert den betreffenden Umgebungswert außer Kraft setzt. Der Abgleich in der URL wird von links nach rechts ausgeführt.

Explizit bereitgestellte Werte, für die keine Übereinstimmungen mit einem Routensegment ermittelt werden, werden der Abfragezeichenfolge hinzugefügt. In der folgenden Tabelle werden die Ergebnisse dargestellt, die aus der Verwendung der Routenvorlage `{controller}/{action}/{id?}` hervorgehen:

| Umgebungswerte                     | Explizite Werte                        | Ergebnis                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |

Wenn eine Route über einen Standardwert verfügt, der keinem Parameter entspricht, und dieser Wert explizit bereitgestellt wird, muss er dem Standardwert entsprechen:

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

Für diese Route wird nur dann ein Link generiert, wenn die übereinstimmenden Werte für `controller` und `action` bereitgestellt werden.

## <a name="complex-segments"></a>Komplexe Segmente

Komplexe Segmente (z.B. `[Route("/x{token}y")]`) werden von rechts nach links auf eine nicht gierige Weise durch entsprechende Literale verarbeitet. Unter [diesem Code](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) finden Sie eine ausführliche Erklärung für das Abgleichen komplexer Segmente. Das [Codebeispiel](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) wird von ASP.NET Core nicht verwendet, aber es bietet eine gute Erklärung komplexer Segmente.
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/aspnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->

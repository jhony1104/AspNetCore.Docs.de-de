---
title: 'title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:'
author: rick-anderson
description: "'Blazor'"
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/1/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/routing
ms.openlocfilehash: f99ac6aea7bd959473fdbbfeec6ba19d4dad4ff3
ms.sourcegitcommit: 58722eb309767e462bdbf3082bd38737a4ef168f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84106545"
---
# <a name="routing-in-aspnet-core"></a>'Identity'

'Let's Encrypt'

::: moniker range=">= aspnetcore-3.0"

'Razor' 'SignalR' uid: Routing in ASP.NET Core Von [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5) und [Rick Anderson](https://twitter.com/RickAndMSFT) Das Routing wird für das Abgleichen von HTTP-Anforderungen und das Verteilen an ausführbare Endpunkte der App eingesetzt.

[Endpunkte](#endpoint) sind die Einheiten des ausführbaren Codes für die Anforderungsverarbeitung in der App.

- Endpunkte werden in der App definiert und beim Start der App konfiguriert.
- Beim Endpunktabgleich können Werte aus der Anforderungs-URL extrahiert und für die Verarbeitung der Anforderung bereitgestellt werden.
- SignalR
- Mithilfe von Endpunktinformationen aus der App lassen sich durch das Routing URLs generieren, die Endpunkten zugeordnet werden.
- Apps können das Routing mit folgenden Funktionen konfigurieren:
- Controller

Razor Pages gRPC-Dienste

* Endpunktfähige [Middleware](xref:fundamentals/middleware/index) wie [Integritätsprüfungen](xref:host-and-deploy/health-checks)
* Für das Routing registrierte Delegate und Lambdas

In diesem Artikel werden die grundlegenden Details zum ASP.NET Core-Routing beschrieben. Informationen zur Routingkonfiguration finden Sie wie folgt:

* Für Controller unter <xref:mvc/controllers/routing>.
* Konventionen für Razor Pages finden Sie unter <xref:razor-pages/razor-pages-conventions>.

Das in diesem Dokument beschriebene Endpunktroutingsystem gilt für ASP.NET Core 3.0 und höher.

Weitere Informationen zum vorherigen Routingsystem, das auf <xref:Microsoft.AspNetCore.Routing.IRouter> basiert, erhalten Sie, wenn Sie die ASP.NET Core 2.1-Version wie folgt auswählen: Mit der Versionsauswahl für eine vorherige Version.

## <a name="routing-basics"></a>Mit Auswahl von [ASP.NET Core 2.1](https://docs.microsoft.com/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples/3.x) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)) Die Downloadbeispiele für dieses Dokument werden durch eine bestimmte `Startup`-Klasse aktiviert.

Um ein bestimmtes Beispiel auszuführen, ändern Sie *Program.cs*, um die gewünschte `Startup`-Klasse aufzurufen.

[!code-csharp[](routing/samples/3.x/RoutingSample/Startup.cs?name=snippet&highlight=8,10)]

Routinggrundlagen

* Alle ASP.NET Core-Vorlagen enthalten die Routingfunktionen im generierten Code. Das Routing wird in `Startup.Configure` in der [Middlewarepipeline](xref:fundamentals/middleware/index) registriert.
* Der folgende Code veranschaulicht ein einfaches Beispiel für das Routing: Beim Routing wird ein Middlewarepaar verwendet, das durch <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> und <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> registriert wird:

`UseRouting` fügt der Middlewarepipeline einen Routenabgleich hinzu.

* Diese Middleware prüft die in der App definierten Endpunkte und wählt anhand der Anforderung die [beste Übereinstimmung](#urlm) aus.
  * `UseEndpoints` fügt der Middlewarepipeline die Endpunktausführung hinzu.
  * Dabei wird der mit dem ausgewählten Endpunkt verknüpfte Delegat ausgeführt. Das vorherige Beispiel enthält eine einzelnen *Route-zu- Code*-Endpunkt unter Verwendung der [MapGet](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*)-Methode:
* Wenn eine `GET`-HTTP-Anforderung an die Stamm-URL `/`gesendet wird:

### <a name="endpoint"></a>Der angezeigte Anforderungsdelegat wird ausgeführt.

<a name="endpoint"></a>

`Hello World!` wird in die HTTP-Antwort geschrieben. Die Stamm-URL `/` lautet standardmäßig `https://localhost:5001/`.

* Wenn die Anforderungsmethode nicht `GET` bzw. die Stamm-URL nicht `/` ist, gibt es keinen Routenabgleich und es wird ein HTTP-404-Fehler zurückgegeben.
* Endpunkt

Die `MapGet`-Methode wird verwendet, um einen **Endpunkt** zu definieren. Ein Endpunkt kann Folgendes sein:
Ausgewählt, indem die URL und die HTTP-Methode abgeglichen werden.
- Ausgeführt, indem ein Delegat ausgeführt wird.
- Endpunkte, die von der App zugeordnet und ausgeführt werden können, sind in `UseEndpoints` konfiguriert.
- Mit <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*>, <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapPost*> und [ähnlichen Methoden](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions) werden beispielsweise Anforderungsdelegate mit dem Routingsystem verbunden. 
- Zudem können weitere Methoden zur Verbindung von ASP.NET Core-Frameworkfunktionen mit dem Routingsystem verwendet werden:

[MapRazorPages für Razor Pages](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*)

[!code-csharp[](routing/samples/3.x/RoutingSample/RouteTemplateStartup.cs?name=snippet)]

[MapControllers für Controller](xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*) [MapHub\<THub> für SignalR](xref:Microsoft.AspNetCore.SignalR.HubRouteBuilder.MapHub*) [MapGrpcService\<TService> für gRPC](xref:grpc/aspnetcore)

* Das folgende Beispiel zeigt das Routing mit einer anspruchsvolleren Routenvorlage:
* Die Zeichenfolge `/hello/{name:alpha}` ist eine **Routenvorlage**.  Sie wird verwendet, um zu konfigurieren, wie der Endpunkt abgeglichen wird. In diesem Fall gleicht die Vorlage Folgendes ab:

Eine URL wie `/hello/Ryan`

* Alle URL-Pfade, die mit `/hello/` beginnen, gefolgt von einer Sequenz alphabetischer Zeichen.
* `:alpha` wendet eine Routeneinschränkung an, die nur alphabetische Zeichen abgleicht.

[Routeneinschränkungen](#route-constraint-reference) werden weiter unten in diesem Dokument erläutert. Das zweite Segment des URL-Pfads, `{name:alpha}`:

Ist an den Parameter `name` gebunden.

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

Ist in [HttpRequest.RouteValues](xref:Microsoft.AspNetCore.Http.HttpRequest.RouteValues*) erfasst und gespeichert.

* Das in diesem Dokument beschriebene Endpunktroutingsystem gilt ab ASP.NET Core 3.0.
* Alle Versionen von ASP.NET Core unterstützen jedoch dieselben Routenvorlagenfunktionen und Routeneinschränkungen.

Das folgende Beispiel zeigt das Routing mit [Integritätsprüfungen](xref:host-and-deploy/health-checks) und Autorisierung: Im vorherigen Beispiel wird veranschaulicht, wie Sie:

Die Autorisierungsmiddleware für das Routing verwenden. Endpunkte zum Konfigurieren des Autorisierungsverhaltens verwendet werden können.

* Der <xref:Microsoft.AspNetCore.Builder.HealthCheckEndpointRouteBuilderExtensions.MapHealthChecks*>-Aufruf fügt einen Endpunkt für eine Integritätsprüfung hinzu.
* Durch die Verkettung von <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*> mit diesem Aufruf wird eine Autorisierungsrichtlinie an den Endpunkt angefügt.

<a name="metadata"></a>

### <a name="endpoint-metadata"></a>Der Aufruf von <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> und <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*> wird die Authentifizierungs- und Autorisierungsmiddleware hinzugefügt.

Diese Middleware wird zum Ausführen folgender Aktionen zwischen <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> und `UseEndpoints` platziert: Anzeigen des von `UseRouting` ausgewählten Endpunkts. Anwenden einer Autorisierungsrichtlinie vor dem Senden von <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> an den Endpunkt. Endpunktmetadaten

* Im vorangehenden Beispiel gibt es zwei Endpunkte, aber nur dem für die Integritätsprüfung ist eine Autorisierungsrichtlinie angefügt.
* Wenn die Anforderung mit dem Endpunkt der Integritätsprüfung, `/healthz`, übereinstimmt, wird eine Autorisierungsprüfung durchgeführt.

## <a name="routing-concepts"></a>Dadurch wird veranschaulicht, dass Endpunkten zusätzliche Daten zugeordnet werden können.

Diese zusätzlichen Daten werden als **Metadaten** des Endpunkts bezeichnet: Die Metadaten lassen sich von routingfähiger Middleware verarbeiten.

<a name="endpoint"></a>

### <a name="aspnet-core-endpoint-definition"></a>Die Metadaten können einen beliebigen .NET-Typ aufweisen.

Routingkonzepte

* Durch Hinzufügen des effizienten **Endpunkt**-Konzepts stellt das Routingsystem eine Ergänzung der Middlewarepipeline dar.
* Endpunkte stehen für Funktionseinheiten der App, die sich in Bezug auf Routing, Autorisierung und die Anzahl der ASP.NET Core-Systeme voneinander unterscheiden.
* ASP.NET Core-Endpunktdefinition
* Ein ASP.NET Core-Endpunkt ist:

Ausführbar: Verfügt über eine <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate>-Eigenschaft.

[!code-csharp[](routing/samples/3.x/RoutingSample/EndpointInspectorStartup.cs?name=snippet)]

Erweiterbar: Verfügt über eine [Metadata](xref:Microsoft.AspNetCore.Http.Endpoint.Metadata*)-Sammlung. Selectable (Auswählbar): Verfügt optional über [Routinginformationen](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern*). Aufzählbar: Die Sammlung von Endpunkten kann durch Abrufen der <xref:Microsoft.AspNetCore.Routing.EndpointDataSource>-Klasse aus [DI](xref:fundamentals/dependency-injection) aufgelistet werden. Der folgende Code zeigt, wie der Endpunkt, der mit der aktuellen Anforderung übereinstimmt, abgerufen und geprüft werden kann: Der Endpunkt, falls ausgewählt, kann aus dem `HttpContext`-Element abgerufen werden.

Seine Eigenschaften können geprüft werden.

<a name="mt"></a>

Endpunktobjekte sind unveränderlich und können nach der Erstellung nicht mehr geändert werden.

[!code-csharp[](routing/samples/3.x/RoutingSample/MiddlewareFlowStartup.cs?name=snippet)]

Der häufigste Typ des Endpunkts ist eine <xref:Microsoft.AspNetCore.Routing.RouteEndpoint>-Klasse. Die `RouteEndpoint`-Klasse enthält Informationen, durch die diese vom Routingsystem ausgewählt werden kann.

Im vorangehenden Code wird mit [app.Use](xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*) eine [Middleware](xref:fundamentals/middleware/index) inline konfiguriert.

```txt
1. Endpoint: (null)
2. Endpoint: Hello
3. Endpoint: Hello
```

Der folgende Code zeigt, dass es, je nachdem, wo `app.Use` in der Pipeline aufgerufen wird, möglicherweise keinen Endpunkt gibt:

```txt
1. Endpoint: (null)
2. Endpoint: (null)
4. Endpoint: (null)
```

In diesem vorangehenden Beispiel werden `Console.WriteLine`-Anweisungen hinzugefügt, die anzeigen, ob ein Endpunkt ausgewählt wurde oder nicht.

* Aus Gründen der Übersichtlichkeit wird in dem Beispiel dem bereitgestellten `/`-Endpunkt ein Anzeigename zugewiesen.
* Wenn Sie diesen Code mit einer URL `/` ausführen, wird Folgendes angezeigt:
* Wenn Sie diesen Code mit einer anderen URL ausführen, wird Folgendes angezeigt: Diese Ausgabe zeigt Folgendes:
* Der Endpunkt ist immer NULL, bevor `UseRouting` aufgerufen wird.

Wenn eine Übereinstimmung gefunden wird, ist der Endpunkt zwischen `UseRouting` und <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> ungleich NULL. Die `UseEndpoints`-Middleware ist eine **Terminalmiddleware**, wenn eine Übereinstimmung gefunden wird. [Terminalmiddleware](#tm) wird weiter unten in diesem Dokument definiert. Die Middleware nach `UseEndpoints` wird nur ausgeführt, wenn keine Übereinstimmung gefunden wird.

Die `UseRouting`-Middleware verwendet die [SetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.SetEndpoint*)-Methode, um den Endpunkt an den aktuellen Kontext anzufügen. Es ist möglich, die `UseRouting`-Middleware durch benutzerdefinierte Logik zu ersetzen und dennoch die Vorteile durch die Verwendung von Endpunkten zu nutzen. Endpunkte befinden sich auf niedriger Ebene, wie Middleware, und sind nicht an die Routingimplementierung gekoppelt.

Die meisten Apps müssen `UseRouting` nicht durch eigene Logik ersetzen.

[!code-csharp[](routing/samples/3.x/RoutingSample/IntegratedMiddlewareStartup.cs?name=snippet)]

Die `UseEndpoints`-Middleware ist so konzipiert, dass Sie zusammen mit der `UseRouting`-Middleware verwendet werden kann.

* Die Hauptlogik zum Ausführen eines Endpunkts ist nicht kompliziert.
    * Mit <xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*> können Sie einen Endpunkt abrufen und dann dessen <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate>-Eigenschaft aufrufen.
* Der folgende Code veranschaulicht, wie Middleware das Routing beeinflussen oder darauf reagieren kann:
    * Im vorherigen Beispiel werden zwei wichtige Konzepte dargestellt:
      * Die Middleware kann vor `UseRouting` ausgeführt werden, um die Daten zu ändern, auf denen das Routing basiert.
      * Normalerweise verändert Middleware, die vor dem Routing eingesetzt wird, eine Eigenschaft der Anforderung, wie z.B. <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>, <xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions.UseHttpMethodOverride*> oder <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*>.
    * Die Middleware kann zwischen `UseRouting` und <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> ausgeführt werden, um die Ergebnisse des Routings vor der Ausführung des Endpunkts zu verarbeiten.

Middleware, die zwischen `UseRouting` und `UseEndpoints` ausgeführt wird: Überprüft in der Regel die Metadaten, um die Endpunkte zu ermitteln. Trifft häufig Sicherheitsentscheidungen, wie `UseAuthorization` und `UseCors`. Durch die Kombination aus Middleware und Metadaten ist es möglich, für jeden einzelnen Endpunkt Richtlinien zu konfigurieren. Der vorangehende Code zeigt ein Beispiel für eine benutzerdefinierte Middleware, die entpunktbezogene Richtlinien unterstützt. Die Middleware schreibt ein *Überwachungsprotokoll* für den Zugriff auf vertrauliche Daten in der Konsole. Die Middleware kann so konfiguriert werden, dass ein Endpunkt mit den `AuditPolicyAttribute`-Metadaten *überwacht* wird.

In diesem Beispiel wird ein *Opt-In*-Muster veranschaulicht, bei dem nur Endpunkte überwacht werden, die als vertraulich markiert sind. Es ist möglich, diese Logik umgekehrt zu definieren, indem beispielsweise alles geprüft wird, was nicht als sicher markiert ist. Das Endpunktmetadaten-System ist flexibel.

* Diese Logik lässt sich für jeden Anwendungsfall passend schreiben.
* Der vorherige Beispielcode soll die grundlegenden Konzepte von Endpunkten veranschaulichen.

**Das Beispiel ist nicht für Produktionsumgebungen vorgesehen.** Eine vollständigere Version einer Middleware für *Überwachungsprotokolle* würde Folgendes bieten:

* Protokollieren in einer Datei oder einer Datenbank.
* Einschließen von Details wie Benutzer, IP-Adresse, Name des vertraulichen Endpunkts usw.

Die Metadaten der Überwachungsrichtlinie `AuditPolicyAttribute` sind als `Attribute` definiert, um die Verwendung mit klassenbasierten Frameworks wie Controllern und SignalR zu erleichtern. Bei Verwendung von *Route-zu-Code*: Metadaten werden an eine Generator-API angefügt.

<a name="tm"></a>

### <a name="comparing-a-terminal-middleware-and-routing"></a>Klassenbasierte Frameworks enthalten beim Erstellen von Endpunkten alle Attribute der entsprechenden Methode und Klasse.

Die bewährten Methoden für Metadatentypen sind, sie entweder als Schnittstellen oder als Attribute zu definieren.

[!code-csharp[](routing/samples/3.x/RoutingSample/TerminalMiddlewareStartup.cs?name=snippet)]

Schnittstellen und Attribute ermöglichen die Wiederverwendung von Code. Das Metadatensystem ist flexibel und weist keine Einschränkungen auf.

* Vergleichen von Terminalmiddleware und Routing
* Das folgende Codebeispiel zeigt den Unterschied zwischen Middleware und Routing:

Beim in `Approach 1:` gezeigten Stil von Middleware handelt es sich um **Terminalmiddleware**.

Sie wird als Terminalmiddleware bezeichnet, da sie einen Abgleich durchgeführt.
* Der Abgleich im vorangehenden Beispiel ist `Path == "/"` für die Middleware und `Path == "/Movie"` für das Routing.
    * Bei einem erfolgreichen Abgleich führt sie bestimmte Funktionen aus und kehrt zurück, anstatt die `next`-Middleware aufzurufen.
    * Sie wird „Terminal Middleware“ genannt, da sie die Suche beendet, einige Funktionen ausführt und dann zurückkehrt.
* Vergleichen von Terminalmiddleware und Routing:
    * Beide Ansätze ermöglichen das Beenden der Verarbeitungspipeline:
* Die Middleware beendet die Pipeline, indem Sie zurückkehrt, anstatt `next` aufzurufen.
    * Endpunkte beenden immer die Verarbeitung.
    * Terminalmiddleware ermöglicht die Positionierung der Middleware an einer beliebigen Stelle in der Pipeline: Endpunkte werden an der Position von <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> ausgeführt.
* Mit Terminalmiddleware kann beliebiger Code beendet werden, wenn die Middleware übereinstimmt:
    * Ein benutzerdefinierter Routenabgleichscode kann sehr umständlich und schwierig zu schreiben sein.

Das Routing bietet unkomplizierte Lösungen für typische Apps.

* Die meisten Apps erfordern keinen benutzerdefinierten Routenabgleichscode.
* Endpunkte haben eine Schnittstelle mit Middleware wie `UseAuthorization` und `UseCors`. Die Verwendung einer Terminalmiddleware mit `UseAuthorization` oder `UseCors` erfordert eine manuelle Verknüpfung mit dem Autorisierungssystem.

Ein [Endpunkt](#endpoint) definiert beides:

* Einen Delegaten zum Verarbeiten von Anforderungen.
* Eine Sammlung beliebiger Metadaten.

Die Metadaten werden zur Implementierung von übergreifenden Belangen verwendet, die auf Richtlinien und der Konfiguration basieren, die den einzelnen Endpunkten angefügt sind.

Terminalmiddleware kann sehr nützlich sein, erfordert aber möglicherweise auch: Einen großen Codierungs- und Testaufwand.
* Eine manuelle Integration in andere Systeme für die gewünschte Flexibilität.
* Ziehen Sie daher zunächst die Integration von Routingfunktionen in Betracht, bevor Sie damit beginnen, Terminalmiddleware zu schreiben.
* Vorhandene Terminalmiddleware, die in [Map](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) oder <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> integriert ist, kann in der Regel in einen routingfähigen Endpunkt umgewandelt werden. [MapHealthChecks](https://github.com/aspnet/AspNetCore/blob/master/src/Middleware/HealthChecks/src/Builder/HealthCheckEndpointRouteBuilderExtensions.cs#L16) zeigt das Muster für eine Routinglösung:
* Schreiben Sie eine Erweiterungsmethode in der <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>-Schnittstelle.
* Erstellen Sie eine geschachtelte Middlewarepipeline mit <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.CreateApplicationBuilder*>.
* Fügen Sie die Middleware an die neue Pipeline an.

In diesem Fall <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>.

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

Verwenden Sie <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.Build*>, um die Middlewarepipeline in einem <xref:Microsoft.AspNetCore.Http.RequestDelegate>-Delegaten zu erstellen. Rufen Sie `Map` auf, und stellen Sie die neue Middlewarepipeline bereit. Geben Sie das Generatorobjekt zurück, das von `Map` aus der Erweiterungsmethode bereitgestellt wurde.

Im folgenden Code ist die Verwendung von [MapHealthChecks](xref:host-and-deploy/health-checks) gezeigt: Das vorangehende Beispiel zeigt, warum das Zurückgeben des Generatorobjekts wichtig ist.

<a name="urlm"></a>

### <a name="url-matching"></a>Wenn das Generatorobjekt zurückgegeben wird, kann der App-Entwickler Richtlinien konfigurieren, z. B. die Autorisierung für den Endpunkt.

* In diesem Beispiel ist die Middleware für Integritätsprüfungen nicht direkt in das Autorisierungssystem integriert.
* Das Metadatensystem wurde als Antwort auf die Probleme erstellt, die von Erweiterbarkeitsautoren mithilfe von Terminalmiddleware aufgetreten sind.
* Für jede Middleware ist es problematisch, deren eigene Integration in das Autorisierungssystem umzusetzen.

URL-Zuordnung

* Bei diesem Prozess werden eingehende Anforderungen durch Routing mit einem [Endpunkt](#endpoint) abgeglichen.
* Basiert auf Daten im URL-Pfad und den URL-Headern.

Kann erweitert werden, um beliebige Daten in der Anforderung zu überprüfen. Wenn eine Routingmiddleware ausgeführt wird, legt sie ein `Endpoint`-Element fest und leitet Werte an eine [Anforderungsfunktion](xref:fundamentals/request-features) in der <xref:Microsoft.AspNetCore.Http.HttpContext>-Klasse der aktuellen Anforderung weiter: Durch Aufrufen von [HttpContext.GetEndPoint](<xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*>) wird der Endpunkt abgerufen.

`HttpRequest.RouteValues` ruft die Sammlung der Routenwerte ab. [Middleware](xref:fundamentals/middleware/index), die nach der Routingmiddleware ausgeführt wird, kann den Endpunkt erkennen und Maßnahmen ergreifen.

* So kann beispielsweise eine Autorisierungsmiddleware die Erfassung der Metadaten des Endpunkts für eine Autorisierungsrichtlinie abfragen.

> [!WARNING]
> Nachdem die gesamte Middleware in der Anforderungsverarbeitungspipeline ausgeführt wurde, wird der Delegat des ausgewählten Endpunkts aufgerufen.
>
> Das Routingsystem ist beim Endpunktrouting für alle Weiterleitungsentscheidungen zuständig.
>
> * Da die Middleware Richtlinien auf der Grundlage des ausgewählten Endpunkts anwendet, ist Folgendes wichtig:
> * Alle Entscheidungen, die sich auf die Verteilung oder die Anwendung von Sicherheitsrichtlinien auswirken können, werden im Routingsystem getroffen.

Wenn für Abwärtskompatibilität ein Controller- oder Razor Pages-Endpunktdelegat ausgeführt wird, werden die Eigenschaften von [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) auf Grundlage der bisher verarbeiteten Anforderungen auf entsprechende Werte festgelegt. Der Typ `RouteContext` wird in einer zukünftigen Version als veraltet markiert: Migrieren Sie `RouteData.Values` zu `HttpRequest.RouteValues`. Migrieren Sie `RouteData.DataTokens`, um [IDataTokensMetadata](xref:Microsoft.AspNetCore.Routing.IDataTokensMetadata) aus den Endpunktmetadaten abzurufen. Der URL-Abgleich erfolgt in mehreren Phasen und kann konfiguriert werden. In jeder Phase werden mehrere Übereinstimmungen ausgegeben. Diese Übereinstimmungen lassen sich in der nächsten Phase weiter eingrenzen.

1. Die Routingimplementierung garantiert keine Verarbeitungsreihenfolge für übereinstimmende Endpunkte.
1. **Alle** möglichen Übereinstimmungen werden gleichzeitig verarbeitet.
1. Für die URL-Abgleichsphasen gilt folgende Reihenfolge.
1. ASP.NET Core:

Verarbeitet den URL-Pfad mit mehreren Endpunkten und ihren Routenvorlagen und sammelt **alle** Übereinstimmungen.

* Nimmt die vorangehende Liste und entfernt die Übereinstimmungen, die nicht zu den angewendeten Routeneinschränkungen passen.
* Nimmt die vorangehende Liste und entfernt die Übereinstimmungen, die nicht zu den [MatcherPolicy](xref:Microsoft.AspNetCore.Routing.MatcherPolicy)-Instanzen passen.

Verwendet [EndpointSelector](xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector), um eine abschließende Entscheidung anhand der vorangehenden Liste zu treffen. Die Liste der Endpunkte wird entsprechend den folgenden Punkten priorisiert: Die [RouteEndpoint.Order](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order*)-Eigenschaft Die [Priorität der Routenvorlage](#rtp)

Alle übereinstimmenden Endpunkte werden in jeder Phase verarbeitet, bis <xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector> erreicht ist. `EndpointSelector` stellt die abschließende Phase dar.

* Darin wird der Endpunkt mit der höchsten Priorität aus den Übereinstimmungen als beste Übereinstimmung ausgewählt.
* Wenn es andere Übereinstimmungen mit derselben Priorität wie die beste Übereinstimmung gibt, wird ein Ausnahmefehler wegen einer nicht eindeutigen Übereinstimmung ausgelöst.

Die Routenpriorität wird anhand einer **spezifischeren** Routenvorlage berechnet, der eine höhere Priorität eingeräumt wird. Dies wird z. B. anhand der Vorlagen `/hello` und `/{message}` deutlich:

Beide stimmen mit dem URL-Pfad `/hello` überein. `/hello` ist spezifischer und hat daher eine höhere Priorität.

* Im Allgemeinen eignet sich die Routenpriorität gut, um die beste Übereinstimmung für die in der Praxis verwendeten URL-Schemata zu finden.
* Verwenden Sie <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order> nur bei Bedarf, um eine Mehrdeutigkeit zu vermeiden.
* Aufgrund der Erweiterungsmöglichkeiten, die das Routing bietet, kann das Routingsystem die mehrdeutigen Routen nicht im Voraus berechnen.
* Betrachten Sie ein Beispiel wie die Routenvorlagen `/{message:alpha}` und `/{message:int}`:

> [!WARNING]
>
> Die `alpha`-Einschränkung gleicht nur alphabetische Zeichen ab. Die `int`-Einschränkung gleicht nur Zahlen ab. Diese Vorlagen haben die gleiche Routenpriorität, aber es gibt keine einzige URL, auf die sie beide zutreffen.
>
> Wenn das Routingsystem beim Starten einen Mehrdeutigkeitsfehler gemeldet hat, würde dieser den gültigen Anwendungsfall blockieren. Die Reihenfolge der Vorgänge in <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> wirkt sich nicht auf das Routingverhalten aus, mit einer Ausnahme.
> 
> * <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> und <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> weisen ihren Endpunkten automatisch einen Reihenfolgenwert zu, basierend auf der Reihenfolge, in der sie aufgerufen werden.
> * Dadurch wird das Langzeitverhalten von Controllern simuliert, ohne dass das Routingsystem die gleichen Garantien bietet wie ältere Routingimplementierungen. Bei der bisherigen Routingimplementierung war es möglich, eine Routingerweiterung vorzunehmen, die von der Verarbeitungsreihenfolge der Routen abhängt.
>
> Endpunktrouting in ASP.NET Core 3.0 und höher:

<a name="rtp"></a>

### <a name="route-template-precedence-and-endpoint-selection-order"></a>Weist kein Konzept für Routen auf.

Bietet keine garantierte Reihenfolge. Alle Endpunkte werden gleichzeitig verarbeitet.

* Wenn dies bedeutet, dass Sie mit dem Legacy-Routingsystem nicht weiterkommen, [öffnen Sie ein GitHub-Problem zur Unterstützung](https://github.com/dotnet/aspnetcore/issues).
* Routenvorlagenpriorität und Reihenfolge der Endpunktauswahl

Die [Routenvorlagenpriorität](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L16) ist ein System, bei dem jeder Routenvorlage ein Wert zugewiesen wird, je nachdem, wie spezifisch diese ist. Routenvorlagenpriorität: Verhindert, dass die Reihenfolge der Endpunkte häufig angepasst werden muss.

Versucht, die allgemeinen Erwartungen an das Routingverhalten abzugleichen.

* Dies wird z. B. anhand der Vorlagen `/Products/List` und `/Products/{id}` deutlich.
* Es wäre begründet, anzunehmen, dass `/Products/List` eine bessere Übereinstimmung als `/Products/{id}` für den URL-Pfad `/Products/List` ist.
* Dies funktioniert, weil das Literalsegment `/List` eine höhere Priorität hat als das Parametersegment `/{id}`.
* Wie die Priorisierung im Einzelnen funktioniert, ist an die Definition der Routenvorlagen gekoppelt:
* Vorlagen mit mehr Segmenten sind in der Regel spezifischer. Ein Segment mit Literaltext gilt als spezifischer als ein Parametersegment.

Ein Parametersegment mit einer Einschränkung gilt als spezifischer als ein Parametersegment ohne Einschränkung.

<a name="lg"></a>

### <a name="url-generation-concepts"></a>Ein komplexes Segment wird als genauso spezifisch betrachtet wie ein Parametersegment mit einer Einschränkung.

Catch-All-Parameter, die am wenigsten spezifisch sind.

* Unter **Catch-All** in der [Referenz zu Routenvorlagen](#rtr) finden Sie wichtige Informationen zu Catch-All-Routen.
* Einen Verweis auf genaue Werte finden Sie im [Quellcode auf GitHub](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L189).

Konzepte zur URL-Generierung URL-Generierung: Der Prozess, bei dem durch Routing ein URL-Pfad basierend auf mehreren Routenwerten erstellt wird. Sie ermöglicht eine logische Trennung zwischen den Endpunkten und den URLs, die auf diese zugreifen.

Das Endpunktrouting umfasst die API zur Linkgenerierung (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>). `LinkGenerator` ist ein Singleton-Dienst, der in [DI](xref:fundamentals/dependency-injection) verfügbar ist. Die `LinkGenerator`-API kann außerhalb des Kontexts einer ausgeführten Anforderung verwendet werden.

[Mvc.IUrlHelper](xref:Microsoft.AspNetCore.Mvc.IUrlHelper) und Szenarios, die von <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> abhängig sind (z. B. [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro), HTML-Hilfsprogramme und [Aktionsergebnisse](xref:mvc/controllers/actions)), verwenden die `LinkGenerator`-API, um entsprechende Funktionen bereitzustellen.

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

Die API zur Linkgenerierung wird von Konzepten wie **Adressen** und **Adressschemas** unterstützt. Sie können mithilfe eines Adressschemas die Endpunkte bestimmen, die bei der Linkgenerierung berücksichtigt werden sollen.

Beispielsweise werden Routennamen und Routenwerte als Adressschemas implementiert. Diese Szenarios kennen viele Benutzer von Controllern und Razor Pages. Die API zur Linkgenerierung kann Controller und Razor Pages über die folgenden Erweiterungsmethoden miteinander verknüpfen: Beim Überladen dieser Methoden werden Argumente akzeptiert, die den `HttpContext` umfassen. Diese Methoden sind zwar in funktionaler Hinsicht äquivalent zu [Url.Action](xref:System.Web.Mvc.UrlHelper.Action*) und [Url.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*), bieten aber zusätzliche Flexibilität und Optionen.

Die `GetPath*`-Methoden sind `Url.Action` und `Url.Page` in der Hinsicht ähnlich, dass sie einen URI mit einem absoluten Pfad generieren. Die `GetUri*`-Methoden generieren immer einen absoluten URI mit einem Schema und einem Host.

1. Die Methoden, die einen `HttpContext` akzeptieren, generieren im Kontext der ausgeführten Anforderung einen URI.
1. Die [Umgebungsroutenwerte](#ambient), der URL-basierte Pfad, das Schema und der Host von der ausführenden Anforderung werden so lange verwendet, bis sie außer Kraft gesetzt werden. <xref:Microsoft.AspNetCore.Routing.LinkGenerator> wird mit einer Adresse aufgerufen.

Ein URI wird in zwei Schritten generiert: Eine Adresse wird an eine Liste von Endpunkten gebunden, die der Adresse zugeordnet werden können.

| Jedes <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern> eines Endpunkts wird bewertet, bis ein Routenmuster gefunden wird, das den angegebenen Werten zugeordnet werden kann. | Die daraus resultierende Ausgabe wird mit URI-Teilen kombiniert, die für die API zur Linkgenerierung bereitgestellt wird, und zurückgegeben. |
| ---------------- | ----------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Die von <xref:Microsoft.AspNetCore.Routing.LinkGenerator> bereitgestellten Methoden unterstützen die Standardfunktionen zur Generierung von Links für jeden beliebigen Adresstypen. |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Am praktischsten ist es, die API zur Linkgenerierung mit Erweiterungsmethoden zu verwenden, die Vorgänge für einen bestimmten Adresstypen ausführen:             |

> [!WARNING]
> Erweiterungsmethode
>
> * Beschreibung title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'
>
> * 'Identity' 'Let's Encrypt' 'Razor' 'SignalR' uid:

### <a name="middleware-example"></a>title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor' 'Identity'

[!code-csharp[](routing/samples/3.x/RoutingSample/Middleware/ProductsLinkMiddleware.cs?name=snippet)]

<a name="rtr"></a>

## <a name="route-template-reference"></a>'Let's Encrypt'

'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:  'Blazor'

'Identity' 'Let's Encrypt' 'Razor' 'SignalR' uid:

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

* 'Blazor'
* 'Identity' 'Let's Encrypt'
  * 'Razor'
  * 'SignalR' uid:

[!INCLUDE[](~/includes/catchall.md)]

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor' 'Identity' 'Let's Encrypt' 'Razor' 'SignalR' uid:

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity' 'Let's Encrypt' 'Razor'

* `/files/myFile.txt`
* `/files/myFile`

'SignalR' uid: -------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity' 'Let's Encrypt' 'Razor'

* 'SignalR' uid:
* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor' 'Identity' 'Let's Encrypt' 'Razor'

'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

'Identity' 'Let's Encrypt' 'Razor' 'SignalR' uid: ------ | | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Generiert einen URI mit einem absoluten Pfad, der auf den angegebenen Werten basiert.

| | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Generiert einen absoluten URI, der auf den angegebenen Werten basiert.

| Beachten Sie die folgenden Implikationen zum Aufrufen von <xref:Microsoft.AspNetCore.Routing.LinkGenerator>-Methoden:                           | Verwenden Sie `GetUri*`-Erweiterungsmethoden in App-Konfigurationen, die den `Host`-Header von eingehenden Anforderungen nicht überprüfen, mit Bedacht.    | Wenn der `Host`-Header von eingehenden Anforderungen nicht überprüft wird, können nicht vertrauenswürdige Anforderungseingaben zurück an den Client in URIs einer Ansicht bzw. Seite zurückgesendet werden.                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Es wird empfohlen, dass alle Produktions-Apps ihren Server so konfigurieren, dass der `Host`-Header auf bekannte gültige Werte überprüft wird.                                     |
| `{Page=Home}`                            | `/`                     | Verwenden Sie <xref:Microsoft.AspNetCore.Routing.LinkGenerator> in Kombination mit `Map` oder `MapWhen` in Middleware mit Bedacht.                                         |
| `{Page=Home}`                            | `/Contact`              | `Map*` ändert den Basispfad der ausgeführten Anforderung. Dies beeinflusst die Ausgabe der Linkgenerierung.                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | Für alle <xref:Microsoft.AspNetCore.Routing.LinkGenerator>-APIs ist die Angabe eines Basispfads zulässig.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | Geben Sie einen leeren Basispfad an, um die Auswirkungen von `Map*` auf die Linkgenerierung rückgängig zu machen. |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | Middlewarebeispiel Im folgenden Beispiel verwendet eine Middleware die <xref:Microsoft.AspNetCore.Routing.LinkGenerator>-API, um eine Verknüpfung zu einer Aktionsmethode herzustellen, die Speicherprodukte aufführt.        |
| `{controller=Home}/{action=Index}/{id?}` | `/Products`         | Sie können für jede beliebige Klasse in einer App die API zur Linkgenerierung verwenden, indem Sie diese in eine Klasse einfügen und `GenerateLink` aufrufen: Referenz für Routenvorlagen        |

Token in `{}` definieren Routenparameter, die beim Abgleich der Route gebunden werden. In einem Routensegment können mehrere Routenparameter definiert werden, müssen aber durch einen Literalwert getrennt werden.

### <a name="complex-segments"></a>`{controller=Home}{action=Index}` ist z.B. keine gültige Route, da sich zwischen `{controller}` und `{action}` kein Literalwert befindet.

Routenparameter müssen einen Namen besitzen und können zusätzliche Attribute aufweisen. Eine Literalzeichenfolge, die nicht den Routenparametern entspricht (z.B. `{id}`), muss zusammen mit dem Pfadtrennzeichen `/` mit dem URL-Text übereinstimmen.
Beim Abgleich von Text wird nicht zwischen Groß-/Kleinbuchstaben unterschieden, und die Übereinstimmung basiert auf der decodierten Repräsentation des URL-Pfads. Damit das Trennzeichen (`{` oder `}`) der Routenparameter-Literalzeichenfolge bei einem Abgleich gefunden wird, muss es doppelt vorhanden sein, was einem Escapezeichen entspricht. Beispielsweise `{{` oder `}}`.

[!INCLUDE[](~/includes/regex.md)]

Sternchen `*` oder Doppelsternchen `**`: Können als Präfix für einen Routenparameter verwendet werden, um eine Bindung zum verbleibenden Teil des URI herzustellen.

* Werden **Catch-All**-Parameter genannt. Zum Beispiel `blog/{**slug}`:
* Entspricht einem beliebigen URI, der mit `/blog` beginnt und einem beliebigen Wert folgt.
* Der Wert nach `/blog` wird dem [Slug](https://developer.mozilla.org/docs/Glossary/Slug)-Routenwert zugewiesen. Durch Catch-All-Parameter können auch leere Zeichenfolgen gefunden werden.
* Der Catch-All-Parameter schützt die entsprechenden Zeichen (Escaping), wenn die Route verwendet wird, um eine URL, einschließlich Pfadtrennzeichen (`/`) zu generieren.
* Z.B. generiert die Route `foo/{*path}` mit den Routenwerten `{ path = "my/path" }``foo/my%2Fpath`.

Beachten Sie den umgekehrten Schrägstrich mit Escapezeichen. Um Trennzeichen für Roundtrips einsetzen zu können, verwenden Sie das Routenparameterpräfix `**`. Die Route `foo/{**path}` mit `{ path = "my/path" }` generiert `foo/my/path`.
* Bei einem URL-Muster, durch das ein Dateiname mit einer optionalen Erweiterung erfasst werden soll, sind noch weitere Aspekte zu berücksichtigen. Dies wird z.B. anhand der Vorlage `files/{filename}.{ext?}` deutlich.
* Wenn sowohl für `filename` als auch für `ext` Werte vorhanden sind, werden beide Werte angegeben.
* Wenn nur für `filename` ein Wert in der URL vorhanden ist, wird für die Route eine Übereinstimmung ermittelt, da der nachstehende Punkt (`.`) optional ist. Für die folgenden URLs wird eine Übereinstimmung für die Route ermittelt:
* Routenparameter können über mehrere **Standardwerte** verfügen, die nach dem Parameternamen angegeben werden und durch ein Gleichheitszeichen (`=`) voneinander getrennt werden.
* Mit `{controller=Home}` wird beispielsweise `Home` als Standardwert für `controller` definiert.

Der Standardwert wird verwendet, wenn kein Wert in der Parameter-URL vorhanden ist.

* Routenparameter sind optional, wenn am Ende des Parameternamens ein Fragezeichen (`?`) angefügt wird.
* Beispielsweise `id?`.

Zwischen optionalen Werten und Standardroutenparametern besteht folgender Unterschied:

<a name="greedy"></a>

Ein Routenparameter mit einem Standardwert erzeugt immer einen Wert. Ein optionaler Parameter hat nur dann einen Wert, wenn ein Wert von der Anforderungs-URL bereitgestellt wird.

## <a name="route-constraint-reference"></a>Routenparameter können Einschränkungen aufweisen, die mit dem gebundenen Routenwert der URL übereinstimmen müssen.

Eine Inline-Einschränkung für einen Routenparameter geben Sie an, indem Sie hinter dem Namen des Routenparameters einen Doppelpunkt (`:`) und einen Einschränkungsnamen hinzufügen. Wenn für die Einschränkung Argumente erforderlich sind, werden diese nach dem Einschränkungsnamen in Klammern (`(...)`) eingeschlossen. Mehrere *Inline-Einschränkungen* können festgelegt werden, indem ein weiterer Doppelpunkt (`:`) und Einschränkungsname hinzugefügt werden. Der Einschränkungsname und die Argumente werden dem <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver>-Dienst übergeben, wodurch eine Instanz von <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> für die URL-Verarbeitung erstellt werden kann. In der Routenvorlage `blog/{article:minlength(10)}` wird beispielsweise die Einschränkung `minlength` mit dem Argument `10` festgelegt.

> [!WARNING]
> Weitere Informationen zu Routeneinschränkungen und eine Liste der vom Framework bereitgestellten Einschränkungen finden Sie im Abschnitt [Referenz zu Routeneinschränkungen](#route-constraint-reference). Routenparameter können darüber hinaus über Parametertransformatoren verfügen. Diese wandeln den Wert eines Parameters beim Generieren von Links um und passen Aktionen und Seiten an URLs an. Wie Einschränkungen können auch Parametertransformatoren einem Routenparameter inline hinzugefügt werden, indem ein Doppelpunkt (`:`) und der Name des Transformators hinter dem Namen des Routenparameters hinzugefügt werden.

In der Routenvorlage `blog/{article:slugify}` wird beispielsweise der Transformator `slugify` festgelegt.

| Weitere Informationen zu Parametertransformatoren finden Sie im Abschnitt [Parametertransformatorreferenz](#parameter-transformer-reference). | Die folgende Tabelle enthält Beispielvorlagen für Routen und deren Verhalten: | Routenvorlage | Beispiel-URI für Übereinstimmung |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | Der Anforderungs-URI&hellip; | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: |
| `bool` | `{active:bool}` | 'Blazor' | 'Identity' 'Let's Encrypt' |
| `datetime` | `{dob:datetime}` | 'Razor' | 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: |
| `decimal` | `{price:decimal}` | 'Blazor' | 'Identity' 'Let's Encrypt'|
| `double` | `{weight:double}` | 'Razor' | 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:|
| `float` | `{weight:float}` | 'Blazor' | 'Identity' 'Let's Encrypt'|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638` | 'Razor' |
| `long` | `{ticks:long}` | 'SignalR' uid: | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | 'Blazor' |
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | 'Identity' |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | 'Let's Encrypt' |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | 'Razor' |
| `min(value)` | `{age:min(18)}` | `19` | 'SignalR' uid: |
| `max(value)` | `{age:max(120)}` | `91` | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: |
| `range(min,max)` | `{age:range(18,120)}` | `91` | 'Blazor' |
| `alpha` | `{name:alpha}` | `Rick` | 'Identity' |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | 'Let's Encrypt' 'Razor' |
| `required` | `{name:required}` | `Rick` | 'SignalR' uid: |

[!INCLUDE[](~/includes/regex.md)]

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> 'Identity' 'Let's Encrypt' 'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

### <a name="regular-expressions-in-constraints"></a>'Identity'

[!INCLUDE[](~/includes/regex.md)]

'Let's Encrypt' 'Razor' 'SignalR' uid:

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex.cs?name=snippet)]

'Blazor'

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex2.cs?name=snippet)]

'Identity' 'Let's Encrypt'

'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

* 'Blazor'
* 'Identity'

'Let's Encrypt' 'Razor'

| 'SignalR' uid:    | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

'Blazor' 'Identity' 'Let's Encrypt' 'Razor' 'SignalR' uid:

| title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:   | 'Blazor'    | 'Identity' | 'Let's Encrypt'               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | 'Razor'     | 'SignalR' uid:   | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:     |
| `[a-z]{2}`   | 'Blazor' | 'Identity'   | 'Let's Encrypt'     |
| `[a-z]{2}`   | 'Razor'        | 'SignalR' uid:   | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:    |
| `[a-z]{2}`   | 'Blazor'        | 'Identity'   | 'Let's Encrypt'    |
| `^[a-z]{2}$` | 'Razor'     | 'SignalR' uid:    | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: |
| `^[a-z]{2}$` | 'Blazor' | 'Identity'    | 'Let's Encrypt' |

'Razor'

'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity' 'Let's Encrypt'

### <a name="custom-route-constraints"></a>'Razor'

'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor' 'Identity'

'Let's Encrypt' 'Razor'

'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity'

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet)]

'Let's Encrypt'

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet&highlight=6,13)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

'Razor'

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

'SignalR' uid:

* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor' 'Identity'

'Let's Encrypt'

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet2)]

'Razor'

* 'SignalR' uid:
* -------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

## <a name="parameter-transformer-reference"></a>'Blazor'

'Identity'

* 'Let's Encrypt'
* 'Razor'
* 'SignalR' uid:
* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'

'Identity'

'Let's Encrypt'

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet2)]

'Razor'

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet)]

'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

```csharp
routes.MapControllerRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

'Blazor' 'Identity' 'Let's Encrypt'

'Razor'

* 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'
* 'Identity' 'Let's Encrypt' 'Razor' 'SignalR' uid:

<a name="ugr"></a>

## <a name="url-generation-reference"></a>title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor' 'Identity' 'Let's Encrypt'

'Razor' 'SignalR' uid:

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor' 'Identity'

### <a name="troubleshooting-url-generation-with-logging"></a>'Let's Encrypt'

'Razor' 'SignalR' uid:

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

### <a name="addresses"></a>'Blazor'

'Identity'

'Let's Encrypt'

* 'Razor'
    * 'SignalR' uid:
    * title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
    * 'Blazor'
    * 'Identity'
    * 'Let's Encrypt'
* 'Razor'
    * 'SignalR' uid:
    * title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
    * 'Blazor'

'Identity'

* 'Let's Encrypt'
* 'Razor'

<a name="ambient"></a>

### <a name="ambient-values-and-explicit-values"></a>'SignalR' uid:

------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity'

'Let's Encrypt' 'Razor'

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet)]

'SignalR' uid:

* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'

'Identity'

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet2)]

'Let's Encrypt'

'Razor'

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet3)]

'SignalR' uid:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/GadgetController.cs?name=snippet)]

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

* 'Blazor'
* 'Identity'
* <xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*>   
'Let's Encrypt' 'Razor'

'SignalR' uid:

[!code-csharp[](routing/samples/3.x/RoutingSample/Pages/Index.cshtml.cs?name=snippet)]

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

 `@page "{id:int}"`

'Blazor'

'Identity'

* 'Let's Encrypt'
* 'Razor'
* 'SignalR' uid: <!--by the user-->
* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor' 'Identity'

'Let's Encrypt'

### <a name="url-generation-process"></a>'Razor'

'SignalR' uid:

* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'

'Identity'  'Let's Encrypt' 'Razor'

'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

* 'Blazor'
* 'Identity'
* 'Let's Encrypt'
* 'Razor'

'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor' 'Identity' 'Let's Encrypt' 'Razor' 'SignalR' uid:

* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor'

* 'Identity' 'Let's Encrypt'
* 'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:  'Blazor'
* 'Identity' 'Let's Encrypt' 'Razor'

'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

* 'Identity'
* 'Let's Encrypt'

'Razor' 'SignalR' uid:

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

* 'Blazor'
* 'Identity'
    * 'Let's Encrypt'
    * 'Razor'
    * 'SignalR' uid:
    * title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor' 'Identity' 'Let's Encrypt'

'Razor' 'SignalR' uid:

* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'
* 'Identity'
  * 'Let's Encrypt'
  * 'Razor'
  * 'SignalR' uid:
  * <!-- review default-valued parameters optional parameters --> title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor' 'Identity'

| 'Let's Encrypt'                     | 'Razor'                        | 'SignalR' uid:                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:                | 'Blazor'                       | `/Home/About`           |
| 'Identity'                | 'Let's Encrypt' | `/Order/About`          |
| 'Razor' | 'SignalR' uid:                       | `/Home/About`           |
| title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:                | 'Blazor'        | `/Home/About?color=Red` |

### <a name="problems-with-route-value-invalidation"></a>'Identity'

'Let's Encrypt' 'Razor' 'SignalR' uid:

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupUnsupported.cs?name=snippet)]

'Blazor' 'Identity' 'Let's Encrypt'

* 'Razor'
* 'SignalR' uid:

## <a name="configuring-endpoint-metadata"></a>title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor'

* 'Identity'
* 'Let's Encrypt'
* 'Razor'
* <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*>
* 'SignalR' uid:
* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
* <xref:security/authorization/roles>

<a name="hostmatch"></a>

## <a name="host-matching-in-routes-with-requirehost"></a>'Blazor'

'Identity' 'Let's Encrypt'

* 'Razor'
* 'SignalR' uid:
* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'

'Identity' 'Let's Encrypt' 'Razor'

'SignalR' uid:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRequireHost.cs?name=snippet)]

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/ProductController.cs?name=snippet)]

'Blazor'

* 'Identity'
* 'Let's Encrypt'

## <a name="performance-guidance-for-routing"></a>'Razor'

'SignalR' uid:

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity'

* 'Let's Encrypt'
* 'Razor'

'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

'Identity'

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupDelay.cs?name=snippet)]

'Let's Encrypt'

* 'Razor'
* 'SignalR' uid:

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:  'Blazor'

'Identity'

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippetSW)]

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippet)]

### <a name="potentially-expensive-routing-features"></a>'Let's Encrypt'

'Razor'

* 'SignalR' uid:

* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 
  * 'Blazor'
  * 'Identity'
  * 'Let's Encrypt'

* 'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

## <a name="guidance-for-library-authors"></a>'Identity'

'Let's Encrypt' 'Razor'

### <a name="define-endpoints"></a>'SignalR' uid:

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor' 'Identity' 'Let's Encrypt' 'Razor'

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...);

    endpoints.MapHealthChecks("/healthz");
});
```

'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

* 'Identity'
* 'Let's Encrypt'

'Razor' 'SignalR' uid:

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequrireAuthorization()
                                 .WithMyFrameworkFeature(awesome: true);

    endpoints.MapHealthChecks("/healthz");
});
```

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity'

'Let's Encrypt'

'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

### <a name="creating-routing-integrated-middleware"></a>'Blazor'

'Identity'

'Let's Encrypt'

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet2)]

'Razor' 'SignalR' uid:

* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'

'Identity'

* 'Let's Encrypt'
* 'Razor'

'SignalR' uid:

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet)]

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

* 'Blazor'
* 'Identity'

'Let's Encrypt' 'Razor'

'SignalR' uid:

```csharp
app.UseRouting();

app.UseAuthorization(new AuthorizationPolicy() { ... });

app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequrireAuthorization();
});
```

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' <!-- shown where?  (shown here) --> 'Identity'

* 'Let's Encrypt'
* 'Razor'

'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

'Blazor' 'Identity' 'Let's Encrypt' 'Razor'

'SignalR' uid:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

------------------------------------- | | `hello`                                  | `/hello`                | Nur für den Pfad `/hello` wird eine Übereinstimmung ermittelt. | | `{Page=Home}`                            | `/`                     | Sucht nach Übereinstimmungen und legt `Page` auf `Home` fest. | | `{Page=Home}`                            | `/Contact`                     | Sucht nach Übereinstimmungen und legt `Page` auf `Contact` fest.

```csharp
// Use the routing logic of ASP.NET Core 2.1 or earlier:
services.AddMvc(options => options.EnableEndpointRouting = false)
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

| | `{controller}/{action}/{id?}`            | `/Products/List`        | Wird dem `Products`-Controller und der `List`-Aktion zugeordnet.

> [!IMPORTANT]
> | | `{controller}/{action}/{id?}`            | `/Products/Details/123`        | Wird dem `Products`-Controller und der `Details`-Aktion zugeordnet, bei der `id` auf 123 festgelegt ist. | | `{controller=Home}/{action=Index}/{id?}` | `/`        | Wird dem `Home`-Controller und der `Index`-Methode zugeordnet. `id` wird ignoriert.

| | `{controller=Home}/{action=Index}/{id?}` | `/Products`        | Wird dem `Products`-Controller und der `Index`-Methode zugeordnet.

## <a name="routing-basics"></a>`id` wird ignoriert.

Mit Vorlagen lässt sich Routing besonders leicht durchführen. Einschränkungen und Standardwerte können auch außerhalb der Routenvorlage angegeben werden.

* Komplexe Segmente
* Komplexe Segmente werden von rechts nach links auf eine [nicht gierige](#greedy) Weise durch entsprechende Literaltrennzeichen verarbeitet.

Beispielsweise ist `[Route("/a{b}c{d}")]` ein komplexes Segment. Komplexe Segmente funktionieren auf eine bestimmte Weise, die für eine erfolgreiche Verwendung verstanden werden muss.

Das Beispiel in diesem Abschnitt zeigt, warum komplexe Segmente nur dann wirklich gut funktionieren, wenn der Trennzeichentext nicht innerhalb der Parameterwerte erscheint. Für komplexere Fälle ist die Verwendung eines [RegEx](/dotnet/standard/base-types/regular-expressions) und das anschließende manuelle Extrahieren der Werte erforderlich. Dies ist eine Zusammenfassung der Schritte, die beim Routing mit der Vorlage `/a{b}c{d}` und dem URL-Pfad `/abcd` ausgeführt werden.

`|` wird verwendet, um die Funktionsweise des Algorithmus besser zu veranschaulichen: Das erste Literal von rechts nach links ist `c`. Daher wird `/abcd` von rechts durchsucht und `/ab|c|d` gefunden.

Alles auf der rechten Seite (`d`) ist jetzt mit dem Routenparameter `{d}` abgeglichen. Das nächste Literal von rechts nach links ist `a`.

Also wird `/ab|c|d` dort gesucht, wo die Suche unterbrochen wurde, und dann wird `a` in `/|a|b|c|d` gefunden.

Der Wert auf der rechten Seite (`b`) ist jetzt mit dem Routenparameter `{b}` abgeglichen. Es ist kein verbleibender Text und keine verbleibende Routenvorlage vorhanden. Folglich ist dies eine Übereinstimmung.

Nachfolgend ist ein Beispiel für einen negativen Fall mit derselben Vorlage `/a{b}c{d}` und dem URL-Pfad `/aabcd`.

* `|` wird verwendet, um die Funktionsweise des Algorithmus besser zu veranschaulichen:
* Bei diesem Fall handelt es sich nicht um eine Übereinstimmung, was durch denselben Algorithmus belegt wird:
* Das erste Literal von rechts nach links ist `c`.
* Daher wird `/aabcd` von rechts durchsucht und `/aab|c|d` gefunden.
* Alles auf der rechten Seite (`d`) ist jetzt mit dem Routenparameter `{d}` abgeglichen.
* Das nächste Literal von rechts nach links ist `a`.
* Also wird `/aab|c|d` dort gesucht, wo die Suche unterbrochen wurde, und dann wird `a` in `/a|a|b|c|d` gefunden.
* Der Wert auf der rechten Seite (`b`) ist jetzt mit dem Routenparameter `{b}` abgeglichen.
* Zu diesem Zeitpunkt gibt es noch verbleibenden Text `a`, aber es gibt keine Routenvorlage mehr, die der Algorithmus analysieren kann, weshalb dies keine Übereinstimmung ist.

  * Da der übereinstimmende Algorithmus [nicht gierig](#greedy) ist:
  * Entspricht er der kleinstmöglichen Textmenge in jedem Schritt.

> [!NOTE]
> Alle Fälle, in denen der Trennzeichenwert in den Parameterwerten angezeigt wird, stimmen nicht überein. Reguläre Ausdrücke bieten eine viel bessere Kontrolle über das Abgleichsverhalten.

Beim gierigen Abgleich, auch als [Lazy Matching](https://wikipedia.org/wiki/Regular_expression#Lazy_matching) bezeichnet, wird die größtmögliche Zeichenfolge abgeglichen. Beim nicht gierigen Abgleich ist dies die kürzeste Zeichenfolge. Referenz für Routeneinschränkungen

### <a name="url-matching"></a>Routeneinschränkungen werden angewendet, wenn eine Übereinstimmung mit der eingehenden URL gefunden wurde und der URL-Pfad in Routenwerten mit Token versehen wird.

In der Regel wird mit Routeneinschränkungen der Routenwert der zugehörigen Vorlage geprüft. Dabei wird anhand einer True/False-Entscheidung bestimmt, ob der Wert gültig ist. Für einige Routeneinschränkungen werden anstelle des Routenwerts andere Daten verwendet, um zu ermitteln, ob das Routing einer Anforderung möglich ist. <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> kann beispielsweise auf der Grundlage des HTTP-Verbs eine Anforderung entweder annehmen oder ablehnen.

Einschränkungen werden in Routinganforderungen und bei der Linkgenerierung verwendet. Verwenden Sie keine Einschränkungen für die Eingabeüberprüfung.

Wenn Einschränkungen für die Eingabevalidierung verwendet werden, führt eine ungültige Eingabe zu einem `404`-Fehler (Nicht gefunden).

Eine ungültige Eingabe sollte zu einer ungültigen Anforderung (`400`) mit einer entsprechenden Fehlermeldung führen. Verwenden Sie Routeneinschränkungen nicht, um Eingaben für eine bestimmte Route zu überprüfen, sondern um ähnliche Routen zu unterscheiden.

In der folgenden Tabelle werden Beispiele für Routeneinschränkungen und deren zu erwartendes Verhalten beschrieben: Einschränkung Beispiel Beispiele für Übereinstimmungen

Hinweise title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity' 'Let's Encrypt'

<a name="lg"></a>

### <a name="url-generation-with-linkgenerator"></a>'Razor'

'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor' 'Identity' 'Let's Encrypt' 'Razor'

'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

'Identity'

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

'Let's Encrypt' 'Razor'

'SignalR' uid: ----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity'

'Let's Encrypt' 'Razor'

1. 'SignalR' uid:
1. ---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

'Identity' 'Let's Encrypt'

| 'Razor'   | 'SignalR' uid:                                                         |
| ------------------ | ------------------------------------------------------------------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | 'Blazor'             |

> [!WARNING]
> 'Identity'
>
> * 'Let's Encrypt' 'Razor' 'SignalR' uid:
>
> * title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity' 'Let's Encrypt'

## <a name="differences-from-earlier-versions-of-routing"></a>'Razor'

'SignalR' uid:

* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

* 'Blazor' 'Identity'

* 'Let's Encrypt'

  'Razor'

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  'SignalR' uid:

  ```csharp
  var link = Url.Action("ReadPost", "blog", new { id = 17, });
  ```

  title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity'

  'Let's Encrypt'

* 'Razor'

  'SignalR' uid:

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  -------- | ----- | | `int` | `{id:int}` | `123456789`, `-123456789` | Stimmt mit sämtlichen Integerwerten überein | | `bool` | `{active:bool}` | `true`, `FALSE` | Stimmt mit `true` oder `false` überein.

  ```csharp
  var link = Url.Action("ReadPost", "Blog", new { id = 17, });
  ```

  Groß/-Kleinschreibung wird beachtet | | `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Stimmt mit einem gültigen `DateTime`-Wert in der invarianten Kultur überein. Siehe vorherige Warnung. | | `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Stimmt mit einem gültigen `decimal`-Wert in der invarianten Kultur überein. Siehe vorherige Warnung.| | `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Stimmt mit einem gültigen `double`-Wert in der invarianten Kultur überein.

* Siehe vorherige Warnung.| | `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Stimmt mit einem gültigen `float`-Wert in der invarianten Kultur überein.

  Siehe vorherige Warnung.| | `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638` | Stimmt mit einem gültigen `Guid`-Wert überein. | | `long` | `{ticks:long}` | `123456789`, `-123456789` | Stimmt mit einem gültigen `long`-Wert überein. | | `minlength(value)` | `{username:minlength(4)}` | `Rick` | Die Zeichenfolge muss mindestens vier Zeichen lang sein. | | `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | Die Zeichenfolge darf maximal acht Zeichen lang sein. | | `length(length)` | `{filename:length(12)}` | `somefile.txt` | Die Zeichenfolge muss genau zwölf Zeichen lang sein. | | `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | Die Zeichenfolge darf zwischen acht und 16 Zeichen lang sein. | | `min(value)` | `{age:min(18)}` | `19` | Der Integerwert muss mindestens 18 betragen. | | `max(value)` | `{age:max(120)}` | `91` | Der Integerwert darf maximal 120 betragen. | | `range(min,max)` | `{age:range(18,120)}` | `91` | Der Integerwert muss zwischen 18 und 120 betragen.| | `alpha` | `{name:alpha}` | `Rick` | Die Zeichenfolge muss aus mindestens einem Buchstaben bestehen, `a`-`z` Groß-/Kleinschreibung muss berücksichtigt werden. | | `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | Die Zeichenfolge muss mit dem regulären Ausdruck übereinstimmen. Weitere Informationen finden Sie unter Tipps zum Definieren eines regulären Ausdrucks. | | `required` | `{name:required}` | `Rick` | Hierdurch wird erzwungen, dass ein Wert, der kein Parameter ist, für die URL-Generierung vorhanden sein muss. | Auf einen einzelnen Parameter können mehrere durch Doppelpunkte getrennte Einschränkungen angewendet werden.

  Durch die folgende Einschränkung wird ein Parameter beispielsweise auf einen Integerwert größer oder gleich 1 beschränkt: Für Routeneinschränkungen, mit denen die URL überprüft wird und die in den CLR-Typ umgewandelt werden, wird immer die invariante Kultur verwendet.

  Dies gilt z. B. für die Konvertierung in den CLR-Typ `int` oder `DateTime`.

  ```cshtml
  @page "{id}"
  @Url.Page("/Login")
  ```

  Diese Einschränkungen setzen voraus, dass die URL nicht lokalisierbar ist.

  ```cshtml
  @page "{id?}"
  ```

  Die vom Framework bereitgestellten Routeneinschränkungen ändern nicht die Werte, die in Routenwerten gespeichert sind. Alle Routenwerte, die aus der URL analysiert werden, werden als Zeichenfolgen gespeichert. Durch die `float`-Einschränkung wird beispielsweise versucht, den Routenwert in einen Gleitkommawert zu konvertieren. Mit dem konvertierten Wert wird allerdings nur überprüft, ob eine Umwandlung überhaupt möglich ist.

  Reguläre Ausdrücke in Einschränkungen Reguläre Ausdrücke können mithilfe der `regex(...)`-Routeneinschränkung als Inline-Einschränkungen angegeben werden.

* Methoden der <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>-Familie akzeptieren auch ein Objektliteral von Einschränkungen.

  Wenn dieses Formular verwendet wird, werden Zeichenfolgenwerte als reguläre Ausdrücke interpretiert. Der folgende Code verwendet eine Inline-RegEx-Einschränkung:

  Der folgende Code verwendet ein Objektliteral, um eine RegEx-Einschränkung anzugeben:

  | Im ASP.NET Core-Framework wird dem Konstruktor für reguläre Ausdrücke `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` hinzugefügt.              | Eine Beschreibung dieser Member finden Sie unter <xref:System.Text.RegularExpressions.RegexOptions>.<br>`Url.Action(new { category = "admin/products" })`&hellip; |
  | ------------------ | --------------------------------------------------------------------- |
  | `/search/{*page}`  | In regulären Ausdrücken werden Trennzeichen und Token verwendet, die auch beim Routing und in der Programmiersprache C# in ähnlicher Weise verwendet werden.             |
  | `/search/{**page}` | `/search/admin/products`                                              |

### <a name="middleware-example"></a>Token, die reguläre Ausdrücke enthalten, müssen mit einem Escapezeichen versehen werden.

Wenn Sie den regulären Ausdruck `^\d{3}-\d{2}-\d{4}$` in einer Inline-Einschränkung verwenden möchten, nutzen Sie eine der folgenden Optionen: Ersetzen Sie `\`-Zeichen in der Zeichenfolge durch `\\`-Zeichen in der C#-Quelldatei, um das Escapezeichen für die Zeichenfolge `\` zu setzen.

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

### <a name="create-routes"></a>[Ausführliche Zeichenfolgeliterale](/dotnet/csharp/language-reference/keywords/string)

Wenn Sie Trennzeichen für Routenparameter mit Escapezeichen versehen möchten (`{`, `}`, `[`, `]`), geben Sie jedes Zeichen im Ausdruck doppelt ein (z. B. `{{`, `}}`, `[[`, `]]`). In der folgenden Tabelle werden reguläre Ausdrücke und Ausdrücke aufgeführt, die mit Escapezeichen versehen sind:

Regulärer Ausdruck Mit Escapezeichen versehener regulärer Ausdruck title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor'

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

'Identity' 'Let's Encrypt'

'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor' 'Identity' 'Let's Encrypt' 'Razor'

'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

'Identity'

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

'Let's Encrypt' 'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor' 'Identity'

'Let's Encrypt'

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
> 'Razor' 'SignalR' uid:

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{**article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

'Blazor' 'Identity' 'Let's Encrypt' 'Razor' 'SignalR' uid:

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

'Blazor'

!['Identity'](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>'Let's Encrypt'

'Razor' 'SignalR' uid:

> [!TIP]
> title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity'

'Let's Encrypt'

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor' 'Identity'

'Let's Encrypt'

> [!NOTE]
> 'Razor'

'SignalR' uid:

## <a name="use-routing-middleware"></a>----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor'

'Identity'

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

'Let's Encrypt' 'Razor'

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* 'SignalR' uid:
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

| 'Blazor'                    | 'Identity'                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | 'Let's Encrypt' 'Razor' |
| `/package/track/-3`    | 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: |
| `/package/track/-3/`   | 'Blazor' 'Identity' |
| `/package/track/`      | 'Let's Encrypt'              |
| `GET /hello/Joe`       | 'Razor'                                          |
| `POST /hello/Joe`      | 'SignalR' uid: |
| `GET /hello/Joe/Smith` | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:              |

'Blazor'

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

'Identity' 'Let's Encrypt'

## <a name="route-template-reference"></a>'Razor'

'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity'

'Let's Encrypt' 'Razor' 'SignalR' uid:

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity' 'Let's Encrypt' 'Razor'

* `/files/myFile.txt`
* `/files/myFile`

'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity'

'Let's Encrypt' 'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

'Identity' 'Let's Encrypt' 'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor' 'Identity' 'Let's Encrypt' 'Razor'

'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

'Identity' 'Let's Encrypt' 'Razor' 'SignalR' uid:

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

| 'Blazor'                           | 'Identity'    | 'Let's Encrypt'                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | 'Razor'                                     |
| `{Page=Home}`                            | `/`                     | 'SignalR' uid:                                         |
| `{Page=Home}`                            | `/Contact`              | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | 'Blazor'                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | 'Identity' |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | 'Let's Encrypt'        |

'Razor' 'SignalR' uid:

> [!TIP]
> title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

## <a name="reserved-routing-names"></a>'Blazor'

'Identity'

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a>'Let's Encrypt'

'Razor' 'SignalR' uid: --------------- | | `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               | Beim Routing verwendete reguläre Ausdrücke beginnen oft mit dem `^`-Zeichen und stellen die Startposition der Zeichenfolge dar. Die Ausdrücke enden häufig mit einem Dollarzeichen (`$`) und stellen das Ende der Zeichenfolge dar.

> [!WARNING]
> Mit den Zeichen `^` und `$` wird sichergestellt, dass der reguläre Ausdruck mit dem vollständigen Routenparameterwert übereinstimmt. Ohne die Zeichen `^` und `$` werden mit dem regulären Ausdruck alle Teilzeichenfolgen ermittelt, was häufig nicht gewünscht ist. In der folgenden Tabelle finden Sie Beispiele für reguläre Ausdrücke. Außerdem wird erklärt, warum ein Abgleich erfolgreich ist oder fehlschlägt:

expression

| Zeichenfolge | Match | Kommentar | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | 'Blazor' | 'Identity' |
| `bool` | `{active:bool}` | 'Let's Encrypt' | 'Razor' 'SignalR' uid: |
| `datetime` | `{dob:datetime}` | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: | 'Blazor' 'Identity'|
| `decimal` | `{price:decimal}` | 'Let's Encrypt' | 'Razor' 'SignalR' uid:|
| `double` | `{weight:double}` | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: | 'Blazor' 'Identity'|
| `float` | `{weight:float}` | 'Let's Encrypt' | 'Razor' 'SignalR' uid:|
| `guid` | `{id:guid}` | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: | 'Blazor' |
| `long` | `{ticks:long}` | 'Identity' | 'Let's Encrypt' |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | 'Razor' |
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | 'SignalR' uid: |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | ------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | 'Blazor' |
| `min(value)` | `{age:min(18)}` | `19` | 'Identity' |
| `max(value)` | `{age:max(120)}` | `91` | 'Let's Encrypt' |
| `range(min,max)` | `{age:range(18,120)}` | `91` | 'Razor' |
| `alpha` | `{name:alpha}` | `Rick` | 'SignalR' uid:  title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | 'Blazor' 'Identity' |
| `required` | `{name:required}` | `Rick` | 'Let's Encrypt' |

'Razor' 'SignalR' uid:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> ----- | :---: |  --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity' 'Let's Encrypt' 'Razor'

## <a name="regular-expressions"></a>'SignalR' uid:

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

'Identity' 'Let's Encrypt' 'Razor'

* 'SignalR' uid:
* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'

'Identity' 'Let's Encrypt'

| 'Razor'    | 'SignalR' uid:     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity' 'Let's Encrypt' 'Razor'

| 'SignalR' uid:   | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:    | 'Blazor' | 'Identity'               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | 'Let's Encrypt'     | 'Razor'   | 'SignalR' uid:     |
| `[a-z]{2}`   | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: | 'Blazor'   | 'Identity'     |
| `[a-z]{2}`   | 'Let's Encrypt'        | 'Razor'   | 'SignalR' uid:    |
| `[a-z]{2}`   | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:        | 'Blazor'   | 'Identity'    |
| `^[a-z]{2}$` | 'Let's Encrypt'     | 'Razor'    | 'SignalR' uid: |
| `^[a-z]{2}$` | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: | 'Blazor'    | 'Identity' |

'Let's Encrypt'

'Razor' 'SignalR' uid: ---------- | | `[a-z]{2}`   | hello     | Ja   | Teilzeichenfolge stimmt überein     | | `[a-z]{2}`   | 123abc456 | Ja   | Teilzeichenfolge stimmt überein     | | `[a-z]{2}`   | mz        | Ja   | Stimmt mit Ausdruck überein    | | `[a-z]{2}`   | MZ        | Ja   | Groß-/Kleinschreibung wird nicht berücksichtigt    | | `^[a-z]{2}$` | hello     | Nein    | Siehe `^` und `$` oben | | `^[a-z]{2}$` | 123abc456 | Nein    | Siehe `^` und `$` oben | Weitere Informationen zur Syntax von regulären Ausdrücken finden Sie unter [Sprachelemente für reguläre Ausdrücke – Kurzübersicht](/dotnet/standard/base-types/regular-expression-language-quick-reference).

## <a name="custom-route-constraints"></a>Einen regulären Ausdruck können Sie verwenden, um einen Parameter auf zulässige Werte einzuschränken.

Mit `{action:regex(^(list|get|create)$)}` werden beispielsweise für den `action`-Routenwert nur die Werte `list`, `get` oder `create` abgeglichen. Wenn die Zeichenfolge `^(list|get|create)$` dem Einschränkungswörterbuch übergeben wird, führt dies zum gleichen Ergebnis.

Auch Einschränkungen, die dem zugehörigen Wörterbuch hinzugefügt werden und mit keiner vorgegebenen Einschränkung übereinstimmen, werden als reguläre Ausdrücke behandelt. Einschränkungen, die innerhalb einer Vorlage übergeben werden und mit keiner vorgegebenen Einschränkung übereinstimmen, werden nicht als reguläre Ausdrücke behandelt. Benutzerdefinierte Routeneinschränkungen Benutzerdefinierte Routeneinschränkungen können durch Implementierung der <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>-Schnittstelle erstellt werden.

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

Die `IRouteConstraint`-Schnittstelle umfasst die <xref:System.Web.Routing.IRouteConstraint.Match*>-Methode, die `true` zurückgibt, wenn die Einschränkung erfüllt wird, und andernfalls `false`. Benutzerdefinierte Routeneinschränkungen werden nur selten benötigt.

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="parameter-transformer-reference"></a>Bevor Sie eine benutzerdefinierte Routeneinschränkung implementieren, sollten Sie Alternativen in Betracht ziehen, wie z. B. Modellbindung.

Der ASP.NET Core-Ordner [Constraints](https://github.com/dotnet/aspnetcore/tree/master/src/Http/Routing/src/Constraints) bietet nützliche Beispiele für die Erstellung einer Einschränkung.

* Beispiel: [GuidRouteConstraint](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Constraints/GuidRouteConstraint.cs#L18).
* Zum Verwenden eines benutzerdefinierten `IRouteConstraint`-Elements muss der Routeneinschränkungstyp bei der <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>-Eigenschaft der App im Dienstcontainer registriert werden.
* Eine `ConstraintMap` ist ein Wörterbuch, das Routeneinschränkungsschlüssel `IRouteConstraint`-Implementierungen zuordnet, die diese Einschränkungen überprüfen.
* Die `ConstraintMap` einer App kann in `Startup.ConfigureServices` entweder als Teil eines [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*)-Aufrufs oder durch direktes Konfigurieren von <xref:Microsoft.AspNetCore.Routing.RouteOptions> mit `services.Configure<RouteOptions>` aktualisiert werden.
* Zum Beispiel:

Die vorangehende Einschränkung wird im folgenden Code angewendet:

Die Implementierung von `MyCustomConstraint` verhindert die Anwendung von `0` auf einen Routenparameter:

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

Der vorangehende Code: Verhindert, dass `0` im `{id}`-Segment der Route vorhanden ist.

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

Dient als einfaches Beispiel für die Implementierung einer benutzerdefinierten Einschränkung. Es sollte nicht in einer Produktions-App eingesetzt werden. Der folgende Code bietet einen besseren Ansatz, um zu verhindern, dass eine `id` mit einer `0` verarbeitet wird:

Der vorangehende Code bietet im Vergleich zum `MyCustomConstraint`-Ansatz folgende Vorteile:

* Eine benutzerdefinierte Einschränkung ist nicht erforderlich. Es wird ein beschreibender Fehler zurückgegeben, wenn der Routenparameter `0` enthält. Parametertransformatorreferenz Parametertransformatoren:
* Werden beim Generieren eines Links mit <xref:Microsoft.AspNetCore.Routing.LinkGenerator> ausgeführt. Implementieren Sie <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer?displayProperty=fullName>. Werden mithilfe von <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> konfiguriert. Nehmen den Routenwert des Parameters an und transformieren ihn in einen neuen Zeichenfolgenwert.

## <a name="url-generation-reference"></a>Haben die Verwendung des transformierten Wert in einem generierten Link zur Folge.

Beispielsweise generiert ein benutzerdefinierter Parametertransformator `slugify` im Routenmuster `blog\{article:slugify}` mit `Url.Action(new { article = "MyTestArticle" })``blog\my-test-article`.

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

Betrachten Sie die folgende Implementierung von `IOutboundParameterTransformer`: Um einen Parametertransformator in einem Routenmuster zu verwenden, konfigurieren Sie ihn mit <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in `Startup.ConfigureServices`: Das ASP.NET Core-Framework verwendet Parametertransformatoren, um den URI zu transformieren, zu dem ein Endpunkt aufgelöst wird.

Beispielsweise wandeln Parametertransformatoren die Routenwerte um, die zum Zuordnen folgender Elemente verwendet werden: `area`, `controller`, `action` und `page`. Mit der vorstehenden Routenvorlage wird die Aktion `SubscriptionManagementController.GetAll` dem URI `/subscription-management/get-all` zugeordnet. Ein Parametertransformator ändert nicht die zum Generieren eines Links verwendeten Routenwerte. Beispielsweise gibt `Url.Action("GetAll", "SubscriptionManagement")``/subscription-management/get-all` aus.

ASP.NET Core bietet API-Konventionen für die Verwendung von Parametertransformatoren mit generierten Routen: Die <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention?displayProperty=fullName>-Konvention wendet einen angegebenen Parametertransformator auf alle Attributrouten in der App an. Der Parametertransformator transformiert Attributroutentoken, wenn diese ersetzt werden.

Weitere Informationen finden Sie unter [Verwenden eines Parametertransformators zum Anpassen der Tokenersetzung](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement). <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention> Pages verwendet die Razor-API-Konvention.

| Diese Konvention wendet einen angegebenen Parametertransformator auf alle automatisch erkannten Razor Pages-Seiten an.                     | Der Parametertransformator transformiert die Ordner- und Dateinamensegmente von Razor Pages-Routen.                        | Weitere Informationen finden Sie unter [Verwenden eines Parametertransformators zum Anpassen von Seitenrouten](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| Referenz für URL-Generierung                | Dieser Abschnitt enthält eine Referenz für den Algorithmus, der durch die URL-Generierung implementiert wird.                       | `/Home/About`           |
| In der Praxis werden bei den meisten komplexen Beispielen für die URL-Generierung Controller oder Razor Pages verwendet.                | Weitere Informationen finden Sie unter [Routing in Controllern](xref:mvc/controllers/routing) (Routing in Controllern). | `/Order/About`          |
| Die URL-Generierung beginnt mit einem Aufruf von [LinkGenerator.GetPathByAddress](xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*) oder einer ähnlichen Methode. | Die Methode wird mit einer Adresse, mehreren Routenwerten und optional mit Informationen zur aktuellen Anforderung von `HttpContext` versehen.                       | `/Home/About`           |
| Im ersten Schritt wird die Adresse verwendet, um bestimmte Endpunktkandidaten mithilfe einer [`IEndpointAddressScheme<TAddress>`](xref:Microsoft.AspNetCore.Routing.IEndpointAddressScheme`1)-Schnittstelle aufzulösen, die dem Adresstyp entspricht.                | Sobald eine Kandidatengruppe anhand des Adressschemas gefunden wurde, werden die Endpunkte geordnet und iterativ verarbeitet, bis die URL-Generierung erfolgreich abgeschlossen ist.        | `/Home/About?color=Red` |

Bei der URL-Generierung wird **nicht** auf Mehrdeutigkeiten geprüft, daher ist das erste zurückgegebene Ergebnis das Endergebnis.

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

Behandeln von Problemen mit der Protokollierung bei der URL-Generierung

## <a name="complex-segments"></a>Der erste Schritt bei der Behebung von Problemen bei der URL-Generierung ist die Einstellung des Protokolliergrads von `Microsoft.AspNetCore.Routing` auf `TRACE`.

`LinkGenerator` protokolliert viele Details über die Verarbeitung, die bei der Problembehebung nützlich sein können. Ausführliche Informationen zur URL-Generierung finden Sie unter [Referenz für URL-Generierung](#ugr). Adressen
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/dotnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Mithilfe von Adressen wird bei der URL-Generierung ein Aufruf in der API zur Linkgenerierung an mehrere Endpunktkandidaten gebunden. Adressen sind ein erweiterbares Konzept, das standardmäßig mit zwei Implementierungen bereitgestellt wird: Die Verwendung von *Endpunktname* (`string`) als Adresse: Bietet ähnliche Funktionalität wie der Routenname von MVC.

Wird der <xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata>-Metadatentyp verwendet.

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

> [!IMPORTANT]
> Löst die bereitgestellte Zeichenfolge anhand der Metadaten aller registrierten Endpunkte auf. Löst beim Start eine Ausnahme aus, wenn mehrere Endpunkte den gleichen Namen aufweisen. Wird für die allgemeine Verwendung außerhalb von Controllern und Razor Pages empfohlen.

Die Verwendung von *Routenwerten* (<xref:Microsoft.AspNetCore.Routing.RouteValuesAddress>) als Adresse:

## <a name="routing-basics"></a>Bietet eine ähnliche Funktionalität wie die veraltete Funktion zur URL-Generierung von Controllern und Razor Pages.

Lässt sich nur schwer erweitern und debuggen. Bietet die Implementierung, die von `IUrlHelper`, Taghilfsprogrammen, HTML-Hilfsprogrammen, Aktionsergebnissen usw. verwendet wird.

* Aufgabe des Adressschemas ist es, die Verbindung zwischen der Adresse und den übereinstimmenden Endpunkten anhand von beliebigen Kriterien herzustellen:
* Das Schema für Endpunktenamen führt eine allgemeine Wörterbuchsuche durch.

Das Schema der Routenwerte weist eine komplexe beste Teilmenge des Mengenalgorithmus auf.

Umgebungswerte und explizite Werte Aus der aktuellen Anforderung greift das Routing auf die Routenwerte der aktuellen Anforderung `HttpContext.Request.RouteValues` zu. Die mit der aktuellen Anforderung verbundenen Werte werden als **Umgebungswerte** bezeichnet.

Aus Gründen der Übersichtlichkeit werden in der Dokumentation die an die Methoden übergebenen Routenwerte als **explizite Werte** bezeichnet. Das folgende Beispiel zeigt Umgebungswerte und explizite Werte. Er liefert Umgebungswerte aus der aktuellen Anforderung und explizite Werte: `{ id = 17, }`:

Der vorangehende Code: Gibt `/Widget/Index/17` zurück.

Ruft <xref:Microsoft.AspNetCore.Routing.LinkGenerator> über [DI](xref:fundamentals/dependency-injection) ab.

* Der folgende Code liefert keine Umgebungswerte und keine expliziten Werte: `{ controller = "Home", action = "Subscribe", id = 17, }`:
* Die vorhergehende Methode gibt `/Home/Subscribe/17` zurück

Der folgende Code in `WidgetController` gibt `/Widget/Subscribe/17` zurück: Der folgende Code stellt den Controller aus den Umgebungswerten in der aktuellen Anforderung und explizite Werte dar: `{ action = "Edit", id = 17, }`: Für den Code oben gilt: `/Gadget/Edit/17` wird zurückgegeben.

<xref:Microsoft.AspNetCore.Mvc.ControllerBase.Url> ruft die <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>-Schnittstelle ab.

* generiert eine URL mit einem absoluten Pfad für eine Aktionsmethode.
* Die URL enthält den angegebenen `action`-Namen und `route`-Werte.
* Sie liefert Umgebungswerte aus der aktuellen Anforderung und explizite Werte: `{ page = "./Edit, id = 17, }`:
* Im vorangehenden Code wird `url` auf `/Edit/17` festgelegt, wenn die Option zum Bearbeiten der Razor Page die folgende Seitenanweisung enthält:
* Wenn die Routenvorlage `"{id:int}"` nicht in der Seite „Bearbeiten“ enthalten ist, ist `url` gleich `/Edit?id=17`.
* Das Verhalten der <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>-Schnittstelle von MVC fügt zusätzlich zu den hier beschriebenen Regeln eine weitere Komplexitätsebene hinzu: `IUrlHelper` liefert immer die Routenwerte aus der aktuellen Anforderung als Umgebungswerte.
<!-- fix [middleware](xref:fundamentals/middleware/index) -->
[IUrlHelper.action](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*) kopiert immer die aktuellen Routenwerte `action` und `controller` als explizite Werte, sofern sie nicht vom Entwickler außer Kraft gesetzt werden. [IUrlHelper.page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*) kopiert immer den aktuellen Routenwert `page` als expliziten Wert, sofern er nicht außer Kraft gesetzt wird. `IUrlHelper.Page` setzt immer den aktuellen Routenwert `handler` mit `null` als expliziten Wert außer Kraft, sofern er nicht außer Kraft gesetzt wird.

### <a name="url-matching"></a>Benutzer sind oft von den Verhaltensdetails der Umgebungswerte überrascht, da MVC anscheinend nicht den eigenen Regeln folgt.

Aus Verlaufs- und Kompatibilitätsgründen weisen bestimmte Routenwerte wie `action`, `controller`, `page` und `handler` ein spezielles Verhalten auf. Die äquivalente Funktionalität, die durch `LinkGenerator.GetPathByAction` und `LinkGenerator.GetPathByPage` bereitgestellt wird, verdoppelt diese Anomalien von `IUrlHelper` aus Kompatibilitätsgründen. URL-Generierungsprozess

Sobald die Gruppe der Endpunktkandidaten ermittelt ist, wird der URL-Generierungsalgorithmus angewendet: Die Endpunkte werden iterativ verarbeitet. Das erste erfolgreiche Ergebnis wird zurückgegeben. Der erste Schritt in diesem Prozess wird als **Routenwertinvalidierung** bezeichnet.

Die Routenwertinvalidierung ist der Prozess, bei dem das Routing entscheidet, welche Routenwerte aus den Umgebungswerten verwendet und welche ignoriert werden sollen. Jeder Umgebungswert wird berücksichtigt und entweder mit den expliziten Werten kombiniert oder aber ignoriert.

Denken Sie daran, dass Umgebungswerte Anwendungsentwicklern in allgemeinen Fällen das Schreiben von Code sparen können.

In der Regel sind die Szenarios, in denen Umgebungswerte hilfreich sind, mit MVC verknüpft: Bei der Verknüpfung mit einer anderen Aktion im gleichen Controller muss der Controllername nicht angegeben werden.

Bei der Verknüpfung mit einem anderen Controller im gleichen Bereich muss der Bereich nicht angegeben werden. Bei der Verknüpfung mit der gleichen Aktionsmethode müssen keine Routenwerte angegeben werden. Bei der Verknüpfung mit einem anderen Teil der App sollen keine Routenwerte übertragen werden, die für diesen Teil der App irrelevant sind. Aufrufe an `LinkGenerator` oder `IUrlHelper`, die `null` zurückgeben, sind meist dadurch bedingt, dass die Routenwertinvalidierung nicht verstanden wurde.

Beheben Sie die Routenwertinvalidierung, indem Sie explizit mehr Routenwerte angeben, um zu prüfen, ob das Problem dadurch gelöst wird. Bei der Routenwertinvalidierung wird davon ausgegangen, dass das URL-Schema der Anwendung hierarchisch ist, mit einer von links nach rechts gebildeten Hierarchie. Sehen Sie sich die einfache Controllerroutenvorlage `{controller}/{action}/{id?}` an, um ein Gespür dafür zu bekommen, wie dies in der Praxis funktioniert. Durch eine **Änderung** auf einen Wert werden alle rechts angezeigten Routenwerte **ungültig**. Dies spricht für die These von der Hierarchie.

<a name="lg"></a>

### <a name="url-generation"></a>Wenn die App einen Umgebungswert für `id` hat und der Vorgang einen anderen Wert für `controller` angibt:

`id` wird nicht wiederverwendet, weil `{controller}` links von `{id?}` steht. Einige Beispiele veranschaulichen dieses Prinzip:

Wenn die expliziten Werte einen Wert für `id` enthalten, wird der Umgebungswert für `id` ignoriert. Die Umgebungswerte für `controller` und `action` können verwendet werden.

Wenn die expliziten Werte einen Wert für `action` enthalten, wird jeder Umgebungswert für `action` ignoriert.

* Die Umgebungswerte für `controller` können verwendet werden.
* Wenn sich der explizite Wert für `action` von dem Umgebungswert für `action` unterscheidet, wird der Wert `id` nicht verwendet.
* Wenn der explizite Wert für `action` mit dem Umgebungswert für `action` übereinstimmt, kann der Wert `id` verwendet werden.

Wenn die expliziten Werte einen Wert für `controller` enthalten, wird jeder Umgebungswert für `controller` ignoriert. Wenn sich der explizite Wert für `controller` von dem Umgebungswert für `controller` unterscheidet, werden die Werte `action` und `id` nicht verwendet. Wenn der explizite Wert für `controller` mit dem Umgebungswert für `controller` übereinstimmt, können die Werte `action` und `id` verwendet werden. Dieser Prozess wird zusätzlich durch die vorhandenen Attributrouten und dedizierten konventionellen Routen erschwert.

> [!TIP]
> Konventionelle Routen des Controllers wie `{controller}/{action}/{id?}` legen eine Hierarchie mithilfe von Routenparametern fest. Bei [bestimmten konventionellen Routen](xref:mvc/controllers/routing#dcr) und [Attributrouten](xref:mvc/controllers/routing#ar) zu Controllern und Razor Pages:

Gibt es eine Hierarchie für Routenwerte. Werden diese nicht in der Vorlage angezeigt. Für diese Fälle definiert die URL-Generierung das Konzept der **erforderlichen Werte**.

Bei Endpunkten, die von Controllern und Razor Pages erstellt wurden, sind erforderliche Werte angegeben, die eine Routenwertinvalidierung ermöglichen. Der Algorithmus der Routenwertinvalidierung im Detail: Die erforderlichen Wertnamen werden mit den Routenparametern kombiniert und dann von links nach rechts verarbeitet.

Für jeden Parameter werden der Umgebungswert und der explizite Wert verglichen:

Wenn der Umgebungswert und der explizite Wert gleich sind, wird der Prozess fortgesetzt. Wenn der Umgebungswert vorhanden ist und der explizite Wert nicht, wird der Umgebungswert bei der URL-Generierung verwendet.

### <a name="create-routes"></a>Wenn der Umgebungswert nicht vorhanden ist und der explizite Wert vorhanden ist, verwerfen Sie den Umgebungswert und alle nachfolgenden Umgebungswerte.

Wenn der Umgebungswert und der explizite Wert vorhanden und die beiden Werte unterschiedlich sind, verwerfen Sie den Umgebungswert und alle nachfolgenden Umgebungswerte. An diesem Punkt ist der Vorgang zur URL-Generierung bereit, Routeneinschränkungen auszuwerten. Die akzeptierten Werte werden mit den Standardwerten der Parameter kombiniert, die für Einschränkungen bereitgestellt werden.

Wenn alle Einschränkungen erfüllt sind, wird der Vorgang fortgesetzt. Als Nächstes können die **akzeptierten Werte** verwendet werden, um die Routenvorlage zu erweitern.

Die Routenvorlage wird verarbeitet: Von links nach rechts. Für jeden Parameter wird der akzeptierte Wert ersetzt. In den folgenden Sonderfällen: Wenn bei den akzeptierten Werten ein Wert fehlt und der Parameter einen Standardwert hat, wird der Standardwert verwendet.

Wenn bei den akzeptierten Werten ein Wert fehlt und der Parameter optional ist, wird die Verarbeitung fortgesetzt.

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Wenn irgendein Routenparameter rechts neben einem fehlenden optionalen Parameter einen Wert hat, schlägt der Vorgang fehl. Zusammenhängende Parameter mit Standardwerten und optionale Parameter werden, wenn möglich, reduziert dargestellt.

Explizit bereitgestellte Werte, für die keine Übereinstimmungen mit einem Routensegment ermittelt werden, werden der Abfragezeichenfolge hinzugefügt. In der folgenden Tabelle werden die Ergebnisse dargestellt, die aus der Verwendung der Routenvorlage `{controller}/{action}/{id?}` hervorgehen: Umgebungswerte

Explizite Werte Ergebnis title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

'Identity' 'Let's Encrypt' 'Razor'

'SignalR' uid:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity' 'Let's Encrypt'

'Razor' 'SignalR' uid:

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

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
> 'Blazor' 'Identity'

'Let's Encrypt'

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{*article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity'

'Let's Encrypt'

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

'Razor'

!['SignalR' uid:](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor' 'Identity'

> [!TIP]
> 'Let's Encrypt' 'Razor' 'SignalR' uid:

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

'Blazor' 'Identity' 'Let's Encrypt'

'Razor' 'SignalR' uid:

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

> [!NOTE]
> 'Blazor'

'Identity'

## <a name="use-routing-middleware"></a>'Let's Encrypt'

'Razor'

'SignalR' uid:

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* 'Identity'
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

'Let's Encrypt'

| 'Razor'                    | 'SignalR' uid:                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' |
| `/package/track/-3`    | 'Identity' 'Let's Encrypt' |
| `/package/track/-3/`   | 'Razor' 'SignalR' uid: |
| `/package/track/`      | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:              |
| `GET /hello/Joe`       | 'Blazor'                                          |
| `POST /hello/Joe`      | 'Identity' |
| `GET /hello/Joe/Smith` | 'Let's Encrypt'              |

'Razor' 'SignalR' uid:

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

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

'Blazor' 'Identity' 'Let's Encrypt' 'Razor'

'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

## <a name="route-template-reference"></a>'Blazor'

'Identity' 'Let's Encrypt' 'Razor' 'SignalR' uid:

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity'

'Let's Encrypt' 'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

* `/files/myFile.txt`
* `/files/myFile`

'Identity' 'Let's Encrypt' 'Razor' 'SignalR' uid:

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity'

'Let's Encrypt' 'Razor' 'SignalR' uid: ----------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

'Identity' 'Let's Encrypt' 'Razor' 'SignalR' uid:

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity'

'Let's Encrypt'

| 'Razor'                           | 'SignalR' uid:    | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | 'Blazor'                                     |
| `{Page=Home}`                            | `/`                     | 'Identity'                                         |
| `{Page=Home}`                            | `/Contact`              | 'Let's Encrypt'                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | 'Razor'                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | 'SignalR' uid: |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:        |

'Blazor' 'Identity'

> [!TIP]
> 'Let's Encrypt'

## <a name="route-constraint-reference"></a>'Razor'

'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity' 'Let's Encrypt'

> [!WARNING]
> 'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor'

| 'Identity' | 'Let's Encrypt' | 'Razor' | 'SignalR' uid: |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: | 'Blazor' |
| `bool` | `{active:bool}` | 'Identity' | 'Let's Encrypt' |
| `datetime` | `{dob:datetime}` | 'Razor' | 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:|
| `decimal` | `{price:decimal}` | 'Blazor' | 'Identity' 'Let's Encrypt'|
| `double` | `{weight:double}` | 'Razor' | 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:|
| `float` | `{weight:float}` | 'Blazor' | 'Identity' 'Let's Encrypt'|
| `guid` | `{id:guid}` | 'Razor' | 'SignalR' uid: |
| `long` | `{ticks:long}` | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: | 'Blazor' |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | 'Identity' |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | 'Let's Encrypt' |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | 'Razor' |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | 'SignalR' uid: |
| `min(value)` | `{age:min(18)}` | `19` | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: |
| `max(value)` | `{age:max(120)}` | `91` | 'Blazor' |
| `range(min,max)` | `{age:range(18,120)}` | `91` | 'Identity' |
| `alpha` | `{name:alpha}` | `Rick` | 'Let's Encrypt' |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | 'Razor' |
| `required` | `{name:required}` | `Rick` | 'SignalR' uid: |

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> 'Identity' 'Let's Encrypt' 'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

## <a name="regular-expressions"></a>'Blazor'

'Identity' 'Let's Encrypt'

'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity'

| 'Let's Encrypt'    | 'Razor'     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity' 'Let's Encrypt'

| 'Razor'   | 'SignalR' uid:    | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: | 'Blazor'               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | 'Identity'     | 'Let's Encrypt'   | 'Razor'     |
| `[a-z]{2}`   | 'SignalR' uid: | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:   | 'Blazor'     |
| `[a-z]{2}`   | 'Identity'        | 'Let's Encrypt'   | 'Razor'    |
| `[a-z]{2}`   | 'SignalR' uid:        | ------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:   | 'Blazor'    |
| `^[a-z]{2}$` | 'Identity'     | 'Let's Encrypt'    | 'Razor' |
| `^[a-z]{2}$` | 'SignalR' uid: | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:    | 'Blazor' |

'Identity'

'Let's Encrypt' 'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

## <a name="custom-route-constraints"></a>'Blazor'

'Identity' 'Let's Encrypt'

'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

'Identity' 'Let's Encrypt'

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="url-generation-reference"></a>'Razor'

'SignalR' uid:

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity'

'Let's Encrypt' 'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor' 'Identity' 'Let's Encrypt'

'Razor' 'SignalR' uid:

| title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:                     | 'Blazor'                        | 'Identity'                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| 'Let's Encrypt'                | 'Razor'                       | `/Home/About`           |
| 'SignalR' uid:                | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: | `/Order/About`          |
| 'Blazor' | 'Identity'                       | `/Home/About`           |
| 'Let's Encrypt'                | 'Razor'        | `/Home/About?color=Red` |

'SignalR' uid:

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

## <a name="complex-segments"></a>'Blazor'

'Identity' 'Let's Encrypt' 'Razor'

::: moniker-end

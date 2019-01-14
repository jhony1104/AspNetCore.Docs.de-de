---
title: Filter in ASP.NET Core
author: ardalis
description: Erfahren Sie, wie Filter funktionieren und wie Sie sie in ASP.NET Core MVC verwenden.
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2018
uid: mvc/controllers/filters
ms.openlocfilehash: d4fe49a9225b9980a956ef9c773ad631beb557ae
ms.sourcegitcommit: cec77d5ad8a0cedb1ecbec32834111492afd0cd2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207459"
---
# <a name="filters-in-aspnet-core"></a>Filter in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) und [Steve Smith](https://ardalis.com/)

In ASP.Net Core MVC ermöglichen *Filter* Ihnen, Code vor oder nach bestimmten Stufen der Anforderungsverarbeitungspipeline auszuführen.

> [!IMPORTANT]
> Die Informationen in diesem Artikel können **nicht** auf Razor-Seiten angewendet werden. In ASP.NET Core 2.1 und in neueren Versionen werden [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) und [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) für Razor-Seiten unterstützt. Weitere Informationen finden Sie unter [Filtermethoden für Razor-Seiten](xref:razor-pages/filter).

 Integrierte Filter sind für folgende Aufgaben zuständig:

 * Autorisierung (der Zugriff auf Ressourcen, für die ein Benutzer nicht autorisiert ist, wird verhindert)
 * Überprüfung von Anforderungen auf die Verwendung von HTTPS
 * Zwischenspeicherung von Antworten (die Anforderungspipeline wird unterbrochen, damit eine zwischengespeicherte Antwort zurückgegeben wird) 

Durch die Erstellung benutzerdefinierter Filter kann mit aktionsübergreifenden Problemen umgegangen werden. Dadurch kann die Duplizierung von Code bei mehreren Aktionen verhindert werden. Sie können zum Beispiel die Fehlerbehandlung in einem Ausnahmefilter konsolidieren.

[Beispiel anzeigen oder von GitHub herunterladen](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).

## <a name="how-do-filters-work"></a>Wie funktionieren Filter?

Filter werden in der *MVC-Aktionsaufrufpipeline* ausgeführt, die manchmal auch als *Filterpipeline* bezeichnet wird.  Die Filterpipeline wird ausgeführt, nachdem MVC die auszuführende Aktion ausgewählt hat.

![Die Anforderung wird von weiterer Middleware, Routingmiddleware, Aktionsauswahl und der MVC-Aktionsaufrufpipeline verarbeitet. Die Verarbeitung von Anforderungen verläuft weiterhin durch Aktionsauswahl, Routingmiddleware und verschiedenen weiteren Middlewares, bevor eine Antwort an den Client gesendet wird.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a>Filtertypen

Jeder Filtertyp wird zu einem anderen Zeitpunkt in der Filterpipeline ausgeführt.

* [Autorisierungfilter](#authorization-filters) werden zuerst ausgeführt und werden verwendet, um festzustellen, ob der aktuelle Benutzer für die aktuelle Anforderung berechtigt ist. Sie können die Pipeline kurzschließen, wenn eine Anforderung nicht autorisiert ist. 

* [Ressourcenfilter](#resource-filters) sind die ersten Filter, die eine Anforderung nach der Autorisierung behandeln.  Diese Filter können vor der restlichen Filterpipeline, und nachdem der Rest der Filterpipeline abgeschlossen wurde, Code ausführen. Sie helfen dabei, das Zwischenspeichern zu implementieren, oder beim Kurzschließen der Filterpipeline zur Verbesserung der Leistung. Außerdem werden Ressourcenfilter vor der Modellbindung ausgeführt und können diese so beeinflussen.

* [Aktionsfilter](#action-filters) können, vor und nach dem Aufruf einer individuelle Aktionsmethode, Code ausführen. Sie können zur Bearbeitung der Argumente, die an eine Aktion übermittelt werden, und des Ergebnisses, das von der Aktion zurückgegeben wird, verwendet werden.

* [Ausnahmefilter](#exception-filters) werden dazu verwendet, globale Richtlinien auf unbehandelte Ausnahmen anzuwenden, die auftreten, bevor etwas in den Antworttext geschrieben wurde.

* [Ergebnisfilter](#result-filters) können, vor und nach dem Ergebnis einer ausgeführten individuellen Aktion, Code ausführen. Sie werden nur ausgeführt, wenn die Aktionsmethode erfolgreich ausgeführt wurde. Ergebnisfilter sind hilfreich für Logik, die die Ausführung von Ansichten oder Formatierern umfasst.

Das folgende Diagramm veranschaulicht, wie diese Filtertypen mit der Filterpipeline interagieren.

![Die Anforderung wird von Autorisierungsfilter, Ressourcenfilter, Modellbindung, Aktionsfilter, Aktionsausführung sowie Aktionsergebniskonvertierung, Ausnahmefilter, Ergebnisfilter und Ergebnisausführung verarbeitet. Beim Verlassen der Pipeline wird die Anforderung nur von Ergebnisfiltern und Ressourcenfiltern verarbeitet, bevor sie an den Client gesendet wird.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a>Implementierung

Filter unterstützen sowohl synchrone als auch asynchrone Implementierungen über verschiedene Schnittstellendefinitionen. 

Synchrone Filter, die Code sowohl vor als auch nach ihrer Pipeline ausführen können, definieren die Methoden On*Stage*Executing und On*Stage*Executed. Zum Beispiel wird `OnActionExecuting` aufgerufen, bevor die Aktionsmethode aufgerufen wird, und `OnActionExecuted` wird aufgerufen, nachdem die Aktionsmethode zurückgegeben wird.

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/SampleActionFilter.cs?name=snippet1)]

Asynchrone Filter definieren eine einzelne On*Stage*ExecutionAsync-Methode. Diese Methode verwendet einen *FilterType*ExecutionDelegate-Delegaten, der die Pipelinestufe des Filters ausführt. Zum Beispiel ruft `ActionExecutionDelegate` die Aktionsmethode oder den nächsten Aktionsfilter auf, und Sie können Code ausführen, bevor oder nachdem Sie sie aufrufen.

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/SampleAsyncActionFilter.cs?highlight=6,8-10,13)]

Sie können Schnittstellen für mehrere Filterstufen in eine einzelne Klasse implementieren. Beispielsweise implementiert die Klasse [ActionFilterAttribute](/dotnet/api/microsoft.aspnetcore.mvc.filters.actionfilterattribute?view=aspnetcore-2.0) `IActionFilter`, `IResultFilter` und deren asynchrone Gegenstücke.

> [!NOTE]
> Implementieren Sie **entweder** die synchrone oder asynchrone Version einer Filterschnittstelle, aber nicht beide. Das Framework prüft zuerst, ob der Filter die asynchrone Schnittstelle implementiert, und wenn dies der Fall ist, ruft es sie auf. Wenn dies nicht der Fall ist, ruft es die Methode(n) der synchronen Schnittstelle auf. Wenn Sie beide Schnittstellen für eine Klasse implementieren würden, würde nur die asynchrone Methode aufgerufen werden. Wenn Sie abstrakte Klassen wie [ActionFilterAttribute](/dotnet/api/microsoft.aspnetcore.mvc.filters.actionfilterattribute?view=aspnetcore-2.0) verwenden, würden Sie nur die synchronen oder asynchronen Methoden für jeden Filtertyp überschreiben.

### <a name="ifilterfactory"></a>IFilterFactory

[IFilterFactory](/dotnet/api/microsoft.aspnetcore.mvc.filters.ifilterfactory) implementiert [IFilterMetadata](/dotnet/api/microsoft.aspnetcore.mvc.filters.ifiltermetadata). Deshalb kann eine `IFilterFactory`-Instanz an einer beliebigen Stelle in der Filterpipeline als `IFilterMetadata`-Instanz verwendet werden. Wenn das Framework den Aufruf des Filters vorbereitet, versucht es ihn in `IFilterFactory` umzuwandeln. Wenn diese Umwandlung gelingt, wird die Methode [CreateInstance](/dotnet/api/microsoft.aspnetcore.mvc.filters.ifilterfactory.createinstance) aufgerufen, um die Instanz `IFilterMetadata` zu erstellen, die aufgerufen wird. Da die exakte Filterpipeline beim Start der Anwendung nicht explizit festgelegt werden muss, wird dadurch ein sehr flexibles Design ermöglicht.

Als weiteres Verfahren zum Erstellen von Filtern, können Sie `IFilterFactory` in Ihre eigenen Implementierungen von Attributen integrieren:

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

### <a name="built-in-filter-attributes"></a>Integrierte Filterattribute

Das Framework enthält integrierte attributbasierte Filter, die Sie als Unterklasse erstellen und anpassen können. Zum Beispiel fügt der folgende Ergebnisfilter der Antwort einen Header hinzu.

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/AddHeaderAttribute.cs?highlight=5,16)]

Wie im obigen Beispiel dargestellt wird, können Filter mithilfe von Attributen Argumente akzeptieren. Sie können dieses Attribut einem Controller oder einer Aktionsmethode hinzufügen, und den Namen und Wert des HTTP-Headers angeben:

[!code-csharp[](./filters/sample/src/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

Das Ergebnis der Aktion `Index` wird unten angezeigt – die Antwortheader werden unten rechts angezeigt.

![Entwicklertools von Microsoft Edge zeigen Antwortheader an, einschließlich Autor Steve Smith @ardalis](filters/_static/add-header.png)

Einige der Filterschnittstellen besitzen entsprechende Attribute, die als Basisklassen für benutzerdefinierte Implementierungen verwendet werden können.

Filterattribute:

* `ActionFilterAttribute`
* `ExceptionFilterAttribute`
* `ResultFilterAttribute`
* `FormatFilterAttribute`
* `ServiceFilterAttribute`
* `TypeFilterAttribute`

`TypeFilterAttribute` und `ServiceFilterAttribute` werden [weiter unten in diesem Artikel](#dependency-injection) erklärt.

## <a name="filter-scopes-and-order-of-execution"></a>Filterbereiche und Reihenfolge der Ausführung

Der Pipeline kann an einem von drei *Bereichen* ein Filter hinzugefügt werden. Sie können einen Filter einer bestimmten Aktionsmethode oder Controllerklasse hinzufügen, indem Sie ein Attribut verwenden. Alternativ können Sie einen Filter global für alle Controller und Aktionen registrieren. Hierzu fügen Sie ihn der `MvcOptions.Filters`-Sammlung in `ConfigureServices` hinzu:

[!code-csharp[](./filters/sample/src/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-8)]

### <a name="default-order-of-execution"></a>Standardreihenfolge der Ausführung

Wenn es mehrere Filter für eine bestimmte Stufe der Pipeline gibt, bestimmt der Bereich die Standardreihenfolge der Filterausführung.  Globale Filter umfassen Klassenfilter, welche hingegen Methodenfilter umfassen. Diese Verschachtelung ähnelt russischen Puppen, weil jede Erweiterung des Bereichs den vorherigen Bereich umschließt wie eine [Matroschka](https://wikipedia.org/wiki/Matryoshka_doll). Normalerweise erhalten Sie das erwünschte überschreibende Verhalten, ohne dass Sie die Reihenfolge explizit bestimmen müssen.

Der *nach dem Ereignis* auszuführende Filtercode wird in umgekehrter Reihenfolge zum Code *vor dem Ereignis* ausgeführt. Die Sequenz sieht folgendermaßen aus:

* Der *before*-Code der Filter, die global angewendet werden
  * Der *before*-Code der Filter, die auf Controller angewendet werden
    * Der *before*-Code der Filter, die auf Aktionsmethoden angewendet werden
    * Der *after*-Code der Filter, die auf Aktionsmethoden angewendet werden
  * Der *after*-Code der Filter, die auf Controller angewendet werden
* Der *after*-Code der Filter, die global angewendet werden
  
Hier ist ein Beispiel, das die Reihenfolge, in der Filtermethoden für synchrone Aktionsfilter aufgerufen werden, veranschaulicht.

| Sequenz | Filterbereich | Filtermethode |
|:--------:|:------------:|:-------------:|
| 1 | Global | `OnActionExecuting` |
| 2 | Controller | `OnActionExecuting` |
| 3 | Methode | `OnActionExecuting` |
| 4 | Methode | `OnActionExecuted` |
| 5 | Controller | `OnActionExecuted` |
| 6 | Global | `OnActionExecuted` |

Diese Sequenz veranschaulicht Folgendes:

* Der Methodenfilter ist innerhalb des Controllerfilters geschachtelt.
* Der Controllerfilter ist innerhalb des globalen Filters geschachtelt. 

Anders ausgedrückt: Wenn Sie sich in der Methode On*Stage*ExecutionAsync des asynchronen Filters befinden, werden alle Filter mit einem engeren Bereich ausgeführt, während Ihr Code sich auf dem Stapel befindet.

> [!NOTE]
> Jeder Controller, der von der Basisklasse `Controller` erbt, enthält die Methoden `OnActionExecuting` und `OnActionExecuted`. Diese Methode umschließt die Filter, die für eine bestimmte Aktion ausgeführt werden: `OnActionExecuting` wird vor allen anderen Filtern aufgerufen, und `OnActionExecuted` wird nach allen Filtern aufgerufen.

### <a name="overriding-the-default-order"></a>Überschreiben der Standardreihenfolge

Sie können die Standardsequenz der Ausführung überschreiben, indem Sie `IOrderedFilter` implementieren. Diese Schnittstelle macht die Eigenschaft `Order` verfügbar, die bei der Bestimmung der Ausführungsreihenfolge Vorrang vor dem Bereich hat. Bei einem Filter mit einem niedrigeren `Order`-Wert wird der *before*-Code vor dem Code eines Filters mit einem höheren `Order`-Wert ausgeführt. Bei einem Filter mit einem niedrigeren `Order`-Wert wird der *after*-Code nach dem Code eines Filters mit einem höheren `Order`-Wert ausgeführt. Sie können einen Konstruktorparameter verwenden, um die Eigenschaft `Order` festzulegen:

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

Wenn Sie dieselben drei Aktionsfilter wie im obigen Beispiel verwenden, die Eigenschaft `Order` des Controllers und des globalen Filters jedoch auf jeweils 1 und 2 festlegen, wäre die Ausführungsreihenfolge umgekehrt.

| Sequenz | Filterbereich | `Order` -Eigenschaft | Filtermethode |
|:--------:|:------------:|:-----------------:|:-------------:|
| 1 | Methode | 0 | `OnActionExecuting` |
| 2 | Controller | 1  | `OnActionExecuting` |
| 3 | Global | 2  | `OnActionExecuting` |
| 4 | Global | 2  | `OnActionExecuted` |
| 5 | Controller | 1  | `OnActionExecuted` |
| 6 | Methode | 0  | `OnActionExecuted` |

Die Eigenschaft `Order` hat eine höhere Priorität als der Bereich bei der Bestimmung der Reihenfolge, in der Filter ausgeführt werden. Filter werden erst nach der Reihenfolge sortiert, und dann werden Gleichstände über den Bereich aufgelöst. Alle integrierten Filter implementieren `IOrderedFilter` und legen den Standartwert von `Order` auf 0 fest. Bei integrierten Filtern bestimmt der Geltungsbereich die Reihenfolge, falls Sie nicht `Order` auf einen Wert ungleich 0 festlegen.

## <a name="cancellation-and-short-circuiting"></a>Abbrechen und Kurzschließen

Sie können die Filterpipeline jederzeit kurzschließen, indem Sie die Eigenschaft `Result` auf den Parameter `context` in der Filtermethode festlegen. Zum Beispiel verhindert der folgende Ressourcenfilter, dass der Rest der Pipeline ausgeführt wird.

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?highlight=12,13,14,15)]

Im folgenden Code verwenden sowohl der Filter `ShortCircuitingResourceFilter` und der Filter `AddHeader` die Aktionsmethode `SomeResource` als Ziel. Die `ShortCircuitingResourceFilter`:

* Der Filter wird zuerst ausgeführt, da es sich um einen Ressourcenfilter handelt und `AddHeader` ein Aktionsfilter ist.
* Der Filter unterbricht alle verbleibenden Pipelineschritte.

Der `AddHeader`-Filter wird daher nie für die `SomeResource`-Aktion ausgeführt. Dasselbe Verhalten würde auch dann auftreten, wenn beide Filter auf Aktionsmethodenebene angewendet würden, wenn `ShortCircuitingResourceFilter` zuerst ausgeführt wird. Der `ShortCircuitingResourceFilter` wird aufgrund seines Filtertyps oder durch die explizite Verwendung der `Order`-Eigenschaft zuerst ausgeführt.

[!code-csharp[](./filters/sample/src/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a>Dependency Injection

Filter können nach Typ oder Instanz hinzugefügt werden. Wenn Sie eine Instanz hinzufügen, wird diese Instanz für jede Anforderung verwendet. Wenn Sie einen Typ hinzufügen, wird der Filter typ-aktiviert, d.h. für jede Anforderung wird eine Instanz erstellt, und alle Konstruktorabhängigkeiten werden durch [Dependency Injection](../../fundamentals/dependency-injection.md) (DI) aufgefüllt. Das Hinzufügen eines Filters über einen Typ entspricht `filters.Add(new TypeFilterAttribute(typeof(MyFilter)))`.

Filter, die als Attribute implementiert und Controllerklassen oder Aktionsmethoden direkt hinzugefügt werden, können keine Konstruktorabhängigkeiten von [Dependency Injection](../../fundamentals/dependency-injection.md) (DI) erhalten. Dies liegt daran, dass die Konstruktorparameter für Attribute dort bereitgestellt werden müssen, wo sie angewendet werden. Dies ist eine Einschränkung der Funktionsweise von Attributen.

Wenn Ihre Filter Abhängigkeiten enthalten, die Sie von DI beziehen, gibt es mehrere unterstützte Ansätze. Sie können Ihren Filter auf eine Klasse oder eine Aktionsmethode anwenden, indem Sie eine der folgenden Methoden verwenden:

* `ServiceFilterAttribute`
* `TypeFilterAttribute`
* `IFilterFactory` in Ihrem Attribut implementiert

> [!NOTE]
> Sie sollten die Protokollierung von DI verwenden. Es wird jedoch empfohlen, dass Sie vermeiden, Filter ausschließlich für die Protokollierung zu erstellen und zu verwenden, da [integrierte Frameworkprotokollierungsfeatures](xref:fundamentals/logging/index) möglicherweise bereits das enthalten, was Sie brauchen. Wenn Sie Ihren Filtern die Protokollierung hinzufügen, sollten diese sich auf Probleme mit Geschäftsdomänen oder auf das spezifische Verhalten Ihres Filters beziehen anstelle von MVC-Aktionen oder anderen Frameworkereignissen.

### <a name="servicefilterattribute"></a>ServiceFilterAttribute

Die Typen der Dienstfilterimplementierung werden in der DI registriert. Ein `ServiceFilterAttribute` ruft eine Instanz des Filter von DI ab. Fügen Sie das `Startup.ConfigureServices` dem Container in `ServiceFilterAttribute` hinzu, und verweisen Sie darauf in einem `[ServiceFilter]`-Attribut:

[!code-csharp[](./filters/sample/src/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=11)]

[!code-csharp[](../../mvc/controllers/filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

Bei `ServiceFilterAttribute` ist die Einstellung `IsReusable` ein Hinweis darauf, dass die Filterinstanz *möglicherweise* außerhalb des Anforderungsbereich, in dem sie erstellt wurde, wiederverwendet wird. Das Framework bietet keine Garantie, dass eine einzelne Instanz des Filters erstellt wird oder der Filter nicht erneut später vom DI-Container angefordert wird. Verwenden Sie nicht `IsReusable` bei einem Filter, der von Diensten mit einer anderen Lebensdauer als der eines Singletons abhängig ist.

Die Verwendung von `ServiceFilterAttribute` ohne Registrierung der Filtertypergebnisse löst eine Ausnahme aus:

```
System.InvalidOperationException: No service for type
'FiltersSample.Filters.AddHeaderFilterWithDI' has been registered.
```

`ServiceFilterAttribute` implementiert `IFilterFactory`. `IFilterFactory` macht die `CreateInstance`-Methode zum Erstellen einer `IFilterMetadata`-Instanz verfügbar. Die `CreateInstance`-Methode lädt den angegebenen Typ aus dem Dienstecontainer (DI).

### <a name="typefilterattribute"></a>TypeFilterAttribute

`TypeFilterAttribute` ähnelt `ServiceFilterAttribute`. Der zugehörige Typ wird allerdings nicht direkt vom DI-Container aufgelöst. Stattdessen wird der Typ mithilfe von `Microsoft.Extensions.DependencyInjection.ObjectFactory` instanziiert.

Dieser Unterschied hat folgende Auswirkungen:

* Typen, auf die mithilfe von `TypeFilterAttribute` verwiesen wird, müssen nicht zuerst beim Container registriert werden.  Stattdessen werden die Abhängigkeiten vom Container eingebracht. 
* `TypeFilterAttribute` kann optional Konstruktorargumente für den Typ akzeptieren.

Bei `TypeFilterAttribute` ist die Einstellung `IsReusable` ein Hinweis darauf, dass die Filterinstanz *möglicherweise* außerhalb des Anforderungsbereich, in dem sie erstellt wurde, wiederverwendet wird. Das Framework bietet keine Garantie dafür, dass eine einzelne Instanz des Filters erstellt wird. Verwenden Sie nicht `IsReusable` bei einem Filter, der von Diensten mit einer anderen Lebensdauer als der eines Singletons abhängig ist.

Das folgende Beispiel demonstriert, wie Sie Argumente durch Verwendung von `TypeFilterAttribute` an einen Typ übergeben:

[!code-csharp[](../../mvc/controllers/filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/src/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

### <a name="ifilterfactory-implemented-on-your-attribute"></a>IFilterFactory für Ihr Attribut implementiert

Angenommen, Sie verfügen über einen Filter, der

* keine Argumente erfordert
* und dessen Konstruktorabhängigkeiten durch DI eingebracht werden müssen.

In diesem Fall können Sie Ihr eigenes benanntes Attribut für Klassen und Methoden anstelle von `[TypeFilter(typeof(FilterType))]` verwenden. Im folgenden Filter wird gezeigt, wie dies implementiert werden kann:

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

Dieser Filter kann mithilfe der Syntax `[SampleActionFilter]` auf Klassen oder Methoden angewendet werden, um nicht `[TypeFilter]` oder `[ServiceFilter]` verwenden zu müssen.

## <a name="authorization-filters"></a>Autorisierungsfilter

Für *Autorisierungsfilter* gilt Folgendes:

* Sie steuern den Zugriff auf Aktionsmethoden.
* Sie werden als erste Filter in der Filterpipeline ausgeführt. 
* Sie verfügen nur über eine Methode, die vor der Aktion ausgeführt wird, nicht jedoch über eine Methode, die nach der Aktion ausgeführt wird. 

Sie sollten nur einen eigenen Autorisierungsfilter schreiben, wenn Sie Ihr eigenes Autorisierungsframework schreiben. Statt einen benutzerdefinierten Filter zu schreiben, wird empfohlen, dass Sie Ihre Autorisierungsrichtlinien konfigurieren oder eine benutzerdefinierte Autorisierungsrichtlinie schreiben. Die Implementierung eines integrierten Filters ist nur für das Aufrufen des Autorisierungssystems zuständig.

Sie sollten innerhalb von Autorisierungsfiltern keine Ausnahmen auslösen, da diese nicht (von Ausnahmefiltern) behandelt werden. Stattdessen sollten Sie beim Auftreten einer Ausnahme eine Aufforderung verwenden.

Weitere Informationen finden Sie unter [Autorisierung](xref:security/authorization/introduction).

## <a name="resource-filters"></a>Ressourcenfilter

* Sie implementieren entweder die Schnittstelle `IResourceFilter` oder `IAsyncResourceFilter`.
* Ihre Ausführung umschließt die meisten Pipelineschritte. 
* Nur [Autorisierungsfilter](#authorization-filters) werden vor Ressourcenfiltern ausgeführt.

Ressourcenfilter sind hilfreich, wenn die meisten mit einer Anforderung verbundenen Schritte übersprungen werden sollen. Ein Filter, der eine Zwischenspeicherung vornimmt, kann z.B. die verbleibenden Pipelineschritte vermeiden, wenn sich die Antwort bereits im Cache befindet.

Der weiter oben dargestellte [kurzschließende Ressourcenfilter](#short-circuiting-resource-filter) ist ein Beispiel für einen Ressourcenfilter. Ein weiteres Beispiel ist [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/1.1.1/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):

* Dieses Attribut verhindert, dass die Modellbindung auf die Daten des Formulars zugreifen kann. 
* Es ist nützlich für große Dateiuploads, wenn Sie verhindern wollen, dass das Formular in den Arbeitsspeicher eingelesen wird.

## <a name="action-filters"></a>Aktionsfilter

*Für Aktionsfilter gilt Folgendes*:

* Sie implementieren entweder die Schnittstelle `IActionFilter` oder `IAsyncActionFilter`.
* Ihre Ausführung umfasst die Ausführung von Aktionsmethoden.

Hier ist ein Beispiel für einen Aktionsfilter:

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/SampleActionFilter.cs?name=snippet_ActionFilter)]

[ActionExecutingContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.actionexecutingcontext) stellt die folgenden Eigenschaften zur Verfügung:

* `ActionArguments` erlaubt Ihnen, die Bearbeitung der Eingaben in die Aktion.
* `Controller` erlaubt Ihnen die Bearbeitung der Controllerinstanz. 
* `Result` schließt die Ausführung der Aktionsmethode und nachfolgende Aktionsfilter kurz. Eine Ausnahme auszulösen verhindert auch die Ausführung der Aktionsmethode und nachfolgender Filter, wird jedoch wie ein Fehler behandelt, und nicht wie ein erfolgreiches Ergebnis.

[ActionExecutedContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.actionexecutedcontext) stellt `Controller` und `Result` sowie folgende Eigenschaften zur Verfügung:

* `Canceled` ist TRUE, wenn die Aktionsausführung durch einen anderen Filter kurzgeschlossen wurde.
* `Exception` ist ungleich NULL, wenn die Aktion oder ein nachfolgender Aktionsfilter eine Ausnahme ausgelöst hat. Diese Eigenschaft auf NULL zu setzen, „behandelt“ eine Ausnahme, und `Result` wird ausgeführt, als ob es standardgemäß von der Aktionsmethode zurückgegeben wurde.

Bei einem `IAsyncActionFilter` hat der Aufruf von `ActionExecutionDelegate` folgende Auswirkungen:

* Alle nachfolgenden Aktionsfilter und die Aktionsmethode werden ausgeführt.
* `ActionExecutedContext` wird zurückgegeben. 

Weisen Sie einer Ergebnisinstanz `ActionExecutingContext.Result` zu, und rufen Sie nicht `ActionExecutionDelegate` auf, um die Pipeline kurzzuschließen.

Das Framework stellt ein abstraktes `ActionFilterAttribute` bereit, dass Sie als Unterklasse verwenden können. 

Sie können einen Aktionsfilter verwenden, um den Modellzustand zu überprüfen und Fehler zurückzugeben, wenn der Zustand ungültig ist:

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/ValidateModelAttribute.cs)]

Die Methode `OnActionExecuted` wird nach der Aktionsmethode ausgeführt, und sie kann die Ergebnisse der Aktion durch die Eigenschaft `ActionExecutedContext.Result` sehen und bearbeiten. `ActionExecutedContext.Canceled` wird auf TRUE festgelegt, wenn die Ausführung der Aktion durch einen anderen Filter kurzgeschlossen wurde. `ActionExecutedContext.Exception` wird auf einen Wert festgelegt, der ungleich NULL ist, wenn eine Aktion oder ein nachfolgender Aktionsfilter eine Ausnahme ausgelöst hat. Wenn für `ActionExecutedContext.Exception` NULL festgelegt wird,

* werden Ausnahmen effektiv behandelt,
* und `ActionExectedContext.Result` wird so ausgeführt, als würde die Eigenschaft normal von der Aktionsmethode zurückgegeben werden.

## <a name="exception-filters"></a>Ausnahmefilter

*Ausnahmefilter* implementieren entweder die `IExceptionFilter`- oder `IAsyncExceptionFilter`-Schnittstelle. Sie können verwendet werden, um gängige Fehlerbehandlungsrichtlinien für eine App zu implementieren. 

Der folgende Beispielausnahmefilter verwendet eine benutzerdefinierte Fehleransicht für Entwickler, um Details über Ausnahmen anzuzeigen, die auftreten, wenn die App sich in der Entwicklung befindet:

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/CustomExceptionFilterAttribute.cs?name=snippet_ExceptionFilter&highlight=1,14)]

Für Ausnahmefilter gilt Folgendes:

* Sie verfügen nicht über vorangehende oder darauffolgende Ereignisse. 
* Sie implementieren `OnException` oder `OnExceptionAsync`. 
* Sie behandeln Ausnahmefehler, die bei der Controllererstellung, bei der [Modellbindung](../models/model-binding.md), bei Aktionsfiltern oder bei Aktionsmethoden auftreten. 
* Sie erfassen keine Ausnahmen, die in Ressourcenfiltern, Ergebnisfiltern oder bei der Ausführung von MVC-Ergebnissen auftreten.

Legen Sie die Eigenschaft `ExceptionContext.ExceptionHandled` auf TRUE fest, oder schreiben Sie eine Antwort, um eine Ausnahme zu behandeln. Dadurch wird die Weitergabe der Ausnahme beendet. Eine Ausnahmefilter kann eine Ausnahme nicht in ein „erfolgreiches Ergebnis“ umwandeln. Nur ein Aktionsfilter kann das.

> [!NOTE]
> In ASP.NET Core 1.1 wird die Antwort nicht gesendet, wenn Sie `ExceptionHandled` auf TRUE festlegen **und** eine Antwort schreiben. In diesem Szenario sendet ASP.NET Core 1.0 die Antwort und ASP.NET Core 1.1.2 kehrt zum Verhalten von 1.0 zurück. Weitere Informationen finden Sie unter [Problem #5594](https://github.com/aspnet/Mvc/issues/5594) im GitHub-Repository. 

Für Ausnahmefilter gilt Folgendes:

* Sie eignen sich für das Abfangen von Ausnahmen, die in MVC-Aktionen auftreten.
* Sie sind im Vergleich zu Middleware für die Fehlerbehandlung weniger flexibel. 

Sie sollten bei der Ausnahmebehandlung vorzugsweise Middleware verwenden. Verwenden Sie Ausnahmefilter nur dann, wenn Sie für die Fehlerbehandlung auf Grundlage einer ausgewählten MVC-Aktion eine *andere Strategie* anwenden müssen. Zum Beispiel besitzt Ihre App möglicherweise Aktionsmethoden für beide API-Endpunkte und für Ansichten bzw. HTML. Die API-Endpunkte können Fehlerinformationen als JSON zurückgeben, während ansichtsbasierte Aktionen eine Fehlerseite als HTML zurückgeben können.

`ExceptionFilterAttribute` kann als Unterklasse verwendet werden. 

## <a name="result-filters"></a>Ergebnisfilter

* Sie implementieren entweder die Schnittstelle `IResultFilter` oder `IAsyncResultFilter`.
* Ihre Ausführung umfasst die Ausführung von Aktionsergebnissen. 

Hier ist ein Beispiel für einen Ergebnisfilter, der einen HTTP-Header hinzufügt.

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

Die Art des Ergebnisses, das ausgeführt wird, hängt von der jeweiligen Aktion ab. Eine MVC-Aktion, die eine Ansicht zurückgibt, würde alle Razor-Verarbeitungen als Teil der Ausführung von `ViewResult` beinhalten. Eine API-Methode führt möglicherweise eine Art Serialisierung als Teil der Ausführung des Ergebnisses durch. Weitere Informationen zu [Aktionsergebnissen](actions.md)

Ergebnisfilter werden nur für erfolgreiche Ergebnisse ausgeführt, wenn also die Aktion oder Aktionsfilter ein Aktionsergebnis erzeugen. Ergebnisfilter werden nicht ausgeführt, wenn Ausnahmefilter eine Ausnahme behandeln.

Die Methode `OnResultExecuting` kann die Ausführung des Aktionsergebnisses und nachfolgende Ergebnisfilter durch Festlegen von `ResultExecutingContext.Cancel` auf TRUE kurzschließen. Im Allgemeinen sollten Sie beim Kurzschließen an das Antwortobjekt schreiben, um das Erstellen einer leeren Antwort zu vermeiden. Beim Auslösen einer Ausnahme geschieht Folgendes:

* Die Ausführung des Aktionsergebnisses und der nachfolgenden Filter wird verhindert.
* Die ausgelöste Ausnahme wird nicht als erfolgreiches Ergebnis, sondern als Fehler behandelt.

Wenn die Methode `OnResultExecuted` ausgeführt wird, wurde die Antwort wahrscheinlich an den Client gesendet und kann nicht weiterhin geändert werden (außer es wurde eine Ausnahme ausgelöst). `ResultExecutedContext.Canceled` wird auf TRUE festgelegt, wenn die Ausführung des Aktionsergebnisses durch einen anderen Filter kurzgeschlossen wurde.

`ResultExecutedContext.Exception` wird auf einen Wert festgelegt, der ungleich NULL ist, wenn das Aktionsergebnis oder ein nachfolgender Ergebnisfilter eine Ausnahme ausgelöst hat. Das Festlegen von `Exception` auf NULL „behandelt“ eine Ausnahme und verhindert, dass die Ausnahme zu einem späteren Zeitpunkt in der Pipeline ein weiteres Mal von MVC ausgelöst wird. Wenn Sie eine Ausnahme in einem Ergebnisfilter verarbeiten, sind Sie möglicherweise nicht in der Lage, Daten in die Antwort zu schreiben. Wenn das Aktionsergebnis während der Ausführung ausgelöst wird und die Header bereits an den Client übergeben wurden, gibt es keinen zuverlässigen Mechanismus zum Senden von Fehlercode.

Bei einem `IAsyncResultFilter` führt ein Aufruf von `await next` auf `ResultExecutionDelegate` alle nachfolgenden Ergebnisfilter und das Aktionsergebnis aus. Legen Sie `ResultExecutingContext.Cancel` auf TRUE fest, und rufen Sie nicht `ResultExectionDelegate` auf, um die Pipeline kurzzuschließen.

Das Framework stellt ein abstraktes `ResultFilterAttribute` bereit, dass Sie als Unterklasse verwenden können. Die weiter oben dargestellte Klasse [AddHeaderAttribute](#add-header-attribute) ist ein Beispiel für ein Ergebnisfilterattribut.

## <a name="using-middleware-in-the-filter-pipeline"></a>Verwenden von Middleware in der Filterpipeline

Ressourcenfilter funktionieren wie [Middleware](xref:fundamentals/middleware/index), sie umschließen alle Ausführungen, die später in der Pipeline enthalten sind. Filter unterscheiden sich jedoch insofern von Middleware, dass sie Teil von MVC sind, was heißt, dass sie Zugriff auf MVC-Kontext und Konstrukte haben.

In ASP.NET Core 1.1 können Sie Middleware in der Filterpipeline verwenden. Das sollten Sie tun, wenn Sie eine Middlewarekomponente verwenden, die Zugriff auf MVC-Routendaten benötigt bzw. nur für bestimmte Controller oder Aktionen ausgeführt werden soll.

Erstellen Sie einen Typ mit einer `Configure`-Methode, die die Middleware festlegt, die Sie in die Filterpipeline einfügen möchten, um Middleware als Filter zu verwenden. Hier ist ein Beispiel, dass die Lokalisierungsmiddleware verwendet, um die aktuelle Kultur einer Anforderung einzurichten:

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,21)]

Anschließend können Sie ein `MiddlewareFilterAttribute` verwenden, um die Middleware für einen ausgewählten Controller, eine Aktion oder global auszuführen:

[!code-csharp[](./filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

Middlewarefilter werden zum selben Zeitpunkt in der Filterpipeline wie Ressourcenfilter, vor der Modellbindung und nach dem Rest der Pipeline ausgeführt.

## <a name="next-actions"></a>Nächste Schritte

[Laden Sie das Beispiel herunter, testen und bearbeiten Sie es](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/filters/sample), um Filter näher kennenzulernen.

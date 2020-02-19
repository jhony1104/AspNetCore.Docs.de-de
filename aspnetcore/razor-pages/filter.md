---
title: Filtermethoden für Razor-Seiten in ASP-NET Core
author: Rick-Anderson
description: Erfahren Sie, wie Sie Filtermethoden für Razor-Seiten in ASP.NET Core erstellen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
uid: razor-pages/filter
ms.openlocfilehash: a60b17685c6f836de7c0afcc5b89a9894fb8b28f
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447230"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a>Filtermethoden für Razor-Seiten in ASP-NET Core

::: moniker range=">= aspnetcore-3.0"

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

Eine Razor-Seite filtert [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0). [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) erlaubt Razor-Seiten Code auszuführen, bevor und nachdem ein Handler für Razor-Seiten ausgeführt wurde. Filter für Razor-Seiten ähneln [ASP.NET Core MVC-Aktionsfiltern](xref:mvc/controllers/filters#action-filters), sie können allerdings nicht auf einzelne Seitenhandlermethoden angewendet werden.

Filter für Razor-Seiten:

* Führen Code aus, nachdem eine Handlermethode ausgewählt wurde, aber bevor die Modellbindung erfolgt
* Führen Code aus, bevor die Handlermethode ausgeführt wird, nachdem die Modellbindung abgeschlossen ist
* Führen Code aus, nachdem die Handlermethode ausgeführt wird
* Können auf einer Seite oder global implementiert werden
* Können nicht auf seitenspezifische Handlermethoden angewendet werden
* Kann über von der [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (di) aufgefüllte konstruktorabhängigkeiten verfügen. Weitere Informationen finden Sie unter [servicefilterattribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) und [typefilterattribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).

Wenngleich seitenkonstruktoren und Middleware das Ausführen von benutzerdefiniertem Code vor der Ausführung einer Handlermethode ermöglichen, ermöglichen nur Razor Page Filter den Zugriff auf <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> und die Seite. Die Middleware hat Zugriff auf die `HttpContext`, jedoch nicht auf den "Seiten Kontext". Filter verfügen über einen <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> abgeleiteten Parameter, der Zugriff auf `HttpContext`ermöglicht. Das Beispiel [Implementieren eines Filterattributs](#ifa) fügt der Antwort z.B. einen Header hinzu. Dies kann nicht über Konstruktoren oder Middleware erfolgen.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Filter für Razor-Seiten bieten die folgenden Methoden, die global oder auf Seitenebene angewendet werden können:

* Synchrone Methoden:

  * [OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0): Wird aufgerufen, nachdem eine Handlermethode ausgewählt wurde, aber bevor die Modellbindung erfolgt
  * [OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0): Wird aufgerufen, bevor die Handlermethode ausgeführt wird, nachdem die Modellbindung abgeschlossen ist
  * [OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0): Wird aufgerufen, nachdem die Handlermethode ausgeführt wird, bevor das Aktionsergebnis angezeigt wird

* Asynchrone Methoden:

  * [OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0): Wird asynchron aufgerufen, nachdem die Handlermethode ausgewählt wurde, aber bevor die Modellbindung erfolgt
  * [OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0): Wird asynchron aufgerufen, bevor die Handlermethode aufgerufen wird, nachdem die Modellbindung abgeschlossen ist

Implementieren Sie **entweder** die synchrone oder asynchrone Version einer Filterschnittstelle, aber **nicht** beide. Das Framework prüft zuerst, ob der Filter die asynchrone Schnittstelle implementiert, und wenn dies der Fall ist, ruft es sie auf. Wenn dies nicht der Fall ist, ruft es die Methode(n) der synchronen Schnittstelle auf. Wenn beide Schnittstellen implementiert werden, werden nur die Async-Methoden aufgerufen. Die gleiche Regel gilt für Überschreibungen in Seiten. Implementieren Sie die synchrone oder asynchrone Version der Überschreibung, nicht beide.

## <a name="implement-razor-page-filters-globally"></a>Globales implementieren von Filtern für Razor-Seiten

Mit dem folgenden Code wird das `IAsyncPageFilter`-Element implementiert:

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

Im vorangehenden Code ist `ProcessUserAgent.Write` vom Benutzer bereitgestellter Code, der mit der Zeichenfolge des Benutzer-Agents arbeitet.

Der folgende Code aktiviert `SampleAsyncPageFilter` in der `Startup`-Klasse:

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

Im folgenden Code wird <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> aufgerufen, um die `SampleAsyncPageFilter` auf Seiten in */Movies*anzuwenden:

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

Im folgenden Code wird `IPageFilter` synchron implementiert:

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

Der folgende Code aktiviert `SamplePageFilter`:

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a>Implementieren von Filtern für Razor-Seiten durch Überschreiben von Filtermethoden

Der folgende Code überschreibt die asynchronen Razor Page-Filter:

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a>Implementieren eines Filterattributs

Der integrierte Attribut basierte Filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> Filter kann unter klassifiziert werden. Der folgende Filter fügt der Antwort einen Header hinzu:

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

Der folgende Code wendet das Attribut `AddHeader` an:

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

Verwenden Sie ein Tool wie die Browser Entwicklertools, um die Header zu untersuchen. Unter **Antwortheader** wird `author: Rick` angezeigt.

Informationen zum Überschreiben der Reihenfolge finden Sie unter [Überschreiben der Standardreihenfolge](xref:mvc/controllers/filters#overriding-the-default-order).

Informationen zum Kurzschließen der Filterpipeline eines Filters finden Sie unter [Abbrechen und Kurzschließen](xref:mvc/controllers/filters#cancellation-and-short-circuiting).

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a>Autorisieren eines Filterattributs

Das Attribut [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) kann auf ein `PageModel` angewendet werden:

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

Eine Razor-Seite filtert [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0). [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) erlaubt Razor-Seiten Code auszuführen, bevor und nachdem ein Handler für Razor-Seiten ausgeführt wurde. Filter für Razor-Seiten ähneln [ASP.NET Core MVC-Aktionsfiltern](xref:mvc/controllers/filters#action-filters), sie können allerdings nicht auf einzelne Seitenhandlermethoden angewendet werden.

Filter für Razor-Seiten:

* Führen Code aus, nachdem eine Handlermethode ausgewählt wurde, aber bevor die Modellbindung erfolgt
* Führen Code aus, bevor die Handlermethode ausgeführt wird, nachdem die Modellbindung abgeschlossen ist
* Führen Code aus, nachdem die Handlermethode ausgeführt wird
* Können auf einer Seite oder global implementiert werden
* Können nicht auf seitenspezifische Handlermethoden angewendet werden

Code kann ausgeführt werden, bevor eine Handlermethode ausgeführt wird, indem man den Seitenkonstruktor oder Middleware benutzt, aber nur Filter für Razor-Seiten haben Zugriff auf [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext). Filter verfügen über einen Parameter, der von [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) abgeleitet ist, um Zugriff auf `HttpContext` zu ermöglichen. Das Beispiel [Implementieren eines Filterattributs](#ifa) fügt der Antwort z.B. einen Header hinzu. Dies kann nicht über Konstruktoren oder Middleware erfolgen.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Filter für Razor-Seiten bieten die folgenden Methoden, die global oder auf Seitenebene angewendet werden können:

* Synchrone Methoden:

  * [OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0): Wird aufgerufen, nachdem eine Handlermethode ausgewählt wurde, aber bevor die Modellbindung erfolgt
  * [OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0): Wird aufgerufen, bevor die Handlermethode ausgeführt wird, nachdem die Modellbindung abgeschlossen ist
  * [OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0): Wird aufgerufen, nachdem die Handlermethode ausgeführt wird, bevor das Aktionsergebnis angezeigt wird

* Asynchrone Methoden:

  * [OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0): Wird asynchron aufgerufen, nachdem die Handlermethode ausgewählt wurde, aber bevor die Modellbindung erfolgt
  * [OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0): Wird asynchron aufgerufen, bevor die Handlermethode aufgerufen wird, nachdem die Modellbindung abgeschlossen ist

> [!NOTE]
> Implementieren Sie **entweder** die synchrone oder asynchrone Version einer Filterschnittstelle, aber nicht beide. Das Framework prüft zuerst, ob der Filter die asynchrone Schnittstelle implementiert, und wenn dies der Fall ist, ruft es sie auf. Wenn dies nicht der Fall ist, ruft es die Methode(n) der synchronen Schnittstelle auf. Wenn beide Schnittstellen implementiert werden, werden nur die Async-Methoden aufgerufen. Die gleiche Regel gilt für Überschreibungen in Seiten. Implementieren Sie die synchrone oder asynchrone Version der Überschreibung, nicht beide.

## <a name="implement-razor-page-filters-globally"></a>Globales implementieren von Filtern für Razor-Seiten

Mit dem folgenden Code wird das `IAsyncPageFilter`-Element implementiert:

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

Im vorhergehenden Code wird [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) nicht benötigt. Es wird im Beispiel verwendet, um Überwachungsinformationen für die Anwendung bereitzustellen.

Der folgende Code aktiviert `SampleAsyncPageFilter` in der `Startup`-Klasse:

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

Der folgende Code veranschaulicht die vollständige `Startup`-Klasse:

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

Der folgende Code ruft `AddFolderApplicationModelConvention` auf, damit `SampleAsyncPageFilter` nur auf Seiten in */subFolder* angewendet wird:

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

Im folgenden Code wird `IPageFilter` synchron implementiert:

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

Der folgende Code aktiviert `SamplePageFilter`:

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a>Implementieren von Filtern für Razor-Seiten durch Überschreiben von Filtermethoden

Der folgende Code überschreibt die synchronen Filter für Razor-Seiten:

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a>Implementieren eines Filterattributs

Der integrierte attributbasierte Filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) kann als Unterklasse definiert werden. Der folgende Filter fügt der Antwort einen Header hinzu:

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

Der folgende Code wendet das Attribut `AddHeader` an:

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

Informationen zum Überschreiben der Reihenfolge finden Sie unter [Überschreiben der Standardreihenfolge](xref:mvc/controllers/filters#overriding-the-default-order).

Informationen zum Kurzschließen der Filterpipeline eines Filters finden Sie unter [Abbrechen und Kurzschließen](xref:mvc/controllers/filters#cancellation-and-short-circuiting). 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a>Autorisieren eines Filterattributs

Das Attribut [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) kann auf ein `PageModel` angewendet werden:

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

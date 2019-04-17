---
title: Best Practices für ASP.NET Core-Leistung
author: mjrousos
description: Tipps zum Verbessern der Leistung in ASP.NET Core-apps und allgemeiner Leistungsprobleme zu vermeiden.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/13/2019
uid: performance/performance-best-practices
ms.openlocfilehash: 095db38cf3102f6e18930efdbbaeeb90dffad8af
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614447"
---
# <a name="aspnet-core-performance-best-practices"></a>Best Practices für ASP.NET Core-Leistung

Durch [Mike Rousos](https://github.com/mjrousos)

Dieses Thema enthält bewährte Methoden für ASP.NET Core Richtlinien für die Leistung.

<a name="hot"></a>

In diesem Dokument eine *"Hot" Codepfad* ist definiert als ein Codepfad, die häufig aufgerufen wird und, in denen ein Großteil der Ausführungszeit auftritt. "Hot" Codepfade beschränken in der Regel app horizontale Skalierung und Leistung.

## <a name="cache-aggressively"></a>Zwischenspeichern aggressiv

Zwischenspeichern ist in mehrere Teile dieses Dokuments erläutert. Weitere Informationen finden Sie unter <xref:performance/caching/response>.

## <a name="avoid-blocking-calls"></a>Vermeidung von blockierenden aufrufen

ASP.NET Core-apps sollten so entworfen werden, zu viele Anforderungen gleichzeitig verarbeiten. Asynchrone APIs ermöglichen einen kleineren Threadpool auf Tausende von gleichzeitigen Anforderungen zu verarbeiten, indem Sie nicht warten, auf die Aufrufe zum Blockieren. Statt zu warten auf eine lang andauernde synchrone Aufgabe ausführen, kann der Thread auf eine andere Anforderung arbeiten.

Ein häufiges Leistungsproblem in ASP.NET Core-apps ist die blockierende Aufrufe, die asynchron sein kann. Viele synchronen, blockierenden Aufrufe dazu führen, dass [Pool Thread Starvation](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) und Antwortzeiten beeinträchtigt.

**Nicht**:

* Blockiert die asynchrone Ausführung durch den Aufruf ["Task.Wait"](/dotnet/api/system.threading.tasks.task.wait) oder ["Task.Result"](/dotnet/api/system.threading.tasks.task-1.result).
* Sperren Sie in allgemeine Codepfaden. ASP.NET Core-apps sind die meisten leistungsfähig ist, wenn der Code die parallele Ausführung entworfen.

**Führen Sie**:

* Stellen ["Hot" Codepfade](#hot) asynchron.
* Zugriff auf Daten und Vorgängen mit langen Laufzeiten APIs asynchron aufrufen.
* Stellen Sie Controller/Razor-Seitenaktionen asynchrone. Die gesamte Aufrufliste ist asynchron, um von profitieren [Async/await](/dotnet/csharp/programming-guide/concepts/async/) Muster.

Profiler z. B. [PerfView](https://github.com/Microsoft/perfview), können verwendet werden, um Threads häufig hinzugefügt, suchen die [Thread Pool](/windows/desktop/procthread/thread-pool). Die `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` Ereignis gibt an, einen Thread, der an den Threadpool hinzugefügt. <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc  -->

## <a name="minimize-large-object-allocations"></a>Minimieren Sie Zuordnungen großer Objekte

<!-- TODO review Bill - replaced original .NET language below with .NET Core since this targets .NET Core -->
Die [.NET Core-Garbagecollector](/dotnet/standard/garbage-collection/) verwaltet die Belegung und Freigabe von Speicher in ASP.NET Core-apps automatisch. Automatische Garbagecollection bedeutet im Allgemeinen, dass Entwickler nicht kümmern, wie oder wann Speicher freigegeben wird. Jedoch Bereinigen von Objekten, auf die CPU-Zeit hat, damit Entwickler beim Zuordnen von Objekten in minimieren sollten ["Hot" Codepfade](#hot). Garbagecollection ist besonders teuer für große Objekte (> 85 KB). Große Objekte werden gespeichert, auf die [Heap für große Objekte](/dotnet/standard/garbage-collection/large-object-heap) und erfordern eine vollständige (Generation 2) Garbagecollection bereinigt werden. Im Gegensatz zu Generation 0 und Garbage Collections der Generation 1 ist eine Collection der Generation 2 führt zu eine temporären Unterbrechung der Ausführung der app erforderlich. Häufige Zuordnung und Aufhebung der Zuordnung von großen Objekten können dazu führen, dass inkonsistenten Leistung.

Empfehlungen:

* **Führen Sie** zwischenspeichern, große Objekte, die häufig verwendet werden. Zwischenspeichern von großen Objekten wird verhindert, dass die teure Zuordnungen.
* **Führen Sie** pool Puffer mit einer [ `ArrayPool<T>` ](/dotnet/api/system.buffers.arraypool-1) zum Speichern von großen Arrays.
* **Nicht** viele kurzlebige große Objekte zuordnen, auf ["Hot" Codepfade](#hot).

Arbeitsspeicherprobleme, z. B. die obige untersucht werden können, anhand der Garbage Collection (GC) Statistiken in [PerfView](https://github.com/Microsoft/perfview) und untersuchen:

* Garbage Collection der Verzögerung.
* Welcher Anteil der Prozessorzeit Garbagecollection aufgewendet wurde.
* Wie viele Garbage Collections der Generation 0, 1 und 2 sind.

Weitere Informationen finden Sie unter [Garbage Collection und Leistung](/dotnet/standard/garbage-collection/performance).

## <a name="optimize-data-access"></a>Optimieren des Datenzugriffs

Interaktionen mit einem Datenspeicher und andere Remotedienste sind häufig der langsamsten Schritte bei der ASP.NET Core-Apps. Lesen und Schreiben von Daten effizient unbedingt für eine gute Leistung.

Empfehlungen:

* **Führen Sie** alle Datenzugriffs-APIs asynchron aufrufen.
* **Nicht** abrufen mehr Daten als erforderlich erledigt wird. Schreiben Sie Abfragen, um nur die Daten zurückzugeben, die für die aktuelle HTTP-Anforderung erforderlich ist.
* **Führen Sie** sollten Sie Zwischenspeichern häufig verwendeter Daten aus einer Datenbank oder einer remote-Dienst abgerufen werden, wenn etwas veraltete Daten akzeptabel ist, zugegriffen wird. Verwenden Sie je nach Szenario eine [MemoryCache](xref:performance/caching/memory) oder [DistributedCache](xref:performance/caching/distributed). Weitere Informationen finden Sie unter <xref:performance/caching/response>.
* **Führen Sie** minimieren Netzwerkroundtrips. Das Ziel ist es zum Abrufen der erforderlichen Daten in einem einzigen Aufruf statt mehrere Aufrufe.
* **Führen Sie** verwenden [Abfragen ohne nachverfolgung](/ef/core/querying/tracking#no-tracking-queries) in Entity Framework Core beim Zugriff auf Daten für nur-Lese Zwecke. EF Core kann die Ergebnisse der Abfragen ohne nachverfolgung effizienter zurückgeben.
* **Führen Sie** filtern und aggregieren LINQ-Abfragen (mit `.Where`, `.Select`, oder `.Sum` -Anweisungen, z. B.), damit die Filterung von der Datenbank ausgeführt wird.
* **Führen Sie** Beachten Sie, dass EF Core einige Abfrageoperatoren, die auf dem Client aufgelöst wird, was zur Ausführung von ineffizienten Abfragen führen kann. Weitere Informationen finden Sie unter [Client Auswertung Leistungsprobleme](/ef/core/querying/client-eval#client-evaluation-performance-issues).
* **Nicht** Projektionsabfragen auf Sammlungen, die dazu führen können, Ausführen von "N + 1" verwendet SQL-Abfragen. Weitere Informationen finden Sie unter [Optimierung von korrelierten Unterabfragen](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).

Finden Sie unter [EF-High-Performance](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) Ansätze, die die Leistung in apps in großem Maßstab verbessern kann:

* [DbContext-pooling](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [Explizit kompilierte Abfragen](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

Es wird empfohlen, Messen der Auswirkungen der vorherigen Hochleistungs-Ansätze vor dem Ausführen eines Commits für die Codebasis. Die zusätzliche Komplexität der kompilierte Abfragen kann nicht die leistungsverbesserung rechtfertigen.

Abfrage, die Probleme können erkannt werden, überprüfen Sie die Zeit für den Zugriff auf Daten mit aufgewendet [Application Insights](/azure/application-insights/app-insights-overview) oder mit Profilerstellungstools. Die meisten Datenbanken auch Statistiken zur Verfügung stellen zu häufig ausgeführte Abfragen.

## <a name="pool-http-connections-with-httpclientfactory"></a>Pool-HTTP-Verbindungen mit HttpClientFactory

Obwohl ["HttpClient"](/dotnet/api/system.net.http.httpclient) implementiert die `IDisposable` -Schnittstelle, es dient zur Wiederverwendung. Geschlossen `HttpClient` Instanzen lassen Sockets geöffnet, in der `TIME_WAIT` Status für einen kurzen Zeitraum. Wenn ein Codepfad, der erstellt, und verwirft `HttpClient` Objekte wird häufig verwendet, die app verfügbaren Sockets ausgeschöpft werden. [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) wurde in ASP.NET Core 2.1 als Lösung für dieses Problem eingeführt. Er verarbeitet die Lightweightpooling-HTTP-Verbindungen zum Optimieren der Leistung und Zuverlässigkeit.

Empfehlungen:

* **Nicht** erstellen und Löschen von `HttpClient` direkt Instanzen.
* **Führen Sie** verwenden [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) abzurufenden `HttpClient` Instanzen. Weitere Informationen finden Sie unter [HttpClientFactory verwenden, um robuste HTTP-Anforderungen zu implementieren](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).

## <a name="keep-common-code-paths-fast"></a>Häufige schnelle Codepfade beibehalten

Sie möchten, dass alle Ihren Code in schnell, häufig Codepfade aufgerufen werden zur Optimierung am wichtigsten sind:

* Middleware-Komponenten in der app Pipeline zur anforderungsverarbeitung, vor allem Middleware früh in der Pipeline ausgeführt werden. Diese Komponenten haben einen großen Einfluss auf die Leistung.
* Code, der für jede Anforderung oder mehrere Male pro Anforderung ausgeführt wird. Z. B. benutzerdefinierte Protokollierung, Autorisierung Handler oder Initialisierung des kurzlebige Dienste.

Empfehlungen:

* **Nicht** Verwenden benutzerdefinierter Middleware-Komponenten mit lang andauernden Aufgaben.
* **Führen Sie** verwenden Leistungsprofilerstellungstools bereit, z. B. [Visual Studio-Diagnosetools](/visualstudio/profiling/profiling-feature-tour) oder [PerfView](https://github.com/Microsoft/perfview)) zur Identifizierung ["Hot" Codepfade](#hot).

## <a name="complete-long-running-tasks-outside-of-http-requests"></a>Führen Sie die lang andauernde Vorgänge außerhalb von HTTP-Anforderungen

Die meisten Anforderungen an eine ASP.NET Core-app können von einem Controller oder Seitenmodell erforderlichen Dienste aufrufen und Zurückgeben einer HTTP-Antwort verarbeitet werden. Bei einige Anforderungen, bei denen zeitaufwendige Aufgaben ausgeführt werden, ist es besser, die die gesamte Anforderung / Antwort-Vorgang asynchron auszuführen.

Empfehlungen:

* **Nicht** warten, langer Aufgaben als Teil der normalen Verarbeitung von HTTP-Anforderung abgeschlossen.
* **Führen Sie** sollten in Betracht ziehen lang andauernder Anforderungen mit [Hintergrunddienst](xref:fundamentals/host/hosted-services) oder außerhalb des Prozesses mit einem [Azure-Funktion](/azure/azure-functions/). Das Abschließen der Arbeit Out-of-Process ist besonders hilfreich für die CPU-Intensive Aufgaben.
* **Führen Sie** verwenden Sie die Optionen für die Kommunikation in Echtzeit, z. B. [SignalR](xref:signalr/introduction), um die asynchrone Kommunikation mit Clients.

## <a name="minify-client-assets"></a>Minimieren Sie die Client-Ressourcen

ASP.NET Core-apps mit komplexen Front-Ends dienen häufig viele JavaScript, CSS oder Bilddateien. Leistung des anfänglich geladenen-Anforderungen verbessert werden kann:

* In einer kombiniert die Bündelung, mehrere Dateien.
* Durch das Entfernen von Leerzeichen und Kommentare reduziert die minimieren, die Größe der Dateien.

Empfehlungen:

* **Führen Sie** Verwenden von ASP.NET Core [integrierte Unterstützung](xref:client-side/bundling-and-minification) zu bündeln und Minimieren der Clientobjekte.
* **Führen Sie** sollten Sie andere Tools von Drittanbietern, z. B. [Gulp](xref:client-side/using-gulp) oder [Webpack](https://webpack.js.org/) für komplexe Client Asset Management.

## <a name="compress-responses"></a>Komprimieren von Antworten

 Verringern der Größe der Antwort in der Regel erhöht die Reaktionsfähigkeit einer App, häufig erheblich. Eine Möglichkeit zum Reduzieren der Größe der Nutzlast ist zum Komprimieren von Antworten von der app. Weitere Informationen finden Sie unter [antwortkomprimierung](xref:performance/response-compression).

## <a name="use-the-latest-aspnet-core-release"></a>Verwenden Sie die neueste Version von ASP.NET Core

Jeder neuen Version von ASP.NET Core enthält leistungsverbesserungen. Optimierungen in .NET Core und ASP.NET Core bedeutet, dass neuere Versionen in der Regel von einer älteren übertreffen. Beispielsweise .NET Core 2.1 Unterstützung für reguläre Ausdrücke kompilierte und fanpost [ `Span<T>` ](https://msdn.microsoft.com/magazine/mt814808.aspx). Verwenden Sie die Unterstützung von ASP.NET Core 2.2 hinzugefügt, für die HTTP/2. Wenn die Leistung einer Priorität ist, sollten Sie ein Upgrade auf die aktuelle Version von ASP.NET Core.

<!-- TODO review link and taking advantage of new [performance features](#TBD)
Maybe skip this TBD link as each version will have perf improvements -->

## <a name="minimize-exceptions"></a>Minimieren von Ausnahmen

Ausnahmen sollten nur selten sein. Auslösen und Abfangen von Ausnahmen ist relativ zu anderen Flow Codemuster langsam. Aus diesem Grund sollte nicht Ausnahmen verwendet werden, zum normalen Programmablauf steuern.

Empfehlungen:

* **Nicht** verwenden, ist das Auslösen und Abfangen von Ausnahmen als Mittel zum normalen Programmablauf, insbesondere in ["Hot" Codepfade](#hot).
* **Führen Sie** enthalten Logik in der app erkennen und Behandeln von Bedingungen, die eine Ausnahme verursachen würde.
* **Führen Sie** ausgelöst oder abgefangen Ausnahmen für ungewöhnliche oder unerwartete Bedingungen.

App-Diagnose-Tools wie z. B. Application Insights können Sie um allgemeine Ausnahmen in einer app zu erkennen, die Leistung beeinflussen können.

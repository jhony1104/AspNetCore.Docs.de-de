---
title: ASP.NET Best Practices für die Kernleistung
author: mjrousos
description: Tipps zur Leistungssteigerung in ASP.NET Core-Apps und zur Vermeidung häufig auftretender Leistungsprobleme.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/06/2020
no-loc:
- SignalR
uid: performance/performance-best-practices
ms.openlocfilehash: 068a35fbe410dad24030fe68c0dfd062b402212c
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977183"
---
# <a name="aspnet-core-performance-best-practices"></a>ASP.NET Best Practices für die Kernleistung

Von [Mike Rousos](https://github.com/mjrousos)

Dieser Artikel enthält Richtlinien für Best Practices für die Leistung mit ASP.NET Core.

## <a name="cache-aggressively"></a>Cache aggressiv

Das Zwischenspeichern wird in mehreren Teilen dieses Dokuments erläutert. Weitere Informationen finden Sie unter <xref:performance/caching/response>.

## <a name="understand-hot-code-paths"></a>Verstehen von Hotcodepfaden

In diesem Dokument wird ein *Hotcodepfad* als Codepfad definiert, der häufig aufgerufen wird und in dem ein Großteil der Ausführungszeit auftritt. Hotcodepfade beschränken in der Regel das Horizontalerakalieren und die Leistung von Apps und werden in mehreren Teilen dieses Dokuments erläutert.

## <a name="avoid-blocking-calls"></a>Vermeiden Sie das Blockieren von Anrufen

ASP.NET Core-Apps sollten so konzipiert sein, dass viele Anforderungen gleichzeitig verarbeitet werden. Asynchrone APIs ermöglichen es einem kleinen Pool von Threads, Tausende gleichzeitiger Anforderungen zu verarbeiten, indem sie nicht auf das Blockieren von Aufrufen warten. Anstatt auf eine lang andauernde synchrone Aufgabe zu warten, kann der Thread bei einer anderen Anforderung arbeiten.

Ein häufiges Leistungsproblem in ASP.NET Core-Apps ist das Blockieren von Aufrufen, die asynchron sein könnten. Viele synchrone Blockierungsaufrufe führen zum [Aushungern](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) von Thread Pools und zu herabgestuften Reaktionszeiten.

**Nicht**:

* Blockieren Sie die asynchrone Ausführung durch Aufrufen von [Task.Wait](/dotnet/api/system.threading.tasks.task.wait) oder [Task.Result](/dotnet/api/system.threading.tasks.task-1.result).
* Erfassen Sie Sperren in gemeinsamen Codepfaden. ASP.NET Core-Apps sind am leistungsfähigsten, wenn sie so konzipiert sind, dass Code parallel ausgeführt wird.
* Rufen Sie [Task.Run](/dotnet/api/system.threading.tasks.task.run) auf und warten Sie sofort darauf. ASP.NET Core führt bereits App-Code auf normalen Threadpool-Threads aus, sodass das Aufrufen von Task.Run nur zu einer zusätzlichen unnötigen Threadpoolplanung führt. Selbst wenn der geplante Code einen Thread blockieren würde, verhindert Task.Run dies nicht.

**Tun :**

* Machen Sie [Hotcodepfade](#understand-hot-code-paths) asynchron.
* Aufrufdatenzugriffs-, E/A- und Langlauf-Zugriffs-APIs asynchron, wenn eine asynchrone API verfügbar ist. Verwenden Sie [Task.Run](/dotnet/api/system.threading.tasks.task.run) **nicht,** um eine Synchron-API asynchron zu machen.
* Machen Sie Controller/Razor Page-Aktionen asynchron. Die gesamte Aufrufliste ist asynchron, um von [async/await-Mustern](/dotnet/csharp/programming-guide/concepts/async/) zu profitieren.

Ein Profiler, z. B. [PerfView](https://github.com/Microsoft/perfview), kann verwendet werden, um Threads zu suchen, die häufig dem [Threadpool](/windows/desktop/procthread/thread-pools)hinzugefügt werden. Das `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` Ereignis gibt einen Thread an, der dem Threadpool hinzugefügt wurde. <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="minimize-large-object-allocations"></a>Minimierung großer Objektzuordnungen

[Der .NET Core Garbage Collector](/dotnet/standard/garbage-collection/) verwaltet die Zuweisung und Freigabe von Arbeitsspeicher automatisch in ASP.NET Core-Apps. Die automatische Garbage Collection bedeutet im Allgemeinen, dass Entwickler sich keine Gedanken darüber machen müssen, wie oder wann Arbeitsspeicher freigegeben wird. Das Bereinigen nicht referenzierter Objekte nimmt jedoch CPU-Zeit in Anspruch, daher sollten Entwickler die Zuweisung von Objekten in [Hotcodepfaden](#understand-hot-code-paths)minimieren. Die Garbage Collection ist bei großen Objekten (> 85 K Bytes) besonders teuer. Große Objekte werden auf dem [Heap für große Objekte](/dotnet/standard/garbage-collection/large-object-heap) gespeichert und erfordern eine vollständige (Generation 2) Garbage Collection, um sie zu bereinigen. Im Gegensatz zu Sammlungen der Generation 0 und Generation 1 erfordert eine Generation 2-Auflistung eine temporäre Aussetzung der App-Ausführung. Häufige Zuweisungen und Dezuordnungen großer Objekte können zu inkonsistenter Leistung führen.

Empfehlungen:

* **Erwägen Sie** das Zwischenspeichern großer Objekte, die häufig verwendet werden. Das Zwischenspeichern großer Objekte verhindert teure Zuordnungen.
* **Machen Sie** Poolpuffer, indem Sie eine [ArrayPool\<T->](/dotnet/api/system.buffers.arraypool-1) zum Speichern großer Arrays verwenden.
* **Weisen Sie nicht** viele, kurzlebige große Objekte auf [Hotcodepfaden](#understand-hot-code-paths)zu.

Speicherprobleme, wie z. B. das vorherige, können durch Überprüfen von Garbage Collection(GC)-Statistiken in [PerfView](https://github.com/Microsoft/perfview) und Untersuchen von:

* Pausezeit für die Garbage Collection.
* Welcher Prozentsatz der Prozessorzeit wird für die Garbage Collection aufgewendet.
* Wie viele Garbage Collections sind Die Generation 0, 1 und 2.

Weitere Informationen finden Sie unter [Garbage Collection and Performance](/dotnet/standard/garbage-collection/performance).

## <a name="optimize-data-access-and-io"></a>Optimieren von Datenzugriff und E/A

Interaktionen mit einem Datenspeicher und anderen Remotediensten sind häufig die langsamsten Teile einer ASP.NET Core-App. Effizientes Lesen und Schreiben von Daten ist entscheidend für eine gute Leistung.

Empfehlungen:

* **Rufen Sie** alle Datenzugriffs-APIs asynchron auf.
* **Rufen Sie nicht** mehr Daten ab, als erforderlich ist. Schreiben Sie Abfragen, um nur die Daten zurückzugeben, die für die aktuelle HTTP-Anforderung erforderlich sind.
* **Erwägen Sie** das Zwischenspeichern von Daten, auf die häufig zugegriffen wird, die aus einer Datenbank oder einem Remotedienst abgerufen werden, wenn leicht veraltete Daten akzeptabel sind. Verwenden Sie je nach Szenario einen [MemoryCache](xref:performance/caching/memory) oder einen [DistributedCache](xref:performance/caching/distributed). Weitere Informationen finden Sie unter <xref:performance/caching/response>.
* **Minimieren Sie** Netzwerk-Rundfahrten. Das Ziel besteht darin, die erforderlichen Daten in einem einzelnen Aufruf und nicht in mehreren Aufrufen abzurufen.
* **Verwenden Sie** [keine Nachverfolgungsabfragen](/ef/core/querying/tracking#no-tracking-queries) in Entity Framework Core, wenn Sie auf Daten für schreibgeschützte Zwecke zugreifen. EF Core kann die Ergebnisse von Abfragen ohne Nachverfolgung effizienter zurückgeben.
* **Filtern** und aggregieren Sie `.Where`LINQ-Abfragen (z. B. mit , `.Select`oder `.Sum` Anweisungen), damit die Filterung von der Datenbank ausgeführt wird.
* **Beachten Sie,** dass EF Core einige Abfrageoperatoren auf dem Client auflöst, was zu einer ineffizienten Abfrageausführung führen kann. Weitere Informationen finden Sie unter [Leistungsprobleme bei](/ef/core/querying/client-eval#client-evaluation-performance-issues)der Kundenbewertung .
* **Verwenden Sie keine** Projektionsabfragen für Auflistungen, was zur Ausführung von SQL-Abfragen "N + 1" führen kann. Weitere Informationen finden Sie [unter Optimierung korrelierter Unterabfragen](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).

Unter [EF High Performance](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) finden Sie Ansätze, die die Leistung in Apps mit hoher Skalierung verbessern können:

* [DbContext-Pooling](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [Explizit kompilierte Abfragen](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

Es wird empfohlen, die Auswirkungen der vorherigen Hochleistungsansätze zu messen, bevor die Codebasis übertragen wird. Die zusätzliche Komplexität kompilierter Abfragen rechtfertigt die Leistungsverbesserung möglicherweise nicht.

Abfrageprobleme können erkannt werden, indem Sie die Zeit überprüfen, die für den Zugriff auf Daten mit Application Insights oder mit [Profilerstellungstools](/azure/application-insights/app-insights-overview) aufgewendet wurde. Die meisten Datenbanken stellen auch Statistiken über häufig ausgeführte Abfragen zur Verfügung.

## <a name="pool-http-connections-with-httpclientfactory"></a>Http-Verbindungen mit HttpClientFactory bündeln

[Obwohl HttpClient](/dotnet/api/system.net.http.httpclient) die `IDisposable` Schnittstelle implementiert, ist sie für die Wiederverwendung konzipiert. Geschlossene `HttpClient` Instanzen lassen Sockets für einen kurzen Zeitraum im `TIME_WAIT` Zustand geöffnet. Wenn ein Codepfad, der `HttpClient` Objekte erstellt und entsorgt, häufig verwendet wird, kann die App verfügbare Sockets ausschöpfen. [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) wurde in ASP.NET Core 2.1 als Lösung für dieses Problem eingeführt. Es verarbeitet das Pooling von HTTP-Verbindungen, um die Leistung und Zuverlässigkeit zu optimieren.

Empfehlungen:

* **Erstellen** und entsorgen `HttpClient` Sie Instanzen nicht direkt.
* **Verwenden Sie** [HttpClientFactory,](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) um Instanzen abzurufen. `HttpClient` Weitere Informationen finden Sie unter [Verwenden von HttpClientFactory zum Implementieren robuster HTTP-Anforderungen](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).

## <a name="keep-common-code-paths-fast"></a>Halten Sie gemeinsame Codepfade schnell

Sie möchten, dass der gesamte Code schnell ist. Häufig aufgerufene Codepfade sind die wichtigsten zu optimieren. Dazu gehören:

* Middleware-Komponenten in der Anforderungsverarbeitungspipeline der App, insbesondere Middleware, werden früh in der Pipeline ausgeführt. Diese Komponenten haben einen großen Einfluss auf die Leistung.
* Code, der für jede Anforderung oder mehrmals pro Anforderung ausgeführt wird. Beispielsweise benutzerdefinierte Protokollierung, Autorisierungshandler oder Initialisierung vorübergehender Dienste.

Empfehlungen:

* Verwenden **Sie keine** benutzerdefinierten Middlewarekomponenten mit lang andauernden Aufgaben.
* Verwenden **Sie** Leistungsprofilerstellungstools, z. B. [Visual Studio Diagnostic Tools](/visualstudio/profiling/profiling-feature-tour) oder [PerfView](https://github.com/Microsoft/perfview)), um [Hotcodepfade](#understand-hot-code-paths)zu identifizieren.

## <a name="complete-long-running-tasks-outside-of-http-requests"></a>Abschließen lang andauernder Aufgaben außerhalb von HTTP-Anforderungen

Die meisten Anforderungen an eine ASP.NET Core-App können von einem Controller oder Seitenmodell verarbeitet werden, das die erforderlichen Dienste aufruft und eine HTTP-Antwort zurückgibt. Bei einigen Anforderungen, die lang andauernde Aufgaben umfassen, ist es besser, den gesamten Anforderungsantwortprozess asynchron zu machen.

Empfehlungen:

* **Warten Sie nicht,** bis lang andauernde Aufgaben im Rahmen der normalen HTTP-Anforderungsverarbeitung abgeschlossen sind.
* **Erwägen Sie die** Verarbeitung lang andauernder Anforderungen mit [Hintergrunddiensten](xref:fundamentals/host/hosted-services) oder a-process mit einer [Azure-Funktion](/azure/azure-functions/). Die Abwicklung von Out-of-Process ist besonders für CPU-intensive Aufgaben von Vorteil.
* Verwenden Sie Echtzeitkommunikationsoptionen, **z.** [SignalR](xref:signalr/introduction)B. , um asynchron mit Clients zu kommunizieren.

## <a name="minify-client-assets"></a>Minify-Client-Assets

ASP.NET Core-Apps mit komplexen Front-Ends dienen häufig vielen JavaScript-, CSS- oder Bilddateien. Die Leistung der anfänglichen Lastanforderungen kann verbessert werden durch:

* Bündelung, die mehrere Dateien in einer kombiniert.
* Minimieren, wodurch die Größe von Dateien reduziert wird, indem Leerzeichen und Kommentare entfernt werden.

Empfehlungen:

* Verwenden **Sie** ASP.NET [integrierte Unterstützung](xref:client-side/bundling-and-minification) von Core für die Bündelung und Minimierung von Kundenressourcen.
* **Berücksichtigen Sie** andere Tools von Drittanbietern, z. B. [Webpack](https://webpack.js.org/), für die komplexe Verwaltung von Client-Assets.

## <a name="compress-responses"></a>Komprimieren von Antworten

 Die Verkleinerung der Antwort erhöht in der Regel die Reaktionsfähigkeit einer App, oft dramatisch. Eine Möglichkeit, die Nutzlastzukleinerung zu reduzieren, besteht darin, die Antworten einer App zu komprimieren. Weitere Informationen finden Sie unter [Antwortkomprimierung](xref:performance/response-compression).

## <a name="use-the-latest-aspnet-core-release"></a>Verwenden der neuesten ASP.NET Core-Version

Jede neue Version von ASP.NET Core enthält Leistungsverbesserungen. Optimierungen in .NET Core und ASP.NET Core bedeuten, dass neuere Versionen im Allgemeinen ältere Versionen übertreffen. Beispielsweise hat .NET Core 2.1 Unterstützung für kompilierte reguläre Ausdrücke hinzugefügt und von [Span\<T>](https://msdn.microsoft.com/magazine/mt814808.aspx)profitiert. ASP.NET Core 2.2 hat Unterstützung für HTTP/2 hinzugefügt. [ASP.NET Core 3.0 fügt viele Verbesserungen hinzu,](xref:aspnetcore-3.0) die den Speicherverbrauch reduzieren und den Durchsatz verbessern. Wenn die Leistung priorität hat, sollten Sie ein Upgrade auf die aktuelle Version von ASP.NET Core in Betracht ziehen.

## <a name="minimize-exceptions"></a>Minimieren von Ausnahmen

Ausnahmen sollten selten sein. Das Auslösen und Abfangen von Ausnahmen ist im Vergleich zu anderen Codeflussmustern langsam. Aus diesem Grund sollten Ausnahmen nicht verwendet werden, um den normalen Programmfluss zu steuern.

Empfehlungen:

* **Verwenden Sie das** Auslösen oder Abfangen von Ausnahmen nicht als Mittel des normalen Programmflusses, insbesondere in [heißen Codepfaden](#understand-hot-code-paths).
* **Fügen Sie** Logik in die App ein, um Bedingungen zu erkennen und zu behandeln, die eine Ausnahme verursachen würden.
* **Werfen** oder fangen Sie Ausnahmen für ungewöhnliche oder unerwartete Bedingungen.

App-Diagnosetools, z. B. Application Insights, können dazu beitragen, häufige Ausnahmen in einer App zu identifizieren, die sich auf die Leistung auswirken können.

## <a name="performance-and-reliability"></a>Leistung und Zuverlässigkeit

Die folgenden Abschnitte enthalten Leistungstipps und bekannte Zuverlässigkeitsprobleme und -lösungen.

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a>Vermeiden Sie synchrones Lesen oder Schreiben im HttpRequest/HttpResponse-Text

Alle E/A-E-Werte in ASP.NET Core sind asynchron. Server implementieren `Stream` die Schnittstelle, die sowohl synchrone als auch asynchrone Überladungen enthält. Die asynchronen Threads sollten bevorzugt werden, um das Blockieren von Threadpoolthreads zu vermeiden. Blockierenvon Threads können zum Hungertod des Threadpools führen.

**Tun Sie dies nicht:** Im folgenden Beispiel <xref:System.IO.StreamReader.ReadToEnd*>wird die verwendet. Es blockiert den aktuellen Thread, um auf das Ergebnis zu warten. Dies ist ein Beispiel für [die Synchronisierung über async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
).

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

Im vorherigen Code `Get` liest synchron der gesamte HTTP-Anforderungstext in den Arbeitsspeicher. Wenn der Client langsam hochlädt, führt die App die Synchronisierung über async durch. Die App synchronisiert sich über async, da Kestrel **KEINE** synchronen Lesevorgänge unterstützt.

**Tun Sie dies:** Im folgenden <xref:System.IO.StreamReader.ReadToEndAsync*> Beispiel wird der Thread beim Lesen verwendet und nicht blockiert.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

Der vorhergehende Code liest asynchron den gesamten HTTP-Anforderungstext in den Arbeitsspeicher.

> [!WARNING]
> Wenn die Anforderung groß ist, kann das Lesen des gesamten HTTP-Anforderungstexts in den Arbeitsspeicher zu einer OOM-Bedingung (Un-Speicher) führen. OOM kann zu einem Denial Of Service führen.  Weitere Informationen finden Sie unter Vermeiden Sie das [Lesen großer Anforderungs- oder Antwortkörper in](#arlb) den Speicher in diesem Dokument.

**Tun Sie dies:** Das folgende Beispiel ist vollständig asynchron mit einem nicht gepufferten Anforderungstext:

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

Der vorhergehende Code deserialisiert den Anforderungstext asynchron in ein C-Objekt.

## <a name="prefer-readformasync-over-requestform"></a>Lieber ReadFormAsync gegenüber Request.Form

Verwenden Sie `HttpContext.Request.ReadFormAsync` anstelle von `HttpContext.Request.Form`.
`HttpContext.Request.Form`kann unter folgenden Bedingungen sicher gelesen werden:

* Das Formular wurde durch einen `ReadFormAsync`Aufruf zu gelesen, und
* Der zwischengespeicherte Formularwert wird mit`HttpContext.Request.Form`

**Tun Sie dies nicht:** Im folgenden `HttpContext.Request.Form`Beispiel wird verwendet.  `HttpContext.Request.Form`verwendet [Sync über async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
) und kann zum Verhungern des Threadpools führen.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

**Tun Sie dies:** Im folgenden `HttpContext.Request.ReadFormAsync` Beispiel wird verwendet, um den Formulartext asynchron zu lesen.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a>Vermeiden Sie das Lesen großer Anforderungskörper oder Antwortkörper in den Speicher

In .NET landet jede Objektzuordnung größer als 85 KB im großen Objektheap ([LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)). Große Objekte sind auf zwei Arten teuer:

* Die Zuweisungskosten sind hoch, da der Speicher für ein neu zugewiesenes großes Objekt gelöscht werden muss. Die CLR stellt sicher, dass der Speicher für alle neu zugewiesenen Objekte gelöscht wird.
* LOH wird mit dem Rest des Haufens gesammelt. LOH erfordert eine vollständige [Garbage Collection](/dotnet/standard/garbage-collection/fundamentals) oder [Gen2-Auflistung](/dotnet/standard/garbage-collection/fundamentals#generations).

Dieser [Blogbeitrag](https://adamsitnik.com/Array-Pool/#the-problem) beschreibt das Problem kurz und bündig:

> Wenn ein großes Objekt zugewiesen wird, wird es als Gen 2-Objekt markiert. Nicht Gen 0 wie bei kleinen Objekten. Die Folge ist, dass, wenn Ihnen in LOH der Speicher ausgeht, GC den gesamten verwalteten Heap bereinigt, nicht nur LOH. So säubert es Gen 0, Gen 1 und Gen 2 einschließlich LOH. Dies wird als vollständige Garbage Collection bezeichnet und ist die zeitaufwändigste Garbage Collection. Für viele Anwendungen kann es akzeptabel sein. Aber definitiv nicht für hochleistungsstarke Webserver, auf denen nur wenige große Speicherpuffer benötigt werden, um eine durchschnittliche Webanforderung zu verarbeiten (lesen Sie von einem Socket, dekomprimieren, decodieren & mehr).

Naiv eine große Anforderungs- oder `byte[]` Antwortstelle in einem einzelnen oder: `string`

* Kann dazu führen, dass im LOH schnell der Platz knapp wird.
* Kann Leistungsprobleme für die App verursachen, da vollständige GCs ausgeführt werden.

## <a name="working-with-a-synchronous-data-processing-api"></a>Arbeiten mit einer synchronen Datenverarbeitungs-API

Bei Verwendung eines Serialisierungs-/De-Serializers, der nur synchrone Lese- und Schreibvorgänge unterstützt (z. [B. JSON.NET):](https://www.newtonsoft.com/json/help/html/Introduction.htm)

* Puffern Sie die Daten asynchron in den Speicher, bevor Sie sie an den Serialisierungs-/Deserialisierer übergeben.

> [!WARNING]
> Wenn die Anforderung groß ist, kann dies zu einer OOM-Bedingung (Unzusmus) führen. OOM kann zu einem Denial Of Service führen.  Weitere Informationen finden Sie unter Vermeiden Sie das [Lesen großer Anforderungs- oder Antwortkörper in](#arlb) den Speicher in diesem Dokument.

ASP.NET Core 3.0 verwendet <xref:System.Text.Json> standardmäßig für die JSON-Serialisierung. <xref:System.Text.Json>:

* Lese- und Schreibvorgänge in JSON erfolgen asynchron.
* Der Code ist für UTF-8-Text optimiert.
* In der Regel lässt sich eine höhere Leistung als mit `Newtonsoft.Json` erzielen.

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a>IHttpContextAccessor.HttpContext nicht in einem Feld speichern

Der [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) `HttpContext` gibt die aktive Anforderung zurück, wenn über den Anforderungsthread zugegriffen wird. Der `IHttpContextAccessor.HttpContext` sollte **nicht** in einem Feld oder einer Variablen gespeichert werden.

**Tun Sie dies nicht:** Im folgenden Beispiel `HttpContext` wird der in einem Feld gespeichert und versucht dann, es später zu verwenden.

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

Der vorhergehende Code erfasst `HttpContext` häufig einen Nullwert oder einen falschen Wert im Konstruktor.

**Tun Sie dies:** Das folgende Beispiel:

* Speichert <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> die in einem Feld.
* Verwendet `HttpContext` das Feld zur richtigen `null`Zeit und sucht nach .

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a>Greifen Sie nicht von mehreren Threads aus auf HttpContext zu

`HttpContext`ist *NICHT* threadsicher. Der `HttpContext` parallele Zugriff von mehreren Threads kann zu undefiniertem Verhalten wie Hängen, Abstürzen und Datenbeschädigung führen.

**Tun Sie dies nicht:** Im folgenden Beispiel werden drei parallele Anforderungen ausgeführt und der eingehende Anforderungspfad vor und nach der ausgehenden HTTP-Anforderung protokolliert. Auf den Anforderungspfad wird von mehreren Threads zugegriffen, möglicherweise parallel.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

**Tun Sie dies:** Im folgenden Beispiel werden alle Daten aus der eingehenden Anforderung kopiert, bevor die drei parallelen Anforderungen gestellt werden.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a>Verwenden Sie den HttpContext nicht, nachdem die Anforderung abgeschlossen ist.

`HttpContext`ist nur gültig, solange eine aktive HTTP-Anforderung in der ASP.NET Core-Pipeline vorhanden ist. Die gesamte ASP.NET Core-Pipeline ist eine asynchrone Kette von Delegaten, die jede Anforderung ausführt. Wenn `Task` die von dieser Kette `HttpContext` zurückgegebenwird, wird die recycelt.

**Tun Sie dies nicht:** Im folgenden `async void` Beispiel wird verwendet, wodurch `await` die HTTP-Anforderung abgeschlossen wird, wenn die erste erreicht ist:

* Das ist **ALWAYS** eine schlechte Praxis in ASP.NET Core-Apps.
* Greift auf `HttpResponse` die nach Abschluss der HTTP-Anforderung zu.
* Absturz des Prozesses.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

**Tun Sie dies:** Im folgenden Beispiel `Task` wird eine an das Framework zurückgegeben, sodass die HTTP-Anforderung erst nach Abschluss der Aktion abgeschlossen wird.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a>Erfassen Sie den HttpContext nicht in Hintergrundthreads

**Tun Sie dies nicht:** Das folgende Beispiel zeigt, `HttpContext` dass `Controller` ein Abschluss die von der Eigenschaft erfasst. Dies ist eine schlechte Übung, da die Arbeitsaufgabe:

* Wird außerhalb des Anforderungsbereichs ausgeführt.
* Versuchen Sie, `HttpContext`die falsche zu lesen .

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

**Tun Sie dies:** Das folgende Beispiel:

* Kopiert die Daten, die während der Anforderung in der Hintergrundaufgabe erforderlich sind.
* Verweist auf nichts vom Controller.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

Hintergrundaufgaben sollten als gehostete Dienste implementiert werden. Weitere Informationen finden Sie unter [Hintergrundaufgaben mit gehosteten Diensten](xref:fundamentals/host/hosted-services).

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a>Erfassen Sie keine Dienste, die in die Controller in Hintergrundthreads eingefügt werden.

**Tun Sie dies nicht:** Das folgende Beispiel zeigt, `DbContext` dass `Controller` ein Abschluss die vom Aktionsparameter erfasst. Das ist eine schlechte Praxis.  Die Arbeitsaufgabe kann außerhalb des Anforderungsbereichs ausgeführt werden. Der `ContosoDbContext` ist auf die Anforderung beschränkt, was zu einer `ObjectDisposedException`führt.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

**Tun Sie dies:** Das folgende Beispiel:

* Injiziert eine, <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> um einen Bereich in der Hintergrundarbeitsaufgabe zu erstellen. `IServiceScopeFactory`ist ein Singleton.
* Erstellt einen neuen Abhängigkeitsinjektionsbereich im Hintergrundthread.
* Verweist auf nichts vom Controller.
* Erfasst die `ContosoDbContext` nicht von der eingehenden Anforderung.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

Der folgende hervorgehobene Code:

* Erstellt einen Bereich für die Lebensdauer des Hintergrundvorgangs und löst Dienste davon auf.
* Verwendet `ContosoDbContext` aus dem richtigen Bereich.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a>Ändern Sie den Statuscode oder die Header nicht, nachdem der Antworttext gestartet wurde.

ASP.NET Core puffert den HTTP-Antworttext nicht. Das erste Mal, dass die Antwort geschrieben wird:

* Die Header werden zusammen mit diesem Teil des Textkörpers an den Client gesendet.
* Es ist nicht mehr möglich, Antwortheader zu ändern.

**Tun Sie dies nicht:** Der folgende Code versucht, Antwortheader hinzuzufügen, nachdem die Antwort bereits gestartet wurde:

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

Löst im vorherigen `context.Response.Headers["test"] = "test value";` Code eine `next()` Ausnahme aus, wenn sie in die Antwort geschrieben wurde.

**Tun Sie dies:** Im folgenden Beispiel wird überprüft, ob die HTTP-Antwort gestartet wurde, bevor die Header geändert wurden.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

**Tun Sie dies:** Im folgenden `HttpResponse.OnStarting` Beispiel werden die Header festgelegt, bevor die Antwortheader an den Client geleert werden.

Wenn Überprüft wird, ob die Antwort nicht gestartet wurde, können Sie einen Rückruf registrieren, der kurz vor dem Schreiben von Antwortheadern aufgerufen wird. Überprüfen, ob die Antwort nicht gestartet wurde:

* Bietet die Möglichkeit, Header einfach rechtzeitig anzuhängen oder zu überschreiben.
* Erfordert keine Kenntnis der nächsten Middleware in der Pipeline.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a>Rufen Sie next() nicht an, wenn Sie bereits mit dem Schreiben an den Antworttext begonnen haben.

Komponenten erwarten nur, aufgerufen zu werden, wenn es möglich ist, dass sie die Antwort verarbeiten und bearbeiten können.

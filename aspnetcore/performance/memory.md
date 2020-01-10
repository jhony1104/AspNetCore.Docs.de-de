---
title: Speicherverwaltung und Muster in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Arbeitsspeicher in ASP.net Core verwaltet wird und wie die Garbage Collector (GC) funktioniert.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: performance/memory
ms.openlocfilehash: 0ae367e954e21e2f696a3b292fa64f1d2dba98ec
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829022"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a>Speicherverwaltung und Garbage Collection (GC) in ASP.net Core

Von [Sébastien Ros](https://github.com/sebastienros) und [Rick Anderson](https://twitter.com/RickAndMSFT)

Die Speicherverwaltung ist auch in verwalteten Frameworks wie .net Komplex. Das analysieren und verstehen von Speicherproblemen kann eine Herausforderung darstellen. Inhalt dieses Artikels

* Wurde von vielen *Speicherlecks* und *GC* -Fehlern motiviert. Die meisten dieser Probleme sind darauf zurückzuführen, dass Sie nicht verstehen, wie die Arbeitsspeicher Nutzung in .net Core funktioniert, oder nicht verstehen, wie Sie gemessen wird.
* Veranschaulicht die problematische Speicher Verwendung und schlägt alternative Ansätze vor.

## <a name="how-garbage-collection-gc-works-in-net-core"></a>Funktionsweise von Garbage Collection (GC) in .net Core

Der GC ordnet Heap Segmente zu, wobei jedes Segment ein zusammenhängender Speicherbereich ist. Objekte, die im Heap abgelegt werden, werden in einer von drei Generationen kategorisiert: 0, 1 oder 2. Die Generierung bestimmt die Häufigkeit, mit der die GC versucht, Arbeitsspeicher auf verwalteten Objekten freizugeben, auf die nicht mehr von der APP verwiesen wird. Niedrigere nummerierte Generationen werden häufiger als GC gezählt.

Objekte werden basierend auf Ihrer Lebensdauer von einer Generation zu einer anderen verschoben. Wenn Objekte länger leben, werden Sie in eine höhere Generation verschoben. Wie bereits erwähnt, sind höhere Generationen seltener. Kurzlebige Objekte bleiben immer in Generation 0. Beispielsweise sind Objekte, auf die während der Lebensdauer einer Webanforderung verwiesen wird, kurzlebig. [Singletons](xref:fundamentals/dependency-injection#service-lifetimes) auf Anwendungsebene werden in der Regel zu Generation 2 migriert.

Wenn eine ASP.net Core-App gestartet wird, wird der GC:

* Reserviert Speicher für die anfänglichen Heap Segmente.
* Führt einen Commit für einen kleinen Teil des Speichers aus, wenn die Laufzeit geladen wird.

Die vorhergehenden Speicher Belegungen werden aus Leistungsgründen ausgeführt. Der Leistungsvorteil ergibt sich aus Heap Segmenten im zusammenhängenden Arbeitsspeicher.

### <a name="call-gccollect"></a>Ruft GC auf. Samm

GC wird aufgerufen [. Explizit erfassen](xref:System.GC.Collect*) :

* Sollte **nicht** von Produktions ASP.net Core-apps ausgeführt werden.
* Ist nützlich bei der Untersuchung von Speicher Verlusten.
* Bei der Untersuchung überprüft, ob der GC alle verbleibenden Objekte aus dem Arbeitsspeicher entfernt hat, damit der Arbeitsspeicher gemessen werden kann.

## <a name="analyzing-the-memory-usage-of-an-app"></a>Analysieren der Speicherauslastung einer APP

Dedizierte Tools können bei der Analyse der Speicherauslastung helfen:

- Zählen von Objekt verweisen
- Messen der Auswirkung der GC auf die CPU-Auslastung durch den GC
- Messen des für jede Generation verwendeten Speicherplatzes

Verwenden Sie die folgenden Tools, um die Speicherauslastung zu analysieren:

* [dotnet-Trace](/dotnet/core/diagnostics/dotnet-trace): kann auf Produktions Computern verwendet werden.
* [Analysieren der Speicherauslastung ohne den Visual Studio-Debugger](/visualstudio/profiling/memory-usage-without-debugging2)
* [Profilerstellung zur Arbeitsspeicherverwendung in Visual Studio](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a>Erkennen von Speicherproblemen

Der Task-Manager kann verwendet werden, um eine Vorstellung davon zu erhalten, wie viel Arbeitsspeicher eine ASP.net-App verwendet. Der Arbeitsspeicher Wert des Task-Managers:

* Stellt die Menge an Arbeitsspeicher dar, die vom ASP.NET-Prozess verwendet wird.
* Enthält die Living-Objekte der APP und andere Arbeitsspeicherconsumer, z. b. systemeigene Speicherauslastung.

Wenn der Arbeitsspeicher Wert des Task-Managers unbegrenzt zunimmt und nie vereinfacht wird, weist die APP einen Speicher Verlust auf. In den folgenden Abschnitten werden verschiedene Speicher Verwendungs Muster veranschaulicht und erläutert.

## <a name="sample-display-memory-usage-app"></a>Beispiel für die Anzeige der Arbeitsspeicher Auslastung

Die [Memoryleak-Beispiel-App](https://github.com/sebastienros/memoryleak) ist auf GitHub verfügbar. Die memoryleck-App:

* Enthält einen Diagnose Controller, der Echt Zeit Speicher-und GC-Daten für die APP sammelt.
* Verfügt über eine Index Seite, auf der die Speicher-und GC-Daten angezeigt werden. Die Index Seite wird jede Sekunde aktualisiert.
* Enthält einen API-Controller, der verschiedene Speicher Auslastungs Muster bereitstellt.
* Ist kein unterstütztes Tool, kann jedoch verwendet werden, um Speicher Auslastungs Muster von ASP.net Core-apps anzuzeigen.

Führen Sie memoryleck aus. Der zugewiesene Arbeitsspeicher steigt langsam, bis eine GC auftritt. Der Arbeitsspeicher nimmt zu, da das Tool benutzerdefiniertes Objekt zum Erfassen von Daten zuordnet Die folgende Abbildung zeigt die Index Seite memoryleck, wenn eine Generation 0-GC auftritt. Das Diagramm zeigt 0 RPS (Anforderungen pro Sekunde), da keine API-Endpunkte vom API-Controller aufgerufen wurden.

![Vorheriges Diagramm](memory/_static/0RPS.png)

Im Diagramm werden zwei Werte für die Speicherauslastung angezeigt:

- Reserviert: die Menge an Arbeitsspeicher, die von verwalteten Objekten belegt wird.
- [Workingset](/windows/win32/memory/working-set): der Satz von Seiten im virtuellen Adressraum des Prozesses, der sich zurzeit im physischen Speicher befinden. Das angezeigte Workingset ist derselbe Wert, den Task-Manager anzeigt.

### <a name="transient-objects"></a>Vorübergehende Objekte

Die folgende API erstellt eine 10-KB-Zeichen folgen Instanz und gibt Sie an den Client zurück. Bei jeder Anforderung wird ein neues-Objekt im Arbeitsspeicher zugeordnet und in die Antwort geschrieben. Zeichen folgen werden als UTF-16-Zeichen in .net gespeichert, sodass jedes Zeichen 2 Bytes im Arbeitsspeicher benötigt.

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

Das folgende Diagramm wird mit einer relativ kleinen Auslastung in generiert, um anzuzeigen, wie die Speicher Belegungen durch die GC beeinträchtigt werden.

![Vorheriges Diagramm](memory/_static/bigstring.png)

Das vorangehende Diagramm zeigt Folgendes:

* 4K RPS (Anforderungen pro Sekunde).
* Garbage Collections der Generation 0 erfolgen ungefähr alle zwei Sekunden.
* Die Workingsets sind konstant bei ungefähr 500 MB.
* CPU ist 12%.
* Die Arbeitsspeicher Nutzung und-Freigabe (über GC) ist stabil.

Das folgende Diagramm wird mit dem maximalen Durchsatz erstellt, der vom Computer verarbeitet werden kann.

![Vorheriges Diagramm](memory/_static/bigstring2.png)

Das vorangehende Diagramm zeigt Folgendes:

* 22K RPS
* Garbage Collections der Generation 0 sind mehrmals pro Sekunde aufgetreten.
* Sammlungen der Generation 1 werden ausgelöst, da die APP bedeutend mehr Arbeitsspeicher pro Sekunde zugewiesen hat.
* Die Workingsets sind konstant bei ungefähr 500 MB.
* Die CPU-Auslastung beträgt 33%.
* Die Arbeitsspeicher Nutzung und-Freigabe (über GC) ist stabil.
* CPU (33%) ist nicht über ausgelastet, daher kann der Garbage Collection mit einer hohen Anzahl von Zuordnungen mithalten.

### <a name="workstation-gc-vs-server-gc"></a>Arbeitsstation GC im Vergleich zu Server GC

Der .NET Garbage Collector verfügt über zwei verschiedene Modi:

* **Arbeitsstation GC**: für den Desktop optimiert.
* **Server-GC** Der standardmäßige GC für ASP.net Core-apps. Für den Server optimiert.

Der GC-Modus kann explizit in der Projektdatei oder in der Datei " *runtimeconfig. JSON* " der veröffentlichten App festgelegt werden. Das folgende Markup zeigt das Festlegen von `ServerGarbageCollection` in der Projektdatei:

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

Um `ServerGarbageCollection` in der Projektdatei zu ändern, muss die APP neu erstellt werden.

**Hinweis:** Server Garbage Collection ist auf Computern mit einem einzelnen Kern **nicht** verfügbar. Weitere Informationen finden Sie unter <xref:System.Runtime.GCSettings.IsServerGC>.

In der folgenden Abbildung ist das Arbeitsspeicher Profil unter einem 5K RPS mithilfe der Arbeitsstations-GC dargestellt.

![Vorheriges Diagramm](memory/_static/workstation.png)

Die Unterschiede zwischen diesem Diagramm und der Server Version sind von Bedeutung:

- Das Workingset sinkt von 500 MB auf 70 MB.
- Der GC führt die Garbage Collection der Generation 0 mehrmals pro Sekunde statt alle zwei Sekunden aus.
- GC fällt von 300 MB auf 10 MB.

In einer typischen Webserver Umgebung ist die CPU-Auslastung wichtiger als der Arbeitsspeicher, daher ist die Server-GC besser geeignet. Wenn die Arbeitsspeicher Auslastung hoch ist und die CPU-Auslastung relativ gering ist, kann die GC der Arbeitsstation leistungsfähiger werden. Beispielsweise eine hohe Dichte, in der mehrere Web-Apps gehostet werden, bei denen Arbeitsspeicher knapp

### <a name="persistent-object-references"></a>Persistente Objekt Verweise

Der GC kann keine Objekte freigeben, auf die verwiesen wird. Objekte, auf die verwiesen wird, die jedoch nicht mehr benötigt werden, führen zu einem Speicherplatz. Wenn die APP Objekte häufig anordnet und Sie nicht mehr freigibt, wenn Sie nicht mehr benötigt werden, erhöht sich die Speicherauslastung im Laufe der Zeit.

Die folgende API erstellt eine 10-KB-Zeichen folgen Instanz und gibt Sie an den Client zurück. Der Unterschied zum vorherigen Beispiel besteht darin, dass auf diese Instanz von einem statischen Member verwiesen wird. Dies bedeutet, dass Sie nie für die Auflistung verfügbar ist.

```csharp
private static ConcurrentBag<string> _staticStrings = new ConcurrentBag<string>();

[HttpGet("staticstring")]
public ActionResult<string> GetStaticString()
{
    var bigString = new String('x', 10 * 1024);
    _staticStrings.Add(bigString);
    return bigString;
}
```

Der vorangehende Code:

* Ist ein Beispiel für einen typischen Speicherplatz.
* Bei häufigen aufrufen bewirkt, dass der APP-Speicher zunimmt, bis der Prozess mit einer `OutOfMemory` Ausnahme abstürzt.

![Vorheriges Diagramm](memory/_static/eternal.png)

In der vorangehenden Abbildung:

* Ein Auslastungs Test des `/api/staticstring` Endpunkts bewirkt eine lineare Zunahme des Speichers.
* Der GC versucht, Arbeitsspeicher freizugeben, wenn die Arbeitsspeicher Auslastung zunimmt, indem eine Sammlung der Generation 2 aufgerufen wird.
* Der GC kann den kompromittierten Speicher nicht freigeben. Der zugewiesene und Workingset erhöhen sich mit der Zeit.

Einige Szenarien, wie z. b. Caching, erfordern, dass Objekt Verweise aufrechterhalten werden, bis die Arbeitsspeicher Auslastung die Freigabe aufgehoben. Die <xref:System.WeakReference>-Klasse kann für diese Art von cachingcode verwendet werden. Ein `WeakReference`-Objekt wird unter Arbeitsspeicher Druck gesammelt. Die Standard Implementierung von <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> verwendet `WeakReference`.

### <a name="native-memory"></a>Nativer Speicher

Einige .net Core-Objekte basieren auf System eigenem Arbeitsspeicher. Der Native Arbeitsspeicher kann **nicht** vom GC gesammelt werden. Das .NET-Objekt, das den nativen Speicher verwendet, muss es mit System eigenem Code freigeben

.NET stellt die <xref:System.IDisposable>-Schnittstelle bereit, damit Entwickler systemeigenen Speicher freigeben können. Selbst wenn <xref:System.IDisposable.Dispose*> nicht aufgerufen wird, wird von ordnungsgemäß implementierten Klassen `Dispose` aufgerufen, wenn der [Finalizer](/dotnet/csharp/programming-guide/classes-and-structs/destructors) ausgeführt wird.

Betrachten Sie folgenden Code:

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

[Physicalfileprovider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) ist eine verwaltete Klasse, daher wird jede Instanz am Ende der Anforderung gesammelt.

In der folgenden Abbildung wird das Speicher Profil angezeigt, während der `fileprovider`-API fortlaufend aufgerufen wird.

![Vorheriges Diagramm](memory/_static/fileprovider.png)

Das vorangehende Diagramm zeigt ein offensichtliches Problem mit der Implementierung dieser Klasse, da die Speicherauslastung weiterhin zunimmt. Dies ist ein bekanntes Problem, das in [diesem Problem](https://github.com/dotnet/aspnetcore/issues/3110)nachverfolgt wird.

Der gleiche Fehler kann im Benutzercode auftreten, indem eine der folgenden Aktionen durchgeführt wird:

* Die Klasse wird nicht ordnungsgemäß freigegeben.
* Vergessen Sie nicht, die `Dispose`-Methode der abhängigen Objekte aufzurufen, die verworfen werden sollen.

### <a name="large-objects-heap"></a>Heap für große Objekte

Häufige Speicher Belegungen/freie Zyklen können den Arbeitsspeicher fragmentieren, insbesondere bei der Zuordnung von großen Arbeitsspeicher Blöcken. Objekte werden in zusammenhängenden Speicherblöcken zugeordnet. Um die Fragmentierung zu minimieren, wird das Defragmentieren, wenn der GC Speicher freigibt, defragmentiert. Dieser Vorgang wird als " **Komprimierung**" bezeichnet. Die Komprimierung umfasst das Verschieben von Objekten. Das Verschieben von großen Objekten führt zu einer Leistungs Einbuße. Aus diesem Grund erstellt der GC eine spezielle Speicher Zone für _große_ Objekte ( [Large Object Heap](/dotnet/standard/garbage-collection/large-object-heap) , Loh). Objekte, die größer als 85.000 Byte sind (etwa 83 KB):

* Auf dem Loh platziert.
* Nicht komprimiert.
* Wird während der Generation 2-GCS gesammelt.

Wenn der Loh voll ist, löst der GC eine Sammlung der Generation 2 aus. Sammlungen der Generation 2:

* Sind grundsätzlich langsam.
* Zusätzlich entstehen die Kosten für das Auslösen einer Sammlung für alle anderen Generationen.

Der folgende Code komprimiert den Loh sofort:

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

Informationen zur Komprimierung des Loh finden Sie unter <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode>.

In Containern, die .net Core 3,0 und höher verwenden, wird der Loh automatisch komprimiert.

Die folgende API veranschaulicht dieses Verhalten:

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

Im folgenden Diagramm wird das Speicher Profil für das Aufrufen des `/api/loh/84975` Endpunkts unter maximale Auslastung angezeigt:

![Vorheriges Diagramm](memory/_static/loh1.png)

Das folgende Diagramm zeigt das Speicher Profil für das Aufrufen des `/api/loh/84976` Endpunkts, wobei *nur noch ein Byte*zugeordnet wird:

![Vorheriges Diagramm](memory/_static/loh2.png)

Hinweis: die `byte[]` Struktur hat mehr Byte. Aus diesem Grund wird das 85.000-Limit von 84.976 bytes ausgelöst.

Vergleichen der beiden vorangehenden Diagramme:

* Das Workingset ist für beide Szenarien ähnlich, etwa 450 MB.
* Die unter Loh-Anforderungen (84.975 Bytes) zeigen überwiegend Auflistungen der Generation 0.
* Die over Loh-Anforderungen generieren Konstante Generation 2-Auflistungen. Sammlungen der Generation 2 sind aufwendig. Es ist mehr CPU erforderlich, und der Durchsatz sinkt fast 50%.

Temporäre große Objekte sind besonders problematisch, da Sie Gen2-GCS verursachen.

Um die maximale Leistung zu erzielen, sollte die Verwendung von großen Objekten minimiert werden. Teilen Sie nach Möglichkeit große Objekte auf. Beispielsweise wird durch die Middleware zum zwischen [Speichern von Antworten](xref:performance/caching/response) in ASP.net Core die Cache Einträge in Blöcke aufgeteilt, die kleiner als 85.000 Bytes sind.

Die folgenden Links zeigen die ASP.net Core Methode zum Beibehalten von Objekten unter dem Loh-Grenzwert:

* [Responsecaching/Streams/streamutilities. cs](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [Responsecaching/memoryresponsecache. cs](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

Weitere Informationen finden Sie unter: .

* [Large Object Heap wurde aufgedeckt.](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Großer Objekt Heap](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a>HttpClient

Wenn <xref:System.Net.Http.HttpClient> falsch verwendet wird, kann dies zu einem Ressourcen Fehler führen. System Ressourcen, z. b. Datenbankverbindungen, Sockets, Datei Handles usw.:

* Sind knapper als der Arbeitsspeicher.
* Sind schwieriger, wenn es zu einem kompromittierten als Arbeitsspeicher

Erfahrene .NET-Entwickler wissen, <xref:System.IDisposable.Dispose*> für Objekte aufzurufen, die <xref:System.IDisposable>implementieren. Das Verwerfen von Objekten, die `IDisposable` implementieren, führt normalerweise zu einem kompromittierten Speicher oder zu kompromittierten Systemressourcen.

`HttpClient` implementiert `IDisposable`, sollte jedoch bei jedem Aufruf **nicht** verworfen werden. Stattdessen sollten `HttpClient` wieder verwendet werden.

Der folgende Endpunkt erstellt eine neue `HttpClient`-Instanz für jede Anforderung und gibt Sie frei:

```csharp
[HttpGet("httpclient1")]
public async Task<int> GetHttpClient1(string url)
{
    using (var httpClient = new HttpClient())
    {
        var result = await httpClient.GetAsync(url);
        return (int)result.StatusCode;
    }
}
```

Unter Last werden die folgenden Fehlermeldungen protokolliert:

```
fail: Microsoft.AspNetCore.Server.Kestrel[13]
      Connection id "0HLG70PBE1CR1", Request id "0HLG70PBE1CR1:00000031":
      An unhandled exception was thrown by the application.
System.Net.Http.HttpRequestException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted --->
    System.Net.Sockets.SocketException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted
   at System.Net.Http.ConnectHelper.ConnectAsync(String host, Int32 port,
    CancellationToken cancellationToken)
```

Obwohl die `HttpClient` Instanzen verworfen werden, dauert die Freigabe der tatsächlichen Netzwerkverbindung vom Betriebssystem einige Zeit. Durch das fortlaufende Erstellen neuer Verbindungen erfolgt die Auslastung der _Ports_ . Jede Client Verbindung erfordert einen eigenen ClientPort.

Eine Möglichkeit, die Port Auslastung zu verhindern, besteht darin, dieselbe `HttpClient` Instanz wiederzuverwenden:

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

Die `HttpClient` Instanz wird freigegeben, wenn die APP angehalten wird. Dieses Beispiel zeigt, dass nicht jede verwerfbare Ressource nach jeder Verwendung verworfen werden sollte.

Im folgenden finden Sie eine bessere Möglichkeit, um die Lebensdauer einer `HttpClient` Instanz zu behandeln:

* [HttpClient und Lebensdauer Verwaltung](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [HttpClient-Factory-Blog](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a>Objekt Pooling

Im vorherigen Beispiel wurde gezeigt, wie die `HttpClient` Instanz statisch gemacht und von allen Anforderungen wieder verwendet werden kann. Durch die Wiederverwendung wird verhindert, dass Ressourcen aussteht.

Objekt Pooling:

* Verwendet das Verwendungs Muster.
* Ist für Objekte konzipiert, die teuer zu erstellen sind.

Ein Pool ist eine Sammlung von vorinitialisierten Objekten, die über mehrere Threads reserviert und freigegeben werden können. Pools können Zuordnungs Regeln definieren, z. b. Grenzwerte, vordefinierte Größen oder Wachstumsrate.

Das nuget-Paket [Microsoft. Extensions. Objectpool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) enthält Klassen, die die Verwaltung solcher Pools erleichtern.

Der folgende API-Endpunkt instanziiert einen `byte` Puffer, der bei jeder Anforderung mit Zufallszahlen aufgefüllt wird:

```csharp
        [HttpGet("array/{size}")]
        public byte[] GetArray(int size)
        {
            var random = new Random();
            var array = new byte[size];
            random.NextBytes(array);

            return array;
        }
```

Im folgenden Diagramm wird der Aufruf der vorangehenden API mit mittlerer Auslastung angezeigt:

![Vorheriges Diagramm](memory/_static/array.png)

Im vorangehenden Diagramm erfolgen die Sammlungen der Generation 0 ungefähr einmal pro Sekunde.

Der vorangehende Code kann optimiert werden, indem der `byte` Puffer mithilfe von [arraypool\<t > gepoolt](xref:System.Buffers.ArrayPool`1)wird. Eine statische-Instanz wird in allen Anforderungen wieder verwendet.

Unterscheidet sich bei diesem Ansatz, dass ein in einem Pool zusammengefasste Objekt von der API zurückgegeben wird. Dies bedeutet Folgendes:

* Das-Objekt befindet sich außerhalb des-Steuer Elements, sobald Sie von der-Methode zurückkehren.
* Das Objekt kann nicht freigegeben werden.

So richten Sie die Entsorgung des-Objekts ein:

* Kapseln Sie das gepoolte Array in ein verwerfbares Objekt.
* Registrieren Sie das in einem Pool zusammengefasste Objekt mit [HttpContext. Response. registerforverwerfen](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).

`RegisterForDispose` übernimmt das Aufrufen von `Dispose`für das Zielobjekt, sodass es nur freigegeben wird, wenn die HTTP-Anforderung beendet ist.

```csharp
private static ArrayPool<byte> _arrayPool = ArrayPool<byte>.Create();

private class PooledArray : IDisposable
{
    public byte[] Array { get; private set; }

    public PooledArray(int size)
    {
        Array = _arrayPool.Rent(size);
    }

    public void Dispose()
    {
        _arrayPool.Return(Array);
    }
}

[HttpGet("pooledarray/{size}")]
public byte[] GetPooledArray(int size)
{
    var pooledArray = new PooledArray(size);

    var random = new Random();
    random.NextBytes(pooledArray.Array);

    HttpContext.Response.RegisterForDispose(pooledArray);

    return pooledArray.Array;
}
```

Das Anwenden derselben Last wie die nicht in einem Pool zusammengefasste Version führt zu folgendem Diagramm:

![Vorheriges Diagramm](memory/_static/pooledarray.png)

Der Hauptunterschied besteht darin, dass Bytes zugeordnet werden, was zu einer wesentlich geringeren Auflistung der Generation 0 gehört.

## <a name="additional-resources"></a>Weitere Ressourcen

* [Garbage Collection](/dotnet/standard/garbage-collection/)
* [Grundlegendes zu verschiedenen GC-Modi mit neben läufigkeits Schnellansicht](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [Large Object Heap wurde aufgedeckt.](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Großer Objekt Heap](/dotnet/standard/garbage-collection/large-object-heap)

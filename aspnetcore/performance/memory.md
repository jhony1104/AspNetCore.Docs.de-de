---
title: Speicherverwaltung und Muster in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Speicher in ASP.NET Core verwaltet wird und wie der Garbage Collector (GC) funktioniert.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: performance/memory
ms.openlocfilehash: b2af9cb567cdb1d7b2d0942601fcc3ebd999a5d9
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440947"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a>Speicherverwaltung und Garbage Collection (GC) in ASP.NET Core

Von [Sébastien Ros](https://github.com/sebastienros) und [Rick Anderson](https://twitter.com/RickAndMSFT)

Die Speicherverwaltung ist komplex, selbst in einem verwalteten Framework wie .NET. Das Analysieren und Verstehen von Speicherproblemen kann eine Herausforderung darstellen. Dieser Artikel:

* Wurde durch viele *Speicherlecks* und *GC nicht funktionierende* Probleme motiviert. Die meisten dieser Probleme wurden dadurch verursacht, dass sie nicht verstanden haben, wie der Speicherverbrauch in .NET Core funktioniert, oder nicht, wie er gemessen wird.
* Veranschaulicht die problematische Speichernutzung und schlägt alternative Ansätze vor.

## <a name="how-garbage-collection-gc-works-in-net-core"></a>Funktionsweise der Garbage Collection (GC) in .NET Core

Der GC weist Heapsegmente zu, bei denen jedes Segment ein zusammenhängender Speicherbereich ist. Objekte, die im Heap platziert werden, werden in eine von 3 Generationen kategorisiert: 0, 1 oder 2. Die Generierung bestimmt die Häufigkeit, mit der der GC versucht, Speicher für verwaltete Objekte freizugeben, auf die nicht mehr von der App verwiesen wird. Niedrigere nummerierte Generationen sind häufiger GC'd.

Objekte werden basierend auf ihrer Lebensdauer von einer Generation in eine andere verschoben. Da Objekte länger leben, werden sie in eine höhere Generation verschoben. Wie bereits erwähnt, sind höhere Generationen weniger oft GC'd. Kurzfristig lebende Objekte bleiben immer in Generation 0. Beispielsweise sind Objekte, auf die während der Lebensdauer einer Webanforderung verwiesen wird, kurzlebig. [Singletons](xref:fundamentals/dependency-injection#service-lifetimes) auf Anwendungsebene werden in der Regel in die Generation 2 migriert.

Wenn eine ASP.NET Core-App gestartet wird, wird der GC:

* Reserviert etwas Speicher für die anfänglichen Heapsegmente.
* Überträgt einen kleinen Teil des Arbeitsspeichers, wenn die Laufzeit geladen wird.

Die vorherigen Speicherzuweisungen werden aus Leistungsgründen durchgeführt. Der Leistungsvorteil ergibt sich aus Heapsegmenten im zusammenhängenden Speicher.

### <a name="call-gccollect"></a>Rufen Sie GC auf. Sammeln

Aufruf von [GC. Sammeln Sie](xref:System.GC.Collect*) explizit:

* Sollte **nicht** durch produktion ASP.NET Core-Apps durchgeführt werden.
* Ist nützlich, wenn Speicherverluste untersucht werden.
* Bei der Untersuchung überprüft, ob der GC alle baumelnden Objekte aus dem Speicher entfernt hat, damit der Speicher gemessen werden kann.

## <a name="analyzing-the-memory-usage-of-an-app"></a>Analysieren der Speichernutzung einer App

Spezielle Tools können bei der Analyse der Speichernutzung helfen:

- Zählen von Objektreferenzen
- Messen, wie viel Einfluss der GC auf die CPU-Auslastung hat
- Messen des für jede Generation genutzten Speichers

Verwenden Sie die folgenden Tools, um die Speichernutzung zu analysieren:

* [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Kann auf Produktionsmaschinen verwendet werden.
* [Analysieren der Arbeitsspeicherauslastung ohne Visual Studio-Debugger](/visualstudio/profiling/memory-usage-without-debugging2)
* [Profilerstellung zur Arbeitsspeicherverwendung in Visual Studio](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a>Erkennen von Speicherproblemen

Der Task-Manager kann verwendet werden, um eine Vorstellung davon zu erhalten, wie viel Arbeitsspeicher eine ASP.NET App verwendet. Der Speicherwert des Task-Managers:

* Stellt die Speichermenge dar, die vom ASP.NET Prozess verwendet wird.
* Enthält die lebenden Objekte der App und andere Speicherverbraucher, z. B. die Verwendung von systemeigenem Speicher.

Wenn der Speicherwert des Task-Managers auf unbestimmte Zeit steigt und nie abgeflacht wird, weist die App einen Speicherverlust auf. In den folgenden Abschnitten werden mehrere Speichernutzungsmuster veranschaulicht und erläutert.

## <a name="sample-display-memory-usage-app"></a>BeispielanzeigeSpeichernutzungs-App

Die [MemoryLeak-Beispiel-App](https://github.com/sebastienros/memoryleak) ist auf GitHub verfügbar. Die MemoryLeak App:

* Enthält einen Diagnosecontroller, der Echtzeitspeicher und GC-Daten für die App sammelt.
* Verfügt über eine Indexseite, auf der die Speicher- und GC-Daten angezeigt werden. Die Indexseite wird jede Sekunde aktualisiert.
* Enthält einen API-Controller, der verschiedene Speicherlademuster bereitstellt.
* Es ist kein unterstütztes Tool, kann jedoch verwendet werden, um Speichernutzungsmuster von ASP.NET Core-Apps anzuzeigen.

Führen Sie MemoryLeak aus. Der zugewiesene Speicher nimmt langsam zu, bis ein GC auftritt. Der Arbeitsspeicher wird erhöht, da das Tool benutzerdefinierte Objekte zum Erfassen von Daten zuweist. Die folgende Abbildung zeigt die MemoryLeak Index-Seite, wenn ein Gen 0 GC auftritt. Das Diagramm zeigt 0 RPS (Requests per Second), da keine API-Endpunkte vom API-Controller aufgerufen wurden.

![vorhergehendes Diagramm](memory/_static/0RPS.png)

Das Diagramm zeigt zwei Werte für die Speichernutzung an:

- Zugeordnet: die Menge an Speicher, die von verwalteten Objekten belegt wird
- [Arbeitssatz](/windows/win32/memory/working-set): Der Satz von Seiten im virtuellen Adressraum des Prozesses, die sich derzeit im physischen Speicher befinden. Der angezeigte Arbeitssatz ist derselbe Wert, den Task-Manager anzeigt.

### <a name="transient-objects"></a>Transiente Objekte

Die folgende API erstellt eine 10-KB-Zeichenfolgeninstanz und gibt sie an den Client zurück. Bei jeder Anforderung wird ein neues Objekt im Speicher zugewiesen und in die Antwort geschrieben. Zeichenfolgen werden als UTF-16-Zeichen in .NET gespeichert, sodass jedes Zeichen 2 Bytes im Arbeitsspeicher benötigt.

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

Das folgende Diagramm wird mit einer relativ geringen Last generiert, um zu zeigen, wie sich die Speicherzuweisungen auf den GC auswirken.

![vorhergehendes Diagramm](memory/_static/bigstring.png)

Das obige Diagramm zeigt:

* 4K RPS (Anforderungen pro Sekunde).
* Generation 0 GC-Sammlungen treten etwa alle zwei Sekunden auf.
* Der Arbeitssatz ist konstant bei ca. 500 MB.
* Die CPU beträgt 12%.
* Der Speicherverbrauch und die Freigabe (über GC) sind stabil.

Das folgende Diagramm wird mit dem maximalen Durchsatz aufgenommen, der von der Maschine verarbeitet werden kann.

![vorhergehendes Diagramm](memory/_static/bigstring2.png)

Das obige Diagramm zeigt:

* 22K RPS
* GC-Sammlungen der Generation 0 treten mehrmals pro Sekunde auf.
* Sammlungen der Generation 1 werden ausgelöst, weil die App deutlich mehr Arbeitsspeicher pro Sekunde zugewiesen hat.
* Der Arbeitssatz ist konstant bei ca. 500 MB.
* Die CPU beträgt 33 %.
* Der Speicherverbrauch und die Freigabe (über GC) sind stabil.
* Die CPU (33%) nicht überausgenutzt ist, daher kann die Garbage Collection mit einer hohen Anzahl von Zuweisungen Schritt halten.

### <a name="workstation-gc-vs-server-gc"></a>Workstation GC vs. Server GC

Der .NET Garbage Collector verfügt über zwei verschiedene Modi:

* **Workstation GC**: Optimiert für den Desktop.
* **Server GC**. Der Standard-GC für ASP.NET Core-Apps. Optimiert für den Server.

Der GC-Modus kann explizit in der Projektdatei oder in der *Datei runtimeconfig.json* der veröffentlichten App festgelegt werden. Das folgende Markup `ServerGarbageCollection` zeigt die Einstellung in der Projektdatei:

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

Wenn `ServerGarbageCollection` Sie die Projektdatei ändern, muss die App neu erstellt werden.

**Hinweis:** Die Servergarbage Collection ist auf Computern mit einem einzigen Kern **nicht** verfügbar. Weitere Informationen finden Sie unter <xref:System.Runtime.GCSettings.IsServerGC>.

Die folgende Abbildung zeigt das Speicherprofil unter einem 5K RPS mit dem Workstation GC.

![vorhergehendes Diagramm](memory/_static/workstation.png)

Die Unterschiede zwischen diesem Diagramm und der Serverversion sind erheblich:

- Der Arbeitssatz sinkt von 500 MB auf 70 MB.
- Der GC erstellt Die Generation 0-Sammlungen mehrmals pro Sekunde statt alle zwei Sekunden.
- GC sinkt von 300 MB auf 10 MB.

In einer typischen Webserverumgebung ist die CPU-Auslastung wichtiger als Speicher, daher ist der Server GC besser. Wenn die Speicherauslastung hoch und die CPU-Auslastung relativ gering ist, ist der Workstation-GC möglicherweise leistungsstärker. Zum Beispiel, hohe Dichte Hosting mehrere Web-Apps, wo Speicher knapp ist.

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a>GC mit Docker und kleinen Containern

Wenn mehrere containerisierte Apps auf einem Computer ausgeführt werden, ist Workstation GC möglicherweise vorformanter als Server GC. Weitere Informationen finden Sie unter [Ausführen mit Server GC in einem kleinen Container](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) und Ausführen mit Server GC in einem kleinen [Containerszenario Teil 1 – Hard Limit für den GC-Heap](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).

### <a name="persistent-object-references"></a>Persistente Objektverweise

Der GC kann keine Objekte freisetzen, auf die verwiesen wird. Objekte, auf die verwiesen wird, aber nicht mehr benötigt wird, führen zu einem Speicherverlust. Wenn die App häufig Objekte zuweist und sie nicht mehr freigibt, nachdem sie nicht mehr benötigt werden, nimmt die Speicherauslastung mit der Zeit zu.

Die folgende API erstellt eine 10-KB-Zeichenfolgeninstanz und gibt sie an den Client zurück. Der Unterschied zum vorherigen Beispiel besteht darin, dass auf diese Instanz von einem statischen Member verwiesen wird, d. h., sie ist nie für die Auflistung verfügbar.

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

* Ein Beispiel für ein typisches Speicherleck.
* Bei häufigen Aufrufen erhöht sich der App-Speicher, bis der Prozess mit einer `OutOfMemory` Ausnahme abstürzt.

![vorhergehendes Diagramm](memory/_static/eternal.png)

Im obigen Bild:

* Das Auslastungstesten des `/api/staticstring` Endpunkts führt zu einer linearen Erhöhung des Arbeitsspeichers.
* Der GC versucht, Speicher freizugeben, wenn der Speicherdruck wächst, indem er eine Generation 2-Sammlung aufruft.
* Der GC kann den durchgesickerten Speicher nicht freimachen. Zugeordnete und Arbeitsset erhöhen sich mit der Zeit.

In einigen Szenarien, z. B. dem Zwischenspeichern, müssen Objektverweise so lange gespeichert werden, bis sie vom Speicherdruck freigegeben werden. Die <xref:System.WeakReference> Klasse kann für diesen Typ von Zwischenspeicherungscode verwendet werden. Ein `WeakReference` Objekt wird unter Speicherdruck gesammelt. Die Standardimplementierung <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> `WeakReference`von uses .

### <a name="native-memory"></a>Natives Gedächtnis

Einige .NET Core-Objekte basieren auf systemeigenem Speicher. Nativer Speicher kann vom GC **nicht** erfasst werden. Das .NET-Objekt, das systemeigenen Speicher verwendet, muss es mithilfe von systemeigenem Code freisetzen.

.NET stellt <xref:System.IDisposable> die Schnittstelle bereit, mit der Entwickler systemeigenen Speicher freigeben können. Auch <xref:System.IDisposable.Dispose*> wenn nicht aufgerufen wird, `Dispose` rufen korrekt implementierte Klassen auf, wenn der [Finalizer](/dotnet/csharp/programming-guide/classes-and-structs/destructors) ausgeführt wird.

Betrachten Sie folgenden Code:

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) ist eine verwaltete Klasse, daher wird jede Instanz am Ende der Anforderung gesammelt.

Die folgende Abbildung zeigt das Speicherprofil, während die `fileprovider` API kontinuierlich aufruft.

![vorhergehendes Diagramm](memory/_static/fileprovider.png)

Das obige Diagramm zeigt ein offensichtliches Problem mit der Implementierung dieser Klasse, da sie die Speicherauslastung weiter erhöht. Dies ist ein bekanntes Problem, das in [diesem Problem](https://github.com/dotnet/aspnetcore/issues/3110)nachverfolgt wird.

Das gleiche Leck kann im Benutzercode passieren, durch eine der folgenden:

* Die Klasse wird nicht korrekt freigegeben.
* Vergessen, die `Dispose`Methode der abhängigen Objekte aufzurufen, die verworfen werden sollen.

### <a name="large-objects-heap"></a>Heap für große Objekte

Häufige Speicherzuweisungen/freie Zyklen können speicherfragmentieren, insbesondere wenn große Speicherblöcke zugewiesen werden. Objekte werden in zusammenhängenden Speicherblöcken zugeordnet. Um die Fragmentierung zu verringern, versucht der GC, den Speicher freizugeben, ihn zu defragmentieren. Dieser Prozess wird als **Verdichtung**bezeichnet. Bei der Verdichtung werden Objekte bewegt. Das Verschieben großer Objekte führt zu leistungseinbußen. Aus diesem Grund erstellt der GC eine spezielle Speicherzone für _große_ Objekte, die als ["Large Object Heap" (LOH)](/dotnet/standard/garbage-collection/large-object-heap) bezeichnet wird. Objekte, die größer als 85.000 Bytes (ca. 83 KB) sind:

* Platziert auf der LOH.
* Nicht verdichtet.
* Gesammelt während der Generation 2 GCs.

Wenn der LOH voll ist, löst der GC eine Generation 2-Auflistung aus. Generationen 2 Kollektionen:

* Sind von Natur aus langsam.
* Zusätzlich entstehen die Kosten für das Auslösen einer Sammlung für alle anderen Generationen.

Der folgende Code komprimiert den LOH sofort:

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

Weitere <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> Informationen zum Verdichten des LOH finden Sie unter .

In Containern mit .NET Core 3.0 und höher wird der LOH automatisch komprimiert.

Die folgende API, die dieses Verhalten veranschaulicht:

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

Das folgende Diagramm zeigt das `/api/loh/84975` Speicherprofil des Aufrufs des Endpunkts unter maximaler Auslastung:

![vorhergehendes Diagramm](memory/_static/loh1.png)

Das folgende Diagramm zeigt das `/api/loh/84976` Speicherprofil des Aufrufs des Endpunkts und weist *nur ein weiteres Byte*zu:

![vorhergehendes Diagramm](memory/_static/loh2.png)

Hinweis: `byte[]` Die Struktur verfügt über Overhead-Bytes. Aus diesem Grund lösen 84.976 Bytes die 85.000-Grenze aus.

Vergleich der beiden vorhergehenden Diagramme:

* Der Arbeitssatz ist für beide Szenarien ähnlich, etwa 450 MB.
* Die unter LOH-Anforderungen (84.975 Bytes) zeigt hauptsächlich Auflistungen der Generation 0 an.
* Die über LOH-Anforderungen generieren Sammlungen der konstanten Generation 2. Kollektionen der Generation 2 sind teuer. Mehr CPU ist erforderlich und der Durchsatz sinkt um fast 50%.

Temporäre große Objekte sind besonders problematisch, da sie gen2 GCs verursachen.

Für maximale Leistung sollte die Verwendung großer Objekte minimiert werden. Teilen Sie nach Möglichkeit große Objekte auf. Beispielsweise teilt [die Response Caching-Middleware](xref:performance/caching/response) in ASP.NET Core die Cacheeinträge in Blöcke mit weniger als 85.000 Bytes auf.

Die folgenden Links zeigen den ASP.NET Core-Ansatz, um Objekte unter dem LOH-Limit zu halten:

* [ResponseCaching/Streams/StreamUtilities.cs](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [ResponseCaching/MemoryResponseCache.cs](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

Weitere Informationen finden Sie unter

* [Großer Objektheap aufgedeckt](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Großer Objektheap](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a>HttpClient

Eine falsche <xref:System.Net.Http.HttpClient> Verwendung kann zu einem Ressourcenleck führen. Systemressourcen, z. B. Datenbankverbindungen, Sockets, Dateihandles usw.:

* Sind knapper als Speicher.
* Sind problematischer, wenn sie durchgesickert sind als Speicher.

Erfahrene .NET-Entwickler <xref:System.IDisposable.Dispose*> wissen, Objekte <xref:System.IDisposable>aufzurufen, die implementieren. Das Nichtveräußern `IDisposable` von Objekten, die implementiert werden, führt in der Regel zu undichtem Speicher oder ausgelaufenen Systemressourcen.

`HttpClient`implementiert `IDisposable`, sollte jedoch **nicht** bei jedem Aufruf verworfen werden. Vielmehr `HttpClient` sollte wiederverwendet werden.

Der folgende Endpunkt erstellt und `HttpClient` verfügt über eine neue Instanz für jede Anforderung:

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

Unter Laden werden die folgenden Fehlermeldungen protokolliert:

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

Auch wenn `HttpClient` die Instanzen verworfen werden, dauert es einige Zeit, bis die eigentliche Netzwerkverbindung vom Betriebssystem freigegeben wird. Durch die kontinuierliche Erstellung neuer Verbindungen kommt es zur Erschöpfung der _Ports._ Jede Clientverbindung erfordert einen eigenen Clientport.

Eine Möglichkeit, die Erschöpfung des `HttpClient` Ports zu verhindern, besteht darin, dieselbe Instanz wiederzuverwenden:

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

Die `HttpClient` Instanz wird freigegeben, wenn die App beendet wird. Dieses Beispiel zeigt, dass nicht jede Einwegressource nach jeder Verwendung verworfen werden sollte.

Im Folgenden finden Sie eine bessere Möglichkeit, die Lebensdauer einer `HttpClient` Instanz zu verarbeiten:

* [HttpClient und die Verwaltung der Lebensdauer](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [HTTPClient-Factory-Blog](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a>Objektpooling

Das vorherige Beispiel `HttpClient` zeigte, wie die Instanz statisch gemacht und von allen Anforderungen wiederverwendet werden kann. Die Wiederverwendung verhindert, dass keine Ressourcen mehr vorhanden sind.

Objektpooling:

* Verwendet das Wiederverwendungsmuster.
* Ist für Objekte konzipiert, die teuer zu erstellen sind.

Ein Pool ist eine Sammlung von vorinitialisierten Objekten, die reserviert und über Threads hinweg freigegeben werden können. Pools können Allokationsregeln wie Grenzwerte, vordefinierte Größen oder Wachstumsraten definieren.

Das NuGet-Paket [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) enthält Klassen, die beim Verwalten solcher Pools helfen.

Der folgende API-Endpunkt instanziiert einen `byte` Puffer, der bei jeder Anforderung mit Zufallszahlen gefüllt ist:

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

Die folgende Diagrammanzeige, die die vorherige API mit moderater Auslastung aufruft:

![vorhergehendes Diagramm](memory/_static/array.png)

Im vorherigen Diagramm werden Auflistungen der Generierung 0 ungefähr einmal pro Sekunde angezeigt.

Der vorangehende Code kann `byte` optimiert werden, indem der Puffer mithilfe von [ArrayPool\<T>](xref:System.Buffers.ArrayPool`1). Eine statische Instanz wird über Anforderungen hinweg wiederverwendet.

Bei diesem Ansatz unterscheidet sich, dass ein gepooltes Objekt von der API zurückgegeben wird. Das bedeutet:

* Das Objekt ist nicht mehr in der Hand, sobald Sie von der Methode zurückkehren.
* Sie können das Objekt nicht freigeben.

So richten Sie die Entsorgung des Objekts ein:

* Kapseln Sie das gepoolte Array in einem Einwegobjekt.
* Registrieren Sie das gepoolte Objekt mit [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).

`RegisterForDispose`wird das Zielobjekt aufrufen, `Dispose`sodass es nur freigegeben wird, wenn die HTTP-Anforderung abgeschlossen ist.

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

Wenn Sie dieselbe Last wie die nicht gepoolte Version anwenden, wird das folgende Diagramm angezeigt:

![vorhergehendes Diagramm](memory/_static/pooledarray.png)

Der Hauptunterschied sind zugeordnete Bytes und infolgedessen viel weniger Generation 0-Auflistungen.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Automatische Speicherbereinigung](/dotnet/standard/garbage-collection/)
* [Grundlegendes zu verschiedenen GC-Modi mit Parallelitäts-Visualisierung](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [Großer Objektheap aufgedeckt](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Großer Objektheap](/dotnet/standard/garbage-collection/large-object-heap)

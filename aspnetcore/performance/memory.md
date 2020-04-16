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
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a><span data-ttu-id="71e3f-103">Speicherverwaltung und Garbage Collection (GC) in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="71e3f-103">Memory management and garbage collection (GC) in ASP.NET Core</span></span>

<span data-ttu-id="71e3f-104">Von [Sébastien Ros](https://github.com/sebastienros) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="71e3f-104">By [Sébastien Ros](https://github.com/sebastienros) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="71e3f-105">Die Speicherverwaltung ist komplex, selbst in einem verwalteten Framework wie .NET.</span><span class="sxs-lookup"><span data-stu-id="71e3f-105">Memory management is complex, even in a managed framework like .NET.</span></span> <span data-ttu-id="71e3f-106">Das Analysieren und Verstehen von Speicherproblemen kann eine Herausforderung darstellen.</span><span class="sxs-lookup"><span data-stu-id="71e3f-106">Analyzing and understanding memory issues can be challenging.</span></span> <span data-ttu-id="71e3f-107">Dieser Artikel:</span><span class="sxs-lookup"><span data-stu-id="71e3f-107">This article:</span></span>

* <span data-ttu-id="71e3f-108">Wurde durch viele *Speicherlecks* und *GC nicht funktionierende* Probleme motiviert.</span><span class="sxs-lookup"><span data-stu-id="71e3f-108">Was motivated by many *memory leak* and *GC not working* issues.</span></span> <span data-ttu-id="71e3f-109">Die meisten dieser Probleme wurden dadurch verursacht, dass sie nicht verstanden haben, wie der Speicherverbrauch in .NET Core funktioniert, oder nicht, wie er gemessen wird.</span><span class="sxs-lookup"><span data-stu-id="71e3f-109">Most of these issues were caused by not understanding how memory consumption works in .NET Core, or not understanding how it's measured.</span></span>
* <span data-ttu-id="71e3f-110">Veranschaulicht die problematische Speichernutzung und schlägt alternative Ansätze vor.</span><span class="sxs-lookup"><span data-stu-id="71e3f-110">Demonstrates problematic memory use, and suggests alternative approaches.</span></span>

## <a name="how-garbage-collection-gc-works-in-net-core"></a><span data-ttu-id="71e3f-111">Funktionsweise der Garbage Collection (GC) in .NET Core</span><span class="sxs-lookup"><span data-stu-id="71e3f-111">How garbage collection (GC) works in .NET Core</span></span>

<span data-ttu-id="71e3f-112">Der GC weist Heapsegmente zu, bei denen jedes Segment ein zusammenhängender Speicherbereich ist.</span><span class="sxs-lookup"><span data-stu-id="71e3f-112">The GC allocates heap segments where each segment is a contiguous range of memory.</span></span> <span data-ttu-id="71e3f-113">Objekte, die im Heap platziert werden, werden in eine von 3 Generationen kategorisiert: 0, 1 oder 2.</span><span class="sxs-lookup"><span data-stu-id="71e3f-113">Objects placed in the heap are categorized into one of 3 generations: 0, 1, or 2.</span></span> <span data-ttu-id="71e3f-114">Die Generierung bestimmt die Häufigkeit, mit der der GC versucht, Speicher für verwaltete Objekte freizugeben, auf die nicht mehr von der App verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="71e3f-114">The generation determines the frequency the GC attempts to release memory on managed objects that are no longer referenced by the app.</span></span> <span data-ttu-id="71e3f-115">Niedrigere nummerierte Generationen sind häufiger GC'd.</span><span class="sxs-lookup"><span data-stu-id="71e3f-115">Lower numbered generations are GC'd more frequently.</span></span>

<span data-ttu-id="71e3f-116">Objekte werden basierend auf ihrer Lebensdauer von einer Generation in eine andere verschoben.</span><span class="sxs-lookup"><span data-stu-id="71e3f-116">Objects are moved from one generation to another based on their lifetime.</span></span> <span data-ttu-id="71e3f-117">Da Objekte länger leben, werden sie in eine höhere Generation verschoben.</span><span class="sxs-lookup"><span data-stu-id="71e3f-117">As objects live longer, they are moved into a higher generation.</span></span> <span data-ttu-id="71e3f-118">Wie bereits erwähnt, sind höhere Generationen weniger oft GC'd.</span><span class="sxs-lookup"><span data-stu-id="71e3f-118">As mentioned previously, higher generations are GC'd less often.</span></span> <span data-ttu-id="71e3f-119">Kurzfristig lebende Objekte bleiben immer in Generation 0.</span><span class="sxs-lookup"><span data-stu-id="71e3f-119">Short term lived objects always remain in generation 0.</span></span> <span data-ttu-id="71e3f-120">Beispielsweise sind Objekte, auf die während der Lebensdauer einer Webanforderung verwiesen wird, kurzlebig.</span><span class="sxs-lookup"><span data-stu-id="71e3f-120">For example, objects that are referenced during the life of a web request are short lived.</span></span> <span data-ttu-id="71e3f-121">[Singletons](xref:fundamentals/dependency-injection#service-lifetimes) auf Anwendungsebene werden in der Regel in die Generation 2 migriert.</span><span class="sxs-lookup"><span data-stu-id="71e3f-121">Application level [singletons](xref:fundamentals/dependency-injection#service-lifetimes) generally migrate to generation 2.</span></span>

<span data-ttu-id="71e3f-122">Wenn eine ASP.NET Core-App gestartet wird, wird der GC:</span><span class="sxs-lookup"><span data-stu-id="71e3f-122">When an ASP.NET Core app starts, the GC:</span></span>

* <span data-ttu-id="71e3f-123">Reserviert etwas Speicher für die anfänglichen Heapsegmente.</span><span class="sxs-lookup"><span data-stu-id="71e3f-123">Reserves some memory for the initial heap segments.</span></span>
* <span data-ttu-id="71e3f-124">Überträgt einen kleinen Teil des Arbeitsspeichers, wenn die Laufzeit geladen wird.</span><span class="sxs-lookup"><span data-stu-id="71e3f-124">Commits a small portion of memory when the runtime is loaded.</span></span>

<span data-ttu-id="71e3f-125">Die vorherigen Speicherzuweisungen werden aus Leistungsgründen durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="71e3f-125">The preceding memory allocations are done for performance reasons.</span></span> <span data-ttu-id="71e3f-126">Der Leistungsvorteil ergibt sich aus Heapsegmenten im zusammenhängenden Speicher.</span><span class="sxs-lookup"><span data-stu-id="71e3f-126">The performance benefit comes from heap segments in contiguous memory.</span></span>

### <a name="call-gccollect"></a><span data-ttu-id="71e3f-127">Rufen Sie GC auf. Sammeln</span><span class="sxs-lookup"><span data-stu-id="71e3f-127">Call GC.Collect</span></span>

<span data-ttu-id="71e3f-128">Aufruf von [GC. Sammeln Sie](xref:System.GC.Collect*) explizit:</span><span class="sxs-lookup"><span data-stu-id="71e3f-128">Calling [GC.Collect](xref:System.GC.Collect*) explicitly:</span></span>

* <span data-ttu-id="71e3f-129">Sollte **nicht** durch produktion ASP.NET Core-Apps durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="71e3f-129">Should **not** be done by production ASP.NET Core apps.</span></span>
* <span data-ttu-id="71e3f-130">Ist nützlich, wenn Speicherverluste untersucht werden.</span><span class="sxs-lookup"><span data-stu-id="71e3f-130">Is useful when investigating memory leaks.</span></span>
* <span data-ttu-id="71e3f-131">Bei der Untersuchung überprüft, ob der GC alle baumelnden Objekte aus dem Speicher entfernt hat, damit der Speicher gemessen werden kann.</span><span class="sxs-lookup"><span data-stu-id="71e3f-131">When investigating, verifies the GC has removed all dangling objects from memory so memory can be measured.</span></span>

## <a name="analyzing-the-memory-usage-of-an-app"></a><span data-ttu-id="71e3f-132">Analysieren der Speichernutzung einer App</span><span class="sxs-lookup"><span data-stu-id="71e3f-132">Analyzing the memory usage of an app</span></span>

<span data-ttu-id="71e3f-133">Spezielle Tools können bei der Analyse der Speichernutzung helfen:</span><span class="sxs-lookup"><span data-stu-id="71e3f-133">Dedicated tools can help analyzing memory usage:</span></span>

- <span data-ttu-id="71e3f-134">Zählen von Objektreferenzen</span><span class="sxs-lookup"><span data-stu-id="71e3f-134">Counting object references</span></span>
- <span data-ttu-id="71e3f-135">Messen, wie viel Einfluss der GC auf die CPU-Auslastung hat</span><span class="sxs-lookup"><span data-stu-id="71e3f-135">Measuring how much impact the GC has on CPU usage</span></span>
- <span data-ttu-id="71e3f-136">Messen des für jede Generation genutzten Speichers</span><span class="sxs-lookup"><span data-stu-id="71e3f-136">Measuring memory space used for each generation</span></span>

<span data-ttu-id="71e3f-137">Verwenden Sie die folgenden Tools, um die Speichernutzung zu analysieren:</span><span class="sxs-lookup"><span data-stu-id="71e3f-137">Use the following tools to analyze memory usage:</span></span>

* <span data-ttu-id="71e3f-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Kann auf Produktionsmaschinen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="71e3f-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Can be  used on production machines.</span></span>
* [<span data-ttu-id="71e3f-139">Analysieren der Arbeitsspeicherauslastung ohne Visual Studio-Debugger</span><span class="sxs-lookup"><span data-stu-id="71e3f-139">Analyze memory usage without the Visual Studio debugger</span></span>](/visualstudio/profiling/memory-usage-without-debugging2)
* [<span data-ttu-id="71e3f-140">Profilerstellung zur Arbeitsspeicherverwendung in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="71e3f-140">Profile memory usage in Visual Studio</span></span>](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a><span data-ttu-id="71e3f-141">Erkennen von Speicherproblemen</span><span class="sxs-lookup"><span data-stu-id="71e3f-141">Detecting memory issues</span></span>

<span data-ttu-id="71e3f-142">Der Task-Manager kann verwendet werden, um eine Vorstellung davon zu erhalten, wie viel Arbeitsspeicher eine ASP.NET App verwendet.</span><span class="sxs-lookup"><span data-stu-id="71e3f-142">Task Manager can be used to get an idea of how much memory an ASP.NET app is using.</span></span> <span data-ttu-id="71e3f-143">Der Speicherwert des Task-Managers:</span><span class="sxs-lookup"><span data-stu-id="71e3f-143">The Task Manager memory value:</span></span>

* <span data-ttu-id="71e3f-144">Stellt die Speichermenge dar, die vom ASP.NET Prozess verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="71e3f-144">Represents the amount of memory that is used by the ASP.NET process.</span></span>
* <span data-ttu-id="71e3f-145">Enthält die lebenden Objekte der App und andere Speicherverbraucher, z. B. die Verwendung von systemeigenem Speicher.</span><span class="sxs-lookup"><span data-stu-id="71e3f-145">Includes the app's living objects and other memory consumers such as native memory usage.</span></span>

<span data-ttu-id="71e3f-146">Wenn der Speicherwert des Task-Managers auf unbestimmte Zeit steigt und nie abgeflacht wird, weist die App einen Speicherverlust auf.</span><span class="sxs-lookup"><span data-stu-id="71e3f-146">If the Task Manager memory value increases indefinitely and never flattens out, the app has a memory leak.</span></span> <span data-ttu-id="71e3f-147">In den folgenden Abschnitten werden mehrere Speichernutzungsmuster veranschaulicht und erläutert.</span><span class="sxs-lookup"><span data-stu-id="71e3f-147">The following sections demonstrate and explain several memory usage patterns.</span></span>

## <a name="sample-display-memory-usage-app"></a><span data-ttu-id="71e3f-148">BeispielanzeigeSpeichernutzungs-App</span><span class="sxs-lookup"><span data-stu-id="71e3f-148">Sample display memory usage app</span></span>

<span data-ttu-id="71e3f-149">Die [MemoryLeak-Beispiel-App](https://github.com/sebastienros/memoryleak) ist auf GitHub verfügbar.</span><span class="sxs-lookup"><span data-stu-id="71e3f-149">The [MemoryLeak sample app](https://github.com/sebastienros/memoryleak) is available on GitHub.</span></span> <span data-ttu-id="71e3f-150">Die MemoryLeak App:</span><span class="sxs-lookup"><span data-stu-id="71e3f-150">The MemoryLeak app:</span></span>

* <span data-ttu-id="71e3f-151">Enthält einen Diagnosecontroller, der Echtzeitspeicher und GC-Daten für die App sammelt.</span><span class="sxs-lookup"><span data-stu-id="71e3f-151">Includes a diagnostic controller that gathers real-time memory and GC data for the app.</span></span>
* <span data-ttu-id="71e3f-152">Verfügt über eine Indexseite, auf der die Speicher- und GC-Daten angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="71e3f-152">Has an Index page that displays the memory and GC data.</span></span> <span data-ttu-id="71e3f-153">Die Indexseite wird jede Sekunde aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="71e3f-153">The Index page is refreshed every second.</span></span>
* <span data-ttu-id="71e3f-154">Enthält einen API-Controller, der verschiedene Speicherlademuster bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="71e3f-154">Contains an API controller that provides various memory load patterns.</span></span>
* <span data-ttu-id="71e3f-155">Es ist kein unterstütztes Tool, kann jedoch verwendet werden, um Speichernutzungsmuster von ASP.NET Core-Apps anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="71e3f-155">Is not a supported tool, however, it can be used to display memory usage patterns of ASP.NET Core apps.</span></span>

<span data-ttu-id="71e3f-156">Führen Sie MemoryLeak aus.</span><span class="sxs-lookup"><span data-stu-id="71e3f-156">Run MemoryLeak.</span></span> <span data-ttu-id="71e3f-157">Der zugewiesene Speicher nimmt langsam zu, bis ein GC auftritt.</span><span class="sxs-lookup"><span data-stu-id="71e3f-157">Allocated memory slowly increases until a GC occurs.</span></span> <span data-ttu-id="71e3f-158">Der Arbeitsspeicher wird erhöht, da das Tool benutzerdefinierte Objekte zum Erfassen von Daten zuweist.</span><span class="sxs-lookup"><span data-stu-id="71e3f-158">Memory increases because the tool allocates custom object to capture data.</span></span> <span data-ttu-id="71e3f-159">Die folgende Abbildung zeigt die MemoryLeak Index-Seite, wenn ein Gen 0 GC auftritt.</span><span class="sxs-lookup"><span data-stu-id="71e3f-159">The following image shows the MemoryLeak Index page when a Gen 0 GC occurs.</span></span> <span data-ttu-id="71e3f-160">Das Diagramm zeigt 0 RPS (Requests per Second), da keine API-Endpunkte vom API-Controller aufgerufen wurden.</span><span class="sxs-lookup"><span data-stu-id="71e3f-160">The chart shows 0 RPS (Requests per second) because no API endpoints from the API controller have been called.</span></span>

![vorhergehendes Diagramm](memory/_static/0RPS.png)

<span data-ttu-id="71e3f-162">Das Diagramm zeigt zwei Werte für die Speichernutzung an:</span><span class="sxs-lookup"><span data-stu-id="71e3f-162">The chart displays two values for the memory usage:</span></span>

- <span data-ttu-id="71e3f-163">Zugeordnet: die Menge an Speicher, die von verwalteten Objekten belegt wird</span><span class="sxs-lookup"><span data-stu-id="71e3f-163">Allocated: the amount of memory occupied by managed objects</span></span>
- <span data-ttu-id="71e3f-164">[Arbeitssatz](/windows/win32/memory/working-set): Der Satz von Seiten im virtuellen Adressraum des Prozesses, die sich derzeit im physischen Speicher befinden.</span><span class="sxs-lookup"><span data-stu-id="71e3f-164">[Working set](/windows/win32/memory/working-set): The set of pages in the virtual address space of the process that are currently resident in physical memory.</span></span> <span data-ttu-id="71e3f-165">Der angezeigte Arbeitssatz ist derselbe Wert, den Task-Manager anzeigt.</span><span class="sxs-lookup"><span data-stu-id="71e3f-165">The working set shown is the same value Task Manager displays.</span></span>

### <a name="transient-objects"></a><span data-ttu-id="71e3f-166">Transiente Objekte</span><span class="sxs-lookup"><span data-stu-id="71e3f-166">Transient objects</span></span>

<span data-ttu-id="71e3f-167">Die folgende API erstellt eine 10-KB-Zeichenfolgeninstanz und gibt sie an den Client zurück.</span><span class="sxs-lookup"><span data-stu-id="71e3f-167">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="71e3f-168">Bei jeder Anforderung wird ein neues Objekt im Speicher zugewiesen und in die Antwort geschrieben.</span><span class="sxs-lookup"><span data-stu-id="71e3f-168">On each request, a new object is allocated in memory and written to the response.</span></span> <span data-ttu-id="71e3f-169">Zeichenfolgen werden als UTF-16-Zeichen in .NET gespeichert, sodass jedes Zeichen 2 Bytes im Arbeitsspeicher benötigt.</span><span class="sxs-lookup"><span data-stu-id="71e3f-169">Strings are stored as UTF-16 characters in .NET so each character takes 2 bytes in memory.</span></span>

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

<span data-ttu-id="71e3f-170">Das folgende Diagramm wird mit einer relativ geringen Last generiert, um zu zeigen, wie sich die Speicherzuweisungen auf den GC auswirken.</span><span class="sxs-lookup"><span data-stu-id="71e3f-170">The following graph is generated with a relatively small load in to show how memory allocations are impacted by the GC.</span></span>

![vorhergehendes Diagramm](memory/_static/bigstring.png)

<span data-ttu-id="71e3f-172">Das obige Diagramm zeigt:</span><span class="sxs-lookup"><span data-stu-id="71e3f-172">The preceding chart shows:</span></span>

* <span data-ttu-id="71e3f-173">4K RPS (Anforderungen pro Sekunde).</span><span class="sxs-lookup"><span data-stu-id="71e3f-173">4K RPS (Requests per second).</span></span>
* <span data-ttu-id="71e3f-174">Generation 0 GC-Sammlungen treten etwa alle zwei Sekunden auf.</span><span class="sxs-lookup"><span data-stu-id="71e3f-174">Generation 0 GC collections occur about every two seconds.</span></span>
* <span data-ttu-id="71e3f-175">Der Arbeitssatz ist konstant bei ca. 500 MB.</span><span class="sxs-lookup"><span data-stu-id="71e3f-175">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="71e3f-176">Die CPU beträgt 12%.</span><span class="sxs-lookup"><span data-stu-id="71e3f-176">CPU is 12%.</span></span>
* <span data-ttu-id="71e3f-177">Der Speicherverbrauch und die Freigabe (über GC) sind stabil.</span><span class="sxs-lookup"><span data-stu-id="71e3f-177">The memory consumption and release (through GC) is stable.</span></span>

<span data-ttu-id="71e3f-178">Das folgende Diagramm wird mit dem maximalen Durchsatz aufgenommen, der von der Maschine verarbeitet werden kann.</span><span class="sxs-lookup"><span data-stu-id="71e3f-178">The following chart is taken at the max throughput that can be handled by the machine.</span></span>

![vorhergehendes Diagramm](memory/_static/bigstring2.png)

<span data-ttu-id="71e3f-180">Das obige Diagramm zeigt:</span><span class="sxs-lookup"><span data-stu-id="71e3f-180">The preceding chart shows:</span></span>

* <span data-ttu-id="71e3f-181">22K RPS</span><span class="sxs-lookup"><span data-stu-id="71e3f-181">22K RPS</span></span>
* <span data-ttu-id="71e3f-182">GC-Sammlungen der Generation 0 treten mehrmals pro Sekunde auf.</span><span class="sxs-lookup"><span data-stu-id="71e3f-182">Generation 0 GC collections occur several times per second.</span></span>
* <span data-ttu-id="71e3f-183">Sammlungen der Generation 1 werden ausgelöst, weil die App deutlich mehr Arbeitsspeicher pro Sekunde zugewiesen hat.</span><span class="sxs-lookup"><span data-stu-id="71e3f-183">Generation 1 collections are triggered because the app allocated significantly more memory per second.</span></span>
* <span data-ttu-id="71e3f-184">Der Arbeitssatz ist konstant bei ca. 500 MB.</span><span class="sxs-lookup"><span data-stu-id="71e3f-184">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="71e3f-185">Die CPU beträgt 33 %.</span><span class="sxs-lookup"><span data-stu-id="71e3f-185">CPU is 33%.</span></span>
* <span data-ttu-id="71e3f-186">Der Speicherverbrauch und die Freigabe (über GC) sind stabil.</span><span class="sxs-lookup"><span data-stu-id="71e3f-186">The memory consumption and release (through GC) is stable.</span></span>
* <span data-ttu-id="71e3f-187">Die CPU (33%) nicht überausgenutzt ist, daher kann die Garbage Collection mit einer hohen Anzahl von Zuweisungen Schritt halten.</span><span class="sxs-lookup"><span data-stu-id="71e3f-187">The CPU (33%) is not over-utilized, therefore the garbage collection can keep up with a high number of allocations.</span></span>

### <a name="workstation-gc-vs-server-gc"></a><span data-ttu-id="71e3f-188">Workstation GC vs. Server GC</span><span class="sxs-lookup"><span data-stu-id="71e3f-188">Workstation GC vs. Server GC</span></span>

<span data-ttu-id="71e3f-189">Der .NET Garbage Collector verfügt über zwei verschiedene Modi:</span><span class="sxs-lookup"><span data-stu-id="71e3f-189">The .NET Garbage Collector has two different modes:</span></span>

* <span data-ttu-id="71e3f-190">**Workstation GC**: Optimiert für den Desktop.</span><span class="sxs-lookup"><span data-stu-id="71e3f-190">**Workstation GC**: Optimized for the desktop.</span></span>
* <span data-ttu-id="71e3f-191">**Server GC**.</span><span class="sxs-lookup"><span data-stu-id="71e3f-191">**Server GC**.</span></span> <span data-ttu-id="71e3f-192">Der Standard-GC für ASP.NET Core-Apps.</span><span class="sxs-lookup"><span data-stu-id="71e3f-192">The default GC for ASP.NET Core apps.</span></span> <span data-ttu-id="71e3f-193">Optimiert für den Server.</span><span class="sxs-lookup"><span data-stu-id="71e3f-193">Optimized for the server.</span></span>

<span data-ttu-id="71e3f-194">Der GC-Modus kann explizit in der Projektdatei oder in der *Datei runtimeconfig.json* der veröffentlichten App festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="71e3f-194">The GC mode can be set explicitly in the project file or in the *runtimeconfig.json* file of the published app.</span></span> <span data-ttu-id="71e3f-195">Das folgende Markup `ServerGarbageCollection` zeigt die Einstellung in der Projektdatei:</span><span class="sxs-lookup"><span data-stu-id="71e3f-195">The following markup shows setting `ServerGarbageCollection` in the project file:</span></span>

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

<span data-ttu-id="71e3f-196">Wenn `ServerGarbageCollection` Sie die Projektdatei ändern, muss die App neu erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="71e3f-196">Changing `ServerGarbageCollection` in the project file requires the app to be rebuilt.</span></span>

<span data-ttu-id="71e3f-197">**Hinweis:** Die Servergarbage Collection ist auf Computern mit einem einzigen Kern **nicht** verfügbar.</span><span class="sxs-lookup"><span data-stu-id="71e3f-197">**Note:** Server garbage collection is **not** available on machines with a single core.</span></span> <span data-ttu-id="71e3f-198">Weitere Informationen finden Sie unter <xref:System.Runtime.GCSettings.IsServerGC>.</span><span class="sxs-lookup"><span data-stu-id="71e3f-198">For more information, see <xref:System.Runtime.GCSettings.IsServerGC>.</span></span>

<span data-ttu-id="71e3f-199">Die folgende Abbildung zeigt das Speicherprofil unter einem 5K RPS mit dem Workstation GC.</span><span class="sxs-lookup"><span data-stu-id="71e3f-199">The following image shows the memory profile under a 5K RPS using the Workstation GC.</span></span>

![vorhergehendes Diagramm](memory/_static/workstation.png)

<span data-ttu-id="71e3f-201">Die Unterschiede zwischen diesem Diagramm und der Serverversion sind erheblich:</span><span class="sxs-lookup"><span data-stu-id="71e3f-201">The differences between this chart and the server version are significant:</span></span>

- <span data-ttu-id="71e3f-202">Der Arbeitssatz sinkt von 500 MB auf 70 MB.</span><span class="sxs-lookup"><span data-stu-id="71e3f-202">The working set drops from 500 MB to 70 MB.</span></span>
- <span data-ttu-id="71e3f-203">Der GC erstellt Die Generation 0-Sammlungen mehrmals pro Sekunde statt alle zwei Sekunden.</span><span class="sxs-lookup"><span data-stu-id="71e3f-203">The GC does generation 0 collections multiple times per second instead of every two seconds.</span></span>
- <span data-ttu-id="71e3f-204">GC sinkt von 300 MB auf 10 MB.</span><span class="sxs-lookup"><span data-stu-id="71e3f-204">GC drops from 300 MB to 10 MB.</span></span>

<span data-ttu-id="71e3f-205">In einer typischen Webserverumgebung ist die CPU-Auslastung wichtiger als Speicher, daher ist der Server GC besser.</span><span class="sxs-lookup"><span data-stu-id="71e3f-205">On a typical web server environment, CPU usage is more important than memory, therefore the Server GC is better.</span></span> <span data-ttu-id="71e3f-206">Wenn die Speicherauslastung hoch und die CPU-Auslastung relativ gering ist, ist der Workstation-GC möglicherweise leistungsstärker.</span><span class="sxs-lookup"><span data-stu-id="71e3f-206">If memory utilization is high and CPU usage is relatively low, the Workstation GC might be more performant.</span></span> <span data-ttu-id="71e3f-207">Zum Beispiel, hohe Dichte Hosting mehrere Web-Apps, wo Speicher knapp ist.</span><span class="sxs-lookup"><span data-stu-id="71e3f-207">For example, high density hosting several web apps where memory is scarce.</span></span>

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a><span data-ttu-id="71e3f-208">GC mit Docker und kleinen Containern</span><span class="sxs-lookup"><span data-stu-id="71e3f-208">GC using Docker and small containers</span></span>

<span data-ttu-id="71e3f-209">Wenn mehrere containerisierte Apps auf einem Computer ausgeführt werden, ist Workstation GC möglicherweise vorformanter als Server GC.</span><span class="sxs-lookup"><span data-stu-id="71e3f-209">When multiple containerized apps are running on one machine, Workstation GC might be more preformant than Server GC.</span></span> <span data-ttu-id="71e3f-210">Weitere Informationen finden Sie unter [Ausführen mit Server GC in einem kleinen Container](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) und Ausführen mit Server GC in einem kleinen [Containerszenario Teil 1 – Hard Limit für den GC-Heap](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).</span><span class="sxs-lookup"><span data-stu-id="71e3f-210">For more information, see [Running with Server GC in a Small Container](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) and [Running with Server GC in a Small Container Scenario Part 1 – Hard Limit for the GC Heap](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).</span></span>

### <a name="persistent-object-references"></a><span data-ttu-id="71e3f-211">Persistente Objektverweise</span><span class="sxs-lookup"><span data-stu-id="71e3f-211">Persistent object references</span></span>

<span data-ttu-id="71e3f-212">Der GC kann keine Objekte freisetzen, auf die verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="71e3f-212">The GC cannot free objects that are referenced.</span></span> <span data-ttu-id="71e3f-213">Objekte, auf die verwiesen wird, aber nicht mehr benötigt wird, führen zu einem Speicherverlust.</span><span class="sxs-lookup"><span data-stu-id="71e3f-213">Objects that are referenced but no longer needed result in a memory leak.</span></span> <span data-ttu-id="71e3f-214">Wenn die App häufig Objekte zuweist und sie nicht mehr freigibt, nachdem sie nicht mehr benötigt werden, nimmt die Speicherauslastung mit der Zeit zu.</span><span class="sxs-lookup"><span data-stu-id="71e3f-214">If the app frequently allocates objects and fails to free them after they are no longer needed, memory usage will increase over time.</span></span>

<span data-ttu-id="71e3f-215">Die folgende API erstellt eine 10-KB-Zeichenfolgeninstanz und gibt sie an den Client zurück.</span><span class="sxs-lookup"><span data-stu-id="71e3f-215">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="71e3f-216">Der Unterschied zum vorherigen Beispiel besteht darin, dass auf diese Instanz von einem statischen Member verwiesen wird, d. h., sie ist nie für die Auflistung verfügbar.</span><span class="sxs-lookup"><span data-stu-id="71e3f-216">The difference with the previous example is that this instance is referenced by a static member, which means it's never available for collection.</span></span>

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

<span data-ttu-id="71e3f-217">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="71e3f-217">The preceding code:</span></span>

* <span data-ttu-id="71e3f-218">Ein Beispiel für ein typisches Speicherleck.</span><span class="sxs-lookup"><span data-stu-id="71e3f-218">Is an example of a typical memory leak.</span></span>
* <span data-ttu-id="71e3f-219">Bei häufigen Aufrufen erhöht sich der App-Speicher, bis der Prozess mit einer `OutOfMemory` Ausnahme abstürzt.</span><span class="sxs-lookup"><span data-stu-id="71e3f-219">With frequent calls, causes app memory to increases until the process crashes with an `OutOfMemory` exception.</span></span>

![vorhergehendes Diagramm](memory/_static/eternal.png)

<span data-ttu-id="71e3f-221">Im obigen Bild:</span><span class="sxs-lookup"><span data-stu-id="71e3f-221">In the preceding image:</span></span>

* <span data-ttu-id="71e3f-222">Das Auslastungstesten des `/api/staticstring` Endpunkts führt zu einer linearen Erhöhung des Arbeitsspeichers.</span><span class="sxs-lookup"><span data-stu-id="71e3f-222">Load testing the `/api/staticstring` endpoint causes a linear increase in memory.</span></span>
* <span data-ttu-id="71e3f-223">Der GC versucht, Speicher freizugeben, wenn der Speicherdruck wächst, indem er eine Generation 2-Sammlung aufruft.</span><span class="sxs-lookup"><span data-stu-id="71e3f-223">The GC tries to free memory as the memory pressure grows, by calling a generation 2 collection.</span></span>
* <span data-ttu-id="71e3f-224">Der GC kann den durchgesickerten Speicher nicht freimachen.</span><span class="sxs-lookup"><span data-stu-id="71e3f-224">The GC cannot free the leaked memory.</span></span> <span data-ttu-id="71e3f-225">Zugeordnete und Arbeitsset erhöhen sich mit der Zeit.</span><span class="sxs-lookup"><span data-stu-id="71e3f-225">Allocated and working set increase with time.</span></span>

<span data-ttu-id="71e3f-226">In einigen Szenarien, z. B. dem Zwischenspeichern, müssen Objektverweise so lange gespeichert werden, bis sie vom Speicherdruck freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="71e3f-226">Some scenarios, such as caching, require object references to be held until memory pressure forces them to be released.</span></span> <span data-ttu-id="71e3f-227">Die <xref:System.WeakReference> Klasse kann für diesen Typ von Zwischenspeicherungscode verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="71e3f-227">The <xref:System.WeakReference> class can be used for this type of caching code.</span></span> <span data-ttu-id="71e3f-228">Ein `WeakReference` Objekt wird unter Speicherdruck gesammelt.</span><span class="sxs-lookup"><span data-stu-id="71e3f-228">A `WeakReference` object is collected under memory pressures.</span></span> <span data-ttu-id="71e3f-229">Die Standardimplementierung <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> `WeakReference`von uses .</span><span class="sxs-lookup"><span data-stu-id="71e3f-229">The default implementation of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> uses `WeakReference`.</span></span>

### <a name="native-memory"></a><span data-ttu-id="71e3f-230">Natives Gedächtnis</span><span class="sxs-lookup"><span data-stu-id="71e3f-230">Native memory</span></span>

<span data-ttu-id="71e3f-231">Einige .NET Core-Objekte basieren auf systemeigenem Speicher.</span><span class="sxs-lookup"><span data-stu-id="71e3f-231">Some .NET Core objects rely on native memory.</span></span> <span data-ttu-id="71e3f-232">Nativer Speicher kann vom GC **nicht** erfasst werden.</span><span class="sxs-lookup"><span data-stu-id="71e3f-232">Native memory can **not** be collected by the GC.</span></span> <span data-ttu-id="71e3f-233">Das .NET-Objekt, das systemeigenen Speicher verwendet, muss es mithilfe von systemeigenem Code freisetzen.</span><span class="sxs-lookup"><span data-stu-id="71e3f-233">The .NET object using native memory must free it using native code.</span></span>

<span data-ttu-id="71e3f-234">.NET stellt <xref:System.IDisposable> die Schnittstelle bereit, mit der Entwickler systemeigenen Speicher freigeben können.</span><span class="sxs-lookup"><span data-stu-id="71e3f-234">.NET provides the <xref:System.IDisposable> interface to let developers release native memory.</span></span> <span data-ttu-id="71e3f-235">Auch <xref:System.IDisposable.Dispose*> wenn nicht aufgerufen wird, `Dispose` rufen korrekt implementierte Klassen auf, wenn der [Finalizer](/dotnet/csharp/programming-guide/classes-and-structs/destructors) ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="71e3f-235">Even if <xref:System.IDisposable.Dispose*> is not called, correctly implemented classes call `Dispose` when the [finalizer](/dotnet/csharp/programming-guide/classes-and-structs/destructors) runs.</span></span>

<span data-ttu-id="71e3f-236">Betrachten Sie folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="71e3f-236">Consider the following code:</span></span>

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

<span data-ttu-id="71e3f-237">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) ist eine verwaltete Klasse, daher wird jede Instanz am Ende der Anforderung gesammelt.</span><span class="sxs-lookup"><span data-stu-id="71e3f-237">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) is a managed class, so any instance will be collected at the end of the request.</span></span>

<span data-ttu-id="71e3f-238">Die folgende Abbildung zeigt das Speicherprofil, während die `fileprovider` API kontinuierlich aufruft.</span><span class="sxs-lookup"><span data-stu-id="71e3f-238">The following image shows the memory profile while invoking the `fileprovider` API continuously.</span></span>

![vorhergehendes Diagramm](memory/_static/fileprovider.png)

<span data-ttu-id="71e3f-240">Das obige Diagramm zeigt ein offensichtliches Problem mit der Implementierung dieser Klasse, da sie die Speicherauslastung weiter erhöht.</span><span class="sxs-lookup"><span data-stu-id="71e3f-240">The preceding chart shows an obvious issue with the implementation of this class, as it keeps increasing memory usage.</span></span> <span data-ttu-id="71e3f-241">Dies ist ein bekanntes Problem, das in [diesem Problem](https://github.com/dotnet/aspnetcore/issues/3110)nachverfolgt wird.</span><span class="sxs-lookup"><span data-stu-id="71e3f-241">This is a known problem that is being tracked in [this issue](https://github.com/dotnet/aspnetcore/issues/3110).</span></span>

<span data-ttu-id="71e3f-242">Das gleiche Leck kann im Benutzercode passieren, durch eine der folgenden:</span><span class="sxs-lookup"><span data-stu-id="71e3f-242">The same leak could be happen in user code, by one of the following:</span></span>

* <span data-ttu-id="71e3f-243">Die Klasse wird nicht korrekt freigegeben.</span><span class="sxs-lookup"><span data-stu-id="71e3f-243">Not releasing the class correctly.</span></span>
* <span data-ttu-id="71e3f-244">Vergessen, die `Dispose`Methode der abhängigen Objekte aufzurufen, die verworfen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="71e3f-244">Forgetting to invoke the `Dispose`method of the dependent objects that should be disposed.</span></span>

### <a name="large-objects-heap"></a><span data-ttu-id="71e3f-245">Heap für große Objekte</span><span class="sxs-lookup"><span data-stu-id="71e3f-245">Large objects heap</span></span>

<span data-ttu-id="71e3f-246">Häufige Speicherzuweisungen/freie Zyklen können speicherfragmentieren, insbesondere wenn große Speicherblöcke zugewiesen werden.</span><span class="sxs-lookup"><span data-stu-id="71e3f-246">Frequent memory allocation/free cycles can fragment memory, especially when allocating large chunks of memory.</span></span> <span data-ttu-id="71e3f-247">Objekte werden in zusammenhängenden Speicherblöcken zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="71e3f-247">Objects are allocated in contiguous blocks of memory.</span></span> <span data-ttu-id="71e3f-248">Um die Fragmentierung zu verringern, versucht der GC, den Speicher freizugeben, ihn zu defragmentieren.</span><span class="sxs-lookup"><span data-stu-id="71e3f-248">To mitigate fragmentation, when the GC frees memory, it trys to defragment it.</span></span> <span data-ttu-id="71e3f-249">Dieser Prozess wird als **Verdichtung**bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="71e3f-249">This process is called **compaction**.</span></span> <span data-ttu-id="71e3f-250">Bei der Verdichtung werden Objekte bewegt.</span><span class="sxs-lookup"><span data-stu-id="71e3f-250">Compaction involves moving objects.</span></span> <span data-ttu-id="71e3f-251">Das Verschieben großer Objekte führt zu leistungseinbußen.</span><span class="sxs-lookup"><span data-stu-id="71e3f-251">Moving large objects imposes a performance penalty.</span></span> <span data-ttu-id="71e3f-252">Aus diesem Grund erstellt der GC eine spezielle Speicherzone für _große_ Objekte, die als ["Large Object Heap" (LOH)](/dotnet/standard/garbage-collection/large-object-heap) bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="71e3f-252">For this reason the GC creates a special memory zone for _large_ objects, called the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span></span> <span data-ttu-id="71e3f-253">Objekte, die größer als 85.000 Bytes (ca. 83 KB) sind:</span><span class="sxs-lookup"><span data-stu-id="71e3f-253">Objects that are greater than 85,000 bytes (approximately 83 KB) are:</span></span>

* <span data-ttu-id="71e3f-254">Platziert auf der LOH.</span><span class="sxs-lookup"><span data-stu-id="71e3f-254">Placed on the LOH.</span></span>
* <span data-ttu-id="71e3f-255">Nicht verdichtet.</span><span class="sxs-lookup"><span data-stu-id="71e3f-255">Not compacted.</span></span>
* <span data-ttu-id="71e3f-256">Gesammelt während der Generation 2 GCs.</span><span class="sxs-lookup"><span data-stu-id="71e3f-256">Collected during generation 2 GCs.</span></span>

<span data-ttu-id="71e3f-257">Wenn der LOH voll ist, löst der GC eine Generation 2-Auflistung aus.</span><span class="sxs-lookup"><span data-stu-id="71e3f-257">When the LOH is full, the GC will trigger a generation 2 collection.</span></span> <span data-ttu-id="71e3f-258">Generationen 2 Kollektionen:</span><span class="sxs-lookup"><span data-stu-id="71e3f-258">Generation 2 collections:</span></span>

* <span data-ttu-id="71e3f-259">Sind von Natur aus langsam.</span><span class="sxs-lookup"><span data-stu-id="71e3f-259">Are inherently slow.</span></span>
* <span data-ttu-id="71e3f-260">Zusätzlich entstehen die Kosten für das Auslösen einer Sammlung für alle anderen Generationen.</span><span class="sxs-lookup"><span data-stu-id="71e3f-260">Additionally incur the cost of triggering a collection on all other generations.</span></span>

<span data-ttu-id="71e3f-261">Der folgende Code komprimiert den LOH sofort:</span><span class="sxs-lookup"><span data-stu-id="71e3f-261">The following code compacts the LOH immediately:</span></span>

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

<span data-ttu-id="71e3f-262">Weitere <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> Informationen zum Verdichten des LOH finden Sie unter .</span><span class="sxs-lookup"><span data-stu-id="71e3f-262">See <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> for information on compacting the LOH.</span></span>

<span data-ttu-id="71e3f-263">In Containern mit .NET Core 3.0 und höher wird der LOH automatisch komprimiert.</span><span class="sxs-lookup"><span data-stu-id="71e3f-263">In containers using .NET Core 3.0 and later, the LOH is automatically compacted.</span></span>

<span data-ttu-id="71e3f-264">Die folgende API, die dieses Verhalten veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="71e3f-264">The following API that illustrates this behavior:</span></span>

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

<span data-ttu-id="71e3f-265">Das folgende Diagramm zeigt das `/api/loh/84975` Speicherprofil des Aufrufs des Endpunkts unter maximaler Auslastung:</span><span class="sxs-lookup"><span data-stu-id="71e3f-265">The following chart shows the memory profile of calling the `/api/loh/84975` endpoint, under maximum load:</span></span>

![vorhergehendes Diagramm](memory/_static/loh1.png)

<span data-ttu-id="71e3f-267">Das folgende Diagramm zeigt das `/api/loh/84976` Speicherprofil des Aufrufs des Endpunkts und weist *nur ein weiteres Byte*zu:</span><span class="sxs-lookup"><span data-stu-id="71e3f-267">The following chart shows the memory profile of calling the `/api/loh/84976` endpoint, allocating *just one more byte*:</span></span>

![vorhergehendes Diagramm](memory/_static/loh2.png)

<span data-ttu-id="71e3f-269">Hinweis: `byte[]` Die Struktur verfügt über Overhead-Bytes.</span><span class="sxs-lookup"><span data-stu-id="71e3f-269">Note: The `byte[]` structure has overhead bytes.</span></span> <span data-ttu-id="71e3f-270">Aus diesem Grund lösen 84.976 Bytes die 85.000-Grenze aus.</span><span class="sxs-lookup"><span data-stu-id="71e3f-270">That's why 84,976 bytes triggers the 85,000 limit.</span></span>

<span data-ttu-id="71e3f-271">Vergleich der beiden vorhergehenden Diagramme:</span><span class="sxs-lookup"><span data-stu-id="71e3f-271">Comparing the two preceding charts:</span></span>

* <span data-ttu-id="71e3f-272">Der Arbeitssatz ist für beide Szenarien ähnlich, etwa 450 MB.</span><span class="sxs-lookup"><span data-stu-id="71e3f-272">The working set is similar for both scenarios, about 450 MB.</span></span>
* <span data-ttu-id="71e3f-273">Die unter LOH-Anforderungen (84.975 Bytes) zeigt hauptsächlich Auflistungen der Generation 0 an.</span><span class="sxs-lookup"><span data-stu-id="71e3f-273">The under LOH requests (84,975 bytes) shows mostly generation 0 collections.</span></span>
* <span data-ttu-id="71e3f-274">Die über LOH-Anforderungen generieren Sammlungen der konstanten Generation 2.</span><span class="sxs-lookup"><span data-stu-id="71e3f-274">The over LOH requests generate constant generation 2 collections.</span></span> <span data-ttu-id="71e3f-275">Kollektionen der Generation 2 sind teuer.</span><span class="sxs-lookup"><span data-stu-id="71e3f-275">Generation 2 collections are expensive.</span></span> <span data-ttu-id="71e3f-276">Mehr CPU ist erforderlich und der Durchsatz sinkt um fast 50%.</span><span class="sxs-lookup"><span data-stu-id="71e3f-276">More CPU is required and throughput drops almost 50%.</span></span>

<span data-ttu-id="71e3f-277">Temporäre große Objekte sind besonders problematisch, da sie gen2 GCs verursachen.</span><span class="sxs-lookup"><span data-stu-id="71e3f-277">Temporary large objects are particularly problematic because they cause gen2 GCs.</span></span>

<span data-ttu-id="71e3f-278">Für maximale Leistung sollte die Verwendung großer Objekte minimiert werden.</span><span class="sxs-lookup"><span data-stu-id="71e3f-278">For maximum performance, large object use should be minimized.</span></span> <span data-ttu-id="71e3f-279">Teilen Sie nach Möglichkeit große Objekte auf.</span><span class="sxs-lookup"><span data-stu-id="71e3f-279">If possible, split up large objects.</span></span> <span data-ttu-id="71e3f-280">Beispielsweise teilt [die Response Caching-Middleware](xref:performance/caching/response) in ASP.NET Core die Cacheeinträge in Blöcke mit weniger als 85.000 Bytes auf.</span><span class="sxs-lookup"><span data-stu-id="71e3f-280">For example, [Response Caching](xref:performance/caching/response) middleware in ASP.NET Core split the cache entries into blocks less than 85,000 bytes.</span></span>

<span data-ttu-id="71e3f-281">Die folgenden Links zeigen den ASP.NET Core-Ansatz, um Objekte unter dem LOH-Limit zu halten:</span><span class="sxs-lookup"><span data-stu-id="71e3f-281">The following links show the ASP.NET Core approach to keeping objects under the LOH limit:</span></span>

* [<span data-ttu-id="71e3f-282">ResponseCaching/Streams/StreamUtilities.cs</span><span class="sxs-lookup"><span data-stu-id="71e3f-282">ResponseCaching/Streams/StreamUtilities.cs</span></span>](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [<span data-ttu-id="71e3f-283">ResponseCaching/MemoryResponseCache.cs</span><span class="sxs-lookup"><span data-stu-id="71e3f-283">ResponseCaching/MemoryResponseCache.cs</span></span>](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

<span data-ttu-id="71e3f-284">Weitere Informationen finden Sie unter</span><span class="sxs-lookup"><span data-stu-id="71e3f-284">For more information, see:</span></span>

* [<span data-ttu-id="71e3f-285">Großer Objektheap aufgedeckt</span><span class="sxs-lookup"><span data-stu-id="71e3f-285">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="71e3f-286">Großer Objektheap</span><span class="sxs-lookup"><span data-stu-id="71e3f-286">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a><span data-ttu-id="71e3f-287">HttpClient</span><span class="sxs-lookup"><span data-stu-id="71e3f-287">HttpClient</span></span>

<span data-ttu-id="71e3f-288">Eine falsche <xref:System.Net.Http.HttpClient> Verwendung kann zu einem Ressourcenleck führen.</span><span class="sxs-lookup"><span data-stu-id="71e3f-288">Incorrectly using <xref:System.Net.Http.HttpClient> can result in a resource leak.</span></span> <span data-ttu-id="71e3f-289">Systemressourcen, z. B. Datenbankverbindungen, Sockets, Dateihandles usw.:</span><span class="sxs-lookup"><span data-stu-id="71e3f-289">System resources, such as database connections, sockets, file handles, etc.:</span></span>

* <span data-ttu-id="71e3f-290">Sind knapper als Speicher.</span><span class="sxs-lookup"><span data-stu-id="71e3f-290">Are more scarce than memory.</span></span>
* <span data-ttu-id="71e3f-291">Sind problematischer, wenn sie durchgesickert sind als Speicher.</span><span class="sxs-lookup"><span data-stu-id="71e3f-291">Are more problematic when leaked than memory.</span></span>

<span data-ttu-id="71e3f-292">Erfahrene .NET-Entwickler <xref:System.IDisposable.Dispose*> wissen, Objekte <xref:System.IDisposable>aufzurufen, die implementieren.</span><span class="sxs-lookup"><span data-stu-id="71e3f-292">Experienced .NET developers know to call <xref:System.IDisposable.Dispose*> on objects that implement <xref:System.IDisposable>.</span></span> <span data-ttu-id="71e3f-293">Das Nichtveräußern `IDisposable` von Objekten, die implementiert werden, führt in der Regel zu undichtem Speicher oder ausgelaufenen Systemressourcen.</span><span class="sxs-lookup"><span data-stu-id="71e3f-293">Not disposing objects that implement `IDisposable` typically results in leaked memory or leaked system resources.</span></span>

<span data-ttu-id="71e3f-294">`HttpClient`implementiert `IDisposable`, sollte jedoch **nicht** bei jedem Aufruf verworfen werden.</span><span class="sxs-lookup"><span data-stu-id="71e3f-294">`HttpClient` implements `IDisposable`, but should **not** be disposed on every invocation.</span></span> <span data-ttu-id="71e3f-295">Vielmehr `HttpClient` sollte wiederverwendet werden.</span><span class="sxs-lookup"><span data-stu-id="71e3f-295">Rather, `HttpClient` should be reused.</span></span>

<span data-ttu-id="71e3f-296">Der folgende Endpunkt erstellt und `HttpClient` verfügt über eine neue Instanz für jede Anforderung:</span><span class="sxs-lookup"><span data-stu-id="71e3f-296">The following endpoint creates and disposes a new  `HttpClient` instance on every request:</span></span>

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

<span data-ttu-id="71e3f-297">Unter Laden werden die folgenden Fehlermeldungen protokolliert:</span><span class="sxs-lookup"><span data-stu-id="71e3f-297">Under load, the following error messages are logged:</span></span>

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

<span data-ttu-id="71e3f-298">Auch wenn `HttpClient` die Instanzen verworfen werden, dauert es einige Zeit, bis die eigentliche Netzwerkverbindung vom Betriebssystem freigegeben wird.</span><span class="sxs-lookup"><span data-stu-id="71e3f-298">Even though the `HttpClient` instances are disposed, the actual network connection takes some time to be released by the operating system.</span></span> <span data-ttu-id="71e3f-299">Durch die kontinuierliche Erstellung neuer Verbindungen kommt es zur Erschöpfung der _Ports._</span><span class="sxs-lookup"><span data-stu-id="71e3f-299">By continuously creating new connections, _ports exhaustion_ occurs.</span></span> <span data-ttu-id="71e3f-300">Jede Clientverbindung erfordert einen eigenen Clientport.</span><span class="sxs-lookup"><span data-stu-id="71e3f-300">Each client connection requires its own client port.</span></span>

<span data-ttu-id="71e3f-301">Eine Möglichkeit, die Erschöpfung des `HttpClient` Ports zu verhindern, besteht darin, dieselbe Instanz wiederzuverwenden:</span><span class="sxs-lookup"><span data-stu-id="71e3f-301">One way to prevent port exhaustion is to reuse the same `HttpClient` instance:</span></span>

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

<span data-ttu-id="71e3f-302">Die `HttpClient` Instanz wird freigegeben, wenn die App beendet wird.</span><span class="sxs-lookup"><span data-stu-id="71e3f-302">The `HttpClient` instance is released when the app stops.</span></span> <span data-ttu-id="71e3f-303">Dieses Beispiel zeigt, dass nicht jede Einwegressource nach jeder Verwendung verworfen werden sollte.</span><span class="sxs-lookup"><span data-stu-id="71e3f-303">This example shows that not every disposable resource should be disposed after each use.</span></span>

<span data-ttu-id="71e3f-304">Im Folgenden finden Sie eine bessere Möglichkeit, die Lebensdauer einer `HttpClient` Instanz zu verarbeiten:</span><span class="sxs-lookup"><span data-stu-id="71e3f-304">See the following for a better way to handle the lifetime of an `HttpClient` instance:</span></span>

* [<span data-ttu-id="71e3f-305">HttpClient und die Verwaltung der Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="71e3f-305">HttpClient and lifetime management</span></span>](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [<span data-ttu-id="71e3f-306">HTTPClient-Factory-Blog</span><span class="sxs-lookup"><span data-stu-id="71e3f-306">HTTPClient factory blog</span></span>](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a><span data-ttu-id="71e3f-307">Objektpooling</span><span class="sxs-lookup"><span data-stu-id="71e3f-307">Object pooling</span></span>

<span data-ttu-id="71e3f-308">Das vorherige Beispiel `HttpClient` zeigte, wie die Instanz statisch gemacht und von allen Anforderungen wiederverwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="71e3f-308">The previous example showed how the `HttpClient` instance can be made static and reused by all requests.</span></span> <span data-ttu-id="71e3f-309">Die Wiederverwendung verhindert, dass keine Ressourcen mehr vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="71e3f-309">Reuse prevents running out of resources.</span></span>

<span data-ttu-id="71e3f-310">Objektpooling:</span><span class="sxs-lookup"><span data-stu-id="71e3f-310">Object pooling:</span></span>

* <span data-ttu-id="71e3f-311">Verwendet das Wiederverwendungsmuster.</span><span class="sxs-lookup"><span data-stu-id="71e3f-311">Uses the reuse pattern.</span></span>
* <span data-ttu-id="71e3f-312">Ist für Objekte konzipiert, die teuer zu erstellen sind.</span><span class="sxs-lookup"><span data-stu-id="71e3f-312">Is designed for objects that are expensive to create.</span></span>

<span data-ttu-id="71e3f-313">Ein Pool ist eine Sammlung von vorinitialisierten Objekten, die reserviert und über Threads hinweg freigegeben werden können.</span><span class="sxs-lookup"><span data-stu-id="71e3f-313">A pool is a collection of pre-initialized objects that can be reserved and released across threads.</span></span> <span data-ttu-id="71e3f-314">Pools können Allokationsregeln wie Grenzwerte, vordefinierte Größen oder Wachstumsraten definieren.</span><span class="sxs-lookup"><span data-stu-id="71e3f-314">Pools can define allocation rules such as limits, predefined sizes, or growth rate.</span></span>

<span data-ttu-id="71e3f-315">Das NuGet-Paket [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) enthält Klassen, die beim Verwalten solcher Pools helfen.</span><span class="sxs-lookup"><span data-stu-id="71e3f-315">The NuGet package [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contains classes that help to manage such pools.</span></span>

<span data-ttu-id="71e3f-316">Der folgende API-Endpunkt instanziiert einen `byte` Puffer, der bei jeder Anforderung mit Zufallszahlen gefüllt ist:</span><span class="sxs-lookup"><span data-stu-id="71e3f-316">The following API endpoint instantiates a `byte` buffer that is filled with random numbers on each request:</span></span>

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

<span data-ttu-id="71e3f-317">Die folgende Diagrammanzeige, die die vorherige API mit moderater Auslastung aufruft:</span><span class="sxs-lookup"><span data-stu-id="71e3f-317">The following chart display calling the preceding API with moderate load:</span></span>

![vorhergehendes Diagramm](memory/_static/array.png)

<span data-ttu-id="71e3f-319">Im vorherigen Diagramm werden Auflistungen der Generierung 0 ungefähr einmal pro Sekunde angezeigt.</span><span class="sxs-lookup"><span data-stu-id="71e3f-319">In the preceding chart, generation 0 collections happen approximately once per second.</span></span>

<span data-ttu-id="71e3f-320">Der vorangehende Code kann `byte` optimiert werden, indem der Puffer mithilfe von [ArrayPool\<T>](xref:System.Buffers.ArrayPool`1).</span><span class="sxs-lookup"><span data-stu-id="71e3f-320">The preceding code can be optimized by pooling the `byte` buffer by using [ArrayPool\<T>](xref:System.Buffers.ArrayPool`1).</span></span> <span data-ttu-id="71e3f-321">Eine statische Instanz wird über Anforderungen hinweg wiederverwendet.</span><span class="sxs-lookup"><span data-stu-id="71e3f-321">A static instance is reused across requests.</span></span>

<span data-ttu-id="71e3f-322">Bei diesem Ansatz unterscheidet sich, dass ein gepooltes Objekt von der API zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="71e3f-322">What's different with this approach is that a pooled object is returned from the API.</span></span> <span data-ttu-id="71e3f-323">Das bedeutet:</span><span class="sxs-lookup"><span data-stu-id="71e3f-323">That means:</span></span>

* <span data-ttu-id="71e3f-324">Das Objekt ist nicht mehr in der Hand, sobald Sie von der Methode zurückkehren.</span><span class="sxs-lookup"><span data-stu-id="71e3f-324">The object is out of your control as soon as you return from the method.</span></span>
* <span data-ttu-id="71e3f-325">Sie können das Objekt nicht freigeben.</span><span class="sxs-lookup"><span data-stu-id="71e3f-325">You can't release the object.</span></span>

<span data-ttu-id="71e3f-326">So richten Sie die Entsorgung des Objekts ein:</span><span class="sxs-lookup"><span data-stu-id="71e3f-326">To set up disposal of the object:</span></span>

* <span data-ttu-id="71e3f-327">Kapseln Sie das gepoolte Array in einem Einwegobjekt.</span><span class="sxs-lookup"><span data-stu-id="71e3f-327">Encapsulate the pooled array in a disposable object.</span></span>
* <span data-ttu-id="71e3f-328">Registrieren Sie das gepoolte Objekt mit [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span><span class="sxs-lookup"><span data-stu-id="71e3f-328">Register the pooled object with [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span></span>

<span data-ttu-id="71e3f-329">`RegisterForDispose`wird das Zielobjekt aufrufen, `Dispose`sodass es nur freigegeben wird, wenn die HTTP-Anforderung abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="71e3f-329">`RegisterForDispose` will take care of calling `Dispose`on the target object so that it's only released when the HTTP request is complete.</span></span>

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

<span data-ttu-id="71e3f-330">Wenn Sie dieselbe Last wie die nicht gepoolte Version anwenden, wird das folgende Diagramm angezeigt:</span><span class="sxs-lookup"><span data-stu-id="71e3f-330">Applying the same load as the non-pooled version results in the following chart:</span></span>

![vorhergehendes Diagramm](memory/_static/pooledarray.png)

<span data-ttu-id="71e3f-332">Der Hauptunterschied sind zugeordnete Bytes und infolgedessen viel weniger Generation 0-Auflistungen.</span><span class="sxs-lookup"><span data-stu-id="71e3f-332">The main difference is allocated bytes, and as a consequence much fewer generation 0 collections.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="71e3f-333">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="71e3f-333">Additional resources</span></span>

* [<span data-ttu-id="71e3f-334">Automatische Speicherbereinigung</span><span class="sxs-lookup"><span data-stu-id="71e3f-334">Garbage Collection</span></span>](/dotnet/standard/garbage-collection/)
* [<span data-ttu-id="71e3f-335">Grundlegendes zu verschiedenen GC-Modi mit Parallelitäts-Visualisierung</span><span class="sxs-lookup"><span data-stu-id="71e3f-335">Understanding different GC modes with Concurrency Visualizer</span></span>](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [<span data-ttu-id="71e3f-336">Großer Objektheap aufgedeckt</span><span class="sxs-lookup"><span data-stu-id="71e3f-336">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="71e3f-337">Großer Objektheap</span><span class="sxs-lookup"><span data-stu-id="71e3f-337">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)

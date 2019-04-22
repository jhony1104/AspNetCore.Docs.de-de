---
title: Zwischenspeichern in Speicher in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie Daten im Arbeitsspeicher in ASP.NET Core zwischenspeichern können.
ms.author: riande
ms.custom: mvc
ms.date: 04/11/2019
uid: performance/caching/memory
ms.openlocfilehash: 6433df36023b79bc679186bee8b0a92371661dbe
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59425048"
---
# <a name="cache-in-memory-in-aspnet-core"></a>Zwischenspeichern in Speicher in ASP.NET Core

Durch [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo), und [Steve Smith](https://ardalis.com/)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/memory/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="caching-basics"></a>Grundlagen der Zwischenspeicherung

Caching kann erheblich die Leistung und Skalierbarkeit einer App verbessert werden, verringern den erforderlichen Aufwand zum Generieren von Inhalt. Funktionsweise der Zwischenspeicherung am stärksten bei den Daten, die nur selten ändern. Caching stellt eine Kopie der Daten, die viel zurückgegeben werden, können aus der Originalquelle schneller. Apps sollten geschrieben und getestet, **nie** hängen von zwischengespeicherten Daten.

ASP.NET Core unterstützt mehrere unterschiedliche Caches gelten. Die einfachste Cache basiert auf der [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache), steht für einen Cache im Arbeitsspeicher des Webservers gespeichert. Apps, die auf eine Serverfarm mit mehreren Servern ausgeführt werden sollten sicherstellen, dass Sitzungen Kurznotiz bei Verwendung von in-Memory-Cache. Persistente Sitzungen stellen Sie sicher, dass nachfolgende Anforderungen von einem Client, die alle auf dem gleichen Server erfolgen. Z. B. Azure-Web-apps verwenden [Application Request Routing](https://www.iis.net/learn/extensions/planning-for-arr) (ARR), alle nachfolgende Anforderungen auf denselben Server weitergeleitet.

Nicht-sticky-Sitzungen in einer Webfarm erfordern eine [verteilter Cache](distributed.md) , Cache Konsistenzprobleme zu vermeiden. Bei einigen apps kann ein verteilter Cache höheren Skalierung als eine in-Memory-Cache unterstützt. Mit einem verteilten Cache lädt die Cache-Speicher an einen externen Prozess ab.

::: moniker range="< aspnetcore-2.0"

Die `IMemoryCache` Cache-Cacheeinträge Einträge im Cache nicht genügend Arbeitsspeicher vorhanden, es sei denn, die [Zwischenspeichern Priorität](/dotnet/api/microsoft.extensions.caching.memory.cacheitempriority) nastaven NA hodnotu `CacheItemPriority.NeverRemove`. Sie können festlegen, die `CacheItemPriority` die Priorität anpassen, mit dem Cache Elemente nicht genügend Arbeitsspeicher entfernt.

::: moniker-end

In-Memory-Cache kann jedes beliebige Objekt speichern. die Schnittstelle für verteilte Caches ist auf `byte[]`. Die im Arbeitsspeicher und verteilten Cache-Speicher-Cacheelemente als Schlüssel-Wert-Paare.

## <a name="systemruntimecachingmemorycache"></a>System.Runtime.Caching/MemoryCache

<xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet-Paket](https://www.nuget.org/packages/System.Runtime.Caching/)) mit verwendet werden kann:

* .NET Standard 2.0 oder höher.
* Alle [.NET Implementierung](/dotnet/standard/net-standard#net-implementation-support) , die auf .NET Standard 2.0 oder höher. Beispiel: ASP.NET Core 2.0 oder höher.
* .NET Framework 4.5 oder höher.

["Microsoft.Extensions.Caching.Memory"](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) / `IMemoryCache` (in diesem Artikel beschrieben) wird empfohlen, `System.Runtime.Caching` / `MemoryCache` da es besser in ASP.NET Core integriert ist. Z. B. `IMemoryCache` funktioniert nativ in ASP.NET Core [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection).

Verwendung `System.Runtime.Caching` / `MemoryCache` als Brücke Kompatibilität beim Portieren von Code von ASP.NET 4.x zu ASP.NET Core.

## <a name="cache-guidelines"></a>Cache-Richtlinien

* Code sollte immer eine Fallbackoption zum Abrufen von Daten aufweisen und **nicht** richten sich nach der ein zwischengespeicherter Wert, der nicht verfügbar sind.
* Der Cache verwendet eine wertvolle Ressource, die Arbeitsspeicher. Cache Wachstum zu beschränken:
  * Führen Sie **nicht** externe Eingaben wie den Zugriffsschlüssel für den Cache verwenden.
  * Verwenden Sie Ablaufzeiten, um den Cache verursachte Wachstum zu begrenzen.
  * [Verwenden Sie zum Beschränken der Größe des Caches SetSize, Größe und SizeLimit](#use-setsize-size-and-sizelimit-to-limit-cache-size). Die ASP.NET Core-Runtime wird die Cachegröße, die basierend auf nicht ausreichendem Arbeitsspeicher nicht beschränkt. Es obliegt dem Entwickler, die Größe des Caches zu beschränken.

## <a name="using-imemorycache"></a>Verwenden von IMemoryCache

In-Memory-caching ist ein *Service* auf den verwiesen wird über Ihre app mit [Dependency Injection](../../fundamentals/dependency-injection.md). Rufen Sie `AddMemoryCache` in `ConfigureServices`:

[!code-csharp[](memory/sample/WebCache/Startup.cs?highlight=9)]

Anfordern der `IMemoryCache` -Instanz in den Konstruktor:

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor)]

::: moniker range="< aspnetcore-2.0"

`IMemoryCache` NuGet-Paket erfordert ["Microsoft.Extensions.Caching.Memory"](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/).

::: moniker-end

::: moniker range="= aspnetcore-2.0"

`IMemoryCache` NuGet-Paket erfordert ["Microsoft.Extensions.Caching.Memory"](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), steht in der die [metapaket "Microsoft.aspnetcore.All"](xref:fundamentals/metapackage).

::: moniker-end

::: moniker range="> aspnetcore-2.0"

`IMemoryCache` NuGet-Paket erfordert ["Microsoft.Extensions.Caching.Memory"](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), steht in der die [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app).

::: moniker-end

Der folgende code verwendet [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) zu überprüfen, ob eine Zeit im Cache vorhanden ist. Wenn Sie eine Zeit nicht zwischengespeichert wird, ein neuer Eintrag erstellt und in den Cache mit hinzugefügt [festgelegt](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).

[!code-csharp [](memory/sample/WebCache/CacheKeys.cs)]

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

Die aktuelle Uhrzeit und die Cachezeit werden angezeigt:

[!code-cshtml[](memory/sample/WebCache/Views/Home/Cache.cshtml)]

Die zwischengespeicherten `DateTime` Wert im Cache bleibt, während Anforderungen innerhalb des Zeitlimits vorhanden sind. Die folgende Abbildung zeigt die aktuelle Uhrzeit und einem älteren Zeitpunkt aus dem Cache abgerufen werden:

![Ansicht "Index" mit zwei verschiedenen Uhrzeiten angezeigt](memory/_static/time.png)

Der folgende code verwendet [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) und [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) zum Zwischenspeichern von Daten.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

Der folgende code ruft [erhalten](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) der Cachezeit abgerufen:

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*> , <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>, und [erhalten](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) Erweiterung Methoden Teil der [CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) Klasse, die die Möglichkeit erweitert <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>. Finden Sie unter [IMemoryCache Methoden](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) und [CacheExtensions Methoden](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) eine Beschreibung der anderen Cache-Methoden.

## <a name="memorycacheentryoptions"></a>MemoryCacheEntryOptions

Im folgenden Beispiel:

* Legt die absolute Ablaufzeit fest. Dies ist die maximale Zeit, die der Eintrag zwischengespeichert werden kann, und verhindert, dass das Element zunehmend veraltet, wenn die gleitende Ablaufzeit ständig erneuert wird.
* Legt eine gleitende Ablaufzeit fest. Anforderungen, die Zugriff auf diese zwischengespeicherte Element werden der gleitende Ablaufdauer zurückgesetzt.
* Legt die Cachepriorität auf `CacheItemPriority.NeverRemove`.
* Legt eine [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) wird, aufgerufen werden, nachdem der Eintrag aus dem Cache entfernt wird. Der Rückruf wird auf einem anderen Thread aus dem Code ausgeführt, die das Element aus dem Cache entfernt.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

::: moniker range=">= aspnetcore-2.0"

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a>Verwenden Sie SetSize, Größe und SizeLimit Beschränken der Größe des Caches

Ein `MemoryCache` -Instanz kann optional angeben und gilt eine größenbeschränkung. Grenzwert für die Arbeitsspeichergröße verfügt keine definierte Maßeinheit, da der Cache keinen Mechanismus, um die Größe der Einträge messen verfügt. Wenn die Cache-Speicher-größenbeschränkung festgelegt ist, müssen alle Einträge Größe angeben. Die ASP.NET Core-Runtime wird die Cachegröße, die basierend auf nicht ausreichendem Arbeitsspeicher nicht beschränkt. Es obliegt dem Entwickler, die Größe des Caches zu beschränken. Die angegebene Größe ist in Einheiten, die der Entwickler entscheidet.

Zum Beispiel:

* Wenn die Zeichenfolgen in erster Linie von die Web-app Zwischenspeichern wurde, kann jeder Eintrag Cachegröße die Länge der Zeichenfolge sein.
* Die app konnte die Größe aller Einträge geben, als 1, und die maximale Größe beträgt die Anzahl der Einträge.

Der folgende Code erstellt feste Größe ohne Einheiten [MemoryCache](/dotnet/api/microsoft.extensions.caching.memory.memorycache?view=aspnetcore-2.1) zugänglich [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection):

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

[SizeLimit](/dotnet/api/microsoft.extensions.caching.memory.memorycacheoptions.sizelimit?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheOptions_SizeLimit) keine Einheiten. Zwischengespeicherter Einträge müssen die Größe angeben, in der alle Einheiten sie halten, am besten geeignet, wenn die Größe des Cachespeichers festgelegt wurde. Alle Benutzer einer Cache-Instanz sollte das gleiche Einheitensystem verwenden. Ein Eintrag wird nicht zwischengespeichert werden, wenn die Summe der Größen zwischengespeicherten Eintrag angegebenen Wert überschreitet `SizeLimit`. Wenn keine Cache-größenbeschränkung festgelegt ist, wird die Cachegröße, legen Sie auf den Eintrag ignoriert.

Der folgende code registriert `MyMemoryCache` mit der [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) Container.

[!code-csharp[](memory/sample/RPcache/Startup.cs?name=snippet&highlight=5)]

`MyMemoryCache` wird als ein unabhängiger Memory-Cache für Komponenten erstellt, die dieser Größe beschränkt Cache kennen und wissen, wie Sie die Größe des Eintrags entsprechend festlegen.

Der folgende code verwendet `MyMemoryCache`:

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet)]

Die Größe des Cacheeintrags festgelegt werden kann [Größe](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) oder [SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) Erweiterungsmethode:

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

::: moniker-end

## <a name="cache-dependencies"></a>-Cacheabhängigkeiten

Das folgende Beispiel zeigt, wie Sie einen Eintrag im Cache ablaufen, wenn ein abhängiger Eintrag abläuft. Ein `CancellationChangeToken` wird das zwischengespeicherte Element hinzugefügt. Wenn `Cancel` aufgerufen wird, auf die `CancellationTokenSource`, beide Cacheeinträge entfernt werden.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

Mit einem `CancellationTokenSource` können mehrere Einträge im Cache als Gruppe entfernt werden. Mit der `using` Muster im obigen Code, Einträge im Cache erstellt wird, in der `using` Block übernimmt, Trigger und die ablaufeinstellungen.

## <a name="additional-notes"></a>Zusätzliche Hinweise

* Wenn Sie einen Rückruf verwenden ein Element im Cache neu auffüllen:

  * Mehrere Anforderungen finde den zwischengespeicherten Schlüssel-Wert leer, da der Rückruf noch nicht abgeschlossen wurde.
  * Dies kann in mehrere Threads, die für das streckungsschema an das zwischengespeicherte Element führen.

* Wenn ein Cacheeintrag verwendet wird, um einen anderen zu erstellen, kopiert das untergeordnete Element der übergeordnete Eintrag Ablauf des Tokens und zeitbasierte ablaufeinstellungen. Das untergeordnete Element ist nicht abgelaufen ist, durch manuelles Entfernen oder Aktualisieren von der übergeordnete Eintrag.

* Verwendung [PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) um die Rückrufe festzulegen, die ausgelöst wird, wenn der Cacheeintrag aus dem Cache entfernt wird.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

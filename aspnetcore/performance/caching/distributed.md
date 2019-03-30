---
title: Verteilte Zwischenspeicherung in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie einen ASP.NET Core verteilten Cache zu verwenden, um app-Leistung und Skalierbarkeit, insbesondere in einer Cloud oder einer serverfarmumgebung zu verbessern.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2019
uid: performance/caching/distributed
ms.openlocfilehash: c3774c26116a4cb70386d0060f2244d224fec8e1
ms.sourcegitcommit: 3e9e1f6d572947e15347e818f769e27dea56b648
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/30/2019
ms.locfileid: "58750989"
---
# <a name="distributed-caching-in-aspnet-core"></a>Verteilte Zwischenspeicherung in ASP.NET Core

Durch [Luke Latham](https://github.com/guardrex) und [Steve Smith](https://ardalis.com/)

Ein verteilter Cache ist ein Cache von mehreren app-Servern, die in der Regel als externer Dienst mit den app-Servern, die darauf zugreifen verwaltet gemeinsam verwendet werden. Ein verteilter Cache kann die Leistung und Skalierbarkeit von einer ASP.NET Core-app verbessern, insbesondere, wenn die app von einem Cloud-Dienst oder einer Serverfarm gehostet wird.

Ein verteilter Cache hat mehrere Vorteile gegenüber anderen Zwischenspeicherungsszenarios, in dem zwischengespeicherte Daten auf einzelnen app-Servern gespeichert werden.

Wenn die zwischengespeicherte Daten verteilt werden, werden die Daten:

* Ist *kohärente* (wie), über die Anforderungen auf mehrere Server hinweg.
* Überdauert Server neu gestartet wurde und app-Bereitstellungen.
* Den nicht lokalen Speicher verwenden.

Konfiguration für verteilte Caches ist implementierungsspezifisch. Dieser Artikel beschreibt das Konfigurieren von SQL Server und verteilten Caches zu Redis. Drittanbieter-Implementierungen sind auch verfügbar, z. B. [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache auf GitHub](https://github.com/Alachisoft/NCache)). Unabhängig davon, welche Implementierung aktiviert ist, der die app interagiert mit dem Cache mithilfe der <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Schnittstelle.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Vorraussetzungen

::: moniker range=">= aspnetcore-2.2"

Eine SQL Server distributed Cache Verweis der [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app) oder fügen Sie einen Paketverweis auf die [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) Paket.

Verteilt Sie mit einem Redis Cache, Verweis der [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app) und fügen Sie einen Paketverweis auf die [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) Paket. Das Redis-Paket nicht enthalten, der `Microsoft.AspNetCore.App` Verpacken, damit Sie das Redis-Paket separat in der Projektdatei verweisen müssen.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Eine SQL Server distributed Cache Verweis der [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app) oder fügen Sie einen Paketverweis auf die [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) Paket.

Verteilt Sie mit einem Redis Cache, Verweis der [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app) und fügen Sie einen Paketverweis auf die [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) Paket. Das Redis-Paket nicht enthalten, der `Microsoft.AspNetCore.App` Verpacken, damit Sie das Redis-Paket separat in der Projektdatei verweisen müssen.

::: moniker-end

## <a name="idistributedcache-interface"></a>IDistributedCache-Schnittstelle

Die <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Schnittstelle bietet die folgenden Methoden zum Bearbeiten von Elementen in der verteilten Cache-Implementierung:

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Akzeptiert einen Zeichenfolgenschlüssel und ruft ein zwischengespeichertes Element als ein `byte[]` array, wenn im Cache gefunden.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Fügt ein Element hinzu (als `byte[]` Array) in den Cache mit einem Zeichenfolgenschlüssel.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Aktualisiert ein Element im Cache basierend auf seinen Schlüssel an, das Zurücksetzen von gleitenden Ablauf des Timeouts (sofern vorhanden).
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Entfernt ein Element im Cache auf den Zeichenfolgenschlüssel basierend.

## <a name="establish-distributed-caching-services"></a>Verteilte Zwischenspeicherung Dienste einrichten

Registrieren Sie eine Implementierung von <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`. Framework bereitgestellten Implementierungen, die in diesem Thema beschriebenen Folgendes umfassen:

* [Verteilte Memory-Cache](#distributed-memory-cache)
* [Verteilte SQL Server-cache](#distributed-sql-server-cache)
* [Verteilte Redis-cache](#distributed-redis-cache)

### <a name="distributed-memory-cache"></a>Verteilte Memory-Cache

Der verteilten Memory-Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) ist eine Framework bereitgestellte Implementierung der <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , Elemente im Arbeitsspeicher speichert. Der verteilten Memory-Cache ist eine tatsächliche verteilter Cache nicht. Zwischengespeicherte Elemente werden von der app-Instanz auf dem Server gespeichert, in dem die app ausgeführt wird.

Der verteilten Memory-Cache ist eine nützliche-Implementierung:

* In Entwicklungs- und Testszenarien.
* Wenn ein einzelnen Server in der Produktion und arbeitsspeichernutzung verwendet wird, ist kein Problem. Implementieren Auszüge aus verteilten Memory-Cache zwischengespeichert Datenspeicher. Sie können für die Implementierung, dass eine echte Cachinglösung, die in der zukünftigen Wenn mehrere Knoten oder Fehlertoleranz erforderlich werden verteilt.

Die Beispiel-app nutzt den verteilten Memory-Cache beim Ausführen der app in der Entwicklungsumgebung in `Startup.ConfigureServices`:

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>Verteilte SQL Server-Cache

Die verteilte SQL Server-Cache-Implementierung (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) ermöglicht es den verteilten Cache, die SQL Server-Datenbank als Sicherungsspeicher verwendet. Um eine SQL Server-Datenbanktabelle zwischengespeicherte Element in einer SQL Server-Instanz zu erstellen, können Sie die `sql-cache` Tool. Das Tool erstellt eine Tabelle mit dem Namen und Schema, das Sie angeben.

Erstellen Sie eine Tabelle in SQL Server durch Ausführen der `sql-cache create` Befehl. Geben Sie die SQL Server-Instanz (`Data Source`), Datenbank (`Initial Catalog`), Schema (z. B. `dbo`), und der Tabelle an (z. B. `TestCache`):

```console
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

Um anzugeben, dass das Tool erfolgreich war, wird eine Meldung protokolliert:

```console
Table and index were created successfully.
```

Die Tabelle erstellt, indem die `sql-cache` Tool weist das folgende Schema:

![SQL Server-Cache-Tabelle](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> Eine app sollte mithilfe einer Instanz von cachewerte bearbeiten <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, sondern eine <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.

Die Beispiel-app implementiert <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in einer nicht-Entwicklungsumgebung in `Startup.ConfigureServices`:

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> Ein <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (und optional <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> und <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) in der Regel außerhalb der quellcodeverwaltung gespeichert werden (z. B. von gespeichert werden. die [Secret Manager](xref:security/app-secrets) oder im *"appSettings.JSON"* / *"appSettings". {Umgebung} .json* Dateien). Die Verbindungszeichenfolge enthält möglicherweise Anmeldeinformationen, die aus Quellcode-Verwaltungssysteme aufbewahrt werden sollen.

### <a name="distributed-redis-cache"></a>Verteilte Redis-Cache

[Redis](https://redis.io/) ist ein open-Source-Speicher in-Memory-Daten, die häufig als ein verteilter Cache verwendet wird. Können Sie Redis lokal, und Sie können konfigurieren, eine [Azure Redis Cache](https://azure.microsoft.com/services/cache/) für eine Azure gehostete ASP.NET Core-app.

::: moniker range=">= aspnetcore-2.2"

Eine app konfiguriert, den Cache-Implementierung mit einer <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> Instanz (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in einer nicht-Entwicklungsumgebung in `Startup.ConfigureServices`:

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Eine app konfiguriert, den Cache-Implementierung mit einer <xref:Microsoft.Extensions.Caching.Redis.RedisCache> Instanz (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

::: moniker-end

So installieren Redis auf Ihrem lokalen Computer:

* Installieren Sie die [Package Chocolatey Redis](https://chocolatey.org/packages/redis-64/).
* Führen Sie `redis-server` über eine Eingabeaufforderung.

## <a name="use-the-distributed-cache"></a>Verwenden Sie den verteilten cache

Verwenden der <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Schnittstelle, fordern Sie eine Instanz von <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> über keinen Konstruktor in der app. Die Instanz wird von bereitgestellt [Abhängigkeitsinjektion (Dependency Injection)](xref:fundamentals/dependency-injection).

Nach dem Start der app <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> injiziert wird `Startup.Configure`. Die aktuelle Zeit zwischengespeichert wird, mithilfe von <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (Weitere Informationen finden Sie unter [Webhost: IApplicationLifetime-Schnittstelle](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

Die Beispiel-app fügt <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in die `IndexModel` für die Verwendung durch die Indexseite.

Jedes Mal die Indexseite geladen wird, der Cache wird überprüft, für die zwischengespeicherten Zeit in `OnGetAsync`. Wenn die zwischengespeicherte Zeit nicht abgelaufen ist, wird die Zeit angezeigt. Wenn 20 Sekunden seit dem letzten die Cachezeit wurde (der letzten Seite geladen wurde) zugegriffen übersteigt, um die Seite zeigt *zwischengespeicherte Zeit abgelaufen*.

Aktualisieren Sie die zwischengespeicherte Zeit sofort auf die aktuelle Uhrzeit, durch Auswählen der **Cachezeit zurücksetzen** Schaltfläche. Die Schaltfläche "-Trigger die `OnPostResetCachedTime` Ereignishandlermethode.

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> Besteht keine Notwendigkeit für eine Singleton- oder Bereichsbezogene Lebensdauer verwenden <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Instanzen (mindestens für die integrierten Implementierungen).
>
> Sie können auch erstellen, eine <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Instanz immer Sie möglicherweise einen benötigen anstelle von DI, aber erstellen eine Instanz im Code kann Code schwieriger zu testen und verstößt gegen die [Prinzip der expliziten Abhängigkeiten](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).

## <a name="recommendations"></a>Empfehlungen

Bei der Entscheidung, welche Implementierung der <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> ist am besten für Ihre app, berücksichtigen Sie Folgendes:

* Vorhandene Infrastruktur
* Leistungsanforderungen
* Kosten
* Teamerfahrung

Lösungen zur ausgabezwischenspeicherung beruhen zumeist auf in-Memory-Speicher, um schnelle Abrufen von zwischengespeicherten Daten bereitzustellen, aber Speicher vorhanden ist eine eingeschränkte Ressource zu erweitern und teuer. Nur Speicher verwendet häufig die Daten in einem Cache.

Im Allgemeinen bietet ein Redis-Cache, einen höheren Durchsatz und geringerer Latenz als eine SQL Server-Cache. Vergleichstests ist jedoch normalerweise erforderlich, um zu bestimmen, die Leistungsmerkmale von zwischenspeicherstrategien.

Wenn SQL Server als einen verteilten Cache-Sicherungsspeicher verwendet wird, verwenden der gleichen Datenbank für den Cache und normale Datenspeicher der app und abrufen kann sowohl die Leistung negativ beeinträchtigt. Es wird empfohlen, eine dedizierte SQL Server-Instanz für den verteilten Cache Sicherungsspeicher verwenden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Redis-Cache in Azure](/azure/azure-cache-for-redis/)
* [SQL­Datenbank in Azure](/azure/sql-database/)
* [ASP.NET Core-Anbieter von IDistributedCache für NCache in Webfarmen](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache auf GitHub](https://github.com/Alachisoft/NCache))
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

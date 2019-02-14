---
title: Verteilte Zwischenspeicherung in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie einen ASP.NET Core verteilten Cache zu verwenden, um app-Leistung und Skalierbarkeit, insbesondere in einer Cloud oder einer serverfarmumgebung zu verbessern.
ms.author: riande
ms.custom: mvc
ms.date: 02/13/2019
uid: performance/caching/distributed
ms.openlocfilehash: a157eb075874d2118e3e34b51410b539a1ec37df
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248587"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="8e183-103">Verteilte Zwischenspeicherung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8e183-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="8e183-104">Von [Steve Smith](https://ardalis.com/) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8e183-104">By [Steve Smith](https://ardalis.com/) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="8e183-105">Ein verteilter Cache ist ein Cache von mehreren app-Servern, die in der Regel als externer Dienst mit den app-Servern, die darauf zugreifen verwaltet gemeinsam verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8e183-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="8e183-106">Ein verteilter Cache kann die Leistung und Skalierbarkeit von einer ASP.NET Core-app verbessern, insbesondere, wenn die app von einem Cloud-Dienst oder einer Serverfarm gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="8e183-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="8e183-107">Ein verteilter Cache hat mehrere Vorteile gegenüber anderen Zwischenspeicherungsszenarios, in dem zwischengespeicherte Daten auf einzelnen app-Servern gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="8e183-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="8e183-108">Wenn die zwischengespeicherte Daten verteilt werden, werden die Daten:</span><span class="sxs-lookup"><span data-stu-id="8e183-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="8e183-109">Ist *kohärente* (wie), über die Anforderungen auf mehrere Server hinweg.</span><span class="sxs-lookup"><span data-stu-id="8e183-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="8e183-110">Überdauert Server neu gestartet wurde und app-Bereitstellungen.</span><span class="sxs-lookup"><span data-stu-id="8e183-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="8e183-111">Den nicht lokalen Speicher verwenden.</span><span class="sxs-lookup"><span data-stu-id="8e183-111">Doesn't use local memory.</span></span>

<span data-ttu-id="8e183-112">Konfiguration für verteilte Caches ist implementierungsspezifisch.</span><span class="sxs-lookup"><span data-stu-id="8e183-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="8e183-113">Dieser Artikel beschreibt das Konfigurieren von SQL Server und verteilten Caches zu Redis.</span><span class="sxs-lookup"><span data-stu-id="8e183-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="8e183-114">Drittanbieter-Implementierungen sind auch verfügbar, z. B. [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache auf GitHub](https://github.com/Alachisoft/NCache)).</span><span class="sxs-lookup"><span data-stu-id="8e183-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="8e183-115">Unabhängig davon, welche Implementierung aktiviert ist, der die app interagiert mit dem Cache mithilfe der <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="8e183-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="8e183-116">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8e183-116">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8e183-117">Vorraussetzungen</span><span class="sxs-lookup"><span data-stu-id="8e183-117">Prerequisites</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="8e183-118">Eine SQL Server distributed Cache Verweis der [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app) oder fügen Sie einen Paketverweis auf die [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) Paket.</span><span class="sxs-lookup"><span data-stu-id="8e183-118">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="8e183-119">Verteilt Sie mit einem Redis Cache, Verweis der [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app) und fügen Sie einen Paketverweis auf die [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) Paket.</span><span class="sxs-lookup"><span data-stu-id="8e183-119">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="8e183-120">Das Redis-Paket nicht enthalten, der `Microsoft.AspNetCore.App` Verpacken, damit Sie das Redis-Paket separat in der Projektdatei verweisen müssen.</span><span class="sxs-lookup"><span data-stu-id="8e183-120">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="8e183-121">Eine SQL Server distributed Cache Verweis der [metapaket "Microsoft.aspnetcore.All"](xref:fundamentals/metapackage) oder fügen Sie einen Paketverweis auf die [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) Paket.</span><span class="sxs-lookup"><span data-stu-id="8e183-121">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="8e183-122">Verteilt Sie mit einem Redis Cache, Verweis der [metapaket "Microsoft.aspnetcore.All"](xref:fundamentals/metapackage) oder fügen Sie einen Paketverweis auf die [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) Paket.</span><span class="sxs-lookup"><span data-stu-id="8e183-122">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) or add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="8e183-123">Das Redis-Paket befindet sich im `Microsoft.AspNetCore.All` Verpacken, weshalb Sie nicht das Redis-Paket separat in der Projektdatei zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="8e183-123">The Redis package is included in `Microsoft.AspNetCore.All` package, so you don't need to reference the Redis package separately in your project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="8e183-124">Eine SQL Server verteilte Caches, fügen Sie einen Paketverweis auf die [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) Paket.</span><span class="sxs-lookup"><span data-stu-id="8e183-124">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="8e183-125">Verwenden Sie einen Redis verteilter Cache, fügen Sie einen Paketverweis auf die [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) Paket.</span><span class="sxs-lookup"><span data-stu-id="8e183-125">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span>

::: moniker-end

## <a name="idistributedcache-interface"></a><span data-ttu-id="8e183-126">IDistributedCache-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="8e183-126">IDistributedCache interface</span></span>

<span data-ttu-id="8e183-127">Die <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Schnittstelle bietet die folgenden Methoden zum Bearbeiten von Elementen in der verteilten Cache-Implementierung:</span><span class="sxs-lookup"><span data-stu-id="8e183-127">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="8e183-128"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Akzeptiert einen Zeichenfolgenschlüssel und ruft ein zwischengespeichertes Element als ein `byte[]` array, wenn im Cache gefunden.</span><span class="sxs-lookup"><span data-stu-id="8e183-128"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="8e183-129"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Fügt ein Element hinzu (als `byte[]` Array) in den Cache mit einem Zeichenfolgenschlüssel.</span><span class="sxs-lookup"><span data-stu-id="8e183-129"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="8e183-130"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Aktualisiert ein Element im Cache basierend auf seinen Schlüssel an, das Zurücksetzen von gleitenden Ablauf des Timeouts (sofern vorhanden).</span><span class="sxs-lookup"><span data-stu-id="8e183-130"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="8e183-131"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Entfernt ein Element im Cache auf den Zeichenfolgenschlüssel basierend.</span><span class="sxs-lookup"><span data-stu-id="8e183-131"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="8e183-132">Verteilte Zwischenspeicherung Dienste einrichten</span><span class="sxs-lookup"><span data-stu-id="8e183-132">Establish distributed caching services</span></span>

<span data-ttu-id="8e183-133">Registrieren Sie eine Implementierung von <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8e183-133">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8e183-134">Framework bereitgestellten Implementierungen, die in diesem Thema beschriebenen Folgendes umfassen:</span><span class="sxs-lookup"><span data-stu-id="8e183-134">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="8e183-135">Verteilte Memory-Cache</span><span class="sxs-lookup"><span data-stu-id="8e183-135">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="8e183-136">Verteilte SQL Server-cache</span><span class="sxs-lookup"><span data-stu-id="8e183-136">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="8e183-137">Verteilte Redis-cache</span><span class="sxs-lookup"><span data-stu-id="8e183-137">Distributed Redis cache</span></span>](#distributed-redis-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="8e183-138">Verteilte Memory-Cache</span><span class="sxs-lookup"><span data-stu-id="8e183-138">Distributed Memory Cache</span></span>

<span data-ttu-id="8e183-139">Der verteilten Memory-Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) ist eine Framework bereitgestellte Implementierung der <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , Elemente im Arbeitsspeicher speichert.</span><span class="sxs-lookup"><span data-stu-id="8e183-139">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="8e183-140">Der verteilten Memory-Cache ist eine tatsächliche verteilter Cache nicht.</span><span class="sxs-lookup"><span data-stu-id="8e183-140">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="8e183-141">Zwischengespeicherte Elemente werden von der app-Instanz auf dem Server gespeichert, in dem die app ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="8e183-141">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="8e183-142">Der verteilten Memory-Cache ist eine nützliche-Implementierung:</span><span class="sxs-lookup"><span data-stu-id="8e183-142">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="8e183-143">In Entwicklungs- und Testszenarien.</span><span class="sxs-lookup"><span data-stu-id="8e183-143">In development and testing scenarios.</span></span>
* <span data-ttu-id="8e183-144">Wenn ein einzelnen Server in der Produktion und arbeitsspeichernutzung verwendet wird, ist kein Problem.</span><span class="sxs-lookup"><span data-stu-id="8e183-144">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="8e183-145">Implementieren Auszüge aus verteilten Memory-Cache zwischengespeichert Datenspeicher.</span><span class="sxs-lookup"><span data-stu-id="8e183-145">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="8e183-146">Sie können für die Implementierung, dass eine echte Cachinglösung, die in der zukünftigen Wenn mehrere Knoten oder Fehlertoleranz erforderlich werden verteilt.</span><span class="sxs-lookup"><span data-stu-id="8e183-146">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="8e183-147">Die Beispiel-app nutzt den verteilten Memory-Cache, wenn die app in der Entwicklungsumgebung ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="8e183-147">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_ConfigureServices&highlight=5)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="8e183-148">Verteilte SQL Server-Cache</span><span class="sxs-lookup"><span data-stu-id="8e183-148">Distributed SQL Server Cache</span></span>

<span data-ttu-id="8e183-149">Die verteilte SQL Server-Cache-Implementierung (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) ermöglicht es den verteilten Cache, die SQL Server-Datenbank als Sicherungsspeicher verwendet.</span><span class="sxs-lookup"><span data-stu-id="8e183-149">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="8e183-150">Um eine SQL Server-Datenbanktabelle zwischengespeicherte Element in einer SQL Server-Instanz zu erstellen, können Sie die `sql-cache` Tool.</span><span class="sxs-lookup"><span data-stu-id="8e183-150">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="8e183-151">Das Tool erstellt eine Tabelle mit dem Namen und Schema, das Sie angeben.</span><span class="sxs-lookup"><span data-stu-id="8e183-151">The tool creates a table with the name and schema that you specify.</span></span>

::: moniker range="< aspnetcore-2.1"

<span data-ttu-id="8e183-152">Hinzufügen `SqlConfig.Tools` auf die `<ItemGroup>` Element der Projektdatei und Ausführung `dotnet restore`.</span><span class="sxs-lookup"><span data-stu-id="8e183-152">Add `SqlConfig.Tools` to the `<ItemGroup>` element of the project file and run `dotnet restore`.</span></span>

```xml
<ItemGroup>
  <DotNetCliToolReference Include="Microsoft.Extensions.Caching.SqlConfig.Tools"
                          Version="2.0.2" />
</ItemGroup>
```

::: moniker-end

<span data-ttu-id="8e183-153">Erstellen Sie eine Tabelle in SQL Server durch Ausführen der `sql-cache create` Befehl.</span><span class="sxs-lookup"><span data-stu-id="8e183-153">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="8e183-154">Geben Sie die SQL Server-Instanz (`Data Source`), Datenbank (`Initial Catalog`), Schema (z. B. `dbo`), und der Tabelle an (z. B. `TestCache`):</span><span class="sxs-lookup"><span data-stu-id="8e183-154">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```console
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="8e183-155">Um anzugeben, dass das Tool erfolgreich war, wird eine Meldung protokolliert:</span><span class="sxs-lookup"><span data-stu-id="8e183-155">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="8e183-156">Die Tabelle erstellt, indem die `sql-cache` Tool weist das folgende Schema:</span><span class="sxs-lookup"><span data-stu-id="8e183-156">The table created by the `sql-cache` tool has the following schema:</span></span>

![SQL Server-Cache-Tabelle](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="8e183-158">Eine app sollte mithilfe einer Instanz von cachewerte bearbeiten <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, sondern eine <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span><span class="sxs-lookup"><span data-stu-id="8e183-158">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="8e183-159">Die Beispiel-app implementiert <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in einer nicht-Entwicklungsumgebung:</span><span class="sxs-lookup"><span data-stu-id="8e183-159">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_ConfigureServices&highlight=9-15)]

> [!NOTE]
> <span data-ttu-id="8e183-160">Ein <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (und optional <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> und <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) in der Regel außerhalb der quellcodeverwaltung gespeichert werden (z. B. von gespeichert werden. die [Secret Manager](xref:security/app-secrets) oder im *"appSettings.JSON"* / *"appSettings". {Umgebung} .json* Dateien).</span><span class="sxs-lookup"><span data-stu-id="8e183-160">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{Environment}.json* files).</span></span> <span data-ttu-id="8e183-161">Die Verbindungszeichenfolge enthält möglicherweise Anmeldeinformationen, die aus Quellcode-Verwaltungssysteme aufbewahrt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="8e183-161">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="8e183-162">Verteilte Redis-Cache</span><span class="sxs-lookup"><span data-stu-id="8e183-162">Distributed Redis Cache</span></span>

<span data-ttu-id="8e183-163">[Redis](https://redis.io/) ist ein open-Source-Speicher in-Memory-Daten, die häufig als ein verteilter Cache verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="8e183-163">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="8e183-164">Können Sie Redis lokal, und Sie können konfigurieren, eine [Azure Redis Cache](https://azure.microsoft.com/services/cache/) für eine Azure gehostete ASP.NET Core-app.</span><span class="sxs-lookup"><span data-stu-id="8e183-164">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span> <span data-ttu-id="8e183-165">Eine app konfiguriert, den Cache-Implementierung mit einer <xref:Microsoft.Extensions.Caching.Redis.RedisCache> Instanz (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span><span class="sxs-lookup"><span data-stu-id="8e183-165">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

<span data-ttu-id="8e183-166">So installieren Redis auf Ihrem lokalen Computer:</span><span class="sxs-lookup"><span data-stu-id="8e183-166">To install Redis on your local machine:</span></span>

* <span data-ttu-id="8e183-167">Installieren Sie die [Package Chocolatey Redis](https://chocolatey.org/packages/redis-64/).</span><span class="sxs-lookup"><span data-stu-id="8e183-167">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
* <span data-ttu-id="8e183-168">Führen Sie `redis-server` über eine Eingabeaufforderung.</span><span class="sxs-lookup"><span data-stu-id="8e183-168">Run `redis-server` from a command prompt.</span></span>

## <a name="use-the-distributed-cache"></a><span data-ttu-id="8e183-169">Verwenden Sie den verteilten cache</span><span class="sxs-lookup"><span data-stu-id="8e183-169">Use the distributed cache</span></span>

<span data-ttu-id="8e183-170">Verwenden der <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Schnittstelle, fordern Sie eine Instanz von <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> über keinen Konstruktor in der app.</span><span class="sxs-lookup"><span data-stu-id="8e183-170">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="8e183-171">Die Instanz wird von bereitgestellt [Abhängigkeitsinjektion (Dependency Injection)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8e183-171">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="8e183-172">Nach dem Start der app <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> injiziert wird `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="8e183-172">When the app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="8e183-173">Die aktuelle Zeit zwischengespeichert wird, mithilfe von <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (Weitere Informationen finden Sie unter [Webhost: IApplicationLifetime-Schnittstelle](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span><span class="sxs-lookup"><span data-stu-id="8e183-173">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="8e183-174">Die Beispiel-app fügt <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in die `IndexModel` für die Verwendung durch die Indexseite.</span><span class="sxs-lookup"><span data-stu-id="8e183-174">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="8e183-175">Jedes Mal die Indexseite geladen wird, der Cache wird überprüft, für die zwischengespeicherten Zeit in `OnGetAsync`.</span><span class="sxs-lookup"><span data-stu-id="8e183-175">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="8e183-176">Wenn die zwischengespeicherte Zeit nicht abgelaufen ist, wird die Zeit angezeigt.</span><span class="sxs-lookup"><span data-stu-id="8e183-176">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="8e183-177">Wenn 20 Sekunden seit dem letzten die Cachezeit wurde (der letzten Seite geladen wurde) zugegriffen übersteigt, um die Seite zeigt *zwischengespeicherte Zeit abgelaufen*.</span><span class="sxs-lookup"><span data-stu-id="8e183-177">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="8e183-178">Aktualisieren Sie die zwischengespeicherte Zeit sofort auf die aktuelle Uhrzeit, durch Auswählen der **Cachezeit zurücksetzen** Schaltfläche.</span><span class="sxs-lookup"><span data-stu-id="8e183-178">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="8e183-179">Die Schaltfläche "-Trigger die `OnPostResetCachedTime` Ereignishandlermethode.</span><span class="sxs-lookup"><span data-stu-id="8e183-179">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="8e183-180">Besteht keine Notwendigkeit für eine Singleton- oder Bereichsbezogene Lebensdauer verwenden <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Instanzen (mindestens für die integrierten Implementierungen).</span><span class="sxs-lookup"><span data-stu-id="8e183-180">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="8e183-181">Sie können auch erstellen, eine <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Instanz immer Sie möglicherweise einen benötigen anstelle von DI, aber erstellen eine Instanz im Code kann Code schwieriger zu testen und verstößt gegen die [Prinzip der expliziten Abhängigkeiten](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="8e183-181">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="8e183-182">Empfehlungen</span><span class="sxs-lookup"><span data-stu-id="8e183-182">Recommendations</span></span>

<span data-ttu-id="8e183-183">Bei der Entscheidung, welche Implementierung der <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> ist am besten für Ihre app, berücksichtigen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="8e183-183">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="8e183-184">Vorhandene Infrastruktur</span><span class="sxs-lookup"><span data-stu-id="8e183-184">Existing infrastructure</span></span>
* <span data-ttu-id="8e183-185">Leistungsanforderungen</span><span class="sxs-lookup"><span data-stu-id="8e183-185">Performance requirements</span></span>
* <span data-ttu-id="8e183-186">Kosten</span><span class="sxs-lookup"><span data-stu-id="8e183-186">Cost</span></span>
* <span data-ttu-id="8e183-187">Teamerfahrung</span><span class="sxs-lookup"><span data-stu-id="8e183-187">Team experience</span></span>

<span data-ttu-id="8e183-188">Lösungen zur ausgabezwischenspeicherung beruhen zumeist auf in-Memory-Speicher, um schnelle Abrufen von zwischengespeicherten Daten bereitzustellen, aber Speicher vorhanden ist eine eingeschränkte Ressource zu erweitern und teuer.</span><span class="sxs-lookup"><span data-stu-id="8e183-188">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="8e183-189">Nur Speicher verwendet häufig die Daten in einem Cache.</span><span class="sxs-lookup"><span data-stu-id="8e183-189">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="8e183-190">Im Allgemeinen bietet ein Redis-Cache, einen höheren Durchsatz und geringerer Latenz als eine SQL Server-Cache.</span><span class="sxs-lookup"><span data-stu-id="8e183-190">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="8e183-191">Vergleichstests ist jedoch normalerweise erforderlich, um zu bestimmen, die Leistungsmerkmale von zwischenspeicherstrategien.</span><span class="sxs-lookup"><span data-stu-id="8e183-191">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="8e183-192">Wenn SQL Server als einen verteilten Cache-Sicherungsspeicher verwendet wird, verwenden der gleichen Datenbank für den Cache und normale Datenspeicher der app und abrufen kann sowohl die Leistung negativ beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="8e183-192">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="8e183-193">Es wird empfohlen, eine dedizierte SQL Server-Instanz für den verteilten Cache Sicherungsspeicher verwenden.</span><span class="sxs-lookup"><span data-stu-id="8e183-193">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8e183-194">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="8e183-194">Additional resources</span></span>

* [<span data-ttu-id="8e183-195">Redis-Cache in Azure</span><span class="sxs-lookup"><span data-stu-id="8e183-195">Redis Cache on Azure</span></span>](https://azure.microsoft.com/documentation/services/redis-cache/)
* [<span data-ttu-id="8e183-196">SQL­Datenbank in Azure</span><span class="sxs-lookup"><span data-stu-id="8e183-196">SQL Database on Azure</span></span>](https://azure.microsoft.com/documentation/services/sql-database/)
* <span data-ttu-id="8e183-197">[ASP.NET Core-Anbieter von IDistributedCache für NCache in Webfarmen](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache auf GitHub](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="8e183-197">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

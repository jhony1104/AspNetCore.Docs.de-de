---
title: Verteiltes Zwischenspeichern in ASP.net Core
author: guardrex
description: Erfahren Sie, wie Sie einen ASP.net Core verteilten Cache verwenden können, um die Leistung und Skalierbarkeit der APP zu verbessern, insbesondere in einer Cloud-oder Serverfarm Umgebung.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/22/2020
uid: performance/caching/distributed
ms.openlocfilehash: 3e039a26505aed1bcc0299880039760fef19fd67
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76727242"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="1e14a-103">Verteiltes Zwischenspeichern in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="1e14a-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="1e14a-104">Von [Luke Latham](https://github.com/guardrex), [Muhsin Nasir](https://github.com/mohsinnasir)und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="1e14a-104">By [Luke Latham](https://github.com/guardrex), [Mohsin Nasir](https://github.com/mohsinnasir), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="1e14a-105">Bei einem verteilten Cache handelt es sich um einen Cache, der von mehreren App-Servern gemeinsam verwendet wird, die in der Regel als externer Dienst für die App-Server verwaltet werden</span><span class="sxs-lookup"><span data-stu-id="1e14a-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="1e14a-106">Ein verteilter Cache kann die Leistung und Skalierbarkeit einer ASP.net Core-App verbessern, insbesondere wenn die APP von einem Cloud-Dienst oder einer Serverfarm gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="1e14a-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="1e14a-107">Ein verteilter Cache hat mehrere Vorteile gegenüber anderen cachingszenarien, in denen zwischengespeicherte Daten auf einzelnen App-Servern gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="1e14a-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="1e14a-108">Wenn zwischengespeicherte Daten verteilt werden, sind die Daten wie folgt:</span><span class="sxs-lookup"><span data-stu-id="1e14a-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="1e14a-109">Ist *in* allen Anforderungen an mehrere Server konsistent (konsistent).</span><span class="sxs-lookup"><span data-stu-id="1e14a-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="1e14a-110">Überstehen Serverneustarts und App-bereit Stellungen.</span><span class="sxs-lookup"><span data-stu-id="1e14a-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="1e14a-111">Verwendet keinen lokalen Arbeitsspeicher.</span><span class="sxs-lookup"><span data-stu-id="1e14a-111">Doesn't use local memory.</span></span>

<span data-ttu-id="1e14a-112">Die Konfiguration verteilter Caches ist Implementierungs spezifisch.</span><span class="sxs-lookup"><span data-stu-id="1e14a-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="1e14a-113">In diesem Artikel wird beschrieben, wie Sie SQL Server und verteilte redis-Caches konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="1e14a-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="1e14a-114">Implementierungen von Drittanbietern sind ebenfalls verfügbar, wie z. b. [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache auf GitHub](https://github.com/Alachisoft/NCache)).</span><span class="sxs-lookup"><span data-stu-id="1e14a-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="1e14a-115">Unabhängig davon, welche Implementierung ausgewählt ist, interagiert die APP mit dem Cache mithilfe der <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>-Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="1e14a-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="1e14a-116">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1e14a-116">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1e14a-117">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="1e14a-117">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1e14a-118">Um einen SQL Server verteilten Cache zu verwenden, fügen Sie dem Paket " [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) " einen Paket Verweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="1e14a-118">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="1e14a-119">Um einen verteilten redis-Cache zu verwenden, fügen Sie dem Paket [Microsoft. Extensions. Caching. stackexchangeredis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) einen Paket Verweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="1e14a-119">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span>

<span data-ttu-id="1e14a-120">Fügen Sie zum Verwenden von NCache-verteiltem Cache einen Paket Verweis zum [ncache. Microsoft. Extensions. Caching. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) -Paket hinzu.</span><span class="sxs-lookup"><span data-stu-id="1e14a-120">To use NCache distributed cache, add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="1e14a-121">Wenn Sie einen SQL Server verteilten Cache verwenden möchten, verweisen Sie auf das [Metapaket "Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app) ", oder fügen Sie dem Paket " [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) " einen Paket Verweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="1e14a-121">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="1e14a-122">Um einen verteilten redis-Cache zu verwenden, verweisen Sie auf das [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app) , und fügen Sie dem Paket [Microsoft. Extensions. Caching. stackexchangeredis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) einen Paket Verweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="1e14a-122">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="1e14a-123">Das redis-Paket ist nicht im `Microsoft.AspNetCore.App` Paket enthalten, daher müssen Sie das redis-Paket separat in der Projektdatei referenzieren.</span><span class="sxs-lookup"><span data-stu-id="1e14a-123">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="1e14a-124">Um den verteilten NCache-Cache zu verwenden, verweisen Sie auf das [Metapaket "Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app) ", und fügen Sie dem [ncache. Microsoft. Extensions. Caching. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) -Paket einen Paket Verweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="1e14a-124">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="1e14a-125">Das NCache-Paket ist nicht im `Microsoft.AspNetCore.App` Paket enthalten, daher müssen Sie das NCache-Paket separat in der Projektdatei referenzieren.</span><span class="sxs-lookup"><span data-stu-id="1e14a-125">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="1e14a-126">Wenn Sie einen SQL Server verteilten Cache verwenden möchten, verweisen Sie auf das [Metapaket "Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app) ", oder fügen Sie dem Paket " [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) " einen Paket Verweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="1e14a-126">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="1e14a-127">Um einen verteilten redis-Cache zu verwenden, verweisen Sie auf das [Metapaket "Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app) ", und fügen Sie dem Paket " [Microsoft. Extensions. Caching. redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) " einen Paket Verweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="1e14a-127">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="1e14a-128">Das redis-Paket ist nicht im `Microsoft.AspNetCore.App` Paket enthalten, daher müssen Sie das redis-Paket separat in der Projektdatei referenzieren.</span><span class="sxs-lookup"><span data-stu-id="1e14a-128">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="1e14a-129">Um den verteilten NCache-Cache zu verwenden, verweisen Sie auf das [Metapaket "Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app) ", und fügen Sie dem [ncache. Microsoft. Extensions. Caching. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) -Paket einen Paket Verweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="1e14a-129">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="1e14a-130">Das NCache-Paket ist nicht im `Microsoft.AspNetCore.App` Paket enthalten, daher müssen Sie das NCache-Paket separat in der Projektdatei referenzieren.</span><span class="sxs-lookup"><span data-stu-id="1e14a-130">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

::: moniker-end

## <a name="idistributedcache-interface"></a><span data-ttu-id="1e14a-131">Idistributedcache-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="1e14a-131">IDistributedCache interface</span></span>

<span data-ttu-id="1e14a-132">Die <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>-Schnittstelle stellt die folgenden Methoden zum Bearbeiten von Elementen in der Implementierung verteilter Caches bereit:</span><span class="sxs-lookup"><span data-stu-id="1e14a-132">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="1e14a-133"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>akzeptiert <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; einen Zeichen folgen Schlüssel und ruft ein zwischengespeichertes Element als `byte[]` Array ab, wenn es im Cache gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="1e14a-133"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="1e14a-134"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>fügt <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; dem Cache ein Element (als `byte[]` Array) mit einem Zeichen folgen Schlüssel hinzu.</span><span class="sxs-lookup"><span data-stu-id="1e14a-134"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="1e14a-135"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>aktualisiert <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; ein Element im Cache basierend auf dessen Schlüssel, wobei das gleitende Ablauf Timeout (sofern vorhanden) zurückgesetzt wird.</span><span class="sxs-lookup"><span data-stu-id="1e14a-135"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="1e14a-136"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; ein Cache Element auf der Grundlage des zugehörigen Zeichen folgen Schlüssels entfernt.</span><span class="sxs-lookup"><span data-stu-id="1e14a-136"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="1e14a-137">Einrichten verteilter Cache Dienste</span><span class="sxs-lookup"><span data-stu-id="1e14a-137">Establish distributed caching services</span></span>

<span data-ttu-id="1e14a-138">Registrieren Sie eine Implementierung von <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="1e14a-138">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1e14a-139">Die in diesem Thema beschriebenen Framework-Implementierungen umfassen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="1e14a-139">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="1e14a-140">Verteilter Speicher Cache</span><span class="sxs-lookup"><span data-stu-id="1e14a-140">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="1e14a-141">Verteilter SQL Server Cache</span><span class="sxs-lookup"><span data-stu-id="1e14a-141">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="1e14a-142">Verteilter redis-Cache</span><span class="sxs-lookup"><span data-stu-id="1e14a-142">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="1e14a-143">Verteilter NCache-Cache</span><span class="sxs-lookup"><span data-stu-id="1e14a-143">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="1e14a-144">Verteilter Speicher Cache</span><span class="sxs-lookup"><span data-stu-id="1e14a-144">Distributed Memory Cache</span></span>

<span data-ttu-id="1e14a-145">Der verteilte Arbeitsspeicher Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) ist eine vom Framework bereitgestellte Implementierung von <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, die Elemente im Arbeitsspeicher speichert.</span><span class="sxs-lookup"><span data-stu-id="1e14a-145">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="1e14a-146">Der verteilte Arbeitsspeicher Cache ist kein tatsächlicher verteilter Cache.</span><span class="sxs-lookup"><span data-stu-id="1e14a-146">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="1e14a-147">Zwischengespeicherte Elemente werden von der app-Instanz auf dem Server gespeichert, auf dem die app ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="1e14a-147">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="1e14a-148">Der verteilte Arbeitsspeicher Cache ist eine nützliche Implementierung:</span><span class="sxs-lookup"><span data-stu-id="1e14a-148">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="1e14a-149">In Entwicklungs-und Testszenarien.</span><span class="sxs-lookup"><span data-stu-id="1e14a-149">In development and testing scenarios.</span></span>
* <span data-ttu-id="1e14a-150">Wenn ein einzelner Server in der Produktionsumgebung verwendet wird, ist der Arbeitsspeicher Verbrauch kein Problem.</span><span class="sxs-lookup"><span data-stu-id="1e14a-150">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="1e14a-151">Durch das Implementieren des Cache für verteilte Arbeitsspeicher wird der zwischengespeicherte Datenspeicher</span><span class="sxs-lookup"><span data-stu-id="1e14a-151">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="1e14a-152">Sie ermöglicht die Implementierung einer echten verteilten cachinglösung in Zukunft, wenn mehrere Knoten oder Fehlertoleranz erforderlich werden.</span><span class="sxs-lookup"><span data-stu-id="1e14a-152">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="1e14a-153">Die Beispiel-App nutzt den verteilten Arbeitsspeicher Cache, wenn die app in `Startup.ConfigureServices`in der Entwicklungsumgebung ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="1e14a-153">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

::: moniker-end

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="1e14a-154">Verteilter SQL Server Cache</span><span class="sxs-lookup"><span data-stu-id="1e14a-154">Distributed SQL Server Cache</span></span>

<span data-ttu-id="1e14a-155">Die Implementierung des verteilten SQL Server Caches (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) ermöglicht es dem verteilten Cache, eine SQL Server Datenbank als Sicherungs Speicher zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="1e14a-155">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="1e14a-156">Um eine SQL Server zwischengespeicherte Element Tabelle in einer SQL Server Instanz zu erstellen, können Sie das `sql-cache` Tool verwenden.</span><span class="sxs-lookup"><span data-stu-id="1e14a-156">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="1e14a-157">Das Tool erstellt eine Tabelle mit dem Namen und dem Schema, die Sie angeben.</span><span class="sxs-lookup"><span data-stu-id="1e14a-157">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="1e14a-158">Erstellen Sie eine Tabelle in SQL Server, indem Sie den `sql-cache create`-Befehl ausführen.</span><span class="sxs-lookup"><span data-stu-id="1e14a-158">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="1e14a-159">Geben Sie die SQL Server Instanz (`Data Source`), die Datenbank (`Initial Catalog`), das Schema (z. b. `dbo`) und den Tabellennamen (z. b. `TestCache`) an:</span><span class="sxs-lookup"><span data-stu-id="1e14a-159">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="1e14a-160">Eine Meldung wird protokolliert, um anzuzeigen, dass das Tool erfolgreich war:</span><span class="sxs-lookup"><span data-stu-id="1e14a-160">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="1e14a-161">Die vom `sql-cache` Tool erstellte Tabelle hat das folgende Schema:</span><span class="sxs-lookup"><span data-stu-id="1e14a-161">The table created by the `sql-cache` tool has the following schema:</span></span>

![SQLServer-Cache Tabelle](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="1e14a-163">Eine APP sollte Cache Werte mit einer Instanz von <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>und nicht mit einer <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="1e14a-163">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="1e14a-164">Die Beispiel-App implementiert <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in einer nicht Entwicklungsumgebung in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1e14a-164">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="1e14a-165">Eine <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (und optional, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> und <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) wird in der Regel außerhalb der Quell Code Verwaltung gespeichert (z. b. durch den [Geheimnis-Manager](xref:security/app-secrets) oder in *appSettings. JSON* gespeichert,/*appSettings. { Umgebung}. JSON* -Dateien).</span><span class="sxs-lookup"><span data-stu-id="1e14a-165">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="1e14a-166">Die Verbindungs Zeichenfolge kann Anmelde Informationen enthalten, die aus den Quell Code Verwaltungssystemen ausgeschlossen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="1e14a-166">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="1e14a-167">Verteilte redis Cache</span><span class="sxs-lookup"><span data-stu-id="1e14a-167">Distributed Redis Cache</span></span>

<span data-ttu-id="1e14a-168">[Redis](https://redis.io/) ist ein Open Source-Datenspeicher im Arbeitsspeicher, der häufig als verteilter Cache verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="1e14a-168">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="1e14a-169">Sie können redis lokal verwenden, und Sie können eine [Azure redis Cache](https://azure.microsoft.com/services/cache/) für eine in Azure gehostete ASP.net Core-App konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="1e14a-169">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1e14a-170">Eine App konfiguriert die Cache Implementierung mithilfe einer <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> Instanz (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in einer nicht Entwicklungsumgebung in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1e14a-170">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="1e14a-171">Eine App konfiguriert die Cache Implementierung mithilfe einer <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> Instanz (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in einer nicht Entwicklungsumgebung in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1e14a-171">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="1e14a-172">Eine App konfiguriert die Cache Implementierung mithilfe einer <xref:Microsoft.Extensions.Caching.Redis.RedisCache> Instanz (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span><span class="sxs-lookup"><span data-stu-id="1e14a-172">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

::: moniker-end

<span data-ttu-id="1e14a-173">So installieren Sie redis auf dem lokalen Computer:</span><span class="sxs-lookup"><span data-stu-id="1e14a-173">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="1e14a-174">Installieren Sie das [Chocolatey redis-Paket](https://chocolatey.org/packages/redis-64/).</span><span class="sxs-lookup"><span data-stu-id="1e14a-174">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="1e14a-175">Führen Sie `redis-server` über eine Eingabeaufforderung aus.</span><span class="sxs-lookup"><span data-stu-id="1e14a-175">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="1e14a-176">Verteilter NCache-Cache</span><span class="sxs-lookup"><span data-stu-id="1e14a-176">Distributed NCache Cache</span></span>

<span data-ttu-id="1e14a-177">[NCache](https://github.com/Alachisoft/NCache) ist ein Open Source-verteilter, in-Memory-Cache, der System intern in .net und .net Core entwickelt wurde.</span><span class="sxs-lookup"><span data-stu-id="1e14a-177">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="1e14a-178">NCache funktioniert sowohl lokal als auch als verteilter Cache Cluster für eine ASP.net Core-APP, die in Azure oder auf anderen Hostingplattformen ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="1e14a-178">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="1e14a-179">Informationen zum Installieren und Konfigurieren von NCache auf dem lokalen Computer finden Sie im Leitfaden zu den ersten Schritten mit [NCache für Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span><span class="sxs-lookup"><span data-stu-id="1e14a-179">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="1e14a-180">So konfigurieren Sie NCache:</span><span class="sxs-lookup"><span data-stu-id="1e14a-180">To configure NCache:</span></span>

1. <span data-ttu-id="1e14a-181">Installieren [Sie NCache Open Source nuget](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span><span class="sxs-lookup"><span data-stu-id="1e14a-181">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="1e14a-182">Konfigurieren Sie den Cache Cluster in " [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)".</span><span class="sxs-lookup"><span data-stu-id="1e14a-182">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="1e14a-183">Fügen Sie `Startup.ConfigureServices` den folgenden Code zu folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="1e14a-183">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="1e14a-184">Verwenden des verteilten Caches</span><span class="sxs-lookup"><span data-stu-id="1e14a-184">Use the distributed cache</span></span>

<span data-ttu-id="1e14a-185">Um die <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>-Schnittstelle zu verwenden, fordern Sie eine Instanz von <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> von einem beliebigen Konstruktor in der APP an.</span><span class="sxs-lookup"><span data-stu-id="1e14a-185">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="1e14a-186">Die-Instanz wird von der [Abhängigkeitsinjektion (di)](xref:fundamentals/dependency-injection)bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="1e14a-186">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1e14a-187">Wenn die Beispiel-App gestartet wird, wird <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.Configure`eingefügt.</span><span class="sxs-lookup"><span data-stu-id="1e14a-187">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="1e14a-188">Die aktuelle Zeit wird mithilfe von <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> zwischengespeichert (Weitere Informationen finden Sie unter [generischer Host: ihostapplicationlifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span><span class="sxs-lookup"><span data-stu-id="1e14a-188">The current time is cached using <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (for more information, see [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1e14a-189">Wenn die Beispiel-App gestartet wird, wird <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.Configure`eingefügt.</span><span class="sxs-lookup"><span data-stu-id="1e14a-189">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="1e14a-190">Die aktuelle Zeit wird mithilfe von <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> zwischengespeichert (Weitere Informationen finden Sie unter [Webhost: iapplicationlifetime-Schnittstelle](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span><span class="sxs-lookup"><span data-stu-id="1e14a-190">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

::: moniker-end

<span data-ttu-id="1e14a-191">Die Beispiel-App Fügt <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in die `IndexModel` ein, die von der Index Seite verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="1e14a-191">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="1e14a-192">Jedes Mal, wenn die Index Seite geladen wird, wird der Cache für die zwischengespeicherte Zeit in `OnGetAsync`geprüft.</span><span class="sxs-lookup"><span data-stu-id="1e14a-192">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="1e14a-193">Wenn die zwischengespeicherte Zeit nicht abgelaufen ist, wird die Uhrzeit angezeigt.</span><span class="sxs-lookup"><span data-stu-id="1e14a-193">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="1e14a-194">Wenn 20 Sekunden seit dem letzten Zugriff auf die zwischengespeicherte Zeit (beim letzten Laden dieser Seite) verstrichen sind, wird auf der Seite die *zwischengespeicherte Zeit abgelaufen*angezeigt.</span><span class="sxs-lookup"><span data-stu-id="1e14a-194">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="1e14a-195">Aktualisieren Sie die zwischengespeicherte Zeit sofort auf die aktuelle Zeit, indem Sie die Schaltfläche **zwischengespeicherte Zeit zurücksetzen** auswählen.</span><span class="sxs-lookup"><span data-stu-id="1e14a-195">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="1e14a-196">Die Schaltfläche löst die `OnPostResetCachedTime` Handlermethode aus.</span><span class="sxs-lookup"><span data-stu-id="1e14a-196">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="1e14a-197">Es ist nicht erforderlich, eine Singleton-oder Gültigkeitsdauer für <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Instanzen (zumindest für die integrierten Implementierungen) zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="1e14a-197">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="1e14a-198">Sie können auch immer dann eine <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Instanz erstellen, wenn Sie eine Instanz benötigen, anstatt di zu verwenden, aber das Erstellen einer Instanz im Code kann dazu führen, dass Ihr Code schwieriger zu testen ist und gegen das [explizite Abhängigkeits Prinzip](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)verstößt.</span><span class="sxs-lookup"><span data-stu-id="1e14a-198">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="1e14a-199">Empfehlungen</span><span class="sxs-lookup"><span data-stu-id="1e14a-199">Recommendations</span></span>

<span data-ttu-id="1e14a-200">Beachten Sie Folgendes, wenn Sie entscheiden, welche Implementierung von <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> für Ihre APP am besten geeignet ist:</span><span class="sxs-lookup"><span data-stu-id="1e14a-200">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="1e14a-201">Vorhandene Infrastruktur</span><span class="sxs-lookup"><span data-stu-id="1e14a-201">Existing infrastructure</span></span>
* <span data-ttu-id="1e14a-202">Leistungsanforderungen</span><span class="sxs-lookup"><span data-stu-id="1e14a-202">Performance requirements</span></span>
* <span data-ttu-id="1e14a-203">Kosten</span><span class="sxs-lookup"><span data-stu-id="1e14a-203">Cost</span></span>
* <span data-ttu-id="1e14a-204">Team Darstellung</span><span class="sxs-lookup"><span data-stu-id="1e14a-204">Team experience</span></span>

<span data-ttu-id="1e14a-205">Zwischen Speicherungs Lösungen basieren in der Regel auf in-Memory-Speicher, um das schnelle Abrufen von zwischengespeicherten Daten zu ermöglichen, aber der Arbeitsspeicher ist eine begrenzte Ressource und ist aufwändig</span><span class="sxs-lookup"><span data-stu-id="1e14a-205">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="1e14a-206">Speichert nur häufig verwendete Daten in einem Cache.</span><span class="sxs-lookup"><span data-stu-id="1e14a-206">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="1e14a-207">Im Allgemeinen bietet ein redis Cache einen höheren Durchsatz und eine niedrigere Latenz als bei einem SQL Server Cache.</span><span class="sxs-lookup"><span data-stu-id="1e14a-207">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="1e14a-208">Es ist jedoch normalerweise ein Benchmarkwert erforderlich, um die Leistungsmerkmale von Cache Strategien zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="1e14a-208">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="1e14a-209">Wenn SQL Server als Sicherungs Speicher für verteilte Caches verwendet wird, kann sich die Verwendung der gleichen Datenbank für den Cache und den normalen Datenspeicher und-Abruf der APP negativ auf die Leistung beider Anwendungen auswirken.</span><span class="sxs-lookup"><span data-stu-id="1e14a-209">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="1e14a-210">Es wird empfohlen, für den Sicherungs Speicher im verteilten Cache eine dedizierte SQL Server Instanz zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="1e14a-210">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1e14a-211">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="1e14a-211">Additional resources</span></span>

* [<span data-ttu-id="1e14a-212">Redis Cache in Azure</span><span class="sxs-lookup"><span data-stu-id="1e14a-212">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="1e14a-213">SQL-Datenbank in Azure</span><span class="sxs-lookup"><span data-stu-id="1e14a-213">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="1e14a-214">[ASP.net Core idistributedcache-Anbieter für NCache in Webfarmen](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache auf GitHub](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="1e14a-214">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

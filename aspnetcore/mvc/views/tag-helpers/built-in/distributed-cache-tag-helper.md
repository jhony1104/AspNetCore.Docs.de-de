---
title: Taghilfsprogramm für verteilten Cache in ASP.NET Core
author: pkellner
description: Erfahren Sie, wie das Taghilfsprogramm für verteilten Cache verwendet wird.
ms.author: riande
ms.custom: mvc
ms.date: 01/24/2020
uid: mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper
ms.openlocfilehash: f5957adf3cef8966812a1bf0cbc6b2627d19d026
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653791"
---
# <a name="distributed-cache-tag-helper-in-aspnet-core"></a><span data-ttu-id="d4163-103">Taghilfsprogramm für verteilten Cache in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d4163-103">Distributed Cache Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="d4163-104">Von [Peter Kellner](https://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="d4163-104">By [Peter Kellner](https://peterkellner.net)</span></span>

<span data-ttu-id="d4163-105">Durch das Taghilfsprogramm für verteilten Cache kann die Leistung Ihrer ASP.NET Core-App erheblich verbessert werden, indem deren Inhalte in einer verteilten Cachequelle zwischengespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="d4163-105">The Distributed Cache Tag Helper provides the ability to dramatically improve the performance of your ASP.NET Core app by caching its content to a distributed cache source.</span></span>

<span data-ttu-id="d4163-106">Eine Übersicht der Taghilfsprogramme finden Sie unter <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="d4163-106">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="d4163-107">Das Taghilfsprogramm für verteilten Cache erbt von derselben Basisklasse wie das Cache-Taghilfsprogramm.</span><span class="sxs-lookup"><span data-stu-id="d4163-107">The Distributed Cache Tag Helper inherits from the same base class as the Cache Tag Helper.</span></span> <span data-ttu-id="d4163-108">Alle Attribute des [Cache-Taghilfsprogramms](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) sind für das Taghilfsprogramm für verteilten Cache verfügbar.</span><span class="sxs-lookup"><span data-stu-id="d4163-108">All of the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) attributes are available to the Distributed Tag Helper.</span></span>

<span data-ttu-id="d4163-109">Das Taghilfsprogramm für verteilten Cache verwendet die [Konstruktorinjektion](xref:fundamentals/dependency-injection#constructor-injection-behavior).</span><span class="sxs-lookup"><span data-stu-id="d4163-109">The Distributed Cache Tag Helper uses [constructor injection](xref:fundamentals/dependency-injection#constructor-injection-behavior).</span></span> <span data-ttu-id="d4163-110">Die Schnittstelle <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> wird an den Konstruktor des Taghilfsprogramms für verteilten Cache übergeben.</span><span class="sxs-lookup"><span data-stu-id="d4163-110">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface is passed into the Distributed Cache Tag Helper's constructor.</span></span> <span data-ttu-id="d4163-111">Wenn keine konkrete Implementierung von `IDistributedCache` in `Startup.ConfigureServices` (*Startup.cs*) erstellt wurde, verwendet das Taghilfsprogramm für verteilten Cache zum Speichern von zwischengespeicherten Daten denselben In-Memory-Anbieter wie das [Cache-Taghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="d4163-111">If no concrete implementation of `IDistributedCache` is created in `Startup.ConfigureServices` (*Startup.cs*), the Distributed Cache Tag Helper uses the same in-memory provider for storing cached data as the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

## <a name="distributed-cache-tag-helper-attributes"></a><span data-ttu-id="d4163-112">Attribute des Taghilfsprogramms für verteilten Cache</span><span class="sxs-lookup"><span data-stu-id="d4163-112">Distributed Cache Tag Helper Attributes</span></span>

### <a name="attributes-shared-with-the-cache-tag-helper"></a><span data-ttu-id="d4163-113">Attribute, die für das Cache-Taghilfsprogramm freigegeben sind:</span><span class="sxs-lookup"><span data-stu-id="d4163-113">Attributes shared with the Cache Tag Helper</span></span>

* `enabled`
* `expires-on`
* `expires-after`
* `expires-sliding`
* `vary-by-header`
* `vary-by-query`
* `vary-by-route`
* `vary-by-cookie`
* `vary-by-user`
* `vary-by priority`

<span data-ttu-id="d4163-114">Das Taghilfsprogramm für verteilten Cache erbt von derselben Klasse wie das Cache-Taghilfsprogramm.</span><span class="sxs-lookup"><span data-stu-id="d4163-114">The Distributed Cache Tag Helper inherits from the same class as Cache Tag Helper.</span></span> <span data-ttu-id="d4163-115">Beschreibungen dieser Attribute finden Sie im [Cache-Taghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="d4163-115">For descriptions of these attributes, see the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

### <a name="name"></a><span data-ttu-id="d4163-116">name</span><span class="sxs-lookup"><span data-stu-id="d4163-116">name</span></span>

| <span data-ttu-id="d4163-117">Attributtyp</span><span class="sxs-lookup"><span data-stu-id="d4163-117">Attribute Type</span></span> | <span data-ttu-id="d4163-118">Beispiel</span><span class="sxs-lookup"><span data-stu-id="d4163-118">Example</span></span>                               |
| -------------- | ------------------------------------- |
| <span data-ttu-id="d4163-119">String</span><span class="sxs-lookup"><span data-stu-id="d4163-119">String</span></span>         | `my-distributed-cache-unique-key-101` |

<span data-ttu-id="d4163-120">`name` ist erforderlich.</span><span class="sxs-lookup"><span data-stu-id="d4163-120">`name` is required.</span></span> <span data-ttu-id="d4163-121">Das `name`-Attribut wird als Schlüssel für die einzelnen gespeicherten Cacheinstanzen verwendet.</span><span class="sxs-lookup"><span data-stu-id="d4163-121">The `name` attribute is used as a key for each stored cache instance.</span></span> <span data-ttu-id="d4163-122">Im Gegensatz zum Cache-Taghilfsprogramm, das jeder Instanz basierend auf dem Namen der Razor-Seite und dem Speicherort auf der Razor-Seite einen Cacheschlüssel zuweist, basieren die Schlüssel des Taghilfsprogramms für verteilten Cache nur auf dem Attribut `name`.</span><span class="sxs-lookup"><span data-stu-id="d4163-122">Unlike the Cache Tag Helper that assigns a cache key to each instance based on the Razor page name and location in the Razor page, the Distributed Cache Tag Helper only bases its key on the attribute `name`.</span></span>

<span data-ttu-id="d4163-123">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d4163-123">Example:</span></span>

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</distributed-cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a><span data-ttu-id="d4163-124">Implementierungen von IDistributedCache im Taghilfsprogramm für verteilten Cache</span><span class="sxs-lookup"><span data-stu-id="d4163-124">Distributed Cache Tag Helper IDistributedCache implementations</span></span>

<span data-ttu-id="d4163-125">In ASP.NET Core gibt es zwei Implementierungen von <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>.</span><span class="sxs-lookup"><span data-stu-id="d4163-125">There are two implementations of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> built in to ASP.NET Core.</span></span> <span data-ttu-id="d4163-126">Eine basiert auf SQL Server, die andere auf Redis.</span><span class="sxs-lookup"><span data-stu-id="d4163-126">One is based on SQL Server, and the other is based on Redis.</span></span> <span data-ttu-id="d4163-127">Drittanbieterimplementierungen sind ebenfalls verfügbar, z. B. [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html).</span><span class="sxs-lookup"><span data-stu-id="d4163-127">Third-party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html).</span></span> <span data-ttu-id="d4163-128">Ausführliche Informationen zu diesen Implementierungen finden Sie unter <xref:performance/caching/distributed>.</span><span class="sxs-lookup"><span data-stu-id="d4163-128">Details of these implementations can be found at <xref:performance/caching/distributed>.</span></span> <span data-ttu-id="d4163-129">Für beide Implementierungen wird eine Instanz von `IDistributedCache` in `Startup` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="d4163-129">Both implementations involve setting an instance of `IDistributedCache` in `Startup`.</span></span>

<span data-ttu-id="d4163-130">Es gibt keine Tagattribute, die einer bestimmten Implementierung von `IDistributedCache` explizit zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="d4163-130">There are no tag attributes specifically associated with using any specific implementation of `IDistributedCache`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d4163-131">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d4163-131">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:fundamentals/dependency-injection>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>

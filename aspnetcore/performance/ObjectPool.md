---
title: Objekt-Wiederverwendung mit Objektpool in ASP.NET Core
author: rick-anderson
description: Tipps zum Verbessern der Leistung in ASP.NET Core-apps, die mit der Objektpool.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
uid: performance/ObjectPool
ms.openlocfilehash: 771f19e54a908b8b2cd85ff72f368f16e94a2310
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815523"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a><span data-ttu-id="9427f-103">Objekt-Wiederverwendung mit Objektpool in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9427f-103">Object reuse with ObjectPool in ASP.NET Core</span></span>

<span data-ttu-id="9427f-104">Durch [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9427f-104">By [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9427f-105"><xref:Microsoft.Extensions.ObjectPool> ist Teil der ASP.NET Core-Infrastruktur, die unterstützt wird, halten eine Gruppe von Objekten im Arbeitsspeicher für die Wiederverwendung, anstatt können die Objekte in die Garbage erfasst.</span><span class="sxs-lookup"><span data-stu-id="9427f-105"><xref:Microsoft.Extensions.ObjectPool> is part of the ASP.NET Core infrastructure that supports keeping a group of objects in memory for reuse rather than allowing the objects to be garbage collected.</span></span>

<span data-ttu-id="9427f-106">Möglicherweise möchten den-Objektpool verwenden, wenn die Objekte, die verwaltet werden:</span><span class="sxs-lookup"><span data-stu-id="9427f-106">You might want to use the object pool if the objects that are being managed are:</span></span>

- <span data-ttu-id="9427f-107">Teuer zuordnen/initialisiert werden.</span><span class="sxs-lookup"><span data-stu-id="9427f-107">Expensive to allocate/initialize.</span></span>
- <span data-ttu-id="9427f-108">Stellen Sie eine begrenzte Ressource dar.</span><span class="sxs-lookup"><span data-stu-id="9427f-108">Represent some limited resource.</span></span>
- <span data-ttu-id="9427f-109">Zuverlässig und häufig verwendet.</span><span class="sxs-lookup"><span data-stu-id="9427f-109">Used predictably and frequently.</span></span>

<span data-ttu-id="9427f-110">Das ASP.NET Core-Framework verwendet beispielsweise des Objektpools an einigen Stellen wiederverwenden <xref:System.Text.StringBuilder> Instanzen.</span><span class="sxs-lookup"><span data-stu-id="9427f-110">For example, the ASP.NET Core framework uses the object pool in some places to reuse <xref:System.Text.StringBuilder> instances.</span></span> <span data-ttu-id="9427f-111">`StringBuilder` Ordnet ein, und verwaltet seine eigenen Puffer zum Speichern von Zeichendaten enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="9427f-111">`StringBuilder` allocates and manages its own buffers to hold character data.</span></span> <span data-ttu-id="9427f-112">ASP.NET Core verwendet, regelmäßig `StringBuilder` zum Implementieren dieses features, und führt zu einer Leistungssteigerung wiederverwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9427f-112">ASP.NET Core regularly uses `StringBuilder` to implement features, and reusing them provides a performance benefit.</span></span>

<span data-ttu-id="9427f-113">Objektpooling nicht immer die Leistung verbessert:</span><span class="sxs-lookup"><span data-stu-id="9427f-113">Object pooling doesn't always improve performance:</span></span>

- <span data-ttu-id="9427f-114">Es sei denn, die Kosten der Initialisierung eines Objekts hoch ist, ist es in der Regel langsamer, um das Objekt aus dem Pool abzurufen.</span><span class="sxs-lookup"><span data-stu-id="9427f-114">Unless the initialization cost of an object is high, it's usually slower to get the object from the pool.</span></span>
- <span data-ttu-id="9427f-115">Durch den Pool verwalteten Objekte nicht aufgehoben, bis der Pool aufgehoben ist.</span><span class="sxs-lookup"><span data-stu-id="9427f-115">Objects managed by the pool aren't de-allocated until the pool is de-allocated.</span></span>

<span data-ttu-id="9427f-116">Verwenden Sie die Objektpooling nur nach dem Sammeln von Leistungsdaten mithilfe von realistischen Szenarien für Ihre app oder Bibliothek.</span><span class="sxs-lookup"><span data-stu-id="9427f-116">Use object pooling only after collecting performance data using realistic scenarios for your app or library.</span></span>

<span data-ttu-id="9427f-117">**WARNUNG: Die `ObjectPool` implementiert keine `IDisposable`. Verwenden es mit Typen, die Freigabe benötigen empfohlen nicht.**</span><span class="sxs-lookup"><span data-stu-id="9427f-117">**WARNING: The `ObjectPool` doesn't implement `IDisposable`. We don't recommend using it with types that need disposal.**</span></span>

<span data-ttu-id="9427f-118">**HINWEIS: Der Objektpool keine Begrenzung der Anzahl der Objekte, die er zuordnet, es legt einen Grenzwert für die Anzahl der Objekte, die es beibehalten wird.**</span><span class="sxs-lookup"><span data-stu-id="9427f-118">**NOTE: The ObjectPool doesn't place a limit on the number of objects that it will allocate, it places a limit on the number of objects it will retain.**</span></span>

## <a name="concepts"></a><span data-ttu-id="9427f-119">Konzepte</span><span class="sxs-lookup"><span data-stu-id="9427f-119">Concepts</span></span>

<span data-ttu-id="9427f-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> -die Pool-Abstraktion Basisobjekt.</span><span class="sxs-lookup"><span data-stu-id="9427f-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> - the basic object pool abstraction.</span></span> <span data-ttu-id="9427f-121">Zum Abrufen und Objekte zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="9427f-121">Used to get and return objects.</span></span>

<span data-ttu-id="9427f-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> – Implementieren Sie diese Option, um anzupassen, wie ein Objekt erstellt wird und wie es *zurücksetzen* Wenn an den Pool zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="9427f-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> - implement this to customize how an object is created and how it is *reset* when returned to the pool.</span></span> <span data-ttu-id="9427f-123">Dies kann in einem Pool-Objekt übergeben werden, die Sie direkt zu erstellen... oder</span><span class="sxs-lookup"><span data-stu-id="9427f-123">This can be passed into an object pool that you construct directly.... OR</span></span>

<span data-ttu-id="9427f-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> fungiert als Factory für die Erstellung von Objektpools.</span><span class="sxs-lookup"><span data-stu-id="9427f-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> acts as a factory for creating object pools.</span></span>
<!-- REview, there is no ObjectPoolProvider<T> -->

<span data-ttu-id="9427f-125">Der Objektpool kann in einer app auf verschiedene Weise verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="9427f-125">The ObjectPool can be used in an app in multiple ways:</span></span>

* <span data-ttu-id="9427f-126">Instanziieren einen Pool an.</span><span class="sxs-lookup"><span data-stu-id="9427f-126">Instantiating a pool.</span></span>
* <span data-ttu-id="9427f-127">Registrieren einen Pool in [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (DI) als eine Instanz.</span><span class="sxs-lookup"><span data-stu-id="9427f-127">Registering a pool in [Dependency injection](xref:fundamentals/dependency-injection) (DI) as an instance.</span></span>
* <span data-ttu-id="9427f-128">Registrieren der `ObjectPoolProvider<>` in DI, und verwenden ihn als Factory.</span><span class="sxs-lookup"><span data-stu-id="9427f-128">Registering the `ObjectPoolProvider<>` in DI and using it as a factory.</span></span>

## <a name="how-to-use-objectpool"></a><span data-ttu-id="9427f-129">Gewusst wie: Verwenden der Objektpool</span><span class="sxs-lookup"><span data-stu-id="9427f-129">How to use ObjectPool</span></span>

<span data-ttu-id="9427f-130">Rufen Sie <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> um ein Objekt abzurufen und <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> das Objekt zurück.</span><span class="sxs-lookup"><span data-stu-id="9427f-130">Call <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> to get an object and <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> to return the object.</span></span>  <span data-ttu-id="9427f-131">Es ist nicht erforderlich, dass Sie jedes Objekt zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="9427f-131">There's no requirement that you return every object.</span></span> <span data-ttu-id="9427f-132">Wenn Sie ein Objekt zurückgeben keine, werden Garbage Collection bereinigt.</span><span class="sxs-lookup"><span data-stu-id="9427f-132">If you don't return an object, it will be garbage collected.</span></span>

## <a name="objectpool-sample"></a><span data-ttu-id="9427f-133">Objektpool-Beispiel</span><span class="sxs-lookup"><span data-stu-id="9427f-133">ObjectPool sample</span></span>

<span data-ttu-id="9427f-134">Der folgende Code</span><span class="sxs-lookup"><span data-stu-id="9427f-134">The following code:</span></span>

* <span data-ttu-id="9427f-135">Fügt `ObjectPoolProvider` auf die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (DI)-Container.</span><span class="sxs-lookup"><span data-stu-id="9427f-135">Adds `ObjectPoolProvider` to the [Dependency injection](xref:fundamentals/dependency-injection) (DI) container.</span></span>
* <span data-ttu-id="9427f-136">Hinzugefügt und konfiguriert `ObjectPool<StringBuilder>` zu DI-Container.</span><span class="sxs-lookup"><span data-stu-id="9427f-136">Adds and configures `ObjectPool<StringBuilder>` to the DI container.</span></span>
* <span data-ttu-id="9427f-137">Fügt der `BirthdayMiddleware`.</span><span class="sxs-lookup"><span data-stu-id="9427f-137">Adds the `BirthdayMiddleware`.</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

<span data-ttu-id="9427f-138">Der folgende Code implementiert `BirthdayMiddleware`</span><span class="sxs-lookup"><span data-stu-id="9427f-138">The following code implements `BirthdayMiddleware`</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]

---
title: Wieder verwenden von Objekten mit Objectpool in ASP.net Core
author: rick-anderson
description: Tipps zum Steigern der Leistung in ASP.net Core-apps mit Objectpool.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
uid: performance/ObjectPool
ms.openlocfilehash: 771f19e54a908b8b2cd85ff72f368f16e94a2310
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654379"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a><span data-ttu-id="950f4-103">Wieder verwenden von Objekten mit Objectpool in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="950f4-103">Object reuse with ObjectPool in ASP.NET Core</span></span>

<span data-ttu-id="950f4-104">Von [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak)und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="950f4-104">By [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="950f4-105"><xref:Microsoft.Extensions.ObjectPool> ist Teil der ASP.net Core Infrastruktur, die das Beibehalten einer Gruppe von Objekten im Arbeitsspeicher für die Wiederverwendung unterstützt, anstatt zuzulassen, dass die Objekte in die Garbage Collection aufgenommen werden.</span><span class="sxs-lookup"><span data-stu-id="950f4-105"><xref:Microsoft.Extensions.ObjectPool> is part of the ASP.NET Core infrastructure that supports keeping a group of objects in memory for reuse rather than allowing the objects to be garbage collected.</span></span>

<span data-ttu-id="950f4-106">Möglicherweise möchten Sie den Objekt Pool verwenden, wenn die verwalteten Objekte wie folgt lauten:</span><span class="sxs-lookup"><span data-stu-id="950f4-106">You might want to use the object pool if the objects that are being managed are:</span></span>

- <span data-ttu-id="950f4-107">Das Zuordnen/initialisieren ist aufwendig.</span><span class="sxs-lookup"><span data-stu-id="950f4-107">Expensive to allocate/initialize.</span></span>
- <span data-ttu-id="950f4-108">Stellen Sie eine begrenzte Ressource dar.</span><span class="sxs-lookup"><span data-stu-id="950f4-108">Represent some limited resource.</span></span>
- <span data-ttu-id="950f4-109">Wird vorhersag Bar und häufig verwendet.</span><span class="sxs-lookup"><span data-stu-id="950f4-109">Used predictably and frequently.</span></span>

<span data-ttu-id="950f4-110">Beispielsweise verwendet das ASP.net Core Framework den Objekt Pool an manchen Stellen, um <xref:System.Text.StringBuilder> Instanzen wiederzuverwenden.</span><span class="sxs-lookup"><span data-stu-id="950f4-110">For example, the ASP.NET Core framework uses the object pool in some places to reuse <xref:System.Text.StringBuilder> instances.</span></span> <span data-ttu-id="950f4-111">`StringBuilder` ordnet und verwaltet seine eigenen Puffer zum Speichern von Zeichendaten.</span><span class="sxs-lookup"><span data-stu-id="950f4-111">`StringBuilder` allocates and manages its own buffers to hold character data.</span></span> <span data-ttu-id="950f4-112">ASP.net Core verwendet `StringBuilder` regelmäßig, um Features zu implementieren, und die erneute Verwendung bietet einen Leistungsvorteil.</span><span class="sxs-lookup"><span data-stu-id="950f4-112">ASP.NET Core regularly uses `StringBuilder` to implement features, and reusing them provides a performance benefit.</span></span>

<span data-ttu-id="950f4-113">Das Objekt Pooling verbessert nicht immer die Leistung:</span><span class="sxs-lookup"><span data-stu-id="950f4-113">Object pooling doesn't always improve performance:</span></span>

- <span data-ttu-id="950f4-114">Wenn die Initialisierungs Kosten eines Objekts nicht hoch sind, ist es normalerweise langsamer, das Objekt aus dem Pool zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="950f4-114">Unless the initialization cost of an object is high, it's usually slower to get the object from the pool.</span></span>
- <span data-ttu-id="950f4-115">Die Zuordnung von Objekten, die vom Pool verwaltet werden, wird erst aufgehoben, wenn die Zuordnung des Pools aufgehoben wird</span><span class="sxs-lookup"><span data-stu-id="950f4-115">Objects managed by the pool aren't de-allocated until the pool is de-allocated.</span></span>

<span data-ttu-id="950f4-116">Verwenden Sie das Objekt Pooling nur, nachdem Sie Leistungsdaten mithilfe realistischer Szenarien für Ihre APP oder Bibliothek gesammelt haben.</span><span class="sxs-lookup"><span data-stu-id="950f4-116">Use object pooling only after collecting performance data using realistic scenarios for your app or library.</span></span>

<span data-ttu-id="950f4-117">**Warnung: der `ObjectPool` implementiert `IDisposable`nicht. Es wird nicht empfohlen, es mit Typen zu verwenden, die eine Entsorgung benötigen.**</span><span class="sxs-lookup"><span data-stu-id="950f4-117">**WARNING: The `ObjectPool` doesn't implement `IDisposable`. We don't recommend using it with types that need disposal.**</span></span>

<span data-ttu-id="950f4-118">**Hinweis: die Anzahl der Objekte, die Sie zuordnen soll, wird von Objectpool nicht begrenzt, und die Anzahl der Objekte, die Sie beibehält, ist begrenzt.**</span><span class="sxs-lookup"><span data-stu-id="950f4-118">**NOTE: The ObjectPool doesn't place a limit on the number of objects that it will allocate, it places a limit on the number of objects it will retain.**</span></span>

## <a name="concepts"></a><span data-ttu-id="950f4-119">Konzepte</span><span class="sxs-lookup"><span data-stu-id="950f4-119">Concepts</span></span>

<span data-ttu-id="950f4-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>: die grundlegende Objekt Pool Abstraktion.</span><span class="sxs-lookup"><span data-stu-id="950f4-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> - the basic object pool abstraction.</span></span> <span data-ttu-id="950f4-121">Wird verwendet, um-Objekte zu erhalten und zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="950f4-121">Used to get and return objects.</span></span>

<span data-ttu-id="950f4-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> implementieren, um anzupassen, wie ein Objekt erstellt wird und wie es *zurückgesetzt* wird, wenn es an den Pool zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="950f4-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> - implement this to customize how an object is created and how it is *reset* when returned to the pool.</span></span> <span data-ttu-id="950f4-123">Dies kann an einen Objekt Pool übermittelt werden, den Sie direkt erstellen... Noch</span><span class="sxs-lookup"><span data-stu-id="950f4-123">This can be passed into an object pool that you construct directly.... OR</span></span>

<span data-ttu-id="950f4-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> fungiert als Factory zum Erstellen von Objekt Pools.</span><span class="sxs-lookup"><span data-stu-id="950f4-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> acts as a factory for creating object pools.</span></span>
<!-- REview, there is no ObjectPoolProvider<T> -->

<span data-ttu-id="950f4-125">Der Objectpool kann in einer APP auf verschiedene Weise verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="950f4-125">The ObjectPool can be used in an app in multiple ways:</span></span>

* <span data-ttu-id="950f4-126">Instanziieren eines Pools.</span><span class="sxs-lookup"><span data-stu-id="950f4-126">Instantiating a pool.</span></span>
* <span data-ttu-id="950f4-127">Registrieren eines Pools in [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (di) als-Instanz.</span><span class="sxs-lookup"><span data-stu-id="950f4-127">Registering a pool in [Dependency injection](xref:fundamentals/dependency-injection) (DI) as an instance.</span></span>
* <span data-ttu-id="950f4-128">Registrieren der `ObjectPoolProvider<>` in di und Verwendung als Factory.</span><span class="sxs-lookup"><span data-stu-id="950f4-128">Registering the `ObjectPoolProvider<>` in DI and using it as a factory.</span></span>

## <a name="how-to-use-objectpool"></a><span data-ttu-id="950f4-129">Verwenden von Objectpool</span><span class="sxs-lookup"><span data-stu-id="950f4-129">How to use ObjectPool</span></span>

<span data-ttu-id="950f4-130">Ruft <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> auf, um ein-Objekt abzurufen und <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*>, um das Objekt zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="950f4-130">Call <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> to get an object and <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> to return the object.</span></span>  <span data-ttu-id="950f4-131">Es ist nicht erforderlich, jedes Objekt zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="950f4-131">There's no requirement that you return every object.</span></span> <span data-ttu-id="950f4-132">Wenn Sie kein Objekt zurückgeben, wird die Garbage Collection durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="950f4-132">If you don't return an object, it will be garbage collected.</span></span>

## <a name="objectpool-sample"></a><span data-ttu-id="950f4-133">Objectpool-Beispiel</span><span class="sxs-lookup"><span data-stu-id="950f4-133">ObjectPool sample</span></span>

<span data-ttu-id="950f4-134">Der folgende Code:</span><span class="sxs-lookup"><span data-stu-id="950f4-134">The following code:</span></span>

* <span data-ttu-id="950f4-135">Fügt `ObjectPoolProvider` dem Container für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (di) hinzu.</span><span class="sxs-lookup"><span data-stu-id="950f4-135">Adds `ObjectPoolProvider` to the [Dependency injection](xref:fundamentals/dependency-injection) (DI) container.</span></span>
* <span data-ttu-id="950f4-136">Fügt `ObjectPool<StringBuilder>` dem di-Container hinzu und konfiguriert diese.</span><span class="sxs-lookup"><span data-stu-id="950f4-136">Adds and configures `ObjectPool<StringBuilder>` to the DI container.</span></span>
* <span data-ttu-id="950f4-137">Fügt die `BirthdayMiddleware`hinzu.</span><span class="sxs-lookup"><span data-stu-id="950f4-137">Adds the `BirthdayMiddleware`.</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

<span data-ttu-id="950f4-138">Der folgende Code implementiert `BirthdayMiddleware`</span><span class="sxs-lookup"><span data-stu-id="950f4-138">The following code implements `BirthdayMiddleware`</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]

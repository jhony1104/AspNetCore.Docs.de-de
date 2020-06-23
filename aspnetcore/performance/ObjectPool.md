---
title: Wieder verwenden von Objekten mit Objectpool in ASP.net Core
author: rick-anderson
description: Tipps zum Steigern der Leistung in ASP.net Core-apps mit Objectpool.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/ObjectPool
ms.openlocfilehash: 004ca5724517bf3fbf6512c0b9653793f4e0f702
ms.sourcegitcommit: dd2a1542a4a377123490034153368c135fdbd09e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85241010"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a><span data-ttu-id="10e25-103">Wieder verwenden von Objekten mit Objectpool in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="10e25-103">Object reuse with ObjectPool in ASP.NET Core</span></span>

<span data-ttu-id="10e25-104">Von [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak)und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="10e25-104">By [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="10e25-105"><xref:Microsoft.Extensions.ObjectPool>ist Teil der ASP.net Core-Infrastruktur, die unterstützt, dass eine Gruppe von Objekten für die Wiederverwendung im Speicher beibehalten wird, anstatt die Garbage Collection der Objekte zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="10e25-105"><xref:Microsoft.Extensions.ObjectPool> is part of the ASP.NET Core infrastructure that supports keeping a group of objects in memory for reuse rather than allowing the objects to be garbage collected.</span></span>

<span data-ttu-id="10e25-106">Möglicherweise möchten Sie den Objekt Pool verwenden, wenn die verwalteten Objekte wie folgt lauten:</span><span class="sxs-lookup"><span data-stu-id="10e25-106">You might want to use the object pool if the objects that are being managed are:</span></span>

- <span data-ttu-id="10e25-107">Das Zuordnen/initialisieren ist aufwendig.</span><span class="sxs-lookup"><span data-stu-id="10e25-107">Expensive to allocate/initialize.</span></span>
- <span data-ttu-id="10e25-108">Stellen Sie eine begrenzte Ressource dar.</span><span class="sxs-lookup"><span data-stu-id="10e25-108">Represent some limited resource.</span></span>
- <span data-ttu-id="10e25-109">Wird vorhersag Bar und häufig verwendet.</span><span class="sxs-lookup"><span data-stu-id="10e25-109">Used predictably and frequently.</span></span>

<span data-ttu-id="10e25-110">Beispielsweise verwendet das ASP.net Core Framework den Objekt Pool an manchen Stellen, um Instanzen wiederzuverwenden <xref:System.Text.StringBuilder> .</span><span class="sxs-lookup"><span data-stu-id="10e25-110">For example, the ASP.NET Core framework uses the object pool in some places to reuse <xref:System.Text.StringBuilder> instances.</span></span> <span data-ttu-id="10e25-111">`StringBuilder`ordnet und verwaltet eigene Puffer zum Speichern von Zeichendaten.</span><span class="sxs-lookup"><span data-stu-id="10e25-111">`StringBuilder` allocates and manages its own buffers to hold character data.</span></span> <span data-ttu-id="10e25-112">ASP.net Core werden regelmäßig verwendet `StringBuilder` , um Funktionen zu implementieren, und die Wiederverwendung bietet einen Leistungsvorteil.</span><span class="sxs-lookup"><span data-stu-id="10e25-112">ASP.NET Core regularly uses `StringBuilder` to implement features, and reusing them provides a performance benefit.</span></span>

<span data-ttu-id="10e25-113">Das Objekt Pooling verbessert nicht immer die Leistung:</span><span class="sxs-lookup"><span data-stu-id="10e25-113">Object pooling doesn't always improve performance:</span></span>

- <span data-ttu-id="10e25-114">Wenn die Initialisierungs Kosten eines Objekts nicht hoch sind, ist es normalerweise langsamer, das Objekt aus dem Pool zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="10e25-114">Unless the initialization cost of an object is high, it's usually slower to get the object from the pool.</span></span>
- <span data-ttu-id="10e25-115">Die Zuordnung von Objekten, die vom Pool verwaltet werden, wird erst aufgehoben, wenn die Zuordnung des Pools aufgehoben wird</span><span class="sxs-lookup"><span data-stu-id="10e25-115">Objects managed by the pool aren't de-allocated until the pool is de-allocated.</span></span>

<span data-ttu-id="10e25-116">Verwenden Sie das Objekt Pooling nur, nachdem Sie Leistungsdaten mithilfe realistischer Szenarien für Ihre APP oder Bibliothek gesammelt haben.</span><span class="sxs-lookup"><span data-stu-id="10e25-116">Use object pooling only after collecting performance data using realistic scenarios for your app or library.</span></span>

<span data-ttu-id="10e25-117">**Warnung: der `ObjectPool` implementiert nicht `IDisposable` . Es wird nicht empfohlen, es mit Typen zu verwenden, die eine Entsorgung benötigen.**</span><span class="sxs-lookup"><span data-stu-id="10e25-117">**WARNING: The `ObjectPool` doesn't implement `IDisposable`. We don't recommend using it with types that need disposal.**</span></span>

<span data-ttu-id="10e25-118">**Hinweis: die Anzahl der Objekte, die Sie zuordnen soll, wird von Objectpool nicht begrenzt, und die Anzahl der Objekte, die Sie beibehält, ist begrenzt.**</span><span class="sxs-lookup"><span data-stu-id="10e25-118">**NOTE: The ObjectPool doesn't place a limit on the number of objects that it will allocate, it places a limit on the number of objects it will retain.**</span></span>

## <a name="concepts"></a><span data-ttu-id="10e25-119">Konzepte</span><span class="sxs-lookup"><span data-stu-id="10e25-119">Concepts</span></span>

<span data-ttu-id="10e25-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>: die grundlegende Objekt Pool Abstraktion.</span><span class="sxs-lookup"><span data-stu-id="10e25-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> - the basic object pool abstraction.</span></span> <span data-ttu-id="10e25-121">Wird verwendet, um-Objekte zu erhalten und zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="10e25-121">Used to get and return objects.</span></span>

<span data-ttu-id="10e25-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601>-Implementieren Sie diese, um anzupassen, wie ein Objekt erstellt wird und wie es *zurückgesetzt* wird, wenn es an den Pool zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="10e25-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> - implement this to customize how an object is created and how it is *reset* when returned to the pool.</span></span> <span data-ttu-id="10e25-123">Dies kann an einen Objekt Pool übermittelt werden, den Sie direkt erstellen... Noch</span><span class="sxs-lookup"><span data-stu-id="10e25-123">This can be passed into an object pool that you construct directly.... OR</span></span>

<span data-ttu-id="10e25-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*>fungiert als Factory zum Erstellen von Objekt Pools.</span><span class="sxs-lookup"><span data-stu-id="10e25-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> acts as a factory for creating object pools.</span></span>
<!-- REview, there is no ObjectPoolProvider<T> -->

<span data-ttu-id="10e25-125">Der Objectpool kann in einer APP auf verschiedene Weise verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="10e25-125">The ObjectPool can be used in an app in multiple ways:</span></span>

* <span data-ttu-id="10e25-126">Instanziieren eines Pools.</span><span class="sxs-lookup"><span data-stu-id="10e25-126">Instantiating a pool.</span></span>
* <span data-ttu-id="10e25-127">Registrieren eines Pools in [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (di) als-Instanz.</span><span class="sxs-lookup"><span data-stu-id="10e25-127">Registering a pool in [Dependency injection](xref:fundamentals/dependency-injection) (DI) as an instance.</span></span>
* <span data-ttu-id="10e25-128">Registrieren von `ObjectPoolProvider<>` in di und Verwendung als Factory.</span><span class="sxs-lookup"><span data-stu-id="10e25-128">Registering the `ObjectPoolProvider<>` in DI and using it as a factory.</span></span>

## <a name="how-to-use-objectpool"></a><span data-ttu-id="10e25-129">Verwenden von Objectpool</span><span class="sxs-lookup"><span data-stu-id="10e25-129">How to use ObjectPool</span></span>

<span data-ttu-id="10e25-130">Ruft <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> auf, um ein-Objekt abzurufen und <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> das-Objekt zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="10e25-130">Call <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> to get an object and <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> to return the object.</span></span>  <span data-ttu-id="10e25-131">Es ist nicht erforderlich, jedes Objekt zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="10e25-131">There's no requirement that you return every object.</span></span> <span data-ttu-id="10e25-132">Wenn Sie kein Objekt zurückgeben, wird die Garbage Collection durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="10e25-132">If you don't return an object, it will be garbage collected.</span></span>

## <a name="objectpool-sample"></a><span data-ttu-id="10e25-133">Objectpool-Beispiel</span><span class="sxs-lookup"><span data-stu-id="10e25-133">ObjectPool sample</span></span>

<span data-ttu-id="10e25-134">Der folgende Code</span><span class="sxs-lookup"><span data-stu-id="10e25-134">The following code:</span></span>

* <span data-ttu-id="10e25-135">Fügt `ObjectPoolProvider` dem Container für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (di) hinzu.</span><span class="sxs-lookup"><span data-stu-id="10e25-135">Adds `ObjectPoolProvider` to the [Dependency injection](xref:fundamentals/dependency-injection) (DI) container.</span></span>
* <span data-ttu-id="10e25-136">Hiermit wird der di-Container hinzugefügt und konfiguriert `ObjectPool<StringBuilder>` .</span><span class="sxs-lookup"><span data-stu-id="10e25-136">Adds and configures `ObjectPool<StringBuilder>` to the DI container.</span></span>
* <span data-ttu-id="10e25-137">Fügt hinzu `BirthdayMiddleware` .</span><span class="sxs-lookup"><span data-stu-id="10e25-137">Adds the `BirthdayMiddleware`.</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

<span data-ttu-id="10e25-138">Der folgende Code implementiert`BirthdayMiddleware`</span><span class="sxs-lookup"><span data-stu-id="10e25-138">The following code implements `BirthdayMiddleware`</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

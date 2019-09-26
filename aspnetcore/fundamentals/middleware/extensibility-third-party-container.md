---
title: Middlewareaktivierung mit einem Drittanbietercontainer in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie stark typisierte Middleware mit einer factorybezogenen Aktivierung und einem Drittanbietercontainer in ASP.NET Core verwenden.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: e54a2bd366457fa2d898b7ee26e95021aec5389b
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187092"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a><span data-ttu-id="eae2e-103">Middlewareaktivierung mit einem Drittanbietercontainer in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eae2e-103">Middleware activation with a third-party container in ASP.NET Core</span></span>

<span data-ttu-id="eae2e-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="eae2e-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eae2e-105">In diesem Artikel wird veranschaulicht, wie <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> und <xref:Microsoft.AspNetCore.Http.IMiddleware> als Erweiterungspunkt für die Aktivierung von [Middleware](xref:fundamentals/middleware/index) mit einem Drittanbietercontainer verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="eae2e-105">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="eae2e-106">Einführende Informationen zu `IMiddlewareFactory` und `IMiddleware` finden Sie unter <xref:fundamentals/middleware/extensibility>.</span><span class="sxs-lookup"><span data-stu-id="eae2e-106">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="eae2e-107">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="eae2e-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="eae2e-108">Die Beispiel-App stellt die Aktivierung von Middleware durch eine `IMiddlewareFactory`-Implementierung (`SimpleInjectorMiddlewareFactory`) dar.</span><span class="sxs-lookup"><span data-stu-id="eae2e-108">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="eae2e-109">Das Beispiel verwendet den Dependency Injection-Container [Simple Injector](https://simpleinjector.org).</span><span class="sxs-lookup"><span data-stu-id="eae2e-109">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="eae2e-110">Die Middlewareimplementierungen in diesem Beispiel erfassen den Wert, der von einem Parameter für Abfragezeichenfolgen (`key`) angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="eae2e-110">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="eae2e-111">Die Middleware verwendet einen eingefügten Datenbankkontext (einen bereichsbezogenen Dienst), um den Abfragezeichenwert in einer speicherinternen Datenbank zu erfassen.</span><span class="sxs-lookup"><span data-stu-id="eae2e-111">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="eae2e-112">Die Beispiel-App verwendet [Simple Injector](https://github.com/simpleinjector/SimpleInjector) ausschließlich zu Demonstrationszwecken.</span><span class="sxs-lookup"><span data-stu-id="eae2e-112">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="eae2e-113">Die Verwendung von Simple Injector ist nicht verpflichtend.</span><span class="sxs-lookup"><span data-stu-id="eae2e-113">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="eae2e-114">Die Ansätze für die Aktivierung von Middleware, die in der Dokumentation zu Simple Injector und in GitHub-Problemen beschrieben werden, werden von den Besitzern von Simple Injector empfohlen.</span><span class="sxs-lookup"><span data-stu-id="eae2e-114">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="eae2e-115">Weitere Informationen finden Sie in der [Dokumentation zu Simple Injector](https://simpleinjector.readthedocs.io/en/latest/index.html) und im [GitHub-Repository für Simple Injector](https://github.com/simpleinjector/SimpleInjector).</span><span class="sxs-lookup"><span data-stu-id="eae2e-115">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="eae2e-116">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="eae2e-116">IMiddlewareFactory</span></span>

<span data-ttu-id="eae2e-117"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> stellt Methoden zur Verfügung, um die Middleware zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="eae2e-117"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="eae2e-118">In der Beispiel-App wird eine Middlewarefactory implementiert, um eine `SimpleInjectorActivatedMiddleware`-Instanz zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="eae2e-118">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="eae2e-119">Die Middlewarefactory verwendet den Simple Injector-Container, um die Middleware aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="eae2e-119">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="eae2e-120">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="eae2e-120">IMiddleware</span></span>

<span data-ttu-id="eae2e-121"><xref:Microsoft.AspNetCore.Http.IMiddleware> definiert Middleware für die Anforderungspipeline der App.</span><span class="sxs-lookup"><span data-stu-id="eae2e-121"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="eae2e-122">Middleware, die von einer `IMiddlewareFactory`-Implementierung (*Middleware/SimpleInjectorActivatedMiddleware.cs*) aktiviert wurde:</span><span class="sxs-lookup"><span data-stu-id="eae2e-122">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="eae2e-123">Eine Erweiterung (*Middleware/MiddlewareExtensions.cs*) wird für die Middleware erstellt:</span><span class="sxs-lookup"><span data-stu-id="eae2e-123">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="eae2e-124">`Startup.ConfigureServices` muss mehrere Tasks durchführen:</span><span class="sxs-lookup"><span data-stu-id="eae2e-124">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="eae2e-125">Richten Sie den Simple Injector-Container ein.</span><span class="sxs-lookup"><span data-stu-id="eae2e-125">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="eae2e-126">Registrieren Sie die Factory und die Middleware.</span><span class="sxs-lookup"><span data-stu-id="eae2e-126">Register the factory and middleware.</span></span>
* <span data-ttu-id="eae2e-127">Stellen Sie den Datenbankkontext der App über den Simple Injector-Container zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="eae2e-127">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="eae2e-128">Die Middlewares wird in der Anforderungsverarbeitungspipeline in `Startup.Configure` registriert:</span><span class="sxs-lookup"><span data-stu-id="eae2e-128">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="eae2e-129">In diesem Artikel wird veranschaulicht, wie <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> und <xref:Microsoft.AspNetCore.Http.IMiddleware> als Erweiterungspunkt für die Aktivierung von [Middleware](xref:fundamentals/middleware/index) mit einem Drittanbietercontainer verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="eae2e-129">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="eae2e-130">Einführende Informationen zu `IMiddlewareFactory` und `IMiddleware` finden Sie unter <xref:fundamentals/middleware/extensibility>.</span><span class="sxs-lookup"><span data-stu-id="eae2e-130">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="eae2e-131">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="eae2e-131">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="eae2e-132">Die Beispiel-App stellt die Aktivierung von Middleware durch eine `IMiddlewareFactory`-Implementierung (`SimpleInjectorMiddlewareFactory`) dar.</span><span class="sxs-lookup"><span data-stu-id="eae2e-132">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="eae2e-133">Das Beispiel verwendet den Dependency Injection-Container [Simple Injector](https://simpleinjector.org).</span><span class="sxs-lookup"><span data-stu-id="eae2e-133">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="eae2e-134">Die Middlewareimplementierungen in diesem Beispiel erfassen den Wert, der von einem Parameter für Abfragezeichenfolgen (`key`) angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="eae2e-134">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="eae2e-135">Die Middleware verwendet einen eingefügten Datenbankkontext (einen bereichsbezogenen Dienst), um den Abfragezeichenwert in einer speicherinternen Datenbank zu erfassen.</span><span class="sxs-lookup"><span data-stu-id="eae2e-135">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="eae2e-136">Die Beispiel-App verwendet [Simple Injector](https://github.com/simpleinjector/SimpleInjector) ausschließlich zu Demonstrationszwecken.</span><span class="sxs-lookup"><span data-stu-id="eae2e-136">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="eae2e-137">Die Verwendung von Simple Injector ist nicht verpflichtend.</span><span class="sxs-lookup"><span data-stu-id="eae2e-137">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="eae2e-138">Die Ansätze für die Aktivierung von Middleware, die in der Dokumentation zu Simple Injector und in GitHub-Problemen beschrieben werden, werden von den Besitzern von Simple Injector empfohlen.</span><span class="sxs-lookup"><span data-stu-id="eae2e-138">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="eae2e-139">Weitere Informationen finden Sie in der [Dokumentation zu Simple Injector](https://simpleinjector.readthedocs.io/en/latest/index.html) und im [GitHub-Repository für Simple Injector](https://github.com/simpleinjector/SimpleInjector).</span><span class="sxs-lookup"><span data-stu-id="eae2e-139">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="eae2e-140">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="eae2e-140">IMiddlewareFactory</span></span>

<span data-ttu-id="eae2e-141"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> stellt Methoden zur Verfügung, um die Middleware zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="eae2e-141"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="eae2e-142">In der Beispiel-App wird eine Middlewarefactory implementiert, um eine `SimpleInjectorActivatedMiddleware`-Instanz zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="eae2e-142">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="eae2e-143">Die Middlewarefactory verwendet den Simple Injector-Container, um die Middleware aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="eae2e-143">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="eae2e-144">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="eae2e-144">IMiddleware</span></span>

<span data-ttu-id="eae2e-145"><xref:Microsoft.AspNetCore.Http.IMiddleware> definiert Middleware für die Anforderungspipeline der App.</span><span class="sxs-lookup"><span data-stu-id="eae2e-145"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="eae2e-146">Middleware, die von einer `IMiddlewareFactory`-Implementierung (*Middleware/SimpleInjectorActivatedMiddleware.cs*) aktiviert wurde:</span><span class="sxs-lookup"><span data-stu-id="eae2e-146">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="eae2e-147">Eine Erweiterung (*Middleware/MiddlewareExtensions.cs*) wird für die Middleware erstellt:</span><span class="sxs-lookup"><span data-stu-id="eae2e-147">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="eae2e-148">`Startup.ConfigureServices` muss mehrere Tasks durchführen:</span><span class="sxs-lookup"><span data-stu-id="eae2e-148">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="eae2e-149">Richten Sie den Simple Injector-Container ein.</span><span class="sxs-lookup"><span data-stu-id="eae2e-149">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="eae2e-150">Registrieren Sie die Factory und die Middleware.</span><span class="sxs-lookup"><span data-stu-id="eae2e-150">Register the factory and middleware.</span></span>
* <span data-ttu-id="eae2e-151">Stellen Sie den Datenbankkontext der App über den Simple Injector-Container zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="eae2e-151">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="eae2e-152">Die Middlewares wird in der Anforderungsverarbeitungspipeline in `Startup.Configure` registriert:</span><span class="sxs-lookup"><span data-stu-id="eae2e-152">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="eae2e-153">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="eae2e-153">Additional resources</span></span>

* [<span data-ttu-id="eae2e-154">Middleware</span><span class="sxs-lookup"><span data-stu-id="eae2e-154">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="eae2e-155">Factorybezogene Middlewareaktivierung</span><span class="sxs-lookup"><span data-stu-id="eae2e-155">Factory-based middleware activation</span></span>](xref:fundamentals/middleware/extensibility)
* [<span data-ttu-id="eae2e-156">GitHub-Repository für Simple Injector</span><span class="sxs-lookup"><span data-stu-id="eae2e-156">Simple Injector GitHub repository</span></span>](https://github.com/simpleinjector/SimpleInjector)
* [<span data-ttu-id="eae2e-157">Dokumentation zu Simple Injector</span><span class="sxs-lookup"><span data-stu-id="eae2e-157">Simple Injector documentation</span></span>](https://simpleinjector.readthedocs.io/en/latest/index.html)

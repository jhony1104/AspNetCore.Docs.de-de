---
title: Middlewareaktivierung mit einem Drittanbietercontainer in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie stark typisierte Middleware mit einer factorybezogenen Aktivierung und einem Drittanbietercontainer in ASP.NET Core verwenden.
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2018
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: 9e4d4c6c0232ebc51ad08923e10164262b652280
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64888075"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a><span data-ttu-id="5b050-103">Middlewareaktivierung mit einem Drittanbietercontainer in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5b050-103">Middleware activation with a third-party container in ASP.NET Core</span></span>

<span data-ttu-id="5b050-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5b050-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5b050-105">In diesem Artikel wird veranschaulicht, wie [IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) und [IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) als Erweiterungspunkte für die Aktivierung von [Middleware](xref:fundamentals/middleware/index) mit einem Drittanbietercontainer verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5b050-105">This article demonstrates how to use [IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) and [IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="5b050-106">Einführende Informationen zu `IMiddlewareFactory` und `IMiddleware` finden Sie im Artikel [Factorybezogene Middlewareaktivierung](xref:fundamentals/middleware/extensibility).</span><span class="sxs-lookup"><span data-stu-id="5b050-106">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see the [Factory-based middleware activation](xref:fundamentals/middleware/extensibility) topic.</span></span>

<span data-ttu-id="5b050-107">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5b050-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5b050-108">Die Beispiel-App stellt die Aktivierung von Middleware durch eine `IMiddlewareFactory`-Implementierung (`SimpleInjectorMiddlewareFactory`) dar.</span><span class="sxs-lookup"><span data-stu-id="5b050-108">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="5b050-109">Das Beispiel verwendet den Dependency Injection-Container [Simple Injector](https://simpleinjector.org).</span><span class="sxs-lookup"><span data-stu-id="5b050-109">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="5b050-110">Die Middlewareimplementierungen in diesem Beispiel erfassen den Wert, der von einem Parameter für Abfragezeichenfolgen (`key`) angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="5b050-110">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="5b050-111">Die Middleware verwendet einen eingefügten Datenbankkontext (einen bereichsbezogenen Dienst), um den Abfragezeichenwert in einer speicherinternen Datenbank zu erfassen.</span><span class="sxs-lookup"><span data-stu-id="5b050-111">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="5b050-112">Die Beispiel-App verwendet [Simple Injector](https://github.com/simpleinjector/SimpleInjector) ausschließlich zu Demonstrationszwecken.</span><span class="sxs-lookup"><span data-stu-id="5b050-112">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="5b050-113">Die Verwendung von Simple Injector ist nicht verpflichtend.</span><span class="sxs-lookup"><span data-stu-id="5b050-113">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="5b050-114">Die Ansätze für die Aktivierung von Middleware, die in der Dokumentation zu Simple Injector und in GitHub-Problemen beschrieben werden, werden von den Besitzern von Simple Injector empfohlen.</span><span class="sxs-lookup"><span data-stu-id="5b050-114">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="5b050-115">Weitere Informationen finden Sie in der [Dokumentation zu Simple Injector](https://simpleinjector.readthedocs.io/en/latest/index.html) und im [GitHub-Repository für Simple Injector](https://github.com/simpleinjector/SimpleInjector).</span><span class="sxs-lookup"><span data-stu-id="5b050-115">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="5b050-116">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="5b050-116">IMiddlewareFactory</span></span>

<span data-ttu-id="5b050-117">Die [IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory)-Instanz stellt Methoden zur Verfügung, um Middleware zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="5b050-117">[IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) provides methods to create middleware.</span></span>

<span data-ttu-id="5b050-118">In der Beispiel-App wird eine Middlewarefactory implementiert, um eine `SimpleInjectorActivatedMiddleware`-Instanz zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="5b050-118">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="5b050-119">Die Middlewarefactory verwendet den Simple Injector-Container, um die Middleware aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="5b050-119">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/sample/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="5b050-120">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="5b050-120">IMiddleware</span></span>

<span data-ttu-id="5b050-121">[IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) definiert Middleware für die Anforderungspipeline der App.</span><span class="sxs-lookup"><span data-stu-id="5b050-121">[IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="5b050-122">Middleware, die von einer `IMiddlewareFactory`-Implementierung (*Middleware/SimpleInjectorActivatedMiddleware.cs*) aktiviert wurde:</span><span class="sxs-lookup"><span data-stu-id="5b050-122">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/sample/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="5b050-123">Eine Erweiterung (*Middleware/MiddlewareExtensions.cs*) wird für die Middleware erstellt:</span><span class="sxs-lookup"><span data-stu-id="5b050-123">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/sample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="5b050-124">`Startup.ConfigureServices` muss mehrere Tasks durchführen:</span><span class="sxs-lookup"><span data-stu-id="5b050-124">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="5b050-125">Richten Sie den Simple Injector-Container ein.</span><span class="sxs-lookup"><span data-stu-id="5b050-125">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="5b050-126">Registrieren Sie die Factory und die Middleware.</span><span class="sxs-lookup"><span data-stu-id="5b050-126">Register the factory and middleware.</span></span>
* <span data-ttu-id="5b050-127">Stellen Sie den Datenbankkontext von der App über den Simple Injector-Container für eine Razor-Seite zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="5b050-127">Make the app's database context available from the Simple Injector container for a Razor Page.</span></span>

[!code-csharp[](extensibility-third-party-container/sample/Startup.cs?name=snippet1)]

<span data-ttu-id="5b050-128">Die Middlewares wird in der Anforderungsverarbeitungspipeline in `Startup.Configure` registriert:</span><span class="sxs-lookup"><span data-stu-id="5b050-128">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/sample/Startup.cs?name=snippet2&highlight=13)]

## <a name="additional-resources"></a><span data-ttu-id="5b050-129">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5b050-129">Additional resources</span></span>

* [<span data-ttu-id="5b050-130">Middleware</span><span class="sxs-lookup"><span data-stu-id="5b050-130">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="5b050-131">Factorybezogene Middlewareaktivierung</span><span class="sxs-lookup"><span data-stu-id="5b050-131">Factory-based middleware activation</span></span>](xref:fundamentals/middleware/extensibility)
* [<span data-ttu-id="5b050-132">GitHub-Repository für Simple Injector</span><span class="sxs-lookup"><span data-stu-id="5b050-132">Simple Injector GitHub repository</span></span>](https://github.com/simpleinjector/SimpleInjector)
* [<span data-ttu-id="5b050-133">Dokumentation zu Simple Injector</span><span class="sxs-lookup"><span data-stu-id="5b050-133">Simple Injector documentation</span></span>](https://simpleinjector.readthedocs.io/en/latest/index.html)

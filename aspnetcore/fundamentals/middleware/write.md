---
title: Schreiben von benutzerdefinierter ASP.NET Core-Middleware
author: rick-anderson
description: Erfahren Sie, wie benutzerdefinierte ASP.NET Core-Middleware geschrieben wird.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/6/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/write
ms.openlocfilehash: 6852c1831d1f71af7f4fad3288fd6f897dfaa65f
ms.sourcegitcommit: 30fcf69556b6b6ec54a3879e280d5f61f018b48f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/07/2020
ms.locfileid: "82876191"
---
# <a name="write-custom-aspnet-core-middleware"></a><span data-ttu-id="d31e6-103">Schreiben von benutzerdefinierter ASP.NET Core-Middleware</span><span class="sxs-lookup"><span data-stu-id="d31e6-103">Write custom ASP.NET Core middleware</span></span>

<span data-ttu-id="d31e6-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="d31e6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="d31e6-105">Middleware ist Software, die zu einer Anwendungspipeline zusammengesetzt wird, um Anforderungen und Antworten zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="d31e6-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="d31e6-106">ASP.NET Core stellt in umfangreichem Maß integrierte Middlewarekomponenten zur Verfügung. In manchen Szenarios möchten Sie aber vielleicht selbst eine benutzerdefinierte Middleware schreiben.</span><span class="sxs-lookup"><span data-stu-id="d31e6-106">ASP.NET Core provides a rich set of built-in middleware components, but in some scenarios you might want to write a custom middleware.</span></span>

## <a name="middleware-class"></a><span data-ttu-id="d31e6-107">Middlewareklasse</span><span class="sxs-lookup"><span data-stu-id="d31e6-107">Middleware class</span></span>

<span data-ttu-id="d31e6-108">Für gewöhnlich ist Middleware in einer Klasse gekapselt und wird mit einer Erweiterungsmethode verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="d31e6-108">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="d31e6-109">Sehen Sie sich folgende Middleware an, die die Kultur der aktuellen Anforderung über eine Abfragezeichenfolge festlegt:</span><span class="sxs-lookup"><span data-stu-id="d31e6-109">Consider the following middleware, which sets the culture for the current request from a query string:</span></span>

[!code-csharp[](write/snapshot/StartupCulture.cs)]

<span data-ttu-id="d31e6-110">Im vorhergehenden Beispielcode wird die Erstellung einer Middlewarekomponente veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="d31e6-110">The preceding sample code is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="d31e6-111">Informationen zur Unterstützung der integrierten Lokalisierung für ASP.NET Core finden Sie unter <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="d31e6-111">For ASP.NET Core's built-in localization support, see <xref:fundamentals/localization>.</span></span>

<span data-ttu-id="d31e6-112">Testen Sie die Middleware, indem Sie die Kultur übergeben.</span><span class="sxs-lookup"><span data-stu-id="d31e6-112">Test the middleware by passing in the culture.</span></span> <span data-ttu-id="d31e6-113">Fordern Sie beispielsweise `https://localhost:5001/?culture=no` an.</span><span class="sxs-lookup"><span data-stu-id="d31e6-113">For example, request `https://localhost:5001/?culture=no`.</span></span>

<span data-ttu-id="d31e6-114">Im folgenden Code wird der Middlewaredelegat in eine Klasse verschoben:</span><span class="sxs-lookup"><span data-stu-id="d31e6-114">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

<span data-ttu-id="d31e6-115">Die Middlewareklasse muss Folgendes enthalten:</span><span class="sxs-lookup"><span data-stu-id="d31e6-115">The middleware class must include:</span></span>

* <span data-ttu-id="d31e6-116">Einen öffentlichen Konstruktor mit einem Parameter des Typs <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span><span class="sxs-lookup"><span data-stu-id="d31e6-116">A public constructor with a parameter of type <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span>
* <span data-ttu-id="d31e6-117">Eine öffentliche Methode mit dem Namen `Invoke` oder `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="d31e6-117">A public method named `Invoke` or `InvokeAsync`.</span></span> <span data-ttu-id="d31e6-118">Diese Methode muss Folgendes:</span><span class="sxs-lookup"><span data-stu-id="d31e6-118">This method must:</span></span>
  * <span data-ttu-id="d31e6-119">Eine `Task` zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="d31e6-119">Return a `Task`.</span></span>
  * <span data-ttu-id="d31e6-120">Einen ersten Parameter des Typs <xref:Microsoft.AspNetCore.Http.HttpContext> akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="d31e6-120">Accept a first parameter of type <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>
  
<span data-ttu-id="d31e6-121">Zusätzliche Parameter für den Konstruktor und `Invoke`/`InvokeAsync` werden mittels [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="d31e6-121">Additional parameters for the constructor and `Invoke`/`InvokeAsync` are populated by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

## <a name="middleware-dependencies"></a><span data-ttu-id="d31e6-122">Middlewareabhängigkeiten</span><span class="sxs-lookup"><span data-stu-id="d31e6-122">Middleware dependencies</span></span>

<span data-ttu-id="d31e6-123">Middleware sollte das [Prinzip der expliziten Abhängigkeiten](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) befolgen, indem sie ihre Abhängigkeiten in ihrem Konstruktor verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="d31e6-123">Middleware should follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="d31e6-124">Middleware wird einmal während der *Anwendungslebensdauer* erstellt.</span><span class="sxs-lookup"><span data-stu-id="d31e6-124">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="d31e6-125">Lesen Sie den Abschnitt [Voranforderungsbasierte Middlewareabhängigkeiten](#per-request-middleware-dependencies), wenn Sie Dienste für Middleware innerhalb einer Anforderung gemeinsam verwenden müssen.</span><span class="sxs-lookup"><span data-stu-id="d31e6-125">See the [Per-request middleware dependencies](#per-request-middleware-dependencies) section if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="d31e6-126">Middlewarekomponenten können Ihre Abhängigkeiten über [Dependency Injection (DI)](xref:fundamentals/dependency-injection) mit Konstruktorparametern auflösen.</span><span class="sxs-lookup"><span data-stu-id="d31e6-126">Middleware components can resolve their dependencies from [dependency injection (DI)](xref:fundamentals/dependency-injection) through constructor parameters.</span></span> <span data-ttu-id="d31e6-127">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) kann auch direkt zusätzliche Parameter annehmen.</span><span class="sxs-lookup"><span data-stu-id="d31e6-127">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) can also accept additional parameters directly.</span></span>

## <a name="per-request-middleware-dependencies"></a><span data-ttu-id="d31e6-128">Voranforderungsbasierte Middlewareabhängigkeiten</span><span class="sxs-lookup"><span data-stu-id="d31e6-128">Per-request middleware dependencies</span></span>

<span data-ttu-id="d31e6-129">Weil Middleware zum Zeitpunkt des Anwendungsstarts erstellt wird (und nicht voranforderungsbasiert), werden *bereichsbezogene* Lebensdauerdienste von Middlewarekonstruktoren in den einzelnen Anforderungen nicht gemeinsam mit anderen Typen mit Dependency Injection verwendet.</span><span class="sxs-lookup"><span data-stu-id="d31e6-129">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors aren't shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="d31e6-130">Wenn Sie einen *bereichsbezogenen* Dienst sowohl in Ihrer Middleware als auch in anderen Typen verwenden müssen, fügen Sie diese Dienste zur Signatur der `Invoke`-Methode hinzu.</span><span class="sxs-lookup"><span data-stu-id="d31e6-130">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="d31e6-131">Die `Invoke`-Methode kann zusätzliche Parameter akzeptieren, die durch DI aufgefüllt werden:</span><span class="sxs-lookup"><span data-stu-id="d31e6-131">The `Invoke` method can accept additional parameters that are populated by DI:</span></span>

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="middleware-extension-method"></a><span data-ttu-id="d31e6-132">Erweiterungsmethode für die Middleware</span><span class="sxs-lookup"><span data-stu-id="d31e6-132">Middleware extension method</span></span>

<span data-ttu-id="d31e6-133">Die folgende Erweiterungsmethode stellt die Middleware über <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="d31e6-133">The following extension method exposes the middleware through <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="d31e6-134">Der folgende Code ruft die Methode von `Startup.Configure` auf:</span><span class="sxs-lookup"><span data-stu-id="d31e6-134">The following code calls the middleware from `Startup.Configure`:</span></span>

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a><span data-ttu-id="d31e6-135">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d31e6-135">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

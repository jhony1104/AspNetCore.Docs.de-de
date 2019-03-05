---
title: Schreiben von benutzerdefinierter ASP.NET Core-Middleware
author: rick-anderson
description: Erfahren Sie, wie benutzerdefinierte ASP.NET Core-Middleware geschrieben wird.
ms.author: riande
ms.custom: mvc
ms.date: 02/14/2019
uid: fundamentals/middleware/write
ms.openlocfilehash: 2c5577394a10370d92c8a83f9d806b63f3245c8b
ms.sourcegitcommit: b3894b65e313570e97a2ab78b8addd22f427cac8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56744910"
---
# <a name="write-custom-aspnet-core-middleware"></a><span data-ttu-id="ddee3-103">Schreiben von benutzerdefinierter ASP.NET Core-Middleware</span><span class="sxs-lookup"><span data-stu-id="ddee3-103">Write custom ASP.NET Core middleware</span></span>

<span data-ttu-id="ddee3-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="ddee3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="ddee3-105">Middleware ist Software, die zu einer Anwendungspipeline zusammengesetzt wird, um Anforderungen und Antworten zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="ddee3-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="ddee3-106">ASP.NET Core stellt in umfangreichem Maß integrierte Middlewarekomponenten zur Verfügung. In manchen Szenarios möchten Sie aber vielleicht selbst eine benutzerdefinierte Middleware schreiben.</span><span class="sxs-lookup"><span data-stu-id="ddee3-106">ASP.NET Core provides a rich set of built-in middleware components, but in some scenarios you might want to write a custom middleware.</span></span>

## <a name="middleware-class"></a><span data-ttu-id="ddee3-107">Middlewareklasse</span><span class="sxs-lookup"><span data-stu-id="ddee3-107">Middleware class</span></span>

<span data-ttu-id="ddee3-108">Für gewöhnlich ist Middleware in einer Klasse gekapselt und wird mit einer Erweiterungsmethode verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="ddee3-108">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="ddee3-109">Sehen Sie sich folgende Middleware an, die die Kultur der aktuellen Anforderung über eine Abfragezeichenfolge festlegt:</span><span class="sxs-lookup"><span data-stu-id="ddee3-109">Consider the following middleware, which sets the culture for the current request from a query string:</span></span>

[!code-csharp[](index/snapshot/Culture/StartupCulture.cs?name=snippet1)]

<span data-ttu-id="ddee3-110">Im vorhergehenden Beispielcode wird die Erstellung einer Middlewarekomponente veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="ddee3-110">The preceding sample code is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="ddee3-111">Informationen zur Unterstützung der integrierten Lokalisierung für ASP.NET Core finden Sie unter <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="ddee3-111">For ASP.NET Core's built-in localization support, see <xref:fundamentals/localization>.</span></span>

<span data-ttu-id="ddee3-112">Sie können die Middleware testen, indem Sie die Kultur übergeben.</span><span class="sxs-lookup"><span data-stu-id="ddee3-112">You can test the middleware by passing in the culture.</span></span> <span data-ttu-id="ddee3-113">Beispielsweise `http://localhost:7997/?culture=no`.</span><span class="sxs-lookup"><span data-stu-id="ddee3-113">For example, `http://localhost:7997/?culture=no`.</span></span>

<span data-ttu-id="ddee3-114">Im folgenden Code wird der Middlewaredelegat in eine Klasse verschoben:</span><span class="sxs-lookup"><span data-stu-id="ddee3-114">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[](index/snapshot/Culture/RequestCultureMiddleware.cs)]

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="ddee3-115">Der Name der Middlewaremethode `Task` muss `Invoke` lauten.</span><span class="sxs-lookup"><span data-stu-id="ddee3-115">The middleware `Task` method's name must be `Invoke`.</span></span> <span data-ttu-id="ddee3-116">In ASP.NET Core 2.0 oder höher kann der Name `Invoke` oder `InvokeAsync` lauten.</span><span class="sxs-lookup"><span data-stu-id="ddee3-116">In ASP.NET Core 2.0 or later, the name can be either `Invoke` or `InvokeAsync`.</span></span>

::: moniker-end

## <a name="middleware-extension-method"></a><span data-ttu-id="ddee3-117">Erweiterungsmethode für die Middleware</span><span class="sxs-lookup"><span data-stu-id="ddee3-117">Middleware extension method</span></span>

<span data-ttu-id="ddee3-118">Die folgende Erweiterungsmethode stellt die Middleware über <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="ddee3-118">The following extension method exposes the middleware through <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span></span>

[!code-csharp[](index/snapshot/Culture/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="ddee3-119">Der folgende Code ruft die Methode von `Startup.Configure` auf:</span><span class="sxs-lookup"><span data-stu-id="ddee3-119">The following code calls the middleware from `Startup.Configure`:</span></span>

[!code-csharp[](index/snapshot/Culture/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="ddee3-120">Middleware sollte das [Prinzip der expliziten Abhängigkeiten](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) befolgen, indem sie ihre Abhängigkeiten in ihrem Konstruktor verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="ddee3-120">Middleware should follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="ddee3-121">Middleware wird einmal während der *Anwendungslebensdauer* erstellt.</span><span class="sxs-lookup"><span data-stu-id="ddee3-121">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="ddee3-122">Lesen Sie den Abschnitt [Voranforderungsbasierte Abhängigkeiten](#per-request-dependencies), wenn Sie Dienste für Middleware innerhalb einer Anforderung gemeinsam verwenden müssen.</span><span class="sxs-lookup"><span data-stu-id="ddee3-122">See the [Per-request dependencies](#per-request-dependencies) section if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="ddee3-123">Middlewarekomponenten können Ihre Abhängigkeiten über [Dependency Injection (DI)](xref:fundamentals/dependency-injection) mit Konstruktorparametern auflösen.</span><span class="sxs-lookup"><span data-stu-id="ddee3-123">Middleware components can resolve their dependencies from [dependency injection (DI)](xref:fundamentals/dependency-injection) through constructor parameters.</span></span> <span data-ttu-id="ddee3-124">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) kann auch direkt zusätzliche Parameter annehmen.</span><span class="sxs-lookup"><span data-stu-id="ddee3-124">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) can also accept additional parameters directly.</span></span>

## <a name="per-request-dependencies"></a><span data-ttu-id="ddee3-125">Voranforderungsbasierte Abhängigkeiten</span><span class="sxs-lookup"><span data-stu-id="ddee3-125">Per-request dependencies</span></span>

<span data-ttu-id="ddee3-126">Weil Middleware zum Zeitpunkt des Anwendungsstarts erstellt wird (und nicht voranforderungsbasiert), werden *bereichsbezogene* Lebensdauerdienste von Middlewarekonstruktoren in den einzelnen Anforderungen nicht gemeinsam mit anderen Typen mit Dependency Injection verwendet.</span><span class="sxs-lookup"><span data-stu-id="ddee3-126">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors aren't shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="ddee3-127">Wenn Sie einen *bereichsbezogenen* Dienst sowohl in Ihrer Middleware als auch in anderen Typen verwenden müssen, fügen Sie diese Dienste zur Signatur der `Invoke`-Methode hinzu.</span><span class="sxs-lookup"><span data-stu-id="ddee3-127">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="ddee3-128">Die `Invoke`-Methode kann zusätzliche Parameter akzeptieren, die durch DI aufgefüllt werden:</span><span class="sxs-lookup"><span data-stu-id="ddee3-128">The `Invoke` method can accept additional parameters that are populated by DI:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="ddee3-129">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="ddee3-129">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

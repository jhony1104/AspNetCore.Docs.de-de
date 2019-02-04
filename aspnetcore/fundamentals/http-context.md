---
title: Zugreifen auf HttpContext in ASP.NET Core
author: coderandhiker
description: Anleitung zum Zugreifen auf HttpContext in ASP.NET Core
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2018
uid: fundamentals/httpcontext
ms.openlocfilehash: babc637cdec8590ac14f7924c17e862e5b2f6a81
ms.sourcegitcommit: d22b3c23c45a076c4f394a70b1c8df2fbcdf656d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55428485"
---
# <a name="access-httpcontext-in-aspnet-core"></a><span data-ttu-id="5dbda-103">Zugreifen auf HttpContext in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5dbda-103">Access HttpContext in ASP.NET Core</span></span>

<span data-ttu-id="5dbda-104">ASP.NET Core-Apps greifen über die [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor)-Schnittstelle und die zugehörige Standardimplementierung [HttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.httpcontextaccessor) auf `HttpContext` zu.</span><span class="sxs-lookup"><span data-stu-id="5dbda-104">ASP.NET Core apps access the `HttpContext` through the [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) interface and its default implementation [HttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.httpcontextaccessor).</span></span> <span data-ttu-id="5dbda-105">`IHttpContextAccessor` muss nur verwendet werden, wenn Sie auf `HttpContext` innerhalb eines Diensts zugreifen müssen.</span><span class="sxs-lookup"><span data-stu-id="5dbda-105">It's only necessary to use `IHttpContextAccessor` when you need access to the `HttpContext` inside a service.</span></span>

::: moniker range=">= aspnetcore-2.0"

## <a name="use-httpcontext-from-razor-pages"></a><span data-ttu-id="5dbda-106">Verwenden von HttpContext über Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5dbda-106">Use HttpContext from Razor Pages</span></span>

<span data-ttu-id="5dbda-107">Die Klasse [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) in Razor Pages zeigt die Eigenschaft [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext) an:</span><span class="sxs-lookup"><span data-stu-id="5dbda-107">The Razor Pages [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) exposes the [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext) property:</span></span>

```csharp
public class AboutModel : PageModel
{
    public string Message { get; set; }

    public void OnGet()
    {
        Message = HttpContext.Request.PathBase;
    }
}
```

::: moniker-end

## <a name="use-httpcontext-from-a-razor-view"></a><span data-ttu-id="5dbda-108">Verwenden von HttpContext in einer Razor-Ansicht</span><span class="sxs-lookup"><span data-stu-id="5dbda-108">Use HttpContext from a Razor view</span></span>

<span data-ttu-id="5dbda-109">Razor-Ansichten machen `HttpContext` direkt über die [RazorPage.Context](/dotnet/api/microsoft.aspnetcore.mvc.razor.razorpage.context#Microsoft_AspNetCore_Mvc_Razor_RazorPage_Context)-Eigenschaft in der Ansicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="5dbda-109">Razor views expose the `HttpContext` directly via a [RazorPage.Context](/dotnet/api/microsoft.aspnetcore.mvc.razor.razorpage.context#Microsoft_AspNetCore_Mvc_Razor_RazorPage_Context) property on the view.</span></span> <span data-ttu-id="5dbda-110">Im folgenden Beispiel wird der aktuelle Benutzernamen in einer Intranet-App abgerufen, die die Windows-Authentifizierung verwendet:</span><span class="sxs-lookup"><span data-stu-id="5dbda-110">The following example retrieves the current username in an Intranet app using Windows Authentication:</span></span>

```cshtml
@{
    var username = Context.User.Identity.Name;
}
```

## <a name="use-httpcontext-from-a-controller"></a><span data-ttu-id="5dbda-111">Verwenden von HttpContext über einen Controller</span><span class="sxs-lookup"><span data-stu-id="5dbda-111">Use HttpContext from a controller</span></span>

<span data-ttu-id="5dbda-112">Controller zeigen die Eigenschaft [ControllerBase.HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.httpcontext) an:</span><span class="sxs-lookup"><span data-stu-id="5dbda-112">Controllers expose the [ControllerBase.HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.httpcontext) property:</span></span>

```csharp
public class HomeController : Controller
{
    public IActionResult About()
    {
        var pathBase = HttpContext.Request.PathBase;
        // Do something with the PathBase.

        return View();
    }
}
```

## <a name="use-httpcontext-from-middleware"></a><span data-ttu-id="5dbda-113">Verwenden von HttpContext über Middleware</span><span class="sxs-lookup"><span data-stu-id="5dbda-113">Use HttpContext from middleware</span></span>

<span data-ttu-id="5dbda-114">Bei der Verwendung benutzerdefinierter Middlewarekomponenten wird `HttpContext` an die Methode `Invoke` oder `InvokeAsync` übergeben. Wenn die Middleware konfiguriert ist, kann darauf zugegriffen werden:</span><span class="sxs-lookup"><span data-stu-id="5dbda-114">When working with custom middleware components, `HttpContext` is passed into the `Invoke` or `InvokeAsync` method and can be accessed when the middleware is configured:</span></span>

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        // Middleware initialization optionally using HttpContext
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a><span data-ttu-id="5dbda-115">Verwenden von HttpContext über benutzerdefinierte Komponenten</span><span class="sxs-lookup"><span data-stu-id="5dbda-115">Use HttpContext from custom components</span></span>

<span data-ttu-id="5dbda-116">Für andere Framework- und benutzerdefinierte Komponenten, die Zugriff auf `HttpContext` erfordern, wird empfohlen, eine Abhängigkeit mithilfe des integrierten Containers [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="5dbda-116">For other framework and custom components that require access to `HttpContext`, the recommended approach is to register a dependency using the built-in [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="5dbda-117">Der Abhängigkeitsinjektionscontainer stellt `IHttpContextAccessor` allen Klassen zur Verfügung, die ihn in ihren Konstruktoren als Abhängigkeit deklarieren.</span><span class="sxs-lookup"><span data-stu-id="5dbda-117">The dependency injection container supplies the `IHttpContextAccessor` to any classes that declare it as a dependency in their constructors.</span></span>

::: moniker range=">= aspnetcore-2.1"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddMvc()
         .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddMvc();
     services.AddSingleton<IHttpContextAccessor, HttpContextAccessor>();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

<span data-ttu-id="5dbda-118">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="5dbda-118">In the following example:</span></span>

* <span data-ttu-id="5dbda-119">`UserRepository` deklariert seine Abhängigkeit von `IHttpContextAccessor`.</span><span class="sxs-lookup"><span data-stu-id="5dbda-119">`UserRepository` declares its dependency on `IHttpContextAccessor`.</span></span>
* <span data-ttu-id="5dbda-120">Die Abhängigkeit wird bereitgestellt, wenn die Abhängigkeitsinjektion die Abhängigkeitskette auflöst und eine `UserRepository`-Instanz erstellt.</span><span class="sxs-lookup"><span data-stu-id="5dbda-120">The dependency is supplied when dependency injection resolves the dependency chain and creates an instance of `UserRepository`.</span></span>

```csharp
public class UserRepository : IUserRepository
{
    private readonly IHttpContextAccessor _httpContextAccessor;

    public UserRepository(IHttpContextAccessor httpContextAccessor)
    {
        _httpContextAccessor = httpContextAccessor;
    }

    public void LogCurrentUser()
    {
        var username = _httpContextAccessor.HttpContext.User.Identity.Name;
        service.LogAccessRequest(username);
    }
}
```

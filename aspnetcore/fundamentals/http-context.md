---
title: Zugreifen auf HttpContext in ASP.NET Core
author: coderandhiker
description: Anleitung zum Zugreifen auf HttpContext in ASP.NET Core
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2018
uid: fundamentals/httpcontext
ms.openlocfilehash: 373c036e0839ce51259e23f8503fbe4691b48751
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58209595"
---
# <a name="access-httpcontext-in-aspnet-core"></a>Zugreifen auf HttpContext in ASP.NET Core

ASP.NET Core-Apps greifen über die [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor)-Schnittstelle und die zugehörige Standardimplementierung [HttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.httpcontextaccessor) auf `HttpContext` zu. `IHttpContextAccessor` muss nur verwendet werden, wenn Sie auf `HttpContext` innerhalb eines Diensts zugreifen müssen.

::: moniker range=">= aspnetcore-2.0"

## <a name="use-httpcontext-from-razor-pages"></a>Verwenden von HttpContext über Razor Pages

Die Klasse [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) in Razor Pages zeigt die Eigenschaft [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext) an:

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

## <a name="use-httpcontext-from-a-razor-view"></a>Verwenden von HttpContext in einer Razor-Ansicht

Razor-Ansichten machen `HttpContext` direkt über die [RazorPage.Context](/dotnet/api/microsoft.aspnetcore.mvc.razor.razorpage.context#Microsoft_AspNetCore_Mvc_Razor_RazorPage_Context)-Eigenschaft in der Ansicht verfügbar. Im folgenden Beispiel wird der aktuelle Benutzernamen in einer Intranet-App abgerufen, die die Windows-Authentifizierung verwendet:

```cshtml
@{
    var username = Context.User.Identity.Name;
}
```

## <a name="use-httpcontext-from-a-controller"></a>Verwenden von HttpContext über einen Controller

Controller zeigen die Eigenschaft [ControllerBase.HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.httpcontext) an:

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

## <a name="use-httpcontext-from-middleware"></a>Verwenden von HttpContext über Middleware

Bei der Verwendung benutzerdefinierter Middlewarekomponenten wird `HttpContext` an die Methode `Invoke` oder `InvokeAsync` übergeben. Wenn die Middleware konfiguriert ist, kann darauf zugegriffen werden:

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        // Middleware initialization optionally using HttpContext
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a>Verwenden von HttpContext über benutzerdefinierte Komponenten

Für andere Framework- und benutzerdefinierte Komponenten, die Zugriff auf `HttpContext` erfordern, wird empfohlen, eine Abhängigkeit mithilfe des integrierten Containers [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zu registrieren. Der Abhängigkeitsinjektionscontainer stellt `IHttpContextAccessor` allen Klassen zur Verfügung, die ihn in ihren Konstruktoren als Abhängigkeit deklarieren.

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

Im folgenden Beispiel:

* `UserRepository` deklariert seine Abhängigkeit von `IHttpContextAccessor`.
* Die Abhängigkeit wird bereitgestellt, wenn die Abhängigkeitsinjektion die Abhängigkeitskette auflöst und eine `UserRepository`-Instanz erstellt.

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

## <a name="httpcontext-access-from-a-background-thread"></a>HttpContext-Zugriff von einem Hintergrundthread aus

`HttpContext` ist nicht threadsicher. Eigenschaften von `HttpContext` außerhalb der Verarbeitung einer Anforderung zu lesen oder zu schreiben kann zu einer `NullReferenceException` führen.

> [!NOTE]
> Wenn `HttpContext` außerhalb der Verarbeitung einer Anforderung verwendet wird, führt dies oft zu einer `NullReferenceException`. Wenn Ihre App sporadische `NullReferenceException`s erstellt, überprüfen Sie die Teile des Codes, die die Hintergrundverarbeitung starten, oder die die Verarbeitung fortsetzen, nachdem eine Anforderung erfüllt wurde. Suchen Sie nach Fehlern, wie das Definieren einer Controllermethode als `async void`.

So werden Hintergrundaufgaben mit `HttpContext`-Daten sicher ausgeführt:

* Kopieren Sie die benötigten Daten während der Verarbeitung der Anforderung.
* Übergeben Sie die kopierten Daten an eine Hintergrundaufgabe.

Um unsicheren Code zu vermeiden, sollten Sie `HttpContext` niemals an eine Methode übergeben, die Hintergrundarbeit ausführt. Übergeben Sie stattdessen die benötigten Daten.

```csharp
public class EmailController
{
    public ActionResult SendEmail(string email)
    {
        var correlationId = HttpContext.Request.Headers["x-correlation-id"].ToString();

        // Starts sending an email, but doesn't wait for it to complete
        _ = SendEmailCore(correlationId);
        return View();
    }

    private async Task SendEmailCore(string correlationId)
    {
        // send the email
    }
}

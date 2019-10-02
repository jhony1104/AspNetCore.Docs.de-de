---
title: Fehlerbehandlung in ASP.NET Core-Web-APIs
author: pranavkm
description: Erfahren Sie, wie Sie Fehler in ASP.NET Core-Web-APIs beheben können.
monikerRange: '>= aspnetcore-2.1'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/25/2019
uid: web-api/handle-errors
ms.openlocfilehash: 9c5dd2f89e7351f386d1f0633c831952dc58e568
ms.sourcegitcommit: 994da92edb0abf856b1655c18880028b15a28897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71278732"
---
# <a name="handle-errors-in-aspnet-core-web-apis"></a><span data-ttu-id="daf96-103">Fehlerbehandlung in ASP.NET Core-Web-APIs</span><span class="sxs-lookup"><span data-stu-id="daf96-103">Handle errors in ASP.NET Core web APIs</span></span>

<span data-ttu-id="daf96-104">Dieser Artikel beschreibt die Verarbeitung und Anpassung der Fehlerbehandlung bei ASP.NET Core-Web-APIs.</span><span class="sxs-lookup"><span data-stu-id="daf96-104">This article describes how to handle and customize error handling with ASP.NET Core web APIs.</span></span>

<span data-ttu-id="daf96-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="daf96-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([How to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="daf96-106">Seite mit Ausnahmen für Entwickler</span><span class="sxs-lookup"><span data-stu-id="daf96-106">Developer Exception Page</span></span>

<span data-ttu-id="daf96-107">Die [Seite mit Ausnahmen für Entwickler](xref:fundamentals/error-handling) ist ein nützliches Tool, um detaillierte Stapelüberwachungen für Serverfehler zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="daf96-107">The [Developer Exception Page](xref:fundamentals/error-handling) is a useful tool to get detailed stack traces for server errors.</span></span>

<span data-ttu-id="daf96-108">Die Seite mit Ausnahmen für Entwickler zeigt eine Nur-Text-Antwort an, wenn der Client keine HTML-formatierte Ausgabe akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="daf96-108">The Developer Exception Page displays a plain-text response if the client doesn't accept HTML-formatted output.</span></span> <span data-ttu-id="daf96-109">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="daf96-109">For example:</span></span>

```
> curl https://localhost:5001/weatherforecast
System.ArgumentException: count
   at errorhandling.Controllers.WeatherForecastController.Get(Int32 x) in D:\work\Samples\samples\aspnetcore\mvc\errorhandling\Controllers\WeatherForecastController.cs:line 35
   at lambda_method(Closure , Object , Object[] )
   at Microsoft.Extensions.Internal.ObjectMethodExecutor.Execute(Object target, Object[] parameters)
...
```

> [!WARNING]
> <span data-ttu-id="daf96-110">Aktivieren Sie die Seite mit Ausnahmen für Entwickler **nur dann, wenn die App in der Entwicklungsumgebung ausgeführt wird**.</span><span class="sxs-lookup"><span data-stu-id="daf96-110">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="daf96-111">Wenn die App in der Produktionsumgebung ausgeführt wird, sollten Sie keine detaillierten Ausnahmeinformationen öffentlich teilen.</span><span class="sxs-lookup"><span data-stu-id="daf96-111">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="daf96-112">Weitere Informationen zum Konfigurieren der Umgebungen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="daf96-112">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

## <a name="exception-handler"></a><span data-ttu-id="daf96-113">Ausnahmehandler</span><span class="sxs-lookup"><span data-stu-id="daf96-113">Exception handler</span></span>

<span data-ttu-id="daf96-114">In nicht zur Entwicklung dienenden Umgebungen kann [Middleware zur Ausnahmebehandlung](xref:fundamentals/error-handling) verwendet werden, um eine Fehlernutzlast zu erzeugen:</span><span class="sxs-lookup"><span data-stu-id="daf96-114">In non-development environments, [Exception Handling Middleware](xref:fundamentals/error-handling) can be used to produce an error payload:</span></span>

1. <span data-ttu-id="daf96-115">Rufen Sie <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> in `Startup.Configure` auf, um die Middleware zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="daf96-115">In `Startup.Configure`, invoke <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> to use the middleware:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. <span data-ttu-id="daf96-116">Konfigurieren Sie eine Controlleraktion, um auf die `/error`-Route zu antworten:</span><span class="sxs-lookup"><span data-stu-id="daf96-116">Configure a controller action to respond to the `/error` route:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

<span data-ttu-id="daf96-117">Die oben stehende `Error`-Aktion sendet eine mit [RFC7807](https://tools.ietf.org/html/rfc7807) kompatible Nutzlast an den Client.</span><span class="sxs-lookup"><span data-stu-id="daf96-117">The preceding `Error` action sends an [RFC7807](https://tools.ietf.org/html/rfc7807)-compliant payload to the client.</span></span>

<span data-ttu-id="daf96-118">Middleware zur Ausnahmebehandlung kann auch eine detailliertere Ausgabe der Inhaltsaushandlung in der lokalen Entwicklungsumgebung bieten.</span><span class="sxs-lookup"><span data-stu-id="daf96-118">Exception Handling Middleware can also provide more detailed content-negotiated output in the local development environment.</span></span> <span data-ttu-id="daf96-119">Führen Sie die folgenden Schritte aus, um für ein konsistentes Nutzlastformat in Entwicklungs- und Produktionsumgebungen zu sorgen:</span><span class="sxs-lookup"><span data-stu-id="daf96-119">Use the following steps to produce a consistent payload format across development and production environments:</span></span>

1. <span data-ttu-id="daf96-120">Registrieren Sie umgebungsspezifische Middleware zur Ausnahmebehandlung in `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="daf96-120">In `Startup.Configure`, register environment-specific Exception Handling Middleware instances:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    <span data-ttu-id="daf96-121">Im oben stehenden Code ist die Middleware mit Folgendem registriert:</span><span class="sxs-lookup"><span data-stu-id="daf96-121">In the preceding code, the middleware is registered with:</span></span>

    * <span data-ttu-id="daf96-122">Route `/error-local-development` in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="daf96-122">A route of `/error-local-development` in the Development environment.</span></span>
    * <span data-ttu-id="daf96-123">Route `/error` in einer nicht zur Entwicklung dienenden Umgebung.</span><span class="sxs-lookup"><span data-stu-id="daf96-123">A route of `/error` in environments that aren't Development.</span></span>
    
1. <span data-ttu-id="daf96-124">Wenden Sie das Attributrouting auf Controlleraktionen an:</span><span class="sxs-lookup"><span data-stu-id="daf96-124">Apply attribute routing to controller actions:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

## <a name="use-exceptions-to-modify-the-response"></a><span data-ttu-id="daf96-125">Verwenden von Ausnahmen zum Ändern der Antwort</span><span class="sxs-lookup"><span data-stu-id="daf96-125">Use exceptions to modify the response</span></span>

<span data-ttu-id="daf96-126">Der Inhalt der Antwort kann außerhalb des Controllers geändert werden.</span><span class="sxs-lookup"><span data-stu-id="daf96-126">The contents of the response can be modified from outside of the controller.</span></span> <span data-ttu-id="daf96-127">In der ASP.NET 4.x-Web-API war die einzige Möglichkeit hierfür die Verwendung des Typs <xref:System.Web.Http.HttpResponseException>.</span><span class="sxs-lookup"><span data-stu-id="daf96-127">In ASP.NET 4.x Web API, one way to do this was using the <xref:System.Web.Http.HttpResponseException> type.</span></span> <span data-ttu-id="daf96-128">ASP.NET Core enthält keinen entsprechenden Typ.</span><span class="sxs-lookup"><span data-stu-id="daf96-128">ASP.NET Core doesn't include an equivalent type.</span></span> <span data-ttu-id="daf96-129">Die Unterstützung für `HttpResponseException` kann mit folgenden Schritten hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="daf96-129">Support for `HttpResponseException` can be added with the following steps:</span></span>

1. <span data-ttu-id="daf96-130">Erstellen Sie einen bekannten Ausnahmetyp namens `HttpResponseException`:</span><span class="sxs-lookup"><span data-stu-id="daf96-130">Create a well-known exception type named `HttpResponseException`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. <span data-ttu-id="daf96-131">Erstellen Sie einen Aktionsfilter namens `HttpResponseExceptionFilter`:</span><span class="sxs-lookup"><span data-stu-id="daf96-131">Create an action filter named `HttpResponseExceptionFilter`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

1. <span data-ttu-id="daf96-132">Fügen Sie den Aktionsfilter in `Startup.ConfigureServices` der Filtersammlung hinzu:</span><span class="sxs-lookup"><span data-stu-id="daf96-132">In `Startup.ConfigureServices`, add the action filter to the filters collection:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a><span data-ttu-id="daf96-133">Antwort auf Überprüfungsfehler</span><span class="sxs-lookup"><span data-stu-id="daf96-133">Validation failure error response</span></span>

<span data-ttu-id="daf96-134">Bei Web-API-Controllern antwortet MVC mit dem Antworttyp <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>, wenn bei der Modellüberprüfung ein Fehler auftritt.</span><span class="sxs-lookup"><span data-stu-id="daf96-134">For web API controllers, MVC responds with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> response type when model validation fails.</span></span> <span data-ttu-id="daf96-135">MVC verwendet die Ergebnisse von <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory>, um die Fehlerantwort auf einen Überprüfungsfehler zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="daf96-135">MVC uses the results of <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> to construct the error response for a validation failure.</span></span> <span data-ttu-id="daf96-136">Das folgende Beispiel verwendet die Factory, um den Standardantworttyp von <xref:Microsoft.AspNetCore.Mvc.SerializableError> in `Startup.ConfigureServices` zu ändern:</span><span class="sxs-lookup"><span data-stu-id="daf96-136">The following example uses the factory to change the default response type to <xref:Microsoft.AspNetCore.Mvc.SerializableError> in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a><span data-ttu-id="daf96-137">Fehlerantwort des Clients</span><span class="sxs-lookup"><span data-stu-id="daf96-137">Client error response</span></span>

<span data-ttu-id="daf96-138">Ein *Fehlerergebnis* ist als Ergebnis mit dem HTTP-Statuscode 400 oder höher definiert.</span><span class="sxs-lookup"><span data-stu-id="daf96-138">An *error result* is defined as a result with an HTTP status code of 400 or higher.</span></span> <span data-ttu-id="daf96-139">Bei Web-API-Controllern transformiert MVC ein Fehlerergebnis in ein Ergebnis mit <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="daf96-139">For web API controllers, MVC transforms an error result to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="daf96-140">Die Fehlerantwort kann auf eine der folgenden Arten konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="daf96-140">The error response can be configured in one of the following ways:</span></span>

1. [<span data-ttu-id="daf96-141">Implementieren der ProblemDetailsFactory</span><span class="sxs-lookup"><span data-stu-id="daf96-141">Implement ProblemDetailsFactory</span></span>](#implement-problemdetailsfactory)
1. [<span data-ttu-id="daf96-142">Verwenden von ApiBehaviorOptions.ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="daf96-142">Use ApiBehaviorOptions.ClientErrorMapping</span></span>](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a><span data-ttu-id="daf96-143">Implementieren der ProblemDetailsFactory</span><span class="sxs-lookup"><span data-stu-id="daf96-143">Implement ProblemDetailsFactory</span></span>

<span data-ttu-id="daf96-144">MVC verwendet die `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory`, um alle Instanzen von <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> und <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> zu generieren.</span><span class="sxs-lookup"><span data-stu-id="daf96-144">MVC uses `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` to produce all instances of <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> and <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="daf96-145">Hierzu gehören Clientfehlerantworten, Antworten auf Überprüfungsfehler sowie die Hilfsmethoden `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` und <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem>.</span><span class="sxs-lookup"><span data-stu-id="daf96-145">This includes client error responses, validation failure error responses, and the `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> helper methods.</span></span>

<span data-ttu-id="daf96-146">Um die Antwort mit Problemdetails anzupassen, registrieren Sie eine benutzerdefinierte Implementierung von `ProblemDetailsFactory` in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="daf96-146">To customize the problem details response, register a custom implementation of `ProblemDetailsFactory` in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="daf96-147">Die Fehlerantwort kann so konfiguriert werden wie im Abschnitt [Verwenden von ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="daf96-147">The error response can be configured as outlined in the [Use ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) section.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a><span data-ttu-id="daf96-148">Verwenden von ApiBehaviorOptions.ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="daf96-148">Use ApiBehaviorOptions.ClientErrorMapping</span></span>

<span data-ttu-id="daf96-149">Verwenden Sie die <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping*>-Eigenschaft zum Konfigurieren des Inhalts der `ProblemDetails`-Antwort.</span><span class="sxs-lookup"><span data-stu-id="daf96-149">Use the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping*> property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="daf96-150">Der folgende Code in `Startup.ConfigureServices` aktualisiert beispielsweise die `type`-Eigenschaft für 404-Antworten:</span><span class="sxs-lookup"><span data-stu-id="daf96-150">For example, the following code in `Startup.ConfigureServices` updates the `type` property for 404 responses:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end

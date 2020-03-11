---
title: Fehlerbehandlung in ASP.NET Core
author: rick-anderson
description: Erfahren Sie mehr über die Fehlerbehandlung in ASP.NET Core-Apps.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: fundamentals/error-handling
ms.openlocfilehash: 28b463bccfb8aff4d10b95aa9a984455b4f4b976
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78647071"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="a2b01-103">Fehlerbehandlung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a2b01-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="a2b01-104">Von [Tom Dykstra](https://github.com/tdykstra/) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="a2b01-104">By [Tom Dykstra](https://github.com/tdykstra/) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="a2b01-105">Dieser Artikel beschreibt grundsätzliche Vorgehensweisen zur Behandlung von Fehlern in ASP.NET Core-Web-Apps.</span><span class="sxs-lookup"><span data-stu-id="a2b01-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="a2b01-106">Weitere Informationen für Web-Apps finden Sie unter <xref:web-api/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="a2b01-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="a2b01-107">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)</span><span class="sxs-lookup"><span data-stu-id="a2b01-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="a2b01-108">([Informationen zum Herunterladen](xref:index#how-to-download-a-sample).) Der Artikel enthält Anweisungen zum Festlegen von Präprozessordirektiven (`#if`, `#endif`, `#define`) in der Beispiel-App, um verschiedene Szenarien zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="a2b01-108">([How to download](xref:index#how-to-download-a-sample).) The article includes instructions about how to set preprocessor directives (`#if`, `#endif`, `#define`) in the sample app to enable different scenarios.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="a2b01-109">Seite mit Ausnahmen für Entwickler</span><span class="sxs-lookup"><span data-stu-id="a2b01-109">Developer Exception Page</span></span>

<span data-ttu-id="a2b01-110">Die *Seite mit Ausnahmen für Entwickler* enthält ausführliche Informationen zu Anforderungsausnahmen.</span><span class="sxs-lookup"><span data-stu-id="a2b01-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="a2b01-111">Die Seite wird vom Paket [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) zur Verfügung gestellt, das im [Metapaket „Microsoft.AspNetCore.App“](xref:fundamentals/metapackage-app) enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="a2b01-111">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="a2b01-112">Fügen Sie der `Startup.Configure`-Methode Code zum Aktivieren der Seite hinzu, wenn die App in der [Entwicklungsumgebung](xref:fundamentals/environments) ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="a2b01-112">Add code to the `Startup.Configure` method to enable the page when the app is running in the Development [environment](xref:fundamentals/environments):</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="a2b01-113">Fügen Sie den Aufruf von <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> vor Middleware an der Stelle ein, an der Sie Ausnahmen abfangen möchten.</span><span class="sxs-lookup"><span data-stu-id="a2b01-113">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> before any middleware that you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="a2b01-114">Aktivieren Sie die Seite mit Ausnahmen für Entwickler **nur dann, wenn die App in der Entwicklungsumgebung ausgeführt wird**.</span><span class="sxs-lookup"><span data-stu-id="a2b01-114">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="a2b01-115">Wenn die App in der Produktionsumgebung ausgeführt wird, sollten Sie keine detaillierten Ausnahmeinformationen öffentlich teilen.</span><span class="sxs-lookup"><span data-stu-id="a2b01-115">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="a2b01-116">Weitere Informationen zum Konfigurieren der Umgebungen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="a2b01-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="a2b01-117">Die Seite enthält die folgenden Informationen zu der Ausnahme und der Anforderung:</span><span class="sxs-lookup"><span data-stu-id="a2b01-117">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="a2b01-118">Stapelüberwachung</span><span class="sxs-lookup"><span data-stu-id="a2b01-118">Stack trace</span></span>
* <span data-ttu-id="a2b01-119">Abfragezeichenfolgeparameter (sofern vorhanden)</span><span class="sxs-lookup"><span data-stu-id="a2b01-119">Query string parameters (if any)</span></span>
* <span data-ttu-id="a2b01-120">Cookies (sofern vorhanden)</span><span class="sxs-lookup"><span data-stu-id="a2b01-120">Cookies (if any)</span></span>
* <span data-ttu-id="a2b01-121">Header</span><span class="sxs-lookup"><span data-stu-id="a2b01-121">Headers</span></span>

<span data-ttu-id="a2b01-122">Um in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) die Seite mit Ausnahmen für Entwickler anzuzeigen, verwenden Sie die Präprozessordirektive `DevEnvironment`, und wählen Sie auf der Startseite **Ausnahme auslösen** aus.</span><span class="sxs-lookup"><span data-stu-id="a2b01-122">To see the Developer Exception Page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `DevEnvironment` preprocessor directive and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="a2b01-123">Seite „Ausnahmehandler“</span><span class="sxs-lookup"><span data-stu-id="a2b01-123">Exception handler page</span></span>

<span data-ttu-id="a2b01-124">Um eine benutzerdefinierte Fehlerbehandlung für die Produktionsumgebung zu konfigurieren, verwenden Sie Middleware zur Ausnahmebehandlung.</span><span class="sxs-lookup"><span data-stu-id="a2b01-124">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="a2b01-125">Die Middleware:</span><span class="sxs-lookup"><span data-stu-id="a2b01-125">The middleware:</span></span>

* <span data-ttu-id="a2b01-126">Dient zum Abfangen und Protokollieren von Ausnahmen.</span><span class="sxs-lookup"><span data-stu-id="a2b01-126">Catches and logs exceptions.</span></span>
* <span data-ttu-id="a2b01-127">Führt die Anforderung für die angegebene Seite oder den Controller in einer anderen Pipeline erneut aus.</span><span class="sxs-lookup"><span data-stu-id="a2b01-127">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="a2b01-128">Die Anforderung wird nicht erneut ausgeführt, wenn die Antwort gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="a2b01-128">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="a2b01-129">Im folgenden Beispiel fügt <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> die Middleware zur Ausnahmebehandlung in Nichtentwicklungsumgebungen hinzu:</span><span class="sxs-lookup"><span data-stu-id="a2b01-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="a2b01-130">Die Razor Pages-App-Vorlage stellt im Ordner *Pages* eine Fehlerseite ( *.cshtml*) und <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel>-Klasse (`ErrorModel`) bereit.</span><span class="sxs-lookup"><span data-stu-id="a2b01-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="a2b01-131">Für eine MVC-App enthält die Projektvorlage die Aktionsmethode „Error“ und die Ansicht „Error“.</span><span class="sxs-lookup"><span data-stu-id="a2b01-131">For an MVC app, the project template includes an Error action method and an Error view.</span></span> <span data-ttu-id="a2b01-132">Es folgt die Aktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="a2b01-132">Here's the action method:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="a2b01-133">Markieren Sie die Aktionsmethode für die Fehlerbehandlung nicht mit HTTP-Methodenattributen wie `HttpGet`.</span><span class="sxs-lookup"><span data-stu-id="a2b01-133">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="a2b01-134">Durch explizite Verben könnte bei einigen Anforderungen verhindert werden, dass diese Methode zum Einsatz kommt.</span><span class="sxs-lookup"><span data-stu-id="a2b01-134">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="a2b01-135">Lassen Sie den anonymen Zugriff auf die Methode zu, damit nicht authentifizierte Benutzer die Fehleransicht empfangen können.</span><span class="sxs-lookup"><span data-stu-id="a2b01-135">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="a2b01-136">Zugreifen auf die Ausnahme</span><span class="sxs-lookup"><span data-stu-id="a2b01-136">Access the exception</span></span>

<span data-ttu-id="a2b01-137">Verwenden Sie <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>, um auf die Ausnahme oder den Pfad der ursprünglichen Anforderung in einem Controller für die Fehlerbehandlung oder auf einer Seite zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="a2b01-137">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="a2b01-138">Stellen Sie Clients **keine** vertraulichen Fehlerinformationen bereit.</span><span class="sxs-lookup"><span data-stu-id="a2b01-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="a2b01-139">Die Bereitstellung von Fehlern ist ein Sicherheitsrisiko.</span><span class="sxs-lookup"><span data-stu-id="a2b01-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="a2b01-140">Um in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) die Seite für die Ausnahmebehandlung anzuzeigen, verwenden Sie die Präprozessordirektiven `ProdEnvironment` und `ErrorHandlerPage`, und wählen Sie auf der Startseite **Ausnahme auslösen** aus.</span><span class="sxs-lookup"><span data-stu-id="a2b01-140">To see the exception handling page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerPage` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="a2b01-141">Lambda-Ausdruck für Ausnahmehandler</span><span class="sxs-lookup"><span data-stu-id="a2b01-141">Exception handler lambda</span></span>

<span data-ttu-id="a2b01-142">Eine Alternative zu einer [benutzerdefinierten Ausnahmebehandlungsseite](#exception-handler-page) ist das Angeben eines Lambda-Ausdrucks für <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span><span class="sxs-lookup"><span data-stu-id="a2b01-142">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span></span> <span data-ttu-id="a2b01-143">Die Verwendung eines Lambda-Ausdrucks ermöglicht den Zugriff auf den Fehler, bevor die Antwort zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="a2b01-143">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="a2b01-144">Hier ist ein Beispiel der Verwendung eines Lambda-Ausdrucks für die Ausnahmebehandlung:</span><span class="sxs-lookup"><span data-stu-id="a2b01-144">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="a2b01-145">Im vorangehenden Code wird `await context.Response.WriteAsync(new string(' ', 512));` hinzugefügt, sodass der Internet Explorer-Browser die Fehlermeldung anstelle einer IE-Fehlermeldung anzeigt.</span><span class="sxs-lookup"><span data-stu-id="a2b01-145">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="a2b01-146">Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span><span class="sxs-lookup"><span data-stu-id="a2b01-146">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="a2b01-147">Stellen Sie **keine** vertraulichen Fehlerinformationen aus <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> oder <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> für Clients bereit.</span><span class="sxs-lookup"><span data-stu-id="a2b01-147">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="a2b01-148">Die Bereitstellung von Fehlern ist ein Sicherheitsrisiko.</span><span class="sxs-lookup"><span data-stu-id="a2b01-148">Serving errors is a security risk.</span></span>

<span data-ttu-id="a2b01-149">Um in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) das Ergebnis des Lambda-Ausdrucks für die Ausnahmebehandlung anzuzeigen, verwenden Sie die Präprozessordirektiven `ProdEnvironment` und `ErrorHandlerLambda`, und wählen Sie auf der Startseite **Ausnahme auslösen** aus.</span><span class="sxs-lookup"><span data-stu-id="a2b01-149">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="a2b01-150">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="a2b01-150">UseStatusCodePages</span></span>

<span data-ttu-id="a2b01-151">Ihre ASP.NET Core-App stellt für HTTP-Statuscodes wie *404 – Nicht gefunden* standardmäßig keine Statuscodeseite zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="a2b01-151">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="a2b01-152">Die App gibt einen Statuscode und einen leeren Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="a2b01-152">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="a2b01-153">Verwenden Sie zum Bereitstellen von Statuscodeseiten Middleware für Statuscodeseiten.</span><span class="sxs-lookup"><span data-stu-id="a2b01-153">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="a2b01-154">Die Middleware wird vom Paket [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) zur Verfügung gestellt, das im [Metapaket „Microsoft.AspNetCore.App“](xref:fundamentals/metapackage-app) enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="a2b01-154">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="a2b01-155">Um für gängige Statuscodes einfache Handler im Textformat zu aktivieren, rufen Sie in der `Startup.Configure`-Methode <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> auf:</span><span class="sxs-lookup"><span data-stu-id="a2b01-155">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="a2b01-156">Rufen Sie `UseStatusCodePages` vor Middleware zur Anforderungsverarbeitung auf (z.B. Middleware für statische Dateien und MVC-Middleware).</span><span class="sxs-lookup"><span data-stu-id="a2b01-156">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="a2b01-157">Hier ist ein Beispiel des von den Standardhandlern angezeigten Texts:</span><span class="sxs-lookup"><span data-stu-id="a2b01-157">Here's an example of text displayed by the default handlers:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="a2b01-158">Um in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) eines der verschiedenen Formate für Statuscodeseiten anzuzeigen, verwenden Sie eine der Präprozessordirektiven, die mit `StatusCodePages` beginnen, und wählen Sie auf der Startseite **404 auslösen** aus.</span><span class="sxs-lookup"><span data-stu-id="a2b01-158">To see one of the various status code page formats in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use one of the preprocessor directives that begin with `StatusCodePages`, and select **Trigger a 404** on the home page.</span></span>

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="a2b01-159">UseStatusCodePages mit Formatzeichenfolge</span><span class="sxs-lookup"><span data-stu-id="a2b01-159">UseStatusCodePages with format string</span></span>

<span data-ttu-id="a2b01-160">Um den Inhaltstyp und Text der Antwort anzupassen, verwenden Sie die Überladung von <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*>, die einen Inhaltstyp und eine Formatierungszeichenfolge erfordert:</span><span class="sxs-lookup"><span data-stu-id="a2b01-160">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="a2b01-161">UseStatusCodePages mit Lambda-Ausdruck</span><span class="sxs-lookup"><span data-stu-id="a2b01-161">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="a2b01-162">Um benutzerdefinierten Code für die Fehlerbehandlung und das Schreiben von Antworten anzugeben, verwenden Sie die Überladung von <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*>, die einen Lambda-Ausdruck verwendet:</span><span class="sxs-lookup"><span data-stu-id="a2b01-162">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="a2b01-163">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="a2b01-163">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="a2b01-164">Die Erweiterungsmethode <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>:</span><span class="sxs-lookup"><span data-stu-id="a2b01-164">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> extension method:</span></span>

* <span data-ttu-id="a2b01-165">Sendet den Statuscode *302 Found* (Gefunden) an den Client.</span><span class="sxs-lookup"><span data-stu-id="a2b01-165">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="a2b01-166">Der Client wird an den in der URL-Vorlage angegebenen Standort umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="a2b01-166">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="a2b01-167">Die URL-Vorlage kann, wie im Beispiel gezeigt, einen `{0}`-Platzhalter für den Statuscode enthalten.</span><span class="sxs-lookup"><span data-stu-id="a2b01-167">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="a2b01-168">Wenn die URL-Vorlage mit einer Tilde (~) beginnt, wird die Tilde durch die Angabe für `PathBase` der App ersetzt.</span><span class="sxs-lookup"><span data-stu-id="a2b01-168">If the URL template starts with a tilde (~), the tilde is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="a2b01-169">Wenn Sie in der App auf einen Endpunkt verweisen, müssen Sie eine MVC-Ansicht oder Razor Pages-Seite für den Endpunkt erstellen.</span><span class="sxs-lookup"><span data-stu-id="a2b01-169">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="a2b01-170">Ein Razor Pages-Beispiel finden Sie unter *Pages/StatusCode.cshtml* in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="a2b01-170">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="a2b01-171">Diese Methode wird häufig verwendet, wenn die App Folgendes tun soll:</span><span class="sxs-lookup"><span data-stu-id="a2b01-171">This method is commonly used when the app:</span></span>

* <span data-ttu-id="a2b01-172">Den Client an einen anderen Endpunkt umleiten, in der Regel in Fällen, in denen eine andere App den Fehler verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="a2b01-172">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="a2b01-173">Für Web-Apps gibt die Adressleiste des Browsers des Clients den umgeleiteten Endpunkt wieder.</span><span class="sxs-lookup"><span data-stu-id="a2b01-173">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="a2b01-174">Den ursprünglichen Statuscode mit der anfänglichen Umleitungsantwort nicht beibehalten und zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="a2b01-174">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="a2b01-175">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="a2b01-175">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="a2b01-176">Die Erweiterungsmethode <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>:</span><span class="sxs-lookup"><span data-stu-id="a2b01-176">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> extension method:</span></span>

* <span data-ttu-id="a2b01-177">Gibt den ursprünglichen Statuscode an den Client zurück.</span><span class="sxs-lookup"><span data-stu-id="a2b01-177">Returns the original status code to the client.</span></span>
* <span data-ttu-id="a2b01-178">Generiert den Antworttext durch die erneute Ausführung der Anforderungspipeline mithilfe eines alternativen Pfads.</span><span class="sxs-lookup"><span data-stu-id="a2b01-178">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="a2b01-179">Wenn Sie in der App auf einen Endpunkt verweisen, müssen Sie eine MVC-Ansicht oder Razor Pages-Seite für den Endpunkt erstellen.</span><span class="sxs-lookup"><span data-stu-id="a2b01-179">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="a2b01-180">Ein Razor Pages-Beispiel finden Sie unter *Pages/StatusCode.cshtml* in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="a2b01-180">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="a2b01-181">Diese Methode wird häufig verwendet, wenn die App Folgendes tun soll:</span><span class="sxs-lookup"><span data-stu-id="a2b01-181">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="a2b01-182">Die Anforderung ohne Umleitung an einen anderen Endpunkt verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="a2b01-182">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="a2b01-183">Für Web-Apps gibt die Adressleiste des Browsers des Clients den ursprünglich angeforderten Endpunkt wieder.</span><span class="sxs-lookup"><span data-stu-id="a2b01-183">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="a2b01-184">Den ursprünglichen Statuscode mit der Antwort beibehalten und zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="a2b01-184">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="a2b01-185">Die Vorlagen für URL und Abfragezeichenfolgen können für den Statuscode einen Platzhalter (`{0}`) enthalten.</span><span class="sxs-lookup"><span data-stu-id="a2b01-185">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="a2b01-186">Die URL-Vorlage muss mit einem Schrägstrich (`/`) beginnen.</span><span class="sxs-lookup"><span data-stu-id="a2b01-186">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="a2b01-187">Wenn Sie im Pfad einen Platzhalter verwenden, vergewissern Sie sich, dass der Endpunkt (Seite oder Controller) das Pfadsegment verarbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="a2b01-187">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="a2b01-188">Eine Razor Page für Fehler sollte z.B. den Wert des optionalen Pfadsegments mit der `@page`-Anweisung akzeptieren:</span><span class="sxs-lookup"><span data-stu-id="a2b01-188">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="a2b01-189">Der Endpunkt, der den Fehler verarbeitet, kann die ursprüngliche URL abrufen, die den Fehler generiert hat (siehe folgendes Beispiel):</span><span class="sxs-lookup"><span data-stu-id="a2b01-189">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="a2b01-190">Deaktivieren von Statuscodeseiten</span><span class="sxs-lookup"><span data-stu-id="a2b01-190">Disable status code pages</span></span>

<span data-ttu-id="a2b01-191">Verwenden Sie das Attribut [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute), um Statuscodeseiten für einen MVC-Controller oder eine MVC-Aktionsmethode zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="a2b01-191">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="a2b01-192">Verwenden Sie <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>, um Statuscodeseiten für bestimmte Anforderungen in der Handlermethode von Razor Pages oder in einem MVC-Controller zu deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="a2b01-192">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="a2b01-193">Ausnahmebehandlungscode</span><span class="sxs-lookup"><span data-stu-id="a2b01-193">Exception-handling code</span></span>

<span data-ttu-id="a2b01-194">Code auf Seiten zur Ausnahmebehandlung kann Ausnahmen auslösen.</span><span class="sxs-lookup"><span data-stu-id="a2b01-194">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="a2b01-195">Es ist häufig empfehlenswert, wenn Produktionsfehlerseiten nur statische Inhalte aufweisen.</span><span class="sxs-lookup"><span data-stu-id="a2b01-195">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="a2b01-196">Antwortheader</span><span class="sxs-lookup"><span data-stu-id="a2b01-196">Response headers</span></span>

<span data-ttu-id="a2b01-197">Nachdem die Header für eine Antwort gesendet wurden:</span><span class="sxs-lookup"><span data-stu-id="a2b01-197">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="a2b01-198">Die App kann den Statuscode der Antwort nicht ändern.</span><span class="sxs-lookup"><span data-stu-id="a2b01-198">The app can't change the response's status code.</span></span>
* <span data-ttu-id="a2b01-199">Weder Ausnahmeseiten noch Handler können ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="a2b01-199">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="a2b01-200">Die Antwort muss abgeschlossen oder die Verbindung unterbrochen werden.</span><span class="sxs-lookup"><span data-stu-id="a2b01-200">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="a2b01-201">Sichere Ausnahmebehandlung</span><span class="sxs-lookup"><span data-stu-id="a2b01-201">Server exception handling</span></span>

<span data-ttu-id="a2b01-202">Neben der Ausnahmebehandlungslogik in Ihrer App kann die [HTTP-Serverimplementierung](xref:fundamentals/servers/index) einige Ausnahmebehandlungen durchführen.</span><span class="sxs-lookup"><span data-stu-id="a2b01-202">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="a2b01-203">Wenn der Server eine Ausnahme erfasst, bevor die Antwortheader gesendet werden, sendet der Server die Antwort *500 – Interner Serverfehler* ohne Antworttext.</span><span class="sxs-lookup"><span data-stu-id="a2b01-203">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="a2b01-204">Wenn der Server eine Ausnahme auffängt, nachdem die Antwortheader gesendet wurden, schließt der Server die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="a2b01-204">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="a2b01-205">Anforderungen, die nicht von Ihrer App verarbeitet werden, werden vom Server verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="a2b01-205">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="a2b01-206">Jede Ausnahme, die bei der Anforderungsverarbeitung durch den Server auftritt, wird durch die Ausnahmebehandlung des Servers verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="a2b01-206">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="a2b01-207">Die benutzerdefinierten Fehlerseiten der App, Middleware zur Fehlerbehandlung und Filter haben keine Auswirkungen auf dieses Verhalten.</span><span class="sxs-lookup"><span data-stu-id="a2b01-207">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="a2b01-208">Fehlerbehandlung während des Starts</span><span class="sxs-lookup"><span data-stu-id="a2b01-208">Startup exception handling</span></span>

<span data-ttu-id="a2b01-209">Nur auf Hostebene können Ausnahmen behandelt werden, die während des Starts einer App auftreten.</span><span class="sxs-lookup"><span data-stu-id="a2b01-209">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="a2b01-210">Der Host kann für das [Erfassen von Startfehlern](xref:fundamentals/host/web-host#capture-startup-errors) und [Erfassen detaillierter Fehler](xref:fundamentals/host/web-host#detailed-errors) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="a2b01-210">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="a2b01-211">Die Hostingebene kann nur dann für einen beim Start erfassten Fehler eine Fehlerseite anzeigen, wenn der Fehler nach der Bindung an die Hostadresse bzw. den Port auftritt.</span><span class="sxs-lookup"><span data-stu-id="a2b01-211">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="a2b01-212">Wenn die Bindung misslingt:</span><span class="sxs-lookup"><span data-stu-id="a2b01-212">If binding fails:</span></span>

* <span data-ttu-id="a2b01-213">Die Hostebene protokolliert eine kritische Ausnahme.</span><span class="sxs-lookup"><span data-stu-id="a2b01-213">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="a2b01-214">Der DotNet-Prozess stürzt ab.</span><span class="sxs-lookup"><span data-stu-id="a2b01-214">The dotnet process crashes.</span></span>
* <span data-ttu-id="a2b01-215">Wenn der HTTP-Server [Kestrel](xref:fundamentals/servers/kestrel) heißt, wird keine Fehlerseite angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a2b01-215">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="a2b01-216">Wenn sie auf [IIS](/iis) (oder Azure App Service) oder [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ausgeführt wird, wird ein *Prozessfehler 502.5* vom [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) zurückgegeben, wenn der Prozess nicht starten kann.</span><span class="sxs-lookup"><span data-stu-id="a2b01-216">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="a2b01-217">Weitere Informationen finden Sie unter <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="a2b01-217">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="a2b01-218">Datenbank-Fehlerseite</span><span class="sxs-lookup"><span data-stu-id="a2b01-218">Database error page</span></span>

<span data-ttu-id="a2b01-219">Die Middleware „Datenbank-Fehlerseite“ erfasst datenbankbezogene Ausnahmen, die mithilfe von Entity Framework-Migrationen aufgelöst werden können.</span><span class="sxs-lookup"><span data-stu-id="a2b01-219">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="a2b01-220">Wenn diese Ausnahmen auftreten, wird eine HTML-Antwort mit Details zu möglichen Aktionen zum Beheben des Problems generiert.</span><span class="sxs-lookup"><span data-stu-id="a2b01-220">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="a2b01-221">Diese Seite darf nur in der Entwicklungsumgebung aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="a2b01-221">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="a2b01-222">Aktivieren Sie die Seite, indem Sie `Startup.Configure` Code hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="a2b01-222">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="a2b01-223">Ausnahmefilter</span><span class="sxs-lookup"><span data-stu-id="a2b01-223">Exception filters</span></span>

<span data-ttu-id="a2b01-224">In MVC-Apps können Ausnahmefilter global oder auf einen Controller oder eine Aktion bezogen konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="a2b01-224">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="a2b01-225">In Razor Pages-Apps können sie global oder auf ein Seitenmodell bezogen konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="a2b01-225">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="a2b01-226">Diese Filter verarbeiten jede nicht behandelte Ausnahme, die während der Ausführung eine Controlleraktion oder eines anderen Filters auftritt.</span><span class="sxs-lookup"><span data-stu-id="a2b01-226">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="a2b01-227">Weitere Informationen finden Sie unter <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="a2b01-227">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="a2b01-228">Ausnahmefilter eignen sich zum Auffangen von Ausnahmen, die in MVC-Aktionen auftreten. Sie sind jedoch nicht so flexibel wie Middleware für die Ausnahmebehandlung.</span><span class="sxs-lookup"><span data-stu-id="a2b01-228">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="a2b01-229">Es wird empfohlen, die Middleware zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="a2b01-229">We recommend using the middleware.</span></span> <span data-ttu-id="a2b01-230">Verwenden Sie Filter nur dann, wenn Sie für die Fehlerbehandlung auf Grundlage einer ausgewählten MVC-Aktion eine andere Strategie wählen müssen.</span><span class="sxs-lookup"><span data-stu-id="a2b01-230">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="a2b01-231">Modellstatusfehler</span><span class="sxs-lookup"><span data-stu-id="a2b01-231">Model state errors</span></span>

<span data-ttu-id="a2b01-232">Informationen zum Behandeln von Modellstatusfehlern finden Sie unter [Modellbindung](xref:mvc/models/model-binding) und [Modellvalidierung](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="a2b01-232">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a2b01-233">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a2b01-233">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

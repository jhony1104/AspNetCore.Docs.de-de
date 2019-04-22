---
title: Fehlerbehandlung in ASP.NET Core
author: tdykstra
description: Erfahren Sie mehr über die Fehlerbehandlung in ASP.NET Core-Apps.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 04/07/2019
uid: fundamentals/error-handling
ms.openlocfilehash: cbb9462a3c6010e074dc391aa128ac2cbb901456
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59705577"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="e611c-103">Fehlerbehandlung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e611c-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="e611c-104">Von [Tom Dykstra](https://github.com/tdykstra/), [Luke Latham](https://github.com/guardrex) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="e611c-104">By [Tom Dykstra](https://github.com/tdykstra/), [Luke Latham](https://github.com/guardrex), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="e611c-105">Dieser Artikel beschreibt grundsätzliche Vorgehensweisen zur Behandlung von Fehlern in ASP.NET Core-Anwendungen.</span><span class="sxs-lookup"><span data-stu-id="e611c-105">This article covers common approaches to handling errors in ASP.NET Core apps.</span></span>

<span data-ttu-id="e611c-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)</span><span class="sxs-lookup"><span data-stu-id="e611c-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="e611c-107">([Informationen zum Herunterladen](xref:index#how-to-download-a-sample).) Der Artikel enthält Anweisungen zum Festlegen von Präprozessordirektiven (`#if`, `#endif`, `#define`) in der Beispiel-App, um verschiedene Szenarien zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="e611c-107">([How to download](xref:index#how-to-download-a-sample).) The article includes instructions about how to set preprocessor directives (`#if`, `#endif`, `#define`) in the sample app to enable different scenarios.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="e611c-108">Seite mit Ausnahmen für Entwickler</span><span class="sxs-lookup"><span data-stu-id="e611c-108">Developer Exception Page</span></span>

<span data-ttu-id="e611c-109">Die *Seite mit Ausnahmen für Entwickler* enthält ausführliche Informationen zu Anforderungsausnahmen.</span><span class="sxs-lookup"><span data-stu-id="e611c-109">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="e611c-110">Die Seite wird vom Paket [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) zur Verfügung gestellt, das im [Metapaket „Microsoft.AspNetCore.App“](xref:fundamentals/metapackage-app) enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="e611c-110">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="e611c-111">Fügen Sie der `Startup.Configure`-Methode Code zum Aktivieren der Seite hinzu, wenn die App in der [Entwicklungsumgebung](xref:fundamentals/environments) ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="e611c-111">Add code to the `Startup.Configure` method to enable the page when the app is running in the Development [environment](xref:fundamentals/environments):</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="e611c-112">Fügen Sie den Aufruf von <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> vor Middleware an der Stelle ein, an der Sie Ausnahmen abfangen möchten.</span><span class="sxs-lookup"><span data-stu-id="e611c-112">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> before any middleware that you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="e611c-113">Aktivieren Sie die Seite mit Ausnahmen für Entwickler **nur dann, wenn die App in der Entwicklungsumgebung ausgeführt wird**.</span><span class="sxs-lookup"><span data-stu-id="e611c-113">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="e611c-114">Wenn die App in der Produktionsumgebung ausgeführt wird, sollten Sie keine detaillierten Ausnahmeinformationen öffentlich teilen.</span><span class="sxs-lookup"><span data-stu-id="e611c-114">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="e611c-115">Weitere Informationen zum Konfigurieren der Umgebungen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="e611c-115">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="e611c-116">Die Seite enthält die folgenden Informationen zu der Ausnahme und der Anforderung:</span><span class="sxs-lookup"><span data-stu-id="e611c-116">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="e611c-117">Stapelüberwachung</span><span class="sxs-lookup"><span data-stu-id="e611c-117">Stack trace</span></span>
* <span data-ttu-id="e611c-118">Abfragezeichenfolgeparameter (sofern vorhanden)</span><span class="sxs-lookup"><span data-stu-id="e611c-118">Query string parameters (if any)</span></span>
* <span data-ttu-id="e611c-119">Cookies (sofern vorhanden)</span><span class="sxs-lookup"><span data-stu-id="e611c-119">Cookies (if any)</span></span>
* <span data-ttu-id="e611c-120">Header</span><span class="sxs-lookup"><span data-stu-id="e611c-120">Headers</span></span>

<span data-ttu-id="e611c-121">Um in der [Beispiel-App](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) die Seite mit Ausnahmen für Entwickler anzuzeigen, verwenden Sie die Präprozessordirektive `DevEnvironment`, und wählen Sie auf der Startseite **Ausnahme auslösen** aus.</span><span class="sxs-lookup"><span data-stu-id="e611c-121">To see the Developer Exception Page in the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `DevEnvironment` preprocessor directive and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="e611c-122">Seite „Ausnahmehandler“</span><span class="sxs-lookup"><span data-stu-id="e611c-122">Exception handler page</span></span>

<span data-ttu-id="e611c-123">Um eine benutzerdefinierte Fehlerbehandlung für die Produktionsumgebung zu konfigurieren, verwenden Sie Middleware zur Ausnahmebehandlung.</span><span class="sxs-lookup"><span data-stu-id="e611c-123">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="e611c-124">Die Middleware:</span><span class="sxs-lookup"><span data-stu-id="e611c-124">The middleware:</span></span>

* <span data-ttu-id="e611c-125">Dient zum Abfangen und Protokollieren von Ausnahmen.</span><span class="sxs-lookup"><span data-stu-id="e611c-125">Catches and logs exceptions.</span></span>
* <span data-ttu-id="e611c-126">Führt die Anforderung für die angegebene Seite oder den Controller in einer anderen Pipeline erneut aus.</span><span class="sxs-lookup"><span data-stu-id="e611c-126">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="e611c-127">Die Anforderung wird nicht erneut ausgeführt, wenn die Antwort gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="e611c-127">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="e611c-128">Im folgenden Beispiel fügt <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> die Middleware zur Ausnahmebehandlung in Nichtentwicklungsumgebungen hinzu:</span><span class="sxs-lookup"><span data-stu-id="e611c-128">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="e611c-129">Die Razor Pages-App-Vorlage stellt im Ordner *Pages* eine Fehlerseite (*.cshtml*) und <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel>-Klasse (`ErrorModel`) bereit.</span><span class="sxs-lookup"><span data-stu-id="e611c-129">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="e611c-130">Für eine MVC-App enthält die Projektvorlage die Aktionsmethode „Error“ und die Ansicht „Error“.</span><span class="sxs-lookup"><span data-stu-id="e611c-130">For an MVC app, the project template includes an Error action method and an Error view.</span></span> <span data-ttu-id="e611c-131">Es folgt die Aktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="e611c-131">Here's the action method:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="e611c-132">Die Aktionsmethode zur Fehlerbehandlung wird nicht durch HTTP-Methodenattribute wie `HttpGet` ergänzt.</span><span class="sxs-lookup"><span data-stu-id="e611c-132">Don't decorate the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="e611c-133">Durch explizite Verben könnte bei einigen Anforderungen verhindert werden, dass diese Methode zum Einsatz kommt.</span><span class="sxs-lookup"><span data-stu-id="e611c-133">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="e611c-134">Lassen Sie den anonymen Zugriff auf die Methode zu, damit nicht authentifizierte Benutzer die Fehleransicht empfangen können.</span><span class="sxs-lookup"><span data-stu-id="e611c-134">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="e611c-135">Zugreifen auf die Ausnahme</span><span class="sxs-lookup"><span data-stu-id="e611c-135">Access the exception</span></span>

<span data-ttu-id="e611c-136">Verwenden Sie <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>, um auf die Ausnahme oder den Pfad der ursprünglichen Anforderung in einem Controller für die Fehlerbehandlung oder auf einer Seite zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="e611c-136">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="e611c-137">Stellen Sie Clients **keine** vertraulichen Fehlerinformationen bereit.</span><span class="sxs-lookup"><span data-stu-id="e611c-137">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="e611c-138">Die Bereitstellung von Fehlern ist ein Sicherheitsrisiko.</span><span class="sxs-lookup"><span data-stu-id="e611c-138">Serving errors is a security risk.</span></span>

<span data-ttu-id="e611c-139">Um in der [Beispiel-App](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) die Seite für die Ausnahmebehandlung anzuzeigen, verwenden Sie die Präprozessordirektiven `ProdEnvironment` und `ErrorHandlerPage`, und wählen Sie auf der Startseite **Ausnahme auslösen** aus.</span><span class="sxs-lookup"><span data-stu-id="e611c-139">To see the exception handling page in the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerPage` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="e611c-140">Lambda-Ausdruck für Ausnahmehandler</span><span class="sxs-lookup"><span data-stu-id="e611c-140">Exception handler lambda</span></span>

<span data-ttu-id="e611c-141">Eine Alternative zu einer [benutzerdefinierten Ausnahmebehandlungsseite](#exception-handler-page) ist das Angeben eines Lambda-Ausdrucks für <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span><span class="sxs-lookup"><span data-stu-id="e611c-141">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span></span> <span data-ttu-id="e611c-142">Die Verwendung eines Lambda-Ausdrucks ermöglicht den Zugriff auf den Fehler, bevor die Antwort zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="e611c-142">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="e611c-143">Hier ist ein Beispiel der Verwendung eines Lambda-Ausdrucks für die Ausnahmebehandlung:</span><span class="sxs-lookup"><span data-stu-id="e611c-143">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

> [!WARNING]
> <span data-ttu-id="e611c-144">Stellen Sie **keine** vertraulichen Fehlerinformationen aus <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> oder <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> für Clients bereit.</span><span class="sxs-lookup"><span data-stu-id="e611c-144">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="e611c-145">Die Bereitstellung von Fehlern ist ein Sicherheitsrisiko.</span><span class="sxs-lookup"><span data-stu-id="e611c-145">Serving errors is a security risk.</span></span>

<span data-ttu-id="e611c-146">Um in der [Beispiel-App](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) das Ergebnis des Lambda-Ausdrucks für die Ausnahmebehandlung anzuzeigen, verwenden Sie die Präprozessordirektiven `ProdEnvironment` und `ErrorHandlerLambda`, und wählen Sie auf der Startseite **Ausnahme auslösen** aus.</span><span class="sxs-lookup"><span data-stu-id="e611c-146">To see the result of the exception handling lambda in the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="e611c-147">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="e611c-147">UseStatusCodePages</span></span>

<span data-ttu-id="e611c-148">Ihre ASP.NET Core-App stellt für HTTP-Statuscodes wie *404 – Nicht gefunden* standardmäßig keine Statuscodeseite zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="e611c-148">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="e611c-149">Die App gibt einen Statuscode und einen leeren Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="e611c-149">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="e611c-150">Verwenden Sie zum Bereitstellen von Statuscodeseiten Middleware für Statuscodeseiten.</span><span class="sxs-lookup"><span data-stu-id="e611c-150">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="e611c-151">Die Middleware wird vom Paket [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) zur Verfügung gestellt, das im [Metapaket „Microsoft.AspNetCore.App“](xref:fundamentals/metapackage-app) enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="e611c-151">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="e611c-152">Um für gängige Statuscodes einfache Handler im Textformat zu aktivieren, rufen Sie in der `Startup.Configure`-Methode <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> auf:</span><span class="sxs-lookup"><span data-stu-id="e611c-152">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="e611c-153">Rufen Sie `UseStatusCodePages` vor Middleware zur Anforderungsverarbeitung auf (z.B. Middleware für statische Dateien und MVC-Middleware).</span><span class="sxs-lookup"><span data-stu-id="e611c-153">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="e611c-154">Hier ist ein Beispiel des von den Standardhandlern angezeigten Texts:</span><span class="sxs-lookup"><span data-stu-id="e611c-154">Here's an example of text displayed by the default handlers:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="e611c-155">Um in der [Beispiel-App](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) eines der verschiedenen Formate für Statuscodeseiten anzuzeigen, verwenden Sie eine der Präprozessordirektiven, die mit `StatusCodePages` beginnen, und wählen Sie auf der Startseite **404 auslösen** aus.</span><span class="sxs-lookup"><span data-stu-id="e611c-155">To see one of the various status code page formats in the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use one of the preprocessor directives that begin with `StatusCodePages`, and select **Trigger a 404** on the home page.</span></span>

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="e611c-156">UseStatusCodePages mit Formatzeichenfolge</span><span class="sxs-lookup"><span data-stu-id="e611c-156">UseStatusCodePages with format string</span></span>

<span data-ttu-id="e611c-157">Um den Inhaltstyp und Text der Antwort anzupassen, verwenden Sie die Überladung von <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*>, die einen Inhaltstyp und eine Formatierungszeichenfolge erfordert:</span><span class="sxs-lookup"><span data-stu-id="e611c-157">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="e611c-158">UseStatusCodePages mit Lambda-Ausdruck</span><span class="sxs-lookup"><span data-stu-id="e611c-158">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="e611c-159">Um benutzerdefinierten Code für die Fehlerbehandlung und das Schreiben von Antworten anzugeben, verwenden Sie die Überladung von <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*>, die einen Lambda-Ausdruck verwendet:</span><span class="sxs-lookup"><span data-stu-id="e611c-159">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirect"></a><span data-ttu-id="e611c-160">UseStatusCodePagesWithRedirect</span><span class="sxs-lookup"><span data-stu-id="e611c-160">UseStatusCodePagesWithRedirect</span></span>

<span data-ttu-id="e611c-161">Die Erweiterungsmethode <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>:</span><span class="sxs-lookup"><span data-stu-id="e611c-161">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> extension method:</span></span>

* <span data-ttu-id="e611c-162">Sendet den Statuscode *302 Found* (Gefunden) an den Client.</span><span class="sxs-lookup"><span data-stu-id="e611c-162">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="e611c-163">Der Client wird an den in der URL-Vorlage angegebenen Standort umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="e611c-163">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="e611c-164">Die URL-Vorlage kann, wie im Beispiel gezeigt, einen `{0}`-Platzhalter für den Statuscode enthalten.</span><span class="sxs-lookup"><span data-stu-id="e611c-164">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="e611c-165">Wenn die URL-Vorlage mit einer Tilde (~) beginnt, wird die Tilde durch die Angabe für `PathBase` der App ersetzt.</span><span class="sxs-lookup"><span data-stu-id="e611c-165">If the URL template starts with a tilde (~), the tilde is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="e611c-166">Wenn Sie in der App auf einen Endpunkt verweisen, müssen Sie eine MVC-Ansicht oder Razor Pages-Seite für den Endpunkt erstellen.</span><span class="sxs-lookup"><span data-stu-id="e611c-166">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="e611c-167">Ein Razor Pages-Beispiel finden Sie in der [Beispiel-App](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) unter [StatusCode.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x/Pages/StatusCode.cshtml).</span><span class="sxs-lookup"><span data-stu-id="e611c-167">For a Razor Pages example, see [StatusCode.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x/Pages/StatusCode.cshtml) in the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="e611c-168">Diese Methode wird häufig verwendet, wenn die App Folgendes tun soll:</span><span class="sxs-lookup"><span data-stu-id="e611c-168">This method is commonly used when the app:</span></span>

* <span data-ttu-id="e611c-169">Den Client an einen anderen Endpunkt umleiten, in der Regel in Fällen, in denen eine andere App den Fehler verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="e611c-169">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="e611c-170">Für Web-Apps gibt die Adressleiste des Browsers des Clients den umgeleiteten Endpunkt wieder.</span><span class="sxs-lookup"><span data-stu-id="e611c-170">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="e611c-171">Den ursprünglichen Statuscode mit der anfänglichen Umleitungsantwort nicht beibehalten und zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="e611c-171">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="e611c-172">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="e611c-172">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="e611c-173">Die Erweiterungsmethode <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>:</span><span class="sxs-lookup"><span data-stu-id="e611c-173">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> extension method:</span></span>

* <span data-ttu-id="e611c-174">Gibt den ursprünglichen Statuscode an den Client zurück.</span><span class="sxs-lookup"><span data-stu-id="e611c-174">Returns the original status code to the client.</span></span>
* <span data-ttu-id="e611c-175">Generiert den Antworttext durch die erneute Ausführung der Anforderungspipeline mithilfe eines alternativen Pfads.</span><span class="sxs-lookup"><span data-stu-id="e611c-175">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="e611c-176">Wenn Sie in der App auf einen Endpunkt verweisen, müssen Sie eine MVC-Ansicht oder Razor Pages-Seite für den Endpunkt erstellen.</span><span class="sxs-lookup"><span data-stu-id="e611c-176">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="e611c-177">Ein Razor Pages-Beispiel finden Sie in der [Beispiel-App](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) unter [StatusCode.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x/Pages/StatusCode.cshtml).</span><span class="sxs-lookup"><span data-stu-id="e611c-177">For a Razor Pages example, see [StatusCode.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x/Pages/StatusCode.cshtml) in the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="e611c-178">Diese Methode wird häufig verwendet, wenn die App Folgendes tun soll:</span><span class="sxs-lookup"><span data-stu-id="e611c-178">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="e611c-179">Die Anforderung ohne Umleitung an einen anderen Endpunkt verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="e611c-179">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="e611c-180">Für Web-Apps gibt die Adressleiste des Browsers des Clients den ursprünglich angeforderten Endpunkt wieder.</span><span class="sxs-lookup"><span data-stu-id="e611c-180">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="e611c-181">Den ursprünglichen Statuscode mit der Antwort beibehalten und zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="e611c-181">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="e611c-182">Die Vorlagen für URL und Abfragezeichenfolgen können für den Statuscode einen Platzhalter (`{0}`) enthalten.</span><span class="sxs-lookup"><span data-stu-id="e611c-182">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="e611c-183">Die URL-Vorlage muss mit einem Schrägstrich (`/`) beginnen.</span><span class="sxs-lookup"><span data-stu-id="e611c-183">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="e611c-184">Wenn Sie im Pfad einen Platzhalter verwenden, vergewissern Sie sich, dass der Endpunkt (Seite oder Controller) das Pfadsegment verarbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="e611c-184">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="e611c-185">Eine Razor Page für Fehler sollte z.B. den Wert des optionalen Pfadsegments mit der `@page`-Anweisung akzeptieren:</span><span class="sxs-lookup"><span data-stu-id="e611c-185">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="e611c-186">Der Endpunkt, der den Fehler verarbeitet, kann die ursprüngliche URL abrufen, die den Fehler generiert hat (siehe folgendes Beispiel):</span><span class="sxs-lookup"><span data-stu-id="e611c-186">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="e611c-187">Deaktivieren von Statuscodeseiten</span><span class="sxs-lookup"><span data-stu-id="e611c-187">Disable status code pages</span></span>

<span data-ttu-id="e611c-188">Statuscodeseiten können für bestimmte Anforderungen in der Handlermethode einer Razor-Seite oder in einem MVC-Controller deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="e611c-188">Status code pages can be disabled for specific requests in a Razor Pages handler method or in an MVC controller.</span></span> <span data-ttu-id="e611c-189">Verwenden Sie zum Deaktivieren von Statuscodeseiten <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span><span class="sxs-lookup"><span data-stu-id="e611c-189">To disable status code pages, use the <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="e611c-190">Ausnahmebehandlungscode</span><span class="sxs-lookup"><span data-stu-id="e611c-190">Exception-handling code</span></span>

<span data-ttu-id="e611c-191">Code auf Seiten zur Ausnahmebehandlung kann Ausnahmen auslösen.</span><span class="sxs-lookup"><span data-stu-id="e611c-191">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="e611c-192">Es ist häufig empfehlenswert, wenn Produktionsfehlerseiten nur statische Inhalte aufweisen.</span><span class="sxs-lookup"><span data-stu-id="e611c-192">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="e611c-193">Antwortheader</span><span class="sxs-lookup"><span data-stu-id="e611c-193">Response headers</span></span>

<span data-ttu-id="e611c-194">Nachdem die Header für eine Antwort gesendet wurden:</span><span class="sxs-lookup"><span data-stu-id="e611c-194">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="e611c-195">Die App kann den Statuscode der Antwort nicht ändern.</span><span class="sxs-lookup"><span data-stu-id="e611c-195">The app can't change the response's status code.</span></span>
* <span data-ttu-id="e611c-196">Weder Ausnahmeseiten noch Handler können ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="e611c-196">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="e611c-197">Die Antwort muss abgeschlossen oder die Verbindung unterbrochen werden.</span><span class="sxs-lookup"><span data-stu-id="e611c-197">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="e611c-198">Sichere Ausnahmebehandlung</span><span class="sxs-lookup"><span data-stu-id="e611c-198">Server exception handling</span></span>

<span data-ttu-id="e611c-199">Neben der Ausnahmebehandlungslogik in Ihrer App kann die [HTTP-Serverimplementierung](xref:fundamentals/servers/index) einige Ausnahmebehandlungen durchführen.</span><span class="sxs-lookup"><span data-stu-id="e611c-199">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="e611c-200">Wenn der Server eine Ausnahme erfasst, bevor die Antwortheader gesendet werden, sendet der Server die Antwort *500 – Interner Serverfehler* ohne Antworttext.</span><span class="sxs-lookup"><span data-stu-id="e611c-200">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="e611c-201">Wenn der Server eine Ausnahme auffängt, nachdem die Antwortheader gesendet wurden, schließt der Server die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="e611c-201">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="e611c-202">Anforderungen, die nicht von Ihrer App verarbeitet werden, werden vom Server verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="e611c-202">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="e611c-203">Jede Ausnahme, die bei der Anforderungsverarbeitung durch den Server auftritt, wird durch die Ausnahmebehandlung des Servers verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="e611c-203">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="e611c-204">Die benutzerdefinierten Fehlerseiten der App, Middleware zur Fehlerbehandlung und Filter haben keine Auswirkungen auf dieses Verhalten.</span><span class="sxs-lookup"><span data-stu-id="e611c-204">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="e611c-205">Fehlerbehandlung während des Starts</span><span class="sxs-lookup"><span data-stu-id="e611c-205">Startup exception handling</span></span>

<span data-ttu-id="e611c-206">Nur auf Hostebene können Ausnahmen behandelt werden, die während des Starts einer App auftreten.</span><span class="sxs-lookup"><span data-stu-id="e611c-206">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="e611c-207">Der Host kann für das [Erfassen von Startfehlern](xref:fundamentals/host/web-host#capture-startup-errors) und [Erfassen detaillierter Fehler](xref:fundamentals/host/web-host#detailed-errors) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="e611c-207">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="e611c-208">Die Hostingebene kann nur dann für einen beim Start erfassten Fehler eine Fehlerseite anzeigen, wenn der Fehler nach der Bindung an die Hostadresse bzw. den Port auftritt.</span><span class="sxs-lookup"><span data-stu-id="e611c-208">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="e611c-209">Wenn die Bindung misslingt:</span><span class="sxs-lookup"><span data-stu-id="e611c-209">If binding fails:</span></span>

* <span data-ttu-id="e611c-210">Die Hostebene protokolliert eine kritische Ausnahme.</span><span class="sxs-lookup"><span data-stu-id="e611c-210">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="e611c-211">Der DotNet-Prozess stürzt ab.</span><span class="sxs-lookup"><span data-stu-id="e611c-211">The dotnet process crashes.</span></span>
* <span data-ttu-id="e611c-212">Wenn der HTTP-Server [Kestrel](xref:fundamentals/servers/kestrel) heißt, wird keine Fehlerseite angezeigt.</span><span class="sxs-lookup"><span data-stu-id="e611c-212">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="e611c-213">Wenn sie auf [IIS](/iis) oder [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ausgeführt wird, wird ein *Prozessfehler 502.5* vom [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) zurückgegeben, wenn der Prozess nicht starten kann.</span><span class="sxs-lookup"><span data-stu-id="e611c-213">When running on [IIS](/iis) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="e611c-214">Weitere Informationen finden Sie unter <xref:host-and-deploy/iis/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="e611c-214">For more information, see <xref:host-and-deploy/iis/troubleshoot>.</span></span> <span data-ttu-id="e611c-215">Weitere Informationen zum Beheben von Startproblemen mit Azure App Service finden Sie unter <xref:host-and-deploy/azure-apps/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="e611c-215">For information on troubleshooting startup issues with Azure App Service, see <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="e611c-216">Datenbank-Fehlerseite</span><span class="sxs-lookup"><span data-stu-id="e611c-216">Database error page</span></span>

<span data-ttu-id="e611c-217">Die Middleware [Datenbank-Fehlerseite](<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*>) erfasst datenbankbezogene Ausnahmen, die mithilfe von Entity Framework-Migrationen aufgelöst werden können.</span><span class="sxs-lookup"><span data-stu-id="e611c-217">The [Database Error Page](<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*>) middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="e611c-218">Wenn diese Ausnahmen auftreten, wird eine HTML-Antwort mit Details zu möglichen Aktionen zum Beheben des Problems generiert.</span><span class="sxs-lookup"><span data-stu-id="e611c-218">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="e611c-219">Diese Seite darf nur in der Entwicklungsumgebung aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="e611c-219">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="e611c-220">Aktivieren Sie die Seite, indem Sie `Startup.Configure` Code hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="e611c-220">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

## <a name="exception-filters"></a><span data-ttu-id="e611c-221">Ausnahmefilter</span><span class="sxs-lookup"><span data-stu-id="e611c-221">Exception filters</span></span>

<span data-ttu-id="e611c-222">In MVC-Apps können Ausnahmefilter global oder auf einen Controller oder eine Aktion bezogen konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="e611c-222">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="e611c-223">In Razor Pages-Apps können sie global oder auf ein Seitenmodell bezogen konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="e611c-223">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="e611c-224">Diese Filter verarbeiten jede nicht behandelte Ausnahme, die während der Ausführung eine Controlleraktion oder eines anderen Filters auftritt.</span><span class="sxs-lookup"><span data-stu-id="e611c-224">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="e611c-225">Weitere Informationen finden Sie unter <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="e611c-225">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="e611c-226">Ausnahmefilter eignen sich zum Auffangen von Ausnahmen, die in MVC-Aktionen auftreten. Sie sind jedoch nicht so flexibel wie Middleware für die Ausnahmebehandlung.</span><span class="sxs-lookup"><span data-stu-id="e611c-226">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="e611c-227">Es wird empfohlen, die Middleware zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="e611c-227">We recommend using the middleware.</span></span> <span data-ttu-id="e611c-228">Verwenden Sie Filter nur dann, wenn Sie für die Fehlerbehandlung auf Grundlage einer ausgewählten MVC-Aktion eine andere Strategie wählen müssen.</span><span class="sxs-lookup"><span data-stu-id="e611c-228">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="e611c-229">Modellstatusfehler</span><span class="sxs-lookup"><span data-stu-id="e611c-229">Model state errors</span></span>

<span data-ttu-id="e611c-230">Informationen zum Behandeln von Modellstatusfehlern finden Sie unter [Modellbindung](xref:mvc/models/model-binding) und [Modellvalidierung](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="e611c-230">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e611c-231">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="e611c-231">Additional resources</span></span>

* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-apps/troubleshoot>

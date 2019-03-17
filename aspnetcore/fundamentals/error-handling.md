---
title: Fehlerbehandlung in ASP.NET Core
author: tdykstra
description: Erfahren Sie mehr über die Fehlerbehandlung in ASP.NET Core-Apps.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/05/2019
uid: fundamentals/error-handling
ms.openlocfilehash: d809c70b3fae6b2d21d5ec0871298d905b873d5d
ms.sourcegitcommit: 191d21c1e37b56f0df0187e795d9a56388bbf4c7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/08/2019
ms.locfileid: "57665362"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="6e16c-103">Fehlerbehandlung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6e16c-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="6e16c-104">Von [Tom Dykstra](https://github.com/tdykstra/), [Luke Latham](https://github.com/guardrex) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="6e16c-104">By [Tom Dykstra](https://github.com/tdykstra/), [Luke Latham](https://github.com/guardrex), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="6e16c-105">Dieser Artikel beschreibt grundsätzliche Vorgehensweisen zur Behandlung von Fehlern in ASP.NET Core-Anwendungen.</span><span class="sxs-lookup"><span data-stu-id="6e16c-105">This article covers common approaches to handling errors in ASP.NET Core apps.</span></span>

<span data-ttu-id="6e16c-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6e16c-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="6e16c-107">Seite mit Ausnahmen für Entwickler</span><span class="sxs-lookup"><span data-stu-id="6e16c-107">Developer Exception Page</span></span>

<span data-ttu-id="6e16c-108">Verwenden Sie die *Seite mit Ausnahmen für Entwickler*, um eine App so zu konfigurieren, dass sie eine Seite anzeigt, die ausführliche Informationen zu Anforderungsausnahmen enthält.</span><span class="sxs-lookup"><span data-stu-id="6e16c-108">To configure an app to display a page that shows detailed information about request exceptions, use the *Developer Exception Page*.</span></span> <span data-ttu-id="6e16c-109">Die Seite wird vom [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/)-Paket zur Verfügung gestellt. Dieses ist im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="6e16c-109">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="6e16c-110">Fügen Sie der `Startup.Configure`-Methode eine Zeile hinzu, wenn die App in der [Entwicklungsumgebung](xref:fundamentals/environments) ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="6e16c-110">Add a line to the `Startup.Configure` method when the app is running in the Development [environment](xref:fundamentals/environments):</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_UseDeveloperExceptionPage)]

<span data-ttu-id="6e16c-111">Fügen Sie den Aufruf von <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> vor Middleware ein, wo Sie Ausnahmen abfangen möchten.</span><span class="sxs-lookup"><span data-stu-id="6e16c-111">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> in front of any middleware where you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="6e16c-112">Aktivieren Sie die Seite mit Ausnahmen für Entwickler **nur dann, wenn die App in der Entwicklungsumgebung ausgeführt wird**.</span><span class="sxs-lookup"><span data-stu-id="6e16c-112">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="6e16c-113">Wenn die App in der Produktionsumgebung ausgeführt wird, sollten Sie keine detaillierten Ausnahmeinformationen öffentlich teilen.</span><span class="sxs-lookup"><span data-stu-id="6e16c-113">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="6e16c-114">Weitere Informationen zum Konfigurieren der Umgebungen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="6e16c-114">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="6e16c-115">Wenn Sie die Seite mit Ausnahmen für Entwickler anzeigen möchten, führen Sie die Beispiel-App mit der auf `Development` festgelegten Umgebung aus, und fügen Sie `?throw=true` zur Basis-URL der App hinzu.</span><span class="sxs-lookup"><span data-stu-id="6e16c-115">To see the Developer Exception Page, run the sample app with the environment set to `Development` and add `?throw=true` to the base URL of the app.</span></span> <span data-ttu-id="6e16c-116">Die Seite enthält die folgenden Informationen zu der Ausnahme und der Anforderung:</span><span class="sxs-lookup"><span data-stu-id="6e16c-116">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="6e16c-117">Stapelüberwachung</span><span class="sxs-lookup"><span data-stu-id="6e16c-117">Stack trace</span></span>
* <span data-ttu-id="6e16c-118">Abfragezeichenfolgeparameter (sofern vorhanden)</span><span class="sxs-lookup"><span data-stu-id="6e16c-118">Query string parameters (if any)</span></span>
* <span data-ttu-id="6e16c-119">Cookies (sofern vorhanden)</span><span class="sxs-lookup"><span data-stu-id="6e16c-119">Cookies (if any)</span></span>
* <span data-ttu-id="6e16c-120">Header</span><span class="sxs-lookup"><span data-stu-id="6e16c-120">Headers</span></span>

## <a name="configure-a-custom-exception-handling-page"></a><span data-ttu-id="6e16c-121">Konfigurieren einer benutzerdefinierten Seite zur Ausnahmebehandlung</span><span class="sxs-lookup"><span data-stu-id="6e16c-121">Configure a custom exception handling page</span></span>

<span data-ttu-id="6e16c-122">Wenn die App nicht in der Entwicklungsumgebung ausgeführt wird, rufen Sie die <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>-Erweiterungsmethode auf, um die Middleware zur Ausnahmebehandlung hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="6e16c-122">When the app isn't running in the Development environment, call the <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> extension method to add Exception Handling Middleware.</span></span> <span data-ttu-id="6e16c-123">Die Middleware:</span><span class="sxs-lookup"><span data-stu-id="6e16c-123">The middleware:</span></span>

* <span data-ttu-id="6e16c-124">Fängt Ausnahmen ab.</span><span class="sxs-lookup"><span data-stu-id="6e16c-124">Catches exceptions.</span></span>
* <span data-ttu-id="6e16c-125">Protokolliert Ausnahmen.</span><span class="sxs-lookup"><span data-stu-id="6e16c-125">Logs exceptions.</span></span>
* <span data-ttu-id="6e16c-126">Führt die Anforderung für die angegebene Seite oder den Controller in einer anderen Pipeline erneut aus.</span><span class="sxs-lookup"><span data-stu-id="6e16c-126">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="6e16c-127">Die Anforderung wird nicht erneut ausgeführt, wenn die Antwort gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="6e16c-127">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="6e16c-128">Im folgenden Beispiel aus der Beispiel-App fügt <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> die Middleware zur Ausnahmebehandlung in Nichtentwicklungsumgebungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="6e16c-128">In the following example from the sample app, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> adds the Exception Handling Middleware in non-Development environments.</span></span> <span data-ttu-id="6e16c-129">Die Erweiterungsmethode gibt am `/Error`-Endpunkt für erneut ausgeführte Anforderungen eine Fehlerseite oder einen Controller an, nachdem Ausnahmen abgefangen und protokolliert wurden:</span><span class="sxs-lookup"><span data-stu-id="6e16c-129">The extension method specifies an error page or controller at the `/Error` endpoint for re-executed requests after exceptions are caught and logged:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_UseExceptionHandler1)]

<span data-ttu-id="6e16c-130">Die Razor Pages-App-Vorlage stellt eine Fehlerseite (*.cshtml*) und <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel>-Klasse (`ErrorModel`) im Ordner „Pages“ bereit.</span><span class="sxs-lookup"><span data-stu-id="6e16c-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the Pages folder.</span></span>

<span data-ttu-id="6e16c-131">In einer MVC-App ist die folgende Fehlerhandlermethode in der MVC-App-Vorlage enthalten und wird im Home-Controller angezeigt:</span><span class="sxs-lookup"><span data-stu-id="6e16c-131">In an MVC app, the following error handler method is included in the MVC app template and appears in the Home controller:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="6e16c-132">Die Aktionsmethode zur Fehlerbehandlung wird nicht durch HTTP-Methodenattribute wie `HttpGet` ergänzt.</span><span class="sxs-lookup"><span data-stu-id="6e16c-132">Don't decorate the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="6e16c-133">Durch explizite Verben könnte bei einigen Anforderungen verhindert werden, dass diese Methode zum Einsatz kommt.</span><span class="sxs-lookup"><span data-stu-id="6e16c-133">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="6e16c-134">Lassen Sie den anonymen Zugriff auf die Methode zu, damit nicht authentifizierte Benutzer die Fehleransicht empfangen können.</span><span class="sxs-lookup"><span data-stu-id="6e16c-134">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

## <a name="access-the-exception"></a><span data-ttu-id="6e16c-135">Zugreifen auf die Ausnahme</span><span class="sxs-lookup"><span data-stu-id="6e16c-135">Access the exception</span></span>

<span data-ttu-id="6e16c-136">Verwenden Sie <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>, um auf die Ausnahme oder den Pfad der ursprünglichen Anforderung in einem Controller oder auf einer Seite zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="6e16c-136">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception or the original request path in a controller or page:</span></span>

* <span data-ttu-id="6e16c-137">Der Pfad steht über die <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature.Path>-Eigenschaft zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="6e16c-137">The path is available from the <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature.Path> property.</span></span>
* <span data-ttu-id="6e16c-138">Lesen Sie <xref:System.Exception?displayProperty=fullName> aus der geerbten Eigenschaft [IExceptionHandlerFeature.Error](xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature.Error).</span><span class="sxs-lookup"><span data-stu-id="6e16c-138">Read the <xref:System.Exception?displayProperty=fullName> from the inherited [IExceptionHandlerFeature.Error](xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature.Error) property.</span></span>

```csharp
// using Microsoft.AspNetCore.Diagnostics;

var exceptionHandlerPathFeature = 
    HttpContext.Features.Get<IExceptionHandlerPathFeature>();
var path = exceptionHandlerPathFeature?.Path;
var error = exceptionHandlerPathFeature?.Error;
```

> [!WARNING]
> <span data-ttu-id="6e16c-139">Stellen Sie **keine** vertraulichen Fehlerinformationen aus <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> oder <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> für Clients bereit.</span><span class="sxs-lookup"><span data-stu-id="6e16c-139">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="6e16c-140">Die Bereitstellung von Fehlern ist ein Sicherheitsrisiko.</span><span class="sxs-lookup"><span data-stu-id="6e16c-140">Serving errors is a security risk.</span></span>

## <a name="configure-custom-exception-handling-code"></a><span data-ttu-id="6e16c-141">Konfigurieren eines benutzerdefinierten Ausnahmeverarbeitungscodes</span><span class="sxs-lookup"><span data-stu-id="6e16c-141">Configure custom exception handling code</span></span>

<span data-ttu-id="6e16c-142">Eine Alternative zur Bereitstellung eines Endpunkts für Fehler bei einer [benutzerdefinierten Ausnahmebehandlungsseite](#configure-a-custom-exception-handling-page) besteht darin, einen Lambda-Ausdruck für <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> anzugeben.</span><span class="sxs-lookup"><span data-stu-id="6e16c-142">An alternative to serving an endpoint for errors with a [custom exception handling page](#configure-a-custom-exception-handling-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span></span> <span data-ttu-id="6e16c-143">Die Verwendung eines Lambda-Ausdrucks mit <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> ermöglicht den Zugriff auf den Fehler, bevor die Antwort zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="6e16c-143">Using a lambda with <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> allows access to the error before returning the response.</span></span>

<span data-ttu-id="6e16c-144">Die Beispiel-App veranschaulicht benutzerdefinierten Ausnahmebehandlungscode in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="6e16c-144">The sample app demonstrates custom exception handling code in `Startup.Configure`.</span></span> <span data-ttu-id="6e16c-145">Lösen Sie eine Ausnahme mit dem Link **Ausnahme auslösen** auf der Indexseite aus.</span><span class="sxs-lookup"><span data-stu-id="6e16c-145">Trigger an exception with the **Throw Exception** link on the Index page.</span></span> <span data-ttu-id="6e16c-146">Der folgende Lambda-Ausdruck wird ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="6e16c-146">The following lambda runs:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_UseExceptionHandler2)]

> [!WARNING]
> <span data-ttu-id="6e16c-147">Stellen Sie **keine** vertraulichen Fehlerinformationen aus <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> oder <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> für Clients bereit.</span><span class="sxs-lookup"><span data-stu-id="6e16c-147">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="6e16c-148">Die Bereitstellung von Fehlern ist ein Sicherheitsrisiko.</span><span class="sxs-lookup"><span data-stu-id="6e16c-148">Serving errors is a security risk.</span></span>

## <a name="configure-status-code-pages"></a><span data-ttu-id="6e16c-149">Konfigurieren von Statuscodeseiten</span><span class="sxs-lookup"><span data-stu-id="6e16c-149">Configure status code pages</span></span>

<span data-ttu-id="6e16c-150">Ihre ASP.NET Core-App stellt für HTTP-Statuscodes wie *404 – Nicht gefunden* standardmäßig keine Statuscodeseite zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="6e16c-150">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="6e16c-151">Die App gibt einen Statuscode und einen leeren Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="6e16c-151">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="6e16c-152">Verwenden Sie zum Bereitstellen von Statuscodeseiten Middleware für Statuscodeseiten.</span><span class="sxs-lookup"><span data-stu-id="6e16c-152">To provide status code pages, use Status Code Pages Middleware.</span></span>

<span data-ttu-id="6e16c-153">Die Middleware wird vom [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/)-Paket zur Verfügung gestellt. Dieses ist im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="6e16c-153">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="6e16c-154">Fügen Sie der `Startup.Configure`-Methode eine Zeile hinzu:</span><span class="sxs-lookup"><span data-stu-id="6e16c-154">Add a line to the `Startup.Configure` method:</span></span>

```csharp
app.UseStatusCodePages();
```

<span data-ttu-id="6e16c-155">Rufen Sie die <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*>-Methode vor Middleware für die Anforderungsverarbeitung auf (z.B. Middleware für statische Dateien und MVC-Middleware).</span><span class="sxs-lookup"><span data-stu-id="6e16c-155">Call the <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> method before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="6e16c-156">Standardmäßig fügt die Middleware „Status Code Pages“ Handler im Textformat für gängige Statuscodes wie *404 – nicht gefunden* hinzu:</span><span class="sxs-lookup"><span data-stu-id="6e16c-156">By default, Status Code Pages Middleware adds text-only handlers for common status codes, such as *404 - Not Found*:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="6e16c-157">Die Middleware unterstützt verschiedene Erweiterungsmethoden, mit denen Sie das Verhalten anpassen können.</span><span class="sxs-lookup"><span data-stu-id="6e16c-157">The middleware supports several extension methods that allow you to customize its behavior.</span></span>

<span data-ttu-id="6e16c-158">Eine Überladung von <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> verwendet einen Lambdaausdruck, den Sie verwenden können, um benutzerdefinierte Fehlerbehandlungs-Programmlogik zu verarbeiten und manuell die Antwort zu schreiben:</span><span class="sxs-lookup"><span data-stu-id="6e16c-158">An overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> takes a lambda expression, which you can use to process custom error-handling logic and manually write the response:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="6e16c-159">Eine Überladung von <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> verwendet einen Inhaltstyp und eine Formatzeichenfolge, womit Sie Inhaltstyp und Antworttext anpassen können:</span><span class="sxs-lookup"><span data-stu-id="6e16c-159">An overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> takes a content type and format string, which you can use to customize the content type and response text:</span></span>

```csharp
app.UseStatusCodePages("text/plain", "Status code page, status code: {0}");
```

### <a name="redirect-and-re-execute-extension-methods"></a><span data-ttu-id="6e16c-160">Erweiterungsmethoden zum Umleiten und erneuten Ausführen</span><span class="sxs-lookup"><span data-stu-id="6e16c-160">Redirect and re-execute extension methods</span></span>

<span data-ttu-id="6e16c-161"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>:</span><span class="sxs-lookup"><span data-stu-id="6e16c-161"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>:</span></span>

* <span data-ttu-id="6e16c-162">Sendet den Statuscode *302 Found* (Gefunden) an den Client.</span><span class="sxs-lookup"><span data-stu-id="6e16c-162">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="6e16c-163">Der Client wird an den in der URL-Vorlage angegebenen Standort umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="6e16c-163">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="6e16c-164"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> wird häufig verwendet, wenn die App Folgendes tun sollte:</span><span class="sxs-lookup"><span data-stu-id="6e16c-164"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> is commonly used when the app:</span></span>

* <span data-ttu-id="6e16c-165">Den Client an einen anderen Endpunkt umleiten, in der Regel in Fällen, in denen eine andere App den Fehler verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="6e16c-165">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="6e16c-166">Für Web-Apps gibt die Adressleiste des Browsers des Clients den umgeleiteten Endpunkt wieder.</span><span class="sxs-lookup"><span data-stu-id="6e16c-166">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="6e16c-167">Den ursprünglichen Statuscode mit der anfänglichen Umleitungsantwort nicht beibehalten und zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="6e16c-167">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

<span data-ttu-id="6e16c-168"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>:</span><span class="sxs-lookup"><span data-stu-id="6e16c-168"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>:</span></span>

* <span data-ttu-id="6e16c-169">Gibt den ursprünglichen Statuscode an den Client zurück.</span><span class="sxs-lookup"><span data-stu-id="6e16c-169">Returns the original status code to the client.</span></span>
* <span data-ttu-id="6e16c-170">Generiert den Antworttext durch die erneute Ausführung der Anforderungspipeline mithilfe eines alternativen Pfads.</span><span class="sxs-lookup"><span data-stu-id="6e16c-170">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

```csharp
app.UseStatusCodePagesWithReExecute("/Error/{0}");
```

<span data-ttu-id="6e16c-171"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> wird häufig verwendet, wenn die App Folgendes tun sollte:</span><span class="sxs-lookup"><span data-stu-id="6e16c-171"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> is commonly used when the app should:</span></span>

* <span data-ttu-id="6e16c-172">Die Anforderung ohne Umleitung an einen anderen Endpunkt verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="6e16c-172">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="6e16c-173">Für Web-Apps gibt die Adressleiste des Browsers des Clients den ursprünglich angeforderten Endpunkt wieder.</span><span class="sxs-lookup"><span data-stu-id="6e16c-173">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="6e16c-174">Den ursprünglichen Statuscode mit der Antwort beibehalten und zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="6e16c-174">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="6e16c-175">Vorlagen können einen `{0}`-Platzhalter für den Statuscode enthalten.</span><span class="sxs-lookup"><span data-stu-id="6e16c-175">Templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="6e16c-176">Der Vorlagenpfad muss mit einem Schrägstrich (`/`) beginnen.</span><span class="sxs-lookup"><span data-stu-id="6e16c-176">The template must start with a forward slash (`/`).</span></span> <span data-ttu-id="6e16c-177">Wenn Sie einen Platzhalter verwenden, vergewissern Sie sich, dass der Endpunkt (Seite oder Controller) das Pfadsegment verarbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="6e16c-177">When using a placeholder, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="6e16c-178">Eine Razor Page für Fehler sollte z.B. den Wert des optionalen Pfadsegments mit der `@page`-Anweisung akzeptieren:</span><span class="sxs-lookup"><span data-stu-id="6e16c-178">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="6e16c-179">Statuscodeseiten können für bestimmte Anforderungen in der Handlermethode einer Razor-Seite oder in einem MVC-Controller deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="6e16c-179">Status code pages can be disabled for specific requests in a Razor Pages handler method or in an MVC controller.</span></span> <span data-ttu-id="6e16c-180">Versuchen Sie, das <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> aus der Sammlung [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features) der Anforderung abzurufen und das Feature zu deaktivieren (falls es verfügbar ist), um Statuscodeseiten zu deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="6e16c-180">To disable status code pages, attempt to retrieve the <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> from the request's [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features) collection and disable the feature if it's available:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

<span data-ttu-id="6e16c-181">Um eine <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*>-Überladung zu verwenden, die auf einen Endpunkt in der App verweist, müssen Sie eine MVC-Ansicht oder Razor Page für den Endpunkt erstellen.</span><span class="sxs-lookup"><span data-stu-id="6e16c-181">To use a <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> overload that points to an endpoint within the app, create an MVC view or Razor Page for the endpoint.</span></span> <span data-ttu-id="6e16c-182">Eine Razor Pages-App-Vorlage erstellt beispielsweise die folgende Seite und Seitenmodellklasse:</span><span class="sxs-lookup"><span data-stu-id="6e16c-182">For example, the Razor Pages app template produces the following page and page model class:</span></span>

<span data-ttu-id="6e16c-183">*Error.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6e16c-183">*Error.cshtml*:</span></span>

::: moniker range=">= aspnetcore-2.2"

```cshtml
@page
@model ErrorModel
@{
    ViewData["Title"] = "Error";
}

<h1 class="text-danger">Error.</h1>
<h2 class="text-danger">An error occurred while processing your request.</h2>

@if (Model.ShowRequestId)
{
    <p>
        <strong>Request ID:</strong> <code>@Model.RequestId</code>
    </p>
}

<h3>Development Mode</h3>
<p>
    Swapping to the <strong>Development</strong> environment displays 
    detailed information about the error that occurred.
</p>
<p>
    <strong>The Development environment shouldn't be enabled for deployed 
    applications.</strong> It can result in displaying sensitive information 
    from exceptions to end users. For local debugging, enable the 
    <strong>Development</strong> environment by setting the 
    <strong>ASPNETCORE_ENVIRONMENT</strong> environment variable to 
    <strong>Development</strong> and restarting the app.
</p>
```

<span data-ttu-id="6e16c-184">*Error.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="6e16c-184">*Error.cshtml.cs*:</span></span>

```csharp
[ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, NoStore = true)]
public class ErrorModel : PageModel
{
    public string RequestId { get; set; }

    public bool ShowRequestId => !string.IsNullOrEmpty(RequestId);

    public void OnGet()
    {
        RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier;
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```cshtml
@page
@model ErrorModel
@{
    ViewData["Title"] = "Error";
}

<h1 class="text-danger">Error.</h1>
<h2 class="text-danger">An error occurred while processing your request.</h2>

@if (Model.ShowRequestId)
{
    <p>
        <strong>Request ID:</strong> <code>@Model.RequestId</code>
    </p>
}

<h3>Development Mode</h3>
<p>
    Swapping to <strong>Development</strong> environment will display more detailed 
    information about the error that occurred.
</p>
<p>
    <strong>Development environment should not be enabled in deployed applications
    </strong>, as it can result in sensitive information from exceptions being 
    displayed to end users. For local debugging, development environment can be 
    enabled by setting the <strong>ASPNETCORE_ENVIRONMENT</strong> environment 
    variable to <strong>Development</strong>, and restarting the application.
</p>
```

<span data-ttu-id="6e16c-185">*Error.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="6e16c-185">*Error.cshtml.cs*:</span></span>

```csharp
public class ErrorModel : PageModel
{
    public string RequestId { get; set; }

    public bool ShowRequestId => !string.IsNullOrEmpty(RequestId);

    [ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, 
        NoStore = true)]
    public void OnGet()
    {
        RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier;
    }
}
```

::: moniker-end

## <a name="exception-handling-code"></a><span data-ttu-id="6e16c-186">Ausnahmebehandlungscode</span><span class="sxs-lookup"><span data-stu-id="6e16c-186">Exception-handling code</span></span>

<span data-ttu-id="6e16c-187">Code auf Seiten zur Ausnahmebehandlung kann Ausnahmen auslösen.</span><span class="sxs-lookup"><span data-stu-id="6e16c-187">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="6e16c-188">Es ist häufig empfehlenswert, wenn Produktionsfehlerseiten nur statische Inhalte aufweisen.</span><span class="sxs-lookup"><span data-stu-id="6e16c-188">It's often a good idea for production error pages to consist of purely static content.</span></span>

<span data-ttu-id="6e16c-189">Achten Sie nach dem Senden der Header für eine Antwort auch auf Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6e16c-189">Also, be aware that once the headers for a response are sent:</span></span>

* <span data-ttu-id="6e16c-190">Die App kann den Statuscode der Antwort nicht ändern.</span><span class="sxs-lookup"><span data-stu-id="6e16c-190">The app can't change the response's status code.</span></span>
* <span data-ttu-id="6e16c-191">Weder Ausnahmeseiten noch Handler können ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="6e16c-191">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="6e16c-192">Die Antwort muss abgeschlossen oder die Verbindung unterbrochen werden.</span><span class="sxs-lookup"><span data-stu-id="6e16c-192">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="6e16c-193">Sichere Ausnahmebehandlung</span><span class="sxs-lookup"><span data-stu-id="6e16c-193">Server exception handling</span></span>

<span data-ttu-id="6e16c-194">Neben der Ausnahmebehandlungslogik in Ihrer App kann die [Serverimplementierung](xref:fundamentals/servers/index) einige Ausnahmebehandlungen durchführen.</span><span class="sxs-lookup"><span data-stu-id="6e16c-194">In addition to the exception handling logic in your app, the [server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="6e16c-195">Wenn der Server eine Ausnahme erfasst, bevor die Antwortheader gesendet werden, sendet der Server die Antwort *500 – Interner Serverfehler* ohne Antworttext.</span><span class="sxs-lookup"><span data-stu-id="6e16c-195">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="6e16c-196">Wenn der Server eine Ausnahme auffängt, nachdem die Antwortheader gesendet wurden, schließt der Server die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="6e16c-196">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="6e16c-197">Anforderungen, die nicht von Ihrer App verarbeitet werden, werden vom Server verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="6e16c-197">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="6e16c-198">Jede Ausnahme, die bei der Anforderungsverarbeitung durch den Server auftritt, wird durch die Ausnahmebehandlung des Servers verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="6e16c-198">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="6e16c-199">Die benutzerdefinierten Fehlerseiten der App, Middleware zur Fehlerbehandlung und Filter haben keine Auswirkungen auf dieses Verhalten.</span><span class="sxs-lookup"><span data-stu-id="6e16c-199">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="6e16c-200">Fehlerbehandlung während des Starts</span><span class="sxs-lookup"><span data-stu-id="6e16c-200">Startup exception handling</span></span>

<span data-ttu-id="6e16c-201">Nur auf Hostebene können Ausnahmen behandelt werden, die während des Starts einer App auftreten.</span><span class="sxs-lookup"><span data-stu-id="6e16c-201">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="6e16c-202">Sie können mithilfe des [Webhosts](xref:fundamentals/host/web-host) [ konfigurieren, wie der Host während des Starts auf Fehler reagiert](xref:fundamentals/host/web-host#detailed-errors), indem Sie die Schlüssel `captureStartupErrors` und `detailedErrors` verwenden.</span><span class="sxs-lookup"><span data-stu-id="6e16c-202">Using [Web Host](xref:fundamentals/host/web-host), you can [configure how the host behaves in response to errors during startup](xref:fundamentals/host/web-host#detailed-errors) with the `captureStartupErrors` and `detailedErrors` keys.</span></span>

<span data-ttu-id="6e16c-203">Das Hosting kann nur dann eine Fehlerseite für einen erfassten Fehler beim Start anzeigen, wenn der Fehler nach der Bindung der Hostadresse bzw. des Ports auftritt.</span><span class="sxs-lookup"><span data-stu-id="6e16c-203">Hosting can only show an error page for a captured startup error if the error occurs after host address/port binding.</span></span> <span data-ttu-id="6e16c-204">Wenn bei einer Bindung aus beliebigem Grund ein Fehler auftritt, geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6e16c-204">If any binding fails for any reason:</span></span>

* <span data-ttu-id="6e16c-205">Die Hostebene protokolliert eine kritische Ausnahme.</span><span class="sxs-lookup"><span data-stu-id="6e16c-205">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="6e16c-206">Der DotNet-Prozess stürzt ab.</span><span class="sxs-lookup"><span data-stu-id="6e16c-206">The dotnet process crashes.</span></span>
* <span data-ttu-id="6e16c-207">Wenn die App auf dem [Kestrel](xref:fundamentals/servers/kestrel)-Server ausgeführt wird, wird keine Fehlerseite angezeigt.</span><span class="sxs-lookup"><span data-stu-id="6e16c-207">No error page is displayed when the app is running on the [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

<span data-ttu-id="6e16c-208">Wenn sie auf [IIS](/iis) oder [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ausgeführt wird, wird ein *Prozessfehler 502.5* vom [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) zurückgegeben, wenn der Prozess nicht starten kann.</span><span class="sxs-lookup"><span data-stu-id="6e16c-208">When running on [IIS](/iis) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="6e16c-209">Weitere Informationen finden Sie unter <xref:host-and-deploy/iis/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="6e16c-209">For more information, see <xref:host-and-deploy/iis/troubleshoot>.</span></span> <span data-ttu-id="6e16c-210">Weitere Informationen zum Beheben von Startproblemen mit Azure App Service finden Sie unter <xref:host-and-deploy/azure-apps/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="6e16c-210">For information on troubleshooting startup issues with Azure App Service, see <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>

## <a name="aspnet-core-mvc-error-handling"></a><span data-ttu-id="6e16c-211">ASP.NET Core MVC-Fehlerbehandlung</span><span class="sxs-lookup"><span data-stu-id="6e16c-211">ASP.NET Core MVC error handling</span></span>

<span data-ttu-id="6e16c-212">[MVC](xref:mvc/overview)-Apps verfügen über einige zusätzliche Optionen für die Fehlerbehandlung, z.B. das Konfigurieren von Ausnahmefiltern und das Durchführen einer Modellvalidierung.</span><span class="sxs-lookup"><span data-stu-id="6e16c-212">[MVC](xref:mvc/overview) apps have some additional options for handling errors, such as configuring exception filters and performing model validation.</span></span>

### <a name="exception-filters"></a><span data-ttu-id="6e16c-213">Ausnahmefilter</span><span class="sxs-lookup"><span data-stu-id="6e16c-213">Exception filters</span></span>

<span data-ttu-id="6e16c-214">Ausnahmefilter können in einer MVC-App global oder auf der Grundlage eines Controllers oder einer Aktion konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="6e16c-214">Exception filters can be configured globally or on a per-controller or per-action basis in an MVC app.</span></span> <span data-ttu-id="6e16c-215">Diese Filter verarbeiten jede nicht behandelte Ausnahme, die während der Ausführung eine Controlleraktion oder eines anderen Filters auftritt.</span><span class="sxs-lookup"><span data-stu-id="6e16c-215">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="6e16c-216">Diese Dateien werden andernfalls nicht aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="6e16c-216">These filters aren't called otherwise.</span></span> <span data-ttu-id="6e16c-217">Weitere Informationen finden Sie unter <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="6e16c-217">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="6e16c-218">Ausnahmefilter eignen sich zum Auffangen von Ausnahmen, die in MVC-Aktionen auftreten. Sie sind jedoch nicht so flexibel wie Middleware für die Ausnahmebehandlung.</span><span class="sxs-lookup"><span data-stu-id="6e16c-218">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="6e16c-219">Es wird empfohlen, die Middleware zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="6e16c-219">We recommend using the middleware.</span></span> <span data-ttu-id="6e16c-220">Verwenden Sie Filter nur dann, wenn Sie für die Fehlerbehandlung auf Grundlage einer ausgewählten MVC-Aktion eine *andere* Strategie anwenden müssen.</span><span class="sxs-lookup"><span data-stu-id="6e16c-220">Use filters only where you need to perform error handling *differently* based on which MVC action is chosen.</span></span>

### <a name="handle-model-state-errors"></a><span data-ttu-id="6e16c-221">Behandeln von Modellstatusfehlern</span><span class="sxs-lookup"><span data-stu-id="6e16c-221">Handle model state errors</span></span>

<span data-ttu-id="6e16c-222">Die [Modellvalidierung](xref:mvc/models/validation) tritt vor jeder ausgelösten Controlleraktion auf. Die Aktionsmethode ist dafür verantwortlich, [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) zu untersuchen und angemessen zu reagieren.</span><span class="sxs-lookup"><span data-stu-id="6e16c-222">[Model validation](xref:mvc/models/validation) occurs prior to invoking each controller action, and it's the action method's responsibility to inspect [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) and react appropriately.</span></span>

<span data-ttu-id="6e16c-223">Einige Apps wählen eine Standardkonvention aus, um [Modellvalidierungsfehler](xref:mvc/models/validation) zu behandeln. Dann kann ein [Filter](xref:mvc/controllers/filters) hilfreich sein, um eine solche Richtlinie zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="6e16c-223">Some apps choose to follow a standard convention for dealing with [model validation](xref:mvc/models/validation) errors, in which case a [filter](xref:mvc/controllers/filters) may be an appropriate place to implement such a policy.</span></span> <span data-ttu-id="6e16c-224">Sie sollten testen, wie sich Ihre Aktionen mit ungültigen Modellstatus verhalten.</span><span class="sxs-lookup"><span data-stu-id="6e16c-224">You should test how your actions behave with invalid model states.</span></span> <span data-ttu-id="6e16c-225">Weitere Informationen finden Sie unter <xref:mvc/controllers/testing>.</span><span class="sxs-lookup"><span data-stu-id="6e16c-225">For more information, see <xref:mvc/controllers/testing>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6e16c-226">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="6e16c-226">Additional resources</span></span>

* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-apps/troubleshoot>

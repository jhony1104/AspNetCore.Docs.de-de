---
title: Fehlerbehandlung in ASP.NET Core
author: tdykstra
description: Erfahren Sie mehr über die Fehlerbehandlung in ASP.NET Core-Apps.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/01/2019
uid: fundamentals/error-handling
ms.openlocfilehash: a2ae2cb25c8cc5048b189b4035abbfc32a29aaff
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57345493"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="7a9c4-103">Fehlerbehandlung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7a9c4-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="7a9c4-104">Von [Tom Dykstra](https://github.com/tdykstra/), [Luke Latham](https://github.com/guardrex) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="7a9c4-104">By [Tom Dykstra](https://github.com/tdykstra/), [Luke Latham](https://github.com/guardrex), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="7a9c4-105">Dieser Artikel beschreibt grundsätzliche Vorgehensweisen zur Behandlung von Fehlern in ASP.NET Core-Anwendungen.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-105">This article covers common approaches to handling errors in ASP.NET Core apps.</span></span>

<span data-ttu-id="7a9c4-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7a9c4-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="7a9c4-107">Seite mit Ausnahmen für Entwickler</span><span class="sxs-lookup"><span data-stu-id="7a9c4-107">Developer Exception Page</span></span>

<span data-ttu-id="7a9c4-108">Verwenden Sie die *Seite mit Ausnahmen für Entwickler*, um eine App so zu konfigurieren, dass sie eine Seite anzeigt, die ausführliche Informationen zu Ausnahmen enthält.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-108">To configure an app to display a page that shows detailed information about exceptions, use the *Developer Exception Page*.</span></span> <span data-ttu-id="7a9c4-109">Die Seite wird vom [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/)-Paket zur Verfügung gestellt. Dieses ist im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-109">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="7a9c4-110">Fügen Sie der `Startup.Configure`-Methode eine Zeile hinzu:</span><span class="sxs-lookup"><span data-stu-id="7a9c4-110">Add a line to the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevExceptionPage&highlight=5)]

<span data-ttu-id="7a9c4-111">Fügen Sie den Aufruf von <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> vor Middleware ein, wo Sie Ausnahmen abfangen möchten.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-111">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> in front of any middleware where you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="7a9c4-112">Aktivieren Sie die Seite mit Ausnahmen für Entwickler **nur dann, wenn die App in der Entwicklungsumgebung ausgeführt wird**.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-112">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="7a9c4-113">Wenn die App in der Produktionsumgebung ausgeführt wird, sollten Sie keine detaillierten Ausnahmeinformationen öffentlich teilen.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-113">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="7a9c4-114">Weitere Informationen zum Konfigurieren der Umgebungen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-114">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="7a9c4-115">Wenn Sie die Seite mit Ausnahmen für Entwickler anzeigen möchten, führen Sie die Beispiel-App mit der auf `Development` festgelegten Umgebung aus, und fügen Sie `?throw=true` zur Basis-URL der App hinzu.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-115">To see the Developer Exception Page, run the sample app with the environment set to `Development` and add `?throw=true` to the base URL of the app.</span></span> <span data-ttu-id="7a9c4-116">Die Seite enthält die folgenden Informationen zu der Ausnahme und der Anforderung:</span><span class="sxs-lookup"><span data-stu-id="7a9c4-116">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="7a9c4-117">Stapelüberwachung</span><span class="sxs-lookup"><span data-stu-id="7a9c4-117">Stack trace</span></span>
* <span data-ttu-id="7a9c4-118">Abfragezeichenfolgeparameter (sofern vorhanden)</span><span class="sxs-lookup"><span data-stu-id="7a9c4-118">Query string parameters (if any)</span></span>
* <span data-ttu-id="7a9c4-119">Cookies (sofern vorhanden)</span><span class="sxs-lookup"><span data-stu-id="7a9c4-119">Cookies (if any)</span></span>
* <span data-ttu-id="7a9c4-120">Header</span><span class="sxs-lookup"><span data-stu-id="7a9c4-120">Headers</span></span>

## <a name="configure-a-custom-exception-handling-page"></a><span data-ttu-id="7a9c4-121">Konfigurieren einer benutzerdefinierten Seite zur Ausnahmebehandlung</span><span class="sxs-lookup"><span data-stu-id="7a9c4-121">Configure a custom exception handling page</span></span>

<span data-ttu-id="7a9c4-122">Wenn die App nicht in der Entwicklungsumgebung ausgeführt wird, rufen Sie die <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>-Erweiterungsmethode auf, um die Middleware zur Ausnahmebehandlung hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-122">When the app isn't running in the Development environment, call the <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> extension method to add Exception Handling Middleware.</span></span> <span data-ttu-id="7a9c4-123">Die Middleware:</span><span class="sxs-lookup"><span data-stu-id="7a9c4-123">The middleware:</span></span>

* <span data-ttu-id="7a9c4-124">Fängt Ausnahmen ab.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-124">Catches exceptions.</span></span>
* <span data-ttu-id="7a9c4-125">Protokolliert Ausnahmen.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-125">Logs exceptions.</span></span>
* <span data-ttu-id="7a9c4-126">Führt die Anforderung für die angegebene Seite oder den Controller in einer anderen Pipeline erneut aus.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-126">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="7a9c4-127">Die Anforderung wird nicht erneut ausgeführt, wenn die Antwort gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-127">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="7a9c4-128">Im folgenden Beispiel aus der Beispiel-App fügt <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> die Middleware zur Ausnahmebehandlung in Nichtentwicklungsumgebungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-128">In the following example from the sample app, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> adds the Exception Handling Middleware in non-Development environments.</span></span> <span data-ttu-id="7a9c4-129">Die Erweiterungsmethode gibt am `/Error`-Endpunkt für erneut ausgeführte Anforderungen eine Fehlerseite oder einen Controller an, nachdem Ausnahmen abgefangen und protokolliert wurden:</span><span class="sxs-lookup"><span data-stu-id="7a9c4-129">The extension method specifies an error page or controller at the `/Error` endpoint for re-executed requests after exceptions are caught and logged:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevExceptionPage&highlight=9)]

<span data-ttu-id="7a9c4-130">Die Razor Pages-App-Vorlage stellt eine Fehlerseite (*.cshtml*) und <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel>-Klasse (`ErrorModel`) im Ordner „Pages“ bereit.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the Pages folder.</span></span>

<span data-ttu-id="7a9c4-131">In einer MVC-App ist die folgende Fehlerhandlermethode in der MVC-App-Vorlage enthalten und wird im Home-Controller angezeigt:</span><span class="sxs-lookup"><span data-stu-id="7a9c4-131">In an MVC app, the following error handler method is included in the MVC app template and appears in the Home controller:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="7a9c4-132">Die Aktionsmethode zur Fehlerbehandlung wird nicht durch HTTP-Methodenattribute wie `HttpGet` ergänzt.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-132">Don't decorate the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="7a9c4-133">Durch explizite Verben könnte bei einigen Anforderungen verhindert werden, dass diese Methode zum Einsatz kommt.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-133">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="7a9c4-134">Lassen Sie den anonymen Zugriff auf die Methode zu, damit nicht authentifizierte Benutzer die Fehleransicht empfangen können.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-134">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

## <a name="configure-status-code-pages"></a><span data-ttu-id="7a9c4-135">Konfigurieren von Statuscodeseiten</span><span class="sxs-lookup"><span data-stu-id="7a9c4-135">Configure status code pages</span></span>

<span data-ttu-id="7a9c4-136">Ihre ASP.NET Core-App stellt für HTTP-Statuscodes wie *404 – Nicht gefunden* standardmäßig keine Statuscodeseite zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-136">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="7a9c4-137">Die App gibt einen Statuscode und einen leeren Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-137">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="7a9c4-138">Verwenden Sie zum Bereitstellen von Statuscodeseiten Middleware für Statuscodeseiten.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-138">To provide status code pages, use Status Code Pages Middleware.</span></span>

<span data-ttu-id="7a9c4-139">Die Middleware wird vom [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/)-Paket zur Verfügung gestellt. Dieses ist im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-139">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="7a9c4-140">Fügen Sie der `Startup.Configure`-Methode eine Zeile hinzu:</span><span class="sxs-lookup"><span data-stu-id="7a9c4-140">Add a line to the `Startup.Configure` method:</span></span>

```csharp
app.UseStatusCodePages();
```

<span data-ttu-id="7a9c4-141">Rufen Sie die <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*>-Methode vor Middleware für die Anforderungsverarbeitung auf (z.B. Middleware für statische Dateien und MVC-Middleware).</span><span class="sxs-lookup"><span data-stu-id="7a9c4-141">Call the <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> method before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="7a9c4-142">Standardmäßig fügt die Middleware „Status Code Pages“ Handler im Textformat für gängige Statuscodes wie *404 – nicht gefunden* hinzu:</span><span class="sxs-lookup"><span data-stu-id="7a9c4-142">By default, Status Code Pages Middleware adds text-only handlers for common status codes, such as *404 - Not Found*:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="7a9c4-143">Die Middleware unterstützt verschiedene Erweiterungsmethoden, mit denen Sie das Verhalten anpassen können.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-143">The middleware supports several extension methods that allow you to customize its behavior.</span></span>

<span data-ttu-id="7a9c4-144">Eine Überladung von <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> verwendet einen Lambdaausdruck, den Sie verwenden können, um benutzerdefinierte Fehlerbehandlungs-Programmlogik zu verarbeiten und manuell die Antwort zu schreiben:</span><span class="sxs-lookup"><span data-stu-id="7a9c4-144">An overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> takes a lambda expression, which you can use to process custom error-handling logic and manually write the response:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="7a9c4-145">Eine Überladung von <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> verwendet einen Inhaltstyp und eine Formatzeichenfolge, womit Sie Inhaltstyp und Antworttext anpassen können:</span><span class="sxs-lookup"><span data-stu-id="7a9c4-145">An overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> takes a content type and format string, which you can use to customize the content type and response text:</span></span>

```csharp
app.UseStatusCodePages("text/plain", "Status code page, status code: {0}");
```

### <a name="redirect-and-re-execute-extension-methods"></a><span data-ttu-id="7a9c4-146">Erweiterungsmethoden zum Umleiten und erneuten Ausführen</span><span class="sxs-lookup"><span data-stu-id="7a9c4-146">Redirect and re-execute extension methods</span></span>

<span data-ttu-id="7a9c4-147"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>:</span><span class="sxs-lookup"><span data-stu-id="7a9c4-147"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>:</span></span>

* <span data-ttu-id="7a9c4-148">Sendet den Statuscode *302 Found* (Gefunden) an den Client.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-148">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="7a9c4-149">Der Client wird an den in der URL-Vorlage angegebenen Standort umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-149">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="7a9c4-150"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> wird häufig verwendet, wenn die App Folgendes tun sollte:</span><span class="sxs-lookup"><span data-stu-id="7a9c4-150"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> is commonly used when the app:</span></span>

* <span data-ttu-id="7a9c4-151">Den Client an einen anderen Endpunkt umleiten, in der Regel in Fällen, in denen eine andere App den Fehler verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-151">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="7a9c4-152">Für Web-Apps gibt die Adressleiste des Browsers des Clients den umgeleiteten Endpunkt wieder.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-152">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="7a9c4-153">Den ursprünglichen Statuscode mit der anfänglichen Umleitungsantwort nicht beibehalten und zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-153">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

<span data-ttu-id="7a9c4-154"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>:</span><span class="sxs-lookup"><span data-stu-id="7a9c4-154"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>:</span></span>

* <span data-ttu-id="7a9c4-155">Gibt den ursprünglichen Statuscode an den Client zurück.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-155">Returns the original status code to the client.</span></span>
* <span data-ttu-id="7a9c4-156">Generiert den Antworttext durch die erneute Ausführung der Anforderungspipeline mithilfe eines alternativen Pfads.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-156">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

```csharp
app.UseStatusCodePagesWithReExecute("/Error/{0}");
```

<span data-ttu-id="7a9c4-157"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> wird häufig verwendet, wenn die App Folgendes tun sollte:</span><span class="sxs-lookup"><span data-stu-id="7a9c4-157"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> is commonly used when the app should:</span></span>

* <span data-ttu-id="7a9c4-158">Die Anforderung ohne Umleitung an einen anderen Endpunkt verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-158">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="7a9c4-159">Für Web-Apps gibt die Adressleiste des Browsers des Clients den ursprünglich angeforderten Endpunkt wieder.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-159">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="7a9c4-160">Den ursprünglichen Statuscode mit der Antwort beibehalten und zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-160">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="7a9c4-161">Vorlagen können einen `{0}`-Platzhalter für den Statuscode enthalten.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-161">Templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="7a9c4-162">Der Vorlagenpfad muss mit einem Schrägstrich (`/`) beginnen.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-162">The template must start with a forward slash (`/`).</span></span> <span data-ttu-id="7a9c4-163">Wenn Sie einen Platzhalter verwenden, vergewissern Sie sich, dass der Endpunkt (Seite oder Controller) das Pfadsegment verarbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-163">When using a placeholder, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="7a9c4-164">Eine Razor Page für Fehler sollte z.B. den Wert des optionalen Pfadsegments mit der `@page`-Anweisung akzeptieren:</span><span class="sxs-lookup"><span data-stu-id="7a9c4-164">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="7a9c4-165">Statuscodeseiten können für bestimmte Anforderungen in der Handlermethode einer Razor-Seite oder in einem MVC-Controller deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-165">Status code pages can be disabled for specific requests in a Razor Pages handler method or in an MVC controller.</span></span> <span data-ttu-id="7a9c4-166">Versuchen Sie, das <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> aus der Sammlung [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features) der Anforderung abzurufen und das Feature zu deaktivieren (falls es verfügbar ist), um Statuscodeseiten zu deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="7a9c4-166">To disable status code pages, attempt to retrieve the <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> from the request's [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features) collection and disable the feature if it's available:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

<span data-ttu-id="7a9c4-167">Um eine <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*>-Überladung zu verwenden, die auf einen Endpunkt in der App verweist, müssen Sie eine MVC-Ansicht oder Razor Page für den Endpunkt erstellen.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-167">To use a <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> overload that points to an endpoint within the app, create an MVC view or Razor Page for the endpoint.</span></span> <span data-ttu-id="7a9c4-168">Eine Razor Pages-App-Vorlage erstellt beispielsweise die folgende Seite und Seitenmodellklasse:</span><span class="sxs-lookup"><span data-stu-id="7a9c4-168">For example, the Razor Pages app template produces the following page and page model class:</span></span>

<span data-ttu-id="7a9c4-169">*Error.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="7a9c4-169">*Error.cshtml*:</span></span>

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

<span data-ttu-id="7a9c4-170">*Error.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="7a9c4-170">*Error.cshtml.cs*:</span></span>

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

<span data-ttu-id="7a9c4-171">*Error.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="7a9c4-171">*Error.cshtml.cs*:</span></span>

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

## <a name="exception-handling-code"></a><span data-ttu-id="7a9c4-172">Ausnahmebehandlungscode</span><span class="sxs-lookup"><span data-stu-id="7a9c4-172">Exception-handling code</span></span>

<span data-ttu-id="7a9c4-173">Code auf Seiten zur Ausnahmebehandlung kann Ausnahmen auslösen.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-173">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="7a9c4-174">Es ist häufig empfehlenswert, wenn Produktionsfehlerseiten nur statische Inhalte aufweisen.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-174">It's often a good idea for production error pages to consist of purely static content.</span></span>

<span data-ttu-id="7a9c4-175">Achten Sie nach dem Senden der Header für eine Antwort auch auf Folgendes:</span><span class="sxs-lookup"><span data-stu-id="7a9c4-175">Also, be aware that once the headers for a response are sent:</span></span>

* <span data-ttu-id="7a9c4-176">Die App kann den Statuscode der Antwort nicht ändern.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-176">The app can't change the response's status code.</span></span>
* <span data-ttu-id="7a9c4-177">Weder Ausnahmeseiten noch Handler können ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-177">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="7a9c4-178">Die Antwort muss abgeschlossen oder die Verbindung unterbrochen werden.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-178">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="7a9c4-179">Sichere Ausnahmebehandlung</span><span class="sxs-lookup"><span data-stu-id="7a9c4-179">Server exception handling</span></span>

<span data-ttu-id="7a9c4-180">Neben der Ausnahmebehandlungslogik in Ihrer App kann die [Serverimplementierung](xref:fundamentals/servers/index) einige Ausnahmebehandlungen durchführen.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-180">In addition to the exception handling logic in your app, the [server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="7a9c4-181">Wenn der Server eine Ausnahme erfasst, bevor die Antwortheader gesendet werden, sendet der Server die Antwort *500 – Interner Serverfehler* ohne Antworttext.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-181">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="7a9c4-182">Wenn der Server eine Ausnahme auffängt, nachdem die Antwortheader gesendet wurden, schließt der Server die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-182">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="7a9c4-183">Anforderungen, die nicht von Ihrer App verarbeitet werden, werden vom Server verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-183">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="7a9c4-184">Jede auftretende Ausnahme wird durch die Ausnahmebehandlung des Servers behandelt.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-184">Any exception that occurs is handled by the server's exception handling.</span></span> <span data-ttu-id="7a9c4-185">Konfigurierte benutzerdefinierte Fehlerseiten, Middleware oder Filter zur Fehlerbehandlung haben keine Auswirkungen auf dieses Verhalten.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-185">Any configured custom error pages or exception handling middleware or filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="7a9c4-186">Fehlerbehandlung während des Starts</span><span class="sxs-lookup"><span data-stu-id="7a9c4-186">Startup exception handling</span></span>

<span data-ttu-id="7a9c4-187">Nur auf Hostebene können Ausnahmen behandelt werden, die während des Starts einer App auftreten.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-187">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="7a9c4-188">Sie können mithilfe des [Webhosts](xref:fundamentals/host/web-host) [ konfigurieren, wie der Host während des Starts auf Fehler reagiert](xref:fundamentals/host/web-host#detailed-errors), indem Sie die Schlüssel `captureStartupErrors` und `detailedErrors` verwenden.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-188">Using [Web Host](xref:fundamentals/host/web-host), you can [configure how the host behaves in response to errors during startup](xref:fundamentals/host/web-host#detailed-errors) with the `captureStartupErrors` and `detailedErrors` keys.</span></span>

<span data-ttu-id="7a9c4-189">Das Hosting kann nur dann eine Fehlerseite für einen erfassten Fehler beim Start anzeigen, wenn der Fehler nach der Bindung der Hostadresse bzw. des Ports auftritt.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-189">Hosting can only show an error page for a captured startup error if the error occurs after host address/port binding.</span></span> <span data-ttu-id="7a9c4-190">Wenn bei einer Bindung aus beliebigem Grund ein Fehler auftritt, geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="7a9c4-190">If any binding fails for any reason:</span></span>

* <span data-ttu-id="7a9c4-191">Die Hostebene protokolliert eine kritische Ausnahme.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-191">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="7a9c4-192">Der DotNet-Prozess stürzt ab.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-192">The dotnet process crashes.</span></span>
* <span data-ttu-id="7a9c4-193">Wenn die App auf dem [Kestrel](xref:fundamentals/servers/kestrel)-Server ausgeführt wird, wird keine Fehlerseite angezeigt.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-193">No error page is displayed when the app is running on the [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

<span data-ttu-id="7a9c4-194">Wenn sie auf [IIS](/iis) oder [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ausgeführt wird, wird ein *Prozessfehler 502.5* vom [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) zurückgegeben, wenn der Prozess nicht starten kann.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-194">When running on [IIS](/iis) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="7a9c4-195">Weitere Informationen finden Sie unter <xref:host-and-deploy/iis/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-195">For more information, see <xref:host-and-deploy/iis/troubleshoot>.</span></span> <span data-ttu-id="7a9c4-196">Weitere Informationen zum Beheben von Startproblemen mit Azure App Service finden Sie unter <xref:host-and-deploy/azure-apps/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-196">For information on troubleshooting startup issues with Azure App Service, see <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>

## <a name="aspnet-core-mvc-error-handling"></a><span data-ttu-id="7a9c4-197">ASP.NET Core MVC-Fehlerbehandlung</span><span class="sxs-lookup"><span data-stu-id="7a9c4-197">ASP.NET Core MVC error handling</span></span>

<span data-ttu-id="7a9c4-198">[MVC](xref:mvc/overview)-Apps verfügen über einige zusätzliche Optionen für die Fehlerbehandlung, z.B. das Konfigurieren von Ausnahmefiltern und das Durchführen einer Modellvalidierung.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-198">[MVC](xref:mvc/overview) apps have some additional options for handling errors, such as configuring exception filters and performing model validation.</span></span>

### <a name="exception-filters"></a><span data-ttu-id="7a9c4-199">Ausnahmefilter</span><span class="sxs-lookup"><span data-stu-id="7a9c4-199">Exception filters</span></span>

<span data-ttu-id="7a9c4-200">Ausnahmefilter können in einer MVC-App global oder auf der Grundlage eines Controllers oder einer Aktion konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-200">Exception filters can be configured globally or on a per-controller or per-action basis in an MVC app.</span></span> <span data-ttu-id="7a9c4-201">Diese Filter verarbeiten jede nicht behandelte Ausnahme, die während der Ausführung eine Controlleraktion oder eines anderen Filters auftritt.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-201">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="7a9c4-202">Diese Dateien werden andernfalls nicht aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-202">These filters aren't called otherwise.</span></span> <span data-ttu-id="7a9c4-203">Weitere Informationen dazu finden Sie unter <xref:mvc/controllers/filters>.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-203">To learn more, see <xref:mvc/controllers/filters>.</span></span>

> [!TIP]
> <span data-ttu-id="7a9c4-204">Ausnahmefilter eignen sich zum Auffangen von Ausnahmen, die in MVC-Aktionen auftreten. Sie sind jedoch nicht so flexibel wie Middleware für die Fehlerbehandlung.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-204">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as error handling middleware.</span></span> <span data-ttu-id="7a9c4-205">Wir empfehlen die Verwendung von Middleware.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-205">We recommend the use of middleware.</span></span> <span data-ttu-id="7a9c4-206">Verwenden Sie Filter nur dann, wenn Sie für die Fehlerbehandlung auf Grundlage einer ausgewählten MVC-Aktion eine *andere* Strategie anwenden müssen.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-206">Use filters only where you need to perform error handling *differently* based on which MVC action is chosen.</span></span>

### <a name="handle-model-state-errors"></a><span data-ttu-id="7a9c4-207">Behandeln von Modellstatusfehlern</span><span class="sxs-lookup"><span data-stu-id="7a9c4-207">Handle model state errors</span></span>

<span data-ttu-id="7a9c4-208">Die [Modellvalidierung](xref:mvc/models/validation) tritt vor jeder ausgelösten Controlleraktion auf. Die Aktionsmethode ist dafür verantwortlich, [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) zu untersuchen und angemessen zu reagieren.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-208">[Model validation](xref:mvc/models/validation) occurs prior to invoking each controller action, and it's the action method's responsibility to inspect [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) and react appropriately.</span></span>

<span data-ttu-id="7a9c4-209">Einige Apps wählen eine Standardkonvention aus, um [Modellvalidierungsfehler](xref:mvc/models/validation) zu behandeln. Dann kann ein [Filter](xref:mvc/controllers/filters) hilfreich sein, um eine solche Richtlinie zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-209">Some apps choose to follow a standard convention for dealing with [model validation](xref:mvc/models/validation) errors, in which case a [filter](xref:mvc/controllers/filters) may be an appropriate place to implement such a policy.</span></span> <span data-ttu-id="7a9c4-210">Sie sollten testen, wie sich Ihre Aktionen mit ungültigen Modellstatus verhalten.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-210">You should test how your actions behave with invalid model states.</span></span> <span data-ttu-id="7a9c4-211">Weitere Informationen finden Sie unter <xref:mvc/controllers/testing>.</span><span class="sxs-lookup"><span data-stu-id="7a9c4-211">For more information, see <xref:mvc/controllers/testing>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7a9c4-212">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="7a9c4-212">Additional resources</span></span>

* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-apps/troubleshoot>

---
title: ASP.NET Core-Middleware
author: rick-anderson
description: Erfahren Sie mehr über ASP.NET Core-Middleware und die Anforderungspipeline.
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
uid: fundamentals/middleware/index
ms.openlocfilehash: c55dbd5a9ac31f55daf1cb3146fb18b91b016919
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341588"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="aae88-103">ASP.NET Core-Middleware</span><span class="sxs-lookup"><span data-stu-id="aae88-103">ASP.NET Core Middleware</span></span>

<span data-ttu-id="aae88-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="aae88-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="aae88-105">Middleware ist Software, die zu einer Anwendungspipeline zusammengesetzt wird, um Anforderungen und Antworten zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="aae88-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="aae88-106">Jede Komponente kann Folgendes ausführen:</span><span class="sxs-lookup"><span data-stu-id="aae88-106">Each component:</span></span>

* <span data-ttu-id="aae88-107">Entscheiden, ob die Anforderung an die nächste Komponente in der Pipeline übergeben werden soll.</span><span class="sxs-lookup"><span data-stu-id="aae88-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="aae88-108">Ausführen von Arbeiten, bevor oder nachdem die nächste Komponente in der Pipeline aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="aae88-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="aae88-109">Anforderungsdelegaten werden verwendet, um die Anforderungspipeline zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="aae88-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="aae88-110">Die Anforderungsdelegaten behandeln jede HTTP-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="aae88-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="aae88-111">Anforderungsdelegaten werden mit den Erweiterungsmethoden <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> und <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="aae88-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="aae88-112">Ein einzelner Anforderungsdelegat kann inline als anonyme Methode angegeben werden (sogenannte Inline-Middleware), oder er kann in einer wiederverwendbaren Klasse definiert werden.</span><span class="sxs-lookup"><span data-stu-id="aae88-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="aae88-113">Diese wiederverwendbaren Klassen und anonymen Inline-Methoden sind *Middleware* bzw. *Middlewarekomponenten*.</span><span class="sxs-lookup"><span data-stu-id="aae88-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="aae88-114">Jede Middlewarekomponente in der Anforderungspipeline ist für das Aufrufen der jeweils nächsten Komponente in der Pipeline oder, wenn nötig, für das Kurzschließen der Pipeline zuständig.</span><span class="sxs-lookup"><span data-stu-id="aae88-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span>

<span data-ttu-id="aae88-115">Unter <xref:migration/http-modules> wird der Unterschied zwischen Anforderungspipelines in ASP.NET Core und ASP.NET 4.x erklärt. Außerdem werden dort weitere Beispiele für Middleware gegeben.</span><span class="sxs-lookup"><span data-stu-id="aae88-115"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides more middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="aae88-116">Erstellen einer Middlewarepipeline mit IApplicationBuilder</span><span class="sxs-lookup"><span data-stu-id="aae88-116">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="aae88-117">Die ASP.NET Core-Anforderungspipeline besteht aus einer Sequenz von Anforderungsdelegaten, die nacheinander aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="aae88-117">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="aae88-118">Das Konzept wird im folgenden Diagramm veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="aae88-118">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="aae88-119">Der Ausführungsthread folgt den schwarzen Pfeilen.</span><span class="sxs-lookup"><span data-stu-id="aae88-119">The thread of execution follows the black arrows.</span></span>

![Anforderungsverarbeitungsmuster mit eingehender Anforderung, deren Verarbeitung von drei Middlewares und die ausgehende Antwort der Anwendung.](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="aae88-123">Jeder Delegat kann Vorgänge vor und nach dem nächsten Delegaten ausführen.</span><span class="sxs-lookup"><span data-stu-id="aae88-123">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="aae88-124">Ein Delegat kann sich auch dagegen entscheiden, eine Anforderung an den nächsten Delegaten zu übergeben. Dies wird als *Kurzschluss der Anforderungspipeline* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="aae88-124">A delegate can also decide to not pass a request to the next delegate, which is called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="aae88-125">Das Kurzschließen ist oft sinnvoll, da es unnötige Arbeit verhindert.</span><span class="sxs-lookup"><span data-stu-id="aae88-125">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="aae88-126">Die Middleware für statische Dateien kann beispielsweise die Anforderung einer statischen Datei zurückgeben und den Rest der Pipeline kurzschließen.</span><span class="sxs-lookup"><span data-stu-id="aae88-126">For example, Static File Middleware can return a request for a static file and short-circuit the rest of the pipeline.</span></span> <span data-ttu-id="aae88-127">Die Ausnahmebehandlungsdelegaten werden am Anfang der Pipeline aufgerufen, sodass sie Ausnahmen abfangen können, die zu einem späteren Zeitpunkt in der Pipeline ausgelöst werden.</span><span class="sxs-lookup"><span data-stu-id="aae88-127">Exception-handling delegates are called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="aae88-128">Die einfachste mögliche ASP.NET Core-App enthält einen einzigen Anforderungsdelegaten, der alle Anforderungen verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="aae88-128">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="aae88-129">In diesem Fall ist keine tatsächliche Anforderungspipeline vorhanden.</span><span class="sxs-lookup"><span data-stu-id="aae88-129">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="aae88-130">Stattdessen wird eine einzelne anonyme Funktion als Antwort auf jede HTTP-Anforderung aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="aae88-130">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs?name=snippet1)]

<span data-ttu-id="aae88-131">Der erste <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>-Delegat beendet die Pipeline.</span><span class="sxs-lookup"><span data-stu-id="aae88-131">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegate terminates the pipeline.</span></span>

<span data-ttu-id="aae88-132">Mit <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> können Sie mehrere Anforderungedelegate miteinander verknüpfen.</span><span class="sxs-lookup"><span data-stu-id="aae88-132">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="aae88-133">Der Parameter `next` steht für den nächsten Delegaten in der Pipeline.</span><span class="sxs-lookup"><span data-stu-id="aae88-133">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="aae88-134">Sie können die Pipeline kurzschließen, indem Sie den Parameter *next* *nicht* aufrufen.</span><span class="sxs-lookup"><span data-stu-id="aae88-134">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="aae88-135">Normalerweise können Sie Aktionen sowohl vor als auch nach dem nächsten Delegaten durchführen. Dies wird in folgendem Beispiel veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="aae88-135">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?name=snippet1)]

> [!WARNING]
> <span data-ttu-id="aae88-136">Rufen Sie `next.Invoke` nicht auf, nachdem die Antwort an den Client gesendet wurde.</span><span class="sxs-lookup"><span data-stu-id="aae88-136">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="aae88-137">An <xref:Microsoft.AspNetCore.Http.HttpResponse> vorgenommene Änderungen lösen nach dem Start der Antwort eine Ausnahme aus.</span><span class="sxs-lookup"><span data-stu-id="aae88-137">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="aae88-138">Änderungen wie das Festlegen von Headern und einem Statuscode lösen beispielsweise eine Ausnahme aus.</span><span class="sxs-lookup"><span data-stu-id="aae88-138">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="aae88-139">Wenn Sie nach dem Aufruf von `next` in den Antworttext schreiben, kann dies:</span><span class="sxs-lookup"><span data-stu-id="aae88-139">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="aae88-140">einen Protokollverstoß verursachen,</span><span class="sxs-lookup"><span data-stu-id="aae88-140">May cause a protocol violation.</span></span> <span data-ttu-id="aae88-141">wenn Sie z.B. mehr als das genannte `Content-Length`-Objekt schreiben.</span><span class="sxs-lookup"><span data-stu-id="aae88-141">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="aae88-142">Fehler im Textformat auslösen,</span><span class="sxs-lookup"><span data-stu-id="aae88-142">May corrupt the body format.</span></span> <span data-ttu-id="aae88-143">wenn Sie z.B. eine HTML-Fußzeile in eine CSS-Datei schreiben.</span><span class="sxs-lookup"><span data-stu-id="aae88-143">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="aae88-144"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> ist ein nützlicher Hinweis, der angibt, ob Header gesendet wurden oder ob in den Text geschrieben wurde.</span><span class="sxs-lookup"><span data-stu-id="aae88-144"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

## <a name="order"></a><span data-ttu-id="aae88-145">Reihenfolge</span><span class="sxs-lookup"><span data-stu-id="aae88-145">Order</span></span>

<span data-ttu-id="aae88-146">Die Reihenfolge, in der Middlewarekomponenten in der `Startup.Configure`-Methode hinzugefügt werden, legt die Reihenfolge fest, in der die Middlewarekomponenten bei Anforderungen aufgerufen werden. Bei Antworten gilt die umgekehrte Reihenfolge.</span><span class="sxs-lookup"><span data-stu-id="aae88-146">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="aae88-147">Die Reihenfolge trägt wesentlich zur Sicherheit, Leistung und Funktionalität bei.</span><span class="sxs-lookup"><span data-stu-id="aae88-147">The order is critical for security, performance, and functionality.</span></span>

<span data-ttu-id="aae88-148">Die folgenden `Startup.Configure`-Methode fügt Middlewarekomponenten für allgemeine App-Szenarien hinzu:</span><span class="sxs-lookup"><span data-stu-id="aae88-148">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

::: moniker range=">= aspnetcore-2.0"

1. <span data-ttu-id="aae88-149">Ausnahme-/Fehlerbehandlung</span><span class="sxs-lookup"><span data-stu-id="aae88-149">Exception/error handling</span></span>
1. <span data-ttu-id="aae88-150">HTTP Strict Transport Security Protocol</span><span class="sxs-lookup"><span data-stu-id="aae88-150">HTTP Strict Transport Security Protocol</span></span>
1. <span data-ttu-id="aae88-151">HTTPS-Umleitung</span><span class="sxs-lookup"><span data-stu-id="aae88-151">HTTPS redirection</span></span>
1. <span data-ttu-id="aae88-152">Statischer Dateiserver</span><span class="sxs-lookup"><span data-stu-id="aae88-152">Static file server</span></span>
1. <span data-ttu-id="aae88-153">Cookierichtliniendurchsetzung</span><span class="sxs-lookup"><span data-stu-id="aae88-153">Cookie policy enforcement</span></span>
1. <span data-ttu-id="aae88-154">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="aae88-154">Authentication</span></span>
1. <span data-ttu-id="aae88-155">Sitzung</span><span class="sxs-lookup"><span data-stu-id="aae88-155">Session</span></span>
1. <span data-ttu-id="aae88-156">MVC</span><span class="sxs-lookup"><span data-stu-id="aae88-156">MVC</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    if (env.IsDevelopment())
    {
        // When the app runs in the Development environment:
        //   Use the Developer Exception Page to report app runtime errors.
        //   Use the Database Error Page to report database runtime errors.
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        // When the app doesn't run in the Development environment:
        //   Enable the Exception Handler Middleware to catch exceptions
        //     thrown in the following middlewares.
        //   Use the HTTP Strict Transport Security Protocol (HSTS)
        //     Middleware.
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    // Use HTTPS Redirection Middleware to redirect HTTP requests to HTTPS.
    app.UseHttpsRedirection();

    // Return static files and end the pipeline.
    app.UseStaticFiles();

    // Use Cookie Policy Middleware to conform to EU General Data 
    // Protection Regulation (GDPR) regulations.
    app.UseCookiePolicy();

    // Authenticate before the user accesses secure resources.
    app.UseAuthentication();

    // If the app uses session state, call Session Middleware after Cookie 
    // Policy Middleware and before MVC Middleware.
    app.UseSession();

    // Add MVC to the request pipeline.
    app.UseMvc();
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

1. <span data-ttu-id="aae88-157">Ausnahme-/Fehlerbehandlung</span><span class="sxs-lookup"><span data-stu-id="aae88-157">Exception/error handling</span></span>
1. <span data-ttu-id="aae88-158">Statische Dateien</span><span class="sxs-lookup"><span data-stu-id="aae88-158">Static files</span></span>
1. <span data-ttu-id="aae88-159">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="aae88-159">Authentication</span></span>
1. <span data-ttu-id="aae88-160">Sitzung</span><span class="sxs-lookup"><span data-stu-id="aae88-160">Session</span></span>
1. <span data-ttu-id="aae88-161">MVC</span><span class="sxs-lookup"><span data-stu-id="aae88-161">MVC</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Enable the Exception Handler Middleware to catch exceptions
    //   thrown in the following middlewares.
    app.UseExceptionHandler("/Home/Error");

    // Return static files and end the pipeline.
    app.UseStaticFiles();

    // Authenticate before you access secure resources.
    app.UseIdentity();

    // If the app uses session state, call UseSession before 
    // MVC Middleware.
    app.UseSession();

    // Add MVC to the request pipeline.
    app.UseMvcWithDefaultRoute();
}
```

::: moniker-end

<span data-ttu-id="aae88-162">Im vorhergehenden Beispielcode wird jede Middleware-Erweiterungsmethode in <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> über den <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName>-Namespace verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="aae88-162">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="aae88-163"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> ist die erste Middlewarekomponente, die der Pipeline hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="aae88-163"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="aae88-164">Aus diesem Grund fängt die Middleware für den Ausnahmehandler alle Ausnahmen ab, die in späteren Aufrufen auftreten.</span><span class="sxs-lookup"><span data-stu-id="aae88-164">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="aae88-165">Die Middleware für statische Dateien wird am Anfang der Pipeline aufgerufen, damit sie Anforderungen und Kurzschlüsse verarbeiten kann, ohne dass die verbleibenden Komponenten durchlaufen werden müssen.</span><span class="sxs-lookup"><span data-stu-id="aae88-165">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="aae88-166">Die Middleware für statische Dateien stellt **keine** Autorisierungsüberprüfungen bereit.</span><span class="sxs-lookup"><span data-stu-id="aae88-166">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="aae88-167">Alle Dateien, die von ihr bearbeitet werden, Dateien unter *wwwroot* inbegriffen, sind öffentlich verfügbar.</span><span class="sxs-lookup"><span data-stu-id="aae88-167">Any files served by it, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="aae88-168">Informationen zum Sichern statischer Dateien finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="aae88-168">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="aae88-169">Wenn die Anforderung nicht von der Middleware für statische Dateien verarbeitet wird, wird sie an die Authentifizierungsmiddleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) übergeben, welche die Authentifizierung durchführt.</span><span class="sxs-lookup"><span data-stu-id="aae88-169">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="aae88-170">Die Authentifizierung schließt nicht authentifizierte Anforderungen nicht kurz.</span><span class="sxs-lookup"><span data-stu-id="aae88-170">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="aae88-171">Auch wenn die Authentifizierungsmiddleware Anforderungen authentifiziert, erfolgt die Autorisierung (und Ablehnung) erst dann, wenn MVC eine spezifische Razor Page oder einen MVC-Controller und eine Aktion ausgewählt hat.</span><span class="sxs-lookup"><span data-stu-id="aae88-171">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="aae88-172">Wenn die Anforderung nicht von der Middleware für statische Dateien verarbeitet wird, wird sie an die Identity-Middleware (<xref:Microsoft.AspNetCore.Builder.BuilderExtensions.UseIdentity*>) übergeben, welche die Authentifizierung durchführt.</span><span class="sxs-lookup"><span data-stu-id="aae88-172">If the request isn't handled by Static File Middleware, it's passed on to the Identity Middleware (<xref:Microsoft.AspNetCore.Builder.BuilderExtensions.UseIdentity*>), which performs authentication.</span></span> <span data-ttu-id="aae88-173">Identity schließt keine unautorisierten Anforderungen kurz.</span><span class="sxs-lookup"><span data-stu-id="aae88-173">Identity doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="aae88-174">Auch wenn Identity Anforderungen authentifiziert, erfolgt die Autorisierung (und Ablehnung) erst dann, wenn MVC einen spezifischen Controller und eine Aktion ausgewählt hat.</span><span class="sxs-lookup"><span data-stu-id="aae88-174">Although Identity authenticates requests, authorization (and rejection) occurs only after MVC selects a specific controller and action.</span></span>

::: moniker-end

<span data-ttu-id="aae88-175">Im folgenden Beispiel wird eine Middlewarereihenfolge veranschaulicht, bei der Anforderungen statischer Dateien von der Middleware für statische Dateien vor der Middleware für die Antwortkomprimierung verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="aae88-175">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="aae88-176">Die statischen Dateien werden bei dieser Middlewarereihenfolge nicht komprimiert.</span><span class="sxs-lookup"><span data-stu-id="aae88-176">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="aae88-177">Die MVC-Antworten von <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> können komprimiert werden.</span><span class="sxs-lookup"><span data-stu-id="aae88-177">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files not compressed by Static File Middleware.
    app.UseStaticFiles();
    app.UseResponseCompression();
    app.UseMvcWithDefaultRoute();
}
```

### <a name="use-run-and-map"></a><span data-ttu-id="aae88-178">Use, Run und Map</span><span class="sxs-lookup"><span data-stu-id="aae88-178">Use, Run, and Map</span></span>

<span data-ttu-id="aae88-179">Konfigurieren Sie die HTTP-Pipeline mit `Use`, `Run` und `Map`.</span><span class="sxs-lookup"><span data-stu-id="aae88-179">Configure the HTTP pipeline using `Use`, `Run`, and `Map`.</span></span> <span data-ttu-id="aae88-180">Die `Use`-Methode kann die Pipeline kurzschließen (wenn sie keinen `next`-Anforderungsdelegaten aufruft).</span><span class="sxs-lookup"><span data-stu-id="aae88-180">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="aae88-181">`Run` ist eine Konvention. Einige Middlewarekomponenten machen möglicherweise `Run[Middleware]`-Methoden verfügbar, die am Ende einer Pipeline ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="aae88-181">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="aae88-182"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>-Erweiterungen werden als Konvention zum Branchen der Pipeline verwendet.</span><span class="sxs-lookup"><span data-stu-id="aae88-182"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="aae88-183">`Map*` brancht die Anforderungspipeline auf Grundlage von Übereinstimmungen des angegebenen Anforderungspfads.</span><span class="sxs-lookup"><span data-stu-id="aae88-183">`Map*` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="aae88-184">Wenn der Anforderungspfad mit dem angegebenen Pfad beginnt, wird der Branch ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="aae88-184">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs?name=snippet1)]

<span data-ttu-id="aae88-185">In der folgenden Tabelle sind die Anforderungen und Antworten von `http://localhost:1234` mit dem oben stehenden Code aufgelistet.</span><span class="sxs-lookup"><span data-stu-id="aae88-185">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="aae88-186">Anforderung</span><span class="sxs-lookup"><span data-stu-id="aae88-186">Request</span></span>             | <span data-ttu-id="aae88-187">Antwort</span><span class="sxs-lookup"><span data-stu-id="aae88-187">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="aae88-188">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="aae88-188">localhost:1234</span></span>      | <span data-ttu-id="aae88-189">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="aae88-189">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="aae88-190">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="aae88-190">localhost:1234/map1</span></span> | <span data-ttu-id="aae88-191">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="aae88-191">Map Test 1</span></span>                   |
| <span data-ttu-id="aae88-192">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="aae88-192">localhost:1234/map2</span></span> | <span data-ttu-id="aae88-193">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="aae88-193">Map Test 2</span></span>                   |
| <span data-ttu-id="aae88-194">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="aae88-194">localhost:1234/map3</span></span> | <span data-ttu-id="aae88-195">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="aae88-195">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="aae88-196">Wenn `Map` verwendet wird, werden die übereinstimmenden Pfadsegmente bzw. das übereinstimmende Pfadsegment aus `HttpRequest.Path` entfernt und für jede Anforderung an `HttpRequest.PathBase` angehängt.</span><span class="sxs-lookup"><span data-stu-id="aae88-196">When `Map` is used, the matched path segment(s) are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="aae88-197">[MapWhen](/dotnet/api/microsoft.aspnetcore.builder.mapwhenextensions) brancht die Anforderungspipeline auf Grundlage des Ergebnisses des angegebenen Prädikats.</span><span class="sxs-lookup"><span data-stu-id="aae88-197">[MapWhen](/dotnet/api/microsoft.aspnetcore.builder.mapwhenextensions) branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="aae88-198">Jedes Prädikat vom Typ `Func<HttpContext, bool>` kann verwendet werden, um Anforderungen einem neuen Branch der Pipeline zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="aae88-198">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="aae88-199">Im folgenden Beispiel wird ein Prädikat verwendet, um das Vorhandensein der Abfragezeichenfolgenvariablen `branch` zu ermitteln:</span><span class="sxs-lookup"><span data-stu-id="aae88-199">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?name=snippet1)]

<span data-ttu-id="aae88-200">In der folgenden Tabelle sind die Anforderungen und Antworten von `http://localhost:1234` mit dem oben stehenden Code aufgelistet.</span><span class="sxs-lookup"><span data-stu-id="aae88-200">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="aae88-201">Anforderung</span><span class="sxs-lookup"><span data-stu-id="aae88-201">Request</span></span>                       | <span data-ttu-id="aae88-202">Antwort</span><span class="sxs-lookup"><span data-stu-id="aae88-202">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="aae88-203">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="aae88-203">localhost:1234</span></span>                | <span data-ttu-id="aae88-204">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="aae88-204">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="aae88-205">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="aae88-205">localhost:1234/?branch=master</span></span> | <span data-ttu-id="aae88-206">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="aae88-206">Branch used = master</span></span>         |

<span data-ttu-id="aae88-207">`Map` unterstützt das Schachteln, wie z.B. in folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="aae88-207">`Map` supports nesting, for example:</span></span>

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
   ```

<span data-ttu-id="aae88-208">`Map` kann auch mehrere Segmente auf einmal zuordnen:</span><span class="sxs-lookup"><span data-stu-id="aae88-208">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?name=snippet1&highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="aae88-209">Integrierte Middleware</span><span class="sxs-lookup"><span data-stu-id="aae88-209">Built-in middleware</span></span>

<span data-ttu-id="aae88-210">Die folgenden Middlewarekomponenten sind im Lieferumfang von ASP.NET Core enthalten.</span><span class="sxs-lookup"><span data-stu-id="aae88-210">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="aae88-211">Die Spalte *Reihenfolge* enthält Hinweise zur Platzierung der Middleware in der Anforderungspipeline und zu den Bedingungen, unter denen die Middleware die Anforderung möglicherweise beendet und andere Middleware von der Verarbeitung einer Anforderung abhält.</span><span class="sxs-lookup"><span data-stu-id="aae88-211">The *Order* column provides notes on the middleware's placement in the request pipeline and under what conditions the middleware may terminate the request and prevent other middleware from processing a request.</span></span>

| <span data-ttu-id="aae88-212">Middleware</span><span class="sxs-lookup"><span data-stu-id="aae88-212">Middleware</span></span> | <span data-ttu-id="aae88-213">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="aae88-213">Description</span></span> | <span data-ttu-id="aae88-214">Reihenfolge</span><span class="sxs-lookup"><span data-stu-id="aae88-214">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="aae88-215">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="aae88-215">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="aae88-216">Bietet Unterstützung für Authentifizierungen.</span><span class="sxs-lookup"><span data-stu-id="aae88-216">Provides authentication support.</span></span> | <span data-ttu-id="aae88-217">Bevor `HttpContext.User` erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="aae88-217">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="aae88-218">Terminal für OAuth-Rückrufe.</span><span class="sxs-lookup"><span data-stu-id="aae88-218">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="aae88-219">Cookierichtlinie</span><span class="sxs-lookup"><span data-stu-id="aae88-219">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="aae88-220">Verfolgt die Zustimmung von Benutzern zum Speichern persönlicher Informationen nach und erzwingt die Mindeststandards für Cookiefelder, z.B. `secure` und `SameSite`.</span><span class="sxs-lookup"><span data-stu-id="aae88-220">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="aae88-221">Befindet sich vor der Middleware, die Cookies ausstellt.</span><span class="sxs-lookup"><span data-stu-id="aae88-221">Before middleware that issues cookies.</span></span> <span data-ttu-id="aae88-222">Beispiele: Authentifizierung, Sitzung, MVC (TempData).</span><span class="sxs-lookup"><span data-stu-id="aae88-222">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="aae88-223">CORS</span><span class="sxs-lookup"><span data-stu-id="aae88-223">CORS</span></span>](xref:security/cors) | <span data-ttu-id="aae88-224">Konfiguriert die Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS).</span><span class="sxs-lookup"><span data-stu-id="aae88-224">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="aae88-225">Vor Komponenten, die CORS verwenden.</span><span class="sxs-lookup"><span data-stu-id="aae88-225">Before components that use CORS.</span></span> |
| [<span data-ttu-id="aae88-226">Diagnose</span><span class="sxs-lookup"><span data-stu-id="aae88-226">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="aae88-227">Konfiguriert Diagnosen.</span><span class="sxs-lookup"><span data-stu-id="aae88-227">Configures diagnostics.</span></span> | <span data-ttu-id="aae88-228">Vor Komponenten, die Fehler erzeugen.</span><span class="sxs-lookup"><span data-stu-id="aae88-228">Before components that generate errors.</span></span> |
| [<span data-ttu-id="aae88-229">Weitergeleitete Header</span><span class="sxs-lookup"><span data-stu-id="aae88-229">Forwarded Headers</span></span>](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions) | <span data-ttu-id="aae88-230">Leitet Proxyheader an die aktuelle Anforderung weiter.</span><span class="sxs-lookup"><span data-stu-id="aae88-230">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="aae88-231">Vor Komponenten, die die aktualisierten Felder nutzen.</span><span class="sxs-lookup"><span data-stu-id="aae88-231">Before components that consume the updated fields.</span></span> <span data-ttu-id="aae88-232">Beispiele: Schema, Host, Client-IP, Methode.</span><span class="sxs-lookup"><span data-stu-id="aae88-232">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="aae88-233">Integritätsprüfung</span><span class="sxs-lookup"><span data-stu-id="aae88-233">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="aae88-234">Überprüft die Integrität der ASP.NET Core-App und ihrer Abhängigkeiten, z. B. Überprüfung der Datenbankverfügbarkeit.</span><span class="sxs-lookup"><span data-stu-id="aae88-234">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="aae88-235">Abschließend, wenn eine Anforderung mit einem Integritätsprüfungs-Endpunkt übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="aae88-235">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="aae88-236">Außerkraftsetzung der HTTP-Methode</span><span class="sxs-lookup"><span data-stu-id="aae88-236">HTTP Method Override</span></span>](/dotnet/api/microsoft.aspnetcore.builder.httpmethodoverrideextensions) | <span data-ttu-id="aae88-237">Ermöglicht es eingehenden POST-Anforderungen, die Methode außer Kraft zu setzen.</span><span class="sxs-lookup"><span data-stu-id="aae88-237">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="aae88-238">Vor Komponenten, die die aktualisierte Methode nutzen.</span><span class="sxs-lookup"><span data-stu-id="aae88-238">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="aae88-239">HTTPS-Umleitung</span><span class="sxs-lookup"><span data-stu-id="aae88-239">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="aae88-240">Leitet alle HTTP-Anforderungen an HTTPS um (ASP.NET Core 2.1 oder höher).</span><span class="sxs-lookup"><span data-stu-id="aae88-240">Redirect all HTTP requests to HTTPS (ASP.NET Core 2.1 or later).</span></span> | <span data-ttu-id="aae88-241">Vor Komponenten, die die URL nutzen.</span><span class="sxs-lookup"><span data-stu-id="aae88-241">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="aae88-242">HTTP Strict Transport Security (HSTS)</span><span class="sxs-lookup"><span data-stu-id="aae88-242">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="aae88-243">Sicherheits-Middleware, die einen besonderen Antwortheader hinzufügt (ASP.NET Core 2.1 oder höher).</span><span class="sxs-lookup"><span data-stu-id="aae88-243">Security enhancement middleware that adds a special response header (ASP.NET Core 2.1 or later).</span></span> | <span data-ttu-id="aae88-244">Bevor Antworten gesendet werden und nach Komponenten, die Anforderungen ändern.</span><span class="sxs-lookup"><span data-stu-id="aae88-244">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="aae88-245">Beispiele: weitergeleitete Header, URL-Umschreibung.</span><span class="sxs-lookup"><span data-stu-id="aae88-245">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="aae88-246">MVC</span><span class="sxs-lookup"><span data-stu-id="aae88-246">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="aae88-247">Verarbeitet Anforderungen mit MVC/Razor Pages (ASP.NET Core 2.0 oder höher).</span><span class="sxs-lookup"><span data-stu-id="aae88-247">Processes requests with MVC/Razor Pages (ASP.NET Core 2.0 or later).</span></span> | <span data-ttu-id="aae88-248">Abschließend, wenn eine Anforderung mit einer Route übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="aae88-248">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="aae88-249">OWIN</span><span class="sxs-lookup"><span data-stu-id="aae88-249">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="aae88-250">Interoperabilität mit auf OWIN basierten Apps, Servern und Middleware.</span><span class="sxs-lookup"><span data-stu-id="aae88-250">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="aae88-251">Abschließend, wenn die OWIN-Middleware die Anforderung vollständig verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="aae88-251">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="aae88-252">Zwischenspeichern von Antworten</span><span class="sxs-lookup"><span data-stu-id="aae88-252">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="aae88-253">Bietet Unterstützung für das Zwischenspeichern von Antworten.</span><span class="sxs-lookup"><span data-stu-id="aae88-253">Provides support for caching responses.</span></span> | <span data-ttu-id="aae88-254">Vor Komponenten, für die das Zwischenspeichern erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="aae88-254">Before components that require caching.</span></span> |
| [<span data-ttu-id="aae88-255">Antwortkomprimierung</span><span class="sxs-lookup"><span data-stu-id="aae88-255">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="aae88-256">Bietet Unterstützung für das Komprimieren von Antworten.</span><span class="sxs-lookup"><span data-stu-id="aae88-256">Provides support for compressing responses.</span></span> | <span data-ttu-id="aae88-257">Vor Komponenten, für die das Komprimieren erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="aae88-257">Before components that require compression.</span></span> |
| [<span data-ttu-id="aae88-258">Lokalisierung von Anforderungen</span><span class="sxs-lookup"><span data-stu-id="aae88-258">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="aae88-259">Bietet Unterstützung für die Lokalisierung.</span><span class="sxs-lookup"><span data-stu-id="aae88-259">Provides localization support.</span></span> | <span data-ttu-id="aae88-260">Vor der Lokalisierung vertraulicher Komponenten.</span><span class="sxs-lookup"><span data-stu-id="aae88-260">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="aae88-261">Routing</span><span class="sxs-lookup"><span data-stu-id="aae88-261">Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="aae88-262">Definiert Anforderungsrouten und schränkt diese ein.</span><span class="sxs-lookup"><span data-stu-id="aae88-262">Defines and constrains request routes.</span></span> | <span data-ttu-id="aae88-263">Terminal für entsprechende Routen.</span><span class="sxs-lookup"><span data-stu-id="aae88-263">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="aae88-264">Sitzung</span><span class="sxs-lookup"><span data-stu-id="aae88-264">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="aae88-265">Bietet Unterstützung für das Verwalten von Benutzersitzungen.</span><span class="sxs-lookup"><span data-stu-id="aae88-265">Provides support for managing user sessions.</span></span> | <span data-ttu-id="aae88-266">Vor Komponenten, für die Sitzungen erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="aae88-266">Before components that require Session.</span></span> |
| [<span data-ttu-id="aae88-267">Statische Dateien</span><span class="sxs-lookup"><span data-stu-id="aae88-267">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="aae88-268">Bietet Unterstützung für das Verarbeiten statischer Dateien und das Durchsuchen des Verzeichnisses.</span><span class="sxs-lookup"><span data-stu-id="aae88-268">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="aae88-269">Abschließend, wenn eine Anforderung mit einer Datei übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="aae88-269">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="aae88-270">URL-Umschreibung</span><span class="sxs-lookup"><span data-stu-id="aae88-270">URL Rewriting</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="aae88-271">Bietet Unterstützung für das Umschreiben von URLs und das Umleiten von Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="aae88-271">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="aae88-272">Vor Komponenten, die die URL nutzen.</span><span class="sxs-lookup"><span data-stu-id="aae88-272">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="aae88-273">WebSockets</span><span class="sxs-lookup"><span data-stu-id="aae88-273">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="aae88-274">Aktiviert das WebSockets-Protokoll.</span><span class="sxs-lookup"><span data-stu-id="aae88-274">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="aae88-275">Vor Komponenten, die WebSocket-Anforderungen annehmen müssen.</span><span class="sxs-lookup"><span data-stu-id="aae88-275">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="write-middleware"></a><span data-ttu-id="aae88-276">Schreiben von Middleware</span><span class="sxs-lookup"><span data-stu-id="aae88-276">Write middleware</span></span>

<span data-ttu-id="aae88-277">Für gewöhnlich ist Middleware in einer Klasse gekapselt und wird mit einer Erweiterungsmethode verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="aae88-277">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="aae88-278">Sehen Sie sich folgende Middleware an, die die Kultur der aktuellen Anforderung über eine Abfragezeichenfolge festlegt:</span><span class="sxs-lookup"><span data-stu-id="aae88-278">Consider the following middleware, which sets the culture for the current request from a query string:</span></span>

[!code-csharp[](index/snapshot/Culture/StartupCulture.cs?name=snippet1)]

<span data-ttu-id="aae88-279">Im vorhergehenden Beispielcode wird die Erstellung einer Middlewarekomponente veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="aae88-279">The preceding sample code is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="aae88-280">Informationen zur Unterstützung der integrierten Lokalisierung für ASP.NET Core finden Sie unter <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="aae88-280">For ASP.NET Core's built-in localization support, see <xref:fundamentals/localization>.</span></span>

<span data-ttu-id="aae88-281">Sie können die Middleware testen, indem Sie die Kultur übergeben (z.B. `http://localhost:7997/?culture=no`).</span><span class="sxs-lookup"><span data-stu-id="aae88-281">You can test the middleware by passing in the culture, for example `http://localhost:7997/?culture=no`.</span></span>

<span data-ttu-id="aae88-282">Im folgenden Code wird der Middlewaredelegat in eine Klasse verschoben:</span><span class="sxs-lookup"><span data-stu-id="aae88-282">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[](index/snapshot/Culture/RequestCultureMiddleware.cs)]

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="aae88-283">Der Name der Middlewaremethode `Task` muss `Invoke` lauten.</span><span class="sxs-lookup"><span data-stu-id="aae88-283">The middleware `Task` method's name must be `Invoke`.</span></span> <span data-ttu-id="aae88-284">In ASP.NET Core 2.0 oder höher kann der Name `Invoke` oder `InvokeAsync` lauten.</span><span class="sxs-lookup"><span data-stu-id="aae88-284">In ASP.NET Core 2.0 or later, the name can be either `Invoke` or `InvokeAsync`.</span></span>

::: moniker-end

<span data-ttu-id="aae88-285">Die folgende Erweiterungsmethode stellt die Middleware über <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="aae88-285">The following extension method exposes the middleware through <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span></span>

[!code-csharp[](index/snapshot/Culture/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="aae88-286">Der folgende Code ruft die Methode von `Startup.Configure` auf:</span><span class="sxs-lookup"><span data-stu-id="aae88-286">The following code calls the middleware from `Startup.Configure`:</span></span>

[!code-csharp[](index/snapshot/Culture/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="aae88-287">Middleware sollte das [Prinzip der expliziten Abhängigkeiten](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) befolgen, indem sie ihre Abhängigkeiten in ihrem Konstruktor verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="aae88-287">Middleware should follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="aae88-288">Middleware wird einmal während der *Anwendungslebensdauer* erstellt.</span><span class="sxs-lookup"><span data-stu-id="aae88-288">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="aae88-289">Lesen Sie den Abschnitt [Voranforderungsbasierte Abhängigkeiten](#per-request-dependencies), wenn Sie Dienste für Middleware innerhalb einer Anforderung gemeinsam verwenden müssen.</span><span class="sxs-lookup"><span data-stu-id="aae88-289">See the [Per-request dependencies](#per-request-dependencies) section if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="aae88-290">Middlewarekomponenten können Ihre Abhängigkeiten über [Dependency Injection (DI)](xref:fundamentals/dependency-injection) mit Konstruktorparametern auflösen.</span><span class="sxs-lookup"><span data-stu-id="aae88-290">Middleware components can resolve their dependencies from [dependency injection (DI)](xref:fundamentals/dependency-injection) through constructor parameters.</span></span> <span data-ttu-id="aae88-291">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) kann auch direkt zusätzliche Parameter annehmen.</span><span class="sxs-lookup"><span data-stu-id="aae88-291">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) can also accept additional parameters directly.</span></span>

### <a name="per-request-dependencies"></a><span data-ttu-id="aae88-292">Voranforderungsbasierte Abhängigkeiten</span><span class="sxs-lookup"><span data-stu-id="aae88-292">Per-request dependencies</span></span>

<span data-ttu-id="aae88-293">Weil Middleware zum Zeitpunkt des Anwendungsstarts erstellt wird (und nicht voranforderungsbasiert), werden *bereichsbezogene* Lebensdauerdienste von Middlewarekonstruktoren in den einzelnen Anforderungen nicht gemeinsam mit anderen Typen mit Dependency Injection verwendet.</span><span class="sxs-lookup"><span data-stu-id="aae88-293">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors aren't shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="aae88-294">Wenn Sie einen *bereichsbezogenen* Dienst sowohl in Ihrer Middleware als auch in anderen Typen verwenden müssen, fügen Sie diese Dienste zur Signatur der `Invoke`-Methode hinzu.</span><span class="sxs-lookup"><span data-stu-id="aae88-294">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="aae88-295">Die `Invoke`-Methode kann zusätzliche Parameter akzeptieren, die durch DI aufgefüllt werden:</span><span class="sxs-lookup"><span data-stu-id="aae88-295">The `Invoke` method can accept additional parameters that are populated by DI:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="aae88-296">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="aae88-296">Additional resources</span></span>

* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

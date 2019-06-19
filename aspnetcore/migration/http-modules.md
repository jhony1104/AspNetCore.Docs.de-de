---
title: Migrieren von HTTP-Handler und Module zu ASP.NET Core-middleware
author: rick-anderson
description: ''
ms.author: tdykstra
ms.date: 12/07/2016
uid: migration/http-modules
ms.openlocfilehash: 84381210910c66a7d121120b8c6b0f046cae8c4f
ms.sourcegitcommit: a1283d486ac1dcedfc7ea302e1cc882833e2c515
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207803"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a><span data-ttu-id="43596-102">Migrieren von HTTP-Handler und Module zu ASP.NET Core-middleware</span><span class="sxs-lookup"><span data-stu-id="43596-102">Migrate HTTP handlers and modules to ASP.NET Core middleware</span></span>

<span data-ttu-id="43596-103">In diesem Artikel zeigt, wie Sie vorhandene ASP.NET migrieren [HTTP-Module und Handler in "System.Webserver"](/iis/configuration/system.webserver/) zu ASP.NET Core [Middleware](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="43596-103">This article shows how to migrate existing ASP.NET [HTTP modules and handlers from system.webserver](/iis/configuration/system.webserver/) to ASP.NET Core [middleware](xref:fundamentals/middleware/index).</span></span>

## <a name="modules-and-handlers-revisited"></a><span data-ttu-id="43596-104">Module und Handler revisited</span><span class="sxs-lookup"><span data-stu-id="43596-104">Modules and handlers revisited</span></span>

<span data-ttu-id="43596-105">Bevor Sie fortfahren, um ASP.NET Core-Middleware, zunächst betrachten wir wie HTTP-Module und Handler verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="43596-105">Before proceeding to ASP.NET Core middleware, let's first recap how HTTP modules and handlers work:</span></span>

![Module-Handler](http-modules/_static/moduleshandlers.png)

<span data-ttu-id="43596-107">**Handler sind:**</span><span class="sxs-lookup"><span data-stu-id="43596-107">**Handlers are:**</span></span>

* <span data-ttu-id="43596-108">Klassen, in denen [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span><span class="sxs-lookup"><span data-stu-id="43596-108">Classes that implement [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span></span>

* <span data-ttu-id="43596-109">Verwendet, um Anforderungen mit einem angegebenen Dateinamen oder die Erweiterung, z. B. behandeln *berichtsserverprojekten*</span><span class="sxs-lookup"><span data-stu-id="43596-109">Used to handle requests with a given file name or extension, such as *.report*</span></span>

* <span data-ttu-id="43596-110">[Konfiguriert](/iis/configuration/system.webserver/handlers/) in *"Web.config"*</span><span class="sxs-lookup"><span data-stu-id="43596-110">[Configured](/iis/configuration/system.webserver/handlers/) in *Web.config*</span></span>

<span data-ttu-id="43596-111">**Module sind:**</span><span class="sxs-lookup"><span data-stu-id="43596-111">**Modules are:**</span></span>

* <span data-ttu-id="43596-112">Klassen, in denen ["IHttpModule"](/dotnet/api/system.web.ihttpmodule)</span><span class="sxs-lookup"><span data-stu-id="43596-112">Classes that implement [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span></span>

* <span data-ttu-id="43596-113">Für jede Anforderung aufgerufen</span><span class="sxs-lookup"><span data-stu-id="43596-113">Invoked for every request</span></span>

* <span data-ttu-id="43596-114">(Weitere Verarbeitung einer Anforderung beenden) kurzschließen können</span><span class="sxs-lookup"><span data-stu-id="43596-114">Able to short-circuit (stop further processing of a request)</span></span>

* <span data-ttu-id="43596-115">Hinzufügen der HTTP-Antwort, oder ihre eigenen erstellen können</span><span class="sxs-lookup"><span data-stu-id="43596-115">Able to add to the HTTP response, or create their own</span></span>

* <span data-ttu-id="43596-116">[Konfiguriert](/iis/configuration/system.webserver/modules/) in *"Web.config"*</span><span class="sxs-lookup"><span data-stu-id="43596-116">[Configured](/iis/configuration/system.webserver/modules/) in *Web.config*</span></span>

<span data-ttu-id="43596-117">**Die Reihenfolge, in der Module eingehenden Anforderungen verarbeiten, wird durch bestimmt:**</span><span class="sxs-lookup"><span data-stu-id="43596-117">**The order in which modules process incoming requests is determined by:**</span></span>

1. <span data-ttu-id="43596-118">Die [Anwendungslebenszyklus](https://msdn.microsoft.com/library/ms227673.aspx), dies ist eine Reihe-Ereignisse, die von ASP.NET ausgelöst: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Jedes Modul kann es sich um einen Handler für ein oder mehrere Ereignisse erstellen.</span><span class="sxs-lookup"><span data-stu-id="43596-118">The [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx), which is a series events fired by ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Each module can create a handler for one or more events.</span></span>

2. <span data-ttu-id="43596-119">Für das gleiche Ereignis, die Reihenfolge, in dem sie in konfiguriert sind *"Web.config"* .</span><span class="sxs-lookup"><span data-stu-id="43596-119">For the same event, the order in which they're configured in *Web.config*.</span></span>

<span data-ttu-id="43596-120">Neben der Module, können Sie Handler für die Lebenszyklus-Ereignisse zum Hinzufügen Ihrer *"Global.asax.cs"* Datei.</span><span class="sxs-lookup"><span data-stu-id="43596-120">In addition to modules, you can add handlers for the life cycle events to your *Global.asax.cs* file.</span></span> <span data-ttu-id="43596-121">Diese Handler führen nach dem Handler in der konfigurierten Module.</span><span class="sxs-lookup"><span data-stu-id="43596-121">These handlers run after the handlers in the configured modules.</span></span>

## <a name="from-handlers-and-modules-to-middleware"></a><span data-ttu-id="43596-122">Vom Handler und Module zu middleware</span><span class="sxs-lookup"><span data-stu-id="43596-122">From handlers and modules to middleware</span></span>

<span data-ttu-id="43596-123">**Middleware sind einfacher als HTTP-Module und Handler:**</span><span class="sxs-lookup"><span data-stu-id="43596-123">**Middleware are simpler than HTTP modules and handlers:**</span></span>

* <span data-ttu-id="43596-124">Module, Handler *"Global.asax.cs"* , *"Web.config"* (mit Ausnahme von IIS-Konfiguration) und der Lebenszyklus der Anwendung nicht mehr vorhanden sind</span><span class="sxs-lookup"><span data-stu-id="43596-124">Modules, handlers, *Global.asax.cs*, *Web.config* (except for IIS configuration) and the application life cycle are gone</span></span>

* <span data-ttu-id="43596-125">Die Rollen von Module und Handler haben von Middleware übernommen wurden</span><span class="sxs-lookup"><span data-stu-id="43596-125">The roles of both modules and handlers have been taken over by middleware</span></span>

* <span data-ttu-id="43596-126">Middleware konfiguriert sind, mithilfe von Code statt im *"Web.config"*</span><span class="sxs-lookup"><span data-stu-id="43596-126">Middleware are configured using code rather than in *Web.config*</span></span>

* <span data-ttu-id="43596-127">[Pipeline Verzweigen](xref:fundamentals/middleware/index#use-run-and-map) Sie Anforderungen an bestimmte Middleware senden, basierend auf nicht nur die URL, sondern auch von Anforderungsheadern, Abfragezeichenfolgen usw. ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="43596-127">[Pipeline branching](xref:fundamentals/middleware/index#use-run-and-map) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

<span data-ttu-id="43596-128">**Middleware sind Module sehr ähnlich:**</span><span class="sxs-lookup"><span data-stu-id="43596-128">**Middleware are very similar to modules:**</span></span>

* <span data-ttu-id="43596-129">Im Prinzip für jede Anforderung aufgerufen</span><span class="sxs-lookup"><span data-stu-id="43596-129">Invoked in principle for every request</span></span>

* <span data-ttu-id="43596-130">Kann eine Anforderung kurzschließen, indem [nicht die Anforderung an die nächste Middleware übergeben](#http-modules-shortcircuiting-middleware)</span><span class="sxs-lookup"><span data-stu-id="43596-130">Able to short-circuit a request, by [not passing the request to the next middleware](#http-modules-shortcircuiting-middleware)</span></span>

* <span data-ttu-id="43596-131">Erstellen Sie ihre eigenen HTTP-Antwort</span><span class="sxs-lookup"><span data-stu-id="43596-131">Able to create their own HTTP response</span></span>

<span data-ttu-id="43596-132">**Middleware und Module werden in einer anderen Reihenfolge verarbeitet:**</span><span class="sxs-lookup"><span data-stu-id="43596-132">**Middleware and modules are processed in a different order:**</span></span>

* <span data-ttu-id="43596-133">Reihenfolge der Middleware basiert auf der Reihenfolge, in der sie in die Anforderungspipeline, eingefügt sind während die Reihenfolge der Module vor allem auf basiert [Anwendungslebenszyklus](https://msdn.microsoft.com/library/ms227673.aspx) Ereignisse</span><span class="sxs-lookup"><span data-stu-id="43596-133">Order of middleware is based on the order in which they're inserted into the request pipeline, while order of modules is mainly based on [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx) events</span></span>

* <span data-ttu-id="43596-134">Reihenfolge der Middleware für Antworten ist das Gegenteil von dem für Anforderungen, während die Reihenfolge der Module, die für Anforderungen und Antworten identisch ist</span><span class="sxs-lookup"><span data-stu-id="43596-134">Order of middleware for responses is the reverse from that for requests, while order of modules is the same for requests and responses</span></span>

* <span data-ttu-id="43596-135">Finden Sie unter [erstellen eine middlewarepipeline mit IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span><span class="sxs-lookup"><span data-stu-id="43596-135">See [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span></span>

![Middleware](http-modules/_static/middleware.png)

<span data-ttu-id="43596-137">Beachten Sie, wie in der Abbildung oben die authentifizierungsmiddleware die Anforderung kurzgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="43596-137">Note how in the image above, the authentication middleware short-circuited the request.</span></span>

## <a name="migrating-module-code-to-middleware"></a><span data-ttu-id="43596-138">Migrieren von Modulcode zu middleware</span><span class="sxs-lookup"><span data-stu-id="43596-138">Migrating module code to middleware</span></span>

<span data-ttu-id="43596-139">Ein vorhandenes HTTP-Modul sieht etwa wie folgt:</span><span class="sxs-lookup"><span data-stu-id="43596-139">An existing HTTP module will look similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

<span data-ttu-id="43596-140">Siehe die [Middleware](xref:fundamentals/middleware/index) Seite eine ASP.NET Core-Middleware ist eine Klasse, die verfügbar gemacht ein `Invoke` Methode aufnehmen ein `HttpContext` und Zurückgeben einer `Task`.</span><span class="sxs-lookup"><span data-stu-id="43596-140">As shown in the [Middleware](xref:fundamentals/middleware/index) page, an ASP.NET Core middleware is a class that exposes an `Invoke` method taking an `HttpContext` and returning a `Task`.</span></span> <span data-ttu-id="43596-141">Ihre neue Middleware wird wie folgt aussehen:</span><span class="sxs-lookup"><span data-stu-id="43596-141">Your new middleware will look like this:</span></span>

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

<span data-ttu-id="43596-142">Die vorherige Middleware Vorlage stammt aus dem Abschnitt für [Schreiben von Middleware](xref:fundamentals/middleware/write).</span><span class="sxs-lookup"><span data-stu-id="43596-142">The preceding middleware template was taken from the section on [writing middleware](xref:fundamentals/middleware/write).</span></span>

<span data-ttu-id="43596-143">Die *MyMiddlewareExtensions* Hilfsklasse erleichtert das Konfigurieren Ihrer Middleware in Ihre `Startup` Klasse.</span><span class="sxs-lookup"><span data-stu-id="43596-143">The *MyMiddlewareExtensions* helper class makes it easier to configure your middleware in your `Startup` class.</span></span> <span data-ttu-id="43596-144">Die `UseMyMiddleware` Methode fügt Ihrer Middleware-Klasse, zu der Anforderungspipeline.</span><span class="sxs-lookup"><span data-stu-id="43596-144">The `UseMyMiddleware` method adds your middleware class to the request pipeline.</span></span> <span data-ttu-id="43596-145">Die Middleware erforderlich sind, erhalten in den Konstruktor der Middleware eingefügt.</span><span class="sxs-lookup"><span data-stu-id="43596-145">Services required by the middleware get injected in the middleware's constructor.</span></span>

<a name="http-modules-shortcircuiting-middleware"></a>

<span data-ttu-id="43596-146">Ihr Modul kann eine Anforderung, z. B. wenn der Benutzer nicht autorisiert ist, beenden:</span><span class="sxs-lookup"><span data-stu-id="43596-146">Your module might terminate a request, for example if the user isn't authorized:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

<span data-ttu-id="43596-147">Eine Middleware verarbeitet dies durch den Aufruf nicht `Invoke` auf die nächste Middleware in der Pipeline.</span><span class="sxs-lookup"><span data-stu-id="43596-147">A middleware handles this by not calling `Invoke` on the next middleware in the pipeline.</span></span> <span data-ttu-id="43596-148">Beachten Sie, dass dadurch die Anforderung, vollständig beendet, nicht behalten Sie, da es sich bei vorherigen Middlewares weiterhin aufgerufen wird, wenn die Antwort die Möglichkeit, über die Pipeline zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="43596-148">Keep in mind that this doesn't fully terminate the request, because previous middlewares will still be invoked when the response makes its way back through the pipeline.</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

<span data-ttu-id="43596-149">Beim Migrieren des Moduls Funktionen zu Ihrer neuen Middleware können möglicherweise den Code Kompilieren nicht, da die `HttpContext` Klasse in ASP.NET Core deutlich geändert hat.</span><span class="sxs-lookup"><span data-stu-id="43596-149">When you migrate your module's functionality to your new middleware, you may find that your code doesn't compile because the `HttpContext` class has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="43596-150">[Später](#migrating-to-the-new-httpcontext), sehen Sie, wie Sie in der neuen ASP.NET Core "HttpContext" migrieren.</span><span class="sxs-lookup"><span data-stu-id="43596-150">[Later on](#migrating-to-the-new-httpcontext), you'll see how to migrate to the new ASP.NET Core HttpContext.</span></span>

## <a name="migrating-module-insertion-into-the-request-pipeline"></a><span data-ttu-id="43596-151">Migrieren von Modul einfügen in die Anforderungspipeline</span><span class="sxs-lookup"><span data-stu-id="43596-151">Migrating module insertion into the request pipeline</span></span>

<span data-ttu-id="43596-152">HTTP-Module werden in der Regel hinzugefügt, um die Anforderungspipeline mithilfe *"Web.config"* :</span><span class="sxs-lookup"><span data-stu-id="43596-152">HTTP modules are typically added to the request pipeline using *Web.config*:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

<span data-ttu-id="43596-153">Konvertieren von [Hinzufügen Ihrer neuen Middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) zu der Anforderungspipeline in Ihre `Startup` Klasse:</span><span class="sxs-lookup"><span data-stu-id="43596-153">Convert this by [adding your new middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) to the request pipeline in your `Startup` class:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

<span data-ttu-id="43596-154">Die genaue Stelle in der Pipeline, in dem Sie Ihre neue Middleware einfügen, hängt das Ereignis, das sie als Modul verarbeitet (`BeginRequest`, `EndRequest`usw.) und die Reihenfolge, in der Liste mit den Modulen in *"Web.config"* .</span><span class="sxs-lookup"><span data-stu-id="43596-154">The exact spot in the pipeline where you insert your new middleware depends on the event that it handled as a module (`BeginRequest`, `EndRequest`, etc.) and its order in your list of modules in *Web.config*.</span></span>

<span data-ttu-id="43596-155">Wie bereits erwähnt, besteht keine Anwendungslebenszyklus in ASP.NET Core und die Reihenfolge, in der Antworten von Middleware verarbeitet werden, die von der Reihenfolge von Modulen verwendeten unterscheidet sich.</span><span class="sxs-lookup"><span data-stu-id="43596-155">As previously stated, there's no application life cycle in ASP.NET Core and the order in which responses are processed by middleware differs from the order used by modules.</span></span> <span data-ttu-id="43596-156">Dadurch kann Ihre Bestellungen Entscheidung schwieriger.</span><span class="sxs-lookup"><span data-stu-id="43596-156">This could make your ordering decision more challenging.</span></span>

<span data-ttu-id="43596-157">Wenn Sortierung ein Problem aufgetreten ist, können Sie Ihr Modul in mehreren middlewarekomponenten aufteilen, die unabhängig voneinander sortiert werden können.</span><span class="sxs-lookup"><span data-stu-id="43596-157">If ordering becomes a problem, you could split your module into multiple middleware components that can be ordered independently.</span></span>

## <a name="migrating-handler-code-to-middleware"></a><span data-ttu-id="43596-158">Migrieren von Handlercode zu middleware</span><span class="sxs-lookup"><span data-stu-id="43596-158">Migrating handler code to middleware</span></span>

<span data-ttu-id="43596-159">Ein HTTP-Handler sieht etwa folgendermaßen aus:</span><span class="sxs-lookup"><span data-stu-id="43596-159">An HTTP handler looks something like this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

<span data-ttu-id="43596-160">Im ASP.NET Core-Projekt würden Sie dies eine Middleware, die etwa wie folgt übersetzt:</span><span class="sxs-lookup"><span data-stu-id="43596-160">In your ASP.NET Core project, you would translate this to a middleware similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

<span data-ttu-id="43596-161">Diese Middleware ist sehr ähnlich ist, an die Middleware für Module.</span><span class="sxs-lookup"><span data-stu-id="43596-161">This middleware is very similar to the middleware corresponding to modules.</span></span> <span data-ttu-id="43596-162">Der einzige wirkliche Unterschied besteht darin, die hier gibt es erfolgt kein Aufruf von `_next.Invoke(context)`.</span><span class="sxs-lookup"><span data-stu-id="43596-162">The only real difference is that here there's no call to `_next.Invoke(context)`.</span></span> <span data-ttu-id="43596-163">Das ist sinnvoll, da der Handler am Ende der Pipeline, ist daher gibt es keine nächste Middleware aufrufen.</span><span class="sxs-lookup"><span data-stu-id="43596-163">That makes sense, because the handler is at the end of the request pipeline, so there will be no next middleware to invoke.</span></span>

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a><span data-ttu-id="43596-164">Migrieren von Handler einfügen in die Anforderungspipeline</span><span class="sxs-lookup"><span data-stu-id="43596-164">Migrating handler insertion into the request pipeline</span></span>

<span data-ttu-id="43596-165">Konfigurieren eines HTTP-Handlers erfolgt in *"Web.config"* und sieht etwa folgendermaßen aus:</span><span class="sxs-lookup"><span data-stu-id="43596-165">Configuring an HTTP handler is done in *Web.config* and looks something like this:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

<span data-ttu-id="43596-166">Sie können dies konvertieren, indem die Anforderungspipeline in Ihrer neuen Handler Middleware hinzugefügt Ihre `Startup` -Klasse, ähnlich wie Middleware, die aus Modulen konvertiert.</span><span class="sxs-lookup"><span data-stu-id="43596-166">You could convert this by adding your new handler middleware to the request pipeline in your `Startup` class, similar to middleware converted from modules.</span></span> <span data-ttu-id="43596-167">Das Problem mit diesem Ansatz ist, dass sie alle Anforderungen an Ihre neuen Handler Middleware senden würde.</span><span class="sxs-lookup"><span data-stu-id="43596-167">The problem with that approach is that it would send all requests to your new handler middleware.</span></span> <span data-ttu-id="43596-168">Sie möchten jedoch nur Anforderungen mit einer bestimmten Erweiterung für Ihre Middleware zu erreichen.</span><span class="sxs-lookup"><span data-stu-id="43596-168">However, you only want requests with a given extension to reach your middleware.</span></span> <span data-ttu-id="43596-169">Die Sie die gleiche Funktionalität erhalten würde, die Sie mit der HTTP-Handler hatten.</span><span class="sxs-lookup"><span data-stu-id="43596-169">That would give you the same functionality you had with your HTTP handler.</span></span>

<span data-ttu-id="43596-170">Eine Lösung besteht darin, Branchen die Pipeline für Anforderungen mit einer angegebenen Erweiterung, mit der `MapWhen` -Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="43596-170">One solution is to branch the pipeline for requests with a given extension, using the `MapWhen` extension method.</span></span> <span data-ttu-id="43596-171">Sie dazu in der gleichen `Configure` Methode, die in dem Sie die andere Middleware hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="43596-171">You do this in the same `Configure` method where you add the other middleware:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

<span data-ttu-id="43596-172">`MapWhen` werden diese Parameter verwendet:</span><span class="sxs-lookup"><span data-stu-id="43596-172">`MapWhen` takes these parameters:</span></span>

1. <span data-ttu-id="43596-173">Ein Lambda-Ausdruck, der akzeptiert die `HttpContext` und gibt `true` , wenn Sie den Branch die Anforderung gesendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="43596-173">A lambda that takes the `HttpContext` and returns `true` if the request should go down the branch.</span></span> <span data-ttu-id="43596-174">Dies bedeutet, dass Sie die Anforderungen nicht nur basierend auf ihrer Erweiterung, sondern auch auf die Anforderungsheader, Abfrageparameter usw. verzweigen können.</span><span class="sxs-lookup"><span data-stu-id="43596-174">This means you can branch requests not just based on their extension, but also on request headers, query string parameters, etc.</span></span>

2. <span data-ttu-id="43596-175">Ein Lambda-Ausdruck, der akzeptiert eine `IApplicationBuilder` und fügt die gesamte Middleware für die Verzweigung.</span><span class="sxs-lookup"><span data-stu-id="43596-175">A lambda that takes an `IApplicationBuilder` and adds all the middleware for the branch.</span></span> <span data-ttu-id="43596-176">Dies bedeutet, dass die Verzweigung vor Ihrer Middleware Handler weiteren Middleware hinzugefügt werden können.</span><span class="sxs-lookup"><span data-stu-id="43596-176">This means you can add additional middleware to the branch in front of your handler middleware.</span></span>

<span data-ttu-id="43596-177">Die Middleware zur Pipeline hinzugefügt werden, bevor der Branch für alle Anforderungen aufgerufen werden soll; der Branch wird keine Auswirkungen darauf haben.</span><span class="sxs-lookup"><span data-stu-id="43596-177">Middleware added to the pipeline before the branch will be invoked on all requests; the branch will have no impact on them.</span></span>

## <a name="loading-middleware-options-using-the-options-pattern"></a><span data-ttu-id="43596-178">Laden die Verwendung des optionsmusters-middlewareoptionen.</span><span class="sxs-lookup"><span data-stu-id="43596-178">Loading middleware options using the options pattern</span></span>

<span data-ttu-id="43596-179">Einige Module und Handler verfügen über Konfigurationsoptionen, die in gespeichert werden *"Web.config"* . In ASP.NET Core ist ein neues Konfigurationsmodell jedoch anstelle des verwendet *"Web.config"* .</span><span class="sxs-lookup"><span data-stu-id="43596-179">Some modules and handlers have configuration options that are stored in *Web.config*. However, in ASP.NET Core a new configuration model is used in place of *Web.config*.</span></span>

<span data-ttu-id="43596-180">Die neue [Konfigurationssystem](xref:fundamentals/configuration/index) bietet Ihnen diese Optionen, um dieses Problem zu beheben:</span><span class="sxs-lookup"><span data-stu-id="43596-180">The new [configuration system](xref:fundamentals/configuration/index) gives you these options to solve this:</span></span>

* <span data-ttu-id="43596-181">Die Optionen an die Middleware, direkt einfügen, siehe die [nächsten Abschnitt](#loading-middleware-options-through-direct-injection).</span><span class="sxs-lookup"><span data-stu-id="43596-181">Directly inject the options into the middleware, as shown in the [next section](#loading-middleware-options-through-direct-injection).</span></span>

* <span data-ttu-id="43596-182">Verwenden der [optionsmuster](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="43596-182">Use the [options pattern](xref:fundamentals/configuration/options):</span></span>

1. <span data-ttu-id="43596-183">Erstellen einer Klasse zum halten Ihre middlewareoptionen, z.B.:</span><span class="sxs-lookup"><span data-stu-id="43596-183">Create a class to hold your middleware options, for example:</span></span>

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. <span data-ttu-id="43596-184">Die Optionswerte Store</span><span class="sxs-lookup"><span data-stu-id="43596-184">Store the option values</span></span>

   <span data-ttu-id="43596-185">Das Konfigurationssystem können Sie Werte an einer beliebigen Stelle zu speichern, die Sie möchten.</span><span class="sxs-lookup"><span data-stu-id="43596-185">The configuration system allows you to store option values anywhere you want.</span></span> <span data-ttu-id="43596-186">Allerdings verwenden die meisten Websites *"appSettings.JSON"* , sodass wir dieses Ansatzes eingehen werde:</span><span class="sxs-lookup"><span data-stu-id="43596-186">However, most sites use *appsettings.json*, so we'll take that approach:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   <span data-ttu-id="43596-187">*MyMiddlewareOptionsSection* hier ist ein Name des Abschnitts.</span><span class="sxs-lookup"><span data-stu-id="43596-187">*MyMiddlewareOptionsSection* here is a section name.</span></span> <span data-ttu-id="43596-188">Sie müssen nicht den Namen der Optionsklasse identisch sein.</span><span class="sxs-lookup"><span data-stu-id="43596-188">It doesn't have to be the same as the name of your options class.</span></span>

3. <span data-ttu-id="43596-189">Ordnen Sie die Werte der Optionsklasse</span><span class="sxs-lookup"><span data-stu-id="43596-189">Associate the option values with the options class</span></span>

    <span data-ttu-id="43596-190">Das optionsmuster stellt anhand des ASP.NET Core Dependency Injection-Framework Optionstyp zuordnen (wie z. B. `MyMiddlewareOptions`) mit einem `MyMiddlewareOptions` Objekt, das die tatsächlichen Optionen verfügt.</span><span class="sxs-lookup"><span data-stu-id="43596-190">The options pattern uses ASP.NET Core's dependency injection framework to associate the options type (such as `MyMiddlewareOptions`) with a `MyMiddlewareOptions` object that has the actual options.</span></span>

    <span data-ttu-id="43596-191">Aktualisieren Ihrer `Startup` Klasse:</span><span class="sxs-lookup"><span data-stu-id="43596-191">Update your `Startup` class:</span></span>

   1. <span data-ttu-id="43596-192">Bei Verwendung von *"appSettings.JSON"* , fügen Sie es an den konfigurationsbuilder in die `Startup` Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="43596-192">If you're using *appsettings.json*, add it to the configuration builder in the `Startup` constructor:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. <span data-ttu-id="43596-193">Konfigurieren Sie den optionendienst an:</span><span class="sxs-lookup"><span data-stu-id="43596-193">Configure the options service:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. <span data-ttu-id="43596-194">Ordnen Sie die Optionen der Options-Klasse:</span><span class="sxs-lookup"><span data-stu-id="43596-194">Associate your options with your options class:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. <span data-ttu-id="43596-195">Fügen Sie die Optionen in Ihrer middlewarekonstruktor.</span><span class="sxs-lookup"><span data-stu-id="43596-195">Inject the options into your middleware constructor.</span></span> <span data-ttu-id="43596-196">Dies entspricht dem injizieren von Optionen in einen Controller.</span><span class="sxs-lookup"><span data-stu-id="43596-196">This is similar to injecting options into a controller.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   <span data-ttu-id="43596-197">Die [UseMiddleware](#http-modules-usemiddleware) Erweiterungsmethode, die Ihre Middleware, fügt die `IApplicationBuilder` übernimmt der Abhängigkeitsinjektion.</span><span class="sxs-lookup"><span data-stu-id="43596-197">The [UseMiddleware](#http-modules-usemiddleware) extension method that adds your middleware to the `IApplicationBuilder` takes care of dependency injection.</span></span>

   <span data-ttu-id="43596-198">Dies ist nicht beschränkt auf `IOptions` Objekte.</span><span class="sxs-lookup"><span data-stu-id="43596-198">This isn't limited to `IOptions` objects.</span></span> <span data-ttu-id="43596-199">Jedes andere Objekt, das Ihrer Middleware ist erforderlich, kann auf diese Weise eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="43596-199">Any other object that your middleware requires can be injected this way.</span></span>

## <a name="loading-middleware-options-through-direct-injection"></a><span data-ttu-id="43596-200">Laden über direkte Injection-middlewareoptionen.</span><span class="sxs-lookup"><span data-stu-id="43596-200">Loading middleware options through direct injection</span></span>

<span data-ttu-id="43596-201">Das optionsmuster hat es sich um den Vorteil, den lose Kopplung von Optionswerten und seinem Consumer erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="43596-201">The options pattern has the advantage that it creates loose coupling between options values and their consumers.</span></span> <span data-ttu-id="43596-202">Sobald Sie eine Options-Klasse die tatsächlichen Optionswerte zugeordnet haben, kann jede beliebige andere Klasse mit den Optionen, über die Dependency Injection-Framework zugreifen.</span><span class="sxs-lookup"><span data-stu-id="43596-202">Once you've associated an options class with the actual options values, any other class can get access to the options through the dependency injection framework.</span></span> <span data-ttu-id="43596-203">Es ist nicht erforderlich, um Optionswerte zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="43596-203">There's no need to pass around options values.</span></span>

<span data-ttu-id="43596-204">Dies wird jedoch, wenn Sie dieselbe Middleware zweimal mit verschiedenen Optionen verwenden möchten.</span><span class="sxs-lookup"><span data-stu-id="43596-204">This breaks down though if you want to use the same middleware twice, with different options.</span></span> <span data-ttu-id="43596-205">Zum Beispiel eine autorisierungsmiddleware in anderen Branches, sodass verschiedene Rollen verwendet.</span><span class="sxs-lookup"><span data-stu-id="43596-205">For example an authorization middleware used in different branches allowing different roles.</span></span> <span data-ttu-id="43596-206">Die für eine Optionsklasse können keine zwei unterschiedliche Optionsobjekte zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="43596-206">You can't associate two different options objects with the one options class.</span></span>

<span data-ttu-id="43596-207">Die Lösung besteht darin, die Optionen Objekte durch die tatsächlichen Optionswerte in Ihrem `Startup` Klasse, und übergeben Sie diese direkt auf jede Instanz Ihrer Middleware.</span><span class="sxs-lookup"><span data-stu-id="43596-207">The solution is to get the options objects with the actual options values in your `Startup` class and pass those directly to each instance of your middleware.</span></span>

1. <span data-ttu-id="43596-208">Fügen Sie einen zweiten Schlüssel um *"appSettings.JSON"*</span><span class="sxs-lookup"><span data-stu-id="43596-208">Add a second key to *appsettings.json*</span></span>

   <span data-ttu-id="43596-209">Um einen zweiten Satz von Optionen zum Hinzufügen der *"appSettings.JSON"* Datei, verwenden Sie einen neuen Schlüssel, um eindeutig zu identifizieren:</span><span class="sxs-lookup"><span data-stu-id="43596-209">To add a second set of options to the *appsettings.json* file, use a new key to uniquely identify it:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. <span data-ttu-id="43596-210">Abrufen von Optionswerten, und diese an die Middleware übergeben.</span><span class="sxs-lookup"><span data-stu-id="43596-210">Retrieve options values and pass them to middleware.</span></span> <span data-ttu-id="43596-211">Die `Use...` Erweiterungsmethode (die Ihre Middleware zur Pipeline hinzugefügt wird) ist ein logischer Ansatzpunkt zum Übergeben der Werte:</span><span class="sxs-lookup"><span data-stu-id="43596-211">The `Use...` extension method (which adds your middleware to the pipeline) is a logical place to pass in the option values:</span></span> 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. <span data-ttu-id="43596-212">Aktiviert die Middleware auszuführende Options-Parameter.</span><span class="sxs-lookup"><span data-stu-id="43596-212">Enable middleware to take an options parameter.</span></span> <span data-ttu-id="43596-213">Stellen Sie eine Überladung von der `Use...` Erweiterungsmethode (, die die Options-Parameter akzeptiert und übergibt es an `UseMiddleware`).</span><span class="sxs-lookup"><span data-stu-id="43596-213">Provide an overload of the `Use...` extension method (that takes the options parameter and passes it to `UseMiddleware`).</span></span> <span data-ttu-id="43596-214">Wenn `UseMiddleware` heißt mit Parametern, die Parameter an Ihre middlewarekonstruktor übergeben beim Instanziieren der Middleware-Objekt.</span><span class="sxs-lookup"><span data-stu-id="43596-214">When `UseMiddleware` is called with parameters, it passes the parameters to your middleware constructor when it instantiates the middleware object.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   <span data-ttu-id="43596-215">Beachten Sie, wie dies in der Options-Objekt dient als Wrapper für ein `OptionsWrapper` Objekt.</span><span class="sxs-lookup"><span data-stu-id="43596-215">Note how this wraps the options object in an `OptionsWrapper` object.</span></span> <span data-ttu-id="43596-216">Dadurch wird implementiert `IOptions`, wie von der middlewarekonstruktor erwartet.</span><span class="sxs-lookup"><span data-stu-id="43596-216">This implements `IOptions`, as expected by the middleware constructor.</span></span>

## <a name="migrating-to-the-new-httpcontext"></a><span data-ttu-id="43596-217">Migration zu neuen "HttpContext"</span><span class="sxs-lookup"><span data-stu-id="43596-217">Migrating to the new HttpContext</span></span>

<span data-ttu-id="43596-218">Sie haben weiter oben gesehen, die die `Invoke` -Methode in Ihrer Middleware nimmt einen Parameter vom Typ `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="43596-218">You saw earlier that the `Invoke` method in your middleware takes a parameter of type `HttpContext`:</span></span>

```csharp
public async Task Invoke(HttpContext context)
```

<span data-ttu-id="43596-219">`HttpContext` in ASP.NET Core wurde erheblich geändert werden.</span><span class="sxs-lookup"><span data-stu-id="43596-219">`HttpContext` has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="43596-220">In diesem Abschnitt wird gezeigt, wie die am häufigsten verwendeten Eigenschaften der übersetzen [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) mit dem neuen `Microsoft.AspNetCore.Http.HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="43596-220">This section shows how to translate the most commonly used properties of [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) to the new `Microsoft.AspNetCore.Http.HttpContext`.</span></span>

### <a name="httpcontext"></a><span data-ttu-id="43596-221">HttpContext</span><span class="sxs-lookup"><span data-stu-id="43596-221">HttpContext</span></span>

<span data-ttu-id="43596-222">**"HttpContext.Items"** übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="43596-222">**HttpContext.Items** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

<span data-ttu-id="43596-223">**Eindeutige Anforderungs-ID (keine Entsprechung System.Web.HttpContext)**</span><span class="sxs-lookup"><span data-stu-id="43596-223">**Unique request ID (no System.Web.HttpContext counterpart)**</span></span>

<span data-ttu-id="43596-224">Erhalten Sie eine eindeutige Id für jede Anforderung.</span><span class="sxs-lookup"><span data-stu-id="43596-224">Gives you a unique id for each request.</span></span> <span data-ttu-id="43596-225">Sehr nützlich in Ihre Protokolle eingeschlossen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="43596-225">Very useful to include in your logs.</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a><span data-ttu-id="43596-226">HttpContext.Request</span><span class="sxs-lookup"><span data-stu-id="43596-226">HttpContext.Request</span></span>

<span data-ttu-id="43596-227">**HttpContext.Request.HttpMethod** übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="43596-227">**HttpContext.Request.HttpMethod** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

<span data-ttu-id="43596-228">**HttpContext.Request.QueryString** translates to:</span><span class="sxs-lookup"><span data-stu-id="43596-228">**HttpContext.Request.QueryString** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

<span data-ttu-id="43596-229">**HttpContext.Request.Url** und **HttpContext.Request.RawUrl** übersetzen in:</span><span class="sxs-lookup"><span data-stu-id="43596-229">**HttpContext.Request.Url** and **HttpContext.Request.RawUrl** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

<span data-ttu-id="43596-230">**HttpContext.Request.IsSecureConnection** translates to:</span><span class="sxs-lookup"><span data-stu-id="43596-230">**HttpContext.Request.IsSecureConnection** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

<span data-ttu-id="43596-231">**HttpContext.Request.UserHostAddress** translates to:</span><span class="sxs-lookup"><span data-stu-id="43596-231">**HttpContext.Request.UserHostAddress** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

<span data-ttu-id="43596-232">**HttpContext.Request.Cookies** translates to:</span><span class="sxs-lookup"><span data-stu-id="43596-232">**HttpContext.Request.Cookies** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

<span data-ttu-id="43596-233">**HttpContext.Request.RequestContext.RouteData** translates to:</span><span class="sxs-lookup"><span data-stu-id="43596-233">**HttpContext.Request.RequestContext.RouteData** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

<span data-ttu-id="43596-234">**HttpContext.Request.Headers** translates to:</span><span class="sxs-lookup"><span data-stu-id="43596-234">**HttpContext.Request.Headers** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

<span data-ttu-id="43596-235">**HttpContext.Request.UserAgent** translates to:</span><span class="sxs-lookup"><span data-stu-id="43596-235">**HttpContext.Request.UserAgent** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

<span data-ttu-id="43596-236">**HttpContext.Request.UrlReferrer** translates to:</span><span class="sxs-lookup"><span data-stu-id="43596-236">**HttpContext.Request.UrlReferrer** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

<span data-ttu-id="43596-237">**HttpContext.Request.ContentType** translates to:</span><span class="sxs-lookup"><span data-stu-id="43596-237">**HttpContext.Request.ContentType** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

<span data-ttu-id="43596-238">**HttpContext.Request.Form** translates to:</span><span class="sxs-lookup"><span data-stu-id="43596-238">**HttpContext.Request.Form** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> <span data-ttu-id="43596-239">Lesen Formularwerte, nur dann, wenn der Inhalt Sub-Typ ist *X-www-form-urlencoded* oder *Formulardaten*.</span><span class="sxs-lookup"><span data-stu-id="43596-239">Read form values only if the content sub type is *x-www-form-urlencoded* or *form-data*.</span></span>

<span data-ttu-id="43596-240">**HttpContext.Request.InputStream** translates to:</span><span class="sxs-lookup"><span data-stu-id="43596-240">**HttpContext.Request.InputStream** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> <span data-ttu-id="43596-241">Verwenden Sie diesen Code nur in einem Handler für Typ-Middleware, am Ende einer Pipeline.</span><span class="sxs-lookup"><span data-stu-id="43596-241">Use this code only in a handler type middleware, at the end of a pipeline.</span></span>
>
><span data-ttu-id="43596-242">Sie können die unformatierten Text lesen, wie oben nur einmal pro Anforderung.</span><span class="sxs-lookup"><span data-stu-id="43596-242">You can read the raw body as shown above only once per request.</span></span> <span data-ttu-id="43596-243">Middleware, die versuchen, die zum Lesen des Texts nach dem ersten Lesen liest aus einem leeren Textkörper.</span><span class="sxs-lookup"><span data-stu-id="43596-243">Middleware trying to read the body after the first read will read an empty body.</span></span>
>
><span data-ttu-id="43596-244">Dies gilt nicht zum Lesen eines Formulars wie zuvor gezeigt, da, die aus einem Puffer ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="43596-244">This doesn't apply to reading a form as shown earlier, because that's done from a buffer.</span></span>

### <a name="httpcontextresponse"></a><span data-ttu-id="43596-245">HttpContext.Response</span><span class="sxs-lookup"><span data-stu-id="43596-245">HttpContext.Response</span></span>

<span data-ttu-id="43596-246">**HttpContext.Response.Status** und **HttpContext.Response.StatusDescription** übersetzen in:</span><span class="sxs-lookup"><span data-stu-id="43596-246">**HttpContext.Response.Status** and **HttpContext.Response.StatusDescription** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

<span data-ttu-id="43596-247">**HttpContext.Response.ContentEncoding** und **HttpContext.Response.ContentType** übersetzen in:</span><span class="sxs-lookup"><span data-stu-id="43596-247">**HttpContext.Response.ContentEncoding** and **HttpContext.Response.ContentType** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

<span data-ttu-id="43596-248">**HttpContext.Response.ContentType** auf eigene auch übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="43596-248">**HttpContext.Response.ContentType** on its own also translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

<span data-ttu-id="43596-249">**HttpContext.Response.Output** übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="43596-249">**HttpContext.Response.Output** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

<span data-ttu-id="43596-250">**HttpContext.Response.TransmitFile**</span><span class="sxs-lookup"><span data-stu-id="43596-250">**HttpContext.Response.TransmitFile**</span></span>

<span data-ttu-id="43596-251">Erstellen einer Datei wird erläutert [hier](../fundamentals/request-features.md#middleware-and-request-features).</span><span class="sxs-lookup"><span data-stu-id="43596-251">Serving up a file is discussed [here](../fundamentals/request-features.md#middleware-and-request-features).</span></span>

<span data-ttu-id="43596-252">**HttpContext.Response.Headers**</span><span class="sxs-lookup"><span data-stu-id="43596-252">**HttpContext.Response.Headers**</span></span>

<span data-ttu-id="43596-253">Senden der Antwortheader wird durch die Tatsache verkompliziert, dass wenn Sie festlegen, nachdem alles in den Antworttext geschrieben wurde, sie wird nicht gesendet.</span><span class="sxs-lookup"><span data-stu-id="43596-253">Sending response headers is complicated by the fact that if you set them after anything has been written to the response body, they will not be sent.</span></span>

<span data-ttu-id="43596-254">Die Lösung besteht darin, eine Callback-Methode festgelegt wird, die vor dem Schreiben auf die Antwort beginnt rechts aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="43596-254">The solution is to set a callback method that will be called right before writing to the response starts.</span></span> <span data-ttu-id="43596-255">Dies erfolgt am besten am Anfang der `Invoke` -Methode in Ihrer Middleware.</span><span class="sxs-lookup"><span data-stu-id="43596-255">This is best done at the start of the `Invoke` method in your middleware.</span></span> <span data-ttu-id="43596-256">Es ist diese Callback-Methode, die Ihre Antwortheader festlegt.</span><span class="sxs-lookup"><span data-stu-id="43596-256">It's this callback method that sets your response headers.</span></span>

<span data-ttu-id="43596-257">Im folgenden Code wird eine Rückrufmethode namens `SetHeaders`:</span><span class="sxs-lookup"><span data-stu-id="43596-257">The following code sets a callback method called `SetHeaders`:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="43596-258">Die `SetHeaders` Callback-Methode sieht wie folgt:</span><span class="sxs-lookup"><span data-stu-id="43596-258">The `SetHeaders` callback method would look like this:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

<span data-ttu-id="43596-259">**HttpContext.Response.Cookies**</span><span class="sxs-lookup"><span data-stu-id="43596-259">**HttpContext.Response.Cookies**</span></span>

<span data-ttu-id="43596-260">Übertragen Sie Cookies im Browser in einer *Set-Cookie* -Antwortheader.</span><span class="sxs-lookup"><span data-stu-id="43596-260">Cookies travel to the browser in a *Set-Cookie* response header.</span></span> <span data-ttu-id="43596-261">Senden Cookies erfordert daher demselben Rückruf verwendet zum Senden der Antwortheader:</span><span class="sxs-lookup"><span data-stu-id="43596-261">As a result, sending cookies requires the same callback as used for sending response headers:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="43596-262">Die `SetCookies` Callback-Methode würde wie folgt aussehen:</span><span class="sxs-lookup"><span data-stu-id="43596-262">The `SetCookies` callback method would look like the following:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a><span data-ttu-id="43596-263">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="43596-263">Additional resources</span></span>

* [<span data-ttu-id="43596-264">HTTP-Handler und HTTP-Module (Übersicht)</span><span class="sxs-lookup"><span data-stu-id="43596-264">HTTP Handlers and HTTP Modules Overview</span></span>](/iis/configuration/system.webserver/)
* [<span data-ttu-id="43596-265">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="43596-265">Configuration</span></span>](xref:fundamentals/configuration/index)
* [<span data-ttu-id="43596-266">Application Startup (Starten von Anwendungen)</span><span class="sxs-lookup"><span data-stu-id="43596-266">Application Startup</span></span>](xref:fundamentals/startup)
* [<span data-ttu-id="43596-267">Middleware</span><span class="sxs-lookup"><span data-stu-id="43596-267">Middleware</span></span>](xref:fundamentals/middleware/index)

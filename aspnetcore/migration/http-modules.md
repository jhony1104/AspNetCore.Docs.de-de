---
title: Migrieren von HTTP-Handler und Module zu ASP.NET Core-middleware
author: rick-anderson
description: ''
ms.author: tdykstra
ms.date: 12/07/2016
uid: migration/http-modules
ms.openlocfilehash: 601b93fb12ab5b37b7d8ad8fd9825accc6e314cd
ms.sourcegitcommit: b3894b65e313570e97a2ab78b8addd22f427cac8
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56743854"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a><span data-ttu-id="efbeb-102">Migrieren von HTTP-Handler und Module zu ASP.NET Core-middleware</span><span class="sxs-lookup"><span data-stu-id="efbeb-102">Migrate HTTP handlers and modules to ASP.NET Core middleware</span></span>

<span data-ttu-id="efbeb-103">Durch [Matt Perdeck](https://www.linkedin.com/in/mattperdeck)</span><span class="sxs-lookup"><span data-stu-id="efbeb-103">By [Matt Perdeck](https://www.linkedin.com/in/mattperdeck)</span></span>

<span data-ttu-id="efbeb-104">In diesem Artikel zeigt, wie Sie vorhandene ASP.NET migrieren [HTTP-Module und Handler in "System.Webserver"](/iis/configuration/system.webserver/) zu ASP.NET Core [Middleware](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="efbeb-104">This article shows how to migrate existing ASP.NET [HTTP modules and handlers from system.webserver](/iis/configuration/system.webserver/) to ASP.NET Core [middleware](xref:fundamentals/middleware/index).</span></span>

## <a name="modules-and-handlers-revisited"></a><span data-ttu-id="efbeb-105">Module und Handler revisited</span><span class="sxs-lookup"><span data-stu-id="efbeb-105">Modules and handlers revisited</span></span>

<span data-ttu-id="efbeb-106">Bevor Sie fortfahren, um ASP.NET Core-Middleware, zunächst betrachten wir wie HTTP-Module und Handler verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="efbeb-106">Before proceeding to ASP.NET Core middleware, let's first recap how HTTP modules and handlers work:</span></span>

![Module-Handler](http-modules/_static/moduleshandlers.png)

<span data-ttu-id="efbeb-108">**Handler sind:**</span><span class="sxs-lookup"><span data-stu-id="efbeb-108">**Handlers are:**</span></span>

   * <span data-ttu-id="efbeb-109">Klassen, in denen [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span><span class="sxs-lookup"><span data-stu-id="efbeb-109">Classes that implement [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span></span>

   * <span data-ttu-id="efbeb-110">Verwendet, um Anforderungen mit einem angegebenen Dateinamen oder die Erweiterung, z. B. behandeln *berichtsserverprojekten*</span><span class="sxs-lookup"><span data-stu-id="efbeb-110">Used to handle requests with a given file name or extension, such as *.report*</span></span>

   * <span data-ttu-id="efbeb-111">[Konfiguriert](/iis/configuration/system.webserver/handlers/) in *"Web.config"*</span><span class="sxs-lookup"><span data-stu-id="efbeb-111">[Configured](/iis/configuration/system.webserver/handlers/) in *Web.config*</span></span>

<span data-ttu-id="efbeb-112">**Module sind:**</span><span class="sxs-lookup"><span data-stu-id="efbeb-112">**Modules are:**</span></span>

   * <span data-ttu-id="efbeb-113">Klassen, in denen ["IHttpModule"](/dotnet/api/system.web.ihttpmodule)</span><span class="sxs-lookup"><span data-stu-id="efbeb-113">Classes that implement [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span></span>

   * <span data-ttu-id="efbeb-114">Für jede Anforderung aufgerufen</span><span class="sxs-lookup"><span data-stu-id="efbeb-114">Invoked for every request</span></span>

   * <span data-ttu-id="efbeb-115">(Weitere Verarbeitung einer Anforderung beenden) kurzschließen können</span><span class="sxs-lookup"><span data-stu-id="efbeb-115">Able to short-circuit (stop further processing of a request)</span></span>

   * <span data-ttu-id="efbeb-116">Hinzufügen der HTTP-Antwort, oder ihre eigenen erstellen können</span><span class="sxs-lookup"><span data-stu-id="efbeb-116">Able to add to the HTTP response, or create their own</span></span>

   * <span data-ttu-id="efbeb-117">[Konfiguriert](/iis/configuration/system.webserver/modules/) in *"Web.config"*</span><span class="sxs-lookup"><span data-stu-id="efbeb-117">[Configured](/iis/configuration/system.webserver/modules/) in *Web.config*</span></span>

<span data-ttu-id="efbeb-118">**Die Reihenfolge, in der Module eingehenden Anforderungen verarbeiten, wird durch bestimmt:**</span><span class="sxs-lookup"><span data-stu-id="efbeb-118">**The order in which modules process incoming requests is determined by:**</span></span>

   1. <span data-ttu-id="efbeb-119">Die [Anwendungslebenszyklus](https://msdn.microsoft.com/library/ms227673.aspx), dies ist eine Reihe-Ereignisse, die von ASP.NET ausgelöst: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Jedes Modul kann es sich um einen Handler für ein oder mehrere Ereignisse erstellen.</span><span class="sxs-lookup"><span data-stu-id="efbeb-119">The [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx), which is a series events fired by ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Each module can create a handler for one or more events.</span></span>

   2. <span data-ttu-id="efbeb-120">Für das gleiche Ereignis, die Reihenfolge, in dem sie in konfiguriert sind *"Web.config"*.</span><span class="sxs-lookup"><span data-stu-id="efbeb-120">For the same event, the order in which they're configured in *Web.config*.</span></span>

<span data-ttu-id="efbeb-121">Neben der Module, können Sie Handler für die Lebenszyklus-Ereignisse zum Hinzufügen Ihrer *"Global.asax.cs"* Datei.</span><span class="sxs-lookup"><span data-stu-id="efbeb-121">In addition to modules, you can add handlers for the life cycle events to your *Global.asax.cs* file.</span></span> <span data-ttu-id="efbeb-122">Diese Handler führen nach dem Handler in der konfigurierten Module.</span><span class="sxs-lookup"><span data-stu-id="efbeb-122">These handlers run after the handlers in the configured modules.</span></span>

## <a name="from-handlers-and-modules-to-middleware"></a><span data-ttu-id="efbeb-123">Vom Handler und Module zu middleware</span><span class="sxs-lookup"><span data-stu-id="efbeb-123">From handlers and modules to middleware</span></span>

<span data-ttu-id="efbeb-124">**Middleware sind einfacher als HTTP-Module und Handler:**</span><span class="sxs-lookup"><span data-stu-id="efbeb-124">**Middleware are simpler than HTTP modules and handlers:**</span></span>

   * <span data-ttu-id="efbeb-125">Module, Handler *"Global.asax.cs"*, *"Web.config"* (mit Ausnahme von IIS-Konfiguration) und der Lebenszyklus der Anwendung nicht mehr vorhanden sind</span><span class="sxs-lookup"><span data-stu-id="efbeb-125">Modules, handlers, *Global.asax.cs*, *Web.config* (except for IIS configuration) and the application life cycle are gone</span></span>

   * <span data-ttu-id="efbeb-126">Die Rollen von Module und Handler haben von Middleware übernommen wurden</span><span class="sxs-lookup"><span data-stu-id="efbeb-126">The roles of both modules and handlers have been taken over by middleware</span></span>

   * <span data-ttu-id="efbeb-127">Middleware konfiguriert sind, mithilfe von Code statt im *"Web.config"*</span><span class="sxs-lookup"><span data-stu-id="efbeb-127">Middleware are configured using code rather than in *Web.config*</span></span>

   * <span data-ttu-id="efbeb-128">[Pipeline Verzweigen](xref:fundamentals/middleware/index#use-run-and-map) Sie Anforderungen an bestimmte Middleware senden, basierend auf nicht nur die URL, sondern auch von Anforderungsheadern, Abfragezeichenfolgen usw. ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="efbeb-128">[Pipeline branching](xref:fundamentals/middleware/index#use-run-and-map) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

<span data-ttu-id="efbeb-129">**Middleware sind Module sehr ähnlich:**</span><span class="sxs-lookup"><span data-stu-id="efbeb-129">**Middleware are very similar to modules:**</span></span>

   * <span data-ttu-id="efbeb-130">Im Prinzip für jede Anforderung aufgerufen</span><span class="sxs-lookup"><span data-stu-id="efbeb-130">Invoked in principle for every request</span></span>

   * <span data-ttu-id="efbeb-131">Kann eine Anforderung kurzschließen, indem [nicht die Anforderung an die nächste Middleware übergeben](#http-modules-shortcircuiting-middleware)</span><span class="sxs-lookup"><span data-stu-id="efbeb-131">Able to short-circuit a request, by [not passing the request to the next middleware](#http-modules-shortcircuiting-middleware)</span></span>

   * <span data-ttu-id="efbeb-132">Erstellen Sie ihre eigenen HTTP-Antwort</span><span class="sxs-lookup"><span data-stu-id="efbeb-132">Able to create their own HTTP response</span></span>

<span data-ttu-id="efbeb-133">**Middleware und Module werden in einer anderen Reihenfolge verarbeitet:**</span><span class="sxs-lookup"><span data-stu-id="efbeb-133">**Middleware and modules are processed in a different order:**</span></span>

   * <span data-ttu-id="efbeb-134">Reihenfolge der Middleware basiert auf der Reihenfolge, in der sie in die Anforderungspipeline, eingefügt sind während die Reihenfolge der Module vor allem auf basiert [Anwendungslebenszyklus](https://msdn.microsoft.com/library/ms227673.aspx) Ereignisse</span><span class="sxs-lookup"><span data-stu-id="efbeb-134">Order of middleware is based on the order in which they're inserted into the request pipeline, while order of modules is mainly based on [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx) events</span></span>

   * <span data-ttu-id="efbeb-135">Reihenfolge der Middleware für Antworten ist das Gegenteil von dem für Anforderungen, während die Reihenfolge der Module, die für Anforderungen und Antworten identisch ist</span><span class="sxs-lookup"><span data-stu-id="efbeb-135">Order of middleware for responses is the reverse from that for requests, while order of modules is the same for requests and responses</span></span>

   * <span data-ttu-id="efbeb-136">Finden Sie unter [erstellen eine middlewarepipeline mit IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span><span class="sxs-lookup"><span data-stu-id="efbeb-136">See [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span></span>

![Middleware](http-modules/_static/middleware.png)

<span data-ttu-id="efbeb-138">Beachten Sie, wie in der Abbildung oben die authentifizierungsmiddleware die Anforderung kurzgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="efbeb-138">Note how in the image above, the authentication middleware short-circuited the request.</span></span>

## <a name="migrating-module-code-to-middleware"></a><span data-ttu-id="efbeb-139">Migrieren von Modulcode zu middleware</span><span class="sxs-lookup"><span data-stu-id="efbeb-139">Migrating module code to middleware</span></span>

<span data-ttu-id="efbeb-140">Ein vorhandenes HTTP-Modul sieht etwa wie folgt:</span><span class="sxs-lookup"><span data-stu-id="efbeb-140">An existing HTTP module will look similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

<span data-ttu-id="efbeb-141">Siehe die [Middleware](xref:fundamentals/middleware/index) Seite eine ASP.NET Core-Middleware ist eine Klasse, die verfügbar gemacht ein `Invoke` Methode aufnehmen ein `HttpContext` und Zurückgeben einer `Task`.</span><span class="sxs-lookup"><span data-stu-id="efbeb-141">As shown in the [Middleware](xref:fundamentals/middleware/index) page, an ASP.NET Core middleware is a class that exposes an `Invoke` method taking an `HttpContext` and returning a `Task`.</span></span> <span data-ttu-id="efbeb-142">Ihre neue Middleware wird wie folgt aussehen:</span><span class="sxs-lookup"><span data-stu-id="efbeb-142">Your new middleware will look like this:</span></span>

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

<span data-ttu-id="efbeb-143">Die vorherige Middleware Vorlage stammt aus dem Abschnitt für [Schreiben von Middleware](xref:fundamentals/middleware/write).</span><span class="sxs-lookup"><span data-stu-id="efbeb-143">The preceding middleware template was taken from the section on [writing middleware](xref:fundamentals/middleware/write).</span></span>

<span data-ttu-id="efbeb-144">Die *MyMiddlewareExtensions* Hilfsklasse erleichtert das Konfigurieren Ihrer Middleware in Ihre `Startup` Klasse.</span><span class="sxs-lookup"><span data-stu-id="efbeb-144">The *MyMiddlewareExtensions* helper class makes it easier to configure your middleware in your `Startup` class.</span></span> <span data-ttu-id="efbeb-145">Die `UseMyMiddleware` Methode fügt Ihrer Middleware-Klasse, zu der Anforderungspipeline.</span><span class="sxs-lookup"><span data-stu-id="efbeb-145">The `UseMyMiddleware` method adds your middleware class to the request pipeline.</span></span> <span data-ttu-id="efbeb-146">Die Middleware erforderlich sind, erhalten in den Konstruktor der Middleware eingefügt.</span><span class="sxs-lookup"><span data-stu-id="efbeb-146">Services required by the middleware get injected in the middleware's constructor.</span></span>

<a name="http-modules-shortcircuiting-middleware"></a>

<span data-ttu-id="efbeb-147">Ihr Modul kann eine Anforderung, z. B. wenn der Benutzer nicht autorisiert ist, beenden:</span><span class="sxs-lookup"><span data-stu-id="efbeb-147">Your module might terminate a request, for example if the user isn't authorized:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

<span data-ttu-id="efbeb-148">Eine Middleware verarbeitet dies durch den Aufruf nicht `Invoke` auf die nächste Middleware in der Pipeline.</span><span class="sxs-lookup"><span data-stu-id="efbeb-148">A middleware handles this by not calling `Invoke` on the next middleware in the pipeline.</span></span> <span data-ttu-id="efbeb-149">Beachten Sie, dass dadurch die Anforderung, vollständig beendet, nicht behalten Sie, da es sich bei vorherigen Middlewares weiterhin aufgerufen wird, wenn die Antwort die Möglichkeit, über die Pipeline zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="efbeb-149">Keep in mind that this doesn't fully terminate the request, because previous middlewares will still be invoked when the response makes its way back through the pipeline.</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

<span data-ttu-id="efbeb-150">Beim Migrieren des Moduls Funktionen zu Ihrer neuen Middleware können möglicherweise den Code Kompilieren nicht, da die `HttpContext` Klasse in ASP.NET Core deutlich geändert hat.</span><span class="sxs-lookup"><span data-stu-id="efbeb-150">When you migrate your module's functionality to your new middleware, you may find that your code doesn't compile because the `HttpContext` class has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="efbeb-151">[Später](#migrating-to-the-new-httpcontext), sehen Sie, wie Sie in der neuen ASP.NET Core "HttpContext" migrieren.</span><span class="sxs-lookup"><span data-stu-id="efbeb-151">[Later on](#migrating-to-the-new-httpcontext), you'll see how to migrate to the new ASP.NET Core HttpContext.</span></span>

## <a name="migrating-module-insertion-into-the-request-pipeline"></a><span data-ttu-id="efbeb-152">Migrieren von Modul einfügen in die Anforderungspipeline</span><span class="sxs-lookup"><span data-stu-id="efbeb-152">Migrating module insertion into the request pipeline</span></span>

<span data-ttu-id="efbeb-153">HTTP-Module werden in der Regel hinzugefügt, um die Anforderungspipeline mithilfe *"Web.config"*:</span><span class="sxs-lookup"><span data-stu-id="efbeb-153">HTTP modules are typically added to the request pipeline using *Web.config*:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

<span data-ttu-id="efbeb-154">Konvertieren von [Hinzufügen Ihrer neuen Middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) zu der Anforderungspipeline in Ihre `Startup` Klasse:</span><span class="sxs-lookup"><span data-stu-id="efbeb-154">Convert this by [adding your new middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) to the request pipeline in your `Startup` class:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

<span data-ttu-id="efbeb-155">Die genaue Stelle in der Pipeline, in dem Sie Ihre neue Middleware einfügen, hängt das Ereignis, das sie als Modul verarbeitet (`BeginRequest`, `EndRequest`usw.) und die Reihenfolge, in der Liste mit den Modulen in *"Web.config"*.</span><span class="sxs-lookup"><span data-stu-id="efbeb-155">The exact spot in the pipeline where you insert your new middleware depends on the event that it handled as a module (`BeginRequest`, `EndRequest`, etc.) and its order in your list of modules in *Web.config*.</span></span>

<span data-ttu-id="efbeb-156">Wie bereits erwähnt, besteht keine Anwendungslebenszyklus in ASP.NET Core und die Reihenfolge, in der Antworten von Middleware verarbeitet werden, die von der Reihenfolge von Modulen verwendeten unterscheidet sich.</span><span class="sxs-lookup"><span data-stu-id="efbeb-156">As previously stated, there's no application life cycle in ASP.NET Core and the order in which responses are processed by middleware differs from the order used by modules.</span></span> <span data-ttu-id="efbeb-157">Dadurch kann Ihre Bestellungen Entscheidung schwieriger.</span><span class="sxs-lookup"><span data-stu-id="efbeb-157">This could make your ordering decision more challenging.</span></span>

<span data-ttu-id="efbeb-158">Wenn Sortierung ein Problem aufgetreten ist, können Sie Ihr Modul in mehreren middlewarekomponenten aufteilen, die unabhängig voneinander sortiert werden können.</span><span class="sxs-lookup"><span data-stu-id="efbeb-158">If ordering becomes a problem, you could split your module into multiple middleware components that can be ordered independently.</span></span>

## <a name="migrating-handler-code-to-middleware"></a><span data-ttu-id="efbeb-159">Migrieren von Handlercode zu middleware</span><span class="sxs-lookup"><span data-stu-id="efbeb-159">Migrating handler code to middleware</span></span>

<span data-ttu-id="efbeb-160">Ein HTTP-Handler sieht etwa folgendermaßen aus:</span><span class="sxs-lookup"><span data-stu-id="efbeb-160">An HTTP handler looks something like this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

<span data-ttu-id="efbeb-161">Im ASP.NET Core-Projekt würden Sie dies eine Middleware, die etwa wie folgt übersetzt:</span><span class="sxs-lookup"><span data-stu-id="efbeb-161">In your ASP.NET Core project, you would translate this to a middleware similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

<span data-ttu-id="efbeb-162">Diese Middleware ist sehr ähnlich ist, an die Middleware für Module.</span><span class="sxs-lookup"><span data-stu-id="efbeb-162">This middleware is very similar to the middleware corresponding to modules.</span></span> <span data-ttu-id="efbeb-163">Der einzige wirkliche Unterschied besteht darin, die hier gibt es erfolgt kein Aufruf von `_next.Invoke(context)`.</span><span class="sxs-lookup"><span data-stu-id="efbeb-163">The only real difference is that here there's no call to `_next.Invoke(context)`.</span></span> <span data-ttu-id="efbeb-164">Das ist sinnvoll, da der Handler am Ende der Pipeline, ist daher gibt es keine nächste Middleware aufrufen.</span><span class="sxs-lookup"><span data-stu-id="efbeb-164">That makes sense, because the handler is at the end of the request pipeline, so there will be no next middleware to invoke.</span></span>

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a><span data-ttu-id="efbeb-165">Migrieren von Handler einfügen in die Anforderungspipeline</span><span class="sxs-lookup"><span data-stu-id="efbeb-165">Migrating handler insertion into the request pipeline</span></span>

<span data-ttu-id="efbeb-166">Konfigurieren eines HTTP-Handlers erfolgt in *"Web.config"* und sieht etwa folgendermaßen aus:</span><span class="sxs-lookup"><span data-stu-id="efbeb-166">Configuring an HTTP handler is done in *Web.config* and looks something like this:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

<span data-ttu-id="efbeb-167">Sie können dies konvertieren, indem die Anforderungspipeline in Ihrer neuen Handler Middleware hinzugefügt Ihre `Startup` -Klasse, ähnlich wie Middleware, die aus Modulen konvertiert.</span><span class="sxs-lookup"><span data-stu-id="efbeb-167">You could convert this by adding your new handler middleware to the request pipeline in your `Startup` class, similar to middleware converted from modules.</span></span> <span data-ttu-id="efbeb-168">Das Problem mit diesem Ansatz ist, dass sie alle Anforderungen an Ihre neuen Handler Middleware senden würde.</span><span class="sxs-lookup"><span data-stu-id="efbeb-168">The problem with that approach is that it would send all requests to your new handler middleware.</span></span> <span data-ttu-id="efbeb-169">Sie möchten jedoch nur Anforderungen mit einer bestimmten Erweiterung für Ihre Middleware zu erreichen.</span><span class="sxs-lookup"><span data-stu-id="efbeb-169">However, you only want requests with a given extension to reach your middleware.</span></span> <span data-ttu-id="efbeb-170">Die Sie die gleiche Funktionalität erhalten würde, die Sie mit der HTTP-Handler hatten.</span><span class="sxs-lookup"><span data-stu-id="efbeb-170">That would give you the same functionality you had with your HTTP handler.</span></span>

<span data-ttu-id="efbeb-171">Eine Lösung besteht darin, Branchen die Pipeline für Anforderungen mit einer angegebenen Erweiterung, mit der `MapWhen` -Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="efbeb-171">One solution is to branch the pipeline for requests with a given extension, using the `MapWhen` extension method.</span></span> <span data-ttu-id="efbeb-172">Sie dazu in der gleichen `Configure` Methode, die in dem Sie die andere Middleware hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="efbeb-172">You do this in the same `Configure` method where you add the other middleware:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

<span data-ttu-id="efbeb-173">`MapWhen` werden diese Parameter verwendet:</span><span class="sxs-lookup"><span data-stu-id="efbeb-173">`MapWhen` takes these parameters:</span></span>

1. <span data-ttu-id="efbeb-174">Ein Lambda-Ausdruck, der akzeptiert die `HttpContext` und gibt `true` , wenn Sie den Branch die Anforderung gesendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="efbeb-174">A lambda that takes the `HttpContext` and returns `true` if the request should go down the branch.</span></span> <span data-ttu-id="efbeb-175">Dies bedeutet, dass Sie die Anforderungen nicht nur basierend auf ihrer Erweiterung, sondern auch auf die Anforderungsheader, Abfrageparameter usw. verzweigen können.</span><span class="sxs-lookup"><span data-stu-id="efbeb-175">This means you can branch requests not just based on their extension, but also on request headers, query string parameters, etc.</span></span>

2. <span data-ttu-id="efbeb-176">Ein Lambda-Ausdruck, der akzeptiert eine `IApplicationBuilder` und fügt die gesamte Middleware für die Verzweigung.</span><span class="sxs-lookup"><span data-stu-id="efbeb-176">A lambda that takes an `IApplicationBuilder` and adds all the middleware for the branch.</span></span> <span data-ttu-id="efbeb-177">Dies bedeutet, dass die Verzweigung vor Ihrer Middleware Handler weiteren Middleware hinzugefügt werden können.</span><span class="sxs-lookup"><span data-stu-id="efbeb-177">This means you can add additional middleware to the branch in front of your handler middleware.</span></span>

<span data-ttu-id="efbeb-178">Die Middleware zur Pipeline hinzugefügt werden, bevor der Branch für alle Anforderungen aufgerufen werden soll; der Branch wird keine Auswirkungen darauf haben.</span><span class="sxs-lookup"><span data-stu-id="efbeb-178">Middleware added to the pipeline before the branch will be invoked on all requests; the branch will have no impact on them.</span></span>

## <a name="loading-middleware-options-using-the-options-pattern"></a><span data-ttu-id="efbeb-179">Laden die Verwendung des optionsmusters-middlewareoptionen.</span><span class="sxs-lookup"><span data-stu-id="efbeb-179">Loading middleware options using the options pattern</span></span>

<span data-ttu-id="efbeb-180">Einige Module und Handler verfügen über Konfigurationsoptionen, die in gespeichert werden *"Web.config"*. In ASP.NET Core ist ein neues Konfigurationsmodell jedoch anstelle des verwendet *"Web.config"*.</span><span class="sxs-lookup"><span data-stu-id="efbeb-180">Some modules and handlers have configuration options that are stored in *Web.config*. However, in ASP.NET Core a new configuration model is used in place of *Web.config*.</span></span>

<span data-ttu-id="efbeb-181">Die neue [Konfigurationssystem](xref:fundamentals/configuration/index) bietet Ihnen diese Optionen, um dieses Problem zu beheben:</span><span class="sxs-lookup"><span data-stu-id="efbeb-181">The new [configuration system](xref:fundamentals/configuration/index) gives you these options to solve this:</span></span>

* <span data-ttu-id="efbeb-182">Die Optionen an die Middleware, direkt einfügen, siehe die [nächsten Abschnitt](#loading-middleware-options-through-direct-injection).</span><span class="sxs-lookup"><span data-stu-id="efbeb-182">Directly inject the options into the middleware, as shown in the [next section](#loading-middleware-options-through-direct-injection).</span></span>

* <span data-ttu-id="efbeb-183">Verwenden der [optionsmuster](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="efbeb-183">Use the [options pattern](xref:fundamentals/configuration/options):</span></span>

1. <span data-ttu-id="efbeb-184">Erstellen einer Klasse zum halten Ihre middlewareoptionen, z.B.:</span><span class="sxs-lookup"><span data-stu-id="efbeb-184">Create a class to hold your middleware options, for example:</span></span>

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. <span data-ttu-id="efbeb-185">Die Optionswerte Store</span><span class="sxs-lookup"><span data-stu-id="efbeb-185">Store the option values</span></span>

   <span data-ttu-id="efbeb-186">Das Konfigurationssystem können Sie Werte an einer beliebigen Stelle zu speichern, die Sie möchten.</span><span class="sxs-lookup"><span data-stu-id="efbeb-186">The configuration system allows you to store option values anywhere you want.</span></span> <span data-ttu-id="efbeb-187">Allerdings verwenden die meisten Websites *"appSettings.JSON"*, sodass wir dieses Ansatzes eingehen werde:</span><span class="sxs-lookup"><span data-stu-id="efbeb-187">However, most sites use *appsettings.json*, so we'll take that approach:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   <span data-ttu-id="efbeb-188">*MyMiddlewareOptionsSection* hier ist ein Name des Abschnitts.</span><span class="sxs-lookup"><span data-stu-id="efbeb-188">*MyMiddlewareOptionsSection* here is a section name.</span></span> <span data-ttu-id="efbeb-189">Sie müssen nicht den Namen der Optionsklasse identisch sein.</span><span class="sxs-lookup"><span data-stu-id="efbeb-189">It doesn't have to be the same as the name of your options class.</span></span>

3. <span data-ttu-id="efbeb-190">Ordnen Sie die Werte der Optionsklasse</span><span class="sxs-lookup"><span data-stu-id="efbeb-190">Associate the option values with the options class</span></span>

    <span data-ttu-id="efbeb-191">Das optionsmuster stellt anhand des ASP.NET Core Dependency Injection-Framework Optionstyp zuordnen (wie z. B. `MyMiddlewareOptions`) mit einem `MyMiddlewareOptions` Objekt, das die tatsächlichen Optionen verfügt.</span><span class="sxs-lookup"><span data-stu-id="efbeb-191">The options pattern uses ASP.NET Core's dependency injection framework to associate the options type (such as `MyMiddlewareOptions`) with a `MyMiddlewareOptions` object that has the actual options.</span></span>

    <span data-ttu-id="efbeb-192">Aktualisieren Ihrer `Startup` Klasse:</span><span class="sxs-lookup"><span data-stu-id="efbeb-192">Update your `Startup` class:</span></span>

   1. <span data-ttu-id="efbeb-193">Bei Verwendung von *"appSettings.JSON"*, fügen Sie es an den konfigurationsbuilder in die `Startup` Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="efbeb-193">If you're using *appsettings.json*, add it to the configuration builder in the `Startup` constructor:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. <span data-ttu-id="efbeb-194">Konfigurieren Sie den optionendienst an:</span><span class="sxs-lookup"><span data-stu-id="efbeb-194">Configure the options service:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. <span data-ttu-id="efbeb-195">Ordnen Sie die Optionen der Options-Klasse:</span><span class="sxs-lookup"><span data-stu-id="efbeb-195">Associate your options with your options class:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. <span data-ttu-id="efbeb-196">Fügen Sie die Optionen in Ihrer middlewarekonstruktor.</span><span class="sxs-lookup"><span data-stu-id="efbeb-196">Inject the options into your middleware constructor.</span></span> <span data-ttu-id="efbeb-197">Dies entspricht dem injizieren von Optionen in einen Controller.</span><span class="sxs-lookup"><span data-stu-id="efbeb-197">This is similar to injecting options into a controller.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   <span data-ttu-id="efbeb-198">Die [UseMiddleware](#http-modules-usemiddleware) Erweiterungsmethode, die Ihre Middleware, fügt die `IApplicationBuilder` übernimmt der Abhängigkeitsinjektion.</span><span class="sxs-lookup"><span data-stu-id="efbeb-198">The [UseMiddleware](#http-modules-usemiddleware) extension method that adds your middleware to the `IApplicationBuilder` takes care of dependency injection.</span></span>

   <span data-ttu-id="efbeb-199">Dies ist nicht beschränkt auf `IOptions` Objekte.</span><span class="sxs-lookup"><span data-stu-id="efbeb-199">This isn't limited to `IOptions` objects.</span></span> <span data-ttu-id="efbeb-200">Jedes andere Objekt, das Ihrer Middleware ist erforderlich, kann auf diese Weise eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="efbeb-200">Any other object that your middleware requires can be injected this way.</span></span>

## <a name="loading-middleware-options-through-direct-injection"></a><span data-ttu-id="efbeb-201">Laden über direkte Injection-middlewareoptionen.</span><span class="sxs-lookup"><span data-stu-id="efbeb-201">Loading middleware options through direct injection</span></span>

<span data-ttu-id="efbeb-202">Das optionsmuster hat es sich um den Vorteil, den lose Kopplung von Optionswerten und seinem Consumer erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="efbeb-202">The options pattern has the advantage that it creates loose coupling between options values and their consumers.</span></span> <span data-ttu-id="efbeb-203">Sobald Sie eine Options-Klasse die tatsächlichen Optionswerte zugeordnet haben, kann jede beliebige andere Klasse mit den Optionen, über die Dependency Injection-Framework zugreifen.</span><span class="sxs-lookup"><span data-stu-id="efbeb-203">Once you've associated an options class with the actual options values, any other class can get access to the options through the dependency injection framework.</span></span> <span data-ttu-id="efbeb-204">Es ist nicht erforderlich, um Optionswerte zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="efbeb-204">There's no need to pass around options values.</span></span>

<span data-ttu-id="efbeb-205">Dies wird jedoch, wenn Sie dieselbe Middleware zweimal mit verschiedenen Optionen verwenden möchten.</span><span class="sxs-lookup"><span data-stu-id="efbeb-205">This breaks down though if you want to use the same middleware twice, with different options.</span></span> <span data-ttu-id="efbeb-206">Zum Beispiel eine autorisierungsmiddleware in anderen Branches, sodass verschiedene Rollen verwendet.</span><span class="sxs-lookup"><span data-stu-id="efbeb-206">For example an authorization middleware used in different branches allowing different roles.</span></span> <span data-ttu-id="efbeb-207">Die für eine Optionsklasse können keine zwei unterschiedliche Optionsobjekte zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="efbeb-207">You can't associate two different options objects with the one options class.</span></span>

<span data-ttu-id="efbeb-208">Die Lösung besteht darin, die Optionen Objekte durch die tatsächlichen Optionswerte in Ihrem `Startup` Klasse, und übergeben Sie diese direkt auf jede Instanz Ihrer Middleware.</span><span class="sxs-lookup"><span data-stu-id="efbeb-208">The solution is to get the options objects with the actual options values in your `Startup` class and pass those directly to each instance of your middleware.</span></span>

1. <span data-ttu-id="efbeb-209">Fügen Sie einen zweiten Schlüssel um *"appSettings.JSON"*</span><span class="sxs-lookup"><span data-stu-id="efbeb-209">Add a second key to *appsettings.json*</span></span>

   <span data-ttu-id="efbeb-210">Um einen zweiten Satz von Optionen zum Hinzufügen der *"appSettings.JSON"* Datei, verwenden Sie einen neuen Schlüssel, um eindeutig zu identifizieren:</span><span class="sxs-lookup"><span data-stu-id="efbeb-210">To add a second set of options to the *appsettings.json* file, use a new key to uniquely identify it:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. <span data-ttu-id="efbeb-211">Abrufen von Optionswerten, und diese an die Middleware übergeben.</span><span class="sxs-lookup"><span data-stu-id="efbeb-211">Retrieve options values and pass them to middleware.</span></span> <span data-ttu-id="efbeb-212">Die `Use...` Erweiterungsmethode (die Ihre Middleware zur Pipeline hinzugefügt wird) ist ein logischer Ansatzpunkt zum Übergeben der Werte:</span><span class="sxs-lookup"><span data-stu-id="efbeb-212">The `Use...` extension method (which adds your middleware to the pipeline) is a logical place to pass in the option values:</span></span> 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. <span data-ttu-id="efbeb-213">Aktiviert die Middleware auszuführende Options-Parameter.</span><span class="sxs-lookup"><span data-stu-id="efbeb-213">Enable middleware to take an options parameter.</span></span> <span data-ttu-id="efbeb-214">Stellen Sie eine Überladung von der `Use...` Erweiterungsmethode (, die die Options-Parameter akzeptiert und übergibt es an `UseMiddleware`).</span><span class="sxs-lookup"><span data-stu-id="efbeb-214">Provide an overload of the `Use...` extension method (that takes the options parameter and passes it to `UseMiddleware`).</span></span> <span data-ttu-id="efbeb-215">Wenn `UseMiddleware` heißt mit Parametern, die Parameter an Ihre middlewarekonstruktor übergeben beim Instanziieren der Middleware-Objekt.</span><span class="sxs-lookup"><span data-stu-id="efbeb-215">When `UseMiddleware` is called with parameters, it passes the parameters to your middleware constructor when it instantiates the middleware object.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   <span data-ttu-id="efbeb-216">Beachten Sie, wie dies in der Options-Objekt dient als Wrapper für ein `OptionsWrapper` Objekt.</span><span class="sxs-lookup"><span data-stu-id="efbeb-216">Note how this wraps the options object in an `OptionsWrapper` object.</span></span> <span data-ttu-id="efbeb-217">Dadurch wird implementiert `IOptions`, wie von der middlewarekonstruktor erwartet.</span><span class="sxs-lookup"><span data-stu-id="efbeb-217">This implements `IOptions`, as expected by the middleware constructor.</span></span>

## <a name="migrating-to-the-new-httpcontext"></a><span data-ttu-id="efbeb-218">Migration zu neuen "HttpContext"</span><span class="sxs-lookup"><span data-stu-id="efbeb-218">Migrating to the new HttpContext</span></span>

<span data-ttu-id="efbeb-219">Sie haben weiter oben gesehen, die die `Invoke` -Methode in Ihrer Middleware nimmt einen Parameter vom Typ `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="efbeb-219">You saw earlier that the `Invoke` method in your middleware takes a parameter of type `HttpContext`:</span></span>

```csharp
public async Task Invoke(HttpContext context)
```

<span data-ttu-id="efbeb-220">`HttpContext` in ASP.NET Core wurde erheblich geändert werden.</span><span class="sxs-lookup"><span data-stu-id="efbeb-220">`HttpContext` has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="efbeb-221">In diesem Abschnitt wird gezeigt, wie die am häufigsten verwendeten Eigenschaften der übersetzen [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) mit dem neuen `Microsoft.AspNetCore.Http.HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="efbeb-221">This section shows how to translate the most commonly used properties of [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) to the new `Microsoft.AspNetCore.Http.HttpContext`.</span></span>

### <a name="httpcontext"></a><span data-ttu-id="efbeb-222">HttpContext</span><span class="sxs-lookup"><span data-stu-id="efbeb-222">HttpContext</span></span>

<span data-ttu-id="efbeb-223">**"HttpContext.Items"** übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="efbeb-223">**HttpContext.Items** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

<span data-ttu-id="efbeb-224">**Eindeutige Anforderungs-ID (keine Entsprechung System.Web.HttpContext)**</span><span class="sxs-lookup"><span data-stu-id="efbeb-224">**Unique request ID (no System.Web.HttpContext counterpart)**</span></span>

<span data-ttu-id="efbeb-225">Erhalten Sie eine eindeutige Id für jede Anforderung.</span><span class="sxs-lookup"><span data-stu-id="efbeb-225">Gives you a unique id for each request.</span></span> <span data-ttu-id="efbeb-226">Sehr nützlich in Ihre Protokolle eingeschlossen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="efbeb-226">Very useful to include in your logs.</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a><span data-ttu-id="efbeb-227">HttpContext.Request</span><span class="sxs-lookup"><span data-stu-id="efbeb-227">HttpContext.Request</span></span>

<span data-ttu-id="efbeb-228">**HttpContext.Request.HttpMethod** übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="efbeb-228">**HttpContext.Request.HttpMethod** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

<span data-ttu-id="efbeb-229">**HttpContext.Request.QueryString** translates to:</span><span class="sxs-lookup"><span data-stu-id="efbeb-229">**HttpContext.Request.QueryString** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

<span data-ttu-id="efbeb-230">**HttpContext.Request.Url** und **HttpContext.Request.RawUrl** übersetzen in:</span><span class="sxs-lookup"><span data-stu-id="efbeb-230">**HttpContext.Request.Url** and **HttpContext.Request.RawUrl** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

<span data-ttu-id="efbeb-231">**HttpContext.Request.IsSecureConnection** translates to:</span><span class="sxs-lookup"><span data-stu-id="efbeb-231">**HttpContext.Request.IsSecureConnection** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

<span data-ttu-id="efbeb-232">**HttpContext.Request.UserHostAddress** translates to:</span><span class="sxs-lookup"><span data-stu-id="efbeb-232">**HttpContext.Request.UserHostAddress** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

<span data-ttu-id="efbeb-233">**HttpContext.Request.Cookies** translates to:</span><span class="sxs-lookup"><span data-stu-id="efbeb-233">**HttpContext.Request.Cookies** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

<span data-ttu-id="efbeb-234">**HttpContext.Request.RequestContext.RouteData** translates to:</span><span class="sxs-lookup"><span data-stu-id="efbeb-234">**HttpContext.Request.RequestContext.RouteData** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

<span data-ttu-id="efbeb-235">**HttpContext.Request.Headers** translates to:</span><span class="sxs-lookup"><span data-stu-id="efbeb-235">**HttpContext.Request.Headers** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

<span data-ttu-id="efbeb-236">**HttpContext.Request.UserAgent** translates to:</span><span class="sxs-lookup"><span data-stu-id="efbeb-236">**HttpContext.Request.UserAgent** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

<span data-ttu-id="efbeb-237">**HttpContext.Request.UrlReferrer** translates to:</span><span class="sxs-lookup"><span data-stu-id="efbeb-237">**HttpContext.Request.UrlReferrer** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

<span data-ttu-id="efbeb-238">**HttpContext.Request.ContentType** translates to:</span><span class="sxs-lookup"><span data-stu-id="efbeb-238">**HttpContext.Request.ContentType** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

<span data-ttu-id="efbeb-239">**HttpContext.Request.Form** translates to:</span><span class="sxs-lookup"><span data-stu-id="efbeb-239">**HttpContext.Request.Form** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> <span data-ttu-id="efbeb-240">Lesen Formularwerte, nur dann, wenn der Inhalt Sub-Typ ist *X-www-form-urlencoded* oder *Formulardaten*.</span><span class="sxs-lookup"><span data-stu-id="efbeb-240">Read form values only if the content sub type is *x-www-form-urlencoded* or *form-data*.</span></span>

<span data-ttu-id="efbeb-241">**HttpContext.Request.InputStream** translates to:</span><span class="sxs-lookup"><span data-stu-id="efbeb-241">**HttpContext.Request.InputStream** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> <span data-ttu-id="efbeb-242">Verwenden Sie diesen Code nur in einem Handler für Typ-Middleware, am Ende einer Pipeline.</span><span class="sxs-lookup"><span data-stu-id="efbeb-242">Use this code only in a handler type middleware, at the end of a pipeline.</span></span>
>
><span data-ttu-id="efbeb-243">Sie können die unformatierten Text lesen, wie oben nur einmal pro Anforderung.</span><span class="sxs-lookup"><span data-stu-id="efbeb-243">You can read the raw body as shown above only once per request.</span></span> <span data-ttu-id="efbeb-244">Middleware, die versuchen, die zum Lesen des Texts nach dem ersten Lesen liest aus einem leeren Textkörper.</span><span class="sxs-lookup"><span data-stu-id="efbeb-244">Middleware trying to read the body after the first read will read an empty body.</span></span>
>
><span data-ttu-id="efbeb-245">Dies gilt nicht zum Lesen eines Formulars wie zuvor gezeigt, da, die aus einem Puffer ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="efbeb-245">This doesn't apply to reading a form as shown earlier, because that's done from a buffer.</span></span>

### <a name="httpcontextresponse"></a><span data-ttu-id="efbeb-246">HttpContext.Response</span><span class="sxs-lookup"><span data-stu-id="efbeb-246">HttpContext.Response</span></span>

<span data-ttu-id="efbeb-247">**HttpContext.Response.Status** und **HttpContext.Response.StatusDescription** übersetzen in:</span><span class="sxs-lookup"><span data-stu-id="efbeb-247">**HttpContext.Response.Status** and **HttpContext.Response.StatusDescription** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

<span data-ttu-id="efbeb-248">**HttpContext.Response.ContentEncoding** und **HttpContext.Response.ContentType** übersetzen in:</span><span class="sxs-lookup"><span data-stu-id="efbeb-248">**HttpContext.Response.ContentEncoding** and **HttpContext.Response.ContentType** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

<span data-ttu-id="efbeb-249">**HttpContext.Response.ContentType** auf eigene auch übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="efbeb-249">**HttpContext.Response.ContentType** on its own also translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

<span data-ttu-id="efbeb-250">**HttpContext.Response.Output** übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="efbeb-250">**HttpContext.Response.Output** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

<span data-ttu-id="efbeb-251">**HttpContext.Response.TransmitFile**</span><span class="sxs-lookup"><span data-stu-id="efbeb-251">**HttpContext.Response.TransmitFile**</span></span>

<span data-ttu-id="efbeb-252">Erstellen einer Datei wird erläutert [hier](../fundamentals/request-features.md#middleware-and-request-features).</span><span class="sxs-lookup"><span data-stu-id="efbeb-252">Serving up a file is discussed [here](../fundamentals/request-features.md#middleware-and-request-features).</span></span>

<span data-ttu-id="efbeb-253">**HttpContext.Response.Headers**</span><span class="sxs-lookup"><span data-stu-id="efbeb-253">**HttpContext.Response.Headers**</span></span>

<span data-ttu-id="efbeb-254">Senden der Antwortheader wird durch die Tatsache verkompliziert, dass wenn Sie festlegen, nachdem alles in den Antworttext geschrieben wurde, sie wird nicht gesendet.</span><span class="sxs-lookup"><span data-stu-id="efbeb-254">Sending response headers is complicated by the fact that if you set them after anything has been written to the response body, they will not be sent.</span></span>

<span data-ttu-id="efbeb-255">Die Lösung besteht darin, eine Callback-Methode festgelegt wird, die vor dem Schreiben auf die Antwort beginnt rechts aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="efbeb-255">The solution is to set a callback method that will be called right before writing to the response starts.</span></span> <span data-ttu-id="efbeb-256">Dies erfolgt am besten am Anfang der `Invoke` -Methode in Ihrer Middleware.</span><span class="sxs-lookup"><span data-stu-id="efbeb-256">This is best done at the start of the `Invoke` method in your middleware.</span></span> <span data-ttu-id="efbeb-257">Es ist diese Callback-Methode, die Ihre Antwortheader festlegt.</span><span class="sxs-lookup"><span data-stu-id="efbeb-257">It's this callback method that sets your response headers.</span></span>

<span data-ttu-id="efbeb-258">Im folgenden Code wird eine Rückrufmethode namens `SetHeaders`:</span><span class="sxs-lookup"><span data-stu-id="efbeb-258">The following code sets a callback method called `SetHeaders`:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="efbeb-259">Die `SetHeaders` Callback-Methode sieht wie folgt:</span><span class="sxs-lookup"><span data-stu-id="efbeb-259">The `SetHeaders` callback method would look like this:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

<span data-ttu-id="efbeb-260">**HttpContext.Response.Cookies**</span><span class="sxs-lookup"><span data-stu-id="efbeb-260">**HttpContext.Response.Cookies**</span></span>

<span data-ttu-id="efbeb-261">Übertragen Sie Cookies im Browser in einer *Set-Cookie* -Antwortheader.</span><span class="sxs-lookup"><span data-stu-id="efbeb-261">Cookies travel to the browser in a *Set-Cookie* response header.</span></span> <span data-ttu-id="efbeb-262">Senden Cookies erfordert daher demselben Rückruf verwendet zum Senden der Antwortheader:</span><span class="sxs-lookup"><span data-stu-id="efbeb-262">As a result, sending cookies requires the same callback as used for sending response headers:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="efbeb-263">Die `SetCookies` Callback-Methode würde wie folgt aussehen:</span><span class="sxs-lookup"><span data-stu-id="efbeb-263">The `SetCookies` callback method would look like the following:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a><span data-ttu-id="efbeb-264">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="efbeb-264">Additional resources</span></span>

* [<span data-ttu-id="efbeb-265">HTTP-Handler und HTTP-Module (Übersicht)</span><span class="sxs-lookup"><span data-stu-id="efbeb-265">HTTP Handlers and HTTP Modules Overview</span></span>](/iis/configuration/system.webserver/)
* [<span data-ttu-id="efbeb-266">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="efbeb-266">Configuration</span></span>](xref:fundamentals/configuration/index)
* [<span data-ttu-id="efbeb-267">Application Startup (Starten von Anwendungen)</span><span class="sxs-lookup"><span data-stu-id="efbeb-267">Application Startup</span></span>](xref:fundamentals/startup)
* [<span data-ttu-id="efbeb-268">Middleware</span><span class="sxs-lookup"><span data-stu-id="efbeb-268">Middleware</span></span>](xref:fundamentals/middleware/index)

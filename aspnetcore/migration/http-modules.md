---
title: Migrieren von HTTP-Handlern und Modulen zu ASP.net Core Middleware
author: rick-anderson
description: ''
ms.author: riande
ms.date: 12/07/2016
uid: migration/http-modules
ms.openlocfilehash: bdf27ccb742d4bc05bac71e6c96d71c38dcb4b62
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975493"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a><span data-ttu-id="ff6f8-102">Migrieren von HTTP-Handlern und Modulen zu ASP.net Core Middleware</span><span class="sxs-lookup"><span data-stu-id="ff6f8-102">Migrate HTTP handlers and modules to ASP.NET Core middleware</span></span>

<span data-ttu-id="ff6f8-103">In diesem Artikel wird gezeigt, wie vorhandene ASP.net [-HTTP-Module und-Handler von System. Webserver](/iis/configuration/system.webserver/) zu ASP.net Core [Middleware](xref:fundamentals/middleware/index)migriert werden.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-103">This article shows how to migrate existing ASP.NET [HTTP modules and handlers from system.webserver](/iis/configuration/system.webserver/) to ASP.NET Core [middleware](xref:fundamentals/middleware/index).</span></span>

## <a name="modules-and-handlers-revisited"></a><span data-ttu-id="ff6f8-104">Wieder besuchte Module und Handler</span><span class="sxs-lookup"><span data-stu-id="ff6f8-104">Modules and handlers revisited</span></span>

<span data-ttu-id="ff6f8-105">Bevor Sie mit der ASP.net Core Middleware fortfahren, können Sie zuerst die Funktionsweise von HTTP-Modulen und-Handlern fortsetzen:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-105">Before proceeding to ASP.NET Core middleware, let's first recap how HTTP modules and handlers work:</span></span>

![Module-Handler](http-modules/_static/moduleshandlers.png)

<span data-ttu-id="ff6f8-107">**Handler sind:**</span><span class="sxs-lookup"><span data-stu-id="ff6f8-107">**Handlers are:**</span></span>

* <span data-ttu-id="ff6f8-108">Klassen, die [IHttpHandler](/dotnet/api/system.web.ihttphandler) implementieren</span><span class="sxs-lookup"><span data-stu-id="ff6f8-108">Classes that implement [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span></span>

* <span data-ttu-id="ff6f8-109">Wird zum Verarbeiten von Anforderungen mit einem angegebenen Dateinamen oder einer Erweiterung verwendet, z *. b. Report*</span><span class="sxs-lookup"><span data-stu-id="ff6f8-109">Used to handle requests with a given file name or extension, such as *.report*</span></span>

* <span data-ttu-id="ff6f8-110">In " *Web. config* " [konfiguriert](/iis/configuration/system.webserver/handlers/)</span><span class="sxs-lookup"><span data-stu-id="ff6f8-110">[Configured](/iis/configuration/system.webserver/handlers/) in *Web.config*</span></span>

<span data-ttu-id="ff6f8-111">**Module:**</span><span class="sxs-lookup"><span data-stu-id="ff6f8-111">**Modules are:**</span></span>

* <span data-ttu-id="ff6f8-112">Klassen, die [IHttpModule](/dotnet/api/system.web.ihttpmodule) implementieren</span><span class="sxs-lookup"><span data-stu-id="ff6f8-112">Classes that implement [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span></span>

* <span data-ttu-id="ff6f8-113">Wird für jede Anforderung aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-113">Invoked for every request</span></span>

* <span data-ttu-id="ff6f8-114">Kurzschluss (weitere Verarbeitung einer Anforderung wird beendet)</span><span class="sxs-lookup"><span data-stu-id="ff6f8-114">Able to short-circuit (stop further processing of a request)</span></span>

* <span data-ttu-id="ff6f8-115">Der HTTP-Antwort kann hinzugefügt werden, oder es kann ein eigener erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-115">Able to add to the HTTP response, or create their own</span></span>

* <span data-ttu-id="ff6f8-116">In " *Web. config* " [konfiguriert](/iis/configuration/system.webserver/modules/)</span><span class="sxs-lookup"><span data-stu-id="ff6f8-116">[Configured](/iis/configuration/system.webserver/modules/) in *Web.config*</span></span>

<span data-ttu-id="ff6f8-117">**Die Reihenfolge, in der Module eingehende Anforderungen verarbeiten, wird durch Folgendes bestimmt:**</span><span class="sxs-lookup"><span data-stu-id="ff6f8-117">**The order in which modules process incoming requests is determined by:**</span></span>

1. <span data-ttu-id="ff6f8-118">Der [Lebenszyklus der Anwendung](https://msdn.microsoft.com/library/ms227673.aspx), bei dem es sich um eine Reihe von ASP.net handelt: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest)usw. Jedes Modul kann einen Handler für ein oder mehrere Ereignisse erstellen.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-118">The [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx), which is a series events fired by ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Each module can create a handler for one or more events.</span></span>

2. <span data-ttu-id="ff6f8-119">Für dasselbe Ereignis die Reihenfolge, in der Sie in der Datei " *Web. config*" konfiguriert sind.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-119">For the same event, the order in which they're configured in *Web.config*.</span></span>

<span data-ttu-id="ff6f8-120">Zusätzlich zu Modulen können Sie Handler für die Lebenszyklus Ereignisse ihrer *Global.asax.cs* -Datei hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-120">In addition to modules, you can add handlers for the life cycle events to your *Global.asax.cs* file.</span></span> <span data-ttu-id="ff6f8-121">Diese Handler werden nach den Handlern in den konfigurierten Modulen ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-121">These handlers run after the handlers in the configured modules.</span></span>

## <a name="from-handlers-and-modules-to-middleware"></a><span data-ttu-id="ff6f8-122">Von Handlern und Modulen bis Middleware</span><span class="sxs-lookup"><span data-stu-id="ff6f8-122">From handlers and modules to middleware</span></span>

<span data-ttu-id="ff6f8-123">**Middleware ist einfacher als HTTP-Module und-Handler:**</span><span class="sxs-lookup"><span data-stu-id="ff6f8-123">**Middleware are simpler than HTTP modules and handlers:**</span></span>

* <span data-ttu-id="ff6f8-124">Module, Handler, *Global.asax.cs*, *Web. config* (außer IIS-Konfiguration) und der Lebenszyklus der Anwendung sind nicht mehr vorhanden.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-124">Modules, handlers, *Global.asax.cs*, *Web.config* (except for IIS configuration) and the application life cycle are gone</span></span>

* <span data-ttu-id="ff6f8-125">Die Rollen von Modulen und Handlern wurden von Middleware übernommen.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-125">The roles of both modules and handlers have been taken over by middleware</span></span>

* <span data-ttu-id="ff6f8-126">Die Middleware wird mithilfe von Code anstelle von *Web. config konfiguriert.*</span><span class="sxs-lookup"><span data-stu-id="ff6f8-126">Middleware are configured using code rather than in *Web.config*</span></span>

* <span data-ttu-id="ff6f8-127">Mithilfe der [Pipeline Verzweigung](xref:fundamentals/middleware/index#use-run-and-map) können Sie Anforderungen an bestimmte Middleware senden. Dies basiert nicht nur auf der URL, sondern auch auf Anforderungs Headern, Abfrage Zeichenfolgen usw.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-127">[Pipeline branching](xref:fundamentals/middleware/index#use-run-and-map) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

<span data-ttu-id="ff6f8-128">**Middleware ähnelt Modulen stark:**</span><span class="sxs-lookup"><span data-stu-id="ff6f8-128">**Middleware are very similar to modules:**</span></span>

* <span data-ttu-id="ff6f8-129">Wird im Prinzip für jede Anforderung aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-129">Invoked in principle for every request</span></span>

* <span data-ttu-id="ff6f8-130">Eine Anforderung kann kurz gebunden werden, da [die Anforderung nicht an die nächste Middleware übergeben](#http-modules-shortcircuiting-middleware) wird.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-130">Able to short-circuit a request, by [not passing the request to the next middleware](#http-modules-shortcircuiting-middleware)</span></span>

* <span data-ttu-id="ff6f8-131">Ihre eigene HTTP-Antwort kann erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-131">Able to create their own HTTP response</span></span>

<span data-ttu-id="ff6f8-132">**Middleware und Module werden in einer anderen Reihenfolge verarbeitet:**</span><span class="sxs-lookup"><span data-stu-id="ff6f8-132">**Middleware and modules are processed in a different order:**</span></span>

* <span data-ttu-id="ff6f8-133">Die Reihenfolge der Middleware basiert auf der Reihenfolge, in der Sie in die Anforderungs Pipeline eingefügt werden, während die Reihenfolge der Module hauptsächlich auf [Anwendungslebenszyklus](https://msdn.microsoft.com/library/ms227673.aspx) -Ereignissen basiert.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-133">Order of middleware is based on the order in which they're inserted into the request pipeline, while order of modules is mainly based on [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx) events</span></span>

* <span data-ttu-id="ff6f8-134">Die Reihenfolge der Middleware für Antworten ist das Gegenteil bei Anforderungen, während die Reihenfolge der Module für Anforderungen und Antworten gleich ist.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-134">Order of middleware for responses is the reverse from that for requests, while order of modules is the same for requests and responses</span></span>

* <span data-ttu-id="ff6f8-135">Weitere Informationen finden Sie [unter Erstellen einer middlewarepipeline mit iapplicationbuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span><span class="sxs-lookup"><span data-stu-id="ff6f8-135">See [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span></span>

![Middleware](http-modules/_static/middleware.png)

<span data-ttu-id="ff6f8-137">Beachten Sie, dass in der obigen Abbildung die Authentifizierungs Middleware die Anforderung kurzgeschlossen hat.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-137">Note how in the image above, the authentication middleware short-circuited the request.</span></span>

## <a name="migrating-module-code-to-middleware"></a><span data-ttu-id="ff6f8-138">Migrieren von Modulcode zu Middleware</span><span class="sxs-lookup"><span data-stu-id="ff6f8-138">Migrating module code to middleware</span></span>

<span data-ttu-id="ff6f8-139">Ein vorhandenes HTTP-Modul sieht in etwa wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-139">An existing HTTP module will look similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

<span data-ttu-id="ff6f8-140">Wie auf der Seite [Middleware](xref:fundamentals/middleware/index) gezeigt, ist eine ASP.net Core Middleware eine `Invoke` `HttpContext` -Klasse, die eine-Methode verfügbar macht, `Task`die eine-Klasse und eine zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-140">As shown in the [Middleware](xref:fundamentals/middleware/index) page, an ASP.NET Core middleware is a class that exposes an `Invoke` method taking an `HttpContext` and returning a `Task`.</span></span> <span data-ttu-id="ff6f8-141">Die neue Middleware sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-141">Your new middleware will look like this:</span></span>

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

<span data-ttu-id="ff6f8-142">Die vorangehende Middleware-Vorlage stammt aus dem Abschnitt zum [Schreiben von Middleware](xref:fundamentals/middleware/write).</span><span class="sxs-lookup"><span data-stu-id="ff6f8-142">The preceding middleware template was taken from the section on [writing middleware](xref:fundamentals/middleware/write).</span></span>

<span data-ttu-id="ff6f8-143">Die Hilfsklasse *mymiddlewareextensions* vereinfacht das Konfigurieren ihrer Middleware in der `Startup` Klasse.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-143">The *MyMiddlewareExtensions* helper class makes it easier to configure your middleware in your `Startup` class.</span></span> <span data-ttu-id="ff6f8-144">Die `UseMyMiddleware` -Methode fügt der Anforderungs Pipeline die Middleware-Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-144">The `UseMyMiddleware` method adds your middleware class to the request pipeline.</span></span> <span data-ttu-id="ff6f8-145">Die von der Middleware benötigten Dienste werden in den Konstruktor der Middleware eingefügt.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-145">Services required by the middleware get injected in the middleware's constructor.</span></span>

<a name="http-modules-shortcircuiting-middleware"></a>

<span data-ttu-id="ff6f8-146">Das Modul kann eine Anforderung beenden, z. b. wenn der Benutzer nicht autorisiert ist:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-146">Your module might terminate a request, for example if the user isn't authorized:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

<span data-ttu-id="ff6f8-147">Eine Middleware verarbeitet dies, indem nicht `Invoke` für die nächste Middleware in der Pipeline aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-147">A middleware handles this by not calling `Invoke` on the next middleware in the pipeline.</span></span> <span data-ttu-id="ff6f8-148">Beachten Sie, dass dies die Anforderung nicht vollständig beendet, da vorherige Middlewares weiterhin aufgerufen werden, wenn die Antwort durch die Pipeline wieder zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-148">Keep in mind that this doesn't fully terminate the request, because previous middlewares will still be invoked when the response makes its way back through the pipeline.</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

<span data-ttu-id="ff6f8-149">Wenn Sie die Funktionalität Ihres Moduls zu ihrer neuen Middleware migrieren, stellen Sie möglicherweise fest, dass der Code `HttpContext` nicht kompiliert wird, da die Klasse in ASP.net Core erheblich geändert wurde.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-149">When you migrate your module's functionality to your new middleware, you may find that your code doesn't compile because the `HttpContext` class has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="ff6f8-150">[Später](#migrating-to-the-new-httpcontext)erfahren Sie, wie Sie zum neuen ASP.net Core HttpContext migrieren.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-150">[Later on](#migrating-to-the-new-httpcontext), you'll see how to migrate to the new ASP.NET Core HttpContext.</span></span>

## <a name="migrating-module-insertion-into-the-request-pipeline"></a><span data-ttu-id="ff6f8-151">Migrieren des Moduls in die Anforderungs Pipeline</span><span class="sxs-lookup"><span data-stu-id="ff6f8-151">Migrating module insertion into the request pipeline</span></span>

<span data-ttu-id="ff6f8-152">HTTP-Module werden in der Regel mithilfe von " *Web. config*" der Anforderungs Pipeline hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-152">HTTP modules are typically added to the request pipeline using *Web.config*:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

<span data-ttu-id="ff6f8-153">Konvertieren Sie diese, indem [Sie die neue Middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) der Anforderungs Pipeline in `Startup` ihrer Klasse hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-153">Convert this by [adding your new middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) to the request pipeline in your `Startup` class:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

<span data-ttu-id="ff6f8-154">Die genaue Stelle in der Pipeline, in der Sie die neue Middleware einfügen, hängt von dem Ereignis ab, das in`BeginRequest`der `EndRequest`Liste der Module in der *Web. config*-Datei behandelt wurde (, usw.).</span><span class="sxs-lookup"><span data-stu-id="ff6f8-154">The exact spot in the pipeline where you insert your new middleware depends on the event that it handled as a module (`BeginRequest`, `EndRequest`, etc.) and its order in your list of modules in *Web.config*.</span></span>

<span data-ttu-id="ff6f8-155">Wie bereits erwähnt, gibt es keinen Anwendungslebenszyklus in ASP.net Core und die Reihenfolge, in der die Antworten von der Middleware verarbeitet werden, unterscheidet sich von der von Modulen verwendeten Reihenfolge.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-155">As previously stated, there's no application life cycle in ASP.NET Core and the order in which responses are processed by middleware differs from the order used by modules.</span></span> <span data-ttu-id="ff6f8-156">Dies könnte dazu führen, dass Ihre Bestell Entscheidung schwieriger wird.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-156">This could make your ordering decision more challenging.</span></span>

<span data-ttu-id="ff6f8-157">Wenn die Reihenfolge zu einem Problem wird, können Sie das Modul in mehrere middlewarekomponenten aufteilen, die unabhängig voneinander angeordnet werden können.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-157">If ordering becomes a problem, you could split your module into multiple middleware components that can be ordered independently.</span></span>

## <a name="migrating-handler-code-to-middleware"></a><span data-ttu-id="ff6f8-158">Migrieren von Handlercode zu Middleware</span><span class="sxs-lookup"><span data-stu-id="ff6f8-158">Migrating handler code to middleware</span></span>

<span data-ttu-id="ff6f8-159">Ein HTTP-Handler sieht in etwa wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-159">An HTTP handler looks something like this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

<span data-ttu-id="ff6f8-160">In Ihrem ASP.net Core Projekt würden Sie dies in eine Middleware übersetzen, die in etwa wie folgt aussieht:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-160">In your ASP.NET Core project, you would translate this to a middleware similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

<span data-ttu-id="ff6f8-161">Diese Middleware ähnelt der Middleware, die den Modulen entspricht.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-161">This middleware is very similar to the middleware corresponding to modules.</span></span> <span data-ttu-id="ff6f8-162">Der einzige wirkliche Unterschied besteht darin, dass hier kein- `_next.Invoke(context)`Rückruf erfolgt.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-162">The only real difference is that here there's no call to `_next.Invoke(context)`.</span></span> <span data-ttu-id="ff6f8-163">Das ist sinnvoll, da sich der Handler am Ende der Anforderungs Pipeline befindet, sodass keine nächste Middleware aufgerufen werden kann.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-163">That makes sense, because the handler is at the end of the request pipeline, so there will be no next middleware to invoke.</span></span>

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a><span data-ttu-id="ff6f8-164">Migrieren von handlereinfügung in die Anforderungs Pipeline</span><span class="sxs-lookup"><span data-stu-id="ff6f8-164">Migrating handler insertion into the request pipeline</span></span>

<span data-ttu-id="ff6f8-165">Das Konfigurieren eines HTTP-Handlers erfolgt in " *Web. config* " und sieht in etwa wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-165">Configuring an HTTP handler is done in *Web.config* and looks something like this:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

<span data-ttu-id="ff6f8-166">Sie können dies konvertieren, indem Sie die neue handlermiddleware der Anforderungs Pipeline in `Startup` ihrer Klasse hinzufügen, ähnlich der von Modulen konvertierten Middleware.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-166">You could convert this by adding your new handler middleware to the request pipeline in your `Startup` class, similar to middleware converted from modules.</span></span> <span data-ttu-id="ff6f8-167">Das Problem bei diesem Ansatz besteht darin, dass alle Anforderungen an die neue handlermiddleware gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-167">The problem with that approach is that it would send all requests to your new handler middleware.</span></span> <span data-ttu-id="ff6f8-168">Es ist jedoch nur gewünscht, dass Anforderungen mit einer bestimmten Erweiterung ihre Middleware erreichen.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-168">However, you only want requests with a given extension to reach your middleware.</span></span> <span data-ttu-id="ff6f8-169">Dies bietet Ihnen die gleiche Funktionalität wie bei Ihrem HTTP-Handler.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-169">That would give you the same functionality you had with your HTTP handler.</span></span>

<span data-ttu-id="ff6f8-170">Eine Lösung besteht darin, die Pipeline für Anforderungen mit einer bestimmten Erweiterung mithilfe der `MapWhen` Erweiterungsmethode zu verzweigen.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-170">One solution is to branch the pipeline for requests with a given extension, using the `MapWhen` extension method.</span></span> <span data-ttu-id="ff6f8-171">Dies geschieht in derselben `Configure` Methode, in der Sie die andere Middleware hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-171">You do this in the same `Configure` method where you add the other middleware:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

<span data-ttu-id="ff6f8-172">`MapWhen`Diese Parameter werden von benötigt:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-172">`MapWhen` takes these parameters:</span></span>

1. <span data-ttu-id="ff6f8-173">Ein Lambda, das den `HttpContext` annimmt und `true` zurückgibt, wenn die Anforderung den Branch nach unten durchlaufen soll.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-173">A lambda that takes the `HttpContext` and returns `true` if the request should go down the branch.</span></span> <span data-ttu-id="ff6f8-174">Dies bedeutet, dass Sie Anforderungen verzweigen können, die nicht nur auf Grundlage ihrer Erweiterung basieren, sondern auch auf Anforderungs Headern, Abfrage Zeichen folgen Parametern usw.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-174">This means you can branch requests not just based on their extension, but also on request headers, query string parameters, etc.</span></span>

2. <span data-ttu-id="ff6f8-175">Ein Lambda, das eine `IApplicationBuilder` annimmt und alle Middleware für den Branch hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-175">A lambda that takes an `IApplicationBuilder` and adds all the middleware for the branch.</span></span> <span data-ttu-id="ff6f8-176">Dies bedeutet, dass Sie der Verzweigung vor ihrer handlermiddleware zusätzliche Middleware hinzufügen können.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-176">This means you can add additional middleware to the branch in front of your handler middleware.</span></span>

<span data-ttu-id="ff6f8-177">Der Pipeline hinzugefügte Middleware, bevor die Verzweigung für alle Anforderungen aufgerufen wird. die Verzweigung wirkt sich nicht auf diese aus.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-177">Middleware added to the pipeline before the branch will be invoked on all requests; the branch will have no impact on them.</span></span>

## <a name="loading-middleware-options-using-the-options-pattern"></a><span data-ttu-id="ff6f8-178">Laden von Middleware-Optionen mit dem Options Muster</span><span class="sxs-lookup"><span data-stu-id="ff6f8-178">Loading middleware options using the options pattern</span></span>

<span data-ttu-id="ff6f8-179">Einige Module und Handler verfügen über Konfigurationsoptionen, die in der Datei " *Web. config*" gespeichert werden. In ASP.net Core jedoch anstelle von " *Web. config*" ein neues Konfigurations Modell verwendet.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-179">Some modules and handlers have configuration options that are stored in *Web.config*. However, in ASP.NET Core a new configuration model is used in place of *Web.config*.</span></span>

<span data-ttu-id="ff6f8-180">Das neue [Konfigurationssystem](xref:fundamentals/configuration/index) bietet Ihnen folgende Optionen, um dieses Problem zu beheben:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-180">The new [configuration system](xref:fundamentals/configuration/index) gives you these options to solve this:</span></span>

* <span data-ttu-id="ff6f8-181">Fügen Sie die Optionen direkt in die Middleware ein, wie im [nächsten Abschnitt](#loading-middleware-options-through-direct-injection)gezeigt.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-181">Directly inject the options into the middleware, as shown in the [next section](#loading-middleware-options-through-direct-injection).</span></span>

* <span data-ttu-id="ff6f8-182">Verwenden Sie das [options Muster](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="ff6f8-182">Use the [options pattern](xref:fundamentals/configuration/options):</span></span>

1. <span data-ttu-id="ff6f8-183">Erstellen Sie eine Klasse zum Speichern der Middleware-Optionen, z. b.:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-183">Create a class to hold your middleware options, for example:</span></span>

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. <span data-ttu-id="ff6f8-184">Speichern der Optionswerte</span><span class="sxs-lookup"><span data-stu-id="ff6f8-184">Store the option values</span></span>

   <span data-ttu-id="ff6f8-185">Mit dem Konfigurationssystem können Sie Optionswerte beliebig speichern.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-185">The configuration system allows you to store option values anywhere you want.</span></span> <span data-ttu-id="ff6f8-186">Die meisten Websites verwenden jedoch " *appSettings. JSON*", daher wird dieser Ansatz verwendet:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-186">However, most sites use *appsettings.json*, so we'll take that approach:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   <span data-ttu-id="ff6f8-187">*Mymiddlewareoptionssection* hier ist ein Abschnitts Name.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-187">*MyMiddlewareOptionsSection* here is a section name.</span></span> <span data-ttu-id="ff6f8-188">Er muss nicht mit dem Namen der Options Klasse identisch sein.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-188">It doesn't have to be the same as the name of your options class.</span></span>

3. <span data-ttu-id="ff6f8-189">Zuordnen der Optionswerte zur Options-Klasse</span><span class="sxs-lookup"><span data-stu-id="ff6f8-189">Associate the option values with the options class</span></span>

    <span data-ttu-id="ff6f8-190">Das Options Muster verwendet das Framework für die Abhängigkeitsinjektion von ASP.net Core, um den options `MyMiddlewareOptions`Typ (z `MyMiddlewareOptions` . b.) einem-Objekt zuzuordnen, das über die tatsächlichen Optionen verfügt.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-190">The options pattern uses ASP.NET Core's dependency injection framework to associate the options type (such as `MyMiddlewareOptions`) with a `MyMiddlewareOptions` object that has the actual options.</span></span>

    <span data-ttu-id="ff6f8-191">Aktualisieren Sie `Startup` Ihre Klasse:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-191">Update your `Startup` class:</span></span>

   1. <span data-ttu-id="ff6f8-192">Wenn Sie " *appSettings. JSON*" verwenden, fügen Sie es dem Konfigurations-Generator `Startup` im Konstruktor hinzu:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-192">If you're using *appsettings.json*, add it to the configuration builder in the `Startup` constructor:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. <span data-ttu-id="ff6f8-193">Konfigurieren Sie den Options Dienst:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-193">Configure the options service:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. <span data-ttu-id="ff6f8-194">Ordnen Sie die Optionen der Options-Klasse zu:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-194">Associate your options with your options class:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. <span data-ttu-id="ff6f8-195">Fügen Sie die Optionen in ihren Middleware-Konstruktor ein.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-195">Inject the options into your middleware constructor.</span></span> <span data-ttu-id="ff6f8-196">Dies ähnelt dem Einfügen von Optionen in einen Controller.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-196">This is similar to injecting options into a controller.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   <span data-ttu-id="ff6f8-197">Die [usemiddleware](#http-modules-usemiddleware) -Erweiterungsmethode, mit der die `IApplicationBuilder` Middleware hinzugefügt wird, übernimmt die Abhängigkeitsinjektion.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-197">The [UseMiddleware](#http-modules-usemiddleware) extension method that adds your middleware to the `IApplicationBuilder` takes care of dependency injection.</span></span>

   <span data-ttu-id="ff6f8-198">Dies ist nicht auf `IOptions` -Objekte beschränkt.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-198">This isn't limited to `IOptions` objects.</span></span> <span data-ttu-id="ff6f8-199">Alle anderen Objekte, die von der Middleware benötigt werden, können auf diese Weise eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-199">Any other object that your middleware requires can be injected this way.</span></span>

## <a name="loading-middleware-options-through-direct-injection"></a><span data-ttu-id="ff6f8-200">Laden von Middleware-Optionen durch direkte Injektion</span><span class="sxs-lookup"><span data-stu-id="ff6f8-200">Loading middleware options through direct injection</span></span>

<span data-ttu-id="ff6f8-201">Das Options Muster hat den Vorteil, dass es eine lose Kopplung zwischen Options Werten und ihren Consumern erzeugt.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-201">The options pattern has the advantage that it creates loose coupling between options values and their consumers.</span></span> <span data-ttu-id="ff6f8-202">Nachdem Sie eine Options Klasse mit den tatsächlichen Options Werten verknüpft haben, kann jede andere Klasse über das Framework für die Abhängigkeitsinjektion Zugriff auf die Optionen erhalten.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-202">Once you've associated an options class with the actual options values, any other class can get access to the options through the dependency injection framework.</span></span> <span data-ttu-id="ff6f8-203">Es ist nicht erforderlich, Optionswerte zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-203">There's no need to pass around options values.</span></span>

<span data-ttu-id="ff6f8-204">Dies wird jedoch unterbrochen, wenn Sie dieselbe Middleware zweimal mit unterschiedlichen Optionen verwenden möchten.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-204">This breaks down though if you want to use the same middleware twice, with different options.</span></span> <span data-ttu-id="ff6f8-205">Beispielsweise eine Autorisierungs Middleware, die in verschiedenen Verzweigungen verwendet wird und verschiedene Rollen zulässt.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-205">For example an authorization middleware used in different branches allowing different roles.</span></span> <span data-ttu-id="ff6f8-206">Sie können der One Options-Klasse nicht zwei verschiedene Options Objekte zuordnen.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-206">You can't associate two different options objects with the one options class.</span></span>

<span data-ttu-id="ff6f8-207">Die Lösung besteht darin, die Options Objekte mit den tatsächlichen Options Werten in Ihrer `Startup` Klasse zu erhalten und diese direkt an jede Instanz Ihrer Middleware zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-207">The solution is to get the options objects with the actual options values in your `Startup` class and pass those directly to each instance of your middleware.</span></span>

1. <span data-ttu-id="ff6f8-208">Fügen Sie " *appSettings. JSON* " einen zweiten Schlüssel hinzu.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-208">Add a second key to *appsettings.json*</span></span>

   <span data-ttu-id="ff6f8-209">Um der Datei " *appSettings. JSON* " einen zweiten Satz von Optionen hinzuzufügen, verwenden Sie einen neuen Schlüssel, um ihn eindeutig zu identifizieren:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-209">To add a second set of options to the *appsettings.json* file, use a new key to uniquely identify it:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. <span data-ttu-id="ff6f8-210">Rufen Sie Optionswerte ab, und übergeben Sie Sie an die Middleware.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-210">Retrieve options values and pass them to middleware.</span></span> <span data-ttu-id="ff6f8-211">Die `Use...` Erweiterungsmethode (die die Middleware zur Pipeline hinzufügt) ist ein logischer Speicherort, um die Optionswerte zu übergeben:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-211">The `Use...` extension method (which adds your middleware to the pipeline) is a logical place to pass in the option values:</span></span> 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. <span data-ttu-id="ff6f8-212">Aktivieren Sie die Middleware, um einen Optionsparameter zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-212">Enable middleware to take an options parameter.</span></span> <span data-ttu-id="ff6f8-213">Stellen Sie eine über `Use...` Ladung der Erweiterungsmethode bereit (die den options-Parameter annimmt und Sie an `UseMiddleware`übergibt).</span><span class="sxs-lookup"><span data-stu-id="ff6f8-213">Provide an overload of the `Use...` extension method (that takes the options parameter and passes it to `UseMiddleware`).</span></span> <span data-ttu-id="ff6f8-214">Wenn `UseMiddleware` mit Parametern aufgerufen wird, übergibt es die Parameter an Ihren Middleware-Konstruktor, wenn das Middleware-Objekt instanziiert wird.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-214">When `UseMiddleware` is called with parameters, it passes the parameters to your middleware constructor when it instantiates the middleware object.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   <span data-ttu-id="ff6f8-215">Beachten Sie, dass dies das Options-Objekt `OptionsWrapper` in einem-Objekt umschließt.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-215">Note how this wraps the options object in an `OptionsWrapper` object.</span></span> <span data-ttu-id="ff6f8-216">Dies implementiert `IOptions`, wie vom Middleware-Konstruktor erwartet.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-216">This implements `IOptions`, as expected by the middleware constructor.</span></span>

## <a name="migrating-to-the-new-httpcontext"></a><span data-ttu-id="ff6f8-217">Migrieren zum neuen HttpContext</span><span class="sxs-lookup"><span data-stu-id="ff6f8-217">Migrating to the new HttpContext</span></span>

<span data-ttu-id="ff6f8-218">Sie haben bereits gesehen, `Invoke` dass die-Methode in der Middleware einen Parameter `HttpContext`des Typs annimmt:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-218">You saw earlier that the `Invoke` method in your middleware takes a parameter of type `HttpContext`:</span></span>

```csharp
public async Task Invoke(HttpContext context)
```

<span data-ttu-id="ff6f8-219">`HttpContext`wurde in ASP.net Core erheblich geändert.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-219">`HttpContext` has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="ff6f8-220">In diesem Abschnitt wird gezeigt, wie die am häufigsten verwendeten Eigenschaften von [System. Web. HttpContext](/dotnet/api/system.web.httpcontext) in den `Microsoft.AspNetCore.Http.HttpContext`neuen übersetzt werden.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-220">This section shows how to translate the most commonly used properties of [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) to the new `Microsoft.AspNetCore.Http.HttpContext`.</span></span>

### <a name="httpcontext"></a><span data-ttu-id="ff6f8-221">HttpContext</span><span class="sxs-lookup"><span data-stu-id="ff6f8-221">HttpContext</span></span>

<span data-ttu-id="ff6f8-222">**HttpContext. Items** übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-222">**HttpContext.Items** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

<span data-ttu-id="ff6f8-223">**Eindeutige Anforderungs-ID (kein System. Web. HttpContext-Pendant)**</span><span class="sxs-lookup"><span data-stu-id="ff6f8-223">**Unique request ID (no System.Web.HttpContext counterpart)**</span></span>

<span data-ttu-id="ff6f8-224">Gibt Ihnen eine eindeutige ID für jede Anforderung.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-224">Gives you a unique id for each request.</span></span> <span data-ttu-id="ff6f8-225">Sehr nützlich, um in Ihre Protokolle einzubeziehen.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-225">Very useful to include in your logs.</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a><span data-ttu-id="ff6f8-226">HttpContext.Request</span><span class="sxs-lookup"><span data-stu-id="ff6f8-226">HttpContext.Request</span></span>

<span data-ttu-id="ff6f8-227">**HttpContext. Request. HttpMethod** übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-227">**HttpContext.Request.HttpMethod** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

<span data-ttu-id="ff6f8-228">**HttpContext.Request.QueryString** translates to:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-228">**HttpContext.Request.QueryString** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

<span data-ttu-id="ff6f8-229">" **HttpContext. Request. URL** " und " **HttpContext. Request. RawUrl** " übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-229">**HttpContext.Request.Url** and **HttpContext.Request.RawUrl** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

<span data-ttu-id="ff6f8-230">**HttpContext.Request.IsSecureConnection** translates to:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-230">**HttpContext.Request.IsSecureConnection** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

<span data-ttu-id="ff6f8-231">**HttpContext.Request.UserHostAddress** translates to:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-231">**HttpContext.Request.UserHostAddress** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

<span data-ttu-id="ff6f8-232">**HttpContext. Request. Cookies** übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-232">**HttpContext.Request.Cookies** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

<span data-ttu-id="ff6f8-233">**HttpContext.Request.RequestContext.RouteData** translates to:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-233">**HttpContext.Request.RequestContext.RouteData** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

<span data-ttu-id="ff6f8-234">**HttpContext. Request. Headers** übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-234">**HttpContext.Request.Headers** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

<span data-ttu-id="ff6f8-235">**HttpContext. Request. UserAgent** übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-235">**HttpContext.Request.UserAgent** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

<span data-ttu-id="ff6f8-236">**HttpContext. Request. UrlReferrer** übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-236">**HttpContext.Request.UrlReferrer** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

<span data-ttu-id="ff6f8-237">**HttpContext.Request.ContentType** translates to:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-237">**HttpContext.Request.ContentType** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

<span data-ttu-id="ff6f8-238">**HttpContext. Request. Form** übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-238">**HttpContext.Request.Form** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> <span data-ttu-id="ff6f8-239">Liest Formular Werte nur, wenn der Sub-Inhaltstyp " *x-www-form-urlencoded* " oder " *Form-Data*" ist.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-239">Read form values only if the content sub type is *x-www-form-urlencoded* or *form-data*.</span></span>

<span data-ttu-id="ff6f8-240">**HttpContext.Request.InputStream** translates to:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-240">**HttpContext.Request.InputStream** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> <span data-ttu-id="ff6f8-241">Verwenden Sie diesen Code nur in einer Handlertyp-Middleware am Ende einer Pipeline.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-241">Use this code only in a handler type middleware, at the end of a pipeline.</span></span>
>
><span data-ttu-id="ff6f8-242">Sie können den unformatierten Text nur einmal pro Anforderung lesen.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-242">You can read the raw body as shown above only once per request.</span></span> <span data-ttu-id="ff6f8-243">Middleware, die versucht, den Text nach dem ersten Lesevorgang zu lesen, liest einen leeren Text.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-243">Middleware trying to read the body after the first read will read an empty body.</span></span>
>
><span data-ttu-id="ff6f8-244">Dies gilt nicht für das Lesen eines Formulars wie zuvor gezeigt, da dies aus einem Puffer erfolgt.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-244">This doesn't apply to reading a form as shown earlier, because that's done from a buffer.</span></span>

### <a name="httpcontextresponse"></a><span data-ttu-id="ff6f8-245">HttpContext.Response</span><span class="sxs-lookup"><span data-stu-id="ff6f8-245">HttpContext.Response</span></span>

<span data-ttu-id="ff6f8-246">" **HttpContext. Response. Status** " und " **HttpContext. Response. StatusDescription** " übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-246">**HttpContext.Response.Status** and **HttpContext.Response.StatusDescription** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

<span data-ttu-id="ff6f8-247">" **HttpContext. Response. ContentEncoding** " und " **HttpContext. Response. ContentType** " übersetzen in:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-247">**HttpContext.Response.ContentEncoding** and **HttpContext.Response.ContentType** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

<span data-ttu-id="ff6f8-248">" **HttpContext. Response. ContentType** " selbst übersetzt ebenfalls Folgendes:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-248">**HttpContext.Response.ContentType** on its own also translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

<span data-ttu-id="ff6f8-249">**HttpContext. Response. Output** übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-249">**HttpContext.Response.Output** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

<span data-ttu-id="ff6f8-250">**HttpContext.Response.TransmitFile**</span><span class="sxs-lookup"><span data-stu-id="ff6f8-250">**HttpContext.Response.TransmitFile**</span></span>

<span data-ttu-id="ff6f8-251">Die Einrichtung einer Datei wird [hier](../fundamentals/request-features.md#middleware-and-request-features)erläutert.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-251">Serving up a file is discussed [here](../fundamentals/request-features.md#middleware-and-request-features).</span></span>

<span data-ttu-id="ff6f8-252">**HttpContext.Response.Headers**</span><span class="sxs-lookup"><span data-stu-id="ff6f8-252">**HttpContext.Response.Headers**</span></span>

<span data-ttu-id="ff6f8-253">Das Senden von Antwort Headern ist kompliziert, wenn Sie Sie nach dem Schreiben in den Antworttext festlegen, werden Sie nicht gesendet.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-253">Sending response headers is complicated by the fact that if you set them after anything has been written to the response body, they will not be sent.</span></span>

<span data-ttu-id="ff6f8-254">Die Lösung besteht darin, eine Rückruf Methode festzulegen, die vor dem Schreiben in die Antwort direkt aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-254">The solution is to set a callback method that will be called right before writing to the response starts.</span></span> <span data-ttu-id="ff6f8-255">Dies wird am Anfang `Invoke` der Methode in ihrer Middleware am besten erreicht.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-255">This is best done at the start of the `Invoke` method in your middleware.</span></span> <span data-ttu-id="ff6f8-256">Diese Rückruf Methode legt die Antwortheader fest.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-256">It's this callback method that sets your response headers.</span></span>

<span data-ttu-id="ff6f8-257">Der folgende Code legt eine Rückruf Methode namens `SetHeaders`fest:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-257">The following code sets a callback method called `SetHeaders`:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="ff6f8-258">Die `SetHeaders` Rückruf Methode würde wie folgt aussehen:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-258">The `SetHeaders` callback method would look like this:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

<span data-ttu-id="ff6f8-259">**HttpContext.Response.Cookies**</span><span class="sxs-lookup"><span data-stu-id="ff6f8-259">**HttpContext.Response.Cookies**</span></span>

<span data-ttu-id="ff6f8-260">Cookies gelangen zum Browser in einem *Set-Cookie-* Antwortheader.</span><span class="sxs-lookup"><span data-stu-id="ff6f8-260">Cookies travel to the browser in a *Set-Cookie* response header.</span></span> <span data-ttu-id="ff6f8-261">Das Senden von Cookies erfordert daher denselben Rückruf wie zum Senden von Antwort Headern:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-261">As a result, sending cookies requires the same callback as used for sending response headers:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="ff6f8-262">Die `SetCookies` Rückruf Methode würde wie folgt aussehen:</span><span class="sxs-lookup"><span data-stu-id="ff6f8-262">The `SetCookies` callback method would look like the following:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a><span data-ttu-id="ff6f8-263">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="ff6f8-263">Additional resources</span></span>

* [<span data-ttu-id="ff6f8-264">Übersicht über HTTP-Handler und HTTP-Module</span><span class="sxs-lookup"><span data-stu-id="ff6f8-264">HTTP Handlers and HTTP Modules Overview</span></span>](/iis/configuration/system.webserver/)
* [<span data-ttu-id="ff6f8-265">Configuration</span><span class="sxs-lookup"><span data-stu-id="ff6f8-265">Configuration</span></span>](xref:fundamentals/configuration/index)
* [<span data-ttu-id="ff6f8-266">Application Startup (Starten von Anwendungen)</span><span class="sxs-lookup"><span data-stu-id="ff6f8-266">Application Startup</span></span>](xref:fundamentals/startup)
* [<span data-ttu-id="ff6f8-267">Middleware</span><span class="sxs-lookup"><span data-stu-id="ff6f8-267">Middleware</span></span>](xref:fundamentals/middleware/index)

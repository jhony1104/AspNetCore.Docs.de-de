---
title: 'title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:'
author: rick-anderson
description: "'Blazor'"
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/1/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/routing
ms.openlocfilehash: f99ac6aea7bd959473fdbbfeec6ba19d4dad4ff3
ms.sourcegitcommit: 58722eb309767e462bdbf3082bd38737a4ef168f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84106545"
---
# <a name="routing-in-aspnet-core"></a><span data-ttu-id="18ac7-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-103">'Identity'</span></span>

<span data-ttu-id="18ac7-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-104">'Let's Encrypt'</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="18ac7-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-105">'Razor'</span></span> <span data-ttu-id="18ac7-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-106">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-107">Routing in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="18ac7-107">Routing in ASP.NET Core</span></span> <span data-ttu-id="18ac7-108">Von [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="18ac7-108">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span> <span data-ttu-id="18ac7-109">Das Routing wird für das Abgleichen von HTTP-Anforderungen und das Verteilen an ausführbare Endpunkte der App eingesetzt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-109">Routing is responsible for matching incoming HTTP requests and dispatching those requests to the app's executable endpoints.</span></span>

<span data-ttu-id="18ac7-110">[Endpunkte](#endpoint) sind die Einheiten des ausführbaren Codes für die Anforderungsverarbeitung in der App.</span><span class="sxs-lookup"><span data-stu-id="18ac7-110">[Endpoints](#endpoint) are the app's units of executable request-handling code.</span></span>

- <span data-ttu-id="18ac7-111">Endpunkte werden in der App definiert und beim Start der App konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="18ac7-111">Endpoints are defined in the app and configured when the app starts.</span></span>
- <span data-ttu-id="18ac7-112">Beim Endpunktabgleich können Werte aus der Anforderungs-URL extrahiert und für die Verarbeitung der Anforderung bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-112">The endpoint matching process can extract values from the request's URL and provide those values for request processing.</span></span>
- SignalR
- <span data-ttu-id="18ac7-113">Mithilfe von Endpunktinformationen aus der App lassen sich durch das Routing URLs generieren, die Endpunkten zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-113">Using endpoint information from the app, routing is also able to generate URLs that map to endpoints.</span></span>
- <span data-ttu-id="18ac7-114">Apps können das Routing mit folgenden Funktionen konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="18ac7-114">Apps can configure routing using:</span></span>
- <span data-ttu-id="18ac7-115">Controller</span><span class="sxs-lookup"><span data-stu-id="18ac7-115">Controllers</span></span>

Razor<span data-ttu-id="18ac7-116"> Pages</span><span class="sxs-lookup"><span data-stu-id="18ac7-116"> Pages</span></span> <span data-ttu-id="18ac7-117">gRPC-Dienste</span><span class="sxs-lookup"><span data-stu-id="18ac7-117">gRPC Services</span></span>

* <span data-ttu-id="18ac7-118">Endpunktfähige [Middleware](xref:fundamentals/middleware/index) wie [Integritätsprüfungen](xref:host-and-deploy/health-checks)</span><span class="sxs-lookup"><span data-stu-id="18ac7-118">Endpoint-enabled [middleware](xref:fundamentals/middleware/index) such as [Health Checks](xref:host-and-deploy/health-checks).</span></span>
* <span data-ttu-id="18ac7-119">Für das Routing registrierte Delegate und Lambdas</span><span class="sxs-lookup"><span data-stu-id="18ac7-119">Delegates and lambdas registered with routing.</span></span>

<span data-ttu-id="18ac7-120">In diesem Artikel werden die grundlegenden Details zum ASP.NET Core-Routing beschrieben.</span><span class="sxs-lookup"><span data-stu-id="18ac7-120">This document covers low-level details of ASP.NET Core routing.</span></span> <span data-ttu-id="18ac7-121">Informationen zur Routingkonfiguration finden Sie wie folgt:</span><span class="sxs-lookup"><span data-stu-id="18ac7-121">For information on configuring routing:</span></span>

* <span data-ttu-id="18ac7-122">Für Controller unter <xref:mvc/controllers/routing>.</span><span class="sxs-lookup"><span data-stu-id="18ac7-122">For controllers, see <xref:mvc/controllers/routing>.</span></span>
* <span data-ttu-id="18ac7-123">Konventionen für Razor Pages finden Sie unter <xref:razor-pages/razor-pages-conventions>.</span><span class="sxs-lookup"><span data-stu-id="18ac7-123">For Razor Pages conventions, see <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="18ac7-124">Das in diesem Dokument beschriebene Endpunktroutingsystem gilt für ASP.NET Core 3.0 und höher.</span><span class="sxs-lookup"><span data-stu-id="18ac7-124">The endpoint routing system described in this document applies to ASP.NET Core 3.0 and later.</span></span>

<span data-ttu-id="18ac7-125">Weitere Informationen zum vorherigen Routingsystem, das auf <xref:Microsoft.AspNetCore.Routing.IRouter> basiert, erhalten Sie, wenn Sie die ASP.NET Core 2.1-Version wie folgt auswählen:</span><span class="sxs-lookup"><span data-stu-id="18ac7-125">For information on the previous routing system based on <xref:Microsoft.AspNetCore.Routing.IRouter>, select the ASP.NET Core 2.1 version using one of the following approaches:</span></span> <span data-ttu-id="18ac7-126">Mit der Versionsauswahl für eine vorherige Version.</span><span class="sxs-lookup"><span data-stu-id="18ac7-126">The version selector for a previous version.</span></span>

## <a name="routing-basics"></a><span data-ttu-id="18ac7-127">Mit Auswahl von [ASP.NET Core 2.1](https://docs.microsoft.com/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).</span><span class="sxs-lookup"><span data-stu-id="18ac7-127">Select [ASP.NET Core 2.1 routing](https://docs.microsoft.com/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).</span></span>

<span data-ttu-id="18ac7-128">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples/3.x) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="18ac7-128">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span> <span data-ttu-id="18ac7-129">Die Downloadbeispiele für dieses Dokument werden durch eine bestimmte `Startup`-Klasse aktiviert.</span><span class="sxs-lookup"><span data-stu-id="18ac7-129">The download samples for this document are enabled by a specific `Startup` class.</span></span>

<span data-ttu-id="18ac7-130">Um ein bestimmtes Beispiel auszuführen, ändern Sie *Program.cs*, um die gewünschte `Startup`-Klasse aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-130">To run a specific sample, modify *Program.cs* to call the desired `Startup` class.</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Startup.cs?name=snippet&highlight=8,10)]

<span data-ttu-id="18ac7-131">Routinggrundlagen</span><span class="sxs-lookup"><span data-stu-id="18ac7-131">Routing basics</span></span>

* <span data-ttu-id="18ac7-132">Alle ASP.NET Core-Vorlagen enthalten die Routingfunktionen im generierten Code.</span><span class="sxs-lookup"><span data-stu-id="18ac7-132">All ASP.NET Core templates include routing in the generated code.</span></span> <span data-ttu-id="18ac7-133">Das Routing wird in `Startup.Configure` in der [Middlewarepipeline](xref:fundamentals/middleware/index) registriert.</span><span class="sxs-lookup"><span data-stu-id="18ac7-133">Routing is registered in the [middleware](xref:fundamentals/middleware/index) pipeline in `Startup.Configure`.</span></span>
* <span data-ttu-id="18ac7-134">Der folgende Code veranschaulicht ein einfaches Beispiel für das Routing:</span><span class="sxs-lookup"><span data-stu-id="18ac7-134">The following code shows a basic example of routing:</span></span> <span data-ttu-id="18ac7-135">Beim Routing wird ein Middlewarepaar verwendet, das durch <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> und <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> registriert wird:</span><span class="sxs-lookup"><span data-stu-id="18ac7-135">Routing uses a pair of middleware, registered by <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>:</span></span>

<span data-ttu-id="18ac7-136">`UseRouting` fügt der Middlewarepipeline einen Routenabgleich hinzu.</span><span class="sxs-lookup"><span data-stu-id="18ac7-136">`UseRouting` adds route matching to the middleware pipeline.</span></span>

* <span data-ttu-id="18ac7-137">Diese Middleware prüft die in der App definierten Endpunkte und wählt anhand der Anforderung die [beste Übereinstimmung](#urlm) aus.</span><span class="sxs-lookup"><span data-stu-id="18ac7-137">This middleware looks at the set of endpoints defined in the app, and selects the [best match](#urlm) based on the request.</span></span>
  * <span data-ttu-id="18ac7-138">`UseEndpoints` fügt der Middlewarepipeline die Endpunktausführung hinzu.</span><span class="sxs-lookup"><span data-stu-id="18ac7-138">`UseEndpoints` adds endpoint execution to the middleware pipeline.</span></span>
  * <span data-ttu-id="18ac7-139">Dabei wird der mit dem ausgewählten Endpunkt verknüpfte Delegat ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-139">It runs the delegate associated with the selected endpoint.</span></span> <span data-ttu-id="18ac7-140">Das vorherige Beispiel enthält eine einzelnen *Route-zu- Code*-Endpunkt unter Verwendung der [MapGet](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*)-Methode:</span><span class="sxs-lookup"><span data-stu-id="18ac7-140">The preceding example includes a single *route to code* endpoint using the [MapGet](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*) method:</span></span>
* <span data-ttu-id="18ac7-141">Wenn eine `GET`-HTTP-Anforderung an die Stamm-URL `/`gesendet wird:</span><span class="sxs-lookup"><span data-stu-id="18ac7-141">When an HTTP `GET` request is sent to the root URL `/`:</span></span>

### <a name="endpoint"></a><span data-ttu-id="18ac7-142">Der angezeigte Anforderungsdelegat wird ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-142">The request delegate shown executes.</span></span>

<a name="endpoint"></a>

<span data-ttu-id="18ac7-143">`Hello World!` wird in die HTTP-Antwort geschrieben.</span><span class="sxs-lookup"><span data-stu-id="18ac7-143">`Hello World!` is written to the HTTP response.</span></span> <span data-ttu-id="18ac7-144">Die Stamm-URL `/` lautet standardmäßig `https://localhost:5001/`.</span><span class="sxs-lookup"><span data-stu-id="18ac7-144">By default, the root URL `/` is `https://localhost:5001/`.</span></span>

* <span data-ttu-id="18ac7-145">Wenn die Anforderungsmethode nicht `GET` bzw. die Stamm-URL nicht `/` ist, gibt es keinen Routenabgleich und es wird ein HTTP-404-Fehler zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="18ac7-145">If the request method is not `GET` or the root URL is not `/`, no route matches and an HTTP 404 is returned.</span></span>
* <span data-ttu-id="18ac7-146">Endpunkt</span><span class="sxs-lookup"><span data-stu-id="18ac7-146">Endpoint</span></span>

<span data-ttu-id="18ac7-147">Die `MapGet`-Methode wird verwendet, um einen **Endpunkt** zu definieren.</span><span class="sxs-lookup"><span data-stu-id="18ac7-147">The `MapGet` method is used to define an **endpoint**.</span></span> <span data-ttu-id="18ac7-148">Ein Endpunkt kann Folgendes sein:</span><span class="sxs-lookup"><span data-stu-id="18ac7-148">An endpoint is something that can be:</span></span>
<span data-ttu-id="18ac7-149">Ausgewählt, indem die URL und die HTTP-Methode abgeglichen werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-149">Selected, by matching the URL and HTTP method.</span></span>
- <span data-ttu-id="18ac7-150">Ausgeführt, indem ein Delegat ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="18ac7-150">Executed, by running the delegate.</span></span>
- <span data-ttu-id="18ac7-151">Endpunkte, die von der App zugeordnet und ausgeführt werden können, sind in `UseEndpoints` konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="18ac7-151">Endpoints that can be matched and executed by the app are configured in `UseEndpoints`.</span></span>
- <span data-ttu-id="18ac7-152">Mit <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*>, <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapPost*> und [ähnlichen Methoden](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions) werden beispielsweise Anforderungsdelegate mit dem Routingsystem verbunden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-152">For example, <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*>, <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapPost*>, and [similar methods](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions) connect request delegates to the routing system.</span></span> 
- <span data-ttu-id="18ac7-153">Zudem können weitere Methoden zur Verbindung von ASP.NET Core-Frameworkfunktionen mit dem Routingsystem verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="18ac7-153">Additional methods can be used to connect ASP.NET Core framework features to the routing system:</span></span>

<span data-ttu-id="18ac7-154">[MapRazorPages für Razor Pages](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*)</span><span class="sxs-lookup"><span data-stu-id="18ac7-154">[MapRazorPages for Razor Pages](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*)</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/RouteTemplateStartup.cs?name=snippet)]

[<span data-ttu-id="18ac7-155">MapControllers für Controller</span><span class="sxs-lookup"><span data-stu-id="18ac7-155">MapControllers for controllers</span></span>](xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*) <span data-ttu-id="18ac7-156">[MapHub\<THub> für SignalR](xref:Microsoft.AspNetCore.SignalR.HubRouteBuilder.MapHub*)</span><span class="sxs-lookup"><span data-stu-id="18ac7-156">[MapHub\<THub> for SignalR](xref:Microsoft.AspNetCore.SignalR.HubRouteBuilder.MapHub*)</span></span> [<span data-ttu-id="18ac7-157">MapGrpcService\<TService> für gRPC</span><span class="sxs-lookup"><span data-stu-id="18ac7-157">MapGrpcService\<TService> for gRPC</span></span>](xref:grpc/aspnetcore)

* <span data-ttu-id="18ac7-158">Das folgende Beispiel zeigt das Routing mit einer anspruchsvolleren Routenvorlage:</span><span class="sxs-lookup"><span data-stu-id="18ac7-158">The following example shows routing with a more sophisticated route template:</span></span>
* <span data-ttu-id="18ac7-159">Die Zeichenfolge `/hello/{name:alpha}` ist eine **Routenvorlage**.</span><span class="sxs-lookup"><span data-stu-id="18ac7-159">The string `/hello/{name:alpha}` is a **route template**.</span></span>  <span data-ttu-id="18ac7-160">Sie wird verwendet, um zu konfigurieren, wie der Endpunkt abgeglichen wird.</span><span class="sxs-lookup"><span data-stu-id="18ac7-160">It is used to configure how the endpoint is matched.</span></span> <span data-ttu-id="18ac7-161">In diesem Fall gleicht die Vorlage Folgendes ab:</span><span class="sxs-lookup"><span data-stu-id="18ac7-161">In this case, the template matches:</span></span>

<span data-ttu-id="18ac7-162">Eine URL wie `/hello/Ryan`</span><span class="sxs-lookup"><span data-stu-id="18ac7-162">A URL like `/hello/Ryan`</span></span>

* <span data-ttu-id="18ac7-163">Alle URL-Pfade, die mit `/hello/` beginnen, gefolgt von einer Sequenz alphabetischer Zeichen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-163">Any URL path that begins with `/hello/` followed by a sequence of alphabetic characters.</span></span>
* <span data-ttu-id="18ac7-164">`:alpha` wendet eine Routeneinschränkung an, die nur alphabetische Zeichen abgleicht.</span><span class="sxs-lookup"><span data-stu-id="18ac7-164">`:alpha` applies a route constraint that matches only alphabetic characters.</span></span>

<span data-ttu-id="18ac7-165">[Routeneinschränkungen](#route-constraint-reference) werden weiter unten in diesem Dokument erläutert.</span><span class="sxs-lookup"><span data-stu-id="18ac7-165">[Route constraints](#route-constraint-reference) are explained later in this document.</span></span> <span data-ttu-id="18ac7-166">Das zweite Segment des URL-Pfads, `{name:alpha}`:</span><span class="sxs-lookup"><span data-stu-id="18ac7-166">The second segment of the URL path, `{name:alpha}`:</span></span>

<span data-ttu-id="18ac7-167">Ist an den Parameter `name` gebunden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-167">Is bound to the `name` parameter.</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="18ac7-168">Ist in [HttpRequest.RouteValues](xref:Microsoft.AspNetCore.Http.HttpRequest.RouteValues*) erfasst und gespeichert.</span><span class="sxs-lookup"><span data-stu-id="18ac7-168">Is captured and stored in [HttpRequest.RouteValues](xref:Microsoft.AspNetCore.Http.HttpRequest.RouteValues*).</span></span>

* <span data-ttu-id="18ac7-169">Das in diesem Dokument beschriebene Endpunktroutingsystem gilt ab ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="18ac7-169">The endpoint routing system described in this document is new as of ASP.NET Core 3.0.</span></span>
* <span data-ttu-id="18ac7-170">Alle Versionen von ASP.NET Core unterstützen jedoch dieselben Routenvorlagenfunktionen und Routeneinschränkungen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-170">However, all versions of ASP.NET Core support the same set of route template features and route constraints.</span></span>

<span data-ttu-id="18ac7-171">Das folgende Beispiel zeigt das Routing mit [Integritätsprüfungen](xref:host-and-deploy/health-checks) und Autorisierung:</span><span class="sxs-lookup"><span data-stu-id="18ac7-171">The following example shows routing with [health checks](xref:host-and-deploy/health-checks) and authorization:</span></span> <span data-ttu-id="18ac7-172">Im vorherigen Beispiel wird veranschaulicht, wie Sie:</span><span class="sxs-lookup"><span data-stu-id="18ac7-172">The preceding example demonstrates how:</span></span>

<span data-ttu-id="18ac7-173">Die Autorisierungsmiddleware für das Routing verwenden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-173">The authorization middleware can be used with routing.</span></span> <span data-ttu-id="18ac7-174">Endpunkte zum Konfigurieren des Autorisierungsverhaltens verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="18ac7-174">Endpoints can be used to configure authorization behavior.</span></span>

* <span data-ttu-id="18ac7-175">Der <xref:Microsoft.AspNetCore.Builder.HealthCheckEndpointRouteBuilderExtensions.MapHealthChecks*>-Aufruf fügt einen Endpunkt für eine Integritätsprüfung hinzu.</span><span class="sxs-lookup"><span data-stu-id="18ac7-175">The <xref:Microsoft.AspNetCore.Builder.HealthCheckEndpointRouteBuilderExtensions.MapHealthChecks*> call adds a health check endpoint.</span></span>
* <span data-ttu-id="18ac7-176">Durch die Verkettung von <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*> mit diesem Aufruf wird eine Autorisierungsrichtlinie an den Endpunkt angefügt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-176">Chaining <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*> on to this call attaches an authorization policy to the endpoint.</span></span>

<a name="metadata"></a>

### <a name="endpoint-metadata"></a><span data-ttu-id="18ac7-177">Der Aufruf von <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> und <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*> wird die Authentifizierungs- und Autorisierungsmiddleware hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-177">Calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*> adds the authentication and authorization middleware.</span></span>

<span data-ttu-id="18ac7-178">Diese Middleware wird zum Ausführen folgender Aktionen zwischen <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> und `UseEndpoints` platziert:</span><span class="sxs-lookup"><span data-stu-id="18ac7-178">These middleware are placed between <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and `UseEndpoints` so that they can:</span></span> <span data-ttu-id="18ac7-179">Anzeigen des von `UseRouting` ausgewählten Endpunkts.</span><span class="sxs-lookup"><span data-stu-id="18ac7-179">See which endpoint was selected by `UseRouting`.</span></span> <span data-ttu-id="18ac7-180">Anwenden einer Autorisierungsrichtlinie vor dem Senden von <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> an den Endpunkt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-180">Apply an authorization policy before <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> dispatches to the endpoint.</span></span> <span data-ttu-id="18ac7-181">Endpunktmetadaten</span><span class="sxs-lookup"><span data-stu-id="18ac7-181">Endpoint metadata</span></span>

* <span data-ttu-id="18ac7-182">Im vorangehenden Beispiel gibt es zwei Endpunkte, aber nur dem für die Integritätsprüfung ist eine Autorisierungsrichtlinie angefügt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-182">In the preceding example, there are two endpoints, but only the health check endpoint has an authorization policy attached.</span></span>
* <span data-ttu-id="18ac7-183">Wenn die Anforderung mit dem Endpunkt der Integritätsprüfung, `/healthz`, übereinstimmt, wird eine Autorisierungsprüfung durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-183">If the request matches the health check endpoint, `/healthz`, an authorization check is performed.</span></span>

## <a name="routing-concepts"></a><span data-ttu-id="18ac7-184">Dadurch wird veranschaulicht, dass Endpunkten zusätzliche Daten zugeordnet werden können.</span><span class="sxs-lookup"><span data-stu-id="18ac7-184">This demonstrates that endpoints can have extra data attached to them.</span></span>

<span data-ttu-id="18ac7-185">Diese zusätzlichen Daten werden als **Metadaten** des Endpunkts bezeichnet:</span><span class="sxs-lookup"><span data-stu-id="18ac7-185">This extra data is called endpoint **metadata**:</span></span> <span data-ttu-id="18ac7-186">Die Metadaten lassen sich von routingfähiger Middleware verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="18ac7-186">The metadata can be processed by routing-aware middleware.</span></span>

<a name="endpoint"></a>

### <a name="aspnet-core-endpoint-definition"></a><span data-ttu-id="18ac7-187">Die Metadaten können einen beliebigen .NET-Typ aufweisen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-187">The metadata can be of any .NET type.</span></span>

<span data-ttu-id="18ac7-188">Routingkonzepte</span><span class="sxs-lookup"><span data-stu-id="18ac7-188">Routing concepts</span></span>

* <span data-ttu-id="18ac7-189">Durch Hinzufügen des effizienten **Endpunkt**-Konzepts stellt das Routingsystem eine Ergänzung der Middlewarepipeline dar.</span><span class="sxs-lookup"><span data-stu-id="18ac7-189">The routing system builds on top of the middleware pipeline by adding the powerful **endpoint** concept.</span></span>
* <span data-ttu-id="18ac7-190">Endpunkte stehen für Funktionseinheiten der App, die sich in Bezug auf Routing, Autorisierung und die Anzahl der ASP.NET Core-Systeme voneinander unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-190">Endpoints represent units of the app's functionality that are distinct from each other in terms of routing, authorization, and any number of ASP.NET Core's systems.</span></span>
* <span data-ttu-id="18ac7-191">ASP.NET Core-Endpunktdefinition</span><span class="sxs-lookup"><span data-stu-id="18ac7-191">ASP.NET Core endpoint definition</span></span>
* <span data-ttu-id="18ac7-192">Ein ASP.NET Core-Endpunkt ist:</span><span class="sxs-lookup"><span data-stu-id="18ac7-192">An ASP.NET Core endpoint is:</span></span>

<span data-ttu-id="18ac7-193">Ausführbar: Verfügt über eine <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate>-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="18ac7-193">Executable: Has a <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate>.</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/EndpointInspectorStartup.cs?name=snippet)]

<span data-ttu-id="18ac7-194">Erweiterbar: Verfügt über eine [Metadata](xref:Microsoft.AspNetCore.Http.Endpoint.Metadata*)-Sammlung.</span><span class="sxs-lookup"><span data-stu-id="18ac7-194">Extensible: Has a [Metadata](xref:Microsoft.AspNetCore.Http.Endpoint.Metadata*) collection.</span></span> <span data-ttu-id="18ac7-195">Selectable (Auswählbar): Verfügt optional über [Routinginformationen](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern*).</span><span class="sxs-lookup"><span data-stu-id="18ac7-195">Selectable: Optionally, has [routing information](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern*).</span></span> <span data-ttu-id="18ac7-196">Aufzählbar: Die Sammlung von Endpunkten kann durch Abrufen der <xref:Microsoft.AspNetCore.Routing.EndpointDataSource>-Klasse aus [DI](xref:fundamentals/dependency-injection) aufgelistet werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-196">Enumerable: The collection of endpoints can be listed by retrieving the <xref:Microsoft.AspNetCore.Routing.EndpointDataSource> from [DI](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="18ac7-197">Der folgende Code zeigt, wie der Endpunkt, der mit der aktuellen Anforderung übereinstimmt, abgerufen und geprüft werden kann:</span><span class="sxs-lookup"><span data-stu-id="18ac7-197">The following code shows how to retrieve and inspect the endpoint matching the current request:</span></span> <span data-ttu-id="18ac7-198">Der Endpunkt, falls ausgewählt, kann aus dem `HttpContext`-Element abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-198">The endpoint, if selected, can be retrieved from the `HttpContext`.</span></span>

<span data-ttu-id="18ac7-199">Seine Eigenschaften können geprüft werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-199">Its properties can be inspected.</span></span>

<a name="mt"></a>

<span data-ttu-id="18ac7-200">Endpunktobjekte sind unveränderlich und können nach der Erstellung nicht mehr geändert werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-200">Endpoint objects are immutable and cannot be modified after creation.</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/MiddlewareFlowStartup.cs?name=snippet)]

<span data-ttu-id="18ac7-201">Der häufigste Typ des Endpunkts ist eine <xref:Microsoft.AspNetCore.Routing.RouteEndpoint>-Klasse.</span><span class="sxs-lookup"><span data-stu-id="18ac7-201">The most common type of endpoint is a <xref:Microsoft.AspNetCore.Routing.RouteEndpoint>.</span></span> <span data-ttu-id="18ac7-202">Die `RouteEndpoint`-Klasse enthält Informationen, durch die diese vom Routingsystem ausgewählt werden kann.</span><span class="sxs-lookup"><span data-stu-id="18ac7-202">`RouteEndpoint` includes information that allows it to be to selected by the routing system.</span></span>

<span data-ttu-id="18ac7-203">Im vorangehenden Code wird mit [app.Use](xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*) eine [Middleware](xref:fundamentals/middleware/index) inline konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="18ac7-203">In the preceding code, [app.Use](xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*) configures an in-line [middleware](xref:fundamentals/middleware/index).</span></span>

```txt
1. Endpoint: (null)
2. Endpoint: Hello
3. Endpoint: Hello
```

<span data-ttu-id="18ac7-204">Der folgende Code zeigt, dass es, je nachdem, wo `app.Use` in der Pipeline aufgerufen wird, möglicherweise keinen Endpunkt gibt:</span><span class="sxs-lookup"><span data-stu-id="18ac7-204">The following code shows that, depending on where `app.Use` is called in the pipeline, there may not be an endpoint:</span></span>

```txt
1. Endpoint: (null)
2. Endpoint: (null)
4. Endpoint: (null)
```

<span data-ttu-id="18ac7-205">In diesem vorangehenden Beispiel werden `Console.WriteLine`-Anweisungen hinzugefügt, die anzeigen, ob ein Endpunkt ausgewählt wurde oder nicht.</span><span class="sxs-lookup"><span data-stu-id="18ac7-205">This preceding sample adds `Console.WriteLine` statements that display whether or not an endpoint has been selected.</span></span>

* <span data-ttu-id="18ac7-206">Aus Gründen der Übersichtlichkeit wird in dem Beispiel dem bereitgestellten `/`-Endpunkt ein Anzeigename zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-206">For clarity, the sample assigns a display name to the provided `/` endpoint.</span></span>
* <span data-ttu-id="18ac7-207">Wenn Sie diesen Code mit einer URL `/` ausführen, wird Folgendes angezeigt:</span><span class="sxs-lookup"><span data-stu-id="18ac7-207">Running this code with a URL of `/` displays:</span></span>
* <span data-ttu-id="18ac7-208">Wenn Sie diesen Code mit einer anderen URL ausführen, wird Folgendes angezeigt:</span><span class="sxs-lookup"><span data-stu-id="18ac7-208">Running this code with any other URL displays:</span></span> <span data-ttu-id="18ac7-209">Diese Ausgabe zeigt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="18ac7-209">This output demonstrates that:</span></span>
* <span data-ttu-id="18ac7-210">Der Endpunkt ist immer NULL, bevor `UseRouting` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="18ac7-210">The endpoint is always null before `UseRouting` is called.</span></span>

<span data-ttu-id="18ac7-211">Wenn eine Übereinstimmung gefunden wird, ist der Endpunkt zwischen `UseRouting` und <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> ungleich NULL.</span><span class="sxs-lookup"><span data-stu-id="18ac7-211">If a match is found, the endpoint is non-null between `UseRouting` and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.</span></span> <span data-ttu-id="18ac7-212">Die `UseEndpoints`-Middleware ist eine **Terminalmiddleware**, wenn eine Übereinstimmung gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="18ac7-212">The `UseEndpoints` middleware is **terminal** when a match is found.</span></span> <span data-ttu-id="18ac7-213">[Terminalmiddleware](#tm) wird weiter unten in diesem Dokument definiert.</span><span class="sxs-lookup"><span data-stu-id="18ac7-213">[Terminal middleware](#tm) is defined later in this document.</span></span> <span data-ttu-id="18ac7-214">Die Middleware nach `UseEndpoints` wird nur ausgeführt, wenn keine Übereinstimmung gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="18ac7-214">The middleware after `UseEndpoints` execute only when no match is found.</span></span>

<span data-ttu-id="18ac7-215">Die `UseRouting`-Middleware verwendet die [SetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.SetEndpoint*)-Methode, um den Endpunkt an den aktuellen Kontext anzufügen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-215">The `UseRouting` middleware uses the [SetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.SetEndpoint*) method to attach the endpoint to the current context.</span></span> <span data-ttu-id="18ac7-216">Es ist möglich, die `UseRouting`-Middleware durch benutzerdefinierte Logik zu ersetzen und dennoch die Vorteile durch die Verwendung von Endpunkten zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-216">It's possible to replace the `UseRouting` middleware with custom logic and still get the benefits of using endpoints.</span></span> <span data-ttu-id="18ac7-217">Endpunkte befinden sich auf niedriger Ebene, wie Middleware, und sind nicht an die Routingimplementierung gekoppelt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-217">Endpoints are a low-level primitive like middleware, and aren't coupled to the routing implementation.</span></span>

<span data-ttu-id="18ac7-218">Die meisten Apps müssen `UseRouting` nicht durch eigene Logik ersetzen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-218">Most apps don't need to replace `UseRouting` with custom logic.</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/IntegratedMiddlewareStartup.cs?name=snippet)]

<span data-ttu-id="18ac7-219">Die `UseEndpoints`-Middleware ist so konzipiert, dass Sie zusammen mit der `UseRouting`-Middleware verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="18ac7-219">The `UseEndpoints` middleware is designed to be used in tandem with the `UseRouting` middleware.</span></span>

* <span data-ttu-id="18ac7-220">Die Hauptlogik zum Ausführen eines Endpunkts ist nicht kompliziert.</span><span class="sxs-lookup"><span data-stu-id="18ac7-220">The core logic to execute an endpoint isn't complicated.</span></span>
    * <span data-ttu-id="18ac7-221">Mit <xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*> können Sie einen Endpunkt abrufen und dann dessen <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate>-Eigenschaft aufrufen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-221">Use <xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*> to retrieve the endpoint, and then invoke its <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate> property.</span></span>
* <span data-ttu-id="18ac7-222">Der folgende Code veranschaulicht, wie Middleware das Routing beeinflussen oder darauf reagieren kann:</span><span class="sxs-lookup"><span data-stu-id="18ac7-222">The following code demonstrates how middleware can influence or react to routing:</span></span>
    * <span data-ttu-id="18ac7-223">Im vorherigen Beispiel werden zwei wichtige Konzepte dargestellt:</span><span class="sxs-lookup"><span data-stu-id="18ac7-223">The preceding example demonstrates two important concepts:</span></span>
      * <span data-ttu-id="18ac7-224">Die Middleware kann vor `UseRouting` ausgeführt werden, um die Daten zu ändern, auf denen das Routing basiert.</span><span class="sxs-lookup"><span data-stu-id="18ac7-224">Middleware can run before `UseRouting` to modify the data that routing operates upon.</span></span>
      * <span data-ttu-id="18ac7-225">Normalerweise verändert Middleware, die vor dem Routing eingesetzt wird, eine Eigenschaft der Anforderung, wie z.B. <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>, <xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions.UseHttpMethodOverride*> oder <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*>.</span><span class="sxs-lookup"><span data-stu-id="18ac7-225">Usually middleware that appears before routing modifies some property of the request, such as <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>, <xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions.UseHttpMethodOverride*>, or <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*>.</span></span>
    * <span data-ttu-id="18ac7-226">Die Middleware kann zwischen `UseRouting` und <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> ausgeführt werden, um die Ergebnisse des Routings vor der Ausführung des Endpunkts zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="18ac7-226">Middleware can run between `UseRouting` and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> to process the results of routing before the endpoint is executed.</span></span>

<span data-ttu-id="18ac7-227">Middleware, die zwischen `UseRouting` und `UseEndpoints` ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="18ac7-227">Middleware that runs between `UseRouting` and `UseEndpoints`:</span></span> <span data-ttu-id="18ac7-228">Überprüft in der Regel die Metadaten, um die Endpunkte zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="18ac7-228">Usually inspects metadata to understand the endpoints.</span></span> <span data-ttu-id="18ac7-229">Trifft häufig Sicherheitsentscheidungen, wie `UseAuthorization` und `UseCors`.</span><span class="sxs-lookup"><span data-stu-id="18ac7-229">Often makes security decisions, as done by `UseAuthorization` and `UseCors`.</span></span> <span data-ttu-id="18ac7-230">Durch die Kombination aus Middleware und Metadaten ist es möglich, für jeden einzelnen Endpunkt Richtlinien zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="18ac7-230">The combination of middleware and metadata allows configuring policies per-endpoint.</span></span> <span data-ttu-id="18ac7-231">Der vorangehende Code zeigt ein Beispiel für eine benutzerdefinierte Middleware, die entpunktbezogene Richtlinien unterstützt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-231">The preceding code shows an example of a custom middleware that supports per-endpoint policies.</span></span> <span data-ttu-id="18ac7-232">Die Middleware schreibt ein *Überwachungsprotokoll* für den Zugriff auf vertrauliche Daten in der Konsole.</span><span class="sxs-lookup"><span data-stu-id="18ac7-232">The middleware writes an *audit log* of access to sensitive data to the console.</span></span> <span data-ttu-id="18ac7-233">Die Middleware kann so konfiguriert werden, dass ein Endpunkt mit den `AuditPolicyAttribute`-Metadaten *überwacht* wird.</span><span class="sxs-lookup"><span data-stu-id="18ac7-233">The middleware can be configured to *audit* an endpoint with the `AuditPolicyAttribute` metadata.</span></span>

<span data-ttu-id="18ac7-234">In diesem Beispiel wird ein *Opt-In*-Muster veranschaulicht, bei dem nur Endpunkte überwacht werden, die als vertraulich markiert sind.</span><span class="sxs-lookup"><span data-stu-id="18ac7-234">This sample demonstrates an *opt-in* pattern where only endpoints that are marked as sensitive are audited.</span></span> <span data-ttu-id="18ac7-235">Es ist möglich, diese Logik umgekehrt zu definieren, indem beispielsweise alles geprüft wird, was nicht als sicher markiert ist.</span><span class="sxs-lookup"><span data-stu-id="18ac7-235">It's possible to define this logic in reverse, auditing everything that isn't marked as safe, for example.</span></span> <span data-ttu-id="18ac7-236">Das Endpunktmetadaten-System ist flexibel.</span><span class="sxs-lookup"><span data-stu-id="18ac7-236">The endpoint metadata system is flexible.</span></span>

* <span data-ttu-id="18ac7-237">Diese Logik lässt sich für jeden Anwendungsfall passend schreiben.</span><span class="sxs-lookup"><span data-stu-id="18ac7-237">This logic could be designed in whatever way suits the use case.</span></span>
* <span data-ttu-id="18ac7-238">Der vorherige Beispielcode soll die grundlegenden Konzepte von Endpunkten veranschaulichen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-238">The preceding sample code is intended to demonstrate the basic concepts of endpoints.</span></span>

<span data-ttu-id="18ac7-239">**Das Beispiel ist nicht für Produktionsumgebungen vorgesehen.**</span><span class="sxs-lookup"><span data-stu-id="18ac7-239">**The sample is not intended for production use**.</span></span> <span data-ttu-id="18ac7-240">Eine vollständigere Version einer Middleware für *Überwachungsprotokolle* würde Folgendes bieten:</span><span class="sxs-lookup"><span data-stu-id="18ac7-240">A more complete version of an *audit log* middleware would:</span></span>

* <span data-ttu-id="18ac7-241">Protokollieren in einer Datei oder einer Datenbank.</span><span class="sxs-lookup"><span data-stu-id="18ac7-241">Log to a file or database.</span></span>
* <span data-ttu-id="18ac7-242">Einschließen von Details wie Benutzer, IP-Adresse, Name des vertraulichen Endpunkts usw.</span><span class="sxs-lookup"><span data-stu-id="18ac7-242">Include details such as the user, IP address, name of the sensitive endpoint, and more.</span></span>

<span data-ttu-id="18ac7-243">Die Metadaten der Überwachungsrichtlinie `AuditPolicyAttribute` sind als `Attribute` definiert, um die Verwendung mit klassenbasierten Frameworks wie Controllern und SignalR zu erleichtern.</span><span class="sxs-lookup"><span data-stu-id="18ac7-243">The audit policy metadata `AuditPolicyAttribute` is defined as an `Attribute` for easier use with class-based frameworks such as controllers and SignalR.</span></span> <span data-ttu-id="18ac7-244">Bei Verwendung von *Route-zu-Code*:</span><span class="sxs-lookup"><span data-stu-id="18ac7-244">When using *route to code*:</span></span> <span data-ttu-id="18ac7-245">Metadaten werden an eine Generator-API angefügt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-245">Metadata is attached with a builder API.</span></span>

<a name="tm"></a>

### <a name="comparing-a-terminal-middleware-and-routing"></a><span data-ttu-id="18ac7-246">Klassenbasierte Frameworks enthalten beim Erstellen von Endpunkten alle Attribute der entsprechenden Methode und Klasse.</span><span class="sxs-lookup"><span data-stu-id="18ac7-246">Class-based frameworks include all attributes on the corresponding method and class when creating endpoints.</span></span>

<span data-ttu-id="18ac7-247">Die bewährten Methoden für Metadatentypen sind, sie entweder als Schnittstellen oder als Attribute zu definieren.</span><span class="sxs-lookup"><span data-stu-id="18ac7-247">The best practices for metadata types are to define them either as interfaces or attributes.</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/TerminalMiddlewareStartup.cs?name=snippet)]

<span data-ttu-id="18ac7-248">Schnittstellen und Attribute ermöglichen die Wiederverwendung von Code.</span><span class="sxs-lookup"><span data-stu-id="18ac7-248">Interfaces and attributes allow code reuse.</span></span> <span data-ttu-id="18ac7-249">Das Metadatensystem ist flexibel und weist keine Einschränkungen auf.</span><span class="sxs-lookup"><span data-stu-id="18ac7-249">The metadata system is flexible and doesn't impose any limitations.</span></span>

* <span data-ttu-id="18ac7-250">Vergleichen von Terminalmiddleware und Routing</span><span class="sxs-lookup"><span data-stu-id="18ac7-250">Comparing a terminal middleware and routing</span></span>
* <span data-ttu-id="18ac7-251">Das folgende Codebeispiel zeigt den Unterschied zwischen Middleware und Routing:</span><span class="sxs-lookup"><span data-stu-id="18ac7-251">The following code sample contrasts using middleware with using routing:</span></span>

<span data-ttu-id="18ac7-252">Beim in `Approach 1:` gezeigten Stil von Middleware handelt es sich um **Terminalmiddleware**.</span><span class="sxs-lookup"><span data-stu-id="18ac7-252">The style of middleware shown with `Approach 1:` is **terminal middleware**.</span></span>

<span data-ttu-id="18ac7-253">Sie wird als Terminalmiddleware bezeichnet, da sie einen Abgleich durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-253">It's called terminal middleware because it does a matching operation:</span></span>
* <span data-ttu-id="18ac7-254">Der Abgleich im vorangehenden Beispiel ist `Path == "/"` für die Middleware und `Path == "/Movie"` für das Routing.</span><span class="sxs-lookup"><span data-stu-id="18ac7-254">The matching operation in the preceding sample is `Path == "/"` for the middleware and `Path == "/Movie"` for routing.</span></span>
    * <span data-ttu-id="18ac7-255">Bei einem erfolgreichen Abgleich führt sie bestimmte Funktionen aus und kehrt zurück, anstatt die `next`-Middleware aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-255">When a match is successful, it executes some functionality and returns, rather than invoking the `next` middleware.</span></span>
    * <span data-ttu-id="18ac7-256">Sie wird „Terminal Middleware“ genannt, da sie die Suche beendet, einige Funktionen ausführt und dann zurückkehrt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-256">It's called terminal middleware because it terminates the search, executes some functionality, and then returns.</span></span>
* <span data-ttu-id="18ac7-257">Vergleichen von Terminalmiddleware und Routing:</span><span class="sxs-lookup"><span data-stu-id="18ac7-257">Comparing a terminal middleware and routing:</span></span>
    * <span data-ttu-id="18ac7-258">Beide Ansätze ermöglichen das Beenden der Verarbeitungspipeline:</span><span class="sxs-lookup"><span data-stu-id="18ac7-258">Both approaches allow terminating the processing pipeline:</span></span>
* <span data-ttu-id="18ac7-259">Die Middleware beendet die Pipeline, indem Sie zurückkehrt, anstatt `next` aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-259">Middleware terminates the pipeline by returning rather than invoking `next`.</span></span>
    * <span data-ttu-id="18ac7-260">Endpunkte beenden immer die Verarbeitung.</span><span class="sxs-lookup"><span data-stu-id="18ac7-260">Endpoints are always terminal.</span></span>
    * <span data-ttu-id="18ac7-261">Terminalmiddleware ermöglicht die Positionierung der Middleware an einer beliebigen Stelle in der Pipeline:</span><span class="sxs-lookup"><span data-stu-id="18ac7-261">Terminal middleware allows positioning the middleware at an arbitrary place in the pipeline:</span></span> <span data-ttu-id="18ac7-262">Endpunkte werden an der Position von <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-262">Endpoints execute at the position of <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.</span></span>
* <span data-ttu-id="18ac7-263">Mit Terminalmiddleware kann beliebiger Code beendet werden, wenn die Middleware übereinstimmt:</span><span class="sxs-lookup"><span data-stu-id="18ac7-263">Terminal middleware allows arbitrary code to determine when the middleware matches:</span></span>
    * <span data-ttu-id="18ac7-264">Ein benutzerdefinierter Routenabgleichscode kann sehr umständlich und schwierig zu schreiben sein.</span><span class="sxs-lookup"><span data-stu-id="18ac7-264">Custom route matching code can be verbose and difficult to write correctly.</span></span>

<span data-ttu-id="18ac7-265">Das Routing bietet unkomplizierte Lösungen für typische Apps.</span><span class="sxs-lookup"><span data-stu-id="18ac7-265">Routing provides straightforward solutions for typical apps.</span></span>

* <span data-ttu-id="18ac7-266">Die meisten Apps erfordern keinen benutzerdefinierten Routenabgleichscode.</span><span class="sxs-lookup"><span data-stu-id="18ac7-266">Most apps don't require custom route matching code.</span></span>
* <span data-ttu-id="18ac7-267">Endpunkte haben eine Schnittstelle mit Middleware wie `UseAuthorization` und `UseCors`.</span><span class="sxs-lookup"><span data-stu-id="18ac7-267">Endpoints interface with middleware such as `UseAuthorization` and `UseCors`.</span></span> <span data-ttu-id="18ac7-268">Die Verwendung einer Terminalmiddleware mit `UseAuthorization` oder `UseCors` erfordert eine manuelle Verknüpfung mit dem Autorisierungssystem.</span><span class="sxs-lookup"><span data-stu-id="18ac7-268">Using a terminal middleware with `UseAuthorization` or `UseCors` requires manual interfacing with the authorization system.</span></span>

<span data-ttu-id="18ac7-269">Ein [Endpunkt](#endpoint) definiert beides:</span><span class="sxs-lookup"><span data-stu-id="18ac7-269">An [endpoint](#endpoint) defines both:</span></span>

* <span data-ttu-id="18ac7-270">Einen Delegaten zum Verarbeiten von Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-270">A delegate to process requests.</span></span>
* <span data-ttu-id="18ac7-271">Eine Sammlung beliebiger Metadaten.</span><span class="sxs-lookup"><span data-stu-id="18ac7-271">A collection of arbitrary metadata.</span></span>

<span data-ttu-id="18ac7-272">Die Metadaten werden zur Implementierung von übergreifenden Belangen verwendet, die auf Richtlinien und der Konfiguration basieren, die den einzelnen Endpunkten angefügt sind.</span><span class="sxs-lookup"><span data-stu-id="18ac7-272">The metadata is used to implement cross-cutting concerns based on policies and configuration attached to each endpoint.</span></span>

<span data-ttu-id="18ac7-273">Terminalmiddleware kann sehr nützlich sein, erfordert aber möglicherweise auch:</span><span class="sxs-lookup"><span data-stu-id="18ac7-273">Terminal middleware can be an effective tool, but can require:</span></span> <span data-ttu-id="18ac7-274">Einen großen Codierungs- und Testaufwand.</span><span class="sxs-lookup"><span data-stu-id="18ac7-274">A significant amount of coding and testing.</span></span>
* <span data-ttu-id="18ac7-275">Eine manuelle Integration in andere Systeme für die gewünschte Flexibilität.</span><span class="sxs-lookup"><span data-stu-id="18ac7-275">Manual integration with other systems to achieve the desired level of flexibility.</span></span>
* <span data-ttu-id="18ac7-276">Ziehen Sie daher zunächst die Integration von Routingfunktionen in Betracht, bevor Sie damit beginnen, Terminalmiddleware zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="18ac7-276">Consider integrating with routing before writing a terminal middleware.</span></span>
* <span data-ttu-id="18ac7-277">Vorhandene Terminalmiddleware, die in [Map](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) oder <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> integriert ist, kann in der Regel in einen routingfähigen Endpunkt umgewandelt werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-277">Existing terminal middleware that integrates with [Map](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) or <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> can usually be turned into a routing aware endpoint.</span></span> <span data-ttu-id="18ac7-278">[MapHealthChecks](https://github.com/aspnet/AspNetCore/blob/master/src/Middleware/HealthChecks/src/Builder/HealthCheckEndpointRouteBuilderExtensions.cs#L16) zeigt das Muster für eine Routinglösung:</span><span class="sxs-lookup"><span data-stu-id="18ac7-278">[MapHealthChecks](https://github.com/aspnet/AspNetCore/blob/master/src/Middleware/HealthChecks/src/Builder/HealthCheckEndpointRouteBuilderExtensions.cs#L16) demonstrates the pattern for router-ware:</span></span>
* <span data-ttu-id="18ac7-279">Schreiben Sie eine Erweiterungsmethode in der <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>-Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="18ac7-279">Write an extension method on <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>.</span></span>
* <span data-ttu-id="18ac7-280">Erstellen Sie eine geschachtelte Middlewarepipeline mit <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.CreateApplicationBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="18ac7-280">Create a nested middleware pipeline using <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.CreateApplicationBuilder*>.</span></span>
* <span data-ttu-id="18ac7-281">Fügen Sie die Middleware an die neue Pipeline an.</span><span class="sxs-lookup"><span data-stu-id="18ac7-281">Attach the middleware to the new pipeline.</span></span>

<span data-ttu-id="18ac7-282">In diesem Fall <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>.</span><span class="sxs-lookup"><span data-stu-id="18ac7-282">In this case, <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>.</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

<span data-ttu-id="18ac7-283">Verwenden Sie <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.Build*>, um die Middlewarepipeline in einem <xref:Microsoft.AspNetCore.Http.RequestDelegate>-Delegaten zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-283"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.Build*> the middleware pipeline into a <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span> <span data-ttu-id="18ac7-284">Rufen Sie `Map` auf, und stellen Sie die neue Middlewarepipeline bereit.</span><span class="sxs-lookup"><span data-stu-id="18ac7-284">Call `Map` and provide the new middleware pipeline.</span></span> <span data-ttu-id="18ac7-285">Geben Sie das Generatorobjekt zurück, das von `Map` aus der Erweiterungsmethode bereitgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="18ac7-285">Return the builder object provided by `Map` from the extension method.</span></span>

<span data-ttu-id="18ac7-286">Im folgenden Code ist die Verwendung von [MapHealthChecks](xref:host-and-deploy/health-checks) gezeigt:</span><span class="sxs-lookup"><span data-stu-id="18ac7-286">The following code shows use of [MapHealthChecks](xref:host-and-deploy/health-checks):</span></span> <span data-ttu-id="18ac7-287">Das vorangehende Beispiel zeigt, warum das Zurückgeben des Generatorobjekts wichtig ist.</span><span class="sxs-lookup"><span data-stu-id="18ac7-287">The preceding sample shows why returning the builder object is important.</span></span>

<a name="urlm"></a>

### <a name="url-matching"></a><span data-ttu-id="18ac7-288">Wenn das Generatorobjekt zurückgegeben wird, kann der App-Entwickler Richtlinien konfigurieren, z. B. die Autorisierung für den Endpunkt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-288">Returning the builder object allows the app developer to configure policies such as authorization for the endpoint.</span></span>

* <span data-ttu-id="18ac7-289">In diesem Beispiel ist die Middleware für Integritätsprüfungen nicht direkt in das Autorisierungssystem integriert.</span><span class="sxs-lookup"><span data-stu-id="18ac7-289">In this example, the health checks middleware has no direct integration with the authorization system.</span></span>
* <span data-ttu-id="18ac7-290">Das Metadatensystem wurde als Antwort auf die Probleme erstellt, die von Erweiterbarkeitsautoren mithilfe von Terminalmiddleware aufgetreten sind.</span><span class="sxs-lookup"><span data-stu-id="18ac7-290">The metadata system was created in response to the problems encountered by extensibility authors using terminal middleware.</span></span>
* <span data-ttu-id="18ac7-291">Für jede Middleware ist es problematisch, deren eigene Integration in das Autorisierungssystem umzusetzen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-291">It's problematic for each middleware to implement its own integration with the authorization system.</span></span>

<span data-ttu-id="18ac7-292">URL-Zuordnung</span><span class="sxs-lookup"><span data-stu-id="18ac7-292">URL matching</span></span>

* <span data-ttu-id="18ac7-293">Bei diesem Prozess werden eingehende Anforderungen durch Routing mit einem [Endpunkt](#endpoint) abgeglichen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-293">Is the process by which routing matches an incoming request to an [endpoint](#endpoint).</span></span>
* <span data-ttu-id="18ac7-294">Basiert auf Daten im URL-Pfad und den URL-Headern.</span><span class="sxs-lookup"><span data-stu-id="18ac7-294">Is based on data in the URL path and headers.</span></span>

<span data-ttu-id="18ac7-295">Kann erweitert werden, um beliebige Daten in der Anforderung zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-295">Can be extended to consider any data in the request.</span></span> <span data-ttu-id="18ac7-296">Wenn eine Routingmiddleware ausgeführt wird, legt sie ein `Endpoint`-Element fest und leitet Werte an eine [Anforderungsfunktion](xref:fundamentals/request-features) in der <xref:Microsoft.AspNetCore.Http.HttpContext>-Klasse der aktuellen Anforderung weiter:</span><span class="sxs-lookup"><span data-stu-id="18ac7-296">When a routing middleware executes, it sets an `Endpoint` and route values to a [request feature](xref:fundamentals/request-features) on the <xref:Microsoft.AspNetCore.Http.HttpContext> from the current request:</span></span> <span data-ttu-id="18ac7-297">Durch Aufrufen von [HttpContext.GetEndPoint](<xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*>) wird der Endpunkt abgerufen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-297">Calling [HttpContext.GetEndpoint](<xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*>) gets the endpoint.</span></span>

<span data-ttu-id="18ac7-298">`HttpRequest.RouteValues` ruft die Sammlung der Routenwerte ab.</span><span class="sxs-lookup"><span data-stu-id="18ac7-298">`HttpRequest.RouteValues` gets the collection of route values.</span></span> <span data-ttu-id="18ac7-299">[Middleware](xref:fundamentals/middleware/index), die nach der Routingmiddleware ausgeführt wird, kann den Endpunkt erkennen und Maßnahmen ergreifen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-299">[Middleware](xref:fundamentals/middleware/index) running after the routing middleware can inspect the endpoint and take action.</span></span>

* <span data-ttu-id="18ac7-300">So kann beispielsweise eine Autorisierungsmiddleware die Erfassung der Metadaten des Endpunkts für eine Autorisierungsrichtlinie abfragen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-300">For example, an authorization middleware can interrogate the endpoint's metadata collection for an authorization policy.</span></span>

> [!WARNING]
> <span data-ttu-id="18ac7-301">Nachdem die gesamte Middleware in der Anforderungsverarbeitungspipeline ausgeführt wurde, wird der Delegat des ausgewählten Endpunkts aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-301">After all of the middleware in the request processing pipeline is executed, the selected endpoint's delegate is invoked.</span></span>
>
> <span data-ttu-id="18ac7-302">Das Routingsystem ist beim Endpunktrouting für alle Weiterleitungsentscheidungen zuständig.</span><span class="sxs-lookup"><span data-stu-id="18ac7-302">The routing system in endpoint routing is responsible for all dispatching decisions.</span></span>
>
> * <span data-ttu-id="18ac7-303">Da die Middleware Richtlinien auf der Grundlage des ausgewählten Endpunkts anwendet, ist Folgendes wichtig:</span><span class="sxs-lookup"><span data-stu-id="18ac7-303">Because the middleware applies policies based on the selected endpoint, it's important that:</span></span>
> * <span data-ttu-id="18ac7-304">Alle Entscheidungen, die sich auf die Verteilung oder die Anwendung von Sicherheitsrichtlinien auswirken können, werden im Routingsystem getroffen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-304">Any decision that can affect dispatching or the application of security policies is made inside the routing system.</span></span>

<span data-ttu-id="18ac7-305">Wenn für Abwärtskompatibilität ein Controller- oder Razor Pages-Endpunktdelegat ausgeführt wird, werden die Eigenschaften von [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) auf Grundlage der bisher verarbeiteten Anforderungen auf entsprechende Werte festgelegt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-305">For backwards-compatibility, when a Controller or Razor Pages endpoint delegate is executed, the properties of [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) are set to appropriate values based on the request processing performed thus far.</span></span> <span data-ttu-id="18ac7-306">Der Typ `RouteContext` wird in einer zukünftigen Version als veraltet markiert:</span><span class="sxs-lookup"><span data-stu-id="18ac7-306">The `RouteContext` type will be marked obsolete in a future release:</span></span> <span data-ttu-id="18ac7-307">Migrieren Sie `RouteData.Values` zu `HttpRequest.RouteValues`.</span><span class="sxs-lookup"><span data-stu-id="18ac7-307">Migrate `RouteData.Values` to `HttpRequest.RouteValues`.</span></span> <span data-ttu-id="18ac7-308">Migrieren Sie `RouteData.DataTokens`, um [IDataTokensMetadata](xref:Microsoft.AspNetCore.Routing.IDataTokensMetadata) aus den Endpunktmetadaten abzurufen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-308">Migrate `RouteData.DataTokens` to retrieve [IDataTokensMetadata](xref:Microsoft.AspNetCore.Routing.IDataTokensMetadata) from the endpoint metadata.</span></span> <span data-ttu-id="18ac7-309">Der URL-Abgleich erfolgt in mehreren Phasen und kann konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-309">URL matching operates in a configurable set of phases.</span></span> <span data-ttu-id="18ac7-310">In jeder Phase werden mehrere Übereinstimmungen ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="18ac7-310">In each phase, the output is a set of matches.</span></span> <span data-ttu-id="18ac7-311">Diese Übereinstimmungen lassen sich in der nächsten Phase weiter eingrenzen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-311">The set of matches can be narrowed down further by the next phase.</span></span>

1. <span data-ttu-id="18ac7-312">Die Routingimplementierung garantiert keine Verarbeitungsreihenfolge für übereinstimmende Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="18ac7-312">The routing implementation does not guarantee a processing order for matching endpoints.</span></span>
1. <span data-ttu-id="18ac7-313">**Alle** möglichen Übereinstimmungen werden gleichzeitig verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="18ac7-313">**All** possible matches are processed at once.</span></span>
1. <span data-ttu-id="18ac7-314">Für die URL-Abgleichsphasen gilt folgende Reihenfolge.</span><span class="sxs-lookup"><span data-stu-id="18ac7-314">The URL matching phases occur in the following order.</span></span>
1. <span data-ttu-id="18ac7-315">ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="18ac7-315">ASP.NET Core:</span></span>

<span data-ttu-id="18ac7-316">Verarbeitet den URL-Pfad mit mehreren Endpunkten und ihren Routenvorlagen und sammelt **alle** Übereinstimmungen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-316">Processes the URL path against the set of endpoints and their route templates, collecting **all** of the matches.</span></span>

* <span data-ttu-id="18ac7-317">Nimmt die vorangehende Liste und entfernt die Übereinstimmungen, die nicht zu den angewendeten Routeneinschränkungen passen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-317">Takes the preceding list and removes matches that fail with route constraints applied.</span></span>
* <span data-ttu-id="18ac7-318">Nimmt die vorangehende Liste und entfernt die Übereinstimmungen, die nicht zu den [MatcherPolicy](xref:Microsoft.AspNetCore.Routing.MatcherPolicy)-Instanzen passen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-318">Takes the preceding list and removes matches that fail the set of [MatcherPolicy](xref:Microsoft.AspNetCore.Routing.MatcherPolicy) instances.</span></span>

<span data-ttu-id="18ac7-319">Verwendet [EndpointSelector](xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector), um eine abschließende Entscheidung anhand der vorangehenden Liste zu treffen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-319">Uses the [EndpointSelector](xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector) to make a final decision from the preceding list.</span></span> <span data-ttu-id="18ac7-320">Die Liste der Endpunkte wird entsprechend den folgenden Punkten priorisiert:</span><span class="sxs-lookup"><span data-stu-id="18ac7-320">The list of endpoints is prioritized according to:</span></span> <span data-ttu-id="18ac7-321">Die [RouteEndpoint.Order](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order*)-Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="18ac7-321">The [RouteEndpoint.Order](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order*)</span></span> <span data-ttu-id="18ac7-322">Die [Priorität der Routenvorlage](#rtp)</span><span class="sxs-lookup"><span data-stu-id="18ac7-322">The [route template precedence](#rtp)</span></span>

<span data-ttu-id="18ac7-323">Alle übereinstimmenden Endpunkte werden in jeder Phase verarbeitet, bis <xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector> erreicht ist.</span><span class="sxs-lookup"><span data-stu-id="18ac7-323">All matching endpoints are processed in each phase until the <xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector> is reached.</span></span> <span data-ttu-id="18ac7-324">`EndpointSelector` stellt die abschließende Phase dar.</span><span class="sxs-lookup"><span data-stu-id="18ac7-324">The `EndpointSelector` is the final phase.</span></span>

* <span data-ttu-id="18ac7-325">Darin wird der Endpunkt mit der höchsten Priorität aus den Übereinstimmungen als beste Übereinstimmung ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-325">It chooses the highest priority endpoint from the matches as the best match.</span></span>
* <span data-ttu-id="18ac7-326">Wenn es andere Übereinstimmungen mit derselben Priorität wie die beste Übereinstimmung gibt, wird ein Ausnahmefehler wegen einer nicht eindeutigen Übereinstimmung ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="18ac7-326">If there are other matches with the same priority as the best match, an ambiguous match exception is thrown.</span></span>

<span data-ttu-id="18ac7-327">Die Routenpriorität wird anhand einer **spezifischeren** Routenvorlage berechnet, der eine höhere Priorität eingeräumt wird.</span><span class="sxs-lookup"><span data-stu-id="18ac7-327">The route precedence is computed based on a **more specific** route template being given a higher priority.</span></span> <span data-ttu-id="18ac7-328">Dies wird z. B. anhand der Vorlagen `/hello` und `/{message}` deutlich:</span><span class="sxs-lookup"><span data-stu-id="18ac7-328">For example, consider the templates `/hello` and `/{message}`:</span></span>

<span data-ttu-id="18ac7-329">Beide stimmen mit dem URL-Pfad `/hello` überein.</span><span class="sxs-lookup"><span data-stu-id="18ac7-329">Both match the URL path `/hello`.</span></span> <span data-ttu-id="18ac7-330">`/hello` ist spezifischer und hat daher eine höhere Priorität.</span><span class="sxs-lookup"><span data-stu-id="18ac7-330">`/hello`  is more specific and therefore higher priority.</span></span>

* <span data-ttu-id="18ac7-331">Im Allgemeinen eignet sich die Routenpriorität gut, um die beste Übereinstimmung für die in der Praxis verwendeten URL-Schemata zu finden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-331">In general, route precedence does a good job of choosing the best match for the kinds of URL schemes used in practice.</span></span>
* <span data-ttu-id="18ac7-332">Verwenden Sie <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order> nur bei Bedarf, um eine Mehrdeutigkeit zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-332">Use <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order> only when necessary to avoid an ambiguity.</span></span>
* <span data-ttu-id="18ac7-333">Aufgrund der Erweiterungsmöglichkeiten, die das Routing bietet, kann das Routingsystem die mehrdeutigen Routen nicht im Voraus berechnen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-333">Due to the kinds of extensibility provided by routing, it isn't possible for the routing system to compute ahead of time the ambiguous routes.</span></span>
* <span data-ttu-id="18ac7-334">Betrachten Sie ein Beispiel wie die Routenvorlagen `/{message:alpha}` und `/{message:int}`:</span><span class="sxs-lookup"><span data-stu-id="18ac7-334">Consider an example such as the route templates `/{message:alpha}` and `/{message:int}`:</span></span>

> [!WARNING]
>
> <span data-ttu-id="18ac7-335">Die `alpha`-Einschränkung gleicht nur alphabetische Zeichen ab.</span><span class="sxs-lookup"><span data-stu-id="18ac7-335">The `alpha` constraint matches only alphabetic characters.</span></span> <span data-ttu-id="18ac7-336">Die `int`-Einschränkung gleicht nur Zahlen ab.</span><span class="sxs-lookup"><span data-stu-id="18ac7-336">The `int` constraint matches only numbers.</span></span> <span data-ttu-id="18ac7-337">Diese Vorlagen haben die gleiche Routenpriorität, aber es gibt keine einzige URL, auf die sie beide zutreffen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-337">These templates have the same route precedence, but there's no single URL they both match.</span></span>
>
> <span data-ttu-id="18ac7-338">Wenn das Routingsystem beim Starten einen Mehrdeutigkeitsfehler gemeldet hat, würde dieser den gültigen Anwendungsfall blockieren.</span><span class="sxs-lookup"><span data-stu-id="18ac7-338">If the routing system reported an ambiguity error at startup, it would block this valid use case.</span></span> <span data-ttu-id="18ac7-339">Die Reihenfolge der Vorgänge in <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> wirkt sich nicht auf das Routingverhalten aus, mit einer Ausnahme.</span><span class="sxs-lookup"><span data-stu-id="18ac7-339">The order of operations inside <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> doesn't influence the behavior of routing, with one exception.</span></span>
> 
> * <span data-ttu-id="18ac7-340"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> und <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> weisen ihren Endpunkten automatisch einen Reihenfolgenwert zu, basierend auf der Reihenfolge, in der sie aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-340"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span>
> * <span data-ttu-id="18ac7-341">Dadurch wird das Langzeitverhalten von Controllern simuliert, ohne dass das Routingsystem die gleichen Garantien bietet wie ältere Routingimplementierungen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-341">This simulates long-time behavior of controllers without the routing system providing the same guarantees as older routing implementations.</span></span> <span data-ttu-id="18ac7-342">Bei der bisherigen Routingimplementierung war es möglich, eine Routingerweiterung vorzunehmen, die von der Verarbeitungsreihenfolge der Routen abhängt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-342">In the legacy implementation of routing, it's possible to implement routing extensibility that has a dependency on the order in which routes are processed.</span></span>
>
> <span data-ttu-id="18ac7-343">Endpunktrouting in ASP.NET Core 3.0 und höher:</span><span class="sxs-lookup"><span data-stu-id="18ac7-343">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

<a name="rtp"></a>

### <a name="route-template-precedence-and-endpoint-selection-order"></a><span data-ttu-id="18ac7-344">Weist kein Konzept für Routen auf.</span><span class="sxs-lookup"><span data-stu-id="18ac7-344">Doesn't have a concept of routes.</span></span>

<span data-ttu-id="18ac7-345">Bietet keine garantierte Reihenfolge.</span><span class="sxs-lookup"><span data-stu-id="18ac7-345">Doesn't provide ordering guarantees.</span></span> <span data-ttu-id="18ac7-346">Alle Endpunkte werden gleichzeitig verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="18ac7-346">All endpoints are processed at once.</span></span>

* <span data-ttu-id="18ac7-347">Wenn dies bedeutet, dass Sie mit dem Legacy-Routingsystem nicht weiterkommen, [öffnen Sie ein GitHub-Problem zur Unterstützung](https://github.com/dotnet/aspnetcore/issues).</span><span class="sxs-lookup"><span data-stu-id="18ac7-347">If this means you're stuck using the legacy routing system, [open a GitHub issue for assistance](https://github.com/dotnet/aspnetcore/issues).</span></span>
* <span data-ttu-id="18ac7-348">Routenvorlagenpriorität und Reihenfolge der Endpunktauswahl</span><span class="sxs-lookup"><span data-stu-id="18ac7-348">Route template precedence and endpoint selection order</span></span>

<span data-ttu-id="18ac7-349">Die [Routenvorlagenpriorität](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L16) ist ein System, bei dem jeder Routenvorlage ein Wert zugewiesen wird, je nachdem, wie spezifisch diese ist.</span><span class="sxs-lookup"><span data-stu-id="18ac7-349">[Route template precedence](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L16) is a system that assigns each route template a value based on how specific it is.</span></span> <span data-ttu-id="18ac7-350">Routenvorlagenpriorität:</span><span class="sxs-lookup"><span data-stu-id="18ac7-350">Route template precedence:</span></span> <span data-ttu-id="18ac7-351">Verhindert, dass die Reihenfolge der Endpunkte häufig angepasst werden muss.</span><span class="sxs-lookup"><span data-stu-id="18ac7-351">Avoids the need to adjust the order of endpoints in common cases.</span></span>

<span data-ttu-id="18ac7-352">Versucht, die allgemeinen Erwartungen an das Routingverhalten abzugleichen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-352">Attempts to match the common-sense expectations of routing behavior.</span></span>

* <span data-ttu-id="18ac7-353">Dies wird z. B. anhand der Vorlagen `/Products/List` und `/Products/{id}` deutlich.</span><span class="sxs-lookup"><span data-stu-id="18ac7-353">For example, consider templates `/Products/List` and `/Products/{id}`.</span></span>
* <span data-ttu-id="18ac7-354">Es wäre begründet, anzunehmen, dass `/Products/List` eine bessere Übereinstimmung als `/Products/{id}` für den URL-Pfad `/Products/List` ist.</span><span class="sxs-lookup"><span data-stu-id="18ac7-354">It would be reasonable to assume that `/Products/List` is a better match than `/Products/{id}` for the URL path `/Products/List`.</span></span>
* <span data-ttu-id="18ac7-355">Dies funktioniert, weil das Literalsegment `/List` eine höhere Priorität hat als das Parametersegment `/{id}`.</span><span class="sxs-lookup"><span data-stu-id="18ac7-355">The works because the literal segment `/List` is considered to have better precedence than the parameter segment `/{id}`.</span></span>
* <span data-ttu-id="18ac7-356">Wie die Priorisierung im Einzelnen funktioniert, ist an die Definition der Routenvorlagen gekoppelt:</span><span class="sxs-lookup"><span data-stu-id="18ac7-356">The details of how precedence works are coupled to how route templates are defined:</span></span>
* <span data-ttu-id="18ac7-357">Vorlagen mit mehr Segmenten sind in der Regel spezifischer.</span><span class="sxs-lookup"><span data-stu-id="18ac7-357">Templates with more segments are considered more specific.</span></span> <span data-ttu-id="18ac7-358">Ein Segment mit Literaltext gilt als spezifischer als ein Parametersegment.</span><span class="sxs-lookup"><span data-stu-id="18ac7-358">A segment with literal text is considered more specific than a parameter segment.</span></span>

<span data-ttu-id="18ac7-359">Ein Parametersegment mit einer Einschränkung gilt als spezifischer als ein Parametersegment ohne Einschränkung.</span><span class="sxs-lookup"><span data-stu-id="18ac7-359">A parameter segment with a constraint is considered more specific than one without.</span></span>

<a name="lg"></a>

### <a name="url-generation-concepts"></a><span data-ttu-id="18ac7-360">Ein komplexes Segment wird als genauso spezifisch betrachtet wie ein Parametersegment mit einer Einschränkung.</span><span class="sxs-lookup"><span data-stu-id="18ac7-360">A complex segment is considered as specific as a parameter segment with a constraint.</span></span>

<span data-ttu-id="18ac7-361">Catch-All-Parameter, die am wenigsten spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="18ac7-361">Catch-all parameters are the least specific.</span></span>

* <span data-ttu-id="18ac7-362">Unter **Catch-All** in der [Referenz zu Routenvorlagen](#rtr) finden Sie wichtige Informationen zu Catch-All-Routen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-362">See **catch-all** in the [Route template reference](#rtr) for important information on catch-all routes.</span></span>
* <span data-ttu-id="18ac7-363">Einen Verweis auf genaue Werte finden Sie im [Quellcode auf GitHub](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L189).</span><span class="sxs-lookup"><span data-stu-id="18ac7-363">See the [source code on GitHub](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L189) for a reference of exact values.</span></span>

<span data-ttu-id="18ac7-364">Konzepte zur URL-Generierung</span><span class="sxs-lookup"><span data-stu-id="18ac7-364">URL generation concepts</span></span> <span data-ttu-id="18ac7-365">URL-Generierung:</span><span class="sxs-lookup"><span data-stu-id="18ac7-365">URL generation:</span></span> <span data-ttu-id="18ac7-366">Der Prozess, bei dem durch Routing ein URL-Pfad basierend auf mehreren Routenwerten erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="18ac7-366">Is the process by which routing can create a URL path based on a set of route values.</span></span> <span data-ttu-id="18ac7-367">Sie ermöglicht eine logische Trennung zwischen den Endpunkten und den URLs, die auf diese zugreifen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-367">Allows for a logical separation between endpoints and the URLs that access them.</span></span>

<span data-ttu-id="18ac7-368">Das Endpunktrouting umfasst die API zur Linkgenerierung (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>).</span><span class="sxs-lookup"><span data-stu-id="18ac7-368">Endpoint routing includes the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API.</span></span> <span data-ttu-id="18ac7-369">`LinkGenerator` ist ein Singleton-Dienst, der in [DI](xref:fundamentals/dependency-injection) verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="18ac7-369">`LinkGenerator` is a singleton service available from [DI](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="18ac7-370">Die `LinkGenerator`-API kann außerhalb des Kontexts einer ausgeführten Anforderung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-370">The `LinkGenerator` API can be used outside of the context of an executing request.</span></span>

<span data-ttu-id="18ac7-371">[Mvc.IUrlHelper](xref:Microsoft.AspNetCore.Mvc.IUrlHelper) und Szenarios, die von <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> abhängig sind (z. B. [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro), HTML-Hilfsprogramme und [Aktionsergebnisse](xref:mvc/controllers/actions)), verwenden die `LinkGenerator`-API, um entsprechende Funktionen bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-371">[Mvc.IUrlHelper](xref:Microsoft.AspNetCore.Mvc.IUrlHelper) and scenarios that rely on <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), HTML Helpers, and [Action Results](xref:mvc/controllers/actions), use the `LinkGenerator` API internally to provide link generating capabilities.</span></span>

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

<span data-ttu-id="18ac7-372">Die API zur Linkgenerierung wird von Konzepten wie **Adressen** und **Adressschemas** unterstützt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-372">The link generator is backed by the concept of an **address** and **address schemes**.</span></span> <span data-ttu-id="18ac7-373">Sie können mithilfe eines Adressschemas die Endpunkte bestimmen, die bei der Linkgenerierung berücksichtigt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-373">An address scheme is a way of determining the endpoints that should be considered for link generation.</span></span>

<span data-ttu-id="18ac7-374">Beispielsweise werden Routennamen und Routenwerte als Adressschemas implementiert. Diese Szenarios kennen viele Benutzer von Controllern und Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="18ac7-374">For example, the route name and route values scenarios many users are familiar with from controllers and Razor Pages are implemented as an address scheme.</span></span> <span data-ttu-id="18ac7-375">Die API zur Linkgenerierung kann Controller und Razor Pages über die folgenden Erweiterungsmethoden miteinander verknüpfen:</span><span class="sxs-lookup"><span data-stu-id="18ac7-375">The link generator can link to controllers and Razor Pages via the following extension methods:</span></span> <span data-ttu-id="18ac7-376">Beim Überladen dieser Methoden werden Argumente akzeptiert, die den `HttpContext` umfassen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-376">Overloads of these methods accept arguments that include the `HttpContext`.</span></span> <span data-ttu-id="18ac7-377">Diese Methoden sind zwar in funktionaler Hinsicht äquivalent zu [Url.Action](xref:System.Web.Mvc.UrlHelper.Action*) und [Url.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*), bieten aber zusätzliche Flexibilität und Optionen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-377">These methods are functionally equivalent to [Url.Action](xref:System.Web.Mvc.UrlHelper.Action*) and [Url.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*), but offer additional flexibility and options.</span></span>

<span data-ttu-id="18ac7-378">Die `GetPath*`-Methoden sind `Url.Action` und `Url.Page` in der Hinsicht ähnlich, dass sie einen URI mit einem absoluten Pfad generieren.</span><span class="sxs-lookup"><span data-stu-id="18ac7-378">The `GetPath*` methods are most similar to `Url.Action` and `Url.Page`, in that they generate a URI containing an absolute path.</span></span> <span data-ttu-id="18ac7-379">Die `GetUri*`-Methoden generieren immer einen absoluten URI mit einem Schema und einem Host.</span><span class="sxs-lookup"><span data-stu-id="18ac7-379">The `GetUri*` methods always generate an absolute URI containing a scheme and host.</span></span>

1. <span data-ttu-id="18ac7-380">Die Methoden, die einen `HttpContext` akzeptieren, generieren im Kontext der ausgeführten Anforderung einen URI.</span><span class="sxs-lookup"><span data-stu-id="18ac7-380">The methods that accept an `HttpContext` generate a URI in the context of the executing request.</span></span>
1. <span data-ttu-id="18ac7-381">Die [Umgebungsroutenwerte](#ambient), der URL-basierte Pfad, das Schema und der Host von der ausführenden Anforderung werden so lange verwendet, bis sie außer Kraft gesetzt werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-381">The [ambient](#ambient) route values, URL base path, scheme, and host from the executing request are used unless overridden.</span></span> <span data-ttu-id="18ac7-382"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> wird mit einer Adresse aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-382"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> is called with an address.</span></span>

<span data-ttu-id="18ac7-383">Ein URI wird in zwei Schritten generiert:</span><span class="sxs-lookup"><span data-stu-id="18ac7-383">Generating a URI occurs in two steps:</span></span> <span data-ttu-id="18ac7-384">Eine Adresse wird an eine Liste von Endpunkten gebunden, die der Adresse zugeordnet werden können.</span><span class="sxs-lookup"><span data-stu-id="18ac7-384">An address is bound to a list of endpoints that match the address.</span></span>

| <span data-ttu-id="18ac7-385">Jedes <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern> eines Endpunkts wird bewertet, bis ein Routenmuster gefunden wird, das den angegebenen Werten zugeordnet werden kann.</span><span class="sxs-lookup"><span data-stu-id="18ac7-385">Each endpoint's <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern> is evaluated until a route pattern that matches the supplied values is found.</span></span> | <span data-ttu-id="18ac7-386">Die daraus resultierende Ausgabe wird mit URI-Teilen kombiniert, die für die API zur Linkgenerierung bereitgestellt wird, und zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="18ac7-386">The resulting output is combined with the other URI parts supplied to the link generator and returned.</span></span> |
| ---------------- | ----------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | <span data-ttu-id="18ac7-387">Die von <xref:Microsoft.AspNetCore.Routing.LinkGenerator> bereitgestellten Methoden unterstützen die Standardfunktionen zur Generierung von Links für jeden beliebigen Adresstypen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-387">The methods provided by <xref:Microsoft.AspNetCore.Routing.LinkGenerator> support standard link generation capabilities for any type of address.</span></span> |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | <span data-ttu-id="18ac7-388">Am praktischsten ist es, die API zur Linkgenerierung mit Erweiterungsmethoden zu verwenden, die Vorgänge für einen bestimmten Adresstypen ausführen:</span><span class="sxs-lookup"><span data-stu-id="18ac7-388">The most convenient way to use the link generator is through extension methods that perform operations for a specific address type:</span></span>             |

> [!WARNING]
> <span data-ttu-id="18ac7-389">Erweiterungsmethode</span><span class="sxs-lookup"><span data-stu-id="18ac7-389">Extension Method</span></span>
>
> * <span data-ttu-id="18ac7-390">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="18ac7-390">Description</span></span> <span data-ttu-id="18ac7-391">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-391">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-392">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-392">'Blazor'</span></span>
>
> * <span data-ttu-id="18ac7-393">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-393">'Identity'</span></span> <span data-ttu-id="18ac7-394">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-394">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-395">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-395">'Razor'</span></span> <span data-ttu-id="18ac7-396">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-396">'SignalR' uid:</span></span>

### <a name="middleware-example"></a><span data-ttu-id="18ac7-397">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-397">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="18ac7-398">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-398">'Blazor'</span></span> <span data-ttu-id="18ac7-399">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-399">'Identity'</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Middleware/ProductsLinkMiddleware.cs?name=snippet)]

<a name="rtr"></a>

## <a name="route-template-reference"></a><span data-ttu-id="18ac7-400">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-400">'Let's Encrypt'</span></span>

<span data-ttu-id="18ac7-401">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-401">'Razor'</span></span> <span data-ttu-id="18ac7-402">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-402">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-403">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-403">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>  <span data-ttu-id="18ac7-404">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-404">'Blazor'</span></span>

<span data-ttu-id="18ac7-405">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-405">'Identity'</span></span> <span data-ttu-id="18ac7-406">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-406">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-407">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-407">'Razor'</span></span> <span data-ttu-id="18ac7-408">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-408">'SignalR' uid:</span></span>

<span data-ttu-id="18ac7-409">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-409">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

* <span data-ttu-id="18ac7-410">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-410">'Blazor'</span></span>
* <span data-ttu-id="18ac7-411">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-411">'Identity'</span></span> <span data-ttu-id="18ac7-412">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-412">'Let's Encrypt'</span></span>
  * <span data-ttu-id="18ac7-413">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-413">'Razor'</span></span>
  * <span data-ttu-id="18ac7-414">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-414">'SignalR' uid:</span></span>

[!INCLUDE[](~/includes/catchall.md)]

<span data-ttu-id="18ac7-415">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-415">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="18ac7-416">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-416">'Blazor'</span></span> <span data-ttu-id="18ac7-417">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-417">'Identity'</span></span> <span data-ttu-id="18ac7-418">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-418">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-419">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-419">'Razor'</span></span> <span data-ttu-id="18ac7-420">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-420">'SignalR' uid:</span></span>

<span data-ttu-id="18ac7-421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-422">'Blazor'</span></span> <span data-ttu-id="18ac7-423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-423">'Identity'</span></span> <span data-ttu-id="18ac7-424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-424">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-425">'Razor'</span></span>

* `/files/myFile.txt`
* `/files/myFile`

<span data-ttu-id="18ac7-426">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-426">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-427">-------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-427">-------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-428">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-428">'Blazor'</span></span> <span data-ttu-id="18ac7-429">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-429">'Identity'</span></span> <span data-ttu-id="18ac7-430">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-430">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-431">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-431">'Razor'</span></span>

* <span data-ttu-id="18ac7-432">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-432">'SignalR' uid:</span></span>
* <span data-ttu-id="18ac7-433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="18ac7-434">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-434">'Blazor'</span></span> <span data-ttu-id="18ac7-435">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-435">'Identity'</span></span> <span data-ttu-id="18ac7-436">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-436">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-437">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-437">'Razor'</span></span>

<span data-ttu-id="18ac7-438">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-438">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-440">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-440">'Blazor'</span></span>

<span data-ttu-id="18ac7-441">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-441">'Identity'</span></span> <span data-ttu-id="18ac7-442">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-442">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-443">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-443">'Razor'</span></span> <span data-ttu-id="18ac7-444">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-444">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-445">------ | | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Generiert einen URI mit einem absoluten Pfad, der auf den angegebenen Werten basiert.</span><span class="sxs-lookup"><span data-stu-id="18ac7-445">------ | | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Generates a URI with an absolute path based on the provided values.</span></span>

<span data-ttu-id="18ac7-446">| | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Generiert einen absoluten URI, der auf den angegebenen Werten basiert.</span><span class="sxs-lookup"><span data-stu-id="18ac7-446">| | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Generates an absolute URI based on the provided values.</span></span>

| <span data-ttu-id="18ac7-447">Beachten Sie die folgenden Implikationen zum Aufrufen von <xref:Microsoft.AspNetCore.Routing.LinkGenerator>-Methoden:</span><span class="sxs-lookup"><span data-stu-id="18ac7-447">Pay attention to the following implications of calling <xref:Microsoft.AspNetCore.Routing.LinkGenerator> methods:</span></span>                           | <span data-ttu-id="18ac7-448">Verwenden Sie `GetUri*`-Erweiterungsmethoden in App-Konfigurationen, die den `Host`-Header von eingehenden Anforderungen nicht überprüfen, mit Bedacht.</span><span class="sxs-lookup"><span data-stu-id="18ac7-448">Use `GetUri*` extension methods with caution in an app configuration that doesn't validate the `Host` header of incoming requests.</span></span>    | <span data-ttu-id="18ac7-449">Wenn der `Host`-Header von eingehenden Anforderungen nicht überprüft wird, können nicht vertrauenswürdige Anforderungseingaben zurück an den Client in URIs einer Ansicht bzw. Seite zurückgesendet werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-449">If the `Host` header of incoming requests isn't validated, untrusted request input can be sent back to the client in URIs in a view or page.</span></span>                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | <span data-ttu-id="18ac7-450">Es wird empfohlen, dass alle Produktions-Apps ihren Server so konfigurieren, dass der `Host`-Header auf bekannte gültige Werte überprüft wird.</span><span class="sxs-lookup"><span data-stu-id="18ac7-450">We recommend that all production apps configure their server to validate the `Host` header against known valid values.</span></span>                                     |
| `{Page=Home}`                            | `/`                     | <span data-ttu-id="18ac7-451">Verwenden Sie <xref:Microsoft.AspNetCore.Routing.LinkGenerator> in Kombination mit `Map` oder `MapWhen` in Middleware mit Bedacht.</span><span class="sxs-lookup"><span data-stu-id="18ac7-451">Use <xref:Microsoft.AspNetCore.Routing.LinkGenerator> with caution in middleware in combination with `Map` or `MapWhen`.</span></span>                                         |
| `{Page=Home}`                            | `/Contact`              | <span data-ttu-id="18ac7-452">`Map*` ändert den Basispfad der ausgeführten Anforderung. Dies beeinflusst die Ausgabe der Linkgenerierung.</span><span class="sxs-lookup"><span data-stu-id="18ac7-452">`Map*` changes the base path of the executing request, which affects the output of link generation.</span></span>                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | <span data-ttu-id="18ac7-453">Für alle <xref:Microsoft.AspNetCore.Routing.LinkGenerator>-APIs ist die Angabe eines Basispfads zulässig.</span><span class="sxs-lookup"><span data-stu-id="18ac7-453">All of the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> APIs allow specifying a base path.</span></span>                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | <span data-ttu-id="18ac7-454">Geben Sie einen leeren Basispfad an, um die Auswirkungen von `Map*` auf die Linkgenerierung rückgängig zu machen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-454">Specify an empty base path to undo the `Map*` affect on link generation.</span></span> |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | <span data-ttu-id="18ac7-455">Middlewarebeispiel</span><span class="sxs-lookup"><span data-stu-id="18ac7-455">Middleware example</span></span> <span data-ttu-id="18ac7-456">Im folgenden Beispiel verwendet eine Middleware die <xref:Microsoft.AspNetCore.Routing.LinkGenerator>-API, um eine Verknüpfung zu einer Aktionsmethode herzustellen, die Speicherprodukte aufführt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-456">In the following example, a middleware uses the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API to create a link to an action method that lists store products.</span></span>        |
| `{controller=Home}/{action=Index}/{id?}` | `/Products`         | <span data-ttu-id="18ac7-457">Sie können für jede beliebige Klasse in einer App die API zur Linkgenerierung verwenden, indem Sie diese in eine Klasse einfügen und `GenerateLink` aufrufen:</span><span class="sxs-lookup"><span data-stu-id="18ac7-457">Using the link generator by injecting it into a class and calling `GenerateLink` is available to any class in an app:</span></span> <span data-ttu-id="18ac7-458">Referenz für Routenvorlagen</span><span class="sxs-lookup"><span data-stu-id="18ac7-458">Route template reference</span></span>        |

<span data-ttu-id="18ac7-459">Token in `{}` definieren Routenparameter, die beim Abgleich der Route gebunden werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-459">Tokens within `{}` define route parameters that are bound if the route is matched.</span></span> <span data-ttu-id="18ac7-460">In einem Routensegment können mehrere Routenparameter definiert werden, müssen aber durch einen Literalwert getrennt werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-460">More than one route parameter can be defined in a route segment, but route parameters  must be separated by a literal value.</span></span>

### <a name="complex-segments"></a><span data-ttu-id="18ac7-461">`{controller=Home}{action=Index}` ist z.B. keine gültige Route, da sich zwischen `{controller}` und `{action}` kein Literalwert befindet.</span><span class="sxs-lookup"><span data-stu-id="18ac7-461">For example, `{controller=Home}{action=Index}` isn't a valid route, since there's no literal value between `{controller}` and `{action}`.</span></span>

<span data-ttu-id="18ac7-462">Routenparameter müssen einen Namen besitzen und können zusätzliche Attribute aufweisen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-462">Route parameters must have a name and may have additional attributes specified.</span></span> <span data-ttu-id="18ac7-463">Eine Literalzeichenfolge, die nicht den Routenparametern entspricht (z.B. `{id}`), muss zusammen mit dem Pfadtrennzeichen `/` mit dem URL-Text übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-463">Literal text other than route parameters (for example, `{id}`) and the path separator `/` must match the text in the URL.</span></span>
<span data-ttu-id="18ac7-464">Beim Abgleich von Text wird nicht zwischen Groß-/Kleinbuchstaben unterschieden, und die Übereinstimmung basiert auf der decodierten Repräsentation des URL-Pfads.</span><span class="sxs-lookup"><span data-stu-id="18ac7-464">Text matching is case-insensitive and based on the decoded representation of the URL's path.</span></span> <span data-ttu-id="18ac7-465">Damit das Trennzeichen (`{` oder `}`) der Routenparameter-Literalzeichenfolge bei einem Abgleich gefunden wird, muss es doppelt vorhanden sein, was einem Escapezeichen entspricht.</span><span class="sxs-lookup"><span data-stu-id="18ac7-465">To match a literal route parameter delimiter `{` or `}`, escape the delimiter by repeating the character.</span></span> <span data-ttu-id="18ac7-466">Beispielsweise `{{` oder `}}`.</span><span class="sxs-lookup"><span data-stu-id="18ac7-466">For example `{{` or `}}`.</span></span>

[!INCLUDE[](~/includes/regex.md)]

<span data-ttu-id="18ac7-467">Sternchen `*` oder Doppelsternchen `**`:</span><span class="sxs-lookup"><span data-stu-id="18ac7-467">Asterisk `*` or double asterisk `**`:</span></span> <span data-ttu-id="18ac7-468">Können als Präfix für einen Routenparameter verwendet werden, um eine Bindung zum verbleibenden Teil des URI herzustellen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-468">Can be used as a prefix to a route parameter to bind to the rest of the URI.</span></span>

* <span data-ttu-id="18ac7-469">Werden **Catch-All**-Parameter genannt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-469">Are called a **catch-all** parameters.</span></span> <span data-ttu-id="18ac7-470">Zum Beispiel `blog/{**slug}`:</span><span class="sxs-lookup"><span data-stu-id="18ac7-470">For example, `blog/{**slug}`:</span></span>
* <span data-ttu-id="18ac7-471">Entspricht einem beliebigen URI, der mit `/blog` beginnt und einem beliebigen Wert folgt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-471">Matches any URI that starts with `/blog` and has any value following it.</span></span>
* <span data-ttu-id="18ac7-472">Der Wert nach `/blog` wird dem [Slug](https://developer.mozilla.org/docs/Glossary/Slug)-Routenwert zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-472">The value following `/blog` is assigned to the [slug](https://developer.mozilla.org/docs/Glossary/Slug) route value.</span></span> <span data-ttu-id="18ac7-473">Durch Catch-All-Parameter können auch leere Zeichenfolgen gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-473">Catch-all parameters can also match the empty string.</span></span>
* <span data-ttu-id="18ac7-474">Der Catch-All-Parameter schützt die entsprechenden Zeichen (Escaping), wenn die Route verwendet wird, um eine URL, einschließlich Pfadtrennzeichen (`/`) zu generieren.</span><span class="sxs-lookup"><span data-stu-id="18ac7-474">The catch-all parameter escapes the appropriate characters when the route is used to generate a URL, including path separator `/` characters.</span></span>
* <span data-ttu-id="18ac7-475">Z.B. generiert die Route `foo/{*path}` mit den Routenwerten `{ path = "my/path" }``foo/my%2Fpath`.</span><span class="sxs-lookup"><span data-stu-id="18ac7-475">For example, the route `foo/{*path}` with route values `{ path = "my/path" }` generates `foo/my%2Fpath`.</span></span>

<span data-ttu-id="18ac7-476">Beachten Sie den umgekehrten Schrägstrich mit Escapezeichen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-476">Note the escaped forward slash.</span></span> <span data-ttu-id="18ac7-477">Um Trennzeichen für Roundtrips einsetzen zu können, verwenden Sie das Routenparameterpräfix `**`.</span><span class="sxs-lookup"><span data-stu-id="18ac7-477">To round-trip path separator characters, use the `**` route parameter prefix.</span></span> <span data-ttu-id="18ac7-478">Die Route `foo/{**path}` mit `{ path = "my/path" }` generiert `foo/my/path`.</span><span class="sxs-lookup"><span data-stu-id="18ac7-478">The route `foo/{**path}` with `{ path = "my/path" }` generates `foo/my/path`.</span></span>
* <span data-ttu-id="18ac7-479">Bei einem URL-Muster, durch das ein Dateiname mit einer optionalen Erweiterung erfasst werden soll, sind noch weitere Aspekte zu berücksichtigen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-479">URL patterns that attempt to capture a file name with an optional file extension have additional considerations.</span></span> <span data-ttu-id="18ac7-480">Dies wird z.B. anhand der Vorlage `files/{filename}.{ext?}` deutlich.</span><span class="sxs-lookup"><span data-stu-id="18ac7-480">For example, consider the template `files/{filename}.{ext?}`.</span></span>
* <span data-ttu-id="18ac7-481">Wenn sowohl für `filename` als auch für `ext` Werte vorhanden sind, werden beide Werte angegeben.</span><span class="sxs-lookup"><span data-stu-id="18ac7-481">When values for both `filename` and `ext` exist, both values are populated.</span></span>
* <span data-ttu-id="18ac7-482">Wenn nur für `filename` ein Wert in der URL vorhanden ist, wird für die Route eine Übereinstimmung ermittelt, da der nachstehende Punkt (`.`) optional ist.</span><span class="sxs-lookup"><span data-stu-id="18ac7-482">If only a value for `filename` exists in the URL, the route matches because the trailing `.` is  optional.</span></span> <span data-ttu-id="18ac7-483">Für die folgenden URLs wird eine Übereinstimmung für die Route ermittelt:</span><span class="sxs-lookup"><span data-stu-id="18ac7-483">The following URLs match this route:</span></span>
* <span data-ttu-id="18ac7-484">Routenparameter können über mehrere **Standardwerte** verfügen, die nach dem Parameternamen angegeben werden und durch ein Gleichheitszeichen (`=`) voneinander getrennt werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-484">Route parameters may have **default values** designated by specifying the default value after the parameter name separated by an equals sign (`=`).</span></span>
* <span data-ttu-id="18ac7-485">Mit `{controller=Home}` wird beispielsweise `Home` als Standardwert für `controller` definiert.</span><span class="sxs-lookup"><span data-stu-id="18ac7-485">For example, `{controller=Home}` defines `Home` as the default value for `controller`.</span></span>

<span data-ttu-id="18ac7-486">Der Standardwert wird verwendet, wenn kein Wert in der Parameter-URL vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="18ac7-486">The default value is used if no value is present in the URL for the parameter.</span></span>

* <span data-ttu-id="18ac7-487">Routenparameter sind optional, wenn am Ende des Parameternamens ein Fragezeichen (`?`) angefügt wird.</span><span class="sxs-lookup"><span data-stu-id="18ac7-487">Route parameters are made optional by appending a question mark (`?`) to the end of the parameter name.</span></span>
* <span data-ttu-id="18ac7-488">Beispielsweise `id?`.</span><span class="sxs-lookup"><span data-stu-id="18ac7-488">For example, `id?`.</span></span>

<span data-ttu-id="18ac7-489">Zwischen optionalen Werten und Standardroutenparametern besteht folgender Unterschied:</span><span class="sxs-lookup"><span data-stu-id="18ac7-489">The difference between optional values and default route parameters is:</span></span>

<a name="greedy"></a>

<span data-ttu-id="18ac7-490">Ein Routenparameter mit einem Standardwert erzeugt immer einen Wert.</span><span class="sxs-lookup"><span data-stu-id="18ac7-490">A route parameter with a default value always produces a value.</span></span> <span data-ttu-id="18ac7-491">Ein optionaler Parameter hat nur dann einen Wert, wenn ein Wert von der Anforderungs-URL bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="18ac7-491">An optional parameter has a value only when a value is provided by the request URL.</span></span>

## <a name="route-constraint-reference"></a><span data-ttu-id="18ac7-492">Routenparameter können Einschränkungen aufweisen, die mit dem gebundenen Routenwert der URL übereinstimmen müssen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-492">Route parameters may have constraints that must match the route value bound from the URL.</span></span>

<span data-ttu-id="18ac7-493">Eine Inline-Einschränkung für einen Routenparameter geben Sie an, indem Sie hinter dem Namen des Routenparameters einen Doppelpunkt (`:`) und einen Einschränkungsnamen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-493">Adding `:` and constraint name after the route parameter name specifies an inline constraint on a route parameter.</span></span> <span data-ttu-id="18ac7-494">Wenn für die Einschränkung Argumente erforderlich sind, werden diese nach dem Einschränkungsnamen in Klammern (`(...)`) eingeschlossen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-494">If the constraint requires arguments, they're enclosed in parentheses `(...)` after the constraint name.</span></span> <span data-ttu-id="18ac7-495">Mehrere *Inline-Einschränkungen* können festgelegt werden, indem ein weiterer Doppelpunkt (`:`) und Einschränkungsname hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-495">Multiple *inline constraints* can be specified by appending another `:` and constraint name.</span></span> <span data-ttu-id="18ac7-496">Der Einschränkungsname und die Argumente werden dem <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver>-Dienst übergeben, wodurch eine Instanz von <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> für die URL-Verarbeitung erstellt werden kann.</span><span class="sxs-lookup"><span data-stu-id="18ac7-496">The constraint name and arguments are passed to the <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> service to create an instance of <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> to use in URL processing.</span></span> <span data-ttu-id="18ac7-497">In der Routenvorlage `blog/{article:minlength(10)}` wird beispielsweise die Einschränkung `minlength` mit dem Argument `10` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-497">For example, the route template `blog/{article:minlength(10)}` specifies a `minlength` constraint with the argument `10`.</span></span>

> [!WARNING]
> <span data-ttu-id="18ac7-498">Weitere Informationen zu Routeneinschränkungen und eine Liste der vom Framework bereitgestellten Einschränkungen finden Sie im Abschnitt [Referenz zu Routeneinschränkungen](#route-constraint-reference).</span><span class="sxs-lookup"><span data-stu-id="18ac7-498">For more information on route constraints and a list of the constraints provided by the framework, see the [Route constraint reference](#route-constraint-reference) section.</span></span> <span data-ttu-id="18ac7-499">Routenparameter können darüber hinaus über Parametertransformatoren verfügen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-499">Route parameters may also have parameter transformers.</span></span> <span data-ttu-id="18ac7-500">Diese wandeln den Wert eines Parameters beim Generieren von Links um und passen Aktionen und Seiten an URLs an.</span><span class="sxs-lookup"><span data-stu-id="18ac7-500">Parameter transformers transform a parameter's value when generating links and matching actions and pages to URLs.</span></span> <span data-ttu-id="18ac7-501">Wie Einschränkungen können auch Parametertransformatoren einem Routenparameter inline hinzugefügt werden, indem ein Doppelpunkt (`:`) und der Name des Transformators hinter dem Namen des Routenparameters hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-501">Like constraints, parameter transformers can be added inline to a route parameter by adding a `:` and transformer name after the route parameter name.</span></span>

<span data-ttu-id="18ac7-502">In der Routenvorlage `blog/{article:slugify}` wird beispielsweise der Transformator `slugify` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-502">For example, the route template `blog/{article:slugify}` specifies a `slugify` transformer.</span></span>

| <span data-ttu-id="18ac7-503">Weitere Informationen zu Parametertransformatoren finden Sie im Abschnitt [Parametertransformatorreferenz](#parameter-transformer-reference).</span><span class="sxs-lookup"><span data-stu-id="18ac7-503">For more information on parameter transformers, see the [Parameter transformer reference](#parameter-transformer-reference) section.</span></span> | <span data-ttu-id="18ac7-504">Die folgende Tabelle enthält Beispielvorlagen für Routen und deren Verhalten:</span><span class="sxs-lookup"><span data-stu-id="18ac7-504">The following table demonstrates example route templates and their behavior:</span></span> | <span data-ttu-id="18ac7-505">Routenvorlage</span><span class="sxs-lookup"><span data-stu-id="18ac7-505">Route Template</span></span> | <span data-ttu-id="18ac7-506">Beispiel-URI für Übereinstimmung</span><span class="sxs-lookup"><span data-stu-id="18ac7-506">Example Matching URI</span></span> |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | <span data-ttu-id="18ac7-507">Der Anforderungs-URI&hellip;</span><span class="sxs-lookup"><span data-stu-id="18ac7-507">The request URI&hellip;</span></span> | <span data-ttu-id="18ac7-508">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-508">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> |
| `bool` | `{active:bool}` | <span data-ttu-id="18ac7-509">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-509">'Blazor'</span></span> | <span data-ttu-id="18ac7-510">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-510">'Identity'</span></span> <span data-ttu-id="18ac7-511">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-511">'Let's Encrypt'</span></span> |
| `datetime` | `{dob:datetime}` | <span data-ttu-id="18ac7-512">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-512">'Razor'</span></span> | <span data-ttu-id="18ac7-513">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-513">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-514">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-514">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> |
| `decimal` | `{price:decimal}` | <span data-ttu-id="18ac7-515">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-515">'Blazor'</span></span> | <span data-ttu-id="18ac7-516">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-516">'Identity'</span></span> <span data-ttu-id="18ac7-517">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-517">'Let's Encrypt'</span></span>|
| `double` | `{weight:double}` | <span data-ttu-id="18ac7-518">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-518">'Razor'</span></span> | <span data-ttu-id="18ac7-519">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-519">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-520">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-520">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>|
| `float` | `{weight:float}` | <span data-ttu-id="18ac7-521">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-521">'Blazor'</span></span> | <span data-ttu-id="18ac7-522">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-522">'Identity'</span></span> <span data-ttu-id="18ac7-523">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-523">'Let's Encrypt'</span></span>|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638` | <span data-ttu-id="18ac7-524">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-524">'Razor'</span></span> |
| `long` | `{ticks:long}` | <span data-ttu-id="18ac7-525">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-525">'SignalR' uid:</span></span> | <span data-ttu-id="18ac7-526">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-526">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | <span data-ttu-id="18ac7-527">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-527">'Blazor'</span></span> |
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | <span data-ttu-id="18ac7-528">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-528">'Identity'</span></span> |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | <span data-ttu-id="18ac7-529">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-529">'Let's Encrypt'</span></span> |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | <span data-ttu-id="18ac7-530">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-530">'Razor'</span></span> |
| `min(value)` | `{age:min(18)}` | `19` | <span data-ttu-id="18ac7-531">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-531">'SignalR' uid:</span></span> |
| `max(value)` | `{age:max(120)}` | `91` | <span data-ttu-id="18ac7-532">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-532">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> |
| `range(min,max)` | `{age:range(18,120)}` | `91` | <span data-ttu-id="18ac7-533">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-533">'Blazor'</span></span> |
| `alpha` | `{name:alpha}` | `Rick` | <span data-ttu-id="18ac7-534">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-534">'Identity'</span></span> |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | <span data-ttu-id="18ac7-535">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-535">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-536">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-536">'Razor'</span></span> |
| `required` | `{name:required}` | `Rick` | <span data-ttu-id="18ac7-537">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-537">'SignalR' uid:</span></span> |

[!INCLUDE[](~/includes/regex.md)]

<span data-ttu-id="18ac7-538">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-538">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-539">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-539">'Blazor'</span></span>

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> <span data-ttu-id="18ac7-540">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-540">'Identity'</span></span> <span data-ttu-id="18ac7-541">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-541">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-542">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-542">'Razor'</span></span> <span data-ttu-id="18ac7-543">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-543">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-544">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-544">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-545">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-545">'Blazor'</span></span>

### <a name="regular-expressions-in-constraints"></a><span data-ttu-id="18ac7-546">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-546">'Identity'</span></span>

[!INCLUDE[](~/includes/regex.md)]

<span data-ttu-id="18ac7-547">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-547">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-548">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-548">'Razor'</span></span> <span data-ttu-id="18ac7-549">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-549">'SignalR' uid:</span></span>

<span data-ttu-id="18ac7-550">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-550">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex.cs?name=snippet)]

<span data-ttu-id="18ac7-551">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-551">'Blazor'</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex2.cs?name=snippet)]

<span data-ttu-id="18ac7-552">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-552">'Identity'</span></span> <span data-ttu-id="18ac7-553">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-553">'Let's Encrypt'</span></span>

<span data-ttu-id="18ac7-554">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-554">'Razor'</span></span> <span data-ttu-id="18ac7-555">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-555">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-556">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-556">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

* <span data-ttu-id="18ac7-557">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-557">'Blazor'</span></span>
* <span data-ttu-id="18ac7-558">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-558">'Identity'</span></span>

<span data-ttu-id="18ac7-559">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-559">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-560">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-560">'Razor'</span></span>

| <span data-ttu-id="18ac7-561">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-561">'SignalR' uid:</span></span>    | <span data-ttu-id="18ac7-562">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-562">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

<span data-ttu-id="18ac7-563">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-563">'Blazor'</span></span> <span data-ttu-id="18ac7-564">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-564">'Identity'</span></span> <span data-ttu-id="18ac7-565">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-565">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-566">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-566">'Razor'</span></span> <span data-ttu-id="18ac7-567">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-567">'SignalR' uid:</span></span>

| <span data-ttu-id="18ac7-568">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-568">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>   | <span data-ttu-id="18ac7-569">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-569">'Blazor'</span></span>    | <span data-ttu-id="18ac7-570">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-570">'Identity'</span></span> | <span data-ttu-id="18ac7-571">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-571">'Let's Encrypt'</span></span>               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | <span data-ttu-id="18ac7-572">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-572">'Razor'</span></span>     | <span data-ttu-id="18ac7-573">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-573">'SignalR' uid:</span></span>   | <span data-ttu-id="18ac7-574">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-574">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>     |
| `[a-z]{2}`   | <span data-ttu-id="18ac7-575">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-575">'Blazor'</span></span> | <span data-ttu-id="18ac7-576">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-576">'Identity'</span></span>   | <span data-ttu-id="18ac7-577">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-577">'Let's Encrypt'</span></span>     |
| `[a-z]{2}`   | <span data-ttu-id="18ac7-578">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-578">'Razor'</span></span>        | <span data-ttu-id="18ac7-579">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-579">'SignalR' uid:</span></span>   | <span data-ttu-id="18ac7-580">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-580">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>    |
| `[a-z]{2}`   | <span data-ttu-id="18ac7-581">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-581">'Blazor'</span></span>        | <span data-ttu-id="18ac7-582">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-582">'Identity'</span></span>   | <span data-ttu-id="18ac7-583">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-583">'Let's Encrypt'</span></span>    |
| `^[a-z]{2}$` | <span data-ttu-id="18ac7-584">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-584">'Razor'</span></span>     | <span data-ttu-id="18ac7-585">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-585">'SignalR' uid:</span></span>    | <span data-ttu-id="18ac7-586">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-586">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> |
| `^[a-z]{2}$` | <span data-ttu-id="18ac7-587">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-587">'Blazor'</span></span> | <span data-ttu-id="18ac7-588">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-588">'Identity'</span></span>    | <span data-ttu-id="18ac7-589">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-589">'Let's Encrypt'</span></span> |

<span data-ttu-id="18ac7-590">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-590">'Razor'</span></span>

<span data-ttu-id="18ac7-591">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-591">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-592">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-592">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-593">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-593">'Blazor'</span></span> <span data-ttu-id="18ac7-594">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-594">'Identity'</span></span> <span data-ttu-id="18ac7-595">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-595">'Let's Encrypt'</span></span>

### <a name="custom-route-constraints"></a><span data-ttu-id="18ac7-596">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-596">'Razor'</span></span>

<span data-ttu-id="18ac7-597">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-597">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-598">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-598">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="18ac7-599">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-599">'Blazor'</span></span> <span data-ttu-id="18ac7-600">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-600">'Identity'</span></span>

<span data-ttu-id="18ac7-601">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-601">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-602">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-602">'Razor'</span></span>

<span data-ttu-id="18ac7-603">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-603">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-604">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-604">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-605">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-605">'Blazor'</span></span> <span data-ttu-id="18ac7-606">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-606">'Identity'</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet)]

<span data-ttu-id="18ac7-607">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-607">'Let's Encrypt'</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet&highlight=6,13)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

<span data-ttu-id="18ac7-608">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-608">'Razor'</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

<span data-ttu-id="18ac7-609">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-609">'SignalR' uid:</span></span>

* <span data-ttu-id="18ac7-610">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-610">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="18ac7-611">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-611">'Blazor'</span></span> <span data-ttu-id="18ac7-612">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-612">'Identity'</span></span>

<span data-ttu-id="18ac7-613">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-613">'Let's Encrypt'</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="18ac7-614">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-614">'Razor'</span></span>

* <span data-ttu-id="18ac7-615">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-615">'SignalR' uid:</span></span>
* <span data-ttu-id="18ac7-616">-------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-616">-------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

## <a name="parameter-transformer-reference"></a><span data-ttu-id="18ac7-617">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-617">'Blazor'</span></span>

<span data-ttu-id="18ac7-618">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-618">'Identity'</span></span>

* <span data-ttu-id="18ac7-619">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-619">'Let's Encrypt'</span></span>
* <span data-ttu-id="18ac7-620">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-620">'Razor'</span></span>
* <span data-ttu-id="18ac7-621">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-621">'SignalR' uid:</span></span>
* <span data-ttu-id="18ac7-622">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-622">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="18ac7-623">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-623">'Blazor'</span></span>

<span data-ttu-id="18ac7-624">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-624">'Identity'</span></span>

<span data-ttu-id="18ac7-625">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-625">'Let's Encrypt'</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet2)]

<span data-ttu-id="18ac7-626">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-626">'Razor'</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet)]

<span data-ttu-id="18ac7-627">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-627">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-628">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-628">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

```csharp
routes.MapControllerRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

<span data-ttu-id="18ac7-629">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-629">'Blazor'</span></span> <span data-ttu-id="18ac7-630">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-630">'Identity'</span></span> <span data-ttu-id="18ac7-631">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-631">'Let's Encrypt'</span></span>

<span data-ttu-id="18ac7-632">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-632">'Razor'</span></span>

* <span data-ttu-id="18ac7-633">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-633">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-634">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-634">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-635">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-635">'Blazor'</span></span>
* <span data-ttu-id="18ac7-636">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-636">'Identity'</span></span> <span data-ttu-id="18ac7-637">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-637">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-638">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-638">'Razor'</span></span> <span data-ttu-id="18ac7-639">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-639">'SignalR' uid:</span></span>

<a name="ugr"></a>

## <a name="url-generation-reference"></a><span data-ttu-id="18ac7-640">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-640">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="18ac7-641">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-641">'Blazor'</span></span> <span data-ttu-id="18ac7-642">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-642">'Identity'</span></span> <span data-ttu-id="18ac7-643">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-643">'Let's Encrypt'</span></span>

<span data-ttu-id="18ac7-644">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-644">'Razor'</span></span> <span data-ttu-id="18ac7-645">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-645">'SignalR' uid:</span></span>

<span data-ttu-id="18ac7-646">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-646">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="18ac7-647">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-647">'Blazor'</span></span> <span data-ttu-id="18ac7-648">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-648">'Identity'</span></span>

### <a name="troubleshooting-url-generation-with-logging"></a><span data-ttu-id="18ac7-649">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-649">'Let's Encrypt'</span></span>

<span data-ttu-id="18ac7-650">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-650">'Razor'</span></span> <span data-ttu-id="18ac7-651">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-651">'SignalR' uid:</span></span>

<span data-ttu-id="18ac7-652">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-652">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

### <a name="addresses"></a><span data-ttu-id="18ac7-653">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-653">'Blazor'</span></span>

<span data-ttu-id="18ac7-654">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-654">'Identity'</span></span>

<span data-ttu-id="18ac7-655">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-655">'Let's Encrypt'</span></span>

* <span data-ttu-id="18ac7-656">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-656">'Razor'</span></span>
    * <span data-ttu-id="18ac7-657">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-657">'SignalR' uid:</span></span>
    * <span data-ttu-id="18ac7-658">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-658">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
    * <span data-ttu-id="18ac7-659">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-659">'Blazor'</span></span>
    * <span data-ttu-id="18ac7-660">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-660">'Identity'</span></span>
    * <span data-ttu-id="18ac7-661">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-661">'Let's Encrypt'</span></span>
* <span data-ttu-id="18ac7-662">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-662">'Razor'</span></span>
    * <span data-ttu-id="18ac7-663">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-663">'SignalR' uid:</span></span>
    * <span data-ttu-id="18ac7-664">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-664">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
    * <span data-ttu-id="18ac7-665">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-665">'Blazor'</span></span>

<span data-ttu-id="18ac7-666">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-666">'Identity'</span></span>

* <span data-ttu-id="18ac7-667">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-667">'Let's Encrypt'</span></span>
* <span data-ttu-id="18ac7-668">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-668">'Razor'</span></span>

<a name="ambient"></a>

### <a name="ambient-values-and-explicit-values"></a><span data-ttu-id="18ac7-669">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-669">'SignalR' uid:</span></span>

<span data-ttu-id="18ac7-670">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-670">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-671">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-671">'Blazor'</span></span> <span data-ttu-id="18ac7-672">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-672">'Identity'</span></span>

<span data-ttu-id="18ac7-673">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-673">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-674">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-674">'Razor'</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet)]

<span data-ttu-id="18ac7-675">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-675">'SignalR' uid:</span></span>

* <span data-ttu-id="18ac7-676">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-676">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="18ac7-677">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-677">'Blazor'</span></span>

<span data-ttu-id="18ac7-678">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-678">'Identity'</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet2)]

<span data-ttu-id="18ac7-679">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-679">'Let's Encrypt'</span></span>

<span data-ttu-id="18ac7-680">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-680">'Razor'</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet3)]

<span data-ttu-id="18ac7-681">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-681">'SignalR' uid:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/GadgetController.cs?name=snippet)]

<span data-ttu-id="18ac7-682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

* <span data-ttu-id="18ac7-683">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-683">'Blazor'</span></span>
* <span data-ttu-id="18ac7-684">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-684">'Identity'</span></span>
* <xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*>   
<span data-ttu-id="18ac7-685">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-685">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-686">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-686">'Razor'</span></span>

<span data-ttu-id="18ac7-687">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-687">'SignalR' uid:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="18ac7-688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

 `@page "{id:int}"`

<span data-ttu-id="18ac7-689">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-689">'Blazor'</span></span>

<span data-ttu-id="18ac7-690">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-690">'Identity'</span></span>

* <span data-ttu-id="18ac7-691">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-691">'Let's Encrypt'</span></span>
* <span data-ttu-id="18ac7-692">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-692">'Razor'</span></span>
* <span data-ttu-id="18ac7-693">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-693">'SignalR' uid:</span></span> <!--by the user-->
* <span data-ttu-id="18ac7-694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="18ac7-695">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-695">'Blazor'</span></span> <span data-ttu-id="18ac7-696">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-696">'Identity'</span></span>

<span data-ttu-id="18ac7-697">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-697">'Let's Encrypt'</span></span>

### <a name="url-generation-process"></a><span data-ttu-id="18ac7-698">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-698">'Razor'</span></span>

<span data-ttu-id="18ac7-699">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-699">'SignalR' uid:</span></span>

* <span data-ttu-id="18ac7-700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="18ac7-701">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-701">'Blazor'</span></span>

<span data-ttu-id="18ac7-702">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-702">'Identity'</span></span>  <span data-ttu-id="18ac7-703">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-703">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-704">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-704">'Razor'</span></span>

<span data-ttu-id="18ac7-705">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-705">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

* <span data-ttu-id="18ac7-707">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-707">'Blazor'</span></span>
* <span data-ttu-id="18ac7-708">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-708">'Identity'</span></span>
* <span data-ttu-id="18ac7-709">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-709">'Let's Encrypt'</span></span>
* <span data-ttu-id="18ac7-710">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-710">'Razor'</span></span>

<span data-ttu-id="18ac7-711">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-711">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="18ac7-713">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-713">'Blazor'</span></span> <span data-ttu-id="18ac7-714">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-714">'Identity'</span></span> <span data-ttu-id="18ac7-715">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-715">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-716">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-716">'Razor'</span></span> <span data-ttu-id="18ac7-717">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-717">'SignalR' uid:</span></span>

* <span data-ttu-id="18ac7-718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="18ac7-719">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-719">'Blazor'</span></span>

* <span data-ttu-id="18ac7-720">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-720">'Identity'</span></span> <span data-ttu-id="18ac7-721">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-721">'Let's Encrypt'</span></span>
* <span data-ttu-id="18ac7-722">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-722">'Razor'</span></span> <span data-ttu-id="18ac7-723">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-723">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>  <span data-ttu-id="18ac7-725">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-725">'Blazor'</span></span>
* <span data-ttu-id="18ac7-726">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-726">'Identity'</span></span> <span data-ttu-id="18ac7-727">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-727">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-728">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-728">'Razor'</span></span>

<span data-ttu-id="18ac7-729">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-729">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-731">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-731">'Blazor'</span></span>

* <span data-ttu-id="18ac7-732">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-732">'Identity'</span></span>
* <span data-ttu-id="18ac7-733">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-733">'Let's Encrypt'</span></span>

<span data-ttu-id="18ac7-734">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-734">'Razor'</span></span> <span data-ttu-id="18ac7-735">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-735">'SignalR' uid:</span></span>

<span data-ttu-id="18ac7-736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

* <span data-ttu-id="18ac7-737">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-737">'Blazor'</span></span>
* <span data-ttu-id="18ac7-738">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-738">'Identity'</span></span>
    * <span data-ttu-id="18ac7-739">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-739">'Let's Encrypt'</span></span>
    * <span data-ttu-id="18ac7-740">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-740">'Razor'</span></span>
    * <span data-ttu-id="18ac7-741">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-741">'SignalR' uid:</span></span>
    * <span data-ttu-id="18ac7-742">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-742">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="18ac7-743">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-743">'Blazor'</span></span> <span data-ttu-id="18ac7-744">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-744">'Identity'</span></span> <span data-ttu-id="18ac7-745">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-745">'Let's Encrypt'</span></span>

<span data-ttu-id="18ac7-746">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-746">'Razor'</span></span> <span data-ttu-id="18ac7-747">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-747">'SignalR' uid:</span></span>

* <span data-ttu-id="18ac7-748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="18ac7-749">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-749">'Blazor'</span></span>
* <span data-ttu-id="18ac7-750">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-750">'Identity'</span></span>
  * <span data-ttu-id="18ac7-751">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-751">'Let's Encrypt'</span></span>
  * <span data-ttu-id="18ac7-752">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-752">'Razor'</span></span>
  * <span data-ttu-id="18ac7-753">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-753">'SignalR' uid:</span></span>
  * <!-- review default-valued parameters optional parameters --> <span data-ttu-id="18ac7-754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="18ac7-755">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-755">'Blazor'</span></span> <span data-ttu-id="18ac7-756">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-756">'Identity'</span></span>

| <span data-ttu-id="18ac7-757">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-757">'Let's Encrypt'</span></span>                     | <span data-ttu-id="18ac7-758">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-758">'Razor'</span></span>                        | <span data-ttu-id="18ac7-759">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-759">'SignalR' uid:</span></span>                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| <span data-ttu-id="18ac7-760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>                | <span data-ttu-id="18ac7-761">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-761">'Blazor'</span></span>                       | `/Home/About`           |
| <span data-ttu-id="18ac7-762">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-762">'Identity'</span></span>                | <span data-ttu-id="18ac7-763">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-763">'Let's Encrypt'</span></span> | `/Order/About`          |
| <span data-ttu-id="18ac7-764">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-764">'Razor'</span></span> | <span data-ttu-id="18ac7-765">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-765">'SignalR' uid:</span></span>                       | `/Home/About`           |
| <span data-ttu-id="18ac7-766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>                | <span data-ttu-id="18ac7-767">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-767">'Blazor'</span></span>        | `/Home/About?color=Red` |

### <a name="problems-with-route-value-invalidation"></a><span data-ttu-id="18ac7-768">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-768">'Identity'</span></span>

<span data-ttu-id="18ac7-769">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-769">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-770">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-770">'Razor'</span></span> <span data-ttu-id="18ac7-771">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-771">'SignalR' uid:</span></span>

<span data-ttu-id="18ac7-772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupUnsupported.cs?name=snippet)]

<span data-ttu-id="18ac7-773">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-773">'Blazor'</span></span> <span data-ttu-id="18ac7-774">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-774">'Identity'</span></span> <span data-ttu-id="18ac7-775">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-775">'Let's Encrypt'</span></span>

* <span data-ttu-id="18ac7-776">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-776">'Razor'</span></span>
* <span data-ttu-id="18ac7-777">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-777">'SignalR' uid:</span></span>

## <a name="configuring-endpoint-metadata"></a><span data-ttu-id="18ac7-778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="18ac7-779">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-779">'Blazor'</span></span>

* <span data-ttu-id="18ac7-780">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-780">'Identity'</span></span>
* <span data-ttu-id="18ac7-781">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-781">'Let's Encrypt'</span></span>
* <span data-ttu-id="18ac7-782">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-782">'Razor'</span></span>
* <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*>
* <span data-ttu-id="18ac7-783">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-783">'SignalR' uid:</span></span>
* <span data-ttu-id="18ac7-784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <xref:security/authorization/roles>

<a name="hostmatch"></a>

## <a name="host-matching-in-routes-with-requirehost"></a><span data-ttu-id="18ac7-785">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-785">'Blazor'</span></span>

<span data-ttu-id="18ac7-786">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-786">'Identity'</span></span> <span data-ttu-id="18ac7-787">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-787">'Let's Encrypt'</span></span>

* <span data-ttu-id="18ac7-788">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-788">'Razor'</span></span>
* <span data-ttu-id="18ac7-789">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-789">'SignalR' uid:</span></span>
* <span data-ttu-id="18ac7-790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="18ac7-791">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-791">'Blazor'</span></span>

<span data-ttu-id="18ac7-792">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-792">'Identity'</span></span> <span data-ttu-id="18ac7-793">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-793">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-794">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-794">'Razor'</span></span>

<span data-ttu-id="18ac7-795">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-795">'SignalR' uid:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRequireHost.cs?name=snippet)]

<span data-ttu-id="18ac7-796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/ProductController.cs?name=snippet)]

<span data-ttu-id="18ac7-797">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-797">'Blazor'</span></span>

* <span data-ttu-id="18ac7-798">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-798">'Identity'</span></span>
* <span data-ttu-id="18ac7-799">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-799">'Let's Encrypt'</span></span>

## <a name="performance-guidance-for-routing"></a><span data-ttu-id="18ac7-800">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-800">'Razor'</span></span>

<span data-ttu-id="18ac7-801">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-801">'SignalR' uid:</span></span>

<span data-ttu-id="18ac7-802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-803">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-803">'Blazor'</span></span> <span data-ttu-id="18ac7-804">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-804">'Identity'</span></span>

* <span data-ttu-id="18ac7-805">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-805">'Let's Encrypt'</span></span>
* <span data-ttu-id="18ac7-806">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-806">'Razor'</span></span>

<span data-ttu-id="18ac7-807">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-807">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-808">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-808">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-809">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-809">'Blazor'</span></span>

<span data-ttu-id="18ac7-810">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-810">'Identity'</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupDelay.cs?name=snippet)]

<span data-ttu-id="18ac7-811">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-811">'Let's Encrypt'</span></span>

* <span data-ttu-id="18ac7-812">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-812">'Razor'</span></span>
* <span data-ttu-id="18ac7-813">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-813">'SignalR' uid:</span></span>

<span data-ttu-id="18ac7-814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>  <span data-ttu-id="18ac7-815">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-815">'Blazor'</span></span>

<span data-ttu-id="18ac7-816">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-816">'Identity'</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippetSW)]

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippet)]

### <a name="potentially-expensive-routing-features"></a><span data-ttu-id="18ac7-817">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-817">'Let's Encrypt'</span></span>

<span data-ttu-id="18ac7-818">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-818">'Razor'</span></span>

* <span data-ttu-id="18ac7-819">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-819">'SignalR' uid:</span></span>

* <span data-ttu-id="18ac7-820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> 
  * <span data-ttu-id="18ac7-821">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-821">'Blazor'</span></span>
  * <span data-ttu-id="18ac7-822">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-822">'Identity'</span></span>
  * <span data-ttu-id="18ac7-823">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-823">'Let's Encrypt'</span></span>

* <span data-ttu-id="18ac7-824">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-824">'Razor'</span></span> <span data-ttu-id="18ac7-825">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-825">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-827">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-827">'Blazor'</span></span>

## <a name="guidance-for-library-authors"></a><span data-ttu-id="18ac7-828">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-828">'Identity'</span></span>

<span data-ttu-id="18ac7-829">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-829">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-830">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-830">'Razor'</span></span>

### <a name="define-endpoints"></a><span data-ttu-id="18ac7-831">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-831">'SignalR' uid:</span></span>

<span data-ttu-id="18ac7-832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="18ac7-833">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-833">'Blazor'</span></span> <span data-ttu-id="18ac7-834">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-834">'Identity'</span></span> <span data-ttu-id="18ac7-835">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-835">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-836">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-836">'Razor'</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...);

    endpoints.MapHealthChecks("/healthz");
});
```

<span data-ttu-id="18ac7-837">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-837">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-839">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-839">'Blazor'</span></span>

* <span data-ttu-id="18ac7-840">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-840">'Identity'</span></span>
* <span data-ttu-id="18ac7-841">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-841">'Let's Encrypt'</span></span>

<span data-ttu-id="18ac7-842">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-842">'Razor'</span></span> <span data-ttu-id="18ac7-843">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-843">'SignalR' uid:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequrireAuthorization()
                                 .WithMyFrameworkFeature(awesome: true);

    endpoints.MapHealthChecks("/healthz");
});
```

<span data-ttu-id="18ac7-844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-845">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-845">'Blazor'</span></span> <span data-ttu-id="18ac7-846">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-846">'Identity'</span></span>

<span data-ttu-id="18ac7-847">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-847">'Let's Encrypt'</span></span>

<span data-ttu-id="18ac7-848">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-848">'Razor'</span></span> <span data-ttu-id="18ac7-849">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-849">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

### <a name="creating-routing-integrated-middleware"></a><span data-ttu-id="18ac7-851">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-851">'Blazor'</span></span>

<span data-ttu-id="18ac7-852">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-852">'Identity'</span></span>

<span data-ttu-id="18ac7-853">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-853">'Let's Encrypt'</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet2)]

<span data-ttu-id="18ac7-854">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-854">'Razor'</span></span> <span data-ttu-id="18ac7-855">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-855">'SignalR' uid:</span></span>

* <span data-ttu-id="18ac7-856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="18ac7-857">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-857">'Blazor'</span></span>

<span data-ttu-id="18ac7-858">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-858">'Identity'</span></span>

* <span data-ttu-id="18ac7-859">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-859">'Let's Encrypt'</span></span>
* <span data-ttu-id="18ac7-860">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-860">'Razor'</span></span>

<span data-ttu-id="18ac7-861">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-861">'SignalR' uid:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet)]

<span data-ttu-id="18ac7-862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

* <span data-ttu-id="18ac7-863">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-863">'Blazor'</span></span>
* <span data-ttu-id="18ac7-864">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-864">'Identity'</span></span>

<span data-ttu-id="18ac7-865">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-865">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-866">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-866">'Razor'</span></span>

<span data-ttu-id="18ac7-867">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-867">'SignalR' uid:</span></span>

```csharp
app.UseRouting();

app.UseAuthorization(new AuthorizationPolicy() { ... });

app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequrireAuthorization();
});
```

<span data-ttu-id="18ac7-868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-869">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-869">'Blazor'</span></span> <!-- shown where?  (shown here) --> <span data-ttu-id="18ac7-870">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-870">'Identity'</span></span>

* <span data-ttu-id="18ac7-871">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-871">'Let's Encrypt'</span></span>
* <span data-ttu-id="18ac7-872">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-872">'Razor'</span></span>

<span data-ttu-id="18ac7-873">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-873">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="18ac7-875">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-875">'Blazor'</span></span> <span data-ttu-id="18ac7-876">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-876">'Identity'</span></span> <span data-ttu-id="18ac7-877">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-877">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-878">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-878">'Razor'</span></span>

<span data-ttu-id="18ac7-879">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-879">'SignalR' uid:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="18ac7-880">------------------------------------- | | `hello`                                  | `/hello`                | Nur für den Pfad `/hello` wird eine Übereinstimmung ermittelt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-880">------------------------------------- | | `hello`                                  | `/hello`                | Only matches the single path `/hello`.</span></span> <span data-ttu-id="18ac7-881">| | `{Page=Home}`                            | `/`                     | Sucht nach Übereinstimmungen und legt `Page` auf `Home` fest.</span><span class="sxs-lookup"><span data-stu-id="18ac7-881">| | `{Page=Home}`                            | `/`                     | Matches and sets `Page` to `Home`.</span></span> <span data-ttu-id="18ac7-882">| | `{Page=Home}`                            | `/Contact`                     | Sucht nach Übereinstimmungen und legt `Page` auf `Contact` fest.</span><span class="sxs-lookup"><span data-stu-id="18ac7-882">| | `{Page=Home}`                            | `/Contact`              | Matches and sets `Page` to `Contact`.</span></span>

```csharp
// Use the routing logic of ASP.NET Core 2.1 or earlier:
services.AddMvc(options => options.EnableEndpointRouting = false)
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="18ac7-883">| | `{controller}/{action}/{id?}`            | `/Products/List`        | Wird dem `Products`-Controller und der `List`-Aktion zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="18ac7-883">| | `{controller}/{action}/{id?}`            | `/Products/List`        | Maps to the `Products` controller and `List` action.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="18ac7-884">| | `{controller}/{action}/{id?}`            | `/Products/Details/123`        | Wird dem `Products`-Controller und der `Details`-Aktion zugeordnet, bei der `id` auf 123 festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="18ac7-884">| | `{controller}/{action}/{id?}`            | `/Products/Details/123` | Maps to the `Products` controller and  `Details` action with`id` set to 123.</span></span> <span data-ttu-id="18ac7-885">| | `{controller=Home}/{action=Index}/{id?}` | `/`        | Wird dem `Home`-Controller und der `Index`-Methode zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="18ac7-885">| | `{controller=Home}/{action=Index}/{id?}` | `/`                     | Maps to the `Home` controller and `Index` method.</span></span> <span data-ttu-id="18ac7-886">`id` wird ignoriert.</span><span class="sxs-lookup"><span data-stu-id="18ac7-886">`id` is ignored.</span></span>

<span data-ttu-id="18ac7-887">| | `{controller=Home}/{action=Index}/{id?}` | `/Products`        | Wird dem `Products`-Controller und der `Index`-Methode zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="18ac7-887">| | `{controller=Home}/{action=Index}/{id?}` | `/Products`         | Maps to the `Products` controller and `Index` method.</span></span>

## <a name="routing-basics"></a><span data-ttu-id="18ac7-888">`id` wird ignoriert.</span><span class="sxs-lookup"><span data-stu-id="18ac7-888">`id` is ignored.</span></span>

<span data-ttu-id="18ac7-889">Mit Vorlagen lässt sich Routing besonders leicht durchführen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-889">Using a template is generally the simplest approach to routing.</span></span> <span data-ttu-id="18ac7-890">Einschränkungen und Standardwerte können auch außerhalb der Routenvorlage angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-890">Constraints and defaults can also be specified outside the route template.</span></span>

* <span data-ttu-id="18ac7-891">Komplexe Segmente</span><span class="sxs-lookup"><span data-stu-id="18ac7-891">Complex segments</span></span>
* <span data-ttu-id="18ac7-892">Komplexe Segmente werden von rechts nach links auf eine [nicht gierige](#greedy) Weise durch entsprechende Literaltrennzeichen verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="18ac7-892">Complex segments are processed by matching up literal delimiters from right to left in a [non-greedy](#greedy) way.</span></span>

<span data-ttu-id="18ac7-893">Beispielsweise ist `[Route("/a{b}c{d}")]` ein komplexes Segment.</span><span class="sxs-lookup"><span data-stu-id="18ac7-893">For example, `[Route("/a{b}c{d}")]` is a complex segment.</span></span> <span data-ttu-id="18ac7-894">Komplexe Segmente funktionieren auf eine bestimmte Weise, die für eine erfolgreiche Verwendung verstanden werden muss.</span><span class="sxs-lookup"><span data-stu-id="18ac7-894">Complex segments work in a particular way that must be understood to use them successfully.</span></span>

<span data-ttu-id="18ac7-895">Das Beispiel in diesem Abschnitt zeigt, warum komplexe Segmente nur dann wirklich gut funktionieren, wenn der Trennzeichentext nicht innerhalb der Parameterwerte erscheint.</span><span class="sxs-lookup"><span data-stu-id="18ac7-895">The example in this section demonstrates why complex segments only really work well when the delimiter text doesn't appear inside the parameter values.</span></span> <span data-ttu-id="18ac7-896">Für komplexere Fälle ist die Verwendung eines [RegEx](/dotnet/standard/base-types/regular-expressions) und das anschließende manuelle Extrahieren der Werte erforderlich.</span><span class="sxs-lookup"><span data-stu-id="18ac7-896">Using a [regex](/dotnet/standard/base-types/regular-expressions) and then manually extracting the values is needed for more complex cases.</span></span> <span data-ttu-id="18ac7-897">Dies ist eine Zusammenfassung der Schritte, die beim Routing mit der Vorlage `/a{b}c{d}` und dem URL-Pfad `/abcd` ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-897">This is a summary of the steps that routing performs with the template `/a{b}c{d}` and the URL path `/abcd`.</span></span>

<span data-ttu-id="18ac7-898">`|` wird verwendet, um die Funktionsweise des Algorithmus besser zu veranschaulichen:</span><span class="sxs-lookup"><span data-stu-id="18ac7-898">The `|` is used to help visualize how the algorithm works:</span></span> <span data-ttu-id="18ac7-899">Das erste Literal von rechts nach links ist `c`.</span><span class="sxs-lookup"><span data-stu-id="18ac7-899">The first literal, right to left, is `c`.</span></span> <span data-ttu-id="18ac7-900">Daher wird `/abcd` von rechts durchsucht und `/ab|c|d` gefunden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-900">So `/abcd` is searched from right and finds `/ab|c|d`.</span></span>

<span data-ttu-id="18ac7-901">Alles auf der rechten Seite (`d`) ist jetzt mit dem Routenparameter `{d}` abgeglichen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-901">Everything to the right (`d`) is now matched to the route parameter `{d}`.</span></span> <span data-ttu-id="18ac7-902">Das nächste Literal von rechts nach links ist `a`.</span><span class="sxs-lookup"><span data-stu-id="18ac7-902">The next literal, right to left, is `a`.</span></span>

<span data-ttu-id="18ac7-903">Also wird `/ab|c|d` dort gesucht, wo die Suche unterbrochen wurde, und dann wird `a` in `/|a|b|c|d` gefunden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-903">So `/ab|c|d` is searched starting where we left off, then `a` is found `/|a|b|c|d`.</span></span>

<span data-ttu-id="18ac7-904">Der Wert auf der rechten Seite (`b`) ist jetzt mit dem Routenparameter `{b}` abgeglichen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-904">The value to the right (`b`) is now matched to the route parameter `{b}`.</span></span> <span data-ttu-id="18ac7-905">Es ist kein verbleibender Text und keine verbleibende Routenvorlage vorhanden. Folglich ist dies eine Übereinstimmung.</span><span class="sxs-lookup"><span data-stu-id="18ac7-905">There is no remaining text and no remaining route template, so this is a match.</span></span>

<span data-ttu-id="18ac7-906">Nachfolgend ist ein Beispiel für einen negativen Fall mit derselben Vorlage `/a{b}c{d}` und dem URL-Pfad `/aabcd`.</span><span class="sxs-lookup"><span data-stu-id="18ac7-906">Here's an example of a negative case using the same template `/a{b}c{d}` and the URL path `/aabcd`.</span></span>

* <span data-ttu-id="18ac7-907">`|` wird verwendet, um die Funktionsweise des Algorithmus besser zu veranschaulichen:</span><span class="sxs-lookup"><span data-stu-id="18ac7-907">The `|` is used to help visualize how the algorithm works.</span></span>
* <span data-ttu-id="18ac7-908">Bei diesem Fall handelt es sich nicht um eine Übereinstimmung, was durch denselben Algorithmus belegt wird:</span><span class="sxs-lookup"><span data-stu-id="18ac7-908">This case isn't a match, which is explained by the same algorithm:</span></span>
* <span data-ttu-id="18ac7-909">Das erste Literal von rechts nach links ist `c`.</span><span class="sxs-lookup"><span data-stu-id="18ac7-909">The first literal, right to left, is `c`.</span></span>
* <span data-ttu-id="18ac7-910">Daher wird `/aabcd` von rechts durchsucht und `/aab|c|d` gefunden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-910">So `/aabcd` is searched from right and finds `/aab|c|d`.</span></span>
* <span data-ttu-id="18ac7-911">Alles auf der rechten Seite (`d`) ist jetzt mit dem Routenparameter `{d}` abgeglichen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-911">Everything to the right (`d`) is now matched to the route parameter `{d}`.</span></span>
* <span data-ttu-id="18ac7-912">Das nächste Literal von rechts nach links ist `a`.</span><span class="sxs-lookup"><span data-stu-id="18ac7-912">The next literal, right to left, is `a`.</span></span>
* <span data-ttu-id="18ac7-913">Also wird `/aab|c|d` dort gesucht, wo die Suche unterbrochen wurde, und dann wird `a` in `/a|a|b|c|d` gefunden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-913">So `/aab|c|d` is searched starting where we left off, then `a` is found `/a|a|b|c|d`.</span></span>
* <span data-ttu-id="18ac7-914">Der Wert auf der rechten Seite (`b`) ist jetzt mit dem Routenparameter `{b}` abgeglichen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-914">The value to the right (`b`) is now matched to the route parameter `{b}`.</span></span>
* <span data-ttu-id="18ac7-915">Zu diesem Zeitpunkt gibt es noch verbleibenden Text `a`, aber es gibt keine Routenvorlage mehr, die der Algorithmus analysieren kann, weshalb dies keine Übereinstimmung ist.</span><span class="sxs-lookup"><span data-stu-id="18ac7-915">At this point there is remaining text `a`, but the algorithm has run out of route template to parse, so this is not a match.</span></span>

  * <span data-ttu-id="18ac7-916">Da der übereinstimmende Algorithmus [nicht gierig](#greedy) ist:</span><span class="sxs-lookup"><span data-stu-id="18ac7-916">Since the matching algorithm is [non-greedy](#greedy):</span></span>
  * <span data-ttu-id="18ac7-917">Entspricht er der kleinstmöglichen Textmenge in jedem Schritt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-917">It matches the smallest amount of text possible in each step.</span></span>

> [!NOTE]
> <span data-ttu-id="18ac7-918">Alle Fälle, in denen der Trennzeichenwert in den Parameterwerten angezeigt wird, stimmen nicht überein.</span><span class="sxs-lookup"><span data-stu-id="18ac7-918">Any case where the delimiter value appears inside the parameter values results in not matching.</span></span> <span data-ttu-id="18ac7-919">Reguläre Ausdrücke bieten eine viel bessere Kontrolle über das Abgleichsverhalten.</span><span class="sxs-lookup"><span data-stu-id="18ac7-919">Regular expressions provide much more control over their matching behavior.</span></span>

<span data-ttu-id="18ac7-920">Beim gierigen Abgleich, auch als [Lazy Matching](https://wikipedia.org/wiki/Regular_expression#Lazy_matching) bezeichnet, wird die größtmögliche Zeichenfolge abgeglichen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-920">Greedy matching, also know as [lazy matching](https://wikipedia.org/wiki/Regular_expression#Lazy_matching), matches the largest possible string.</span></span> <span data-ttu-id="18ac7-921">Beim nicht gierigen Abgleich ist dies die kürzeste Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="18ac7-921">Non-greedy matches the smallest possible string.</span></span> <span data-ttu-id="18ac7-922">Referenz für Routeneinschränkungen</span><span class="sxs-lookup"><span data-stu-id="18ac7-922">Route constraint reference</span></span>

### <a name="url-matching"></a><span data-ttu-id="18ac7-923">Routeneinschränkungen werden angewendet, wenn eine Übereinstimmung mit der eingehenden URL gefunden wurde und der URL-Pfad in Routenwerten mit Token versehen wird.</span><span class="sxs-lookup"><span data-stu-id="18ac7-923">Route constraints execute when a match has occurred to the incoming URL and the URL path is tokenized into route values.</span></span>

<span data-ttu-id="18ac7-924">In der Regel wird mit Routeneinschränkungen der Routenwert der zugehörigen Vorlage geprüft. Dabei wird anhand einer True/False-Entscheidung bestimmt, ob der Wert gültig ist.</span><span class="sxs-lookup"><span data-stu-id="18ac7-924">Route constraints generally inspect the route value associated via the route template and make a true or false decision about whether the value is acceptable.</span></span> <span data-ttu-id="18ac7-925">Für einige Routeneinschränkungen werden anstelle des Routenwerts andere Daten verwendet, um zu ermitteln, ob das Routing einer Anforderung möglich ist.</span><span class="sxs-lookup"><span data-stu-id="18ac7-925">Some route constraints use data outside the route value to consider whether the request can be routed.</span></span> <span data-ttu-id="18ac7-926"><xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> kann beispielsweise auf der Grundlage des HTTP-Verbs eine Anforderung entweder annehmen oder ablehnen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-926">For example, the <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> can accept or reject a request based on its HTTP verb.</span></span>

<span data-ttu-id="18ac7-927">Einschränkungen werden in Routinganforderungen und bei der Linkgenerierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="18ac7-927">Constraints are used in routing requests and link generation.</span></span> <span data-ttu-id="18ac7-928">Verwenden Sie keine Einschränkungen für die Eingabeüberprüfung.</span><span class="sxs-lookup"><span data-stu-id="18ac7-928">Don't use constraints for input validation.</span></span>

<span data-ttu-id="18ac7-929">Wenn Einschränkungen für die Eingabevalidierung verwendet werden, führt eine ungültige Eingabe zu einem `404`-Fehler (Nicht gefunden).</span><span class="sxs-lookup"><span data-stu-id="18ac7-929">If constraints are used for input validation, invalid input results in a `404` Not Found response.</span></span>

<span data-ttu-id="18ac7-930">Eine ungültige Eingabe sollte zu einer ungültigen Anforderung (`400`) mit einer entsprechenden Fehlermeldung führen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-930">Invalid input should produce a `400` Bad Request with an appropriate error message.</span></span> <span data-ttu-id="18ac7-931">Verwenden Sie Routeneinschränkungen nicht, um Eingaben für eine bestimmte Route zu überprüfen, sondern um ähnliche Routen zu unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-931">Route constraints are used to disambiguate similar routes, not to validate the inputs for a particular route.</span></span>

<span data-ttu-id="18ac7-932">In der folgenden Tabelle werden Beispiele für Routeneinschränkungen und deren zu erwartendes Verhalten beschrieben:</span><span class="sxs-lookup"><span data-stu-id="18ac7-932">The following table demonstrates example route constraints and their expected behavior:</span></span> <span data-ttu-id="18ac7-933">Einschränkung</span><span class="sxs-lookup"><span data-stu-id="18ac7-933">constraint</span></span> <span data-ttu-id="18ac7-934">Beispiel</span><span class="sxs-lookup"><span data-stu-id="18ac7-934">Example</span></span> <span data-ttu-id="18ac7-935">Beispiele für Übereinstimmungen</span><span class="sxs-lookup"><span data-stu-id="18ac7-935">Example Matches</span></span>

<span data-ttu-id="18ac7-936">Hinweise</span><span class="sxs-lookup"><span data-stu-id="18ac7-936">Notes</span></span> <span data-ttu-id="18ac7-937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-938">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-938">'Blazor'</span></span> <span data-ttu-id="18ac7-939">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-939">'Identity'</span></span> <span data-ttu-id="18ac7-940">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-940">'Let's Encrypt'</span></span>

<a name="lg"></a>

### <a name="url-generation-with-linkgenerator"></a><span data-ttu-id="18ac7-941">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-941">'Razor'</span></span>

<span data-ttu-id="18ac7-942">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-942">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="18ac7-944">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-944">'Blazor'</span></span> <span data-ttu-id="18ac7-945">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-945">'Identity'</span></span> <span data-ttu-id="18ac7-946">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-946">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-947">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-947">'Razor'</span></span>

<span data-ttu-id="18ac7-948">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-948">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-950">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-950">'Blazor'</span></span>

<span data-ttu-id="18ac7-951">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-951">'Identity'</span></span>

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

<span data-ttu-id="18ac7-952">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-952">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-953">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-953">'Razor'</span></span>

<span data-ttu-id="18ac7-954">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-954">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-955">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-955">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-956">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-956">'Blazor'</span></span> <span data-ttu-id="18ac7-957">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-957">'Identity'</span></span>

<span data-ttu-id="18ac7-958">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-958">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-959">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-959">'Razor'</span></span>

1. <span data-ttu-id="18ac7-960">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-960">'SignalR' uid:</span></span>
1. <span data-ttu-id="18ac7-961">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-961">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-962">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-962">'Blazor'</span></span>

<span data-ttu-id="18ac7-963">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-963">'Identity'</span></span> <span data-ttu-id="18ac7-964">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-964">'Let's Encrypt'</span></span>

| <span data-ttu-id="18ac7-965">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-965">'Razor'</span></span>   | <span data-ttu-id="18ac7-966">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-966">'SignalR' uid:</span></span>                                                         |
| ------------------ | ------------------------------------------------------------------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | <span data-ttu-id="18ac7-967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | <span data-ttu-id="18ac7-968">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-968">'Blazor'</span></span>             |

> [!WARNING]
> <span data-ttu-id="18ac7-969">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-969">'Identity'</span></span>
>
> * <span data-ttu-id="18ac7-970">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-970">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-971">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-971">'Razor'</span></span> <span data-ttu-id="18ac7-972">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-972">'SignalR' uid:</span></span>
>
> * <span data-ttu-id="18ac7-973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-974">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-974">'Blazor'</span></span> <span data-ttu-id="18ac7-975">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-975">'Identity'</span></span> <span data-ttu-id="18ac7-976">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-976">'Let's Encrypt'</span></span>

## <a name="differences-from-earlier-versions-of-routing"></a><span data-ttu-id="18ac7-977">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-977">'Razor'</span></span>

<span data-ttu-id="18ac7-978">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-978">'SignalR' uid:</span></span>

* <span data-ttu-id="18ac7-979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

* <span data-ttu-id="18ac7-980">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-980">'Blazor'</span></span> <span data-ttu-id="18ac7-981">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-981">'Identity'</span></span>

* <span data-ttu-id="18ac7-982">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-982">'Let's Encrypt'</span></span>

  <span data-ttu-id="18ac7-983">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-983">'Razor'</span></span>

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  <span data-ttu-id="18ac7-984">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-984">'SignalR' uid:</span></span>

  ```csharp
  var link = Url.Action("ReadPost", "blog", new { id = 17, });
  ```

  <span data-ttu-id="18ac7-985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-986">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-986">'Blazor'</span></span> <span data-ttu-id="18ac7-987">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-987">'Identity'</span></span>

  <span data-ttu-id="18ac7-988">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-988">'Let's Encrypt'</span></span>

* <span data-ttu-id="18ac7-989">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-989">'Razor'</span></span>

  <span data-ttu-id="18ac7-990">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-990">'SignalR' uid:</span></span>

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  <span data-ttu-id="18ac7-991">-------- | ----- | | `int` | `{id:int}` | `123456789`, `-123456789` | Stimmt mit sämtlichen Integerwerten überein | | `bool` | `{active:bool}` | `true`, `FALSE` | Stimmt mit `true` oder `false` überein.</span><span class="sxs-lookup"><span data-stu-id="18ac7-991">-------- | ----- | | `int` | `{id:int}` | `123456789`, `-123456789` | Matches any integer | | `bool` | `{active:bool}` | `true`, `FALSE` | Matches `true` or `false`.</span></span>

  ```csharp
  var link = Url.Action("ReadPost", "Blog", new { id = 17, });
  ```

  <span data-ttu-id="18ac7-992">Groß/-Kleinschreibung wird beachtet | | `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Stimmt mit einem gültigen `DateTime`-Wert in der invarianten Kultur überein.</span><span class="sxs-lookup"><span data-stu-id="18ac7-992">Case-insensitive | | `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Matches a valid `DateTime` value in the invariant culture.</span></span> <span data-ttu-id="18ac7-993">Siehe vorherige Warnung.</span><span class="sxs-lookup"><span data-stu-id="18ac7-993">See preceding warning.</span></span> <span data-ttu-id="18ac7-994">| | `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Stimmt mit einem gültigen `decimal`-Wert in der invarianten Kultur überein.</span><span class="sxs-lookup"><span data-stu-id="18ac7-994">| | `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Matches a valid `decimal` value in the invariant culture.</span></span> <span data-ttu-id="18ac7-995">Siehe vorherige Warnung.| | `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Stimmt mit einem gültigen `double`-Wert in der invarianten Kultur überein.</span><span class="sxs-lookup"><span data-stu-id="18ac7-995">See preceding warning.| | `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Matches a valid `double` value in the invariant culture.</span></span>

* <span data-ttu-id="18ac7-996">Siehe vorherige Warnung.| | `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Stimmt mit einem gültigen `float`-Wert in der invarianten Kultur überein.</span><span class="sxs-lookup"><span data-stu-id="18ac7-996">See preceding warning.| | `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Matches a valid `float` value in the invariant culture.</span></span>

  <span data-ttu-id="18ac7-997">Siehe vorherige Warnung.| | `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638` | Stimmt mit einem gültigen `Guid`-Wert überein. | | `long` | `{ticks:long}` | `123456789`, `-123456789` | Stimmt mit einem gültigen `long`-Wert überein. | | `minlength(value)` | `{username:minlength(4)}` | `Rick` | Die Zeichenfolge muss mindestens vier Zeichen lang sein. | | `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | Die Zeichenfolge darf maximal acht Zeichen lang sein. | | `length(length)` | `{filename:length(12)}` | `somefile.txt` | Die Zeichenfolge muss genau zwölf Zeichen lang sein. | | `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | Die Zeichenfolge darf zwischen acht und 16 Zeichen lang sein. | | `min(value)` | `{age:min(18)}` | `19` | Der Integerwert muss mindestens 18 betragen. | | `max(value)` | `{age:max(120)}` | `91` | Der Integerwert darf maximal 120 betragen. | | `range(min,max)` | `{age:range(18,120)}` | `91` | Der Integerwert muss zwischen 18 und 120 betragen.| | `alpha` | `{name:alpha}` | `Rick` | Die Zeichenfolge muss aus mindestens einem Buchstaben bestehen, `a`-`z` Groß-/Kleinschreibung muss berücksichtigt werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-997">See preceding warning.| | `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638` | Matches a valid `Guid` value | | `long` | `{ticks:long}` | `123456789`, `-123456789` | Matches a valid `long` value | | `minlength(value)` | `{username:minlength(4)}` | `Rick` | String must be at least 4 characters | | `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | String must be no more than 8 characters | | `length(length)` | `{filename:length(12)}` | `somefile.txt` | String must be exactly 12 characters long | | `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | String must be at least 8 and no more than 16 characters long | | `min(value)` | `{age:min(18)}` | `19` | Integer value must be at least 18 | | `max(value)` | `{age:max(120)}` | `91` | Integer value must be no more than 120 | | `range(min,max)` | `{age:range(18,120)}` | `91` | Integer value must be at least 18 but no more than 120 | | `alpha` | `{name:alpha}` | `Rick` | String must consist of one or more alphabetical characters, `a`-`z` and case-insensitive.</span></span> <span data-ttu-id="18ac7-998">| | `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | Die Zeichenfolge muss mit dem regulären Ausdruck übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-998">| | `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | String must match the regular expression.</span></span> <span data-ttu-id="18ac7-999">Weitere Informationen finden Sie unter Tipps zum Definieren eines regulären Ausdrucks.</span><span class="sxs-lookup"><span data-stu-id="18ac7-999">See tips about defining a regular expression.</span></span> <span data-ttu-id="18ac7-1000">| | `required` | `{name:required}` | `Rick` | Hierdurch wird erzwungen, dass ein Wert, der kein Parameter ist, für die URL-Generierung vorhanden sein muss. |</span><span class="sxs-lookup"><span data-stu-id="18ac7-1000">| | `required` | `{name:required}` | `Rick` | Used to enforce that a non-parameter value is present during URL generation |</span></span> <span data-ttu-id="18ac7-1001">Auf einen einzelnen Parameter können mehrere durch Doppelpunkte getrennte Einschränkungen angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1001">Multiple, colon delimited constraints can be applied to a single parameter.</span></span>

  <span data-ttu-id="18ac7-1002">Durch die folgende Einschränkung wird ein Parameter beispielsweise auf einen Integerwert größer oder gleich 1 beschränkt:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1002">For example, the following constraint restricts a parameter to an integer value of 1 or greater:</span></span> <span data-ttu-id="18ac7-1003">Für Routeneinschränkungen, mit denen die URL überprüft wird und die in den CLR-Typ umgewandelt werden, wird immer die invariante Kultur verwendet.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1003">Route constraints that verify the URL and are converted to a CLR type always use the invariant culture.</span></span>

  <span data-ttu-id="18ac7-1004">Dies gilt z. B. für die Konvertierung in den CLR-Typ `int` oder `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1004">For example, conversion to the CLR type `int` or `DateTime`.</span></span>

  ```cshtml
  @page "{id}"
  @Url.Page("/Login")
  ```

  <span data-ttu-id="18ac7-1005">Diese Einschränkungen setzen voraus, dass die URL nicht lokalisierbar ist.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1005">These constraints assume that the URL is not localizable.</span></span>

  ```cshtml
  @page "{id?}"
  ```

  <span data-ttu-id="18ac7-1006">Die vom Framework bereitgestellten Routeneinschränkungen ändern nicht die Werte, die in Routenwerten gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1006">The framework-provided route constraints don't modify the values stored in route values.</span></span> <span data-ttu-id="18ac7-1007">Alle Routenwerte, die aus der URL analysiert werden, werden als Zeichenfolgen gespeichert.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1007">All route values parsed from the URL are stored as strings.</span></span> <span data-ttu-id="18ac7-1008">Durch die `float`-Einschränkung wird beispielsweise versucht, den Routenwert in einen Gleitkommawert zu konvertieren. Mit dem konvertierten Wert wird allerdings nur überprüft, ob eine Umwandlung überhaupt möglich ist.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1008">For example, the `float` constraint attempts to convert the route value to a float, but the converted value is used only to verify it can be converted to a float.</span></span>

  <span data-ttu-id="18ac7-1009">Reguläre Ausdrücke in Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="18ac7-1009">Regular expressions in constraints</span></span> <span data-ttu-id="18ac7-1010">Reguläre Ausdrücke können mithilfe der `regex(...)`-Routeneinschränkung als Inline-Einschränkungen angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1010">Regular expressions can be specified as inline constraints using the `regex(...)` route constraint.</span></span>

* <span data-ttu-id="18ac7-1011">Methoden der <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>-Familie akzeptieren auch ein Objektliteral von Einschränkungen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1011">Methods in the <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> family also accept an object literal of constraints.</span></span>

  <span data-ttu-id="18ac7-1012">Wenn dieses Formular verwendet wird, werden Zeichenfolgenwerte als reguläre Ausdrücke interpretiert.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1012">If that form is used, string values are interpreted as regular expressions.</span></span> <span data-ttu-id="18ac7-1013">Der folgende Code verwendet eine Inline-RegEx-Einschränkung:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1013">The following code uses an inline regex constraint:</span></span>

  <span data-ttu-id="18ac7-1014">Der folgende Code verwendet ein Objektliteral, um eine RegEx-Einschränkung anzugeben:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1014">The following code uses an object literal to specify a regex constraint:</span></span>

  | <span data-ttu-id="18ac7-1015">Im ASP.NET Core-Framework wird dem Konstruktor für reguläre Ausdrücke `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1015">The ASP.NET Core framework adds `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` to the regular expression constructor.</span></span>              | <span data-ttu-id="18ac7-1016">Eine Beschreibung dieser Member finden Sie unter <xref:System.Text.RegularExpressions.RegexOptions>.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1016">See <xref:System.Text.RegularExpressions.RegexOptions> for a description of these members.</span></span><br>`Url.Action(new { category = "admin/products" })`&hellip; |
  | ------------------ | --------------------------------------------------------------------- |
  | `/search/{*page}`  | <span data-ttu-id="18ac7-1017">In regulären Ausdrücken werden Trennzeichen und Token verwendet, die auch beim Routing und in der Programmiersprache C# in ähnlicher Weise verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1017">Regular expressions use delimiters and tokens similar to those used by routing and the C# language.</span></span>             |
  | `/search/{**page}` | `/search/admin/products`                                              |

### <a name="middleware-example"></a><span data-ttu-id="18ac7-1018">Token, die reguläre Ausdrücke enthalten, müssen mit einem Escapezeichen versehen werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1018">Regular expression tokens must be escaped.</span></span>

<span data-ttu-id="18ac7-1019">Wenn Sie den regulären Ausdruck `^\d{3}-\d{2}-\d{4}$` in einer Inline-Einschränkung verwenden möchten, nutzen Sie eine der folgenden Optionen:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1019">To use the regular expression `^\d{3}-\d{2}-\d{4}$` in an inline constraint, use one of the following:</span></span> <span data-ttu-id="18ac7-1020">Ersetzen Sie `\`-Zeichen in der Zeichenfolge durch `\\`-Zeichen in der C#-Quelldatei, um das Escapezeichen für die Zeichenfolge `\` zu setzen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1020">Replace `\` characters provided in the string as `\\` characters in the C# source file in order to escape the `\` string escape character.</span></span>

```csharp
using Microsoft.AspNetCore.Routing;

public class ProductsLinkMiddleware
{
    private readonly LinkGenerator _linkGenerator;

    public ProductsLinkMiddleware(RequestDelegate next, LinkGenerator linkGenerator)
    {
        _linkGenerator = linkGenerator;
    }

    public async Task InvokeAsync(HttpContext httpContext)
    {
        var url = _linkGenerator.GetPathByAction("ListProducts", "Store");

        httpContext.Response.ContentType = "text/plain";

        await httpContext.Response.WriteAsync($"Go to {url} to see our products.");
    }
}
```

### <a name="create-routes"></a><span data-ttu-id="18ac7-1021">[Ausführliche Zeichenfolgeliterale](/dotnet/csharp/language-reference/keywords/string)</span><span class="sxs-lookup"><span data-stu-id="18ac7-1021">[Verbatim string literals](/dotnet/csharp/language-reference/keywords/string).</span></span>

<span data-ttu-id="18ac7-1022">Wenn Sie Trennzeichen für Routenparameter mit Escapezeichen versehen möchten (`{`, `}`, `[`, `]`), geben Sie jedes Zeichen im Ausdruck doppelt ein (z. B. `{{`, `}}`, `[[`, `]]`).</span><span class="sxs-lookup"><span data-stu-id="18ac7-1022">To escape routing parameter delimiter characters `{`, `}`, `[`, `]`, double the characters in the expression, for example, `{{`, `}}`, `[[`, `]]`.</span></span> <span data-ttu-id="18ac7-1023">In der folgenden Tabelle werden reguläre Ausdrücke und Ausdrücke aufgeführt, die mit Escapezeichen versehen sind:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1023">The following table shows a regular expression and its escaped version:</span></span>

<span data-ttu-id="18ac7-1024">Regulärer Ausdruck</span><span class="sxs-lookup"><span data-stu-id="18ac7-1024">Regular expression</span></span> <span data-ttu-id="18ac7-1025">Mit Escapezeichen versehener regulärer Ausdruck</span><span class="sxs-lookup"><span data-stu-id="18ac7-1025">Escaped regular expression</span></span> <span data-ttu-id="18ac7-1026">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1026">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="18ac7-1027">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1027">'Blazor'</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="18ac7-1028">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1028">'Identity'</span></span> <span data-ttu-id="18ac7-1029">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1029">'Let's Encrypt'</span></span>

<span data-ttu-id="18ac7-1030">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1030">'Razor'</span></span> <span data-ttu-id="18ac7-1031">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1031">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-1032">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1032">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="18ac7-1033">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1033">'Blazor'</span></span> <span data-ttu-id="18ac7-1034">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1034">'Identity'</span></span> <span data-ttu-id="18ac7-1035">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1035">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-1036">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1036">'Razor'</span></span>

<span data-ttu-id="18ac7-1037">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1037">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-1038">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1038">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-1039">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1039">'Blazor'</span></span>

<span data-ttu-id="18ac7-1040">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1040">'Identity'</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

<span data-ttu-id="18ac7-1041">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1041">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-1042">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1042">'Razor'</span></span> <span data-ttu-id="18ac7-1043">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1043">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-1044">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1044">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="18ac7-1045">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1045">'Blazor'</span></span> <span data-ttu-id="18ac7-1046">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1046">'Identity'</span></span>

<span data-ttu-id="18ac7-1047">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1047">'Let's Encrypt'</span></span>

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> <span data-ttu-id="18ac7-1048">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1048">'Razor'</span></span> <span data-ttu-id="18ac7-1049">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1049">'SignalR' uid:</span></span>

<span data-ttu-id="18ac7-1050">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1050">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{**article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

<span data-ttu-id="18ac7-1051">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1051">'Blazor'</span></span> <span data-ttu-id="18ac7-1052">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1052">'Identity'</span></span> <span data-ttu-id="18ac7-1053">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1053">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-1054">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1054">'Razor'</span></span> <span data-ttu-id="18ac7-1055">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1055">'SignalR' uid:</span></span>

<span data-ttu-id="18ac7-1056">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1056">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

<span data-ttu-id="18ac7-1057">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1057">'Blazor'</span></span>

!['Identity'](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a><span data-ttu-id="18ac7-1059">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1059">'Let's Encrypt'</span></span>

<span data-ttu-id="18ac7-1060">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1060">'Razor'</span></span> <span data-ttu-id="18ac7-1061">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1061">'SignalR' uid:</span></span>

> [!TIP]
> <span data-ttu-id="18ac7-1062">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1062">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-1063">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1063">'Blazor'</span></span> <span data-ttu-id="18ac7-1064">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1064">'Identity'</span></span>

<span data-ttu-id="18ac7-1065">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1065">'Let's Encrypt'</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="18ac7-1066">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1066">'Razor'</span></span> <span data-ttu-id="18ac7-1067">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1067">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-1068">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1068">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="18ac7-1069">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1069">'Blazor'</span></span> <span data-ttu-id="18ac7-1070">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1070">'Identity'</span></span>

<span data-ttu-id="18ac7-1071">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1071">'Let's Encrypt'</span></span>

> [!NOTE]
> <span data-ttu-id="18ac7-1072">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1072">'Razor'</span></span>

<span data-ttu-id="18ac7-1073">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1073">'SignalR' uid:</span></span>

## <a name="use-routing-middleware"></a><span data-ttu-id="18ac7-1074">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1074">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="18ac7-1075">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1075">'Blazor'</span></span>

<span data-ttu-id="18ac7-1076">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1076">'Identity'</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="18ac7-1077">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1077">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-1078">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1078">'Razor'</span></span>

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <span data-ttu-id="18ac7-1079">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1079">'SignalR' uid:</span></span>
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

<span data-ttu-id="18ac7-1080">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1080">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

| <span data-ttu-id="18ac7-1081">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1081">'Blazor'</span></span>                    | <span data-ttu-id="18ac7-1082">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1082">'Identity'</span></span>                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | <span data-ttu-id="18ac7-1083">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1083">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-1084">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1084">'Razor'</span></span> |
| `/package/track/-3`    | <span data-ttu-id="18ac7-1085">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1085">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-1086">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1086">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> |
| `/package/track/-3/`   | <span data-ttu-id="18ac7-1087">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1087">'Blazor'</span></span> <span data-ttu-id="18ac7-1088">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1088">'Identity'</span></span> |
| `/package/track/`      | <span data-ttu-id="18ac7-1089">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1089">'Let's Encrypt'</span></span>              |
| `GET /hello/Joe`       | <span data-ttu-id="18ac7-1090">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1090">'Razor'</span></span>                                          |
| `POST /hello/Joe`      | <span data-ttu-id="18ac7-1091">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1091">'SignalR' uid:</span></span> |
| `GET /hello/Joe/Smith` | <span data-ttu-id="18ac7-1092">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1092">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>              |

<span data-ttu-id="18ac7-1093">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1093">'Blazor'</span></span>

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

<span data-ttu-id="18ac7-1094">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1094">'Identity'</span></span> <span data-ttu-id="18ac7-1095">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1095">'Let's Encrypt'</span></span>

## <a name="route-template-reference"></a><span data-ttu-id="18ac7-1096">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1096">'Razor'</span></span>

<span data-ttu-id="18ac7-1097">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1097">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-1098">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1098">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-1099">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1099">'Blazor'</span></span> <span data-ttu-id="18ac7-1100">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1100">'Identity'</span></span>

<span data-ttu-id="18ac7-1101">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1101">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-1102">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1102">'Razor'</span></span> <span data-ttu-id="18ac7-1103">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1103">'SignalR' uid:</span></span>

<span data-ttu-id="18ac7-1104">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1104">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-1105">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1105">'Blazor'</span></span> <span data-ttu-id="18ac7-1106">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1106">'Identity'</span></span> <span data-ttu-id="18ac7-1107">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1107">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-1108">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1108">'Razor'</span></span>

* `/files/myFile.txt`
* `/files/myFile`

<span data-ttu-id="18ac7-1109">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1109">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-1110">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1110">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-1111">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1111">'Blazor'</span></span> <span data-ttu-id="18ac7-1112">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1112">'Identity'</span></span>

<span data-ttu-id="18ac7-1113">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1113">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-1114">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1114">'Razor'</span></span> <span data-ttu-id="18ac7-1115">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1115">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-1116">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1116">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-1117">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1117">'Blazor'</span></span>

<span data-ttu-id="18ac7-1118">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1118">'Identity'</span></span> <span data-ttu-id="18ac7-1119">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1119">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-1120">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1120">'Razor'</span></span> <span data-ttu-id="18ac7-1121">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1121">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-1122">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1122">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="18ac7-1123">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1123">'Blazor'</span></span> <span data-ttu-id="18ac7-1124">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1124">'Identity'</span></span> <span data-ttu-id="18ac7-1125">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1125">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-1126">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1126">'Razor'</span></span>

<span data-ttu-id="18ac7-1127">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1127">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-1128">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1128">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-1129">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1129">'Blazor'</span></span>

<span data-ttu-id="18ac7-1130">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1130">'Identity'</span></span> <span data-ttu-id="18ac7-1131">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1131">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-1132">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1132">'Razor'</span></span> <span data-ttu-id="18ac7-1133">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1133">'SignalR' uid:</span></span>

<span data-ttu-id="18ac7-1134">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1134">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

| <span data-ttu-id="18ac7-1135">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1135">'Blazor'</span></span>                           | <span data-ttu-id="18ac7-1136">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1136">'Identity'</span></span>    | <span data-ttu-id="18ac7-1137">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1137">'Let's Encrypt'</span></span>                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | <span data-ttu-id="18ac7-1138">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1138">'Razor'</span></span>                                     |
| `{Page=Home}`                            | `/`                     | <span data-ttu-id="18ac7-1139">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1139">'SignalR' uid:</span></span>                                         |
| `{Page=Home}`                            | `/Contact`              | <span data-ttu-id="18ac7-1140">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1140">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | <span data-ttu-id="18ac7-1141">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1141">'Blazor'</span></span>                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | <span data-ttu-id="18ac7-1142">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1142">'Identity'</span></span> |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | <span data-ttu-id="18ac7-1143">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1143">'Let's Encrypt'</span></span>        |

<span data-ttu-id="18ac7-1144">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1144">'Razor'</span></span> <span data-ttu-id="18ac7-1145">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1145">'SignalR' uid:</span></span>

> [!TIP]
> <span data-ttu-id="18ac7-1146">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1146">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="18ac7-1147">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1147">'Blazor'</span></span>

<span data-ttu-id="18ac7-1148">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1148">'Identity'</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a><span data-ttu-id="18ac7-1149">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1149">'Let's Encrypt'</span></span>

<span data-ttu-id="18ac7-1150">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1150">'Razor'</span></span> <span data-ttu-id="18ac7-1151">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1151">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-1152">--------------- | | `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |</span><span class="sxs-lookup"><span data-stu-id="18ac7-1152">--------------- | | `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |</span></span> <span data-ttu-id="18ac7-1153">Beim Routing verwendete reguläre Ausdrücke beginnen oft mit dem `^`-Zeichen und stellen die Startposition der Zeichenfolge dar.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1153">Regular expressions used in routing often start with the `^` character and match the starting position of the string.</span></span> <span data-ttu-id="18ac7-1154">Die Ausdrücke enden häufig mit einem Dollarzeichen (`$`) und stellen das Ende der Zeichenfolge dar.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1154">The expressions often end with the `$` character and match the end of the string.</span></span>

> [!WARNING]
> <span data-ttu-id="18ac7-1155">Mit den Zeichen `^` und `$` wird sichergestellt, dass der reguläre Ausdruck mit dem vollständigen Routenparameterwert übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1155">The `^` and `$` characters ensure that the regular expression matches the entire route parameter value.</span></span> <span data-ttu-id="18ac7-1156">Ohne die Zeichen `^` und `$` werden mit dem regulären Ausdruck alle Teilzeichenfolgen ermittelt, was häufig nicht gewünscht ist.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1156">Without the `^` and `$` characters, the regular expression matches any substring within the string, which is often undesirable.</span></span> <span data-ttu-id="18ac7-1157">In der folgenden Tabelle finden Sie Beispiele für reguläre Ausdrücke. Außerdem wird erklärt, warum ein Abgleich erfolgreich ist oder fehlschlägt:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1157">The following table provides examples and explains why they match or fail to match:</span></span>

<span data-ttu-id="18ac7-1158">expression</span><span class="sxs-lookup"><span data-stu-id="18ac7-1158">Expression</span></span>

| <span data-ttu-id="18ac7-1159">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="18ac7-1159">String</span></span> | <span data-ttu-id="18ac7-1160">Match</span><span class="sxs-lookup"><span data-stu-id="18ac7-1160">Match</span></span> | <span data-ttu-id="18ac7-1161">Kommentar</span><span class="sxs-lookup"><span data-stu-id="18ac7-1161">Comment</span></span> | <span data-ttu-id="18ac7-1162">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1162">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | <span data-ttu-id="18ac7-1163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1163">'Blazor'</span></span> | <span data-ttu-id="18ac7-1164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1164">'Identity'</span></span> |
| `bool` | `{active:bool}` | <span data-ttu-id="18ac7-1165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1165">'Let's Encrypt'</span></span> | <span data-ttu-id="18ac7-1166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1166">'Razor'</span></span> <span data-ttu-id="18ac7-1167">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1167">'SignalR' uid:</span></span> |
| `datetime` | `{dob:datetime}` | <span data-ttu-id="18ac7-1168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> | <span data-ttu-id="18ac7-1169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1169">'Blazor'</span></span> <span data-ttu-id="18ac7-1170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1170">'Identity'</span></span>|
| `decimal` | `{price:decimal}` | <span data-ttu-id="18ac7-1171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1171">'Let's Encrypt'</span></span> | <span data-ttu-id="18ac7-1172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1172">'Razor'</span></span> <span data-ttu-id="18ac7-1173">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1173">'SignalR' uid:</span></span>|
| `double` | `{weight:double}` | <span data-ttu-id="18ac7-1174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> | <span data-ttu-id="18ac7-1175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1175">'Blazor'</span></span> <span data-ttu-id="18ac7-1176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1176">'Identity'</span></span>|
| `float` | `{weight:float}` | <span data-ttu-id="18ac7-1177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1177">'Let's Encrypt'</span></span> | <span data-ttu-id="18ac7-1178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1178">'Razor'</span></span> <span data-ttu-id="18ac7-1179">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1179">'SignalR' uid:</span></span>|
| `guid` | `{id:guid}` | <span data-ttu-id="18ac7-1180">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1180">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> | <span data-ttu-id="18ac7-1181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1181">'Blazor'</span></span> |
| `long` | `{ticks:long}` | <span data-ttu-id="18ac7-1182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1182">'Identity'</span></span> | <span data-ttu-id="18ac7-1183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1183">'Let's Encrypt'</span></span> |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | <span data-ttu-id="18ac7-1184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1184">'Razor'</span></span> |
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | <span data-ttu-id="18ac7-1185">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1185">'SignalR' uid:</span></span> |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | <span data-ttu-id="18ac7-1186">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1186">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | <span data-ttu-id="18ac7-1187">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1187">'Blazor'</span></span> |
| `min(value)` | `{age:min(18)}` | `19` | <span data-ttu-id="18ac7-1188">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1188">'Identity'</span></span> |
| `max(value)` | `{age:max(120)}` | `91` | <span data-ttu-id="18ac7-1189">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1189">'Let's Encrypt'</span></span> |
| `range(min,max)` | `{age:range(18,120)}` | `91` | <span data-ttu-id="18ac7-1190">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1190">'Razor'</span></span> |
| `alpha` | `{name:alpha}` | `Rick` | <span data-ttu-id="18ac7-1191">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1191">'SignalR' uid:</span></span>  <span data-ttu-id="18ac7-1192">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1192">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | <span data-ttu-id="18ac7-1193">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1193">'Blazor'</span></span> <span data-ttu-id="18ac7-1194">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1194">'Identity'</span></span> |
| `required` | `{name:required}` | `Rick` | <span data-ttu-id="18ac7-1195">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1195">'Let's Encrypt'</span></span> |

<span data-ttu-id="18ac7-1196">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1196">'Razor'</span></span> <span data-ttu-id="18ac7-1197">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1197">'SignalR' uid:</span></span>

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> <span data-ttu-id="18ac7-1198">----- | :---: |  --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1198">----- | :---: |  --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-1199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1199">'Blazor'</span></span> <span data-ttu-id="18ac7-1200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1200">'Identity'</span></span> <span data-ttu-id="18ac7-1201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1201">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-1202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1202">'Razor'</span></span>

## <a name="regular-expressions"></a><span data-ttu-id="18ac7-1203">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1203">'SignalR' uid:</span></span>

<span data-ttu-id="18ac7-1204">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1204">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-1205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1205">'Blazor'</span></span>

<span data-ttu-id="18ac7-1206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1206">'Identity'</span></span> <span data-ttu-id="18ac7-1207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1207">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-1208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1208">'Razor'</span></span>

* <span data-ttu-id="18ac7-1209">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1209">'SignalR' uid:</span></span>
* <span data-ttu-id="18ac7-1210">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1210">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="18ac7-1211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1211">'Blazor'</span></span>

<span data-ttu-id="18ac7-1212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1212">'Identity'</span></span> <span data-ttu-id="18ac7-1213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1213">'Let's Encrypt'</span></span>

| <span data-ttu-id="18ac7-1214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1214">'Razor'</span></span>    | <span data-ttu-id="18ac7-1215">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1215">'SignalR' uid:</span></span>     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

<span data-ttu-id="18ac7-1216">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1216">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-1217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1217">'Blazor'</span></span> <span data-ttu-id="18ac7-1218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1218">'Identity'</span></span> <span data-ttu-id="18ac7-1219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1219">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-1220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1220">'Razor'</span></span>

| <span data-ttu-id="18ac7-1221">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1221">'SignalR' uid:</span></span>   | <span data-ttu-id="18ac7-1222">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1222">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>    | <span data-ttu-id="18ac7-1223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1223">'Blazor'</span></span> | <span data-ttu-id="18ac7-1224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1224">'Identity'</span></span>               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | <span data-ttu-id="18ac7-1225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1225">'Let's Encrypt'</span></span>     | <span data-ttu-id="18ac7-1226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1226">'Razor'</span></span>   | <span data-ttu-id="18ac7-1227">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1227">'SignalR' uid:</span></span>     |
| `[a-z]{2}`   | <span data-ttu-id="18ac7-1228">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1228">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> | <span data-ttu-id="18ac7-1229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1229">'Blazor'</span></span>   | <span data-ttu-id="18ac7-1230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1230">'Identity'</span></span>     |
| `[a-z]{2}`   | <span data-ttu-id="18ac7-1231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1231">'Let's Encrypt'</span></span>        | <span data-ttu-id="18ac7-1232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1232">'Razor'</span></span>   | <span data-ttu-id="18ac7-1233">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1233">'SignalR' uid:</span></span>    |
| `[a-z]{2}`   | <span data-ttu-id="18ac7-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>        | <span data-ttu-id="18ac7-1235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1235">'Blazor'</span></span>   | <span data-ttu-id="18ac7-1236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1236">'Identity'</span></span>    |
| `^[a-z]{2}$` | <span data-ttu-id="18ac7-1237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1237">'Let's Encrypt'</span></span>     | <span data-ttu-id="18ac7-1238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1238">'Razor'</span></span>    | <span data-ttu-id="18ac7-1239">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1239">'SignalR' uid:</span></span> |
| `^[a-z]{2}$` | <span data-ttu-id="18ac7-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> | <span data-ttu-id="18ac7-1241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1241">'Blazor'</span></span>    | <span data-ttu-id="18ac7-1242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1242">'Identity'</span></span> |

<span data-ttu-id="18ac7-1243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1243">'Let's Encrypt'</span></span>

<span data-ttu-id="18ac7-1244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1244">'Razor'</span></span> <span data-ttu-id="18ac7-1245">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1245">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-1246">---------- | | `[a-z]{2}`   | hello     | Ja   | Teilzeichenfolge stimmt überein     | | `[a-z]{2}`   | 123abc456 | Ja   | Teilzeichenfolge stimmt überein     | | `[a-z]{2}`   | mz        | Ja   | Stimmt mit Ausdruck überein    | | `[a-z]{2}`   | MZ        | Ja   | Groß-/Kleinschreibung wird nicht berücksichtigt    | | `^[a-z]{2}$` | hello     | Nein    | Siehe `^` und `$` oben | | `^[a-z]{2}$` | 123abc456 | Nein    | Siehe `^` und `$` oben |</span><span class="sxs-lookup"><span data-stu-id="18ac7-1246">---------- | | `[a-z]{2}`   | hello     | Yes   | Substring matches     | | `[a-z]{2}`   | 123abc456 | Yes   | Substring matches     | | `[a-z]{2}`   | mz        | Yes   | Matches expression    | | `[a-z]{2}`   | MZ        | Yes   | Not case sensitive    | | `^[a-z]{2}$` | hello     | No    | See `^` and `$` above | | `^[a-z]{2}$` | 123abc456 | No    | See `^` and `$` above |</span></span> <span data-ttu-id="18ac7-1247">Weitere Informationen zur Syntax von regulären Ausdrücken finden Sie unter [Sprachelemente für reguläre Ausdrücke – Kurzübersicht](/dotnet/standard/base-types/regular-expression-language-quick-reference).</span><span class="sxs-lookup"><span data-stu-id="18ac7-1247">For more information on regular expression syntax, see [.NET Framework Regular Expressions](/dotnet/standard/base-types/regular-expression-language-quick-reference).</span></span>

## <a name="custom-route-constraints"></a><span data-ttu-id="18ac7-1248">Einen regulären Ausdruck können Sie verwenden, um einen Parameter auf zulässige Werte einzuschränken.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1248">To constrain a parameter to a known set of possible values, use a regular expression.</span></span>

<span data-ttu-id="18ac7-1249">Mit `{action:regex(^(list|get|create)$)}` werden beispielsweise für den `action`-Routenwert nur die Werte `list`, `get` oder `create` abgeglichen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1249">For example, `{action:regex(^(list|get|create)$)}` only matches the `action` route value to `list`, `get`, or `create`.</span></span> <span data-ttu-id="18ac7-1250">Wenn die Zeichenfolge `^(list|get|create)$` dem Einschränkungswörterbuch übergeben wird, führt dies zum gleichen Ergebnis.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1250">If passed into the constraints dictionary, the string `^(list|get|create)$` is equivalent.</span></span>

<span data-ttu-id="18ac7-1251">Auch Einschränkungen, die dem zugehörigen Wörterbuch hinzugefügt werden und mit keiner vorgegebenen Einschränkung übereinstimmen, werden als reguläre Ausdrücke behandelt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1251">Constraints that are passed in the constraints dictionary that don't match one of the known constraints are also treated as regular expressions.</span></span> <span data-ttu-id="18ac7-1252">Einschränkungen, die innerhalb einer Vorlage übergeben werden und mit keiner vorgegebenen Einschränkung übereinstimmen, werden nicht als reguläre Ausdrücke behandelt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1252">Constraints that are passed  within a template that don't match one of the known constraints are not treated as regular expressions.</span></span> <span data-ttu-id="18ac7-1253">Benutzerdefinierte Routeneinschränkungen</span><span class="sxs-lookup"><span data-stu-id="18ac7-1253">Custom route constraints</span></span> <span data-ttu-id="18ac7-1254">Benutzerdefinierte Routeneinschränkungen können durch Implementierung der <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>-Schnittstelle erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1254">Custom route constraints can be created by implementing the <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface.</span></span>

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

<span data-ttu-id="18ac7-1255">Die `IRouteConstraint`-Schnittstelle umfasst die <xref:System.Web.Routing.IRouteConstraint.Match*>-Methode, die `true` zurückgibt, wenn die Einschränkung erfüllt wird, und andernfalls `false`.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1255">The `IRouteConstraint` interface contains <xref:System.Web.Routing.IRouteConstraint.Match*>, which returns `true` if the constraint is satisfied and `false` otherwise.</span></span> <span data-ttu-id="18ac7-1256">Benutzerdefinierte Routeneinschränkungen werden nur selten benötigt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1256">Custom route constraints are rarely needed.</span></span>

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="parameter-transformer-reference"></a><span data-ttu-id="18ac7-1257">Bevor Sie eine benutzerdefinierte Routeneinschränkung implementieren, sollten Sie Alternativen in Betracht ziehen, wie z. B. Modellbindung.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1257">Before implementing a custom route constraint, consider alternatives, such as model binding.</span></span>

<span data-ttu-id="18ac7-1258">Der ASP.NET Core-Ordner [Constraints](https://github.com/dotnet/aspnetcore/tree/master/src/Http/Routing/src/Constraints) bietet nützliche Beispiele für die Erstellung einer Einschränkung.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1258">The ASP.NET Core [Constraints](https://github.com/dotnet/aspnetcore/tree/master/src/Http/Routing/src/Constraints) folder provides good examples of creating a constraints.</span></span>

* <span data-ttu-id="18ac7-1259">Beispiel: [GuidRouteConstraint](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Constraints/GuidRouteConstraint.cs#L18).</span><span class="sxs-lookup"><span data-stu-id="18ac7-1259">For example, [GuidRouteConstraint](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Constraints/GuidRouteConstraint.cs#L18).</span></span>
* <span data-ttu-id="18ac7-1260">Zum Verwenden eines benutzerdefinierten `IRouteConstraint`-Elements muss der Routeneinschränkungstyp bei der <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>-Eigenschaft der App im Dienstcontainer registriert werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1260">To use a custom `IRouteConstraint`, the route constraint type must be registered with the app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in the service container.</span></span>
* <span data-ttu-id="18ac7-1261">Eine `ConstraintMap` ist ein Wörterbuch, das Routeneinschränkungsschlüssel `IRouteConstraint`-Implementierungen zuordnet, die diese Einschränkungen überprüfen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1261">A `ConstraintMap` is a dictionary that maps route constraint keys to `IRouteConstraint` implementations that validate those constraints.</span></span>
* <span data-ttu-id="18ac7-1262">Die `ConstraintMap` einer App kann in `Startup.ConfigureServices` entweder als Teil eines [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*)-Aufrufs oder durch direktes Konfigurieren von <xref:Microsoft.AspNetCore.Routing.RouteOptions> mit `services.Configure<RouteOptions>` aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1262">An app's `ConstraintMap` can be updated in `Startup.ConfigureServices` either as part of a [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) call or by configuring <xref:Microsoft.AspNetCore.Routing.RouteOptions> directly with `services.Configure<RouteOptions>`.</span></span>
* <span data-ttu-id="18ac7-1263">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1263">For example:</span></span>

<span data-ttu-id="18ac7-1264">Die vorangehende Einschränkung wird im folgenden Code angewendet:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1264">The preceding constraint is applied in the following code:</span></span>

<span data-ttu-id="18ac7-1265">Die Implementierung von `MyCustomConstraint` verhindert die Anwendung von `0` auf einen Routenparameter:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1265">The implementation of `MyCustomConstraint` prevents `0` being applied to a route parameter:</span></span>

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

<span data-ttu-id="18ac7-1266">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1266">The preceding code:</span></span> <span data-ttu-id="18ac7-1267">Verhindert, dass `0` im `{id}`-Segment der Route vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1267">Prevents `0` in the `{id}` segment of the route.</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

<span data-ttu-id="18ac7-1268">Dient als einfaches Beispiel für die Implementierung einer benutzerdefinierten Einschränkung.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1268">Is shown to provide a basic example of implementing a custom constraint.</span></span> <span data-ttu-id="18ac7-1269">Es sollte nicht in einer Produktions-App eingesetzt werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1269">It should not be used in a production app.</span></span> <span data-ttu-id="18ac7-1270">Der folgende Code bietet einen besseren Ansatz, um zu verhindern, dass eine `id` mit einer `0` verarbeitet wird:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1270">The following code is a better approach to preventing an `id` containing a `0` from being processed:</span></span>

<span data-ttu-id="18ac7-1271">Der vorangehende Code bietet im Vergleich zum `MyCustomConstraint`-Ansatz folgende Vorteile:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1271">The preceding code has the following advantages over the `MyCustomConstraint` approach:</span></span>

* <span data-ttu-id="18ac7-1272">Eine benutzerdefinierte Einschränkung ist nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1272">It doesn't require a custom constraint.</span></span> <span data-ttu-id="18ac7-1273">Es wird ein beschreibender Fehler zurückgegeben, wenn der Routenparameter `0` enthält.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1273">It returns a more descriptive error when the route parameter includes `0`.</span></span> <span data-ttu-id="18ac7-1274">Parametertransformatorreferenz</span><span class="sxs-lookup"><span data-stu-id="18ac7-1274">Parameter transformer reference</span></span> <span data-ttu-id="18ac7-1275">Parametertransformatoren:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1275">Parameter transformers:</span></span>
* <span data-ttu-id="18ac7-1276">Werden beim Generieren eines Links mit <xref:Microsoft.AspNetCore.Routing.LinkGenerator> ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1276">Execute when generating a link using <xref:Microsoft.AspNetCore.Routing.LinkGenerator>.</span></span> <span data-ttu-id="18ac7-1277">Implementieren Sie <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1277">Implement <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer?displayProperty=fullName>.</span></span> <span data-ttu-id="18ac7-1278">Werden mithilfe von <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1278">Are configured using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>.</span></span> <span data-ttu-id="18ac7-1279">Nehmen den Routenwert des Parameters an und transformieren ihn in einen neuen Zeichenfolgenwert.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1279">Take the parameter's route value and transform it to a new string value.</span></span>

## <a name="url-generation-reference"></a><span data-ttu-id="18ac7-1280">Haben die Verwendung des transformierten Wert in einem generierten Link zur Folge.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1280">Result in using the transformed value in the generated link.</span></span>

<span data-ttu-id="18ac7-1281">Beispielsweise generiert ein benutzerdefinierter Parametertransformator `slugify` im Routenmuster `blog\{article:slugify}` mit `Url.Action(new { article = "MyTestArticle" })``blog\my-test-article`.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1281">For example, a custom `slugify` parameter transformer in route pattern `blog\{article:slugify}` with `Url.Action(new { article = "MyTestArticle" })` generates `blog\my-test-article`.</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

<span data-ttu-id="18ac7-1282">Betrachten Sie die folgende Implementierung von `IOutboundParameterTransformer`:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1282">Consider the following `IOutboundParameterTransformer` implementation:</span></span> <span data-ttu-id="18ac7-1283">Um einen Parametertransformator in einem Routenmuster zu verwenden, konfigurieren Sie ihn mit <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1283">To use a parameter transformer in a route pattern, configure it using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in `Startup.ConfigureServices`:</span></span> <span data-ttu-id="18ac7-1284">Das ASP.NET Core-Framework verwendet Parametertransformatoren, um den URI zu transformieren, zu dem ein Endpunkt aufgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1284">The ASP.NET Core framework uses parameter transformers to transform the URI where an endpoint resolves.</span></span>

<span data-ttu-id="18ac7-1285">Beispielsweise wandeln Parametertransformatoren die Routenwerte um, die zum Zuordnen folgender Elemente verwendet werden: `area`, `controller`, `action` und `page`.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1285">For example, parameter transformers transform the route values used to match an `area`, `controller`, `action`, and `page`.</span></span> <span data-ttu-id="18ac7-1286">Mit der vorstehenden Routenvorlage wird die Aktion `SubscriptionManagementController.GetAll` dem URI `/subscription-management/get-all` zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1286">With the preceding route template, the action `SubscriptionManagementController.GetAll` is matched with the URI `/subscription-management/get-all`.</span></span> <span data-ttu-id="18ac7-1287">Ein Parametertransformator ändert nicht die zum Generieren eines Links verwendeten Routenwerte.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1287">A parameter transformer doesn't change the route values used to generate a link.</span></span> <span data-ttu-id="18ac7-1288">Beispielsweise gibt `Url.Action("GetAll", "SubscriptionManagement")``/subscription-management/get-all` aus.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1288">For example, `Url.Action("GetAll", "SubscriptionManagement")` outputs `/subscription-management/get-all`.</span></span>

<span data-ttu-id="18ac7-1289">ASP.NET Core bietet API-Konventionen für die Verwendung von Parametertransformatoren mit generierten Routen:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1289">ASP.NET Core provides API conventions for using parameter transformers with generated routes:</span></span> <span data-ttu-id="18ac7-1290">Die <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention?displayProperty=fullName>-Konvention wendet einen angegebenen Parametertransformator auf alle Attributrouten in der App an.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1290">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention?displayProperty=fullName> MVC convention applies a specified parameter transformer to all attribute routes in the app.</span></span> <span data-ttu-id="18ac7-1291">Der Parametertransformator transformiert Attributroutentoken, wenn diese ersetzt werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1291">The parameter transformer transforms attribute route tokens as they are replaced.</span></span>

<span data-ttu-id="18ac7-1292">Weitere Informationen finden Sie unter [Verwenden eines Parametertransformators zum Anpassen der Tokenersetzung](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).</span><span class="sxs-lookup"><span data-stu-id="18ac7-1292">For more information, see [Use a parameter transformer to customize token replacement](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).</span></span> <span data-ttu-id="18ac7-1293"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention> Pages verwendet die Razor-API-Konvention.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1293">Razor Pages uses the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention> API convention.</span></span>

| <span data-ttu-id="18ac7-1294">Diese Konvention wendet einen angegebenen Parametertransformator auf alle automatisch erkannten Razor Pages-Seiten an.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1294">This convention applies a specified parameter transformer to all automatically discovered Razor Pages.</span></span>                     | <span data-ttu-id="18ac7-1295">Der Parametertransformator transformiert die Ordner- und Dateinamensegmente von Razor Pages-Routen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1295">The parameter transformer transforms the folder and file name segments of Razor Pages routes.</span></span>                        | <span data-ttu-id="18ac7-1296">Weitere Informationen finden Sie unter [Verwenden eines Parametertransformators zum Anpassen von Seitenrouten](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).</span><span class="sxs-lookup"><span data-stu-id="18ac7-1296">For more information, see [Use a parameter transformer to customize page routes](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).</span></span>                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| <span data-ttu-id="18ac7-1297">Referenz für URL-Generierung</span><span class="sxs-lookup"><span data-stu-id="18ac7-1297">URL generation reference</span></span>                | <span data-ttu-id="18ac7-1298">Dieser Abschnitt enthält eine Referenz für den Algorithmus, der durch die URL-Generierung implementiert wird.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1298">This section contains a reference for the algorithm implemented by URL generation.</span></span>                       | `/Home/About`           |
| <span data-ttu-id="18ac7-1299">In der Praxis werden bei den meisten komplexen Beispielen für die URL-Generierung Controller oder Razor Pages verwendet.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1299">In practice, most complex examples of URL generation use controllers or Razor Pages.</span></span>                | <span data-ttu-id="18ac7-1300">Weitere Informationen finden Sie unter [Routing in Controllern](xref:mvc/controllers/routing) (Routing in Controllern).</span><span class="sxs-lookup"><span data-stu-id="18ac7-1300">See  [routing in controllers](xref:mvc/controllers/routing) for additional information.</span></span> | `/Order/About`          |
| <span data-ttu-id="18ac7-1301">Die URL-Generierung beginnt mit einem Aufruf von [LinkGenerator.GetPathByAddress](xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*) oder einer ähnlichen Methode.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1301">The URL generation process begins with a call to [LinkGenerator.GetPathByAddress](xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*) or a similar method.</span></span> | <span data-ttu-id="18ac7-1302">Die Methode wird mit einer Adresse, mehreren Routenwerten und optional mit Informationen zur aktuellen Anforderung von `HttpContext` versehen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1302">The method is provided with an address, a set of route values, and optionally information about the current request from `HttpContext`.</span></span>                       | `/Home/About`           |
| <span data-ttu-id="18ac7-1303">Im ersten Schritt wird die Adresse verwendet, um bestimmte Endpunktkandidaten mithilfe einer [`IEndpointAddressScheme<TAddress>`](xref:Microsoft.AspNetCore.Routing.IEndpointAddressScheme`1)-Schnittstelle aufzulösen, die dem Adresstyp entspricht.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1303">The first step is to use the address to resolve a set of candidate endpoints using an [`IEndpointAddressScheme<TAddress>`](xref:Microsoft.AspNetCore.Routing.IEndpointAddressScheme`1) that matches the address's type.</span></span>                | <span data-ttu-id="18ac7-1304">Sobald eine Kandidatengruppe anhand des Adressschemas gefunden wurde, werden die Endpunkte geordnet und iterativ verarbeitet, bis die URL-Generierung erfolgreich abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1304">Once of set of candidates is found by the address scheme, the endpoints are ordered and processed iteratively until a URL generation operation succeeds.</span></span>        | `/Home/About?color=Red` |

<span data-ttu-id="18ac7-1305">Bei der URL-Generierung wird **nicht** auf Mehrdeutigkeiten geprüft, daher ist das erste zurückgegebene Ergebnis das Endergebnis.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1305">URL generation does **not** check for ambiguities, the first result returned is the final result.</span></span>

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

<span data-ttu-id="18ac7-1306">Behandeln von Problemen mit der Protokollierung bei der URL-Generierung</span><span class="sxs-lookup"><span data-stu-id="18ac7-1306">Troubleshooting URL generation with logging</span></span>

## <a name="complex-segments"></a><span data-ttu-id="18ac7-1307">Der erste Schritt bei der Behebung von Problemen bei der URL-Generierung ist die Einstellung des Protokolliergrads von `Microsoft.AspNetCore.Routing` auf `TRACE`.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1307">The first step in troubleshooting URL generation is setting the logging level of `Microsoft.AspNetCore.Routing` to `TRACE`.</span></span>

<span data-ttu-id="18ac7-1308">`LinkGenerator` protokolliert viele Details über die Verarbeitung, die bei der Problembehebung nützlich sein können.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1308">`LinkGenerator` logs many details about its processing which can be useful to troubleshoot problems.</span></span> <span data-ttu-id="18ac7-1309">Ausführliche Informationen zur URL-Generierung finden Sie unter [Referenz für URL-Generierung](#ugr).</span><span class="sxs-lookup"><span data-stu-id="18ac7-1309">See [URL generation reference](#ugr) for details on URL generation.</span></span> <span data-ttu-id="18ac7-1310">Adressen</span><span class="sxs-lookup"><span data-stu-id="18ac7-1310">Addresses</span></span>
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/dotnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="18ac7-1311">Mithilfe von Adressen wird bei der URL-Generierung ein Aufruf in der API zur Linkgenerierung an mehrere Endpunktkandidaten gebunden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1311">Addresses are the concept in URL generation used to bind a call into the link generator to a set of candidate endpoints.</span></span> <span data-ttu-id="18ac7-1312">Adressen sind ein erweiterbares Konzept, das standardmäßig mit zwei Implementierungen bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1312">Addresses are an extensible concept that come with two implementations by default:</span></span> <span data-ttu-id="18ac7-1313">Die Verwendung von *Endpunktname* (`string`) als Adresse:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1313">Using *endpoint name* (`string`) as the address:</span></span> <span data-ttu-id="18ac7-1314">Bietet ähnliche Funktionalität wie der Routenname von MVC.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1314">Provides similar functionality to MVC's route name.</span></span>

<span data-ttu-id="18ac7-1315">Wird der <xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata>-Metadatentyp verwendet.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1315">Uses the <xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata> metadata type.</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

> [!IMPORTANT]
> <span data-ttu-id="18ac7-1316">Löst die bereitgestellte Zeichenfolge anhand der Metadaten aller registrierten Endpunkte auf.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1316">Resolves the provided string against the metadata of all registered endpoints.</span></span> <span data-ttu-id="18ac7-1317">Löst beim Start eine Ausnahme aus, wenn mehrere Endpunkte den gleichen Namen aufweisen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1317">Throws an exception on startup if multiple endpoints use the same name.</span></span> <span data-ttu-id="18ac7-1318">Wird für die allgemeine Verwendung außerhalb von Controllern und Razor Pages empfohlen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1318">Recommended for general-purpose use outside of controllers and Razor Pages.</span></span>

<span data-ttu-id="18ac7-1319">Die Verwendung von *Routenwerten* (<xref:Microsoft.AspNetCore.Routing.RouteValuesAddress>) als Adresse:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1319">Using *route values* (<xref:Microsoft.AspNetCore.Routing.RouteValuesAddress>) as the address:</span></span>

## <a name="routing-basics"></a><span data-ttu-id="18ac7-1320">Bietet eine ähnliche Funktionalität wie die veraltete Funktion zur URL-Generierung von Controllern und Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1320">Provides similar functionality to controllers and Razor Pages legacy URL generation.</span></span>

<span data-ttu-id="18ac7-1321">Lässt sich nur schwer erweitern und debuggen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1321">Very complex to extend and debug.</span></span> <span data-ttu-id="18ac7-1322">Bietet die Implementierung, die von `IUrlHelper`, Taghilfsprogrammen, HTML-Hilfsprogrammen, Aktionsergebnissen usw. verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1322">Provides the implementation used by `IUrlHelper`, Tag Helpers, HTML Helpers, Action Results, etc.</span></span>

* <span data-ttu-id="18ac7-1323">Aufgabe des Adressschemas ist es, die Verbindung zwischen der Adresse und den übereinstimmenden Endpunkten anhand von beliebigen Kriterien herzustellen:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1323">The role of the address scheme is to make the association between the address and matching endpoints by arbitrary criteria:</span></span>
* <span data-ttu-id="18ac7-1324">Das Schema für Endpunktenamen führt eine allgemeine Wörterbuchsuche durch.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1324">The endpoint name scheme performs a basic dictionary lookup.</span></span>

<span data-ttu-id="18ac7-1325">Das Schema der Routenwerte weist eine komplexe beste Teilmenge des Mengenalgorithmus auf.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1325">The route values scheme has a complex best subset of set algorithm.</span></span>

<span data-ttu-id="18ac7-1326">Umgebungswerte und explizite Werte</span><span class="sxs-lookup"><span data-stu-id="18ac7-1326">Ambient values and explicit values</span></span> <span data-ttu-id="18ac7-1327">Aus der aktuellen Anforderung greift das Routing auf die Routenwerte der aktuellen Anforderung `HttpContext.Request.RouteValues` zu.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1327">From the current request, routing accesses the route values of the current request `HttpContext.Request.RouteValues`.</span></span> <span data-ttu-id="18ac7-1328">Die mit der aktuellen Anforderung verbundenen Werte werden als **Umgebungswerte** bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1328">The values associated with the current request are referred to as the **ambient values**.</span></span>

<span data-ttu-id="18ac7-1329">Aus Gründen der Übersichtlichkeit werden in der Dokumentation die an die Methoden übergebenen Routenwerte als **explizite Werte** bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1329">For the purpose of clarity, the documentation refers to the route values passed in to methods as **explicit values**.</span></span> <span data-ttu-id="18ac7-1330">Das folgende Beispiel zeigt Umgebungswerte und explizite Werte.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1330">The following example shows ambient values and explicit values.</span></span> <span data-ttu-id="18ac7-1331">Er liefert Umgebungswerte aus der aktuellen Anforderung und explizite Werte: `{ id = 17, }`:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1331">It provides ambient values from the current request and explicit values: `{ id = 17, }`:</span></span>

<span data-ttu-id="18ac7-1332">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1332">The preceding code:</span></span> <span data-ttu-id="18ac7-1333">Gibt `/Widget/Index/17` zurück.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1333">Returns `/Widget/Index/17`</span></span>

<span data-ttu-id="18ac7-1334">Ruft <xref:Microsoft.AspNetCore.Routing.LinkGenerator> über [DI](xref:fundamentals/dependency-injection) ab.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1334">Gets <xref:Microsoft.AspNetCore.Routing.LinkGenerator> via [DI](xref:fundamentals/dependency-injection).</span></span>

* <span data-ttu-id="18ac7-1335">Der folgende Code liefert keine Umgebungswerte und keine expliziten Werte: `{ controller = "Home", action = "Subscribe", id = 17, }`:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1335">The following code provides no ambient values and explicit values: `{ controller = "Home", action = "Subscribe", id = 17, }`:</span></span>
* <span data-ttu-id="18ac7-1336">Die vorhergehende Methode gibt `/Home/Subscribe/17` zurück</span><span class="sxs-lookup"><span data-stu-id="18ac7-1336">The preceding  method returns `/Home/Subscribe/17`</span></span>

<span data-ttu-id="18ac7-1337">Der folgende Code in `WidgetController` gibt `/Widget/Subscribe/17` zurück:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1337">The following code in the `WidgetController` returns `/Widget/Subscribe/17`:</span></span> <span data-ttu-id="18ac7-1338">Der folgende Code stellt den Controller aus den Umgebungswerten in der aktuellen Anforderung und explizite Werte dar: `{ action = "Edit", id = 17, }`:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1338">The following code provides the controller from ambient values in the current request and explicit values: `{ action = "Edit", id = 17, }`:</span></span> <span data-ttu-id="18ac7-1339">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1339">In the preceding code:</span></span> <span data-ttu-id="18ac7-1340">`/Gadget/Edit/17` wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1340">`/Gadget/Edit/17` is returned.</span></span>

<span data-ttu-id="18ac7-1341"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.Url> ruft die <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>-Schnittstelle ab.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1341"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.Url> gets the <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>.</span></span>

* <span data-ttu-id="18ac7-1342">generiert eine URL mit einem absoluten Pfad für eine Aktionsmethode.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1342">generates a URL with an absolute path for an action method.</span></span>
* <span data-ttu-id="18ac7-1343">Die URL enthält den angegebenen `action`-Namen und `route`-Werte.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1343">The URL contains the specified `action` name and `route` values.</span></span>
* <span data-ttu-id="18ac7-1344">Sie liefert Umgebungswerte aus der aktuellen Anforderung und explizite Werte: `{ page = "./Edit, id = 17, }`:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1344">The following code provides ambient values from the current request and explicit values: `{ page = "./Edit, id = 17, }`:</span></span>
* <span data-ttu-id="18ac7-1345">Im vorangehenden Code wird `url` auf `/Edit/17` festgelegt, wenn die Option zum Bearbeiten der Razor Page die folgende Seitenanweisung enthält:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1345">The preceding code sets `url` to  `/Edit/17` when the Edit Razor Page contains the following page directive:</span></span>
* <span data-ttu-id="18ac7-1346">Wenn die Routenvorlage `"{id:int}"` nicht in der Seite „Bearbeiten“ enthalten ist, ist `url` gleich `/Edit?id=17`.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1346">If the Edit page doesn't contain the `"{id:int}"` route template, `url` is `/Edit?id=17`.</span></span>
* <span data-ttu-id="18ac7-1347">Das Verhalten der <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>-Schnittstelle von MVC fügt zusätzlich zu den hier beschriebenen Regeln eine weitere Komplexitätsebene hinzu:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1347">The behavior of MVC's <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> adds a layer of complexity in addition to the rules described here:</span></span> <span data-ttu-id="18ac7-1348">`IUrlHelper` liefert immer die Routenwerte aus der aktuellen Anforderung als Umgebungswerte.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1348">`IUrlHelper` always provides the route values from the current request as ambient values.</span></span>
<!-- fix [middleware](xref:fundamentals/middleware/index) -->
<span data-ttu-id="18ac7-1349">[IUrlHelper.action](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*) kopiert immer die aktuellen Routenwerte `action` und `controller` als explizite Werte, sofern sie nicht vom Entwickler außer Kraft gesetzt werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1349">[IUrlHelper.Action](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*) always copies the current `action` and `controller` route values as explicit values unless overridden by the developer.</span></span> <span data-ttu-id="18ac7-1350">[IUrlHelper.page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*) kopiert immer den aktuellen Routenwert `page` als expliziten Wert, sofern er nicht außer Kraft gesetzt wird.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1350">[IUrlHelper.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*) always copies the current `page` route value as an explicit value unless overridden.</span></span> <span data-ttu-id="18ac7-1351">`IUrlHelper.Page` setzt immer den aktuellen Routenwert `handler` mit `null` als expliziten Wert außer Kraft, sofern er nicht außer Kraft gesetzt wird.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1351">`IUrlHelper.Page` always overrides the current `handler` route value with `null` as an explicit values unless overridden.</span></span>

### <a name="url-matching"></a><span data-ttu-id="18ac7-1352">Benutzer sind oft von den Verhaltensdetails der Umgebungswerte überrascht, da MVC anscheinend nicht den eigenen Regeln folgt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1352">Users are often surprised by the behavioral details of ambient values, because MVC doesn't seem to follow its own rules.</span></span>

<span data-ttu-id="18ac7-1353">Aus Verlaufs- und Kompatibilitätsgründen weisen bestimmte Routenwerte wie `action`, `controller`, `page` und `handler` ein spezielles Verhalten auf.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1353">For historical and compatibility reasons, certain route values such as `action`, `controller`, `page`, and `handler` have their own special-case behavior.</span></span> <span data-ttu-id="18ac7-1354">Die äquivalente Funktionalität, die durch `LinkGenerator.GetPathByAction` und `LinkGenerator.GetPathByPage` bereitgestellt wird, verdoppelt diese Anomalien von `IUrlHelper` aus Kompatibilitätsgründen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1354">The equivalent functionality provided by `LinkGenerator.GetPathByAction` and `LinkGenerator.GetPathByPage` duplicates these anomalies of `IUrlHelper` for compatibility.</span></span> <span data-ttu-id="18ac7-1355">URL-Generierungsprozess</span><span class="sxs-lookup"><span data-stu-id="18ac7-1355">URL generation process</span></span>

<span data-ttu-id="18ac7-1356">Sobald die Gruppe der Endpunktkandidaten ermittelt ist, wird der URL-Generierungsalgorithmus angewendet:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1356">Once the set of candidate endpoints are found, the URL generation algorithm:</span></span> <span data-ttu-id="18ac7-1357">Die Endpunkte werden iterativ verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1357">Processes the endpoints iteratively.</span></span> <span data-ttu-id="18ac7-1358">Das erste erfolgreiche Ergebnis wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1358">Returns the first successful result.</span></span> <span data-ttu-id="18ac7-1359">Der erste Schritt in diesem Prozess wird als **Routenwertinvalidierung** bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1359">The first step in this process is called **route value invalidation**.</span></span>

<span data-ttu-id="18ac7-1360">Die Routenwertinvalidierung ist der Prozess, bei dem das Routing entscheidet, welche Routenwerte aus den Umgebungswerten verwendet und welche ignoriert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1360">Route value invalidation is the process by which routing decides which route values from the ambient values should be used and which should be ignored.</span></span> <span data-ttu-id="18ac7-1361">Jeder Umgebungswert wird berücksichtigt und entweder mit den expliziten Werten kombiniert oder aber ignoriert.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1361">Each ambient value is considered and either combined with the explicit values, or ignored.</span></span>

<span data-ttu-id="18ac7-1362">Denken Sie daran, dass Umgebungswerte Anwendungsentwicklern in allgemeinen Fällen das Schreiben von Code sparen können.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1362">The best way to think about the role of ambient values is that they attempt to save application developers typing, in some common cases.</span></span>

<span data-ttu-id="18ac7-1363">In der Regel sind die Szenarios, in denen Umgebungswerte hilfreich sind, mit MVC verknüpft:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1363">Traditionally, the scenarios where ambient values are helpful are related to MVC:</span></span> <span data-ttu-id="18ac7-1364">Bei der Verknüpfung mit einer anderen Aktion im gleichen Controller muss der Controllername nicht angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1364">When linking to another action in the same controller, the controller name doesn't need to be specified.</span></span>

<span data-ttu-id="18ac7-1365">Bei der Verknüpfung mit einem anderen Controller im gleichen Bereich muss der Bereich nicht angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1365">When linking to another controller in the same area, the area name doesn't need to be specified.</span></span> <span data-ttu-id="18ac7-1366">Bei der Verknüpfung mit der gleichen Aktionsmethode müssen keine Routenwerte angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1366">When linking to the same action method, route values don't need to be specified.</span></span> <span data-ttu-id="18ac7-1367">Bei der Verknüpfung mit einem anderen Teil der App sollen keine Routenwerte übertragen werden, die für diesen Teil der App irrelevant sind.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1367">When linking to another part of the app, you don't want to carry over route values that have no meaning in that part of the app.</span></span> <span data-ttu-id="18ac7-1368">Aufrufe an `LinkGenerator` oder `IUrlHelper`, die `null` zurückgeben, sind meist dadurch bedingt, dass die Routenwertinvalidierung nicht verstanden wurde.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1368">Calls to `LinkGenerator` or `IUrlHelper` that return `null` are usually caused by not understanding route value invalidation.</span></span>

<span data-ttu-id="18ac7-1369">Beheben Sie die Routenwertinvalidierung, indem Sie explizit mehr Routenwerte angeben, um zu prüfen, ob das Problem dadurch gelöst wird.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1369">Troubleshoot route value invalidation by explicitly specifying more of the route values to see if that solves the problem.</span></span> <span data-ttu-id="18ac7-1370">Bei der Routenwertinvalidierung wird davon ausgegangen, dass das URL-Schema der Anwendung hierarchisch ist, mit einer von links nach rechts gebildeten Hierarchie.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1370">Route value invalidation works on the assumption that the app's URL scheme is hierarchical, with a hierarchy formed from left-to-right.</span></span> <span data-ttu-id="18ac7-1371">Sehen Sie sich die einfache Controllerroutenvorlage `{controller}/{action}/{id?}` an, um ein Gespür dafür zu bekommen, wie dies in der Praxis funktioniert.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1371">Consider the basic controller route template `{controller}/{action}/{id?}` to get an intuitive sense of how this works in practice.</span></span> <span data-ttu-id="18ac7-1372">Durch eine **Änderung** auf einen Wert werden alle rechts angezeigten Routenwerte **ungültig**.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1372">A **change** to a value **invalidates** all of the route values that appear to the right.</span></span> <span data-ttu-id="18ac7-1373">Dies spricht für die These von der Hierarchie.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1373">This reflects the assumption about hierarchy.</span></span>

<a name="lg"></a>

### <a name="url-generation"></a><span data-ttu-id="18ac7-1374">Wenn die App einen Umgebungswert für `id` hat und der Vorgang einen anderen Wert für `controller` angibt:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1374">If the app has an ambient value for `id`, and the operation specifies a different value for the `controller`:</span></span>

<span data-ttu-id="18ac7-1375">`id` wird nicht wiederverwendet, weil `{controller}` links von `{id?}` steht.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1375">`id` won't be reused because `{controller}` is to the left of `{id?}`.</span></span> <span data-ttu-id="18ac7-1376">Einige Beispiele veranschaulichen dieses Prinzip:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1376">Some examples demonstrating this principle:</span></span>

<span data-ttu-id="18ac7-1377">Wenn die expliziten Werte einen Wert für `id` enthalten, wird der Umgebungswert für `id` ignoriert.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1377">If the explicit values contain a value for `id`, the ambient value for `id` is ignored.</span></span> <span data-ttu-id="18ac7-1378">Die Umgebungswerte für `controller` und `action` können verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1378">The ambient values for `controller` and `action` can be used.</span></span>

<span data-ttu-id="18ac7-1379">Wenn die expliziten Werte einen Wert für `action` enthalten, wird jeder Umgebungswert für `action` ignoriert.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1379">If the explicit values contain a value for `action`, any ambient value for `action` is ignored.</span></span>

* <span data-ttu-id="18ac7-1380">Die Umgebungswerte für `controller` können verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1380">The ambient values for `controller` can be used.</span></span>
* <span data-ttu-id="18ac7-1381">Wenn sich der explizite Wert für `action` von dem Umgebungswert für `action` unterscheidet, wird der Wert `id` nicht verwendet.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1381">If the explicit value for `action` is different from the ambient value for `action`, the `id` value won't be used.</span></span>
* <span data-ttu-id="18ac7-1382">Wenn der explizite Wert für `action` mit dem Umgebungswert für `action` übereinstimmt, kann der Wert `id` verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1382">If the explicit value for `action` is the same as the ambient value for `action`, the `id` value can be used.</span></span>

<span data-ttu-id="18ac7-1383">Wenn die expliziten Werte einen Wert für `controller` enthalten, wird jeder Umgebungswert für `controller` ignoriert.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1383">If the explicit values contain a value for `controller`, any ambient value for `controller` is ignored.</span></span> <span data-ttu-id="18ac7-1384">Wenn sich der explizite Wert für `controller` von dem Umgebungswert für `controller` unterscheidet, werden die Werte `action` und `id` nicht verwendet.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1384">If the explicit value for `controller` is different from the ambient value for `controller`, the `action` and `id` values won't be used.</span></span> <span data-ttu-id="18ac7-1385">Wenn der explizite Wert für `controller` mit dem Umgebungswert für `controller` übereinstimmt, können die Werte `action` und `id` verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1385">If the explicit value for `controller` is the same as the ambient value for `controller`, the `action` and `id` values can be used.</span></span> <span data-ttu-id="18ac7-1386">Dieser Prozess wird zusätzlich durch die vorhandenen Attributrouten und dedizierten konventionellen Routen erschwert.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1386">This process is further complicated by the existence of attribute routes and dedicated conventional routes.</span></span>

> [!TIP]
> <span data-ttu-id="18ac7-1387">Konventionelle Routen des Controllers wie `{controller}/{action}/{id?}` legen eine Hierarchie mithilfe von Routenparametern fest.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1387">Controller conventional routes such as `{controller}/{action}/{id?}` specify a hierarchy using route parameters.</span></span> <span data-ttu-id="18ac7-1388">Bei [bestimmten konventionellen Routen](xref:mvc/controllers/routing#dcr) und [Attributrouten](xref:mvc/controllers/routing#ar) zu Controllern und Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1388">For [dedicated conventional routes](xref:mvc/controllers/routing#dcr) and [attribute routes](xref:mvc/controllers/routing#ar) to controllers and Razor Pages:</span></span>

<span data-ttu-id="18ac7-1389">Gibt es eine Hierarchie für Routenwerte.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1389">There is a hierarchy of route values.</span></span> <span data-ttu-id="18ac7-1390">Werden diese nicht in der Vorlage angezeigt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1390">They don't appear in the template.</span></span> <span data-ttu-id="18ac7-1391">Für diese Fälle definiert die URL-Generierung das Konzept der **erforderlichen Werte**.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1391">For these cases, URL generation defines the **required values** concept.</span></span>

<span data-ttu-id="18ac7-1392">Bei Endpunkten, die von Controllern und Razor Pages erstellt wurden, sind erforderliche Werte angegeben, die eine Routenwertinvalidierung ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1392">Endpoints created by controllers and Razor Pages have required values specified that allow route value invalidation to work.</span></span> <span data-ttu-id="18ac7-1393">Der Algorithmus der Routenwertinvalidierung im Detail:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1393">The route value invalidation algorithm in detail:</span></span> <span data-ttu-id="18ac7-1394">Die erforderlichen Wertnamen werden mit den Routenparametern kombiniert und dann von links nach rechts verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1394">The required value names are combined with the route parameters, then processed from left-to-right.</span></span>

<span data-ttu-id="18ac7-1395">Für jeden Parameter werden der Umgebungswert und der explizite Wert verglichen:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1395">For each parameter, the ambient value and explicit value are compared:</span></span>

<span data-ttu-id="18ac7-1396">Wenn der Umgebungswert und der explizite Wert gleich sind, wird der Prozess fortgesetzt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1396">If the ambient value and explicit value are the same, the process continues.</span></span> <span data-ttu-id="18ac7-1397">Wenn der Umgebungswert vorhanden ist und der explizite Wert nicht, wird der Umgebungswert bei der URL-Generierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1397">If the ambient value is present and the explicit value isn't, the ambient value is used when generating the URL.</span></span>

### <a name="create-routes"></a><span data-ttu-id="18ac7-1398">Wenn der Umgebungswert nicht vorhanden ist und der explizite Wert vorhanden ist, verwerfen Sie den Umgebungswert und alle nachfolgenden Umgebungswerte.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1398">If the ambient value isn't present and the explicit value is, reject the ambient value and all subsequent ambient values.</span></span>

<span data-ttu-id="18ac7-1399">Wenn der Umgebungswert und der explizite Wert vorhanden und die beiden Werte unterschiedlich sind, verwerfen Sie den Umgebungswert und alle nachfolgenden Umgebungswerte.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1399">If the ambient value and the explicit value are present, and the two values are different, reject the ambient value and all subsequent ambient values.</span></span> <span data-ttu-id="18ac7-1400">An diesem Punkt ist der Vorgang zur URL-Generierung bereit, Routeneinschränkungen auszuwerten.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1400">At this point, the URL generation operation is ready to evaluate route constraints.</span></span> <span data-ttu-id="18ac7-1401">Die akzeptierten Werte werden mit den Standardwerten der Parameter kombiniert, die für Einschränkungen bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1401">The set of accepted values is combined with the parameter default values, which is provided to constraints.</span></span>

<span data-ttu-id="18ac7-1402">Wenn alle Einschränkungen erfüllt sind, wird der Vorgang fortgesetzt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1402">If the constraints all pass, the operation continues.</span></span> <span data-ttu-id="18ac7-1403">Als Nächstes können die **akzeptierten Werte** verwendet werden, um die Routenvorlage zu erweitern.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1403">Next, the **accepted values** can be used to expand the route template.</span></span>

<span data-ttu-id="18ac7-1404">Die Routenvorlage wird verarbeitet:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1404">The route template is processed:</span></span> <span data-ttu-id="18ac7-1405">Von links nach rechts.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1405">From left-to-right.</span></span> <span data-ttu-id="18ac7-1406">Für jeden Parameter wird der akzeptierte Wert ersetzt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1406">Each parameter has its accepted value substituted.</span></span> <span data-ttu-id="18ac7-1407">In den folgenden Sonderfällen:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1407">With the following special cases:</span></span> <span data-ttu-id="18ac7-1408">Wenn bei den akzeptierten Werten ein Wert fehlt und der Parameter einen Standardwert hat, wird der Standardwert verwendet.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1408">If the accepted values is missing a value and the parameter has a default value, the default value is used.</span></span>

<span data-ttu-id="18ac7-1409">Wenn bei den akzeptierten Werten ein Wert fehlt und der Parameter optional ist, wird die Verarbeitung fortgesetzt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1409">If the accepted values is missing a value and the parameter is optional, processing continues.</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="18ac7-1410">Wenn irgendein Routenparameter rechts neben einem fehlenden optionalen Parameter einen Wert hat, schlägt der Vorgang fehl.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1410">If any route parameter to the right of a missing optional parameter has a value, the operation fails.</span></span> <span data-ttu-id="18ac7-1411">Zusammenhängende Parameter mit Standardwerten und optionale Parameter werden, wenn möglich, reduziert dargestellt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1411">Contiguous default-valued parameters and optional parameters are collapsed where possible.</span></span>

<span data-ttu-id="18ac7-1412">Explizit bereitgestellte Werte, für die keine Übereinstimmungen mit einem Routensegment ermittelt werden, werden der Abfragezeichenfolge hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="18ac7-1412">Values explicitly provided that don't match a segment of the route are added to the query string.</span></span> <span data-ttu-id="18ac7-1413">In der folgenden Tabelle werden die Ergebnisse dargestellt, die aus der Verwendung der Routenvorlage `{controller}/{action}/{id?}` hervorgehen:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1413">The following table shows the result when using the route template `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="18ac7-1414">Umgebungswerte</span><span class="sxs-lookup"><span data-stu-id="18ac7-1414">Ambient Values</span></span>

<span data-ttu-id="18ac7-1415">Explizite Werte</span><span class="sxs-lookup"><span data-stu-id="18ac7-1415">Explicit Values</span></span> <span data-ttu-id="18ac7-1416">Ergebnis</span><span class="sxs-lookup"><span data-stu-id="18ac7-1416">Result</span></span> <span data-ttu-id="18ac7-1417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-1418">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1418">'Blazor'</span></span>

<span data-ttu-id="18ac7-1419">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1419">'Identity'</span></span> <span data-ttu-id="18ac7-1420">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1420">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-1421">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1421">'Razor'</span></span>

<span data-ttu-id="18ac7-1422">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1422">'SignalR' uid:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

<span data-ttu-id="18ac7-1423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-1424">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1424">'Blazor'</span></span> <span data-ttu-id="18ac7-1425">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1425">'Identity'</span></span> <span data-ttu-id="18ac7-1426">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1426">'Let's Encrypt'</span></span>

<span data-ttu-id="18ac7-1427">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1427">'Razor'</span></span> <span data-ttu-id="18ac7-1428">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1428">'SignalR' uid:</span></span>

<span data-ttu-id="18ac7-1429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> <span data-ttu-id="18ac7-1430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1430">'Blazor'</span></span> <span data-ttu-id="18ac7-1431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1431">'Identity'</span></span>

<span data-ttu-id="18ac7-1432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1432">'Let's Encrypt'</span></span>

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{*article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

<span data-ttu-id="18ac7-1433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1433">'Razor'</span></span> <span data-ttu-id="18ac7-1434">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1434">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-1435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-1436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1436">'Blazor'</span></span> <span data-ttu-id="18ac7-1437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1437">'Identity'</span></span>

<span data-ttu-id="18ac7-1438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1438">'Let's Encrypt'</span></span>

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

<span data-ttu-id="18ac7-1439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1439">'Razor'</span></span>

!['SignalR' uid:](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a><span data-ttu-id="18ac7-1441">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1441">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="18ac7-1442">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1442">'Blazor'</span></span> <span data-ttu-id="18ac7-1443">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1443">'Identity'</span></span>

> [!TIP]
> <span data-ttu-id="18ac7-1444">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1444">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-1445">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1445">'Razor'</span></span> <span data-ttu-id="18ac7-1446">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1446">'SignalR' uid:</span></span>

<span data-ttu-id="18ac7-1447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="18ac7-1448">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1448">'Blazor'</span></span> <span data-ttu-id="18ac7-1449">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1449">'Identity'</span></span> <span data-ttu-id="18ac7-1450">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1450">'Let's Encrypt'</span></span>

<span data-ttu-id="18ac7-1451">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1451">'Razor'</span></span> <span data-ttu-id="18ac7-1452">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1452">'SignalR' uid:</span></span>

<span data-ttu-id="18ac7-1453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

> [!NOTE]
> <span data-ttu-id="18ac7-1454">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1454">'Blazor'</span></span>

<span data-ttu-id="18ac7-1455">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1455">'Identity'</span></span>

## <a name="use-routing-middleware"></a><span data-ttu-id="18ac7-1456">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1456">'Let's Encrypt'</span></span>

<span data-ttu-id="18ac7-1457">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1457">'Razor'</span></span>

<span data-ttu-id="18ac7-1458">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1458">'SignalR' uid:</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="18ac7-1459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-1460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1460">'Blazor'</span></span>

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <span data-ttu-id="18ac7-1461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1461">'Identity'</span></span>
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

<span data-ttu-id="18ac7-1462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1462">'Let's Encrypt'</span></span>

| <span data-ttu-id="18ac7-1463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1463">'Razor'</span></span>                    | <span data-ttu-id="18ac7-1464">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1464">'SignalR' uid:</span></span>                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | <span data-ttu-id="18ac7-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-1466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1466">'Blazor'</span></span> |
| `/package/track/-3`    | <span data-ttu-id="18ac7-1467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1467">'Identity'</span></span> <span data-ttu-id="18ac7-1468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1468">'Let's Encrypt'</span></span> |
| `/package/track/-3/`   | <span data-ttu-id="18ac7-1469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1469">'Razor'</span></span> <span data-ttu-id="18ac7-1470">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1470">'SignalR' uid:</span></span> |
| `/package/track/`      | <span data-ttu-id="18ac7-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>              |
| `GET /hello/Joe`       | <span data-ttu-id="18ac7-1472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1472">'Blazor'</span></span>                                          |
| `POST /hello/Joe`      | <span data-ttu-id="18ac7-1473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1473">'Identity'</span></span> |
| `GET /hello/Joe/Smith` | <span data-ttu-id="18ac7-1474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1474">'Let's Encrypt'</span></span>              |

<span data-ttu-id="18ac7-1475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1475">'Razor'</span></span> <span data-ttu-id="18ac7-1476">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1476">'SignalR' uid:</span></span>

<span data-ttu-id="18ac7-1477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

<span data-ttu-id="18ac7-1478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1478">'Blazor'</span></span> <span data-ttu-id="18ac7-1479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1479">'Identity'</span></span> <span data-ttu-id="18ac7-1480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1480">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-1481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1481">'Razor'</span></span>

<span data-ttu-id="18ac7-1482">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1482">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

## <a name="route-template-reference"></a><span data-ttu-id="18ac7-1484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1484">'Blazor'</span></span>

<span data-ttu-id="18ac7-1485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1485">'Identity'</span></span> <span data-ttu-id="18ac7-1486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1486">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-1487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1487">'Razor'</span></span> <span data-ttu-id="18ac7-1488">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1488">'SignalR' uid:</span></span>

<span data-ttu-id="18ac7-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-1490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1490">'Blazor'</span></span> <span data-ttu-id="18ac7-1491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1491">'Identity'</span></span>

<span data-ttu-id="18ac7-1492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1492">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-1493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1493">'Razor'</span></span> <span data-ttu-id="18ac7-1494">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1494">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-1496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1496">'Blazor'</span></span>

* `/files/myFile.txt`
* `/files/myFile`

<span data-ttu-id="18ac7-1497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1497">'Identity'</span></span> <span data-ttu-id="18ac7-1498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1498">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-1499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1499">'Razor'</span></span> <span data-ttu-id="18ac7-1500">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1500">'SignalR' uid:</span></span>

<span data-ttu-id="18ac7-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-1502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1502">'Blazor'</span></span> <span data-ttu-id="18ac7-1503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1503">'Identity'</span></span>

<span data-ttu-id="18ac7-1504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1504">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-1505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1505">'Razor'</span></span> <span data-ttu-id="18ac7-1506">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1506">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-1507">----------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1507">----------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-1508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1508">'Blazor'</span></span>

<span data-ttu-id="18ac7-1509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1509">'Identity'</span></span> <span data-ttu-id="18ac7-1510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1510">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-1511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1511">'Razor'</span></span> <span data-ttu-id="18ac7-1512">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1512">'SignalR' uid:</span></span>

<span data-ttu-id="18ac7-1513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-1514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1514">'Blazor'</span></span> <span data-ttu-id="18ac7-1515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1515">'Identity'</span></span>

<span data-ttu-id="18ac7-1516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1516">'Let's Encrypt'</span></span>

| <span data-ttu-id="18ac7-1517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1517">'Razor'</span></span>                           | <span data-ttu-id="18ac7-1518">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1518">'SignalR' uid:</span></span>    | <span data-ttu-id="18ac7-1519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | <span data-ttu-id="18ac7-1520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1520">'Blazor'</span></span>                                     |
| `{Page=Home}`                            | `/`                     | <span data-ttu-id="18ac7-1521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1521">'Identity'</span></span>                                         |
| `{Page=Home}`                            | `/Contact`              | <span data-ttu-id="18ac7-1522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1522">'Let's Encrypt'</span></span>                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | <span data-ttu-id="18ac7-1523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1523">'Razor'</span></span>                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | <span data-ttu-id="18ac7-1524">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1524">'SignalR' uid:</span></span> |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | <span data-ttu-id="18ac7-1525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>        |

<span data-ttu-id="18ac7-1526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1526">'Blazor'</span></span> <span data-ttu-id="18ac7-1527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1527">'Identity'</span></span>

> [!TIP]
> <span data-ttu-id="18ac7-1528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1528">'Let's Encrypt'</span></span>

## <a name="route-constraint-reference"></a><span data-ttu-id="18ac7-1529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1529">'Razor'</span></span>

<span data-ttu-id="18ac7-1530">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1530">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-1531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-1532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1532">'Blazor'</span></span> <span data-ttu-id="18ac7-1533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1533">'Identity'</span></span> <span data-ttu-id="18ac7-1534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1534">'Let's Encrypt'</span></span>

> [!WARNING]
> <span data-ttu-id="18ac7-1535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1535">'Razor'</span></span> <span data-ttu-id="18ac7-1536">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1536">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-1537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="18ac7-1538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1538">'Blazor'</span></span>

| <span data-ttu-id="18ac7-1539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1539">'Identity'</span></span> | <span data-ttu-id="18ac7-1540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1540">'Let's Encrypt'</span></span> | <span data-ttu-id="18ac7-1541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1541">'Razor'</span></span> | <span data-ttu-id="18ac7-1542">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1542">'SignalR' uid:</span></span> |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | <span data-ttu-id="18ac7-1543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> | <span data-ttu-id="18ac7-1544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1544">'Blazor'</span></span> |
| `bool` | `{active:bool}` | <span data-ttu-id="18ac7-1545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1545">'Identity'</span></span> | <span data-ttu-id="18ac7-1546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1546">'Let's Encrypt'</span></span> |
| `datetime` | `{dob:datetime}` | <span data-ttu-id="18ac7-1547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1547">'Razor'</span></span> | <span data-ttu-id="18ac7-1548">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1548">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-1549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>|
| `decimal` | `{price:decimal}` | <span data-ttu-id="18ac7-1550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1550">'Blazor'</span></span> | <span data-ttu-id="18ac7-1551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1551">'Identity'</span></span> <span data-ttu-id="18ac7-1552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1552">'Let's Encrypt'</span></span>|
| `double` | `{weight:double}` | <span data-ttu-id="18ac7-1553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1553">'Razor'</span></span> | <span data-ttu-id="18ac7-1554">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1554">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-1555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>|
| `float` | `{weight:float}` | <span data-ttu-id="18ac7-1556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1556">'Blazor'</span></span> | <span data-ttu-id="18ac7-1557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1557">'Identity'</span></span> <span data-ttu-id="18ac7-1558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1558">'Let's Encrypt'</span></span>|
| `guid` | `{id:guid}` | <span data-ttu-id="18ac7-1559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1559">'Razor'</span></span> | <span data-ttu-id="18ac7-1560">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1560">'SignalR' uid:</span></span> |
| `long` | `{ticks:long}` | <span data-ttu-id="18ac7-1561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> | <span data-ttu-id="18ac7-1562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1562">'Blazor'</span></span> |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | <span data-ttu-id="18ac7-1563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1563">'Identity'</span></span> |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | <span data-ttu-id="18ac7-1564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1564">'Let's Encrypt'</span></span> |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | <span data-ttu-id="18ac7-1565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1565">'Razor'</span></span> |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | <span data-ttu-id="18ac7-1566">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1566">'SignalR' uid:</span></span> |
| `min(value)` | `{age:min(18)}` | `19` | <span data-ttu-id="18ac7-1567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> |
| `max(value)` | `{age:max(120)}` | `91` | <span data-ttu-id="18ac7-1568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1568">'Blazor'</span></span> |
| `range(min,max)` | `{age:range(18,120)}` | `91` | <span data-ttu-id="18ac7-1569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1569">'Identity'</span></span> |
| `alpha` | `{name:alpha}` | `Rick` | <span data-ttu-id="18ac7-1570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1570">'Let's Encrypt'</span></span> |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | <span data-ttu-id="18ac7-1571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1571">'Razor'</span></span> |
| `required` | `{name:required}` | `Rick` | <span data-ttu-id="18ac7-1572">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1572">'SignalR' uid:</span></span> |

<span data-ttu-id="18ac7-1573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-1574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1574">'Blazor'</span></span>

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> <span data-ttu-id="18ac7-1575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1575">'Identity'</span></span> <span data-ttu-id="18ac7-1576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1576">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-1577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1577">'Razor'</span></span> <span data-ttu-id="18ac7-1578">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1578">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-1579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

## <a name="regular-expressions"></a><span data-ttu-id="18ac7-1580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1580">'Blazor'</span></span>

<span data-ttu-id="18ac7-1581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1581">'Identity'</span></span> <span data-ttu-id="18ac7-1582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1582">'Let's Encrypt'</span></span>

<span data-ttu-id="18ac7-1583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1583">'Razor'</span></span> <span data-ttu-id="18ac7-1584">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1584">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-1585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-1586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1586">'Blazor'</span></span> <span data-ttu-id="18ac7-1587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1587">'Identity'</span></span>

| <span data-ttu-id="18ac7-1588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1588">'Let's Encrypt'</span></span>    | <span data-ttu-id="18ac7-1589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1589">'Razor'</span></span>     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

<span data-ttu-id="18ac7-1590">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1590">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-1591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-1592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1592">'Blazor'</span></span> <span data-ttu-id="18ac7-1593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1593">'Identity'</span></span> <span data-ttu-id="18ac7-1594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1594">'Let's Encrypt'</span></span>

| <span data-ttu-id="18ac7-1595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1595">'Razor'</span></span>   | <span data-ttu-id="18ac7-1596">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1596">'SignalR' uid:</span></span>    | <span data-ttu-id="18ac7-1597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> | <span data-ttu-id="18ac7-1598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1598">'Blazor'</span></span>               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | <span data-ttu-id="18ac7-1599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1599">'Identity'</span></span>     | <span data-ttu-id="18ac7-1600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1600">'Let's Encrypt'</span></span>   | <span data-ttu-id="18ac7-1601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1601">'Razor'</span></span>     |
| `[a-z]{2}`   | <span data-ttu-id="18ac7-1602">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1602">'SignalR' uid:</span></span> | <span data-ttu-id="18ac7-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>   | <span data-ttu-id="18ac7-1604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1604">'Blazor'</span></span>     |
| `[a-z]{2}`   | <span data-ttu-id="18ac7-1605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1605">'Identity'</span></span>        | <span data-ttu-id="18ac7-1606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1606">'Let's Encrypt'</span></span>   | <span data-ttu-id="18ac7-1607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1607">'Razor'</span></span>    |
| `[a-z]{2}`   | <span data-ttu-id="18ac7-1608">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1608">'SignalR' uid:</span></span>        | <span data-ttu-id="18ac7-1609">------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1609">------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>   | <span data-ttu-id="18ac7-1610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1610">'Blazor'</span></span>    |
| `^[a-z]{2}$` | <span data-ttu-id="18ac7-1611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1611">'Identity'</span></span>     | <span data-ttu-id="18ac7-1612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1612">'Let's Encrypt'</span></span>    | <span data-ttu-id="18ac7-1613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1613">'Razor'</span></span> |
| `^[a-z]{2}$` | <span data-ttu-id="18ac7-1614">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1614">'SignalR' uid:</span></span> | <span data-ttu-id="18ac7-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>    | <span data-ttu-id="18ac7-1616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1616">'Blazor'</span></span> |

<span data-ttu-id="18ac7-1617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1617">'Identity'</span></span>

<span data-ttu-id="18ac7-1618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1618">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-1619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1619">'Razor'</span></span> <span data-ttu-id="18ac7-1620">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1620">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

## <a name="custom-route-constraints"></a><span data-ttu-id="18ac7-1622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1622">'Blazor'</span></span>

<span data-ttu-id="18ac7-1623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1623">'Identity'</span></span> <span data-ttu-id="18ac7-1624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1624">'Let's Encrypt'</span></span>

<span data-ttu-id="18ac7-1625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1625">'Razor'</span></span> <span data-ttu-id="18ac7-1626">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1626">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-1628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1628">'Blazor'</span></span>

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

<span data-ttu-id="18ac7-1629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1629">'Identity'</span></span> <span data-ttu-id="18ac7-1630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1630">'Let's Encrypt'</span></span>

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="url-generation-reference"></a><span data-ttu-id="18ac7-1631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1631">'Razor'</span></span>

<span data-ttu-id="18ac7-1632">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1632">'SignalR' uid:</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

<span data-ttu-id="18ac7-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="18ac7-1634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1634">'Blazor'</span></span> <span data-ttu-id="18ac7-1635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1635">'Identity'</span></span>

<span data-ttu-id="18ac7-1636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1636">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-1637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1637">'Razor'</span></span> <span data-ttu-id="18ac7-1638">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1638">'SignalR' uid:</span></span> <span data-ttu-id="18ac7-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="18ac7-1640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1640">'Blazor'</span></span> <span data-ttu-id="18ac7-1641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1641">'Identity'</span></span> <span data-ttu-id="18ac7-1642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1642">'Let's Encrypt'</span></span>

<span data-ttu-id="18ac7-1643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1643">'Razor'</span></span> <span data-ttu-id="18ac7-1644">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1644">'SignalR' uid:</span></span>

| <span data-ttu-id="18ac7-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>                     | <span data-ttu-id="18ac7-1646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1646">'Blazor'</span></span>                        | <span data-ttu-id="18ac7-1647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1647">'Identity'</span></span>                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| <span data-ttu-id="18ac7-1648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1648">'Let's Encrypt'</span></span>                | <span data-ttu-id="18ac7-1649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1649">'Razor'</span></span>                       | `/Home/About`           |
| <span data-ttu-id="18ac7-1650">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1650">'SignalR' uid:</span></span>                | <span data-ttu-id="18ac7-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> | `/Order/About`          |
| <span data-ttu-id="18ac7-1652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1652">'Blazor'</span></span> | <span data-ttu-id="18ac7-1653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1653">'Identity'</span></span>                       | `/Home/About`           |
| <span data-ttu-id="18ac7-1654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1654">'Let's Encrypt'</span></span>                | <span data-ttu-id="18ac7-1655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1655">'Razor'</span></span>        | `/Home/About?color=Red` |

<span data-ttu-id="18ac7-1656">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1656">'SignalR' uid:</span></span>

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

<span data-ttu-id="18ac7-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18ac7-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

## <a name="complex-segments"></a><span data-ttu-id="18ac7-1658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1658">'Blazor'</span></span>

<span data-ttu-id="18ac7-1659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1659">'Identity'</span></span> <span data-ttu-id="18ac7-1660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1660">'Let's Encrypt'</span></span> <span data-ttu-id="18ac7-1661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18ac7-1661">'Razor'</span></span>

::: moniker-end

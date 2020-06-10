---
title: 'title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:'
author: rick-anderson
description: "'Blazor'"
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/16/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/url-rewriting
ms.openlocfilehash: e43cd5055737feaef451d27b651c1d301c1f93d2
ms.sourcegitcommit: 58722eb309767e462bdbf3082bd38737a4ef168f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84105947"
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a><span data-ttu-id="d6f9f-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-103">'Identity'</span></span>

<span data-ttu-id="d6f9f-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-104">'Let's Encrypt'</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d6f9f-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-105">'Razor'</span></span>

<span data-ttu-id="d6f9f-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-106">'SignalR' uid:</span></span> <span data-ttu-id="d6f9f-107">URL-umschreibende Middleware in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d6f9f-107">URL Rewriting Middleware in ASP.NET Core</span></span> <span data-ttu-id="d6f9f-108">Von [Mikael Mengistu](https://github.com/mikaelm12)</span><span class="sxs-lookup"><span data-stu-id="d6f9f-108">By [Mikael Mengistu](https://github.com/mikaelm12)</span></span>

* <span data-ttu-id="d6f9f-109">In diesem Artikel wird das Umschreiben von URLs beschrieben. Außerdem erhalten Sie Anweisungen zur Verwendung der URL-umschreibenden Middleware in ASP.NET Core-Apps.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-109">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>
* <span data-ttu-id="d6f9f-110">Bei der URL-Umschreibung werden die Anforderungs-URLs verändert, die auf mindesten einer vordefinierten Regel basieren.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-110">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span>
* <span data-ttu-id="d6f9f-111">Es wird eine Abstraktion zwischen den Speicherorten und Adressen von Ressourcen erstellt, sodass diese nicht eng miteinander verknüpft sind.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-111">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span>
* <span data-ttu-id="d6f9f-112">Die URL-Neuschreibung ist hilfreich in verschiedenen Szenarios wie:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-112">URL rewriting is valuable in several scenarios to:</span></span>
* <span data-ttu-id="d6f9f-113">Kurzzeitiges oder permanentes Verschieben oder Ersetzen von Serverressourcen, sowie Erhalten von stabilen Locators für diese Ressourcen.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-113">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="d6f9f-114">Verteilen der Verarbeitung von Anforderungen auf verschiedene Apps oder Bereiche einer App.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-114">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="d6f9f-115">Entfernen, Hinzufügen oder Umorganisieren von URL-Segmenten bei eingehenden Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-115">Remove, add, or reorganize URL segments on incoming requests.</span></span>

> [!NOTE]
> <span data-ttu-id="d6f9f-116">Optimieren von öffentlichen URLs für die Suchmaschinenoptimierung (Search Engine Optimization, SEO).</span><span class="sxs-lookup"><span data-stu-id="d6f9f-116">Optimize public URLs for Search Engine Optimization (SEO).</span></span> <span data-ttu-id="d6f9f-117">Gestatten der Verwendung von angezeigten öffentlichen URLs, um Besuchern zu helfen, den Inhalt vorherzusagen, der durch die Anforderung einer Ressource zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-117">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>

<span data-ttu-id="d6f9f-118">Umleiten von unsicheren Anforderungen auf sichere Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-118">Redirect insecure requests to secure endpoints.</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="d6f9f-119">Verhindern von Hotlinks zu externen Websites, die eine gehostete statische Ressource auf einer anderen Website verwenden, bei der die Ressource zu ihrem eigenen Inhalt verlinkt wird.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-119">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

<span data-ttu-id="d6f9f-120">Wenn Sie URLs umschreiben, kann das negative Auswirkungen auf die Leistung einer App haben.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-120">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="d6f9f-121">Wenn möglich, sollten Sie so wenig Regeln wie möglich erstellen und darauf achten, dass diese nicht zu kompliziert sind.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-121">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="d6f9f-122">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d6f9f-122">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span> <span data-ttu-id="d6f9f-123">Umleiten und Umschreiben von URLs</span><span class="sxs-lookup"><span data-stu-id="d6f9f-123">URL redirect and URL rewrite</span></span> <span data-ttu-id="d6f9f-124">Der Unterschied zwischen dem *Umleiten* und *Neu schreiben* von URLs ist zwar gering, allerdings haben die beiden Verfahren sehr unterschiedliche Auswirkungen auf die Bereitstellung von Ressourcen für Clients.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-124">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span>

<span data-ttu-id="d6f9f-125">Die URL-umschreibenden Middleware von ASP.NET Core kann für beide Vorgänge verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-125">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

![Bei der *Umleitung von URLs* findet ein clientseitiger Vorgang statt, bei dem der Client angewiesen wird, auf eine Ressource unter einer anderen Adresse zuzugreifen, als die, die der Client ursprünglich angefordert hat.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="d6f9f-131">Der Client sendet eine Anforderung an den Dienst unter dem Pfad für Version 1: „/v1/api“.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-131">A client makes a request to the service at the version 1 path /v1/api.</span></span>

* <span data-ttu-id="d6f9f-132">Der Server sendet eine „302 – Gefunden“-Antwort mit dem neuen, temporären Pfad für Version 2: „/v2/api“.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-132">The server sends back a 302 (Found) response with the new, temporary path for the service at version 2 /v2/api.</span></span> <span data-ttu-id="d6f9f-133">Der Client sendet eine zweite Anforderung an den Dienst unter der Umleitungs-URL.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-133">The client makes a second request to the service at the redirect URL.</span></span>

* <span data-ttu-id="d6f9f-134">Der Server gibt den Statuscode „200 – OK“ zurück.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-134">The server responds with a 200 (OK) status code.</span></span> <span data-ttu-id="d6f9f-135">Wenn Anforderungen auf eine andere URL umgeleitet werden, muss angegeben werden, ob die Umleitung temporär oder permanent sein soll. Geben Sie hierzu den Statuscode mit der Antwort an:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-135">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

<span data-ttu-id="d6f9f-136">Der Statuscode *301 – Permanent verschoben* wird verwendet, wenn der Ressource eine neue permanente URL zugewiesen wurde, und Sie dem Client die Anweisung geben möchten, dass alle zukünftigen Anforderungen an die Ressource die neue URL verwenden sollen.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-136">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span>

<span data-ttu-id="d6f9f-137">*Der Client kann die Antwort zwischenspeichern und wiederverwenden, wenn der Statuscode 301 empfangen wird.*</span><span class="sxs-lookup"><span data-stu-id="d6f9f-137">*The client may cache and reuse the response when a 301 status code is received.*</span></span> <span data-ttu-id="d6f9f-138">Der Statuscode *302 – Gefunden* wird verwendet, wenn die Umleitung temporär ist oder sowieso Änderungen vorbehalten sind.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-138">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="d6f9f-139">Der Statuscode 302 teilt dem Client mit, dass die URL nicht gespeichert und nicht wiederverwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-139">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="d6f9f-140">Weitere Informationen zu Statuscodes finden Sie unter [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="d6f9f-140">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="d6f9f-141">Bei der *Neuschreibung einer URL* handelt es sich um einen serverseitigen Vorgang, bei dem eine Ressource von einer anderen Ressourcenadresse, als der vom Client angeforderten, bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-141">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span>

![Wenn eine URL neu geschrieben wird, ist kein Roundtrip zum Server erforderlich.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="d6f9f-146">Ein Web-API-Dienstendpunkt wurde auf dem Server von Version 1 (v1) auf Version 2 (v2) geändert.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-146">A WebAPI service endpoint has been changed from version 1 (v1) to version 2 (v2) on the server.</span></span>

<span data-ttu-id="d6f9f-147">Der Client sendet eine Anforderung an den Dienst unter dem Pfad für Version 1: „/v1/api“.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-147">A client makes a request to the service at the version 1 path /v1/api.</span></span> <span data-ttu-id="d6f9f-148">Die Anforderungs-URL wird umgeschrieben, damit über den Pfad für Version 2 („/v2/api“) auf den Dienst zugegriffen werden kann.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-148">The request URL is rewritten to access the service at the version 2 path /v2/api.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="d6f9f-149">Der Dienst sendet eine Antwort an den Client mit dem Statuscode „200 – OK“.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-149">The service responds to the client with a 200 (OK) status code.</span></span>

<span data-ttu-id="d6f9f-150">URL-umschreibende Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="d6f9f-150">URL rewriting sample app</span></span>

* <span data-ttu-id="d6f9f-151">Mit der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) können Sie die Features der Middleware zur URL-Neuschreibung testen.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-151">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span>
* <span data-ttu-id="d6f9f-152">Die App wendet Umleitungs- und Neuschreibungsregeln an und zeigt die umgeleitete oder neu geschriebene URL für verschiedene Szenarios an.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-152">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>
* <span data-ttu-id="d6f9f-153">Empfohlene Verwendung der URL-umschreibenden Middleware</span><span class="sxs-lookup"><span data-stu-id="d6f9f-153">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="d6f9f-154">Sie können Middleware zur URL-Neuschreibung verwenden, wenn die folgenden Ansätze nicht möglich sind:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-154">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

[<span data-ttu-id="d6f9f-155">URL Rewrite module with IIS on Windows Server (URL-Neuschreibungsmodul mit IIS auf Windows-Server)</span><span class="sxs-lookup"><span data-stu-id="d6f9f-155">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)

* [<span data-ttu-id="d6f9f-156">Apache mod_rewrite module on Apache Server (Apache mod_rewrite-Modul auf Apache-Server)</span><span class="sxs-lookup"><span data-stu-id="d6f9f-156">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)

  [<span data-ttu-id="d6f9f-157">URL rewriting on Nginx (URL-Neuschreibung auf Nginx)</span><span class="sxs-lookup"><span data-stu-id="d6f9f-157">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/) <span data-ttu-id="d6f9f-158">Sie können die Middleware auch verwenden, wenn die App auf dem [HTTP.sys-Server](xref:fundamentals/servers/httpsys) (früher als WebListener bezeichnet) gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-158">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>
* <span data-ttu-id="d6f9f-159">Die Hauptgründe für die Verwendung der serverbasierten Technologien zur URL-Neuschreibung in IIS, Apache und Nginx sind:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-159">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

  <span data-ttu-id="d6f9f-160">Die Middleware unterstützt nicht alle Features dieser Module.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-160">The middleware doesn't support the full features of these modules.</span></span>

## <a name="package"></a><span data-ttu-id="d6f9f-161">Einige Features der Servermodule funktionieren nicht mit ASP.NET Core-Projekten – z.B. die Einschränkungen `IsFile` und `IsDirectory` des IIS-Neuschreibungsmoduls.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-161">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span>

<span data-ttu-id="d6f9f-162">Verwenden Sie in diesem Szenario stattdessen die Middleware.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-162">In these scenarios, use the middleware instead.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="d6f9f-163">Die Leistung der Middleware stimmt wahrscheinlich nicht mit der Leistung der Module überein.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-163">The performance of the middleware probably doesn't match that of the modules.</span></span>

<span data-ttu-id="d6f9f-164">Benchmarking ist der einzige Weg, um sicher festzustellen, welcher Ansatz die Leistung am meisten beeinträchtigt oder ob die nachlassende Leistung nur geringfügig ist.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-164">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span> <span data-ttu-id="d6f9f-165">Package</span><span class="sxs-lookup"><span data-stu-id="d6f9f-165">Package</span></span> <span data-ttu-id="d6f9f-166">Die URL-umschreibende Middleware wird über das [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite)-Paket bereitgestellt, das implizit in ASP.NET Core-Apps enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-166">URL Rewriting Middleware is provided by the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package, which is implicitly included in ASP.NET Core apps.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="d6f9f-167">Erweiterung und Optionen</span><span class="sxs-lookup"><span data-stu-id="d6f9f-167">Extension and options</span></span>

<span data-ttu-id="d6f9f-168">Legen Sie URL-Neuschreibungs- und -Umleitungsregeln fest, indem Sie eine Instanz der [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions)-Klasse erstellen und Erweiterungsmethoden für jede Neuschreibungsregel hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-168">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span>

* <span data-ttu-id="d6f9f-169">Verketten Sie mehrere Regeln in der Reihenfolge miteinander, in der sie verarbeitet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-169">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="d6f9f-170">Die `RewriteOptions` werden an die Middleware zur URL-Neuschreibung übergeben, wenn diese mit <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> zu der Anforderungspipeline hinzugefügt wird:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-170">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

* <span data-ttu-id="d6f9f-171">Umleitung einer Nicht-WWW-Anforderung an eine WWW-Anforderung</span><span class="sxs-lookup"><span data-stu-id="d6f9f-171">Redirect non-www to www</span></span> <span data-ttu-id="d6f9f-172">Drei Optionen ermöglichen der App, Nicht-`www`-Anforderungen an `www` umzuleiten:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-172">Three options permit the app to redirect non-`www` requests to `www`:</span></span> <span data-ttu-id="d6f9f-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Die Anforderung wird dauerhaft an die Unterdomäne `www` umgeleitet, sofern die Anforderung nicht `www` ist.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="d6f9f-174">Wird mit dem Statuscode [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-174">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="d6f9f-175"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Die Anforderung wird an die Unterdomäne `www` umgeleitet, sofern die eingehende Anforderung nicht `www` ist.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-175"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span>

<span data-ttu-id="d6f9f-176">Wird mit dem Statuscode [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-176">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="d6f9f-177">Eine Überladung ermöglicht es Ihnen, den Statuscode für die Antwort zu senden.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-177">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="d6f9f-178">Verwenden Sie ein Feld der <xref:Microsoft.AspNetCore.Http.StatusCodes>-Klasse, um einen Statuscode zuzuweisen.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-178">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span> <span data-ttu-id="d6f9f-179">Umleitungs-URL</span><span class="sxs-lookup"><span data-stu-id="d6f9f-179">URL redirect</span></span> <span data-ttu-id="d6f9f-180">Verwenden Sie <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*>, um Anforderungen umzuleiten.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-180">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="d6f9f-181">Der erste Parameter enthält Ihren RegEx, damit dieser dem Pfad der eingehenden URL zugeordnet werden kann.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-181">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="d6f9f-182">Beim zweiten Parameter handelt es sich um eine Ersatzzeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-182">The second parameter is the replacement string.</span></span> <span data-ttu-id="d6f9f-183">Der dritte Parameter gibt, falls vorhanden, den Statuscode an.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-183">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="d6f9f-184">Wenn Sie den Statuscode nicht angeben, wird standardmäßig *302 – Gefunden* zurückgegeben, was bedeutet, dass die Ressource kurzzeitig verschoben oder ersetzt wurde.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-184">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span> <span data-ttu-id="d6f9f-185">Aktivieren Sie in Ihrem Browser die Entwicklertools, und senden Sie eine Anforderung an die Beispiel-App mit dem Pfad `/redirect-rule/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-185">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span>

* <span data-ttu-id="d6f9f-186">Der RegEx stimmt mit dem Anforderungspfad unter `redirect-rule/(.*)` überein, und der Pfad wird durch `/redirected/1234/5678` ersetzt.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-186">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span>
* <span data-ttu-id="d6f9f-187">Die Umleitungs-URL wird mit dem Statuscode *302 – Gefunden* an den Client zurückgesendet.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-187">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span>

<span data-ttu-id="d6f9f-188">Unter der Umleitungs-URL sendet der Browser eine neue Anforderung, die in dessen Adressleiste angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-188">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span>

> [!WARNING]
> <span data-ttu-id="d6f9f-189">Da keine Regel in der Beispiel-App mit der Umleitungs-URL übereinstimmt:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-189">Since no rules in the sample app match on the redirect URL:</span></span> <span data-ttu-id="d6f9f-190">Die zweite Anforderung erhält die Antwort *200 – OK* von der App.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-190">The second request receives a *200 - OK* response from the app.</span></span> <span data-ttu-id="d6f9f-191">Der Antworttext zeigt die Umleitungs-URL an.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-191">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="d6f9f-192">Wenn eine URL *weitergeleitet* wird, wird ein Roundtrip zum Server ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-192">A round trip is made to the server when a URL is *redirected*.</span></span>

![Seien Sie vorsichtig, wenn Sie Umleitungsregeln einrichten.](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="d6f9f-194">Bei jeder Anforderung an die App werden Umleitungsregeln überprüft – auch nach einer Umleitung.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-194">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="d6f9f-195">Dabei kann schnell aus Versehen eine *Dauerschleife von Umleitungen* entstehen.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-195">It's easy to accidentally create a *loop of infinite redirects*.</span></span> <span data-ttu-id="d6f9f-196">Ursprüngliche Anforderung: `/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="d6f9f-196">Original Request: `/redirect-rule/1234/5678`</span></span> <span data-ttu-id="d6f9f-197">Browserfenster mit Entwicklertools, die die Anforderungen und Antworten nachverfolgen</span><span class="sxs-lookup"><span data-stu-id="d6f9f-197">Browser window with Developer Tools tracking the requests and responses</span></span> <span data-ttu-id="d6f9f-198">Der Teil des Ausdruck in Klammern wird als *Erfassungsgruppe* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-198">The part of the expression contained within parentheses is called a *capture group*.</span></span>

<span data-ttu-id="d6f9f-199">Der Punkt (`.`) im Ausdruck steht für *Übereinstimmung mit beliebigem Zeichen*.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-199">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="d6f9f-200">Das Sternchen (`*`) steht für *Übereinstimmung mit dem vorausgehenden Zeichen (keinmal oder mindestens einmal)* .</span><span class="sxs-lookup"><span data-stu-id="d6f9f-200">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="d6f9f-201">Daher werden die letzten beiden Pfadsegmente der URL (`1234/5678`) von der Erfassungsgruppe erfasst `(.*)`.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-201">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="d6f9f-202">Alle Werte, die Sie in der Anforderungs-URL nach `redirect-rule/` angeben, werden von dieser Erfassungsgruppe erfasst.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-202">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="d6f9f-203">Erfassungsgruppen werden in der Ersetzungszeichenfolge mit dem Dollarzeichen (`$`) in die Zeichenfolge eingefügt. Danach folgt die Sequenznummer der Erfassung.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-203">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span>

<span data-ttu-id="d6f9f-204">Der erste Wert der Erfassungsgruppe wird mit `$1` abgerufen, der zweite mit `$2`. Dies wird in Sequenzen für die Erfassungsgruppen Ihres RegEx weitergeführt.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-204">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="d6f9f-205">Nur eine Erfassungsgruppe ist in der Beispiel-App im RegEx der Umleitungsregel enthalten. Das bedeutet, dass es in die Ersetzungszeichenfolge nur eine Gruppe eingefügt wird, nämlich `$1`.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-205">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="d6f9f-206">Wenn die Regel angewendet wird, ändert sich die URL in `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-206">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

* <span data-ttu-id="d6f9f-207">URL-Umleitung an einen sicheren Endpunkt</span><span class="sxs-lookup"><span data-stu-id="d6f9f-207">URL redirect to a secure endpoint</span></span>
* <span data-ttu-id="d6f9f-208">Verwenden Sie <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*>, um HTTP-Anforderungen auf denselben Host und Pfad mithilfe des HTTPS-Protokolls umzuleiten.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-208">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span>

<span data-ttu-id="d6f9f-209">Wenn kein Statuscode angegeben wird, wird für die Middleware standardmäßig *302 – Gefunden* zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-209">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="d6f9f-210">Wenn kein Port angegeben ist:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-210">If the port isn't supplied:</span></span> <span data-ttu-id="d6f9f-211">Für die Middleware wird standardmäßig `null` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-211">The middleware defaults to `null`.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="d6f9f-212">Das Schema ändert sich in `https` (HTTPS-Protokoll), und der Client hat über Port 443 Zugriff auf die Ressource.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-212">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span> <span data-ttu-id="d6f9f-213">Im folgenden Beispiel wird dargestellt, wie Sie den Statuscode *301 – Permanent verschoben* festlegen und den Port in 5001 ändern.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-213">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

<span data-ttu-id="d6f9f-214">Verwenden Sie <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*>, um unsichere Anforderungen auf denselben Host und Pfad mit einem sicheren HTTPS-Protokoll auf Port 443 umzuleiten.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-214">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="d6f9f-215">Die Middleware legt den Statuscode auf *301 – Permanent verschoben* fest.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-215">The middleware sets the status code to *301 - Moved Permanently*.</span></span> <span data-ttu-id="d6f9f-216">Wenn Sie eine Umleitung an einen sicheren Endpunkt ohne weitere erforderliche Umleitungsregeln durchführen, wird empfohlen, Middleware für HTTPS-Umleitung zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-216">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="d6f9f-217">Weitere Informationen finden Sie im Artikel zum [Erzwingen von HTTPS](xref:security/enforcing-ssl#require-https).</span><span class="sxs-lookup"><span data-stu-id="d6f9f-217">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="d6f9f-218">Anhand der Beispiel-App wird veranschaulicht, wie `AddRedirectToHttps` oder `AddRedirectToHttpsPermanent` verwendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-218">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span>

![Fügen Sie die Erweiterungsmethode zu den `RewriteOptions` hinzu.](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="d6f9f-220">Senden Sie eine unsichere Anforderung an die App unter einer beliebigen URL.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-220">Make an insecure request to the app at any URL.</span></span>

![Schließen Sie die Sicherheitswarnung des Browsers, in der Sie darüber informiert werden, dass das selbstsignierte Zertifikat nicht vertrauenswürdig ist, oder erstellen sie eine Ausnahme, um dem Zertifikat zu vertrauen.](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="d6f9f-222">Ursprüngliche Anforderung über `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="d6f9f-222">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

<span data-ttu-id="d6f9f-223">Browserfenster mit Entwicklertools, die die Anforderungen und Antworten nachverfolgen</span><span class="sxs-lookup"><span data-stu-id="d6f9f-223">Browser window with Developer Tools tracking the requests and responses</span></span> <span data-ttu-id="d6f9f-224">Ursprüngliche Anforderung über `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="d6f9f-224">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span> <span data-ttu-id="d6f9f-225">Browserfenster mit Entwicklertools, die die Anforderungen und Antworten nachverfolgen</span><span class="sxs-lookup"><span data-stu-id="d6f9f-225">Browser window with Developer Tools tracking the requests and responses</span></span> <span data-ttu-id="d6f9f-226">Umschreiben einer URL</span><span class="sxs-lookup"><span data-stu-id="d6f9f-226">URL rewrite</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="d6f9f-227">Erstellen Sie mithilfe von <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> eine Regel zum Umschreiben von URLs.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-227">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span>

![Der erste Parameter enthält den RegEx, damit dieser der eingehenden URL zugeordnet werden kann.](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="d6f9f-229">Beim zweiten Parameter handelt es sich um eine Ersatzzeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-229">The second parameter is the replacement string.</span></span>

<span data-ttu-id="d6f9f-230">Der dritte Parameter (`skipRemainingRules: {true|false}`) teilt der Middleware mit, ob sie zusätzliche Umschreibungsregeln überspringen soll, wenn die aktuelle Regel angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-230">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span> <span data-ttu-id="d6f9f-231">Ursprüngliche Anforderung: `/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="d6f9f-231">Original Request: `/rewrite-rule/1234/5678`</span></span>

| <span data-ttu-id="d6f9f-232">Browserfenster mit Entwicklertools, die die Anforderung und Antwort nachverfolgen</span><span class="sxs-lookup"><span data-stu-id="d6f9f-232">Browser window with Developer Tools tracking the request and response</span></span>                               | <span data-ttu-id="d6f9f-233">Das Zirkumflexzeichen (`^`) am Anfang des Ausdrucks bedeutet, dass die Übereinstimmung schon am Anfang des URL-Pfads beginnt.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-233">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="d6f9f-234">In dem vorherigen Beispiel zu den Umleitungsregeln, `redirect-rule/(.*)`, beginnt der RegEx nicht mit einem Zirkumflexzeichen (`^`).</span><span class="sxs-lookup"><span data-stu-id="d6f9f-234">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="d6f9f-235">So kann für eine Übereinstimmung ein beliebiges Zeichen im Pfad vor `redirect-rule/` stehen.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-235">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="d6f9f-236">Pfad</span><span class="sxs-lookup"><span data-stu-id="d6f9f-236">Path</span></span>   |

<span data-ttu-id="d6f9f-237">Match</span><span class="sxs-lookup"><span data-stu-id="d6f9f-237">Match</span></span> <span data-ttu-id="d6f9f-238">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-238">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

| <span data-ttu-id="d6f9f-239">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-239">'Blazor'</span></span>                              | <span data-ttu-id="d6f9f-240">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-240">'Identity'</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="d6f9f-241">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-241">'Let's Encrypt'</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="d6f9f-242">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-242">'Razor'</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="d6f9f-243">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-243">'SignalR' uid:</span></span>    |

<span data-ttu-id="d6f9f-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="d6f9f-245">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-245">'Blazor'</span></span> <span data-ttu-id="d6f9f-246">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-246">'Identity'</span></span> <span data-ttu-id="d6f9f-247">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-247">'Let's Encrypt'</span></span> <span data-ttu-id="d6f9f-248">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-248">'Razor'</span></span> <span data-ttu-id="d6f9f-249">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-249">'SignalR' uid:</span></span> <span data-ttu-id="d6f9f-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="d6f9f-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-251">'Blazor'</span></span>

<span data-ttu-id="d6f9f-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-252">'Identity'</span></span> <span data-ttu-id="d6f9f-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-253">'Let's Encrypt'</span></span> <span data-ttu-id="d6f9f-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-254">'Razor'</span></span> <span data-ttu-id="d6f9f-255">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-255">'SignalR' uid:</span></span>

> [!NOTE]
> <span data-ttu-id="d6f9f-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="d6f9f-257">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-257">'Blazor'</span></span>
>
> * <span data-ttu-id="d6f9f-258">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-258">'Identity'</span></span>
> * <span data-ttu-id="d6f9f-259">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-259">'Let's Encrypt'</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="d6f9f-260">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-260">'Razor'</span></span>

<span data-ttu-id="d6f9f-261">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-261">'SignalR' uid:</span></span> <span data-ttu-id="d6f9f-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="d6f9f-263">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-263">'Blazor'</span></span>

<span data-ttu-id="d6f9f-264">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-264">'Identity'</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="d6f9f-265">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-265">'Let's Encrypt'</span></span> <span data-ttu-id="d6f9f-266">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-266">'Razor'</span></span>

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="d6f9f-267">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-267">'SignalR' uid:</span></span>

![title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="d6f9f-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-269">'Blazor'</span></span>

* <span data-ttu-id="d6f9f-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-270">'Identity'</span></span>
* <span data-ttu-id="d6f9f-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-271">'Let's Encrypt'</span></span>
* <span data-ttu-id="d6f9f-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-272">'Razor'</span></span>
* <span data-ttu-id="d6f9f-273">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-273">'SignalR' uid:</span></span>
* <span data-ttu-id="d6f9f-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="d6f9f-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-275">'Blazor'</span></span>
* <span data-ttu-id="d6f9f-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-276">'Identity'</span></span>
* <span data-ttu-id="d6f9f-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-277">'Let's Encrypt'</span></span>
* <span data-ttu-id="d6f9f-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-278">'Razor'</span></span>
* <span data-ttu-id="d6f9f-279">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-279">'SignalR' uid:</span></span>
* <span data-ttu-id="d6f9f-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="d6f9f-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-281">'Blazor'</span></span>
* <span data-ttu-id="d6f9f-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-282">'Identity'</span></span>
* <span data-ttu-id="d6f9f-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-283">'Let's Encrypt'</span></span>
* <span data-ttu-id="d6f9f-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-284">'Razor'</span></span>
* <span data-ttu-id="d6f9f-285">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-285">'SignalR' uid:</span></span>
* <span data-ttu-id="d6f9f-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="d6f9f-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-287">'Blazor'</span></span>
* <span data-ttu-id="d6f9f-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-288">'Identity'</span></span>
* <span data-ttu-id="d6f9f-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-289">'Let's Encrypt'</span></span>
* <span data-ttu-id="d6f9f-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-290">'Razor'</span></span>
* <span data-ttu-id="d6f9f-291">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-291">'SignalR' uid:</span></span>
* <span data-ttu-id="d6f9f-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="d6f9f-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-293">'Blazor'</span></span>
* <span data-ttu-id="d6f9f-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-294">'Identity'</span></span>
* <span data-ttu-id="d6f9f-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-295">'Let's Encrypt'</span></span>
* <span data-ttu-id="d6f9f-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-296">'Razor'</span></span>
* <span data-ttu-id="d6f9f-297">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-297">'SignalR' uid:</span></span>
* <span data-ttu-id="d6f9f-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="d6f9f-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-299">'Blazor'</span></span>

<span data-ttu-id="d6f9f-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-300">'Identity'</span></span> <span data-ttu-id="d6f9f-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-301">'Let's Encrypt'</span></span> <span data-ttu-id="d6f9f-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-302">'Razor'</span></span> <span data-ttu-id="d6f9f-303">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-303">'SignalR' uid:</span></span> <span data-ttu-id="d6f9f-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="d6f9f-305">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-305">'Blazor'</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="d6f9f-306">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-306">'Identity'</span></span> <span data-ttu-id="d6f9f-307">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-307">'Let's Encrypt'</span></span>

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="d6f9f-308">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-308">'Razor'</span></span>

!['SignalR' uid:](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="d6f9f-310">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-310">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="d6f9f-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-311">'Blazor'</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="d6f9f-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-312">'Identity'</span></span>

<span data-ttu-id="d6f9f-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-313">'Let's Encrypt'</span></span>

* <span data-ttu-id="d6f9f-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-314">'Razor'</span></span>
* <span data-ttu-id="d6f9f-315">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-315">'SignalR' uid:</span></span>
* <span data-ttu-id="d6f9f-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="d6f9f-317">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-317">'Blazor'</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="d6f9f-318">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-318">'Identity'</span></span>

<span data-ttu-id="d6f9f-319">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-319">'Let's Encrypt'</span></span>

* <span data-ttu-id="d6f9f-320">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-320">'Razor'</span></span>
* <span data-ttu-id="d6f9f-321">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-321">'SignalR' uid:</span></span>
* <span data-ttu-id="d6f9f-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="d6f9f-323">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-323">'Blazor'</span></span>
* <span data-ttu-id="d6f9f-324">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-324">'Identity'</span></span>
* <span data-ttu-id="d6f9f-325">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-325">'Let's Encrypt'</span></span>
* <span data-ttu-id="d6f9f-326">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-326">'Razor'</span></span>
* <span data-ttu-id="d6f9f-327">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-327">'SignalR' uid:</span></span>
* <span data-ttu-id="d6f9f-328">----------------- | :---: | | `/redirect-rule/1234/5678`         | Ja   | | `/my-cool-redirect-rule/1234/5678` | Ja   | | `/anotherredirect-rule/1234/5678`  | Ja   |</span><span class="sxs-lookup"><span data-stu-id="d6f9f-328">----------------- | :---: | | `/redirect-rule/1234/5678`         | Yes   | | `/my-cool-redirect-rule/1234/5678` | Yes   | | `/anotherredirect-rule/1234/5678`  | Yes   |</span></span>
* <span data-ttu-id="d6f9f-329">Die Umschreibungsregel (`^rewrite-rule/(\d+)/(\d+)`) stimmt nur mit Pfaden überein, wenn sie mit `rewrite-rule/` beginnen.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-329">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span>
* <span data-ttu-id="d6f9f-330">Beachten Sie die unterschiedliche Übereinstimmung in der folgenden Tabelle:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-330">In the following table, note the difference in matching.</span></span>
* <span data-ttu-id="d6f9f-331">Pfad</span><span class="sxs-lookup"><span data-stu-id="d6f9f-331">Path</span></span>
* <span data-ttu-id="d6f9f-332">Match</span><span class="sxs-lookup"><span data-stu-id="d6f9f-332">Match</span></span>
* <span data-ttu-id="d6f9f-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="d6f9f-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-334">'Blazor'</span></span>
* <span data-ttu-id="d6f9f-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-335">'Identity'</span></span>

> [!NOTE]
> <span data-ttu-id="d6f9f-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-336">'Let's Encrypt'</span></span> <span data-ttu-id="d6f9f-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-337">'Razor'</span></span> <span data-ttu-id="d6f9f-338">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-338">'SignalR' uid:</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="d6f9f-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="d6f9f-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-340">'Blazor'</span></span> <span data-ttu-id="d6f9f-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-341">'Identity'</span></span> <span data-ttu-id="d6f9f-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-342">'Let's Encrypt'</span></span> <span data-ttu-id="d6f9f-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-343">'Razor'</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="d6f9f-344">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-344">'SignalR' uid:</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="d6f9f-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> | <span data-ttu-id="d6f9f-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-346">'Blazor'</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="d6f9f-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-347">'Identity'</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="d6f9f-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-348">'Let's Encrypt'</span></span> |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="d6f9f-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-349">'Razor'</span></span> <span data-ttu-id="d6f9f-350">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-350">'SignalR' uid:</span></span> <span data-ttu-id="d6f9f-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="d6f9f-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-352">'Blazor'</span></span> <span data-ttu-id="d6f9f-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-353">'Identity'</span></span> <span data-ttu-id="d6f9f-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-354">'Let's Encrypt'</span></span>

<span data-ttu-id="d6f9f-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-355">'Razor'</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="d6f9f-356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-356">'SignalR' uid:</span></span> <span data-ttu-id="d6f9f-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="d6f9f-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-358">'Blazor'</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="d6f9f-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-359">'Identity'</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="d6f9f-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-360">'Let's Encrypt'</span></span>

<span data-ttu-id="d6f9f-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-361">'Razor'</span></span> <span data-ttu-id="d6f9f-362">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-362">'SignalR' uid:</span></span> <span data-ttu-id="d6f9f-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="d6f9f-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-364">'Blazor'</span></span> <span data-ttu-id="d6f9f-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-365">'Identity'</span></span> <span data-ttu-id="d6f9f-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-366">'Let's Encrypt'</span></span> <span data-ttu-id="d6f9f-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-367">'Razor'</span></span> <span data-ttu-id="d6f9f-368">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-368">'SignalR' uid:</span></span> <span data-ttu-id="d6f9f-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="d6f9f-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-370">'Blazor'</span></span>

!['Identity'](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="d6f9f-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-372">'Let's Encrypt'</span></span>

!['Razor'](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="d6f9f-374">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-374">'SignalR' uid:</span></span>

| <span data-ttu-id="d6f9f-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> | <span data-ttu-id="d6f9f-376">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-376">'Blazor'</span></span><br><span data-ttu-id="d6f9f-377">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-377">'Identity'</span></span> | <span data-ttu-id="d6f9f-378">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-378">'Let's Encrypt'</span></span><br><span data-ttu-id="d6f9f-379">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-379">'Razor'</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="d6f9f-380">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-380">'SignalR' uid:</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="d6f9f-381">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-381">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="d6f9f-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-382">'Blazor'</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="d6f9f-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-383">'Identity'</span></span> | <span data-ttu-id="d6f9f-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-384">'Let's Encrypt'</span></span><br><span data-ttu-id="d6f9f-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-385">'Razor'</span></span><br><span data-ttu-id="d6f9f-386">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-386">'SignalR' uid:</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="d6f9f-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="d6f9f-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-388">'Blazor'</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d6f9f-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-389">'Identity'</span></span>

<span data-ttu-id="d6f9f-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-390">'Let's Encrypt'</span></span> <span data-ttu-id="d6f9f-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-391">'Razor'</span></span> <span data-ttu-id="d6f9f-392">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-392">'SignalR' uid:</span></span>

* <span data-ttu-id="d6f9f-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="d6f9f-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-394">'Blazor'</span></span>
* <span data-ttu-id="d6f9f-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-395">'Identity'</span></span>
* <span data-ttu-id="d6f9f-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-396">'Let's Encrypt'</span></span>
* <span data-ttu-id="d6f9f-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-397">'Razor'</span></span>
* <span data-ttu-id="d6f9f-398">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-398">'SignalR' uid:</span></span>
* <span data-ttu-id="d6f9f-399">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-399">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

> [!NOTE]
> <span data-ttu-id="d6f9f-400">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-400">'Blazor'</span></span> <span data-ttu-id="d6f9f-401">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-401">'Identity'</span></span>

<span data-ttu-id="d6f9f-402">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-402">'Let's Encrypt'</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="d6f9f-403">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-403">'Razor'</span></span>

<span data-ttu-id="d6f9f-404">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-404">'SignalR' uid:</span></span> <span data-ttu-id="d6f9f-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="d6f9f-406">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-406">'Blazor'</span></span> <span data-ttu-id="d6f9f-407">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-407">'Identity'</span></span> <span data-ttu-id="d6f9f-408">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-408">'Let's Encrypt'</span></span>

<span data-ttu-id="d6f9f-409">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-409">'Razor'</span></span>

!['SignalR' uid:](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="d6f9f-415">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-415">'Razor'</span></span>

* <span data-ttu-id="d6f9f-416">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-416">'SignalR' uid:</span></span> <span data-ttu-id="d6f9f-417">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Ja   | | `/my-cool-rewrite-rule/1234/5678` | Nein    | | `/anotherrewrite-rule/1234/5678`  | Nein    |</span><span class="sxs-lookup"><span data-stu-id="d6f9f-417">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Yes   | | `/my-cool-rewrite-rule/1234/5678` | No    | | `/anotherrewrite-rule/1234/5678`  | No    |</span></span>

* <span data-ttu-id="d6f9f-418">Auf den `^rewrite-rule/`-Teil des Ausdruck folgen zwei Erfassungsgruppen: `(\d+)/(\d+)`.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-418">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="d6f9f-419">`\d` steht für *Übereinstimmung mit einer Ziffer (Zahl)* .</span><span class="sxs-lookup"><span data-stu-id="d6f9f-419">The `\d` signifies *match a digit (number)*.</span></span>

<span data-ttu-id="d6f9f-420">Das Pluszeichen (`+`) steht für *match one or more of the preceding character* (Übereinstimmung mit mindestens einem vorausgehenden Zeichen).</span><span class="sxs-lookup"><span data-stu-id="d6f9f-420">The plus sign (`+`) means *match one or more of the preceding character*.</span></span>

<span data-ttu-id="d6f9f-421">Aus diesem Grund muss die URL eine Zahl enthalten, auf die ein Schrägstrich und eine weitere Zahl folgt.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-421">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="d6f9f-422">Die Erfassungsgruppen werden in die umgeschriebene URL als `$1` und `$2` eingefügt.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-422">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="d6f9f-423">Über die Ersetzungszeichenfolge der Neuschreibungsregel werden die Erfassungsgruppen in die Abfragezeichenfolge eingefügt.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-423">The rewrite rule replacement string places the captured groups into the query string.</span></span>

<span data-ttu-id="d6f9f-424">Der angeforderte `/rewrite-rule/1234/5678`-Pfad wird umgeschrieben, um eine Ressource unter `/rewritten?var1=1234&var2=5678` abzurufen.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-424">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span>

<span data-ttu-id="d6f9f-425">Wenn es in der ursprünglichen Anforderung eine Abfragezeichenfolge gibt, bleibt diese erhalten, wenn die URL umgeschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-425">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

![Es gibt keinen Roundtrip zum Server, um die Ressource abzurufen.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="d6f9f-430">Verwenden Sie `skipRemainingRules: true`, wo immer das möglich ist, da das Abgleichen von Regeln rechnerisch sehr aufwendig ist und die Antwortzeit der App erhöht.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-430">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span>

<span data-ttu-id="d6f9f-431">Führen Sie folgende Schritte aus, um die schnellsten App-Antwortzeiten zu erreichen:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-431">For the fastest app response:</span></span> <span data-ttu-id="d6f9f-432">Sortieren Sie Neuschreibungsregeln von der am häufigsten abgeglichenen Regel zu der am seltensten abgeglichenen Regel.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-432">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="d6f9f-433">Überspringen Sie die Verarbeitung der übrigen Regeln, wenn es zu einer Übereinstimmung kommt und keine zusätzlichen Regelverarbeitungen erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-433">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

<span data-ttu-id="d6f9f-434">Apache: „mod_rewrite“</span><span class="sxs-lookup"><span data-stu-id="d6f9f-434">Apache mod_rewrite</span></span>

* <span data-ttu-id="d6f9f-435">Wenden Sie die Apache-Regeln „mod_rewrite“ mit <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> an.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-435">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span>
* <span data-ttu-id="d6f9f-436">Vergewissern Sie sich, dass die Regeldatei mit der App bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-436">Make sure that the rules file is deployed with the app.</span></span>
* <span data-ttu-id="d6f9f-437">Weitere Informationen zu diesen Regeln finden Sie unter [Apache: „mod_rewrite“](https://httpd.apache.org/docs/2.4/rewrite/).</span><span class="sxs-lookup"><span data-stu-id="d6f9f-437">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="d6f9f-438">Zum Lesen der Regeldatei *ApacheModRewrite.txt* wird <xref:System.IO.StreamReader> verwendet:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-438">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

<span data-ttu-id="d6f9f-439">Die Beispiel-App leitet Anforderungen von `/apache-mod-rules-redirect/(.\*)` auf `/redirected?id=$1` um.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-439">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span>

* <span data-ttu-id="d6f9f-440">Der Antwortstatuscode lautet *302 – Gefunden*.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-440">The response status code is *302 - Found*.</span></span>

  <span data-ttu-id="d6f9f-441">Ursprüngliche Anforderung: `/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="d6f9f-441">Original Request: `/apache-mod-rules-redirect/1234`</span></span> <span data-ttu-id="d6f9f-442">Browserfenster mit Entwicklertools, die die Anforderungen und Antworten nachverfolgen</span><span class="sxs-lookup"><span data-stu-id="d6f9f-442">Browser window with Developer Tools tracking the requests and responses</span></span>
* <span data-ttu-id="d6f9f-443">Die Middleware unterstützt die folgenden Servervariablen für die Apache „mod_rewrite“:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-443">The middleware supports the following Apache mod_rewrite server variables:</span></span>

  <span data-ttu-id="d6f9f-444">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="d6f9f-444">CONN_REMOTE_ADDR</span></span>

## <a name="package"></a><span data-ttu-id="d6f9f-445">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="d6f9f-445">HTTP_ACCEPT</span></span>

<span data-ttu-id="d6f9f-446">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="d6f9f-446">HTTP_CONNECTION</span></span>

<span data-ttu-id="d6f9f-447">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="d6f9f-447">HTTP_COOKIE</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="d6f9f-448">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="d6f9f-448">HTTP_FORWARDED</span></span>

<span data-ttu-id="d6f9f-449">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="d6f9f-449">HTTP_HOST</span></span> <span data-ttu-id="d6f9f-450">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="d6f9f-450">HTTP_REFERER</span></span> <span data-ttu-id="d6f9f-451">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="d6f9f-451">HTTP_USER_AGENT</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="d6f9f-452">HTTPS</span><span class="sxs-lookup"><span data-stu-id="d6f9f-452">HTTPS</span></span>

<span data-ttu-id="d6f9f-453">IPV6</span><span class="sxs-lookup"><span data-stu-id="d6f9f-453">IPV6</span></span>

* <span data-ttu-id="d6f9f-454">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="d6f9f-454">QUERY_STRING</span></span> <span data-ttu-id="d6f9f-455">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="d6f9f-455">REMOTE_ADDR</span></span>

* <span data-ttu-id="d6f9f-456">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="d6f9f-456">REMOTE_PORT</span></span> <span data-ttu-id="d6f9f-457">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="d6f9f-457">REQUEST_FILENAME</span></span> <span data-ttu-id="d6f9f-458">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="d6f9f-458">REQUEST_METHOD</span></span> <span data-ttu-id="d6f9f-459">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="d6f9f-459">REQUEST_SCHEME</span></span>

### <a name="url-redirect"></a><span data-ttu-id="d6f9f-460">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="d6f9f-460">REQUEST_URI</span></span>

<span data-ttu-id="d6f9f-461">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="d6f9f-461">SCRIPT_FILENAME</span></span> <span data-ttu-id="d6f9f-462">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="d6f9f-462">SERVER_ADDR</span></span> <span data-ttu-id="d6f9f-463">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="d6f9f-463">SERVER_PORT</span></span> <span data-ttu-id="d6f9f-464">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="d6f9f-464">SERVER_PROTOCOL</span></span> <span data-ttu-id="d6f9f-465">TIME</span><span class="sxs-lookup"><span data-stu-id="d6f9f-465">TIME</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="d6f9f-466">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="d6f9f-466">TIME_DAY</span></span> <span data-ttu-id="d6f9f-467">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="d6f9f-467">TIME_HOUR</span></span> <span data-ttu-id="d6f9f-468">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="d6f9f-468">TIME_MIN</span></span> <span data-ttu-id="d6f9f-469">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="d6f9f-469">TIME_MON</span></span> <span data-ttu-id="d6f9f-470">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="d6f9f-470">TIME_SEC</span></span>

* <span data-ttu-id="d6f9f-471">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="d6f9f-471">TIME_WDAY</span></span>
* <span data-ttu-id="d6f9f-472">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="d6f9f-472">TIME_YEAR</span></span>

<span data-ttu-id="d6f9f-473">Regeln zum IIS-Umschreibungsmodul</span><span class="sxs-lookup"><span data-stu-id="d6f9f-473">IIS URL Rewrite Module rules</span></span>

> [!WARNING]
> <span data-ttu-id="d6f9f-474">Um denselben Regelsatz zu verwenden, der für das IIS-Moduls für URL-Neuschreibung gilt, verwenden Sie <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-474">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="d6f9f-475">Vergewissern Sie sich, dass die Regeldatei mit der App bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-475">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="d6f9f-476">Geben Sie der Middleware nicht die Anweisung, die *web.config*-Datei der App zu verwenden, wenn sie auf der Windows Server-IIS ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-476">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span>

<span data-ttu-id="d6f9f-477">Bei IIS sollten diese Regeln außerhalb der *web.config*-Datei der App gespeichert werden, um Konflikte mit dem IIS-Neuschreibungsmodul zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-477">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span>

![Weitere Informationen und Beispiele zu den Regeln zum IIS-Umschreibungsmodul finden Sie unter [Using Url Rewrite Module 2.0 (Verwenden des URL-Umschreibungsmoduls 2.0)](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) und [URL Rewrite Module Configuration Reference (Konfigurationsreferenz des URL-Umschreibungsmoduls)](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="d6f9f-479">Zum Lesen der Regeldatei *IISUrlRewrite.xml* wird <xref:System.IO.StreamReader> verwendet:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-479">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span> <span data-ttu-id="d6f9f-480">Die Beispiel-App schreibt Anforderungen von `/iis-rules-rewrite/(.*)` in `/rewritten?id=$1` um.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-480">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="d6f9f-481">Die Antwort wird an den Client mit dem Statuscode *200 – OK* gesendet.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-481">The response is sent to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="d6f9f-482">Ursprüngliche Anforderung: `/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="d6f9f-482">Original Request: `/iis-rules-rewrite/1234`</span></span> <span data-ttu-id="d6f9f-483">Browserfenster mit Entwicklertools, die die Anforderung und Antwort nachverfolgen</span><span class="sxs-lookup"><span data-stu-id="d6f9f-483">Browser window with Developer Tools tracking the request and response</span></span>

<span data-ttu-id="d6f9f-484">Wenn Sie über ein aktives IIS-Umschreibungsmodul verfügen, für das die Regeln auf Serverebene konfiguriert sind, die negative Auswirkungen auf Ihre App hätten, können Sie das IIS-Umschreibungsmodul für die App deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-484">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="d6f9f-485">Weitere Informationen finden Sie unter [Disabling IIS modules (Deaktivieren von IIS-Modulen)](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="d6f9f-485">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span> <span data-ttu-id="d6f9f-486">Nicht unterstützte Features</span><span class="sxs-lookup"><span data-stu-id="d6f9f-486">Unsupported features</span></span> <span data-ttu-id="d6f9f-487">Die im Lieferumfang von ASP.NET Core 2.x enthaltene Middleware unterstützt die folgenden Features des IIS-URL-Umschreibungsmoduls nicht:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-487">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="d6f9f-488">Ausgehende Regeln</span><span class="sxs-lookup"><span data-stu-id="d6f9f-488">Outbound Rules</span></span>

<span data-ttu-id="d6f9f-489">Benutzerdefinierte Servervariablen</span><span class="sxs-lookup"><span data-stu-id="d6f9f-489">Custom Server Variables</span></span> <span data-ttu-id="d6f9f-490">Platzhalter</span><span class="sxs-lookup"><span data-stu-id="d6f9f-490">Wildcards</span></span> <span data-ttu-id="d6f9f-491">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="d6f9f-491">LogRewrittenUrl</span></span>

* <span data-ttu-id="d6f9f-492">Unterstützte Servervariablen</span><span class="sxs-lookup"><span data-stu-id="d6f9f-492">Supported server variables</span></span>
* <span data-ttu-id="d6f9f-493">Die Middleware unterstützt die folgenden Servervariablen für das IIS-URL-Umschreibungsmodul:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-493">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

<span data-ttu-id="d6f9f-494">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="d6f9f-494">CONTENT_LENGTH</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="d6f9f-495">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="d6f9f-495">CONTENT_TYPE</span></span> <span data-ttu-id="d6f9f-496">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="d6f9f-496">HTTP_ACCEPT</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="d6f9f-497">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="d6f9f-497">HTTP_CONNECTION</span></span> <span data-ttu-id="d6f9f-498">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="d6f9f-498">HTTP_COOKIE</span></span>

<span data-ttu-id="d6f9f-499">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="d6f9f-499">HTTP_HOST</span></span> <span data-ttu-id="d6f9f-500">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="d6f9f-500">HTTP_REFERER</span></span> <span data-ttu-id="d6f9f-501">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="d6f9f-501">HTTP_URL</span></span> <span data-ttu-id="d6f9f-502">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="d6f9f-502">HTTP_USER_AGENT</span></span>

<span data-ttu-id="d6f9f-503">HTTPS</span><span class="sxs-lookup"><span data-stu-id="d6f9f-503">HTTPS</span></span>

![LOCAL_ADDR](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="d6f9f-505">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="d6f9f-505">QUERY_STRING</span></span>

![REMOTE_ADDR](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="d6f9f-507">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="d6f9f-507">REMOTE_PORT</span></span>

<span data-ttu-id="d6f9f-508">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="d6f9f-508">REQUEST_FILENAME</span></span> <span data-ttu-id="d6f9f-509">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="d6f9f-509">REQUEST_URI</span></span> <span data-ttu-id="d6f9f-510">Sie können <xref:Microsoft.Extensions.FileProviders.IFileProvider> auch über <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> abrufen.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-510">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="d6f9f-511">Über diesen Ansatz können Sie flexibler einen Speicherort für die Dateien der Umschreibungsregeln auswählen.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-511">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="d6f9f-512">Vergewissern Sie sich, dass die Dateien zu den Umschreibungsregeln auf dem Server unter dem von Ihnen angegebenen Pfad bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-512">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>

![Methodenbasierte Regel](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="d6f9f-514">Verwenden Sie <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>, um Ihre eigene Regellogik in einer Methode zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-514">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span>

<span data-ttu-id="d6f9f-515">`Add` macht <xref:Microsoft.AspNetCore.Rewrite.RewriteContext> verfügbar, wodurch die Verwendung von <xref:Microsoft.AspNetCore.Http.HttpContext> in Ihrer Methode ermöglicht wird.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-515">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="d6f9f-516">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) bestimmt, wie die zusätzliche Pipelineverarbeitung erfolgt.</span><span class="sxs-lookup"><span data-stu-id="d6f9f-516">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span>

| <span data-ttu-id="d6f9f-517">Legen Sie den Wert auf eines der <xref:Microsoft.AspNetCore.Rewrite.RuleResult>-Felder fest, die in der folgenden Tabelle beschrieben sind:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-517">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>                               | <span data-ttu-id="d6f9f-518">Aktion</span><span class="sxs-lookup"><span data-stu-id="d6f9f-518">Action</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="d6f9f-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="d6f9f-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-520">'Blazor'</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="d6f9f-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-521">'Identity'</span></span>   |

<span data-ttu-id="d6f9f-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-522">'Let's Encrypt'</span></span> <span data-ttu-id="d6f9f-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-523">'Razor'</span></span>

| <span data-ttu-id="d6f9f-524">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-524">'SignalR' uid:</span></span>                              | <span data-ttu-id="d6f9f-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="d6f9f-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-526">'Blazor'</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="d6f9f-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-527">'Identity'</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="d6f9f-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-528">'Let's Encrypt'</span></span>    |

<span data-ttu-id="d6f9f-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-529">'Razor'</span></span> <span data-ttu-id="d6f9f-530">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-530">'SignalR' uid:</span></span> <span data-ttu-id="d6f9f-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="d6f9f-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-532">'Blazor'</span></span> <span data-ttu-id="d6f9f-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-533">'Identity'</span></span> <span data-ttu-id="d6f9f-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-534">'Let's Encrypt'</span></span> <span data-ttu-id="d6f9f-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-535">'Razor'</span></span> <span data-ttu-id="d6f9f-536">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-536">'SignalR' uid:</span></span>

<span data-ttu-id="d6f9f-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="d6f9f-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-538">'Blazor'</span></span> <span data-ttu-id="d6f9f-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-539">'Identity'</span></span> <span data-ttu-id="d6f9f-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-540">'Let's Encrypt'</span></span>

> [!NOTE]
> <span data-ttu-id="d6f9f-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-541">'Razor'</span></span> <span data-ttu-id="d6f9f-542">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-542">'SignalR' uid:</span></span>
>
> * <span data-ttu-id="d6f9f-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
> * <span data-ttu-id="d6f9f-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-544">'Blazor'</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="d6f9f-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-545">'Identity'</span></span>

<span data-ttu-id="d6f9f-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-546">'Let's Encrypt'</span></span> <span data-ttu-id="d6f9f-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-547">'Razor'</span></span> <span data-ttu-id="d6f9f-548">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-548">'SignalR' uid:</span></span>

<span data-ttu-id="d6f9f-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="d6f9f-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-550">'Blazor'</span></span> <span data-ttu-id="d6f9f-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-551">'Identity'</span></span>

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="d6f9f-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-552">'Let's Encrypt'</span></span>

!['Razor'](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="d6f9f-554">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-554">'SignalR' uid:</span></span>

* <span data-ttu-id="d6f9f-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="d6f9f-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-556">'Blazor'</span></span>
* <span data-ttu-id="d6f9f-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-557">'Identity'</span></span>
* <span data-ttu-id="d6f9f-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-558">'Let's Encrypt'</span></span>
* <span data-ttu-id="d6f9f-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-559">'Razor'</span></span>
* <span data-ttu-id="d6f9f-560">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-560">'SignalR' uid:</span></span>
* <span data-ttu-id="d6f9f-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="d6f9f-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-562">'Blazor'</span></span>
* <span data-ttu-id="d6f9f-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-563">'Identity'</span></span>
* <span data-ttu-id="d6f9f-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-564">'Let's Encrypt'</span></span>
* <span data-ttu-id="d6f9f-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-565">'Razor'</span></span>
* <span data-ttu-id="d6f9f-566">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-566">'SignalR' uid:</span></span>
* <span data-ttu-id="d6f9f-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="d6f9f-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-568">'Blazor'</span></span>
* <span data-ttu-id="d6f9f-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-569">'Identity'</span></span>
* <span data-ttu-id="d6f9f-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-570">'Let's Encrypt'</span></span>
* <span data-ttu-id="d6f9f-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-571">'Razor'</span></span>
* <span data-ttu-id="d6f9f-572">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-572">'SignalR' uid:</span></span>
* <span data-ttu-id="d6f9f-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="d6f9f-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-574">'Blazor'</span></span>
* <span data-ttu-id="d6f9f-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-575">'Identity'</span></span>
* <span data-ttu-id="d6f9f-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-576">'Let's Encrypt'</span></span>
* <span data-ttu-id="d6f9f-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-577">'Razor'</span></span>
* <span data-ttu-id="d6f9f-578">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-578">'SignalR' uid:</span></span>
* <span data-ttu-id="d6f9f-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="d6f9f-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-580">'Blazor'</span></span>
* <span data-ttu-id="d6f9f-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-581">'Identity'</span></span>
* <span data-ttu-id="d6f9f-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-582">'Let's Encrypt'</span></span>
* <span data-ttu-id="d6f9f-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-583">'Razor'</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="d6f9f-584">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-584">'SignalR' uid:</span></span>

<span data-ttu-id="d6f9f-585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="d6f9f-586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-586">'Blazor'</span></span> <span data-ttu-id="d6f9f-587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-587">'Identity'</span></span> <span data-ttu-id="d6f9f-588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-588">'Let's Encrypt'</span></span> <span data-ttu-id="d6f9f-589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-589">'Razor'</span></span>

<span data-ttu-id="d6f9f-590">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-590">'SignalR' uid:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="d6f9f-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="d6f9f-592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-592">'Blazor'</span></span>

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="d6f9f-593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-593">'Identity'</span></span>

!['Let's Encrypt'](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="d6f9f-595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-595">'Razor'</span></span> <span data-ttu-id="d6f9f-596">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-596">'SignalR' uid:</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="d6f9f-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="d6f9f-598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-598">'Blazor'</span></span>

* <span data-ttu-id="d6f9f-599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-599">'Identity'</span></span>
* <span data-ttu-id="d6f9f-600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-600">'Let's Encrypt'</span></span>
* <span data-ttu-id="d6f9f-601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-601">'Razor'</span></span>
* <span data-ttu-id="d6f9f-602">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-602">'SignalR' uid:</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="d6f9f-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="d6f9f-604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-604">'Blazor'</span></span>

* <span data-ttu-id="d6f9f-605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-605">'Identity'</span></span>
* <span data-ttu-id="d6f9f-606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-606">'Let's Encrypt'</span></span>
* <span data-ttu-id="d6f9f-607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-607">'Razor'</span></span>
* <span data-ttu-id="d6f9f-608">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-608">'SignalR' uid:</span></span>
* <span data-ttu-id="d6f9f-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="d6f9f-610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-610">'Blazor'</span></span>
* <span data-ttu-id="d6f9f-611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-611">'Identity'</span></span>
* <span data-ttu-id="d6f9f-612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-612">'Let's Encrypt'</span></span>
* <span data-ttu-id="d6f9f-613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-613">'Razor'</span></span>
* <span data-ttu-id="d6f9f-614">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-614">'SignalR' uid:</span></span>
* <span data-ttu-id="d6f9f-615">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-615">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="d6f9f-616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-616">'Blazor'</span></span>
* <span data-ttu-id="d6f9f-617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-617">'Identity'</span></span>
* <span data-ttu-id="d6f9f-618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-618">'Let's Encrypt'</span></span>
* <span data-ttu-id="d6f9f-619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-619">'Razor'</span></span>
* <span data-ttu-id="d6f9f-620">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-620">'SignalR' uid:</span></span>

> [!NOTE]
> <span data-ttu-id="d6f9f-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="d6f9f-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-622">'Blazor'</span></span> <span data-ttu-id="d6f9f-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-623">'Identity'</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="d6f9f-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-624">'Let's Encrypt'</span></span>

<span data-ttu-id="d6f9f-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-625">'Razor'</span></span> <span data-ttu-id="d6f9f-626">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-626">'SignalR' uid:</span></span> <span data-ttu-id="d6f9f-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="d6f9f-628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-628">'Blazor'</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="d6f9f-629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-629">'Identity'</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="d6f9f-630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-630">'Let's Encrypt'</span></span> | <span data-ttu-id="d6f9f-631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-631">'Razor'</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="d6f9f-632">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-632">'SignalR' uid:</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="d6f9f-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="d6f9f-634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-634">'Blazor'</span></span> <span data-ttu-id="d6f9f-635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-635">'Identity'</span></span> <span data-ttu-id="d6f9f-636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-636">'Let's Encrypt'</span></span> <span data-ttu-id="d6f9f-637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-637">'Razor'</span></span> <span data-ttu-id="d6f9f-638">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-638">'SignalR' uid:</span></span> <span data-ttu-id="d6f9f-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="d6f9f-640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-640">'Blazor'</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="d6f9f-641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-641">'Identity'</span></span> <span data-ttu-id="d6f9f-642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-642">'Let's Encrypt'</span></span> <span data-ttu-id="d6f9f-643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-643">'Razor'</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="d6f9f-644">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-644">'SignalR' uid:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="d6f9f-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="d6f9f-646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-646">'Blazor'</span></span> <span data-ttu-id="d6f9f-647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-647">'Identity'</span></span> <span data-ttu-id="d6f9f-648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-648">'Let's Encrypt'</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="d6f9f-649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-649">'Razor'</span></span> <span data-ttu-id="d6f9f-650">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-650">'SignalR' uid:</span></span> <span data-ttu-id="d6f9f-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="d6f9f-652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-652">'Blazor'</span></span> <span data-ttu-id="d6f9f-653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-653">'Identity'</span></span> <span data-ttu-id="d6f9f-654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-654">'Let's Encrypt'</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="d6f9f-655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-655">'Razor'</span></span>

!['SignalR' uid:](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="d6f9f-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

!['Blazor'](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="d6f9f-659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-659">'Identity'</span></span>

| <span data-ttu-id="d6f9f-660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-660">'Let's Encrypt'</span></span> | <span data-ttu-id="d6f9f-661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-661">'Razor'</span></span><br><span data-ttu-id="d6f9f-662">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-662">'SignalR' uid:</span></span> | <span data-ttu-id="d6f9f-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span><br><span data-ttu-id="d6f9f-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-664">'Blazor'</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="d6f9f-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-665">'Identity'</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="d6f9f-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-666">'Let's Encrypt'</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="d6f9f-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-667">'Razor'</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="d6f9f-668">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-668">'SignalR' uid:</span></span> | <span data-ttu-id="d6f9f-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span><br><span data-ttu-id="d6f9f-670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-670">'Blazor'</span></span><br><span data-ttu-id="d6f9f-671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-671">'Identity'</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="d6f9f-672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-672">'Let's Encrypt'</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="d6f9f-673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-673">'Razor'</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d6f9f-674">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-674">'SignalR' uid:</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <span data-ttu-id="d6f9f-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="d6f9f-676">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-676">'Blazor'</span></span>
* <span data-ttu-id="d6f9f-677">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-677">'Identity'</span></span>
* <span data-ttu-id="d6f9f-678">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-678">'Let's Encrypt'</span></span>
* <span data-ttu-id="d6f9f-679">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-679">'Razor'</span></span>
* <span data-ttu-id="d6f9f-680">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-680">'SignalR' uid:</span></span>
* <span data-ttu-id="d6f9f-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d6f9f-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="d6f9f-682">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-682">'Blazor'</span></span>
* <span data-ttu-id="d6f9f-683">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d6f9f-683">'Identity'</span></span>

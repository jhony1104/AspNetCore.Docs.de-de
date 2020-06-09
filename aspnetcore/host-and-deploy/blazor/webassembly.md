---
title: "title: 'Hosten und Bereitstellen von ASP.NET Core Blazor WebAssembly' author: guardrex description: 'Erfahren Sie, wie Sie eine Blazor-App mithilfe von ASP.NET Core, Content Delivery Network (CDN), Dateiservern und GitHub-Seiten hosten und bereitstellen.'"
author: guardrex
description: "monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 05/28/2020 no-loc:"
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/28/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: 09f74edaa3d1cb0d51e0ce8d0209383885b81f5f
ms.sourcegitcommit: 2e9973cea5c36d0dd64d5e946bb776b8bb73a4b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84239391"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="bc6aa-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bc6aa-103">'Blazor'</span></span>

<span data-ttu-id="bc6aa-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bc6aa-104">'Identity'</span></span>

<span data-ttu-id="bc6aa-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bc6aa-105">'Let's Encrypt'</span></span>

* <span data-ttu-id="bc6aa-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bc6aa-106">'Razor'</span></span>
* <span data-ttu-id="bc6aa-107">'SignalR' uid: host-and-deploy/blazor/webassembly</span><span class="sxs-lookup"><span data-stu-id="bc6aa-107">'SignalR' uid: host-and-deploy/blazor/webassembly</span></span>

<span data-ttu-id="bc6aa-108">Hosten und Bereitstellen von ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="bc6aa-108">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

* <span data-ttu-id="bc6aa-109">Von [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams) und [Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="bc6aa-109">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span> <span data-ttu-id="bc6aa-110">Mit dem [Blazor WebAssembly-Hostingmodell](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="bc6aa-110">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>
* <span data-ttu-id="bc6aa-111">Die Blazor-App, ihre Abhängigkeiten und die .NET-Runtime werden parallel im Browser heruntergeladen.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-111">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span> <span data-ttu-id="bc6aa-112">Die App wird direkt im UI-Thread des Browsers ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-112">The app is executed directly on the browser UI thread.</span></span>

## <a name="precompression"></a><span data-ttu-id="bc6aa-113">Die folgenden Bereitstellungsstrategien werden unterstützt:</span><span class="sxs-lookup"><span data-stu-id="bc6aa-113">The following deployment strategies are supported:</span></span>

<span data-ttu-id="bc6aa-114">Die Blazor-App wird von einer ASP.NET Core-App unterstützt.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-114">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="bc6aa-115">Diese Strategie wird im Abschnitt [Gehostete Bereitstellung mit ASP.NET Core](#hosted-deployment-with-aspnet-core) behandelt.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-115">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>

* <span data-ttu-id="bc6aa-116">Die Blazor-App wird auf einem Webserver oder Webservice für statisches Hosting abgelegt, auf dem .NET nicht zur Unterstützung der Blazor-App verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-116">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span>
* <span data-ttu-id="bc6aa-117">Diese Strategie wird im Abschnitt [Eigenständige Bereitstellung](#standalone-deployment) behandelt, der Informationen zum Hosten einer Blazor WebAssembly-App als untergeordnete IIS-App enthält.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-117">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

<span data-ttu-id="bc6aa-118">Vorkomprimierung</span><span class="sxs-lookup"><span data-stu-id="bc6aa-118">Precompression</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

<span data-ttu-id="bc6aa-119">Wenn eine Blazor WebAssembly-App veröffentlicht wird, wird die Ausgabe vorkomprimiert, um die App-Größe zu verringern und die Notwendigkeit für eine Laufzeitkomprimierung zu beseitigen.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-119">When a Blazor WebAssembly app is published, the output is precompressed to reduce the app's size and remove the need for runtime compression.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="bc6aa-120">Die folgenden Komprimierungsalgorithmen werden verwendet:</span><span class="sxs-lookup"><span data-stu-id="bc6aa-120">The following compression algorithms are used:</span></span>

<span data-ttu-id="bc6aa-121">[Brotli](https://tools.ietf.org/html/rfc7932) (höchste Stufe)</span><span class="sxs-lookup"><span data-stu-id="bc6aa-121">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span> [<span data-ttu-id="bc6aa-122">Gzip</span><span class="sxs-lookup"><span data-stu-id="bc6aa-122">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

* <span data-ttu-id="bc6aa-123">Um die Komprimierung zu deaktivieren, fügen Sie der Projektdatei der App die `BlazorEnableCompression` MSBuild-Eigenschaft hinzu, und setzen Sie den Wert auf `false`:</span><span class="sxs-lookup"><span data-stu-id="bc6aa-123">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>
* <span data-ttu-id="bc6aa-124">Informationen zur Komprimierungskonfiguration der Datei *web. config* von IIS finden Sie im Abschnitt [IIS: Brotli- und Gzip-Komprimierung](#brotli-and-gzip-compression).</span><span class="sxs-lookup"><span data-stu-id="bc6aa-124">For IIS *web.config* compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span>

<span data-ttu-id="bc6aa-125">Erneutes Generieren von URLs für ein ordnungsgemäßes Routing</span><span class="sxs-lookup"><span data-stu-id="bc6aa-125">Rewrite URLs for correct routing</span></span>

1. <span data-ttu-id="bc6aa-126">Routinganforderungen für Seitenkomponenten in einer Blazor WebAssembly-App sind nicht so unkompliziert wie Routinganforderungen in einer gehosteten Blazor Server-App.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-126">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span>
1. <span data-ttu-id="bc6aa-127">Gehen Sie von einer Blazor WebAssembly-App mit zwei Komponenten aus:</span><span class="sxs-lookup"><span data-stu-id="bc6aa-127">Consider a Blazor WebAssembly app with two components:</span></span>
1. <span data-ttu-id="bc6aa-128">*Main.razor*: Wird im Stammverzeichnis der App geladen und enthält einen Link zur `About`-Komponente (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="bc6aa-128">*Main.razor*: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
1. <span data-ttu-id="bc6aa-129">*About.razor*: `About`-Komponente.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-129">*About.razor*: `About` component.</span></span>

<span data-ttu-id="bc6aa-130">Wenn das Standarddokument der App über die Adressleiste des Browsers (z. B. `https://www.contoso.com/`) angefordert wird, geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="bc6aa-130">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span> <span data-ttu-id="bc6aa-131">Der Browser sendet eine Anforderung.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-131">The browser makes a request.</span></span> <span data-ttu-id="bc6aa-132">Die Standardseite wird zurückgegeben, in der Regel *index.html*.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-132">The default page is returned, which is usually *index.html*.</span></span>

<span data-ttu-id="bc6aa-133">*index.html* startet die App.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-133">*index.html* bootstraps the app.</span></span> <span data-ttu-id="bc6aa-134">Der Blazor-Router wird geladen, und die Razor-Komponente `Main` wird gerendert.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-134">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="bc6aa-135">Auf der Seite „Main“ kann der Link zur `About`-Komponente auf dem Client ausgewählt werden, da der Blazor-Router dafür sorgt, dass der Browser im Internet keine Anforderung für `About` an `www.contoso.com` sendet, und stattdessen die gerenderte `About`-Komponente selbst bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-135">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="bc6aa-136">Alle Anforderungen von internen Endpunkten *innerhalb der Blazor WebAssembly-App* funktionieren auf dieselbe Weise: Durch Anforderungen werden keine browserbasierten Anforderungen an serverseitig gehostete Ressourcen im Internet ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-136">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span>

<span data-ttu-id="bc6aa-137">Der Router verarbeitet Anforderungen intern.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-137">The router handles the requests internally.</span></span> <span data-ttu-id="bc6aa-138">Wenn eine Anforderung für `www.contoso.com/About` über die Adressleiste des Browsers gesendet wird, tritt bei der Anforderung ein Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-138">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="bc6aa-139">Diese Ressource ist im Internethost der App nicht vorhanden. Daher wird die Antwort *404 Nicht gefunden* zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-139">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="bc6aa-140">Da Browser Anforderungen für clientseitige Seiten an internetbasierte Hosts senden, müssen Webserver und Hostingdienste alle Anforderungen für Ressourcen, die sich nicht physisch auf dem Server befinden, auf der Seite *index.html* erneut generieren.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-140">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span>

<span data-ttu-id="bc6aa-141">Wenn *index.html* zurückgegeben wird, übernimmt der Blazor-Router der App und antwortet mit der richtigen Ressource.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-141">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span> <span data-ttu-id="bc6aa-142">Wenn Sie die Bereitstellung auf einem IIS-Server durchführen, können Sie das URL-Rewrite-Modul mit der veröffentlichten *web.config*-Datei der App verwenden.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-142">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="bc6aa-143">Weitere Informationen finden Sie im Abschnitt [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="bc6aa-143">For more information, see the [IIS](#iis) section.</span></span> <span data-ttu-id="bc6aa-144">Gehostete Bereitstellung mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bc6aa-144">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="bc6aa-145">Mit einer *gehosteten Bereitstellung* wird die Blazor WebAssembly-App über eine auf einem Webserver ausgeführte [ASP.NET Core-App für Browser](xref:index) bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-145">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="bc6aa-146">Der Client Blazor der WebAssembly-App wird im Ordner */bin/Release/{ZIELFRAMEWORK}/publish/wwwroot* der Server-App zusammen mit allen anderen statischen Webressourcen der Server-App veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-146">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="bc6aa-147">Die beiden Apps werden zusammen bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-147">The two apps are deployed together.</span></span>

<span data-ttu-id="bc6aa-148">Hierfür wird ein Webserver benötigt, auf dem eine ASP.NET Core-App gehostet werden kann.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-148">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="bc6aa-149">Bei einer gehosteten Bereitstellung enthält Visual Studio die **Blazor WebAssembly-App**-Projektvorlage (`blazorwasm`-Vorlage bei Verwendung des Befehls [dotnet new](/dotnet/core/tools/dotnet-new)) mit der ausgewählten Option **Hosted** (`-ho|--hosted`, wenn Sie den `dotnet new`-Befehl verwenden).</span><span class="sxs-lookup"><span data-stu-id="bc6aa-149">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="bc6aa-150">Weitere Informationen zum Hosten und Bereitstellen von ASP.NET Core-Apps finden Sie unter <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-150">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="bc6aa-151">Informationen zum Bereitstellen für Azure App Service finden Sie unter <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-151">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

<span data-ttu-id="bc6aa-152">Eigenständige Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="bc6aa-152">Standalone deployment</span></span>

<span data-ttu-id="bc6aa-153">Bei einer *eigenständigen Bereitstellung* wird die Blazor WebAssembly-App in Form von statischen Dateien bereitgestellt, die von Clients direkt angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-153">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="bc6aa-154">Jeder Server für statische Dateien kann die Blazor-App bedienen.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-154">Any static file server is able to serve the Blazor app.</span></span> <span data-ttu-id="bc6aa-155">Eigenständige Bereitstellungsobjekte werden im Ordner */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-155">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="iis"></a><span data-ttu-id="bc6aa-156">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="bc6aa-156">Azure App Service</span></span>

Blazor<span data-ttu-id="bc6aa-157"> WebAssembly-Apps können in Azure App Service unter Windows bereitgestellt werden. Dort wird die App auf [IIS](#iis) gehostet.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-157"> WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span> <span data-ttu-id="bc6aa-158">Die Bereitstellung einer eigenständigen Blazor WebAssembly-App in Azure App Service für Linux wird derzeit nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-158">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span>

<span data-ttu-id="bc6aa-159">Ein Linux-Serverimage zum Hosten der App ist derzeit nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-159">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="bc6aa-160">Wir arbeiten daran, sodass dieses Szenario bald unterstützt werden kann.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-160">Work is in progress to enable this scenario.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="bc6aa-161">IIS</span><span class="sxs-lookup"><span data-stu-id="bc6aa-161">IIS</span></span>

<span data-ttu-id="bc6aa-162">IIS ist ein leistungsfähiger Server für statische Dateien für Blazor-Apps.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-162">IIS is a capable static file server for Blazor apps.</span></span>

* <span data-ttu-id="bc6aa-163">Informationen zum Konfigurieren von IIS zum Hosten von Blazor finden Sie unter [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis) (Erstellen einer statischen Website unter IIS).</span><span class="sxs-lookup"><span data-stu-id="bc6aa-163">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>
  * <span data-ttu-id="bc6aa-164">Veröffentlichte Objekte werden im Ordner */bin/Release/{Zielframework}/publish* erstellt.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-164">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>
  * <span data-ttu-id="bc6aa-165">Die Inhalte des Ordners *publish* werden auf dem Webserver oder über den Hostingdienst gehostet.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-165">Host the contents of the *publish* folder on the web server or hosting service.</span></span>
  * <span data-ttu-id="bc6aa-166">web.config</span><span class="sxs-lookup"><span data-stu-id="bc6aa-166">web.config</span></span>
  * <span data-ttu-id="bc6aa-167">Beim Veröffentlichen eines Blazor-Projekts wird eine Datei *web.config* mit der folgenden IIS-Konfiguration erstellt:</span><span class="sxs-lookup"><span data-stu-id="bc6aa-167">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>
  * <span data-ttu-id="bc6aa-168">Für die folgenden Dateierweiterungen werden MIME-Typen festgelegt:</span><span class="sxs-lookup"><span data-stu-id="bc6aa-168">MIME types are set for the following file extensions:</span></span>
* <span data-ttu-id="bc6aa-169">*.dll*: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="bc6aa-169">*.dll*: `application/octet-stream`</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="bc6aa-170">*.json*: `application/json`</span><span class="sxs-lookup"><span data-stu-id="bc6aa-170">*.json*: `application/json`</span></span>
  * <span data-ttu-id="bc6aa-171">*.wasm*: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="bc6aa-171">*.wasm*: `application/wasm`</span></span>
  * <span data-ttu-id="bc6aa-172">*.woff*: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="bc6aa-172">*.woff*: `application/font-woff`</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="bc6aa-173">*.woff2*: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="bc6aa-173">*.woff2*: `application/font-woff`</span></span>

<span data-ttu-id="bc6aa-174">Für die folgenden MIME-Typen wird die HTTP-Komprimierung aktiviert:</span><span class="sxs-lookup"><span data-stu-id="bc6aa-174">HTTP compression is enabled for the following MIME types:</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="bc6aa-175">URL-Rewrite-Modul-Regeln werden eingerichtet:</span><span class="sxs-lookup"><span data-stu-id="bc6aa-175">URL Rewrite Module rules are established:</span></span>

<span data-ttu-id="bc6aa-176">Stellen Sie das Unterverzeichnis bereit, in dem sich die statischen Objekte der App befinden (*wwwroot/{PATH REQUESTED}* ).</span><span class="sxs-lookup"><span data-stu-id="bc6aa-176">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span> <span data-ttu-id="bc6aa-177">Richten Sie ein SPA-Fallbackrouting ein, sodass Anforderungen für nicht dateibasierte Objekte an das Standarddokument der App im entsprechenden Ordner für statische Objekte (*wwwroot/index.html*) umgeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-177">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span> <span data-ttu-id="bc6aa-178">Verwenden einer benutzerdefinierten Datei web.config</span><span class="sxs-lookup"><span data-stu-id="bc6aa-178">Use a custom web.config</span></span> <span data-ttu-id="bc6aa-179">Um eine benutzerdefinierte Datei *web.config* zu verwenden, speichern Sie die benutzerdefinierte Datei *web.config* im Stammverzeichnis des Projektordners und veröffentlichen das Projekt.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-179">To use a custom *web.config* file, place the custom *web.config* file at the root of the project folder and publish the project.</span></span>

1. <span data-ttu-id="bc6aa-180">Installieren des URL-Rewrite-Moduls</span><span class="sxs-lookup"><span data-stu-id="bc6aa-180">Install the URL Rewrite Module</span></span> <span data-ttu-id="bc6aa-181">Das [URL-Rewrite-Modul](https://www.iis.net/downloads/microsoft/url-rewrite) wird zum Umschreiben von URLs benötigt.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-181">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="bc6aa-182">Das Modul wird nicht standardmäßig installiert und ist für die Installation als Webserver (IIS)-Rollendienstfunktion nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-182">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span>
1. <span data-ttu-id="bc6aa-183">Das Modul muss von der IIS-Website heruntergeladen werden.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-183">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="bc6aa-184">Verwenden Sie den Webplattform-Installer zur Installation des Moduls:</span><span class="sxs-lookup"><span data-stu-id="bc6aa-184">Use the Web Platform Installer to install the module:</span></span> <span data-ttu-id="bc6aa-185">Navigieren Sie lokal zur [URL-Rewrite-Module-Downloadseite](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="bc6aa-185">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="bc6aa-186">Wählen Sie zum Herunterladen der englischen Version des WebPI-Installers **WebPI** aus.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-186">For the English version, select **WebPI** to download the WebPI installer.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="bc6aa-187">Wählen Sie zum Herunterladen des Installers in einer anderen Sprache die entsprechende Architektur für den Server (x86/x64) aus.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-187">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>

<span data-ttu-id="bc6aa-188">Kopieren Sie den Installer auf den Server.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-188">Copy the installer to the server.</span></span> <span data-ttu-id="bc6aa-189">Führen Sie den Installer aus.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-189">Run the installer.</span></span>

* <span data-ttu-id="bc6aa-190">Klicken Sie auf die Schaltfläche **Installieren**, und stimmen Sie den Lizenzbedingungen zu.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-190">Select the **Install** button and accept the license terms.</span></span>
* <span data-ttu-id="bc6aa-191">Der Server muss nach Abschluss der Installation nicht neu gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-191">A server restart isn't required after the install completes.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="bc6aa-192">Konfigurieren der Website</span><span class="sxs-lookup"><span data-stu-id="bc6aa-192">Configure the website</span></span>

<span data-ttu-id="bc6aa-193">Legen Sie für den **physischen Pfad** der Website den Ordner der App fest.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-193">Set the website's **Physical path** to the app's folder.</span></span>

* <span data-ttu-id="bc6aa-194">Der Ordner enthält Folgendes:</span><span class="sxs-lookup"><span data-stu-id="bc6aa-194">The folder contains:</span></span>

  <span data-ttu-id="bc6aa-195">Die Datei *web.config*, die von IIS zum Konfigurieren der Website verwendet wird, mit den erforderlichen Umleitungsregeln und Dateiinhaltstypen</span><span class="sxs-lookup"><span data-stu-id="bc6aa-195">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="bc6aa-196">Den Ordner der App für statische Objekte</span><span class="sxs-lookup"><span data-stu-id="bc6aa-196">The app's static asset folder.</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

<span data-ttu-id="bc6aa-197">Hosten als IIS-untergeordnete App</span><span class="sxs-lookup"><span data-stu-id="bc6aa-197">Host as an IIS sub-app</span></span> <span data-ttu-id="bc6aa-198">Wenn eine eigenständige App als IIS-untergeordnete App gehostet wird, führen Sie einen der folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="bc6aa-198">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="bc6aa-199">Deaktivieren Sie den vererbten ASP.NET Core Module-Handler.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-199">Disable the inherited ASP.NET Core Module handler.</span></span>

<span data-ttu-id="bc6aa-200">Entfernen Sie den Handler in der veröffentlichen Datei *web.config* der Blazor-App, indem Sie der Datei einen `<handlers>`-Abschnitt hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="bc6aa-200">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span> <span data-ttu-id="bc6aa-201">Deaktivieren Sie die Vererbung des `<system.webServer>`-Abschnitts der Stammanwendung (d. h. der übergeordneten Anwendung), indem Sie ein `<location>`-Element verwenden, bei dem `inheritInChildApplications` auf `false` festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="bc6aa-201">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="bc6aa-202">Das Entfernen des Handlers bzw. das Deaktivieren der Vererbung wird zusätzlich zur [Konfiguration des Basispfads der App](xref:host-and-deploy/blazor/index#app-base-path) durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-202">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

<span data-ttu-id="bc6aa-203">Legen Sie den Basispfad der App in der *index.html*-Datei der App auf den IIS-Alias fest, der beim Konfigurieren der untergeordneten App in IIS verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-203">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span> <span data-ttu-id="bc6aa-204">Brotli- und Gzip-Komprimierung</span><span class="sxs-lookup"><span data-stu-id="bc6aa-204">Brotli and Gzip compression</span></span> <span data-ttu-id="bc6aa-205">IIS kann über *web.config* so konfiguriert werden, dass Brotli- oder Gzip-komprimierte Blazor Ressourcen bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-205">IIS can be configured via *web.config* to serve Brotli or Gzip compressed Blazor assets.</span></span>

<span data-ttu-id="bc6aa-206">Eine Beispielkonfiguration finden Sie unter [web.config](webassembly/_samples/web.config?raw=true).</span><span class="sxs-lookup"><span data-stu-id="bc6aa-206">For an example configuration, see [web.config](webassembly/_samples/web.config?raw=true).</span></span>

### <a name="azure-storage"></a><span data-ttu-id="bc6aa-207">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="bc6aa-207">Troubleshooting</span></span>

<span data-ttu-id="bc6aa-208">Wenn die Fehlermeldung *500: Interner Serverfehler* angezeigt wird und IIS-Manager beim Zugriff auf die Konfiguration der Website eine Fehlermeldung anzeigt, überprüfen Sie, ob das URL-Rewrite-Modul installiert ist.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-208">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="bc6aa-209">Wenn das Modul nicht installiert ist, kann die Datei *web.config* von IIS nicht analysiert werden.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-209">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span>

<span data-ttu-id="bc6aa-210">Dadurch wird verhindert, dass die Konfiguration der Website vom IIS-Manager geladen wird, und dass die statischen Blazor-Dateien auf der Website bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-210">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

* <span data-ttu-id="bc6aa-211">Weitere Informationen zur Problembehandlung von Bereitstellungen für IIS finden Sie unter <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-211">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>
* <span data-ttu-id="bc6aa-212">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="bc6aa-212">Azure Storage</span></span> <span data-ttu-id="bc6aa-213">Das Hosten statischer Dateien von [Azure Storage](/azure/storage/) ermöglicht das serverlose Blazor-App-Hosting.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-213">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="bc6aa-214">Benutzerdefinierte Domänennamen, das Azure Content Delivery Network (CDN) und HTTPS werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-214">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span> <span data-ttu-id="bc6aa-215">Wenn der Blobdienst für das Hosten von statischen Websites in einem Speicherkonto aktiviert ist, gehen Sie folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="bc6aa-215">When the blob service is enabled for static website hosting on a storage account:</span></span>

<span data-ttu-id="bc6aa-216">Legen Sie den **Namen des Indexdokuments** auf `index.html` fest.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-216">Set the **Index document name** to `index.html`.</span></span>

* <span data-ttu-id="bc6aa-217">Legen Sie den **Pfad des Fehlerdokuments** auf `index.html` fest.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-217">Set the **Error document path** to `index.html`.</span></span>
* Razor<span data-ttu-id="bc6aa-218">-Komponenten und andere Endpunkte, bei denen es sich nicht um Dateien handelt, befinden sich nicht in physischen Pfaden in dem statischen Inhalt, der vom Blobdienst gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-218"> components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span>

  <span data-ttu-id="bc6aa-219">Wenn eine Anforderung für eine dieser Ressourcen empfangen wird, die vom Blazor-Router verarbeitet werden soll, leitet der vom Blob-Dienst generierte *Fehler 404 – Nicht gefunden* die Anforderung an den **Pfad des Fehlerdokuments** weiter.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-219">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span>
  
  1. <span data-ttu-id="bc6aa-220">Das Blob *index.html* wird zurückgegeben, und der Blazor-Router lädt und verarbeitet den Pfad.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-220">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>
  1. <span data-ttu-id="bc6aa-221">Wenn Dateien aufgrund ungeeigneter MIME-Typen im `Content-Type`-Headern der Dateien nicht zur Laufzeit geladen werden, führen Sie eine der folgenden Aktionen durch:</span><span class="sxs-lookup"><span data-stu-id="bc6aa-221">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

<span data-ttu-id="bc6aa-222">Konfigurieren Sie die Tools so, dass beim Bereitstellen der Dateien die richtigen MIME-Typen (`Content-Type`-Header) bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-222">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>

### <a name="nginx"></a><span data-ttu-id="bc6aa-223">Ändern Sie nach Bereitstellung der App die MIME-Typen (`Content-Type`-Header) für die Dateien.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-223">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

<span data-ttu-id="bc6aa-224">Führen Sie im Storage-Explorer (Azure-Portal) für jede Datei folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="bc6aa-224">In Storage Explorer (Azure portal) for each file:</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="bc6aa-225">Klicken Sie mit der rechten Maustaste auf die Datei, und wählen Sie **Eigenschaften** aus.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-225">Right-click the file and select **Properties**.</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="bc6aa-226">Legen Sie **ContentType-** fest, und klicken Sie auf die Schaltfläche **Speichern**</span><span class="sxs-lookup"><span data-stu-id="bc6aa-226">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="bc6aa-227">Weitere Informationen finden Sie unter [Hosten von statischen Websites in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="bc6aa-227">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span> <span data-ttu-id="bc6aa-228">Nginx</span><span class="sxs-lookup"><span data-stu-id="bc6aa-228">Nginx</span></span>

<span data-ttu-id="bc6aa-229">Die folgende *nginx.conf*-Datei wurde vereinfacht, um zu zeigen, wie Nginx so konfiguriert wird, dass die Datei *Index.html* gesendet wird, wenn auf der Festplatte keine entsprechende Datei gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-229">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="bc6aa-230">Weitere Informationen zur Nginx-Webserverkonfiguration für die Produktion finden Sie unter [Creating NGINX Plus and NGINX Configuration Files (Erstellen von Konfigurationsdateien für NGINX Plus und NGINX)](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="bc6aa-230">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

<span data-ttu-id="bc6aa-231">Nginx in Docker</span><span class="sxs-lookup"><span data-stu-id="bc6aa-231">Nginx in Docker</span></span>

1. <span data-ttu-id="bc6aa-232">Richten Sie zum Hosten von Blazor in Docker mithilfe von NGINX das Dockerfile für die Verwendung des auf Alpine basierenden NGINX-Images ein.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-232">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="bc6aa-233">Aktualisieren Sie die Dockerfile zum Kopieren der Datei *nginx.config* in den Container.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-233">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. <span data-ttu-id="bc6aa-234">Fügen Sie der Dockerfile eine Zeile hinzu, wie im folgenden Beispiel dargestellt:</span><span class="sxs-lookup"><span data-stu-id="bc6aa-234">Add one line to the Dockerfile, as shown in the following example:</span></span>

1. <span data-ttu-id="bc6aa-235">Apache</span><span class="sxs-lookup"><span data-stu-id="bc6aa-235">Apache</span></span>

1. <span data-ttu-id="bc6aa-236">So stellen Sie eine Blazor WebAssembly-App für CentOS 7 oder höher bereit</span><span class="sxs-lookup"><span data-stu-id="bc6aa-236">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

<span data-ttu-id="bc6aa-237">Erstellen Sie die Apache-Konfigurationsdatei.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-237">Create the Apache configuration file.</span></span>

### <a name="github-pages"></a><span data-ttu-id="bc6aa-238">Im folgenden Beispiel wird eine vereinfachte Konfigurationsdatei veranschaulicht (*blazorapp.config*):</span><span class="sxs-lookup"><span data-stu-id="bc6aa-238">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

<span data-ttu-id="bc6aa-239">Platzieren Sie die Apache-Konfigurationsdatei in das `/etc/httpd/conf.d/`-Verzeichnis, das das standardmäßige Apache-Konfigurationsverzeichnis in CentOS 7 ist.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-239">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span> <span data-ttu-id="bc6aa-240">Platzieren Sie die Dateien der App in das `/var/www/blazorapp`-Verzeichnis (den Speicherort, der für `DocumentRoot` in der Konfigurationsdatei angegeben ist).</span><span class="sxs-lookup"><span data-stu-id="bc6aa-240">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span> <span data-ttu-id="bc6aa-241">Starten Sie den Apache-Dienst neu.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-241">Restart the Apache service.</span></span>

<span data-ttu-id="bc6aa-242">Weitere Informationen finden Sie unter [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) und [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="bc6aa-242">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span> <span data-ttu-id="bc6aa-243">GitHub-Seiten</span><span class="sxs-lookup"><span data-stu-id="bc6aa-243">GitHub Pages</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="bc6aa-244">Fügen Sie zum Verarbeiten von URL-Umschreibungen eine *404.html*-Datei mit einem Skript hinzu, mit dem die Umleitung der Anforderung an die Seite *index.html* verarbeitet wird.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-244">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span>

<span data-ttu-id="bc6aa-245">Ein Beispiel für eine Implementierung, das von der Community bereitgestellt wurde, finden Sie unter [Single Page Apps for GitHub Pages (Single-Page-Apps für GitHub Pages)](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages auf GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="bc6aa-245">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span>

### <a name="content-root"></a><span data-ttu-id="bc6aa-246">Ein Beispiel für die Verwendung des Community-Ansatzes finden Sie unter [blazor-demo/blazor-demo.github.io auf GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([Livewebsite](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="bc6aa-246">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="bc6aa-247">Wenn Sie anstelle einer Organisationswebsite eine Projektwebsite verwenden, fügen Sie der Datei *index.html* das `<base>`-Tag hinzu, oder aktualisieren Sie das Tag in der Datei.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-247">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="bc6aa-248">Legen Sie den Wert des `href`-Attributs auf den Namen des GitHub-Repositorys mit einem nachfolgenden Slash fest (z.B. `my-repository/`).</span><span class="sxs-lookup"><span data-stu-id="bc6aa-248">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

* <span data-ttu-id="bc6aa-249">Hostkonfigurationswerte</span><span class="sxs-lookup"><span data-stu-id="bc6aa-249">Host configuration values</span></span> <span data-ttu-id="bc6aa-250">[Blazor WebAssembly-Apps](xref:blazor/hosting-models#blazor-webassembly) können die folgenden Hostkonfigurationswerte als Befehlszeilenargumente zur Laufzeit in der Entwicklungsumgebung akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-250">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="bc6aa-251">Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="bc6aa-251">Content root</span></span> <span data-ttu-id="bc6aa-252">Mit dem Argument `--contentroot` wird der absolute Pfad zum Verzeichnis festgelegt, das die Inhaltsdateien (das [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root)) der App enthält.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-252">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="bc6aa-253">In den folgenden Beispielen ist `/content-root-path` der Stammpfad für Inhalte der App.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-253">In the following examples, `/content-root-path` is the app's content root path.</span></span> <span data-ttu-id="bc6aa-254">Das Argument wird beim lokalen Ausführen der App bei einer Eingabeaufforderung übergeben.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-254">Pass the argument when running the app locally at a command prompt.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="bc6aa-255">Führen Sie den folgenden Befehl über das Verzeichnis der App aus:</span><span class="sxs-lookup"><span data-stu-id="bc6aa-255">From the app's directory, execute:</span></span>

<span data-ttu-id="bc6aa-256">Fügen Sie der Datei *launchSettings.json* der App im **IIS Express**-Profil einen Eintrag hinzu.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-256">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="bc6aa-257">Diese Einstellung wird verwendet, wenn die Anwendung mit dem Visual Studio-Debugger und an einer Eingabeaufforderung mit `dotnet run` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-257">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span> <span data-ttu-id="bc6aa-258">Geben Sie in Visual Studio das Argument in **Eigenschaften** > **Debuggen** > **Anwendungsargumente** ein.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-258">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="bc6aa-259">Wenn Sie das Argument auf der Visual Studio-Eigenschaftenseite festlegen, wird das Argument der Datei *launchSettings.json* hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-259">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span> <span data-ttu-id="bc6aa-260">Basispfad</span><span class="sxs-lookup"><span data-stu-id="bc6aa-260">Path base</span></span>

* <span data-ttu-id="bc6aa-261">Mit dem Argument `--pathbase` wird der Basispfad für eine App festgelegt, die lokal mit einem relativen URL-Pfad ausgeführt wird, der sich nicht im Stammverzeichnis befindet (d. h. das `<base>`-Tag `href` wird für das Staging und die Produktion auf einen anderen Pfad als `/` festgelegt).</span><span class="sxs-lookup"><span data-stu-id="bc6aa-261">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="bc6aa-262">In den folgenden Beispielen ist `/relative-URL-path` die Pfadbasis der App.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-262">In the following examples, `/relative-URL-path` is the app's path base.</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="bc6aa-263">Weitere Informationen finden Sie unter [Basispfad einer App](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="bc6aa-263">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="bc6aa-264">Im Gegensatz zu dem Pfad, der für `href` des `<base>`-Tags bereitgestellt wird, wird beim Übergeben des `--pathbase`-Argumentwerts kein Schrägstrich (`/`) nachgestellt.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-264">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="bc6aa-265">Wenn der Basispfad einer App im `<base>`-Tag als `<base href="/CoolApp/">` (mit nachgestelltem Schrägstrich) bereitgestellt wird, wird der Argumentwert in der Befehlszeile als `--pathbase=/CoolApp` (ohne nachgestellten Schrägstrich) übergeben.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-265">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span> <span data-ttu-id="bc6aa-266">Das Argument wird beim lokalen Ausführen der App bei einer Eingabeaufforderung übergeben.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-266">Pass the argument when running the app locally at a command prompt.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="bc6aa-267">Führen Sie den folgenden Befehl über das Verzeichnis der App aus:</span><span class="sxs-lookup"><span data-stu-id="bc6aa-267">From the app's directory, execute:</span></span>

<span data-ttu-id="bc6aa-268">Fügen Sie der Datei *launchSettings.json* der App im **IIS Express**-Profil einen Eintrag hinzu.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-268">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span>

* <span data-ttu-id="bc6aa-269">Diese Einstellung wird verwendet, wenn die Anwendung mit dem Visual Studio-Debugger und an einer Eingabeaufforderung mit `dotnet run` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-269">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span> <span data-ttu-id="bc6aa-270">Geben Sie in Visual Studio das Argument in **Eigenschaften** > **Debuggen** > **Anwendungsargumente** ein.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-270">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="bc6aa-271">Wenn Sie das Argument auf der Visual Studio-Eigenschaftenseite festlegen, wird das Argument der Datei *launchSettings.json* hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-271">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span> <span data-ttu-id="bc6aa-272">URLs</span><span class="sxs-lookup"><span data-stu-id="bc6aa-272">URLs</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="bc6aa-273">Mit dem Argument `--urls` werden die IP-oder Hostadressen mit Ports und Protokollen festgelegt, die auf Anforderungen lauschen sollen.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-273">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span> <span data-ttu-id="bc6aa-274">Das Argument wird beim lokalen Ausführen der App bei einer Eingabeaufforderung übergeben.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-274">Pass the argument when running the app locally at a command prompt.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="bc6aa-275">Führen Sie den folgenden Befehl über das Verzeichnis der App aus:</span><span class="sxs-lookup"><span data-stu-id="bc6aa-275">From the app's directory, execute:</span></span>

<span data-ttu-id="bc6aa-276">Fügen Sie der Datei *launchSettings.json* der App im **IIS Express**-Profil einen Eintrag hinzu.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-276">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="bc6aa-277">Diese Einstellung wird verwendet, wenn die Anwendung mit dem Visual Studio-Debugger und an einer Eingabeaufforderung mit `dotnet run` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-277">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="bc6aa-278">Geben Sie in Visual Studio das Argument in **Eigenschaften** > **Debuggen** > **Anwendungsargumente** ein.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-278">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span>

<span data-ttu-id="bc6aa-279">Wenn Sie das Argument auf der Visual Studio-Eigenschaftenseite festlegen, wird das Argument der Datei *launchSettings.json* hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-279">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span> <span data-ttu-id="bc6aa-280">Konfigurieren des Linkers</span><span class="sxs-lookup"><span data-stu-id="bc6aa-280">Configure the Linker</span></span>

* Blazor<span data-ttu-id="bc6aa-281"> führt auf jedem Releasebuild eine IL-Verknüpfung (Intermediate Language, Zwischensprache) durch, um nicht benötigte Zwischensprache aus den Ausgabeassemblys zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-281"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span>
* <span data-ttu-id="bc6aa-282">Weitere Informationen finden Sie unter <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-282">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>
* <span data-ttu-id="bc6aa-283">Benutzerdefiniertes Laden von Startressourcen</span><span class="sxs-lookup"><span data-stu-id="bc6aa-283">Custom boot resource loading</span></span>

<span data-ttu-id="bc6aa-284">Eine Blazor WebAssembly-App kann mit der Funktion `loadBootResource` initialisiert werden, um den integrierten Mechanismus zum Laden von Startressourcen zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-284">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span>

| <span data-ttu-id="bc6aa-285">Verwenden Sie `loadBootResource` für die folgenden Szenarien:</span><span class="sxs-lookup"><span data-stu-id="bc6aa-285">Use `loadBootResource` for the following scenarios:</span></span>    | <span data-ttu-id="bc6aa-286">Benutzern erlauben, statische Ressourcen wie Zeitzonendaten oder *dotnet.wasm* aus einem CDN zu laden.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-286">Allow users to load static resources, such as timezone data or *dotnet.wasm* from a CDN.</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="bc6aa-287">Komprimierte Assemblys über eine HTTP-Anforderung laden und auf dem Client für Hosts dekomprimieren, die das Abrufen komprimierter Inhalte vom Server nicht unterstützen.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-287">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span> <span data-ttu-id="bc6aa-288">Ressourcen per Alias an einen anderen Namen umleiten, indem jede `fetch`-Anforderung an einen neuen Namen umgeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-288">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span> |
| `name`       | <span data-ttu-id="bc6aa-289">`loadBootResource`-Parameter sind in der folgenden Tabelle aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-289">`loadBootResource` parameters appear in the following table.</span></span> |
| `defaultUri` | <span data-ttu-id="bc6aa-290">Parameter</span><span class="sxs-lookup"><span data-stu-id="bc6aa-290">Parameter</span></span> |
| `integrity`  | <span data-ttu-id="bc6aa-291">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="bc6aa-291">Description</span></span> |

<span data-ttu-id="bc6aa-292">Der Typ der Ressource.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-292">The type of the resource.</span></span>

* <span data-ttu-id="bc6aa-293">Zulässige Typen: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span><span class="sxs-lookup"><span data-stu-id="bc6aa-293">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> <span data-ttu-id="bc6aa-294">Der Name der Ressource.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-294">The name of the resource.</span></span>

  * <span data-ttu-id="bc6aa-295">Der relative oder absolute URI der Ressource.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-295">The relative or absolute URI of the resource.</span></span>
  * <span data-ttu-id="bc6aa-296">Die Integritätszeichenfolge, die den erwarteten Inhalt in der Antwort darstellt.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-296">The integrity string representing the expected content in the response.</span></span>
  * <span data-ttu-id="bc6aa-297">`loadBootResource` gibt eines der folgenden Elemente zurück, um den Ladevorgang außer Kraft zu setzen:</span><span class="sxs-lookup"><span data-stu-id="bc6aa-297">`loadBootResource` returns any of the following to override the loading process:</span></span>

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* <span data-ttu-id="bc6aa-298">URI-Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-298">URI string.</span></span> <span data-ttu-id="bc6aa-299">Im folgenden Beispiel (*wwwroot/Index.html*) werden die folgenden Dateien von einem CDN unter `https://my-awesome-cdn.com/` bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="bc6aa-299">In the following example (*wwwroot/index.html*), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  <span data-ttu-id="bc6aa-300">*dotnet.\*.js*</span><span class="sxs-lookup"><span data-stu-id="bc6aa-300">*dotnet.\*.js*</span></span>
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* <span data-ttu-id="bc6aa-301">*dotnet.wasm*</span><span class="sxs-lookup"><span data-stu-id="bc6aa-301">*dotnet.wasm*</span></span>

<span data-ttu-id="bc6aa-302">Zeitzonendaten</span><span class="sxs-lookup"><span data-stu-id="bc6aa-302">Timezone data</span></span> <span data-ttu-id="bc6aa-303">`Promise<Response>`</span><span class="sxs-lookup"><span data-stu-id="bc6aa-303">`Promise<Response>`.</span></span>

<span data-ttu-id="bc6aa-304">Übergeben Sie den Parameter `integrity` in einem Header, um das Standardverhalten bei der Integritätsprüfung beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-304">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span> <span data-ttu-id="bc6aa-305">Das folgende Beispiel (*wwwwroot/index.html*) fügt einen benutzerdefinierten HTTP-Header den ausgehenden Anforderungen hinzu und übergibt den Parameter `integrity` an den Aufruf von `fetch`:</span><span class="sxs-lookup"><span data-stu-id="bc6aa-305">The following example (*wwwroot/index.html*) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="bc6aa-306">`null`/`undefined`, was zum standardmäßigen Ladeverhalten führt.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-306">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="bc6aa-307">Externe Quellen müssen die erforderlichen CORS-Header für Browser zurückgeben, um das ursprungsübergreifende Laden von Ressourcen zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-307">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span>

<span data-ttu-id="bc6aa-308">CDNs stellen in der Regel die erforderlichen Header standardmäßig zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-308">CDNs usually provide the required headers by default.</span></span> <span data-ttu-id="bc6aa-309">Sie müssen nur für benutzerdefinierte Verhaltensmuster Typen angeben.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-309">You only need to specify types for custom behaviors.</span></span>

<span data-ttu-id="bc6aa-310">Nicht mit `loadBootResource` angegebene Typen werden vom Framework entsprechend ihres Standardladeverhaltens geladen.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-310">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

<span data-ttu-id="bc6aa-311">Ändern der Dateinamenerweiterung von DLL-Dateien</span><span class="sxs-lookup"><span data-stu-id="bc6aa-311">Change the filename extension of DLL files</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="bc6aa-312">Falls Sie die Dateinamenerweiterungen der veröffentlichten *DLL*-Dateien der App ändern müssen, befolgen Sie die Anleitung in diesem Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-312">In case you have a need to change the filename extensions of the app's published *.dll* files, follow the guidance in this section.</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="bc6aa-313">Verwenden Sie nach Veröffentlichen der App ein Shellskript oder eine DevOps-Buildpipeline, um *DLL*-Dateien umzubenennen und eine andere Dateierweiterung zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-313">After publishing the app, use a shell script or DevOps build pipeline to rename *.dll* files to use a different file extension.</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="bc6aa-314">Wählen Sie die *DLL*-Dateien im Verzeichnis *wwwwroot* der veröffentlichten Ausgabe der App aus (Beispiel: *{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot*).</span><span class="sxs-lookup"><span data-stu-id="bc6aa-314">Target the *.dll* files in the *wwwroot* directory of the app's published output (for example, *{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot*).</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="bc6aa-315">In den folgenden Beispielen werden *DLL*-Dateien so umbenannt, dass die Dateierweiterung *.bin* verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-315">In the following examples, *.dll* files are renamed to use the *.bin* file extension.</span></span>

<span data-ttu-id="bc6aa-316">Unter Windows:</span><span class="sxs-lookup"><span data-stu-id="bc6aa-316">On Windows:</span></span>

* <span data-ttu-id="bc6aa-317">Wenn auch Service Worker-Assets verwendet werden, fügen Sie den folgenden Befehl hinzu:</span><span class="sxs-lookup"><span data-stu-id="bc6aa-317">If service worker assets are also in use, add the following command:</span></span> <span data-ttu-id="bc6aa-318">Unter Linux oder macOS:</span><span class="sxs-lookup"><span data-stu-id="bc6aa-318">On Linux or macOS:</span></span>
* <span data-ttu-id="bc6aa-319">Wenn auch Service Worker-Assets verwendet werden, fügen Sie den folgenden Befehl hinzu:</span><span class="sxs-lookup"><span data-stu-id="bc6aa-319">If service worker assets are also in use, add the following command:</span></span>

<span data-ttu-id="bc6aa-320">Um eine andere Dateierweiterung als *.bin* zu verwenden, ersetzen Sie *.bin* in den vorherigen Befehlen.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-320">To use a different file extension than *.bin*, replace *.bin* in the preceding commands.</span></span> <span data-ttu-id="bc6aa-321">Wählen Sie für die komprimierten Dateien *blazor.boot.json.gz* und *blazor.boot.json.br* einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="bc6aa-321">To address the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files, adopt either of the following approaches:</span></span> <span data-ttu-id="bc6aa-322">Entfernen Sie die komprimierten Dateien *blazor.boot.json.gz* und *blazor.boot.json.br*.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-322">Remove the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files.</span></span>

<span data-ttu-id="bc6aa-323">Bei diesem Ansatz ist die Komprimierung deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-323">Compression is disabled with this approach.</span></span>

<span data-ttu-id="bc6aa-324">Komprimieren Sie die aktualisierte Datei *blazor.boot.json* erneut.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-324">Recompress the updated *blazor.boot.json* file.</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="bc6aa-325">Die vorherige Anleitung gilt auch, wenn Service Worker-Assets verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-325">The preceding guidance also applies when service worker assets are in use.</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="bc6aa-326">Entfernen Sie *wwwroot/service-worker-assets.js.br* und *wwwroot/service-worker-assets.js.gz*, oder komprimieren Sie sie erneut.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-326">Remove or recompress *wwwroot/service-worker-assets.js.br* and *wwwroot/service-worker-assets.js.gz*.</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="bc6aa-327">Andernfalls treten bei Dateiintegritätsprüfungen im Browser Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="bc6aa-327">Otherwise, file integrity checks fail in the browser.</span></span>
 

---
title: Clientseitiges Hosten und Bereitstellen von ASP.NET WebAssembly
author: guardrex
description: Erfahren Sie, wie Sie eine Blazor-App mithilfe von ASP.NET Core, Content Delivery Network (CDN), Dateiservern und GitHub-Seiten hosten und bereitstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: 943dbb772d9a7bcb337012c126828d1ab4eb545c
ms.sourcegitcommit: 383017d7060a6d58f6a79cf4d7335d5b4b6c5659
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72816065"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="37dfb-103">Clientseitiges Hosten und Bereitstellen von ASP.NET WebAssembly</span><span class="sxs-lookup"><span data-stu-id="37dfb-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="37dfb-104">Von [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="37dfb-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="37dfb-105">Mit dem [Blazor WebAssembly-Hostingmodell](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="37dfb-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="37dfb-106">Die Blazor-App, die jeweiligen Abhängigkeiten und die .NET-Runtime werden im Browser herunterladen.</span><span class="sxs-lookup"><span data-stu-id="37dfb-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="37dfb-107">Die App wird direkt im UI-Thread des Browsers ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="37dfb-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="37dfb-108">Die folgenden Bereitstellungsstrategien werden unterstützt:</span><span class="sxs-lookup"><span data-stu-id="37dfb-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="37dfb-109">Die Blazor-App wird von einer ASP.NET Core-App unterstützt.</span><span class="sxs-lookup"><span data-stu-id="37dfb-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="37dfb-110">Diese Strategie wird im Abschnitt [Gehostete Bereitstellung mit ASP.NET Core](#hosted-deployment-with-aspnet-core) behandelt.</span><span class="sxs-lookup"><span data-stu-id="37dfb-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="37dfb-111">Die Blazor-App wird auf einem statischen Hosting-Webserver oder -Webservice abgelegt, auf dem .NET nicht zur Unterstützung der Blazor-App verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="37dfb-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="37dfb-112">Diese Strategie wird im Abschnitt [Eigenständige Bereitstellung](#standalone-deployment) behandelt, der Informationen zum Hosten einer Blazor WebAssembly-App als IIS-untergeordnete App enthält.</span><span class="sxs-lookup"><span data-stu-id="37dfb-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="37dfb-113">Erneutes Generieren von URLs für ein ordnungsgemäßes Routing</span><span class="sxs-lookup"><span data-stu-id="37dfb-113">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="37dfb-114">Routinganforderungen für Seitenkomponenten in einer Blazor WebAssembly-App sind nicht so unkompliziert wie Routinganforderungen in einer gehosteten Blazor Server-App.</span><span class="sxs-lookup"><span data-stu-id="37dfb-114">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="37dfb-115">Stellen Sie sich eine Blazor WebAssembly-App mit zwei Komponenten vor:</span><span class="sxs-lookup"><span data-stu-id="37dfb-115">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="37dfb-116">*Main.razor*: Wird im Stammverzeichnis der App geladen und enthält einen Link zur `About`-Komponente (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="37dfb-116">*Main.razor* &ndash; Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="37dfb-117">*About.razor*: `About`-Komponente.</span><span class="sxs-lookup"><span data-stu-id="37dfb-117">*About.razor* &ndash; `About` component.</span></span>

<span data-ttu-id="37dfb-118">Wenn das Standarddokument der App über die Adressleiste des Browsers (z. B. `https://www.contoso.com/`) angefordert wird, geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="37dfb-118">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="37dfb-119">Der Browser sendet eine Anforderung.</span><span class="sxs-lookup"><span data-stu-id="37dfb-119">The browser makes a request.</span></span>
1. <span data-ttu-id="37dfb-120">Die Standardseite wird zurückgegeben, in der Regel *index.html*.</span><span class="sxs-lookup"><span data-stu-id="37dfb-120">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="37dfb-121">*index.html* startet die App.</span><span class="sxs-lookup"><span data-stu-id="37dfb-121">*index.html* bootstraps the app.</span></span>
1. <span data-ttu-id="37dfb-122">Der Blazor-Router lädt, und die Razor-Komponente `Main` wird gerendert.</span><span class="sxs-lookup"><span data-stu-id="37dfb-122">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="37dfb-123">Auf der Seite „Main“ kann der Link zur `About`-Komponente auf dem Client ausgewählt werden, weil der Blazor-Router dafür sorgt, dass der Browser im Internet keine Anforderung für `About` an `www.contoso.com` sendet, und stattdessen die gerenderte `About`-Komponente selbst bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="37dfb-123">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="37dfb-124">Alle Anforderungen von internen Endpunkten *innerhalb der Blazor WebAssembly-App* funktionieren auf dieselbe Weise: Durch Anforderungen werden keine browserbasierten Anforderungen an serverseitig gehostete Ressourcen im Internet ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="37dfb-124">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="37dfb-125">Der Router verarbeitet Anforderungen intern.</span><span class="sxs-lookup"><span data-stu-id="37dfb-125">The router handles the requests internally.</span></span>

<span data-ttu-id="37dfb-126">Wenn eine Anforderung für `www.contoso.com/About` über die Adressleiste des Browsers gesendet wird, tritt bei der Anforderung ein Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="37dfb-126">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="37dfb-127">Diese Ressource ist im Internethost der App nicht vorhanden. Daher wird die Antwort *404 Nicht gefunden* zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="37dfb-127">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="37dfb-128">Da Browser Anforderungen für clientseitige Seiten an internetbasierte Hosts senden, müssen Webserver und Hostingdienste alle Anforderungen für Ressourcen, die sich nicht physisch auf dem Server befinden, auf der Seite *index.html* erneut generieren.</span><span class="sxs-lookup"><span data-stu-id="37dfb-128">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="37dfb-129">Wenn *index.html* zurückgegeben wird, übernimmt der Blazor-Router der App und antwortet mit der richtigen Ressource.</span><span class="sxs-lookup"><span data-stu-id="37dfb-129">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="37dfb-130">Wenn Sie die Bereitstellung auf einem IIS-Server durchführen, können Sie das URL-Rewrite-Modul mit der veröffentlichten *web.config*-Datei der App verwenden.</span><span class="sxs-lookup"><span data-stu-id="37dfb-130">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="37dfb-131">Weitere Informationen finden Sie im Abschnitt [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="37dfb-131">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="37dfb-132">Gehostete Bereitstellung mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="37dfb-132">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="37dfb-133">Mit einer *gehosteten Bereitstellung* wird die Blazor WebAssembly-App über eine auf einem Webserver ausgeführte [ASP.NET Core-App](xref:index) für Browser bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="37dfb-133">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="37dfb-134">Die Blazor-App ist mit der ASP.NET Core-App in der veröffentlichen Ausgabe enthalten, sodass die beiden Apps zusammen bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="37dfb-134">The Blazor app is included with the ASP.NET Core app in the published output so that the two apps are deployed together.</span></span> <span data-ttu-id="37dfb-135">Hierfür wird ein Webserver benötigt, auf dem eine ASP.NET Core-App gehostet werden kann.</span><span class="sxs-lookup"><span data-stu-id="37dfb-135">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="37dfb-136">Bei einer gehosteten Bereitstellung enthält Visual Studio die **Blazor WebAssembly-App**-Projektvorlage (`blazorwasm`-Vorlage bei Verwendung des Befehls [dotnet new](/dotnet/core/tools/dotnet-new)) mit der ausgewählten Option **Hosted**.</span><span class="sxs-lookup"><span data-stu-id="37dfb-136">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected.</span></span>

<span data-ttu-id="37dfb-137">Weitere Informationen zum Hosten und Bereitstellen von ASP.NET Core-Apps finden Sie unter <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="37dfb-137">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="37dfb-138">Informationen zum Bereitstellen für Azure App Service finden Sie unter <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="37dfb-138">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="37dfb-139">Eigenständige Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="37dfb-139">Standalone deployment</span></span>

<span data-ttu-id="37dfb-140">Bei einer *eigenständigen Bereitstellung* wird die Blazor WebAssembly-App in Form von statischen Dateien bereitgestellt, die von Clients direkt angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="37dfb-140">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="37dfb-141">Jeder statische Dateiserver kann die Blazor-App bedienen.</span><span class="sxs-lookup"><span data-stu-id="37dfb-141">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="37dfb-142">Eigenständige Bereitstellungsobjekte werden im Ordner */bin/Release/{ZIELFRAMEWORK}/publish/{ASSEMBLYNAME}/dist* veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="37dfb-142">Standalone deployment assets are published to the *bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* folder.</span></span>

### <a name="iis"></a><span data-ttu-id="37dfb-143">IIS</span><span class="sxs-lookup"><span data-stu-id="37dfb-143">IIS</span></span>

<span data-ttu-id="37dfb-144">IIS ist ein leistungsfähiger Server für statische Dateien für Blazor-Apps.</span><span class="sxs-lookup"><span data-stu-id="37dfb-144">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="37dfb-145">Informationen zum Konfigurieren von IIS zum Hosten von Blazor finden Sie unter [Build a Static Website on IIS (Erstellen einer statischen Website unter IIS)](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="37dfb-145">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="37dfb-146">Veröffentlichte Objekte werden im Ordner */bin/Release/{Zielframework}/publish* erstellt.</span><span class="sxs-lookup"><span data-stu-id="37dfb-146">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="37dfb-147">Die Inhalte des Ordners *publish* werden auf dem Webserver oder über den Hostingdienst gehostet.</span><span class="sxs-lookup"><span data-stu-id="37dfb-147">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="37dfb-148">web.config</span><span class="sxs-lookup"><span data-stu-id="37dfb-148">web.config</span></span>

<span data-ttu-id="37dfb-149">Beim Veröffentlichen eines Blazor-Projekts wird eine *web.config*-Datei mit der folgenden IIS-Konfiguration erstellt:</span><span class="sxs-lookup"><span data-stu-id="37dfb-149">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="37dfb-150">Für die folgenden Dateierweiterungen werden MIME-Typen festgelegt:</span><span class="sxs-lookup"><span data-stu-id="37dfb-150">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="37dfb-151">*.dll* &ndash; `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="37dfb-151">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="37dfb-152">*.json* &ndash; `application/json`</span><span class="sxs-lookup"><span data-stu-id="37dfb-152">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="37dfb-153">*.wasm* &ndash; `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="37dfb-153">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="37dfb-154">*.woff* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="37dfb-154">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="37dfb-155">*.woff2* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="37dfb-155">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="37dfb-156">Für die folgenden MIME-Typen wird die HTTP-Komprimierung aktiviert:</span><span class="sxs-lookup"><span data-stu-id="37dfb-156">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="37dfb-157">URL-Rewrite-Modul-Regeln werden eingerichtet:</span><span class="sxs-lookup"><span data-stu-id="37dfb-157">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="37dfb-158">Stellen Sie das Unterverzeichnis bereit, in dem sich die statischen Objekte der App befinden ( *{ASSEMBLYNAME}/dist/{ANGEFORDERTER PFAD}* ).</span><span class="sxs-lookup"><span data-stu-id="37dfb-158">Serve the sub-directory where the app's static assets reside (*{ASSEMBLY NAME}/dist/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="37dfb-159">Richten Sie ein SPA-Fallbackrouting ein, sodass Anforderungen für nicht dateibasierte Objekte an das Standarddokument der App im entsprechenden Ordner für statische Objekte ( *{ASSEMBLYNAME}/dist/index.html*) umgeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="37dfb-159">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*{ASSEMBLY NAME}/dist/index.html*).</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="37dfb-160">Installieren des URL-Rewrite-Moduls</span><span class="sxs-lookup"><span data-stu-id="37dfb-160">Install the URL Rewrite Module</span></span>

<span data-ttu-id="37dfb-161">Das [URL-Rewrite-Modul](https://www.iis.net/downloads/microsoft/url-rewrite) wird zum Umschreiben von URLs benötigt.</span><span class="sxs-lookup"><span data-stu-id="37dfb-161">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="37dfb-162">Das Modul wird nicht standardmäßig installiert und ist für die Installation als Webserver (IIS)-Rollendienstfunktion nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="37dfb-162">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="37dfb-163">Das Modul muss von der IIS-Website heruntergeladen werden.</span><span class="sxs-lookup"><span data-stu-id="37dfb-163">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="37dfb-164">Verwenden Sie den Webplattform-Installer zur Installation des Moduls:</span><span class="sxs-lookup"><span data-stu-id="37dfb-164">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="37dfb-165">Navigieren Sie lokal zur [URL-Rewrite-Module-Downloadseite](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="37dfb-165">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="37dfb-166">Wählen Sie zum Herunterladen der englischen Version des WebPI-Installers **WebPI** aus.</span><span class="sxs-lookup"><span data-stu-id="37dfb-166">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="37dfb-167">Wählen Sie zum Herunterladen des Installers in einer anderen Sprache die entsprechende Architektur für den Server (x86/x64) aus.</span><span class="sxs-lookup"><span data-stu-id="37dfb-167">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="37dfb-168">Kopieren Sie den Installer auf den Server.</span><span class="sxs-lookup"><span data-stu-id="37dfb-168">Copy the installer to the server.</span></span> <span data-ttu-id="37dfb-169">Führen Sie den Installer aus.</span><span class="sxs-lookup"><span data-stu-id="37dfb-169">Run the installer.</span></span> <span data-ttu-id="37dfb-170">Klicken Sie auf die Schaltfläche **Installieren**, und stimmen Sie den Lizenzbedingungen zu.</span><span class="sxs-lookup"><span data-stu-id="37dfb-170">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="37dfb-171">Der Server muss nach Abschluss der Installation nicht neu gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="37dfb-171">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="37dfb-172">Konfigurieren der Website</span><span class="sxs-lookup"><span data-stu-id="37dfb-172">Configure the website</span></span>

<span data-ttu-id="37dfb-173">Legen Sie für den **physischen Pfad** der Website den Ordner der App fest.</span><span class="sxs-lookup"><span data-stu-id="37dfb-173">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="37dfb-174">Der Ordner enthält Folgendes:</span><span class="sxs-lookup"><span data-stu-id="37dfb-174">The folder contains:</span></span>

* <span data-ttu-id="37dfb-175">Die Datei *web.config*, die von IIS zum Konfigurieren der Website verwendet wird, mit den erforderlichen Umleitungsregeln und Dateiinhaltstypen</span><span class="sxs-lookup"><span data-stu-id="37dfb-175">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="37dfb-176">Den Ordner der App für statische Objekte</span><span class="sxs-lookup"><span data-stu-id="37dfb-176">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="37dfb-177">Hosten als IIS-untergeordnete App</span><span class="sxs-lookup"><span data-stu-id="37dfb-177">Host as an IIS sub-app</span></span>

<span data-ttu-id="37dfb-178">Wenn eine eigenständige App als IIS-untergeordnete App gehostet wird, führen Sie einen der folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="37dfb-178">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="37dfb-179">Deaktivieren Sie den vererbten ASP.NET Core Module-Handler.</span><span class="sxs-lookup"><span data-stu-id="37dfb-179">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="37dfb-180">Entfernen Sie den Handler in der veröffentlichen *web.config*-Datei der Blazor-App, indem Sie der Datei einen `<handlers>`-Abschnitt hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="37dfb-180">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="37dfb-181">Deaktivieren Sie die Vererbung des `<system.webServer>`-Abschnitts der Stammanwendung (d. h. der übergeordneten Anwendung), indem Sie ein `<location>`-Element verwenden, bei dem `inheritInChildApplications` auf `false` festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="37dfb-181">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="37dfb-182">Das Entfernen des Handlers bzw. das Deaktivieren der Vererbung wird zusätzlich zur [Konfiguration des Basispfads der App](xref:host-and-deploy/blazor/index#app-base-path) durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="37dfb-182">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="37dfb-183">Legen Sie den Basispfad der App in der *index.html*-Datei der App auf den IIS-Alias fest, der beim Konfigurieren der untergeordneten App in IIS verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="37dfb-183">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="37dfb-184">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="37dfb-184">Troubleshooting</span></span>

<span data-ttu-id="37dfb-185">Wenn die Fehlermeldung *500: Interner Serverfehler* angezeigt wird und IIS-Manager beim Zugriff auf die Konfiguration der Website eine Fehlermeldung anzeigt, überprüfen Sie, ob das URL-Rewrite-Modul installiert ist.</span><span class="sxs-lookup"><span data-stu-id="37dfb-185">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="37dfb-186">Wenn das Modul nicht installiert ist, kann die Datei *web.config* von IIS nicht analysiert werden.</span><span class="sxs-lookup"><span data-stu-id="37dfb-186">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="37dfb-187">Dadurch wird verhindert, dass die Konfiguration der Website von IIS-Manager geladen wird, und dass die statischen Blazor-Dateien auf der Website bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="37dfb-187">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="37dfb-188">Weitere Informationen zur Problembehandlung von Bereitstellungen für IIS finden Sie unter <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="37dfb-188">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="37dfb-189">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="37dfb-189">Azure Storage</span></span>

<span data-ttu-id="37dfb-190">Das Hosten statischer Dateien von [Azure Storage](/azure/storage/) ermöglicht das serverlose Blazor-App-Hosting.</span><span class="sxs-lookup"><span data-stu-id="37dfb-190">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="37dfb-191">Benutzerdefinierte Domänennamen, das Azure Content Delivery Network (CDN) und HTTPS werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="37dfb-191">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="37dfb-192">Wenn der Blobdienst für das Hosten von statischen Websites in einem Speicherkonto aktiviert ist, gehen Sie folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="37dfb-192">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="37dfb-193">Legen Sie den **Namen des Indexdokuments** auf `index.html` fest.</span><span class="sxs-lookup"><span data-stu-id="37dfb-193">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="37dfb-194">Legen Sie den **Pfad des Fehlerdokuments** auf `index.html` fest.</span><span class="sxs-lookup"><span data-stu-id="37dfb-194">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="37dfb-195">Razor-Komponenten und andere Endpunkte, bei denen es sich nicht um Dateien handelt, befinden sich nicht in physischen Pfaden in dem statischen Inhalt, der vom Blobdienst gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="37dfb-195">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="37dfb-196">Wenn eine Anforderung für eine dieser Ressourcen empfangen wird, die vom Blazor-Router verarbeitet werden soll, leitet der vom Blobdienst generierte Fehler *404 – Nicht gefunden* die Anforderung an den **Pfad des Fehlerdokuments** weiter.</span><span class="sxs-lookup"><span data-stu-id="37dfb-196">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="37dfb-197">Das Blob *index.html* wird zurückgegeben, und der Blazor-Router lädt und verarbeitet den Pfad.</span><span class="sxs-lookup"><span data-stu-id="37dfb-197">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="37dfb-198">Weitere Informationen finden Sie unter [Hosten von statischen Websites in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="37dfb-198">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="37dfb-199">Nginx</span><span class="sxs-lookup"><span data-stu-id="37dfb-199">Nginx</span></span>

<span data-ttu-id="37dfb-200">Die folgende *nginx.conf*-Datei wurde vereinfacht, um zu zeigen, wie Nginx so konfiguriert wird, dass die Datei *Index.html* gesendet wird, wenn auf der Festplatte keine entsprechende Datei gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="37dfb-200">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="37dfb-201">Weitere Informationen zur Nginx-Webserverkonfiguration für die Produktion finden Sie unter [Creating NGINX Plus and NGINX Configuration Files (Erstellen von Konfigurationsdateien für NGINX Plus und NGINX)](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="37dfb-201">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="37dfb-202">Nginx in Docker</span><span class="sxs-lookup"><span data-stu-id="37dfb-202">Nginx in Docker</span></span>

<span data-ttu-id="37dfb-203">Richten Sie zum Hosten von Blazor in Docker mithilfe von Nginx die Dockerfile für die Verwendung des auf Alpine basierenden Nginx-Images ein.</span><span class="sxs-lookup"><span data-stu-id="37dfb-203">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="37dfb-204">Aktualisieren Sie die Dockerfile zum Kopieren der Datei *nginx.config* in den Container.</span><span class="sxs-lookup"><span data-stu-id="37dfb-204">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="37dfb-205">Fügen Sie der Dockerfile eine Zeile hinzu, wie im folgenden Beispiel dargestellt:</span><span class="sxs-lookup"><span data-stu-id="37dfb-205">Add one line to the Dockerfile, as shown in the following example:</span></span>

```Dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="37dfb-206">Apache</span><span class="sxs-lookup"><span data-stu-id="37dfb-206">Apache</span></span>

<span data-ttu-id="37dfb-207">So stellen Sie eine Blazor-WebAssembly-App für CentOS 7 oder später bereit:</span><span class="sxs-lookup"><span data-stu-id="37dfb-207">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="37dfb-208">Erstellen Sie die Apache-Konfigurationsdatei.</span><span class="sxs-lookup"><span data-stu-id="37dfb-208">Create the Apache configuration file.</span></span> <span data-ttu-id="37dfb-209">Im folgenden Beispiel wird eine vereinfachte Konfigurationsdatei veranschaulicht (*blazorapp.config*):</span><span class="sxs-lookup"><span data-stu-id="37dfb-209">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType aplication/wasm .wasm
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

1. <span data-ttu-id="37dfb-210">Platzieren Sie die Apache-Konfigurationsdatei in das `/etc/httpd/conf.d/`-Verzeichnis, das das standardmäßige Apache-Konfigurationsverzeichnis in CentOS 7 ist.</span><span class="sxs-lookup"><span data-stu-id="37dfb-210">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="37dfb-211">Platzieren Sie die Dateien der App in das `/var/www/blazorapp`-Verzeichnis (den Speicherort, der für `DocumentRoot` in der Konfigurationsdatei angegeben ist).</span><span class="sxs-lookup"><span data-stu-id="37dfb-211">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="37dfb-212">Starten Sie den Apache-Dienst neu.</span><span class="sxs-lookup"><span data-stu-id="37dfb-212">Restart the Apache service.</span></span>

<span data-ttu-id="37dfb-213">Weitere Informationen finden Sie unter [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) und [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="37dfb-213">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="37dfb-214">GitHub-Seiten</span><span class="sxs-lookup"><span data-stu-id="37dfb-214">GitHub Pages</span></span>

<span data-ttu-id="37dfb-215">Fügen Sie zum Verarbeiten von URL-Umschreibungen eine *404.html*-Datei mit einem Skript hinzu, mit dem die Umleitung der Anforderung an die Seite *index.html* verarbeitet wird.</span><span class="sxs-lookup"><span data-stu-id="37dfb-215">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="37dfb-216">Ein Beispiel für eine Implementierung, das von der Community bereitgestellt wurde, finden Sie unter [Single Page Apps for GitHub Pages (Single-Page-Apps für GitHub Pages)](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages auf GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="37dfb-216">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="37dfb-217">Ein Beispiel für die Verwendung des Community-Ansatzes finden Sie unter [blazor-demo/blazor-demo.github.io auf GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([Livewebsite](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="37dfb-217">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="37dfb-218">Wenn Sie anstelle einer Organisationswebsite eine Projektwebsite verwenden, fügen Sie der Datei *index.html* das `<base>`-Tag hinzu, oder aktualisieren Sie das Tag in der Datei.</span><span class="sxs-lookup"><span data-stu-id="37dfb-218">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="37dfb-219">Legen Sie den Wert des `href`-Attributs auf den Namen des GitHub-Repositorys mit einem nachfolgenden Slash fest (z.B. `my-repository/`).</span><span class="sxs-lookup"><span data-stu-id="37dfb-219">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="37dfb-220">Hostkonfigurationswerte</span><span class="sxs-lookup"><span data-stu-id="37dfb-220">Host configuration values</span></span>

<span data-ttu-id="37dfb-221">[Blazor WebAssembly-Apps](xref:blazor/hosting-models#blazor-webassembly) können die folgenden Hostkonfigurationswerte als Befehlszeilenargumente zur Laufzeit in der Entwicklungsumgebung akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="37dfb-221">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="37dfb-222">Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="37dfb-222">Content root</span></span>

<span data-ttu-id="37dfb-223">Mit dem Argument `--contentroot` wird der absolute Pfad zum Verzeichnis festgelegt, das die Inhaltsdateien (das [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root)) der App enthält.</span><span class="sxs-lookup"><span data-stu-id="37dfb-223">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="37dfb-224">In den folgenden Beispielen ist `/content-root-path` der Stammpfad für Inhalte der App.</span><span class="sxs-lookup"><span data-stu-id="37dfb-224">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="37dfb-225">Das Argument wird beim lokalen Ausführen der App bei einer Eingabeaufforderung übergeben.</span><span class="sxs-lookup"><span data-stu-id="37dfb-225">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="37dfb-226">Führen Sie den folgenden Befehl über das Verzeichnis der App aus:</span><span class="sxs-lookup"><span data-stu-id="37dfb-226">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="37dfb-227">Fügen Sie der Datei *launchSettings.json* der App im **IIS Express**-Profil einen Eintrag hinzu.</span><span class="sxs-lookup"><span data-stu-id="37dfb-227">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="37dfb-228">Diese Einstellung wird verwendet, wenn die Anwendung mit dem Visual Studio-Debugger und an einer Eingabeaufforderung mit `dotnet run` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="37dfb-228">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="37dfb-229">Geben Sie in Visual Studio das Argument in **Eigenschaften** > **Debuggen** > **Anwendungsargumente** ein.</span><span class="sxs-lookup"><span data-stu-id="37dfb-229">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="37dfb-230">Wenn Sie das Argument auf der Visual Studio-Eigenschaftenseite festlegen, wird das Argument der Datei *launchSettings.json* hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="37dfb-230">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="37dfb-231">Basispfad</span><span class="sxs-lookup"><span data-stu-id="37dfb-231">Path base</span></span>

<span data-ttu-id="37dfb-232">Mit dem Argument `--pathbase` wird der Basispfad für eine App festgelegt, die lokal mit einem relativen URL-Pfad ausgeführt wird, der sich nicht im Stammverzeichnis befindet (d. h. das `<base>`-Tag `href` wird für das Staging und die Produktion auf einen anderen Pfad als `/` festgelegt).</span><span class="sxs-lookup"><span data-stu-id="37dfb-232">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="37dfb-233">In den folgenden Beispielen ist `/relative-URL-path` die Pfadbasis der App.</span><span class="sxs-lookup"><span data-stu-id="37dfb-233">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="37dfb-234">Weitere Informationen finden Sie unter [Basispfad einer App](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="37dfb-234">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="37dfb-235">Im Gegensatz zu dem Pfad, der für `href` des `<base>`-Tags bereitgestellt wird, wird beim Übergeben des `--pathbase`-Argumentwerts kein Schrägstrich (`/`) nachgestellt.</span><span class="sxs-lookup"><span data-stu-id="37dfb-235">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="37dfb-236">Wenn der Basispfad einer App im `<base>`-Tag als `<base href="/CoolApp/">` (mit nachgestelltem Schrägstrich) bereitgestellt wird, wird der Argumentwert in der Befehlszeile als `--pathbase=/CoolApp` (ohne nachgestellten Schrägstrich) übergeben.</span><span class="sxs-lookup"><span data-stu-id="37dfb-236">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="37dfb-237">Das Argument wird beim lokalen Ausführen der App bei einer Eingabeaufforderung übergeben.</span><span class="sxs-lookup"><span data-stu-id="37dfb-237">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="37dfb-238">Führen Sie den folgenden Befehl über das Verzeichnis der App aus:</span><span class="sxs-lookup"><span data-stu-id="37dfb-238">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="37dfb-239">Fügen Sie der Datei *launchSettings.json* der App im **IIS Express**-Profil einen Eintrag hinzu.</span><span class="sxs-lookup"><span data-stu-id="37dfb-239">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="37dfb-240">Diese Einstellung wird verwendet, wenn die Anwendung mit dem Visual Studio-Debugger und an einer Eingabeaufforderung mit `dotnet run` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="37dfb-240">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="37dfb-241">Geben Sie in Visual Studio das Argument in **Eigenschaften** > **Debuggen** > **Anwendungsargumente** ein.</span><span class="sxs-lookup"><span data-stu-id="37dfb-241">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="37dfb-242">Wenn Sie das Argument auf der Visual Studio-Eigenschaftenseite festlegen, wird das Argument der Datei *launchSettings.json* hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="37dfb-242">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="37dfb-243">URLs</span><span class="sxs-lookup"><span data-stu-id="37dfb-243">URLs</span></span>

<span data-ttu-id="37dfb-244">Mit dem Argument `--urls` werden die IP-oder Hostadressen mit Ports und Protokollen festgelegt, die auf Anforderungen lauschen sollen.</span><span class="sxs-lookup"><span data-stu-id="37dfb-244">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="37dfb-245">Das Argument wird beim lokalen Ausführen der App bei einer Eingabeaufforderung übergeben.</span><span class="sxs-lookup"><span data-stu-id="37dfb-245">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="37dfb-246">Führen Sie den folgenden Befehl über das Verzeichnis der App aus:</span><span class="sxs-lookup"><span data-stu-id="37dfb-246">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="37dfb-247">Fügen Sie der Datei *launchSettings.json* der App im **IIS Express**-Profil einen Eintrag hinzu.</span><span class="sxs-lookup"><span data-stu-id="37dfb-247">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="37dfb-248">Diese Einstellung wird verwendet, wenn die Anwendung mit dem Visual Studio-Debugger und an einer Eingabeaufforderung mit `dotnet run` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="37dfb-248">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="37dfb-249">Geben Sie in Visual Studio das Argument in **Eigenschaften** > **Debuggen** > **Anwendungsargumente** ein.</span><span class="sxs-lookup"><span data-stu-id="37dfb-249">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="37dfb-250">Wenn Sie das Argument auf der Visual Studio-Eigenschaftenseite festlegen, wird das Argument der Datei *launchSettings.json* hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="37dfb-250">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="37dfb-251">Konfigurieren des Linkers</span><span class="sxs-lookup"><span data-stu-id="37dfb-251">Configure the Linker</span></span>

<span data-ttu-id="37dfb-252">Blazor führt auf jedem Build eine IL-Verknüpfung (Intermediate Language, Zwischensprache) durch, um nicht benötigte IL aus den Ausgabeassemblys zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="37dfb-252">Blazor performs Intermediate Language (IL) linking on each build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="37dfb-253">Verknüpfungen mit Assemblys können beim Erstellen des Builds gesteuert werden.</span><span class="sxs-lookup"><span data-stu-id="37dfb-253">Assembly linking can be controlled on build.</span></span> <span data-ttu-id="37dfb-254">Weitere Informationen finden Sie unter <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="37dfb-254">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>

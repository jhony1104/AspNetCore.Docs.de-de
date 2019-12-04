---
title: Bündel und minimieren statischer Ressourcen in ASP.net Core
author: scottaddie
description: Erfahren Sie, wie Sie statische Ressourcen in einer ASP.net Core-Webanwendung optimieren, indem Sie bündeln und Mini erverfahren anwenden.
ms.author: scaddie
ms.custom: mvc
ms.date: 06/17/2019
uid: client-side/bundling-and-minification
ms.openlocfilehash: a7a5c40d6c31c4416212c02c1b491dd794f2a1d3
ms.sourcegitcommit: b3e1e31e5d8bdd94096cf27444594d4a7b065525
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74803278"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="4dcc3-103">Bündel und minimieren statischer Ressourcen in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="4dcc3-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="4dcc3-104">Von [Scott Adder](https://twitter.com/Scott_Addie) und [David Pine](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="4dcc3-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="4dcc3-105">In diesem Artikel werden die Vorteile der Anwendung von Bündelung und Minimierung erläutert, einschließlich der Art und Weise, wie diese Funktionen mit ASP.net Core-Web-Apps verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="4dcc3-106">Was ist Bündelung und Minimierung?</span><span class="sxs-lookup"><span data-stu-id="4dcc3-106">What is bundling and minification</span></span>

<span data-ttu-id="4dcc3-107">Bündelung und Minimierung sind zwei unterschiedliche Leistungsoptimierungen, die Sie in einer Web-App anwenden können.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="4dcc3-108">Die gemeinsame Verwendung, Bündelung und Minimierung verbessern die Leistung, indem die Anzahl der Serveranforderungen reduziert und die Größe der angeforderten statischen Ressourcen verringert wird.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="4dcc3-109">Bündelung und Minimierung verbessern hauptsächlich die Ladezeit für die erste Seiten Anforderung.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="4dcc3-110">Nachdem eine Webseite angefordert wurde, speichert der Browser die statischen Assets (JavaScript, CSS und Images) zwischen.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="4dcc3-111">Folglich verbessern die Bündelung und Minimierung die Leistung nicht, wenn die gleiche Seite oder Seiten auf derselben Website angefordert werden, die dieselben Assets anfordert.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="4dcc3-112">Wenn der Ablauf Header für die Objekte nicht ordnungsgemäß festgelegt ist und Bündelung und Minimierung nicht verwendet wird, kennzeichnet die Heuristik des Browsers die Assets nach einigen Tagen.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="4dcc3-113">Außerdem erfordert der Browser eine Validierungs Anforderung für jedes Asset.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="4dcc3-114">In diesem Fall bieten Bündelung und Minimierung eine Leistungsverbesserung, auch nach der ersten Seiten Anforderung.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="4dcc3-115">Anbietet</span><span class="sxs-lookup"><span data-stu-id="4dcc3-115">Bundling</span></span>

<span data-ttu-id="4dcc3-116">Bei der Bündelung werden mehrere Dateien zu einer einzelnen Datei kombiniert.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="4dcc3-117">Die Bündelung reduziert die Anzahl der Serveranforderungen, die zum Rendering eines webassets erforderlich sind, z. b. eine Webseite.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="4dcc3-118">Sie können eine beliebige Anzahl einzelner Bündel speziell für CSS, JavaScript usw. erstellen. Weniger Dateien bedeuten weniger HTTP-Anforderungen vom Browser an den Server oder den Dienst, der Ihre Anwendung bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="4dcc3-119">Dies führt zu einer verbesserten Leistung der ersten Seiten Auslastung.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="4dcc3-120">Minimierung</span><span class="sxs-lookup"><span data-stu-id="4dcc3-120">Minification</span></span>

<span data-ttu-id="4dcc3-121">Die minifizierung entfernt unnötige Zeichen aus dem Code, ohne die Funktionalität zu ändern.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="4dcc3-122">Das Ergebnis ist eine signifikante Größenreduzierung der angeforderten Assets (z. b. CSS, Bilder und JavaScript-Dateien).</span><span class="sxs-lookup"><span data-stu-id="4dcc3-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="4dcc3-123">Zu den allgemeinen Nebeneffekten der Minimierung zählen das verkürzen von Variablennamen in ein Zeichen und das Entfernen von Kommentaren und unnötigen Leerraum.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="4dcc3-124">Beachten Sie die folgende JavaScript-Funktion:</span><span class="sxs-lookup"><span data-stu-id="4dcc3-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="4dcc3-125">Die minifizierung reduziert die-Funktion wie folgt:</span><span class="sxs-lookup"><span data-stu-id="4dcc3-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="4dcc3-126">Zusätzlich zum Entfernen der Kommentare und unnötiger Leerzeichen wurden die folgenden Parameter-und Variablennamen wie folgt umbenannt:</span><span class="sxs-lookup"><span data-stu-id="4dcc3-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="4dcc3-127">Ursprünglich</span><span class="sxs-lookup"><span data-stu-id="4dcc3-127">Original</span></span> | <span data-ttu-id="4dcc3-128">Umbenannt</span><span class="sxs-lookup"><span data-stu-id="4dcc3-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="4dcc3-129">Auswirkungen von Bündelung und Minimierung</span><span class="sxs-lookup"><span data-stu-id="4dcc3-129">Impact of bundling and minification</span></span>

<span data-ttu-id="4dcc3-130">In der folgenden Tabelle werden die Unterschiede zwischen dem individuellen Laden von Assets und der Verwendung von Bündelung und Minimierung erläutert:</span><span class="sxs-lookup"><span data-stu-id="4dcc3-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="4dcc3-131">-Aktion</span><span class="sxs-lookup"><span data-stu-id="4dcc3-131">Action</span></span> | <span data-ttu-id="4dcc3-132">Mit B/M</span><span class="sxs-lookup"><span data-stu-id="4dcc3-132">With B/M</span></span> | <span data-ttu-id="4dcc3-133">Ohne B/M</span><span class="sxs-lookup"><span data-stu-id="4dcc3-133">Without B/M</span></span> | <span data-ttu-id="4dcc3-134">Anwendungseinstellungen</span><span class="sxs-lookup"><span data-stu-id="4dcc3-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="4dcc3-135">Datei Anforderungen</span><span class="sxs-lookup"><span data-stu-id="4dcc3-135">File Requests</span></span>  | <span data-ttu-id="4dcc3-136">7</span><span class="sxs-lookup"><span data-stu-id="4dcc3-136">7</span></span>   | <span data-ttu-id="4dcc3-137">18</span><span class="sxs-lookup"><span data-stu-id="4dcc3-137">18</span></span>     | <span data-ttu-id="4dcc3-138">157%</span><span class="sxs-lookup"><span data-stu-id="4dcc3-138">157%</span></span>
<span data-ttu-id="4dcc3-139">KB übertragen</span><span class="sxs-lookup"><span data-stu-id="4dcc3-139">KB Transferred</span></span> | <span data-ttu-id="4dcc3-140">156</span><span class="sxs-lookup"><span data-stu-id="4dcc3-140">156</span></span> | <span data-ttu-id="4dcc3-141">264.68</span><span class="sxs-lookup"><span data-stu-id="4dcc3-141">264.68</span></span> | <span data-ttu-id="4dcc3-142">70%</span><span class="sxs-lookup"><span data-stu-id="4dcc3-142">70%</span></span>
<span data-ttu-id="4dcc3-143">Ladezeit (MS)</span><span class="sxs-lookup"><span data-stu-id="4dcc3-143">Load Time (ms)</span></span> | <span data-ttu-id="4dcc3-144">885</span><span class="sxs-lookup"><span data-stu-id="4dcc3-144">885</span></span> | <span data-ttu-id="4dcc3-145">2360</span><span class="sxs-lookup"><span data-stu-id="4dcc3-145">2360</span></span>   | <span data-ttu-id="4dcc3-146">167%</span><span class="sxs-lookup"><span data-stu-id="4dcc3-146">167%</span></span>

<span data-ttu-id="4dcc3-147">Browser sind im Hinblick auf HTTP-Anforderungs Header recht ausführlich.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="4dcc3-148">Die Metrik "Insgesamt gesendete Bytes" hat beim bündeln eine erhebliche Reduzierung erkannt.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="4dcc3-149">Die Ladezeit zeigt eine bedeutende Verbesserung, dieses Beispiel wird jedoch lokal ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="4dcc3-150">Höhere Leistungssteigerungen werden erzielt, wenn Bündelung und Minimierung mit Assets verwendet werden, die über ein Netzwerk übertragen werden.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="4dcc3-151">Auswählen einer Strategie für Bündelung und Minimierung</span><span class="sxs-lookup"><span data-stu-id="4dcc3-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="4dcc3-152">Die MVC-und Razor Pages-Projektvorlagen stellen eine sofort einstufige Lösung für die Bündelung und Minimierung dar, die aus einer JSON-Konfigurationsdatei besteht.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-152">The MVC and Razor Pages project templates provide an out-of-the-box solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="4dcc3-153">Drittanbieter Tools, wie z. b. die [grunt](xref:client-side/using-grunt) -Aufgabenausführung, erfüllen die gleichen Aufgaben mit etwas mehr Komplexität.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="4dcc3-154">Ein Drittanbieter Tool eignet sich hervorragend, wenn Ihr Entwicklungs Workflow die Verarbeitung über die Bündelung und Minimierung hinaus erfordert&mdash;z. b. linting-und Image-Optimierung.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="4dcc3-155">Mithilfe der Entwurfszeit Bündelung und-Minimierung werden die minisierten Dateien vor der Bereitstellung der App erstellt.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="4dcc3-156">Das bündeln und minimieren vor der Bereitstellung bietet den Vorteil der reduzierten Serverauslastung.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="4dcc3-157">Es ist jedoch wichtig zu erkennen, dass die Entwurfszeit Bündelung und-Minimierung die Komplexität der Builds erhöht und nur mit statischen Dateien funktioniert.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="4dcc3-158">Konfigurieren von Bündelung und Minimierung</span><span class="sxs-lookup"><span data-stu-id="4dcc3-158">Configure bundling and minification</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="4dcc3-159">In ASP.net Core 2,0 oder früher bieten die MVC-und Razor Pages-Projektvorlagen eine *bundleconfig. JSON* -Konfigurationsdatei, die die Optionen für jedes Bündel definiert:</span><span class="sxs-lookup"><span data-stu-id="4dcc3-159">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="4dcc3-160">Fügen Sie in ASP.net Core 2,1 oder höher eine neue JSON-Datei mit dem Namen *bundleconfig. JSON*in das MVC-oder Razor Pages-Projektstamm Verzeichnis ein.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-160">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="4dcc3-161">Fügen Sie den folgenden JSON-Code als Ausgangspunkt in diese Datei ein:</span><span class="sxs-lookup"><span data-stu-id="4dcc3-161">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="4dcc3-162">Die Datei *bundleconfig. JSON* definiert die Optionen für jedes Paket.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-162">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="4dcc3-163">Im vorherigen Beispiel wird eine einzelne Paketkonfiguration für die benutzerdefinierten JavaScript-Dateien (*wwwroot/js/Site. js*) und Stylesheet (*wwwroot/CSS/Site. CSS*) definiert.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-163">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="4dcc3-164">Zu den Konfigurationsoptionen gehören:</span><span class="sxs-lookup"><span data-stu-id="4dcc3-164">Configuration options include:</span></span>

* <span data-ttu-id="4dcc3-165">`outputFileName`: der Name der Paketdatei, die ausgegeben werden soll.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-165">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="4dcc3-166">Kann einen relativen Pfad aus der Datei " *bundleconfig. JSON* " enthalten.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-166">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="4dcc3-167">**erforderlich**</span><span class="sxs-lookup"><span data-stu-id="4dcc3-167">**required**</span></span>
* <span data-ttu-id="4dcc3-168">`inputFiles`: ein Array von Dateien, die zusammengefasst werden sollen.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-168">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="4dcc3-169">Dabei handelt es sich um relative Pfade zur Konfigurationsdatei.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-169">These are relative paths to the configuration file.</span></span> <span data-ttu-id="4dcc3-170">**optional**: \* ein leerer Wert führt zu einer leeren Ausgabedatei.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-170">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="4dcc3-171">[globmuster](https://www.tldp.org/LDP/abs/html/globbingref.html) werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="4dcc3-172">`minify`: die Minimierung-Optionen für den Ausgabetyp.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-172">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="4dcc3-173">**optional**, *Standard-`minify: { enabled: true }`*</span><span class="sxs-lookup"><span data-stu-id="4dcc3-173">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="4dcc3-174">Konfigurationsoptionen sind pro Ausgabe Dateityp verfügbar.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-174">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="4dcc3-175">CSS Minifier</span><span class="sxs-lookup"><span data-stu-id="4dcc3-175">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="4dcc3-176">JavaScript-minifier</span><span class="sxs-lookup"><span data-stu-id="4dcc3-176">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="4dcc3-177">HTML Minifier</span><span class="sxs-lookup"><span data-stu-id="4dcc3-177">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="4dcc3-178">`includeInProject`: Flag, das angibt, ob generierte Dateien der Projektdatei hinzugefügt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-178">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="4dcc3-179">**optional**, *Standard-false*</span><span class="sxs-lookup"><span data-stu-id="4dcc3-179">**optional**, *default - false*</span></span>
* <span data-ttu-id="4dcc3-180">`sourceMap`: Flag, das angibt, ob eine Quell Zuordnung für die gebündelte Datei generiert werden soll.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-180">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="4dcc3-181">**optional**, *Standard-false*</span><span class="sxs-lookup"><span data-stu-id="4dcc3-181">**optional**, *default - false*</span></span>
* <span data-ttu-id="4dcc3-182">`sourceMapRootPath`: der Stammpfad zum Speichern der generierten Quell Zuordnungs Datei.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-182">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="build-time-execution-of-bundling-and-minification"></a><span data-ttu-id="4dcc3-183">Build-Zeit-Ausführung von Bündelung und Minimierung</span><span class="sxs-lookup"><span data-stu-id="4dcc3-183">Build-time execution of bundling and minification</span></span>

<span data-ttu-id="4dcc3-184">Das [buildbundlerminifier](https://www.nuget.org/packages/BuildBundlerMinifier/) -nuget-Paket ermöglicht die Ausführung von Bündelung und Minimierung zur Buildzeit.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-184">The [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet package enables the execution of bundling and minification at build time.</span></span> <span data-ttu-id="4dcc3-185">Das Paket fügt [MSBuild-Ziele](/visualstudio/msbuild/msbuild-targets) ein, die zum Zeitpunkt der Erstellung und Bereinigung ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-185">The package injects [MSBuild Targets](/visualstudio/msbuild/msbuild-targets) which run at build and clean time.</span></span> <span data-ttu-id="4dcc3-186">Die Datei " *bundleconfig. JSON* " wird vom Buildprozess analysiert, um die Ausgabedateien basierend auf der definierten Konfiguration zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-186">The *bundleconfig.json* file is analyzed by the build process to produce the output files based on the defined configuration.</span></span>

> [!NOTE]
> <span data-ttu-id="4dcc3-187">Buildbundlerminifier gehört zu einem Community-gesteuerten Projekt auf GitHub, für das Microsoft keine Unterstützung bietet.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-187">BuildBundlerMinifier belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="4dcc3-188">[Hier](https://github.com/madskristensen/BundlerMinifier/issues)sollten Probleme aufgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-188">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4dcc3-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4dcc3-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4dcc3-190">Fügen Sie dem Projekt das *buildbundlerminifier* -Paket hinzu.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-190">Add the *BuildBundlerMinifier* package to your project.</span></span>

<span data-ttu-id="4dcc3-191">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-191">Build the project.</span></span> <span data-ttu-id="4dcc3-192">Im Ausgabefenster wird Folgendes angezeigt:</span><span class="sxs-lookup"><span data-stu-id="4dcc3-192">The following appears in the Output window:</span></span>

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Begin processing bundleconfig.json
1>  Minified wwwroot/css/site.min.css
1>  Minified wwwroot/js/site.min.js
1>Bundler: Done processing bundleconfig.json
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

<span data-ttu-id="4dcc3-193">Bereinigen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-193">Clean the project.</span></span> <span data-ttu-id="4dcc3-194">Im Ausgabefenster wird Folgendes angezeigt:</span><span class="sxs-lookup"><span data-stu-id="4dcc3-194">The following appears in the Output window:</span></span>

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4dcc3-195">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="4dcc3-195">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="4dcc3-196">Fügen Sie dem Projekt das *buildbundlerminifier* -Paket hinzu:</span><span class="sxs-lookup"><span data-stu-id="4dcc3-196">Add the *BuildBundlerMinifier* package to your project:</span></span>

```dotnetcli
dotnet add package BuildBundlerMinifier
```

<span data-ttu-id="4dcc3-197">Wenn Sie ASP.net Core 1. x verwenden, stellen Sie das neu hinzugefügte Paket wieder her:</span><span class="sxs-lookup"><span data-stu-id="4dcc3-197">If using ASP.NET Core 1.x, restore the newly added package:</span></span>

```dotnetcli
dotnet restore
```

<span data-ttu-id="4dcc3-198">Erstellen Sie das Projekt:</span><span class="sxs-lookup"><span data-stu-id="4dcc3-198">Build the project:</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="4dcc3-199">Folgendes wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="4dcc3-199">The following appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

<span data-ttu-id="4dcc3-200">Bereinigen Sie das Projekt:</span><span class="sxs-lookup"><span data-stu-id="4dcc3-200">Clean the project:</span></span>

```dotnetcli
dotnet clean
```

<span data-ttu-id="4dcc3-201">Die folgende Ausgabe wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="4dcc3-201">The following output appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Cleaning output from bundleconfig.json
  Bundler: Done cleaning output file from bundleconfig.json
```

---

## <a name="ad-hoc-execution-of-bundling-and-minification"></a><span data-ttu-id="4dcc3-202">Ad-hoc-Ausführung von Bündelung und Minimierung</span><span class="sxs-lookup"><span data-stu-id="4dcc3-202">Ad hoc execution of bundling and minification</span></span>

<span data-ttu-id="4dcc3-203">Es ist möglich, die Bündelung und Minimierung von Aufgaben auf Ad-hoc-Basis auszuführen, ohne das Projekt zu entwickeln.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-203">It's possible to run the bundling and minification tasks on an ad hoc basis, without building the project.</span></span> <span data-ttu-id="4dcc3-204">Fügen Sie dem Projekt das nuget-Paket [bundlerminifier. Core](https://www.nuget.org/packages/BundlerMinifier.Core/) hinzu:</span><span class="sxs-lookup"><span data-stu-id="4dcc3-204">Add the [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet package to your project:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> <span data-ttu-id="4dcc3-205">Bundlerminifier. Core gehört zu einem Community-gesteuerten Projekt auf GitHub, für das Microsoft keine Unterstützung bietet.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-205">BundlerMinifier.Core belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="4dcc3-206">[Hier](https://github.com/madskristensen/BundlerMinifier/issues)sollten Probleme aufgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-206">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="4dcc3-207">Dieses Paket erweitert das .net Core-CLI, um das *dotnet-Bundle-* Tool einzubeziehen.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-207">This package extends the .NET Core CLI to include the *dotnet-bundle* tool.</span></span> <span data-ttu-id="4dcc3-208">Der folgende Befehl kann im Fenster der Paket-Manager-Konsole (PMC) oder in einer Befehlsshell ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="4dcc3-208">The following command can be executed in the Package Manager Console (PMC) window or in a command shell:</span></span>

```dotnetcli
dotnet bundle
```

> [!IMPORTANT]
> <span data-ttu-id="4dcc3-209">Der nuget-Paket-Manager fügt der \*. CSPROJ-Datei Abhängigkeiten als `<PackageReference />` Knoten hinzu.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-209">NuGet Package Manager adds dependencies to the \*.csproj file as `<PackageReference />` nodes.</span></span> <span data-ttu-id="4dcc3-210">Der `dotnet bundle` Befehl wird nur bei der .net Core-CLI registriert, wenn ein `<DotNetCliToolReference />` Knoten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-210">The `dotnet bundle` command is registered with the .NET Core CLI only when a `<DotNetCliToolReference />` node is used.</span></span> <span data-ttu-id="4dcc3-211">Ändern Sie die Datei \*. csproj entsprechend.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-211">Modify the \*.csproj file accordingly.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="4dcc3-212">Dateien zum Workflow hinzufügen</span><span class="sxs-lookup"><span data-stu-id="4dcc3-212">Add files to workflow</span></span>

<span data-ttu-id="4dcc3-213">Sehen Sie sich ein Beispiel an, in dem eine zusätzliche *benutzerdefinierte CSS* -Datei ähnlich der folgenden hinzugefügt wird:</span><span class="sxs-lookup"><span data-stu-id="4dcc3-213">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="4dcc3-214">Fügen Sie den relativen Pfad zu *bundleconfig. JSON*hinzu, um " *Custom. CSS* " zu minimieren und mit " *Site. CSS* " in einer " *Site. min. CSS* "-Datei zu bündeln:</span><span class="sxs-lookup"><span data-stu-id="4dcc3-214">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="4dcc3-215">Alternativ könnte das folgende globmuster verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="4dcc3-215">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> <span data-ttu-id="4dcc3-216">Dieses globmuster stimmt mit allen CSS-Dateien überein und schließt das minierte Dateimuster aus.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-216">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="4dcc3-217">Erstellen Sie die Anwendung.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-217">Build the application.</span></span> <span data-ttu-id="4dcc3-218">Öffnen Sie " *Site. min. CSS* ", und beachten Sie, dass der Inhalt von " *Custom. CSS* " an das Ende der Datei angehängt wird.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-218">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="4dcc3-219">Umgebungs basierte Bündelung und Minimierung</span><span class="sxs-lookup"><span data-stu-id="4dcc3-219">Environment-based bundling and minification</span></span>

<span data-ttu-id="4dcc3-220">Als bewährte Vorgehensweise sollten die gebündelten und die minierten Dateien Ihrer APP in einer Produktionsumgebung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-220">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="4dcc3-221">Während der Entwicklung werden die ursprünglichen Dateien zum einfacheren Debuggen der APP gemacht.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-221">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="4dcc3-222">Geben Sie mithilfe des [Umgebungs-taghilfsprogramms](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in ihren Ansichten an, welche Dateien in Ihre Seiten eingeschlossen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-222">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="4dcc3-223">Das Umgebungs-taghilfsprogramm rendert nur seinen Inhalt, wenn es in bestimmten [Umgebungen](xref:fundamentals/environments)ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-223">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="4dcc3-224">Das folgende `environment`-Tag rendert die nicht verarbeiteten CSS-Dateien, wenn Sie in der `Development`-Umgebung ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="4dcc3-224">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="4dcc3-225">Das folgende `environment`-Tag rendert die gebündelten und die minierten CSS-Dateien, wenn Sie in einer anderen Umgebung als `Development`ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-225">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="4dcc3-226">Wenn Sie z. b. in `Production` oder `Staging` ausführen, wird das Rendering dieser Stylesheets ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="4dcc3-226">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="4dcc3-227">Verwenden von bundleconfig. JSON aus Gulp</span><span class="sxs-lookup"><span data-stu-id="4dcc3-227">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="4dcc3-228">Es gibt Fälle, in denen das bündeln und minimieren einer App eine zusätzliche Verarbeitung erfordert.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-228">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="4dcc3-229">Beispiele hierfür sind Bildoptimierung, Cache-Busting und CDN-Asset-Verarbeitung.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-229">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="4dcc3-230">Um diese Anforderungen zu erfüllen, können Sie den Bundle-und Minimierung-Workflow für die Verwendung von Gulp konvertieren.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-230">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="use-the-bundler--minifier-extension"></a><span data-ttu-id="4dcc3-231">Verwenden der Bundler-& minifier-Erweiterung</span><span class="sxs-lookup"><span data-stu-id="4dcc3-231">Use the Bundler & Minifier extension</span></span>

<span data-ttu-id="4dcc3-232">Die Visual Studio [Bundler-& minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) -Erweiterung behandelt die Konvertierung in Gulp.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-232">The Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) extension handles the conversion to Gulp.</span></span>

> [!NOTE]
> <span data-ttu-id="4dcc3-233">Die Bundler-& minifier-Erweiterung gehört zu einem Community-gesteuerten Projekt auf GitHub, für das Microsoft keine Unterstützung bietet.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-233">The Bundler & Minifier extension belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="4dcc3-234">[Hier](https://github.com/madskristensen/BundlerMinifier/issues)sollten Probleme aufgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-234">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="4dcc3-235">Klicken Sie in Projektmappen-Explorer mit der rechten Maustaste auf die Datei *bundleconfig. JSON* , und wählen Sie **Bundler & minifier** aus, > in **Gulp konvertieren...** :</span><span class="sxs-lookup"><span data-stu-id="4dcc3-235">Right-click the *bundleconfig.json* file in Solution Explorer and select **Bundler & Minifier** > **Convert To Gulp...**:</span></span>

![In Gulp-Kontextmenü Element konvertieren](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

<span data-ttu-id="4dcc3-237">Die Dateien " *gulpfile. js* " und " *Package. JSON* " werden dem Projekt hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-237">The *gulpfile.js* and *package.json* files are added to the project.</span></span> <span data-ttu-id="4dcc3-238">Die unterstützenden [NPM](https://www.npmjs.com/) -Pakete, die im `devDependencies` Abschnitt der Datei " *Package. JSON* " aufgeführt sind, werden installiert.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-238">The supporting [npm](https://www.npmjs.com/) packages listed in the *package.json* file's `devDependencies` section are installed.</span></span>

<span data-ttu-id="4dcc3-239">Führen Sie den folgenden Befehl im PMC-Fenster aus, um die Gulp-CLI als globale Abhängigkeit zu installieren:</span><span class="sxs-lookup"><span data-stu-id="4dcc3-239">Run the following command in the PMC window to install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="4dcc3-240">Die Datei " *gulpfile. js* " liest die Datei " *bundleconfig. JSON* " für die Eingaben, Ausgaben und Einstellungen.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-240">The *gulpfile.js* file reads the *bundleconfig.json* file for the inputs, outputs, and settings.</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a><span data-ttu-id="4dcc3-241">Manuell konvertieren</span><span class="sxs-lookup"><span data-stu-id="4dcc3-241">Convert manually</span></span>

<span data-ttu-id="4dcc3-242">Wenn Visual Studio und/oder die Bundler-& minifier-Erweiterung nicht verfügbar ist, konvertieren Sie manuell.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-242">If Visual Studio and/or the Bundler & Minifier extension aren't available, convert manually.</span></span>

<span data-ttu-id="4dcc3-243">Fügen Sie dem Projektstamm eine Datei " *Package. JSON* " mit dem folgenden `devDependencies`hinzu:</span><span class="sxs-lookup"><span data-stu-id="4dcc3-243">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="4dcc3-244">Das `gulp-uglify`-Modul unterstützt ECMAScript (es) 2015/ES6 und höher nicht.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-244">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="4dcc3-245">Installieren Sie [Gulp-terser](https://www.npmjs.com/package/gulp-terser) anstelle von `gulp-uglify`, um ES2015/ES6 oder höher zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-245">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="4dcc3-246">Installieren Sie die Abhängigkeiten, indem Sie den folgenden Befehl auf der gleichen Ebene wie " *Package. JSON*" ausführen:</span><span class="sxs-lookup"><span data-stu-id="4dcc3-246">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```console
npm i
```

<span data-ttu-id="4dcc3-247">Installieren Sie die Gulp-CLI als globale Abhängigkeit:</span><span class="sxs-lookup"><span data-stu-id="4dcc3-247">Install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="4dcc3-248">Kopieren Sie die nachfolgende Datei " *gulpfile. js* " in das Projektstamm Verzeichnis:</span><span class="sxs-lookup"><span data-stu-id="4dcc3-248">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="4dcc3-249">Ausführen von Gulp-Tasks</span><span class="sxs-lookup"><span data-stu-id="4dcc3-249">Run Gulp tasks</span></span>

<span data-ttu-id="4dcc3-250">Fügen Sie das folgende [MSBuild-Ziel](/visualstudio/msbuild/msbuild-targets) der \*. CSPROJ-Datei hinzu, um die Gulp-minierationsaufgabe vor dem Erstellen des Projekts in Visual Studio zu starten:</span><span class="sxs-lookup"><span data-stu-id="4dcc3-250">To trigger the Gulp minification task before the project builds in Visual Studio, add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the \*.csproj file:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="4dcc3-251">In diesem Beispiel werden alle Aufgaben, die im `MyPreCompileTarget` Ziel definiert sind, vor dem vordefinierten `Build` Ziel ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="4dcc3-251">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="4dcc3-252">Die Ausgabe ähnlich der folgenden wird im Ausgabefenster von Visual Studio angezeigt:</span><span class="sxs-lookup"><span data-stu-id="4dcc3-252">Output similar to the following appears in Visual Studio's Output window:</span></span>

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
1>[14:17:49] Using gulpfile C:\BuildBundlerMinifierApp\gulpfile.js
1>[14:17:49] Starting 'min:js'...
1>[14:17:49] Starting 'min:css'...
1>[14:17:49] Starting 'min:html'...
1>[14:17:49] Finished 'min:js' after 83 ms
1>[14:17:49] Finished 'min:css' after 88 ms
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

## <a name="additional-resources"></a><span data-ttu-id="4dcc3-253">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="4dcc3-253">Additional resources</span></span>

* [<span data-ttu-id="4dcc3-254">Verwenden von Grunt</span><span class="sxs-lookup"><span data-stu-id="4dcc3-254">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="4dcc3-255">Verwenden mehrerer Umgebungen</span><span class="sxs-lookup"><span data-stu-id="4dcc3-255">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="4dcc3-256">Tag Helpers (Taghilfsprogramme)</span><span class="sxs-lookup"><span data-stu-id="4dcc3-256">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)

---
title: Bündelung und Minimierung von statischen Objekten in ASP.NET Core
author: scottaddie
description: Erfahren Sie, wie Sie statische Ressourcen in einer ASP.NET Core-Webanwendung durch Anwenden von bündelungs-und minimierungsverfahren zu optimieren.
ms.author: scaddie
ms.custom: mvc
ms.date: 06/17/2019
uid: client-side/bundling-and-minification
ms.openlocfilehash: c3ec67d9f5c2b8eb44b5ced911ccfd0fa40a52a7
ms.sourcegitcommit: 4ef0362ef8b6e5426fc5af18f22734158fe587e1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67152776"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="aeb60-103">Bündelung und Minimierung von statischen Objekten in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aeb60-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="aeb60-104">Durch [Scott Addie](https://twitter.com/Scott_Addie) und [David Kiefer](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="aeb60-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="aeb60-105">Dieser Artikel beschreibt die Vorteile der Anwendung Bündelung und Minimierung, einschließlich, wie diese Features in ASP.NET Core-Web-apps verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="aeb60-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="aeb60-106">Was ist die Bündelung und Minimierung</span><span class="sxs-lookup"><span data-stu-id="aeb60-106">What is bundling and minification</span></span>

<span data-ttu-id="aeb60-107">Bündelung und Minimierung sind zwei unterschiedliche leistungsoptimierungen, die Sie in einer Web-app anwenden können.</span><span class="sxs-lookup"><span data-stu-id="aeb60-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="aeb60-108">Zusammen verwendet werden, Verbessern der Bündelung und Minimierung Leistung durch Reduzierung der Anzahl von serveranforderungen und Verringern der Größe der angeforderten statischen Objekten.</span><span class="sxs-lookup"><span data-stu-id="aeb60-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="aeb60-109">Bündelung und Minimierung wird in erster Linie die erste Anforderung Seitenladezeit verbessern.</span><span class="sxs-lookup"><span data-stu-id="aeb60-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="aeb60-110">Nach einer Webseite angefordert wurde, speichert der Browser die statischen Ressourcen (JavaScript, CSS und Bilder).</span><span class="sxs-lookup"><span data-stu-id="aeb60-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="aeb60-111">Folglich leistungsverbesserung nicht Bündelung und Minimierung beim Anfordern von derselben Seite oder Seiten am selben Standort die gleichen Ressourcen anfordern.</span><span class="sxs-lookup"><span data-stu-id="aeb60-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="aeb60-112">Wenn der Ablauf Header ist nicht korrekt Ressourcen festgelegt und wenn Bündelung und Minimierung wird nicht verwendet, im Browser auf die Aktualität Heuristik markieren Sie die Ressourcen veraltete nach einigen Tagen.</span><span class="sxs-lookup"><span data-stu-id="aeb60-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="aeb60-113">Darüber hinaus muss der Browser eine Anforderung zur abonnementüberprüfung für jedes Medienobjekt.</span><span class="sxs-lookup"><span data-stu-id="aeb60-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="aeb60-114">In diesem Fall geben die Bündelung und Minimierung eine besseren Leistung auch nach der die erste Seitenanforderung.</span><span class="sxs-lookup"><span data-stu-id="aeb60-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="aeb60-115">Bündeln</span><span class="sxs-lookup"><span data-stu-id="aeb60-115">Bundling</span></span>

<span data-ttu-id="aeb60-116">Bündelung werden mehrere Dateien in einer einzelnen Datei kombiniert.</span><span class="sxs-lookup"><span data-stu-id="aeb60-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="aeb60-117">Bündelung reduziert die Anzahl der serveranforderungen, die erforderlich sind, um eine Web-Ressource, z. B. eine Webseite zu rendern.</span><span class="sxs-lookup"><span data-stu-id="aeb60-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="aeb60-118">Sie können eine beliebige Anzahl von den einzelnen Paketen speziell für CSS, JavaScript usw. erstellen. Weniger Dateien bedeutet weniger HTTP-Anforderungen vom Browser an den Server oder aus dem Dienst, der Ihre Anwendung bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="aeb60-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="aeb60-119">Dies führt zu verbessert die ladeleistung für die ersten Seiten.</span><span class="sxs-lookup"><span data-stu-id="aeb60-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="aeb60-120">Minimierung</span><span class="sxs-lookup"><span data-stu-id="aeb60-120">Minification</span></span>

<span data-ttu-id="aeb60-121">Minimierung werden unnötige Zeichen aus Code ohne dabei Funktionalität zu entfernt.</span><span class="sxs-lookup"><span data-stu-id="aeb60-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="aeb60-122">Das Ergebnis ist eine wesentliche Verringerung angeforderten Assets (z. B. CSS, Bilder und JavaScript-Dateien).</span><span class="sxs-lookup"><span data-stu-id="aeb60-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="aeb60-123">Allgemeine Nebeneffekte der Minimierung enthalten, verkürzen Sie die Variablennamen, um ein Zeichen, und Entfernen von Kommentaren und unnötiger Leerraum.</span><span class="sxs-lookup"><span data-stu-id="aeb60-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="aeb60-124">Betrachten Sie die folgende JavaScript-Funktion:</span><span class="sxs-lookup"><span data-stu-id="aeb60-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="aeb60-125">Minimierung reduziert sich die Funktion auf Folgendes:</span><span class="sxs-lookup"><span data-stu-id="aeb60-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="aeb60-126">Zusätzlich zu entfernen, die Kommentare und unnötiger Leerraum, wurden die folgenden Parameter und Variablen Namen folgendermaßen umbenannt:</span><span class="sxs-lookup"><span data-stu-id="aeb60-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="aeb60-127">Ursprünglich</span><span class="sxs-lookup"><span data-stu-id="aeb60-127">Original</span></span> | <span data-ttu-id="aeb60-128">Umbenannt</span><span class="sxs-lookup"><span data-stu-id="aeb60-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="aeb60-129">Auswirkungen der Bündelung und Minimierung</span><span class="sxs-lookup"><span data-stu-id="aeb60-129">Impact of bundling and minification</span></span>

<span data-ttu-id="aeb60-130">In der folgende Tabelle werden Unterschiede zwischen einzeln Laden von Medienobjekten und mithilfe von Bündelung und Minimierung beschrieben:</span><span class="sxs-lookup"><span data-stu-id="aeb60-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="aeb60-131">Aktion</span><span class="sxs-lookup"><span data-stu-id="aeb60-131">Action</span></span> | <span data-ttu-id="aeb60-132">Mit B/Min.</span><span class="sxs-lookup"><span data-stu-id="aeb60-132">With B/M</span></span> | <span data-ttu-id="aeb60-133">Ohne B/Min.</span><span class="sxs-lookup"><span data-stu-id="aeb60-133">Without B/M</span></span> | <span data-ttu-id="aeb60-134">Änderung</span><span class="sxs-lookup"><span data-stu-id="aeb60-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="aeb60-135">Dateianforderungen</span><span class="sxs-lookup"><span data-stu-id="aeb60-135">File Requests</span></span>  | <span data-ttu-id="aeb60-136">7</span><span class="sxs-lookup"><span data-stu-id="aeb60-136">7</span></span>   | <span data-ttu-id="aeb60-137">18</span><span class="sxs-lookup"><span data-stu-id="aeb60-137">18</span></span>     | <span data-ttu-id="aeb60-138">157%</span><span class="sxs-lookup"><span data-stu-id="aeb60-138">157%</span></span>
<span data-ttu-id="aeb60-139">KB übertragen</span><span class="sxs-lookup"><span data-stu-id="aeb60-139">KB Transferred</span></span> | <span data-ttu-id="aeb60-140">156</span><span class="sxs-lookup"><span data-stu-id="aeb60-140">156</span></span> | <span data-ttu-id="aeb60-141">264.68</span><span class="sxs-lookup"><span data-stu-id="aeb60-141">264.68</span></span> | <span data-ttu-id="aeb60-142">70%</span><span class="sxs-lookup"><span data-stu-id="aeb60-142">70%</span></span>
<span data-ttu-id="aeb60-143">Ladezeit (ms)</span><span class="sxs-lookup"><span data-stu-id="aeb60-143">Load Time (ms)</span></span> | <span data-ttu-id="aeb60-144">885</span><span class="sxs-lookup"><span data-stu-id="aeb60-144">885</span></span> | <span data-ttu-id="aeb60-145">2360</span><span class="sxs-lookup"><span data-stu-id="aeb60-145">2360</span></span>   | <span data-ttu-id="aeb60-146">167%</span><span class="sxs-lookup"><span data-stu-id="aeb60-146">167%</span></span>

<span data-ttu-id="aeb60-147">Browser sind recht ausführlich im Hinblick auf HTTP-Anforderungsheadern.</span><span class="sxs-lookup"><span data-stu-id="aeb60-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="aeb60-148">Die Gesamtzahl der Bytes gesendet, dass die Metrik zu eine erhebliche Reduzierung beim bündeln angezeigt wurde.</span><span class="sxs-lookup"><span data-stu-id="aeb60-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="aeb60-149">Die Ladezeit zeigt eine bedeutende Verbesserung, jedoch in diesem Beispiel wird lokal ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="aeb60-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="aeb60-150">Deutlichere leistungsverbesserungen werden realisiert werden, wenn Bündelung und Minimierung mit Ressourcen verwenden, die über ein Netzwerk übertragen.</span><span class="sxs-lookup"><span data-stu-id="aeb60-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="aeb60-151">Wählen Sie eine Strategie für die Bündelung und Minimierung</span><span class="sxs-lookup"><span data-stu-id="aeb60-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="aeb60-152">Die Razor-Seiten und MVC-Projektvorlagen bieten eine Out-of-the-Box-Lösung für die Bündelung und Minimierung, bestehend aus einer JSON-Konfigurationsdatei an.</span><span class="sxs-lookup"><span data-stu-id="aeb60-152">The MVC and Razor Pages project templates provide an out-of-the-box solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="aeb60-153">Drittanbieter-tools, z. B. die [Grunt](xref:client-side/using-grunt) task Runner, die gleichen Aufgaben mit etwas mehr Komplexität.</span><span class="sxs-lookup"><span data-stu-id="aeb60-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="aeb60-154">Ein Tool eines Drittanbieters ist hervorragend aus, wenn Ihrem Entwicklungsworkflow Verarbeitung auf über die Bündelung und Minimierung erfordert&mdash;wie Linting und Image.</span><span class="sxs-lookup"><span data-stu-id="aeb60-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="aeb60-155">Verwenden Sie während der Entwurfszeit Bündelung und Minimierung, werden die minimierten Dateien vor der Bereitstellung von der app erstellt.</span><span class="sxs-lookup"><span data-stu-id="aeb60-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="aeb60-156">Bündeln und Minimieren der vor der Bereitstellung bietet den Vorteil des reduzierten Last auf.</span><span class="sxs-lookup"><span data-stu-id="aeb60-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="aeb60-157">Allerdings es ist wichtig zu erkennen, während der Entwurfszeit Bündelung und Minimierung erhöht die Komplexität des Builds ab und funktioniert nur mit statischen Dateien.</span><span class="sxs-lookup"><span data-stu-id="aeb60-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="aeb60-158">Konfigurieren der Bündelung und Minimierung</span><span class="sxs-lookup"><span data-stu-id="aeb60-158">Configure bundling and minification</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="aeb60-159">Geben Sie die Projektvorlagen von MVC und Razor Pages in ASP.NET Core 2.0 oder früher, eine *bundleconfig.json* Konfigurationsdatei, die Optionen für jedes Paket definiert:</span><span class="sxs-lookup"><span data-stu-id="aeb60-159">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="aeb60-160">In ASP.NET Core 2.1 oder höher, fügen Sie eine neue JSON-Datei, mit dem Namen *bundleconfig.json*, um das Stammverzeichnis des Projekts MVC oder Razor-Seiten.</span><span class="sxs-lookup"><span data-stu-id="aeb60-160">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="aeb60-161">Gehören Sie den folgenden JSON-Code in dieser Datei als Ausgangspunkt:</span><span class="sxs-lookup"><span data-stu-id="aeb60-161">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="aeb60-162">Die *bundleconfig.json* -Datei definiert die Optionen für jedes Paket.</span><span class="sxs-lookup"><span data-stu-id="aeb60-162">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="aeb60-163">Im vorherigen Beispiel eine Paket-Konfiguration für die benutzerdefinierten JavaScript-Code definiert ist (*wwwroot/js/site.js*) und Stylesheet (*wwwroot/css/site.css*) Dateien.</span><span class="sxs-lookup"><span data-stu-id="aeb60-163">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="aeb60-164">Konfigurationsoptionen sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="aeb60-164">Configuration options include:</span></span>

* <span data-ttu-id="aeb60-165">`outputFileName`: Der Name der Bundle-Datei ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="aeb60-165">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="aeb60-166">Einen relativen Pfad darf die *bundleconfig.json* Datei.</span><span class="sxs-lookup"><span data-stu-id="aeb60-166">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="aeb60-167">**required**</span><span class="sxs-lookup"><span data-stu-id="aeb60-167">**required**</span></span>
* <span data-ttu-id="aeb60-168">`inputFiles`: Ein Array von Dateien zu bündeln.</span><span class="sxs-lookup"><span data-stu-id="aeb60-168">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="aeb60-169">Dies sind die relativen Pfade der Konfigurationsdatei hinzu.</span><span class="sxs-lookup"><span data-stu-id="aeb60-169">These are relative paths to the configuration file.</span></span> <span data-ttu-id="aeb60-170">**optionale**, \* ein leerer Wert in einer leeren Ausgabedatei führt.</span><span class="sxs-lookup"><span data-stu-id="aeb60-170">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="aeb60-171">[Verwendung von Platzhaltern](http://www.tldp.org/LDP/abs/html/globbingref.html) Muster werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="aeb60-171">[globbing](http://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="aeb60-172">`minify`: Geben Sie die Minimierung-Optionen für die Ausgabe.</span><span class="sxs-lookup"><span data-stu-id="aeb60-172">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="aeb60-173">**optionale**, *Standard: `minify: { enabled: true }`*</span><span class="sxs-lookup"><span data-stu-id="aeb60-173">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="aeb60-174">Konfigurationsoptionen sind pro ausgabedateityp verfügbar.</span><span class="sxs-lookup"><span data-stu-id="aeb60-174">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="aeb60-175">CSS Minifier</span><span class="sxs-lookup"><span data-stu-id="aeb60-175">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="aeb60-176">JavaScript Minifier</span><span class="sxs-lookup"><span data-stu-id="aeb60-176">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="aeb60-177">HTML Minifier</span><span class="sxs-lookup"><span data-stu-id="aeb60-177">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="aeb60-178">`includeInProject`: Flag zum angeben, ob die generierte Dateien zur Projektdatei hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="aeb60-178">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="aeb60-179">**optionale**, *Standard: false*</span><span class="sxs-lookup"><span data-stu-id="aeb60-179">**optional**, *default - false*</span></span>
* <span data-ttu-id="aeb60-180">`sourceMap`: Flag zum angeben, ob eine quellzuordnung für die gebündelte Datei generiert.</span><span class="sxs-lookup"><span data-stu-id="aeb60-180">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="aeb60-181">**optionale**, *Standard: false*</span><span class="sxs-lookup"><span data-stu-id="aeb60-181">**optional**, *default - false*</span></span>
* <span data-ttu-id="aeb60-182">`sourceMapRootPath`: Der Stammpfad für das Speichern der generierten Quelldatei für die Zuordnung.</span><span class="sxs-lookup"><span data-stu-id="aeb60-182">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="build-time-execution-of-bundling-and-minification"></a><span data-ttu-id="aeb60-183">Zeitpunkt der Erstellung der Ausführung der Bündelung und Minimierung</span><span class="sxs-lookup"><span data-stu-id="aeb60-183">Build-time execution of bundling and minification</span></span>

<span data-ttu-id="aeb60-184">Die [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet-Paket ermöglicht die Ausführung der Bündelung und Minimierung zum Zeitpunkt der Erstellung.</span><span class="sxs-lookup"><span data-stu-id="aeb60-184">The [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet package enables the execution of bundling and minification at build time.</span></span> <span data-ttu-id="aeb60-185">Fügt das Paket [MSBuild-Ziele](/visualstudio/msbuild/msbuild-targets) die Ausführung zu erstellen und bereinigen Uhrzeit.</span><span class="sxs-lookup"><span data-stu-id="aeb60-185">The package injects [MSBuild Targets](/visualstudio/msbuild/msbuild-targets) which run at build and clean time.</span></span> <span data-ttu-id="aeb60-186">Die *bundleconfig.json* Datei wird analysiert, im Buildprozess, um die Ausgabedateien, die basierend auf der definierten Konfiguration zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="aeb60-186">The *bundleconfig.json* file is analyzed by the build process to produce the output files based on the defined configuration.</span></span>

> [!NOTE]
> <span data-ttu-id="aeb60-187">BuildBundlerMinifier gehört zu einer Community getragenes Projekt auf GitHub für die keine Unterstützung von Microsoft bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="aeb60-187">BuildBundlerMinifier belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="aeb60-188">Sollten Probleme erfasst werden [hier](https://github.com/madskristensen/BundlerMinifier/issues).</span><span class="sxs-lookup"><span data-stu-id="aeb60-188">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="aeb60-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aeb60-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aeb60-190">Hinzufügen der *BuildBundlerMinifier* Paket Ihrem Projekt.</span><span class="sxs-lookup"><span data-stu-id="aeb60-190">Add the *BuildBundlerMinifier* package to your project.</span></span>

<span data-ttu-id="aeb60-191">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="aeb60-191">Build the project.</span></span> <span data-ttu-id="aeb60-192">Folgendes wird im Ausgabefenster angezeigt:</span><span class="sxs-lookup"><span data-stu-id="aeb60-192">The following appears in the Output window:</span></span>

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

<span data-ttu-id="aeb60-193">Bereinigen Sie das Projekt ein.</span><span class="sxs-lookup"><span data-stu-id="aeb60-193">Clean the project.</span></span> <span data-ttu-id="aeb60-194">Folgendes wird im Ausgabefenster angezeigt:</span><span class="sxs-lookup"><span data-stu-id="aeb60-194">The following appears in the Output window:</span></span>

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="aeb60-195">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="aeb60-195">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="aeb60-196">Hinzufügen der *BuildBundlerMinifier* Paket Ihrem Projekt:</span><span class="sxs-lookup"><span data-stu-id="aeb60-196">Add the *BuildBundlerMinifier* package to your project:</span></span>

```console
dotnet add package BuildBundlerMinifier
```

<span data-ttu-id="aeb60-197">Bei Verwendung von ASP.NET Core 1.x, die neu hinzugefügte Paket wiederherstellen:</span><span class="sxs-lookup"><span data-stu-id="aeb60-197">If using ASP.NET Core 1.x, restore the newly added package:</span></span>

```console
dotnet restore
```

<span data-ttu-id="aeb60-198">Erstellen Sie das Projekt:</span><span class="sxs-lookup"><span data-stu-id="aeb60-198">Build the project:</span></span>

```console
dotnet build
```

<span data-ttu-id="aeb60-199">Im folgenden wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="aeb60-199">The following appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

<span data-ttu-id="aeb60-200">Bereinigen Sie das Projekt:</span><span class="sxs-lookup"><span data-stu-id="aeb60-200">Clean the project:</span></span>

```console
dotnet clean
```

<span data-ttu-id="aeb60-201">Die folgende Ausgabe wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="aeb60-201">The following output appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Cleaning output from bundleconfig.json
  Bundler: Done cleaning output file from bundleconfig.json
```

---

## <a name="ad-hoc-execution-of-bundling-and-minification"></a><span data-ttu-id="aeb60-202">Ad-hoc-Ausführung von Bündelung und Minimierung</span><span class="sxs-lookup"><span data-stu-id="aeb60-202">Ad hoc execution of bundling and minification</span></span>

<span data-ttu-id="aeb60-203">Es ist möglich, die auf ad-hoc-Basis, die Bündelung und Minimierung Aufgaben ausführen, ohne das Projekt erstellen.</span><span class="sxs-lookup"><span data-stu-id="aeb60-203">It's possible to run the bundling and minification tasks on an ad hoc basis, without building the project.</span></span> <span data-ttu-id="aeb60-204">Hinzufügen der [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet-Paket Ihrem Projekt:</span><span class="sxs-lookup"><span data-stu-id="aeb60-204">Add the [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet package to your project:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> <span data-ttu-id="aeb60-205">BundlerMinifier.Core gehört zu einer Community getragenes Projekt auf GitHub für die keine Unterstützung von Microsoft bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="aeb60-205">BundlerMinifier.Core belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="aeb60-206">Sollten Probleme erfasst werden [hier](https://github.com/madskristensen/BundlerMinifier/issues).</span><span class="sxs-lookup"><span data-stu-id="aeb60-206">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="aeb60-207">Dieses Paket erweitert, die .NET Core-CLI zum Einschließen der *Dotnet-Bundle* Tool.</span><span class="sxs-lookup"><span data-stu-id="aeb60-207">This package extends the .NET Core CLI to include the *dotnet-bundle* tool.</span></span> <span data-ttu-id="aeb60-208">Der folgende Befehl kann im Fenster Paket-Manager-Konsole (PMC) oder in einer Befehlsshell ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="aeb60-208">The following command can be executed in the Package Manager Console (PMC) window or in a command shell:</span></span>

```console
dotnet bundle
```

> [!IMPORTANT]
> <span data-ttu-id="aeb60-209">NuGet-Paket-Manager fügt Abhängigkeiten hinzu, mit der \*.csproj-Datei als `<PackageReference />` Knoten.</span><span class="sxs-lookup"><span data-stu-id="aeb60-209">NuGet Package Manager adds dependencies to the \*.csproj file as `<PackageReference />` nodes.</span></span> <span data-ttu-id="aeb60-210">Die `dotnet bundle` Befehl wird mit .NET Core-CLI registriert nur, wenn eine `<DotNetCliToolReference />` Knoten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="aeb60-210">The `dotnet bundle` command is registered with the .NET Core CLI only when a `<DotNetCliToolReference />` node is used.</span></span> <span data-ttu-id="aeb60-211">Ändern Sie entsprechend der \*.csproj-Datei.</span><span class="sxs-lookup"><span data-stu-id="aeb60-211">Modify the \*.csproj file accordingly.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="aeb60-212">Hinzufügen von Dateien an den workflow</span><span class="sxs-lookup"><span data-stu-id="aeb60-212">Add files to workflow</span></span>

<span data-ttu-id="aeb60-213">Sehen Sie ein Beispiel, in dem ein zusätzlicher *custom.css* Datei hinzugefügt wird, wie den folgenden:</span><span class="sxs-lookup"><span data-stu-id="aeb60-213">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="aeb60-214">Zu verkleinernde *custom.css* und zu bündeln mit *"Site.CSS"* in einem *site.min.css* hinzufügen. den relativen Pfad zum *bundleconfig.json*:</span><span class="sxs-lookup"><span data-stu-id="aeb60-214">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="aeb60-215">Alternativ könnte die folgenden Platzhaltermuster verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="aeb60-215">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/*(*.css|!(*.min.css))"]
> ```
>
> <span data-ttu-id="aeb60-216">Dieses Platzhaltermuster für die Verwendung von entspricht allen CSS-Dateien und schließt die minimierte Dateien-Muster.</span><span class="sxs-lookup"><span data-stu-id="aeb60-216">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="aeb60-217">Erstellen Sie die Anwendung.</span><span class="sxs-lookup"><span data-stu-id="aeb60-217">Build the application.</span></span> <span data-ttu-id="aeb60-218">Open *site.min.css* und beachten Sie, dass den Inhalt des *custom.css* am Ende der Datei angefügt wird.</span><span class="sxs-lookup"><span data-stu-id="aeb60-218">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="aeb60-219">Umgebungsbasierte Bündelung und Minimierung</span><span class="sxs-lookup"><span data-stu-id="aeb60-219">Environment-based bundling and minification</span></span>

<span data-ttu-id="aeb60-220">Als bewährte Methode sollte die gebündelten und minimierten Dateien Ihrer App in einer produktionsumgebung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="aeb60-220">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="aeb60-221">Stellen die ursprünglichen Dateien zum einfacheren Debuggen der app, während der Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="aeb60-221">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="aeb60-222">Festlegen, welche Dateien sollen in Ihren Seiten mit den [Environment-Taghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in Ihren Ansichten.</span><span class="sxs-lookup"><span data-stu-id="aeb60-222">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="aeb60-223">Rendert die Environment-Taghilfsprogramm nur seinen Inhalt, bei der Ausführung in bestimmten [Umgebungen](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="aeb60-223">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="aeb60-224">Die folgenden `environment` Tag rendert die unverarbeiteten CSS-Dateien aus, wenn die Ausführung im der `Development` Umgebung:</span><span class="sxs-lookup"><span data-stu-id="aeb60-224">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="aeb60-225">Die folgenden `environment` Tag rendert die gebündelten und minimierten CSS-Dateien aus, wenn in einer Umgebung ausgeführt, außer `Development`.</span><span class="sxs-lookup"><span data-stu-id="aeb60-225">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="aeb60-226">Z. B. die Ausführung `Production` oder `Staging` löst das Rendern von diesen Stylesheets:</span><span class="sxs-lookup"><span data-stu-id="aeb60-226">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="aeb60-227">Nutzen Sie bundleconfig.json von Gulp</span><span class="sxs-lookup"><span data-stu-id="aeb60-227">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="aeb60-228">Es gibt Fälle, in denen Bündelung und Minimierung einer app-Workflow zusätzliche Verarbeitung erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="aeb60-228">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="aeb60-229">Beispiele für sind imageoptimierung, Cache-busting und Verarbeiten von CDN-Medienobjekt.</span><span class="sxs-lookup"><span data-stu-id="aeb60-229">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="aeb60-230">Um diese Anforderungen zu erfüllen, können Sie die Bündelung und Minimierung Workflow zum Verwenden von Gulp konvertieren.</span><span class="sxs-lookup"><span data-stu-id="aeb60-230">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="use-the-bundler--minifier-extension"></a><span data-ttu-id="aeb60-231">Verwenden Sie die Erweiterung Bundler & Minifier</span><span class="sxs-lookup"><span data-stu-id="aeb60-231">Use the Bundler & Minifier extension</span></span>

<span data-ttu-id="aeb60-232">Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) Erweiterung verarbeitet die Konvertierung in Gulp.</span><span class="sxs-lookup"><span data-stu-id="aeb60-232">The Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) extension handles the conversion to Gulp.</span></span>

> [!NOTE]
> <span data-ttu-id="aeb60-233">Die Erweiterung "Bundler & Minifier" gehört zu einer Community getragenes Projekt auf GitHub für die keine Unterstützung von Microsoft bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="aeb60-233">The Bundler & Minifier extension belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="aeb60-234">Sollten Probleme erfasst werden [hier](https://github.com/madskristensen/BundlerMinifier/issues).</span><span class="sxs-lookup"><span data-stu-id="aeb60-234">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="aeb60-235">Mit der rechten Maustaste die *bundleconfig.json* im Projektmappen-Explorer und wählen Sie **Bundler & Minifier** > **konvertieren zu Gulp...** :</span><span class="sxs-lookup"><span data-stu-id="aeb60-235">Right-click the *bundleconfig.json* file in Solution Explorer and select **Bundler & Minifier** > **Convert To Gulp...**:</span></span>

![Kontextmenüelement konvertieren zu Gulp](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

<span data-ttu-id="aeb60-237">Die *"gulpfile.js"* und *"Package.JSON"* Dateien werden dem Projekt hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="aeb60-237">The *gulpfile.js* and *package.json* files are added to the project.</span></span> <span data-ttu-id="aeb60-238">Die unterstützenden [Npm](https://www.npmjs.com/) Pakete aufgeführt, die der *"Package.JSON"* dateimodell `devDependencies` Abschnitt installiert sind.</span><span class="sxs-lookup"><span data-stu-id="aeb60-238">The supporting [npm](https://www.npmjs.com/) packages listed in the *package.json* file's `devDependencies` section are installed.</span></span>

<span data-ttu-id="aeb60-239">Führen Sie den folgenden Befehl in der PMC-Fenster als globale Abhängigkeit der Gulp-CLI zu installieren:</span><span class="sxs-lookup"><span data-stu-id="aeb60-239">Run the following command in the PMC window to install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="aeb60-240">Die *"gulpfile.js"* Datei liest die *bundleconfig.json* -Datei für die Eingaben, Ausgaben und Einstellungen.</span><span class="sxs-lookup"><span data-stu-id="aeb60-240">The *gulpfile.js* file reads the *bundleconfig.json* file for the inputs, outputs, and settings.</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a><span data-ttu-id="aeb60-241">Konvertieren Sie manuell</span><span class="sxs-lookup"><span data-stu-id="aeb60-241">Convert manually</span></span>

<span data-ttu-id="aeb60-242">Wenn Visual Studio bzw. die Erweiterung "Bundler & Minifier" nicht verfügbar sind, konvertieren Sie manuell ein.</span><span class="sxs-lookup"><span data-stu-id="aeb60-242">If Visual Studio and/or the Bundler & Minifier extension aren't available, convert manually.</span></span>

<span data-ttu-id="aeb60-243">Hinzufügen einer *"Package.JSON"* -Datei mit den folgenden `devDependencies`, um das Stammverzeichnis des Projekts:</span><span class="sxs-lookup"><span data-stu-id="aeb60-243">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="aeb60-244">Die `gulp-uglify` Modul ECMAScript (ES) 2015 unterstützt keine / ES6 und höher.</span><span class="sxs-lookup"><span data-stu-id="aeb60-244">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="aeb60-245">Installieren Sie [Gulp-terser](https://www.npmjs.com/package/gulp-terser) anstelle von `gulp-uglify` ES2015 verwenden / ES6 oder höher.</span><span class="sxs-lookup"><span data-stu-id="aeb60-245">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="aeb60-246">Installieren Sie die Abhängigkeiten mithilfe des folgenden Befehls auf der gleichen Ebene wie *"Package.JSON"* :</span><span class="sxs-lookup"><span data-stu-id="aeb60-246">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```console
npm i
```

<span data-ttu-id="aeb60-247">Installieren Sie das Gulp-CLI als globale Abhängigkeit:</span><span class="sxs-lookup"><span data-stu-id="aeb60-247">Install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="aeb60-248">Kopieren der *"gulpfile.js"* Datei unter dem Projektstamm:</span><span class="sxs-lookup"><span data-stu-id="aeb60-248">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="aeb60-249">Führen Sie Gulp-tasks</span><span class="sxs-lookup"><span data-stu-id="aeb60-249">Run Gulp tasks</span></span>

<span data-ttu-id="aeb60-250">Fügen Sie zum Auslösen des Gulp-Tasks Minimierung, bevor das Projekt in Visual Studio erstellt die folgenden [MSBuild-Ziel](/visualstudio/msbuild/msbuild-targets) der \*.csproj-Datei:</span><span class="sxs-lookup"><span data-stu-id="aeb60-250">To trigger the Gulp minification task before the project builds in Visual Studio, add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the \*.csproj file:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="aeb60-251">In diesem Beispiel werden alle Aufgaben in definiert die `MyPreCompileTarget` Ziel ausführen, bevor die vordefinierten `Build` Ziel.</span><span class="sxs-lookup"><span data-stu-id="aeb60-251">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="aeb60-252">Im Ausgabefenster von Visual Studio wird eine Ausgabe ähnlich der folgenden angezeigt:</span><span class="sxs-lookup"><span data-stu-id="aeb60-252">Output similar to the following appears in Visual Studio's Output window:</span></span>

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


## <a name="additional-resources"></a><span data-ttu-id="aeb60-253">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="aeb60-253">Additional resources</span></span>

* [<span data-ttu-id="aeb60-254">Verwenden von Grunt</span><span class="sxs-lookup"><span data-stu-id="aeb60-254">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="aeb60-255">Verwenden mehrerer Umgebungen</span><span class="sxs-lookup"><span data-stu-id="aeb60-255">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="aeb60-256">Taghilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="aeb60-256">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)

---
title: Bündelung und Minimierung statischer Ressourcen in ASP.NET Core
author: scottaddie
description: Hier erfahren Sie, wie Sie statische Ressourcen in einer ASP.NET Core-Webanwendung mithilfe von Bündelungs- und Minimierungsverfahren optimieren.
ms.author: scaddie
ms.custom: mvc
ms.date: 06/17/2019
uid: client-side/bundling-and-minification
ms.openlocfilehash: a7a5c40d6c31c4416212c02c1b491dd794f2a1d3
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78646783"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="f6925-103">Bündelung und Minimierung statischer Ressourcen in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f6925-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="f6925-104">Von [Scott Addie](https://twitter.com/Scott_Addie) und [David Pine](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="f6925-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="f6925-105">In diesem Artikel lernen Sie die Vorteile der Funktionen Bündelung und Minimierung sowie ihre Verwendung mit ASP.NET Core-Web-Apps kennen.</span><span class="sxs-lookup"><span data-stu-id="f6925-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="f6925-106">Was versteht man unter Bündelung und Minimierung?</span><span class="sxs-lookup"><span data-stu-id="f6925-106">What is bundling and minification</span></span>

<span data-ttu-id="f6925-107">Die Bündelung und Minimierung sind zwei Methoden zur Leistungsoptimierung, die Sie unabhängig voneinander in einer Web-App anwenden können.</span><span class="sxs-lookup"><span data-stu-id="f6925-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="f6925-108">Zusammengenommen führen diese Funktionen zu einer Leistungssteigerung, indem die Anzahl der Serveranforderungen sowie die Größe der angeforderten statischen Ressourcen reduziert werden.</span><span class="sxs-lookup"><span data-stu-id="f6925-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="f6925-109">Durch Bündelung und Minimierung wird in erster Linie die Ladezeit für die Anforderung der ersten Seite verkürzt.</span><span class="sxs-lookup"><span data-stu-id="f6925-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="f6925-110">Sobald eine Webseite angefordert wurde, werden die statischen Ressourcen (JavaScript, CSS und Bilder) vom Browser zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="f6925-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="f6925-111">Die Bündelung und Minimierung haben folglich keine Auswirkungen auf die Leistung, wenn dieselbe Seite (oder Seiten) auf derselben Website angefordert wird, die ihrerseits dieselben Ressourcen anfordert.</span><span class="sxs-lookup"><span data-stu-id="f6925-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="f6925-112">Wurde der Expires-Header für die Ressourcen nicht korrekt festgelegt und die Bündelungs- und Minimierungsfunktionen nicht verwendet, kennzeichnen die Aktualitätsheuristiken des Browsers die Ressourcen nach ein paar Tagen als veraltet.</span><span class="sxs-lookup"><span data-stu-id="f6925-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="f6925-113">Zusätzlich benötigt der Browser für jede Ressource eine Überprüfungsanforderung.</span><span class="sxs-lookup"><span data-stu-id="f6925-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="f6925-114">In solchen Fällen führt die Bündelung und Minimierung auch nach der Anforderung der ersten Seite zu einer Leistungsverbesserung.</span><span class="sxs-lookup"><span data-stu-id="f6925-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="f6925-115">Bündelung</span><span class="sxs-lookup"><span data-stu-id="f6925-115">Bundling</span></span>

<span data-ttu-id="f6925-116">Bei der Bündelung werden mehrere Dateien zu einer einzelnen Datei kombiniert.</span><span class="sxs-lookup"><span data-stu-id="f6925-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="f6925-117">Durch die Bündelung wird die zum Rendern einer Webressource, z. B. einer Webseite, erforderliche Anzahl von Serveranforderungen reduziert.</span><span class="sxs-lookup"><span data-stu-id="f6925-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="f6925-118">Sie können beliebig viele Einzelbündel erstellen, insbesondere für CSS, JavaScript usw. Je kleiner die Anzahl von Dateien, desto weniger HTTP-Anforderungen müssen zwischen Browser und Server oder vom Dienst verarbeitet werden, der Ihre Anwendung bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="f6925-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="f6925-119">Dadurch kann die Leistung des Ladevorgangs der ersten Seite gesteigert werden.</span><span class="sxs-lookup"><span data-stu-id="f6925-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="f6925-120">Minimierung</span><span class="sxs-lookup"><span data-stu-id="f6925-120">Minification</span></span>

<span data-ttu-id="f6925-121">Bei der Minimierung werden unnötige Zeichen aus dem Code entfernt, ohne dass die Funktionalität geändert wird.</span><span class="sxs-lookup"><span data-stu-id="f6925-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="f6925-122">Dadurch lässt sich die Größe der angeforderten Ressourcen (wie CSS, Bilder oder JavaScript-Dateien) deutlich reduzieren.</span><span class="sxs-lookup"><span data-stu-id="f6925-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="f6925-123">Häufiger Nebeneffekt einer Minimierung sind auf ein Zeichen verkürzte Variablennamen und entfernte Kommentare und Leerzeichen.</span><span class="sxs-lookup"><span data-stu-id="f6925-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="f6925-124">Betrachten Sie die folgende JavaScript-Funktion:</span><span class="sxs-lookup"><span data-stu-id="f6925-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="f6925-125">Durch Minimierung kann die Funktion auf folgenden Code verkürzt werden:</span><span class="sxs-lookup"><span data-stu-id="f6925-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="f6925-126">Zusätzlich zur Entfernung von Kommentaren und unnötigen Leerzeichen werden die folgenden Parameter und Variablennamen folgendermaßen umbenannt:</span><span class="sxs-lookup"><span data-stu-id="f6925-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="f6925-127">Ursprünglich</span><span class="sxs-lookup"><span data-stu-id="f6925-127">Original</span></span> | <span data-ttu-id="f6925-128">Umbenannt</span><span class="sxs-lookup"><span data-stu-id="f6925-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="f6925-129">Auswirkungen der Bündelung und Minimierung</span><span class="sxs-lookup"><span data-stu-id="f6925-129">Impact of bundling and minification</span></span>

<span data-ttu-id="f6925-130">In der folgenden Tabelle werden die Unterschiede bei den Ladezeiten für einzelne Ressourcen mit und ohne Bündelung und Minimierung veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="f6925-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="f6925-131">Aktion</span><span class="sxs-lookup"><span data-stu-id="f6925-131">Action</span></span> | <span data-ttu-id="f6925-132">Mit B/M</span><span class="sxs-lookup"><span data-stu-id="f6925-132">With B/M</span></span> | <span data-ttu-id="f6925-133">Ohne B/M</span><span class="sxs-lookup"><span data-stu-id="f6925-133">Without B/M</span></span> | <span data-ttu-id="f6925-134">Änderung</span><span class="sxs-lookup"><span data-stu-id="f6925-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="f6925-135">Dateianforderungen</span><span class="sxs-lookup"><span data-stu-id="f6925-135">File Requests</span></span>  | <span data-ttu-id="f6925-136">7</span><span class="sxs-lookup"><span data-stu-id="f6925-136">7</span></span>   | <span data-ttu-id="f6925-137">18</span><span class="sxs-lookup"><span data-stu-id="f6925-137">18</span></span>     | <span data-ttu-id="f6925-138">157 %</span><span class="sxs-lookup"><span data-stu-id="f6925-138">157%</span></span>
<span data-ttu-id="f6925-139">Übertragene KB</span><span class="sxs-lookup"><span data-stu-id="f6925-139">KB Transferred</span></span> | <span data-ttu-id="f6925-140">156</span><span class="sxs-lookup"><span data-stu-id="f6925-140">156</span></span> | <span data-ttu-id="f6925-141">264,68</span><span class="sxs-lookup"><span data-stu-id="f6925-141">264.68</span></span> | <span data-ttu-id="f6925-142">70 %</span><span class="sxs-lookup"><span data-stu-id="f6925-142">70%</span></span>
<span data-ttu-id="f6925-143">Ladezeit (ms)</span><span class="sxs-lookup"><span data-stu-id="f6925-143">Load Time (ms)</span></span> | <span data-ttu-id="f6925-144">885</span><span class="sxs-lookup"><span data-stu-id="f6925-144">885</span></span> | <span data-ttu-id="f6925-145">2360</span><span class="sxs-lookup"><span data-stu-id="f6925-145">2360</span></span>   | <span data-ttu-id="f6925-146">167 %</span><span class="sxs-lookup"><span data-stu-id="f6925-146">167%</span></span>

<span data-ttu-id="f6925-147">Browser arbeiten in Bezug auf HTTP-Anforderungsheader ziemlich ausführlich.</span><span class="sxs-lookup"><span data-stu-id="f6925-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="f6925-148">Die Gesamtmetrik der gesendeten Bytes ergibt bei der Bündelung eine deutliche Verringerung.</span><span class="sxs-lookup"><span data-stu-id="f6925-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="f6925-149">Auch die Ladezeit wird deutlich verbessert. Und dies obwohl das vorangegangene Beispiel lokal ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="f6925-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="f6925-150">Bei einer Übertragung der Ressourcen über ein Netzwerk können durch Bündelung und Minimierung noch größere Leistungssteigerungen erzielt werden.</span><span class="sxs-lookup"><span data-stu-id="f6925-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="f6925-151">Auswählen einer Bündelungs- und Minimierungsstrategie</span><span class="sxs-lookup"><span data-stu-id="f6925-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="f6925-152">Die MVC- und Razor Pages-Projektvorlagen enthalten eine einsatzbereite Bündelungs- und Minimierungslösung bestehend aus einer JSON-Konfigurationsdatei.</span><span class="sxs-lookup"><span data-stu-id="f6925-152">The MVC and Razor Pages project templates provide an out-of-the-box solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="f6925-153">Drittanbietertools, wie die [Grunt](xref:client-side/using-grunt)-Aufgabenausführung, führen dieselben Aufgaben auf etwas komplexere Weise aus.</span><span class="sxs-lookup"><span data-stu-id="f6925-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="f6925-154">Ein Drittanbietertool eignet sich hervorragend in Fällen, in denen Ihr Entwicklungsworkflow Verarbeitungsschritte erfordert, die über die Bündelung und Minimierung hinausgehen, wie etwa Linting und Bildoptimierung.</span><span class="sxs-lookup"><span data-stu-id="f6925-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="f6925-155">Bei der Bündelung und Minimierung zur Entwurfszeit werden die minimierten Dateien vor Bereitstellung der App erstellt.</span><span class="sxs-lookup"><span data-stu-id="f6925-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="f6925-156">Werden die Bündelung und Minimierung vor der Bereitstellung durchgeführt, kann die Serverauslastung reduziert werden.</span><span class="sxs-lookup"><span data-stu-id="f6925-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="f6925-157">Beachten Sie jedoch, dass eine Bündelung und Minimierung zur Entwurfszeit die Komplexität des Builds erhöht und auch nur mit statischen Dateien ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="f6925-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="f6925-158">Konfigurieren der Bündelung und Minimierung</span><span class="sxs-lookup"><span data-stu-id="f6925-158">Configure bundling and minification</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="f6925-159">In ASP.NET Core 2.0 oder früher enthalten die MVC- und Razor Pages-Projektvorlagen eine Konfigurationsdatei mit dem Namen *bundleconfig.json*, in der die Optionen für jedes Bündel definiert werden:</span><span class="sxs-lookup"><span data-stu-id="f6925-159">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="f6925-160">Fügen Sie ab ASP.NET Core 2.1 dem MVC- oder Razor Pages-Projektstamm eine neue JSON-Datei mit dem Namen *bundleconfig.json* hinzu.</span><span class="sxs-lookup"><span data-stu-id="f6925-160">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="f6925-161">Fügen Sie dieser Datei als Startpunkt den folgenden JSON-Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="f6925-161">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="f6925-162">In der Datei *bundleconfig.json* werden die Optionen für jedes Bündel definiert.</span><span class="sxs-lookup"><span data-stu-id="f6925-162">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="f6925-163">Im vorherigen Beispiel wird für die benutzerdefinierten JavaScript- (*wwwroot/js/site.js*) und Stylesheet-Dateien (*wwwroot/css/site.css*) eine einzelne Bündelkonfiguration definiert.</span><span class="sxs-lookup"><span data-stu-id="f6925-163">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="f6925-164">Zu den Konfigurationsoptionen gehören:</span><span class="sxs-lookup"><span data-stu-id="f6925-164">Configuration options include:</span></span>

* <span data-ttu-id="f6925-165">`outputFileName`: Name der Bündeldatei, die ausgegeben werden soll.</span><span class="sxs-lookup"><span data-stu-id="f6925-165">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="f6925-166">Kann einen relativen Pfad der Datei *bundleconfig.json* enthalten.</span><span class="sxs-lookup"><span data-stu-id="f6925-166">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="f6925-167">**erforderlich**</span><span class="sxs-lookup"><span data-stu-id="f6925-167">**required**</span></span>
* <span data-ttu-id="f6925-168">`inputFiles`: Array von Dateien, die gebündelt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f6925-168">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="f6925-169">Enthält relative Pfade zur Konfigurationsdatei.</span><span class="sxs-lookup"><span data-stu-id="f6925-169">These are relative paths to the configuration file.</span></span> <span data-ttu-id="f6925-170">**Optional**, \*ein leerer Wert erzeugt eine leere Ausgabedatei.</span><span class="sxs-lookup"><span data-stu-id="f6925-170">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="f6925-171">[Globmuster](https://www.tldp.org/LDP/abs/html/globbingref.html) werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="f6925-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="f6925-172">`minify`: Minimierungsoptionen für den Ausgabetyp.</span><span class="sxs-lookup"><span data-stu-id="f6925-172">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="f6925-173">**Optional**, *Standardwert: `minify: { enabled: true }`* .</span><span class="sxs-lookup"><span data-stu-id="f6925-173">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="f6925-174">Für jeden Ausgabedateityp stehen Konfigurationsoptionen zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="f6925-174">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="f6925-175">CSS-Minifier</span><span class="sxs-lookup"><span data-stu-id="f6925-175">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="f6925-176">JavaScript-Minifier</span><span class="sxs-lookup"><span data-stu-id="f6925-176">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="f6925-177">HTML-Minifier</span><span class="sxs-lookup"><span data-stu-id="f6925-177">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="f6925-178">`includeInProject`: Flag, das angibt, ob der Projektdatei generierte Dateien hinzugefügt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f6925-178">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="f6925-179">**Optional**, *Standardwert: FALSE*.</span><span class="sxs-lookup"><span data-stu-id="f6925-179">**optional**, *default - false*</span></span>
* <span data-ttu-id="f6925-180">`sourceMap`: Flag, das angibt, ob für die Bündeldatei eine Quellzuordnungsdatei generiert werden soll.</span><span class="sxs-lookup"><span data-stu-id="f6925-180">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="f6925-181">**Optional**, *Standardwert: FALSE*.</span><span class="sxs-lookup"><span data-stu-id="f6925-181">**optional**, *default - false*</span></span>
* <span data-ttu-id="f6925-182">`sourceMapRootPath`: Stammpfad zum Speichern der generierten Quellzuordnungsdatei.</span><span class="sxs-lookup"><span data-stu-id="f6925-182">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="build-time-execution-of-bundling-and-minification"></a><span data-ttu-id="f6925-183">Ausführen der Bündelung und Minimierung zur Buildzeit</span><span class="sxs-lookup"><span data-stu-id="f6925-183">Build-time execution of bundling and minification</span></span>

<span data-ttu-id="f6925-184">Mit dem NuGet-Paket [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) können Sie die Bündelung und Minimierung zur Buildzeit ausführen.</span><span class="sxs-lookup"><span data-stu-id="f6925-184">The [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet package enables the execution of bundling and minification at build time.</span></span> <span data-ttu-id="f6925-185">Durch das Paket werden [MSBuild-Ziele](/visualstudio/msbuild/msbuild-targets) eingefügt, die zum Zeitpunkt des Builds und der Bereinigung ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="f6925-185">The package injects [MSBuild Targets](/visualstudio/msbuild/msbuild-targets) which run at build and clean time.</span></span> <span data-ttu-id="f6925-186">Die Datei *bundleconfig.json* wird im Buildprozess analysiert und die Ausgabedateien basierend auf der definierten Konfiguration erstellt.</span><span class="sxs-lookup"><span data-stu-id="f6925-186">The *bundleconfig.json* file is analyzed by the build process to produce the output files based on the defined configuration.</span></span>

> [!NOTE]
> <span data-ttu-id="f6925-187">Das Paket „BuildBundlerMinifier“ gehört zu einem von der Community gesteuerten Projekt auf GitHub, für das Microsoft keine Unterstützung bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="f6925-187">BuildBundlerMinifier belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="f6925-188">Hilfe bei Problemen finden Sie [hier](https://github.com/madskristensen/BundlerMinifier/issues).</span><span class="sxs-lookup"><span data-stu-id="f6925-188">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6925-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6925-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f6925-190">Fügen Sie Ihrem Projekt das Paket *BuildBundlerMinifier* hinzu.</span><span class="sxs-lookup"><span data-stu-id="f6925-190">Add the *BuildBundlerMinifier* package to your project.</span></span>

<span data-ttu-id="f6925-191">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="f6925-191">Build the project.</span></span> <span data-ttu-id="f6925-192">Die folgende Ausgabe wird im Ausgabefenster angezeigt:</span><span class="sxs-lookup"><span data-stu-id="f6925-192">The following appears in the Output window:</span></span>

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

<span data-ttu-id="f6925-193">Bereinigen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="f6925-193">Clean the project.</span></span> <span data-ttu-id="f6925-194">Die folgende Ausgabe wird im Ausgabefenster angezeigt:</span><span class="sxs-lookup"><span data-stu-id="f6925-194">The following appears in the Output window:</span></span>

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-cli"></a>[<span data-ttu-id="f6925-195">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="f6925-195">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f6925-196">Fügen Sie Ihrem Projekt das Paket *BuildBundlerMinifier* hinzu:</span><span class="sxs-lookup"><span data-stu-id="f6925-196">Add the *BuildBundlerMinifier* package to your project:</span></span>

```dotnetcli
dotnet add package BuildBundlerMinifier
```

<span data-ttu-id="f6925-197">Wenn Sie ASP.NET Core 1.x verwenden, stellen Sie das neu hinzugefügte Paket wieder her:</span><span class="sxs-lookup"><span data-stu-id="f6925-197">If using ASP.NET Core 1.x, restore the newly added package:</span></span>

```dotnetcli
dotnet restore
```

<span data-ttu-id="f6925-198">Erstellen Sie das Projekt:</span><span class="sxs-lookup"><span data-stu-id="f6925-198">Build the project:</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="f6925-199">Die folgende Ausgabe wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="f6925-199">The following appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

<span data-ttu-id="f6925-200">Bereinigen Sie das Projekt:</span><span class="sxs-lookup"><span data-stu-id="f6925-200">Clean the project:</span></span>

```dotnetcli
dotnet clean
```

<span data-ttu-id="f6925-201">Die folgende Ausgabe wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="f6925-201">The following output appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Cleaning output from bundleconfig.json
  Bundler: Done cleaning output file from bundleconfig.json
```

---

## <a name="ad-hoc-execution-of-bundling-and-minification"></a><span data-ttu-id="f6925-202">Ausführen der Bündelung und Minimierung auf Ad-hoc-Basis</span><span class="sxs-lookup"><span data-stu-id="f6925-202">Ad hoc execution of bundling and minification</span></span>

<span data-ttu-id="f6925-203">Bündelungs- und Minimierungsaufgaben können auch ad hoc ausgeführt werden, ohne dass das Projekt erstellt werden muss.</span><span class="sxs-lookup"><span data-stu-id="f6925-203">It's possible to run the bundling and minification tasks on an ad hoc basis, without building the project.</span></span> <span data-ttu-id="f6925-204">Fügen Sie Ihrem Projekt das NuGet-Paket [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) hinzu:</span><span class="sxs-lookup"><span data-stu-id="f6925-204">Add the [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet package to your project:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> <span data-ttu-id="f6925-205">Das Paket „BundlerMinifier.Core“ gehört zu einem von der Community gesteuerten Projekt auf GitHub, für das Microsoft keine Unterstützung bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="f6925-205">BundlerMinifier.Core belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="f6925-206">Hilfe bei Problemen finden Sie [hier](https://github.com/madskristensen/BundlerMinifier/issues).</span><span class="sxs-lookup"><span data-stu-id="f6925-206">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="f6925-207">Mit diesem Paket wird die .NET Core-CLI erweitert, sodass sie anschließend das Tool *dotnet-bundle* enthält.</span><span class="sxs-lookup"><span data-stu-id="f6925-207">This package extends the .NET Core CLI to include the *dotnet-bundle* tool.</span></span> <span data-ttu-id="f6925-208">Sie können den folgenden Befehl im Fenster der Paket-Manager-Konsole (PMC) oder in der Befehlsshell ausführen:</span><span class="sxs-lookup"><span data-stu-id="f6925-208">The following command can be executed in the Package Manager Console (PMC) window or in a command shell:</span></span>

```dotnetcli
dotnet bundle
```

> [!IMPORTANT]
> <span data-ttu-id="f6925-209">Mit dem NuGet-Paket-Manager werden der Datei „\*.csproj“ Abhängigkeiten als `<PackageReference />`-Knoten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="f6925-209">NuGet Package Manager adds dependencies to the \*.csproj file as `<PackageReference />` nodes.</span></span> <span data-ttu-id="f6925-210">Der Befehl `dotnet bundle` wird nur bei Verwendung eines `<DotNetCliToolReference />`-Knotens bei der .NET Core-CLI registriert.</span><span class="sxs-lookup"><span data-stu-id="f6925-210">The `dotnet bundle` command is registered with the .NET Core CLI only when a `<DotNetCliToolReference />` node is used.</span></span> <span data-ttu-id="f6925-211">Ändern Sie die Datei „\*.csproj“ entsprechend ab.</span><span class="sxs-lookup"><span data-stu-id="f6925-211">Modify the \*.csproj file accordingly.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="f6925-212">Hinzufügen von Dateien zum Workflow</span><span class="sxs-lookup"><span data-stu-id="f6925-212">Add files to workflow</span></span>

<span data-ttu-id="f6925-213">Angenommen, Sie möchten eine zusätzliche *custom.css*-Datei hinzufügen, die etwa folgendermaßen aussieht:</span><span class="sxs-lookup"><span data-stu-id="f6925-213">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="f6925-214">Fügen Sie der Datei *bundleconfig.json* den relativen Pfad hinzu, um die Datei *custom.css* zu minimieren und zusammen mit *site.css* in die Datei *site.min.css* zu bündeln:</span><span class="sxs-lookup"><span data-stu-id="f6925-214">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="f6925-215">Alternativ dazu können Sie auch das folgende Globmuster verwenden:</span><span class="sxs-lookup"><span data-stu-id="f6925-215">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> <span data-ttu-id="f6925-216">Dieses Globmuster stimmt mit allen CSS-Dateien überein und schließt das minierte Dateimuster aus.</span><span class="sxs-lookup"><span data-stu-id="f6925-216">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="f6925-217">Erstellen Sie die Anwendung.</span><span class="sxs-lookup"><span data-stu-id="f6925-217">Build the application.</span></span> <span data-ttu-id="f6925-218">Öffnen Sie die Datei *site.min.css*. Dabei werden Sie erkennen, dass der Inhalt von *custom.css* an das Ende der Datei angefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="f6925-218">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="f6925-219">Umgebungsbasierte Bündelung und Minimierung</span><span class="sxs-lookup"><span data-stu-id="f6925-219">Environment-based bundling and minification</span></span>

<span data-ttu-id="f6925-220">Als bewährte Methode wird empfohlen, die gebündelten und minimierten Dateien Ihrer App in einer Produktionsumgebung zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="f6925-220">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="f6925-221">Während der Entwicklung wird das Debuggen der App durch die Originaldateien erleichtert.</span><span class="sxs-lookup"><span data-stu-id="f6925-221">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="f6925-222">Geben Sie mithilfe des [Umgebungstaghilfsprogramms](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in Ihren Ansichten die Dateien an, die in Ihre Seiten einbezogen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f6925-222">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="f6925-223">Das Umgebungstaghilfsprogramm rendert die Inhalte nur, wenn es in bestimmten [Umgebungen](xref:fundamentals/environments) ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="f6925-223">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="f6925-224">Das folgende `environment`-Tag rendert die nicht verarbeiteten CSS-Dateien bei der Ausführung in einer `Development`-Umgebung:</span><span class="sxs-lookup"><span data-stu-id="f6925-224">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="f6925-225">Das folgende `environment`-Tag rendert die gebündelten und minimierten CSS-Dateien nur bei der Ausführung in einer Umgebung, die keine `Development`-Umgebung darstellt.</span><span class="sxs-lookup"><span data-stu-id="f6925-225">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="f6925-226">Die Ausführung in einer `Production`- oder `Staging`-Umgebung löst beispielsweise das Rendern der folgenden Stylesheets aus:</span><span class="sxs-lookup"><span data-stu-id="f6925-226">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="f6925-227">Verarbeiten der Datei „bundleconfig.json“ mit Gulp</span><span class="sxs-lookup"><span data-stu-id="f6925-227">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="f6925-228">In einigen Fällen erfordert der Workflow für die Bündelung und Minimierung einer App zusätzliche Verarbeitungsschritte.</span><span class="sxs-lookup"><span data-stu-id="f6925-228">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="f6925-229">Dazu gehören Bildoptimierung, Cache-Busting und die Verarbeitung von CDN-Ressourcen.</span><span class="sxs-lookup"><span data-stu-id="f6925-229">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="f6925-230">Zur Durchführung dieser Aufgaben können Sie den Workflow für die Bündelung und Minimierung so konvertieren, dass Gulp verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f6925-230">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="use-the-bundler--minifier-extension"></a><span data-ttu-id="f6925-231">Verwenden der Erweiterung „Bundler & Minifier“</span><span class="sxs-lookup"><span data-stu-id="f6925-231">Use the Bundler & Minifier extension</span></span>

<span data-ttu-id="f6925-232">Die Konvertierung in Gulp wird durch die Visual Studio-Erweiterung [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="f6925-232">The Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) extension handles the conversion to Gulp.</span></span>

> [!NOTE]
> <span data-ttu-id="f6925-233">Die Erweiterung „Bundler & Minifier“ gehört zu einem von der Community gesteuerten Projekt auf GitHub, für das Microsoft keine Unterstützung bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="f6925-233">The Bundler & Minifier extension belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="f6925-234">Hilfe bei Problemen finden Sie [hier](https://github.com/madskristensen/BundlerMinifier/issues).</span><span class="sxs-lookup"><span data-stu-id="f6925-234">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="f6925-235">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Datei *bundleconfig.json*, und klicken Sie anschließend auf **Bundler & Minifier** > **In Gulp konvertieren...** :</span><span class="sxs-lookup"><span data-stu-id="f6925-235">Right-click the *bundleconfig.json* file in Solution Explorer and select **Bundler & Minifier** > **Convert To Gulp...**:</span></span>

![Kontextmenüelement „In Gulp konvertieren“](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

<span data-ttu-id="f6925-237">Die Dateien *gulpfile.js* und *package.json* werden dem Projekt hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="f6925-237">The *gulpfile.js* and *package.json* files are added to the project.</span></span> <span data-ttu-id="f6925-238">Die im Bereich `devDependencies` der Datei *package.json* aufgelisteten unterstützenden [npm](https://www.npmjs.com/)-Pakete werden installiert.</span><span class="sxs-lookup"><span data-stu-id="f6925-238">The supporting [npm](https://www.npmjs.com/) packages listed in the *package.json* file's `devDependencies` section are installed.</span></span>

<span data-ttu-id="f6925-239">Führen Sie im PMC-Fenster folgenden Befehl aus, um die Gulp-CLI als globale Abhängigkeit zu installieren:</span><span class="sxs-lookup"><span data-stu-id="f6925-239">Run the following command in the PMC window to install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="f6925-240">Die Datei *gulpfile.js* liest die Datei *bundleconfig.json*, um die Eingaben, Ausgaben und Einstellungen zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="f6925-240">The *gulpfile.js* file reads the *bundleconfig.json* file for the inputs, outputs, and settings.</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a><span data-ttu-id="f6925-241">Manuelles Konvertieren</span><span class="sxs-lookup"><span data-stu-id="f6925-241">Convert manually</span></span>

<span data-ttu-id="f6925-242">Sind Visual Studio und/oder die Erweiterung „Bundler & Minifier“ nicht verfügbar, können Sie die Konvertierung manuell durchführen.</span><span class="sxs-lookup"><span data-stu-id="f6925-242">If Visual Studio and/or the Bundler & Minifier extension aren't available, convert manually.</span></span>

<span data-ttu-id="f6925-243">Fügen Sie dem Projektstamm eine *package.json*-Datei mit den folgenden `devDependencies` hinzu:</span><span class="sxs-lookup"><span data-stu-id="f6925-243">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="f6925-244">ECMAScript (ES) 2015/ES6 und höher wird vom `gulp-uglify`-Modul nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="f6925-244">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="f6925-245">Installieren Sie anstelle von `gulp-uglify` das Plug-In [gulp-terser](https://www.npmjs.com/package/gulp-terser), wenn Sie ES2015/ES6 oder höher verwenden möchten.</span><span class="sxs-lookup"><span data-stu-id="f6925-245">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="f6925-246">Installieren Sie die Abhängigkeiten, indem Sie den folgenden Befehl auf der Ebene von *package.json* ausführen:</span><span class="sxs-lookup"><span data-stu-id="f6925-246">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```console
npm i
```

<span data-ttu-id="f6925-247">Installieren Sie die Gulp-CLI als globale Abhängigkeit:</span><span class="sxs-lookup"><span data-stu-id="f6925-247">Install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="f6925-248">Kopieren Sie die folgende *gulpfile.js*-Datei in den Projektstamm:</span><span class="sxs-lookup"><span data-stu-id="f6925-248">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="f6925-249">Ausführen von Gulp-Aufgaben</span><span class="sxs-lookup"><span data-stu-id="f6925-249">Run Gulp tasks</span></span>

<span data-ttu-id="f6925-250">Fügen Sie der Datei „\*.csproj“ das folgende [MSBuild-Ziel](/visualstudio/msbuild/msbuild-targets) hinzu, um die Minimierungsaufgabe in Gulp vor dem Erstellen des Projekts in Visual Studio auszulösen:</span><span class="sxs-lookup"><span data-stu-id="f6925-250">To trigger the Gulp minification task before the project builds in Visual Studio, add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the \*.csproj file:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="f6925-251">In diesem Beispiel werden alle im Ziel `MyPreCompileTarget` definierten Aufgaben vor dem vordefinierten `Build`-Ziel ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="f6925-251">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="f6925-252">Etwa folgende Ausgabe wird im Visual Studio-Ausgabefenster angezeigt:</span><span class="sxs-lookup"><span data-stu-id="f6925-252">Output similar to the following appears in Visual Studio's Output window:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="f6925-253">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f6925-253">Additional resources</span></span>

* [<span data-ttu-id="f6925-254">Verwenden von Grunt</span><span class="sxs-lookup"><span data-stu-id="f6925-254">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="f6925-255">Verwenden mehrerer Umgebungen</span><span class="sxs-lookup"><span data-stu-id="f6925-255">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="f6925-256">Taghilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="f6925-256">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)

---
title: Bündelung und Minimierung von statischen Objekten in ASP.NET Core
author: scottaddie
description: Erfahren Sie, wie Sie statische Ressourcen in einer ASP.NET Core-Webanwendung durch Anwenden von bündelungs-und minimierungsverfahren zu optimieren.
ms.author: scaddie
ms.custom: mvc
ms.date: 05/10/2019
uid: client-side/bundling-and-minification
ms.openlocfilehash: ba01d365a25dfbd13fed89263d7489b2ce2a8771
ms.sourcegitcommit: ffe3ed7921ec6c7c70abaac1d10703ec9a43374c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65535933"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>Bündelung und Minimierung von statischen Objekten in ASP.NET Core

Durch [Scott Addie](https://twitter.com/Scott_Addie) und [David Kiefer](https://twitter.com/davidpine7)

Dieser Artikel beschreibt die Vorteile der Anwendung Bündelung und Minimierung, einschließlich, wie diese Features in ASP.NET Core-Web-apps verwendet werden können.

## <a name="what-is-bundling-and-minification"></a>Was ist die Bündelung und Minimierung

Bündelung und Minimierung sind zwei unterschiedliche leistungsoptimierungen, die Sie in einer Web-app anwenden können. Zusammen verwendet werden, Verbessern der Bündelung und Minimierung Leistung durch Reduzierung der Anzahl von serveranforderungen und Verringern der Größe der angeforderten statischen Objekten.

Bündelung und Minimierung wird in erster Linie die erste Anforderung Seitenladezeit verbessern. Nach einer Webseite angefordert wurde, speichert der Browser die statischen Ressourcen (JavaScript, CSS und Bilder). Folglich leistungsverbesserung nicht Bündelung und Minimierung beim Anfordern von derselben Seite oder Seiten am selben Standort die gleichen Ressourcen anfordern. Wenn der Ablauf Header ist nicht korrekt Ressourcen festgelegt und wenn Bündelung und Minimierung wird nicht verwendet, im Browser auf die Aktualität Heuristik markieren Sie die Ressourcen veraltete nach einigen Tagen. Darüber hinaus muss der Browser eine Anforderung zur abonnementüberprüfung für jedes Medienobjekt. In diesem Fall geben die Bündelung und Minimierung eine besseren Leistung auch nach der die erste Seitenanforderung.

### <a name="bundling"></a>Bündeln

Bündelung werden mehrere Dateien in einer einzelnen Datei kombiniert. Bündelung reduziert die Anzahl der serveranforderungen, die erforderlich sind, um eine Web-Ressource, z. B. eine Webseite zu rendern. Sie können eine beliebige Anzahl von den einzelnen Paketen speziell für CSS, JavaScript usw. erstellen. Weniger Dateien bedeutet weniger HTTP-Anforderungen vom Browser an den Server oder aus dem Dienst, der Ihre Anwendung bereitstellen. Dies führt zu verbessert die ladeleistung für die ersten Seiten.

### <a name="minification"></a>Minimierung

Minimierung werden unnötige Zeichen aus Code ohne dabei Funktionalität zu entfernt. Das Ergebnis ist eine wesentliche Verringerung angeforderten Assets (z. B. CSS, Bilder und JavaScript-Dateien). Allgemeine Nebeneffekte der Minimierung enthalten, verkürzen Sie die Variablennamen, um ein Zeichen, und Entfernen von Kommentaren und unnötiger Leerraum.

Betrachten Sie die folgende JavaScript-Funktion:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

Minimierung reduziert sich die Funktion auf Folgendes:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

Zusätzlich zu entfernen, die Kommentare und unnötiger Leerraum, wurden die folgenden Parameter und Variablen Namen folgendermaßen umbenannt:

Ursprünglich | Umbenannt
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a>Auswirkungen der Bündelung und Minimierung

In der folgende Tabelle werden Unterschiede zwischen einzeln Laden von Medienobjekten und mithilfe von Bündelung und Minimierung beschrieben:

Aktion | Mit B/Min. | Ohne B/Min. | Änderung
--- | :---: | :---: | :---:
Dateianforderungen  | 7   | 18     | 157%
KB übertragen | 156 | 264.68 | 70%
Ladezeit (ms) | 885 | 2360   | 167%

Browser sind recht ausführlich im Hinblick auf HTTP-Anforderungsheadern. Die Gesamtzahl der Bytes gesendet, dass die Metrik zu eine erhebliche Reduzierung beim bündeln angezeigt wurde. Die Ladezeit zeigt eine bedeutende Verbesserung, jedoch in diesem Beispiel wird lokal ausgeführt wurde. Deutlichere leistungsverbesserungen werden realisiert werden, wenn Bündelung und Minimierung mit Ressourcen verwenden, die über ein Netzwerk übertragen.

## <a name="choose-a-bundling-and-minification-strategy"></a>Wählen Sie eine Strategie für die Bündelung und Minimierung

Die Razor-Seiten und MVC-Projektvorlagen bieten eine Out-of-the-Box-Lösung für die Bündelung und Minimierung, bestehend aus einer JSON-Konfigurationsdatei an. Drittanbieter-tools, z. B. die [Grunt](xref:client-side/using-grunt) task Runner, die gleichen Aufgaben mit etwas mehr Komplexität. Ein Tool eines Drittanbieters ist hervorragend aus, wenn Ihrem Entwicklungsworkflow Verarbeitung auf über die Bündelung und Minimierung erfordert&mdash;wie Linting und Image. Verwenden Sie während der Entwurfszeit Bündelung und Minimierung, werden die minimierten Dateien vor der Bereitstellung von der app erstellt. Bündeln und Minimieren der vor der Bereitstellung bietet den Vorteil des reduzierten Last auf. Allerdings es ist wichtig zu erkennen, während der Entwurfszeit Bündelung und Minimierung erhöht die Komplexität des Builds ab und funktioniert nur mit statischen Dateien.

## <a name="configure-bundling-and-minification"></a>Konfigurieren der Bündelung und Minimierung

::: moniker range="<= aspnetcore-2.0"

Geben Sie die Projektvorlagen von MVC und Razor Pages in ASP.NET Core 2.0 oder früher, eine *bundleconfig.json* Konfigurationsdatei, die Optionen für jedes Paket definiert:

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

In ASP.NET Core 2.1 oder höher, fügen Sie eine neue JSON-Datei, mit dem Namen *bundleconfig.json*, um das Stammverzeichnis des Projekts MVC oder Razor-Seiten. Gehören Sie den folgenden JSON-Code in dieser Datei als Ausgangspunkt:

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

Die *bundleconfig.json* -Datei definiert die Optionen für jedes Paket. Im vorherigen Beispiel eine Paket-Konfiguration für die benutzerdefinierten JavaScript-Code definiert ist (*wwwroot/js/site.js*) und Stylesheet (*wwwroot/css/site.css*) Dateien.

Konfigurationsoptionen sind verfügbar:

* `outputFileName`: Der Name der Bundle-Datei ausgegeben. Einen relativen Pfad darf die *bundleconfig.json* Datei. **required**
* `inputFiles`: Ein Array von Dateien zu bündeln. Dies sind die relativen Pfade der Konfigurationsdatei hinzu. **optionale**, * ein leerer Wert in einer leeren Ausgabedatei führt. [Verwendung von Platzhaltern](http://www.tldp.org/LDP/abs/html/globbingref.html) Muster werden unterstützt.
* `minify`: Geben Sie die Minimierung-Optionen für die Ausgabe. **optionale**, *Standard: `minify: { enabled: true }`*
  * Konfigurationsoptionen sind pro ausgabedateityp verfügbar.
    * [CSS Minifier](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [JavaScript Minifier](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [HTML Minifier](https://github.com/madskristensen/BundlerMinifier/wiki)
* `includeInProject`: Flag zum angeben, ob die generierte Dateien zur Projektdatei hinzufügen. **optionale**, *Standard: false*
* `sourceMap`: Flag zum angeben, ob eine quellzuordnung für die gebündelte Datei generiert. **optionale**, *Standard: false*
* `sourceMapRootPath`: Der Stammpfad für das Speichern der generierten Quelldatei für die Zuordnung.

## <a name="build-time-execution-of-bundling-and-minification"></a>Zeitpunkt der Erstellung der Ausführung der Bündelung und Minimierung

Die [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet-Paket ermöglicht die Ausführung der Bündelung und Minimierung zum Zeitpunkt der Erstellung. Fügt das Paket [MSBuild-Ziele](/visualstudio/msbuild/msbuild-targets) die Ausführung zu erstellen und bereinigen Uhrzeit. Die *bundleconfig.json* Datei wird analysiert, im Buildprozess, um die Ausgabedateien, die basierend auf der definierten Konfiguration zu erstellen.

> [!NOTE]
> BuildBundlerMinifier gehört zu einer Community getragenes Projekt auf GitHub für die keine Unterstützung von Microsoft bereitstellt. Sollten Probleme erfasst werden [hier](https://github.com/madskristensen/BundlerMinifier/issues).

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Hinzufügen der *BuildBundlerMinifier* Paket Ihrem Projekt.

Erstellen Sie das Projekt. Folgendes wird im Ausgabefenster angezeigt:

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

Bereinigen Sie das Projekt ein. Folgendes wird im Ausgabefenster angezeigt:

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Hinzufügen der *BuildBundlerMinifier* Paket Ihrem Projekt:

```console
dotnet add package BuildBundlerMinifier
```

Bei Verwendung von ASP.NET Core 1.x, die neu hinzugefügte Paket wiederherstellen:

```console
dotnet restore
```

Erstellen Sie das Projekt:

```console
dotnet build
```

Im folgenden wird angezeigt:

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

Bereinigen Sie das Projekt:

```console
dotnet clean
```

Die folgende Ausgabe wird angezeigt:

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Cleaning output from bundleconfig.json
  Bundler: Done cleaning output file from bundleconfig.json
```

---

## <a name="ad-hoc-execution-of-bundling-and-minification"></a>Ad-hoc-Ausführung von Bündelung und Minimierung

Es ist möglich, die auf ad-hoc-Basis, die Bündelung und Minimierung Aufgaben ausführen, ohne das Projekt erstellen. Hinzufügen der [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet-Paket Ihrem Projekt:

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> BundlerMinifier.Core gehört zu einer Community getragenes Projekt auf GitHub für die keine Unterstützung von Microsoft bereitstellt. Sollten Probleme erfasst werden [hier](https://github.com/madskristensen/BundlerMinifier/issues).

Dieses Paket erweitert, die .NET Core-CLI zum Einschließen der *Dotnet-Bundle* Tool. Der folgende Befehl kann im Fenster Paket-Manager-Konsole (PMC) oder in einer Befehlsshell ausgeführt werden:

```console
dotnet bundle
```

> [!IMPORTANT]
> NuGet-Paket-Manager fügt Abhängigkeiten hinzu, mit der *.csproj-Datei als `<PackageReference />` Knoten. Die `dotnet bundle` Befehl wird mit .NET Core-CLI registriert nur, wenn eine `<DotNetCliToolReference />` Knoten verwendet wird. Ändern Sie entsprechend der *.csproj-Datei.

## <a name="add-files-to-workflow"></a>Hinzufügen von Dateien an den workflow

Sehen Sie ein Beispiel, in dem ein zusätzlicher *custom.css* Datei hinzugefügt wird, wie den folgenden:

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

Zu verkleinernde *custom.css* und zu bündeln mit *"Site.CSS"* in einem *site.min.css* hinzufügen. den relativen Pfad zum *bundleconfig.json*:

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> Alternativ könnte die folgenden Platzhaltermuster verwendet werden:
>
> ```json
> "inputFiles": ["wwwroot/**/*(*.css|!(*.min.css))"]
> ```
>
> Dieses Platzhaltermuster für die Verwendung von entspricht allen CSS-Dateien und schließt die minimierte Dateien-Muster.

Erstellen Sie die Anwendung. Open *site.min.css* und beachten Sie, dass den Inhalt des *custom.css* am Ende der Datei angefügt wird.

## <a name="environment-based-bundling-and-minification"></a>Umgebungsbasierte Bündelung und Minimierung

Als bewährte Methode sollte die gebündelten und minimierten Dateien Ihrer App in einer produktionsumgebung verwendet werden. Stellen die ursprünglichen Dateien zum einfacheren Debuggen der app, während der Entwicklung.

Festlegen, welche Dateien sollen in Ihren Seiten mit den [Environment-Taghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in Ihren Ansichten. Rendert die Environment-Taghilfsprogramm nur seinen Inhalt, bei der Ausführung in bestimmten [Umgebungen](xref:fundamentals/environments).

Die folgenden `environment` Tag rendert die unverarbeiteten CSS-Dateien aus, wenn die Ausführung im der `Development` Umgebung:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

Die folgenden `environment` Tag rendert die gebündelten und minimierten CSS-Dateien aus, wenn in einer Umgebung ausgeführt, außer `Development`. Z. B. die Ausführung `Production` oder `Staging` löst das Rendern von diesen Stylesheets:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a>Nutzen Sie bundleconfig.json von Gulp

Es gibt Fälle, in denen Bündelung und Minimierung einer app-Workflow zusätzliche Verarbeitung erforderlich ist. Beispiele für sind imageoptimierung, Cache-busting und Verarbeiten von CDN-Medienobjekt. Um diese Anforderungen zu erfüllen, können Sie die Bündelung und Minimierung Workflow zum Verwenden von Gulp konvertieren.

### <a name="use-the-bundler--minifier-extension"></a>Verwenden Sie die Erweiterung Bundler & Minifier

Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) Erweiterung verarbeitet die Konvertierung in Gulp.

> [!NOTE]
> Die Erweiterung "Bundler & Minifier" gehört zu einer Community getragenes Projekt auf GitHub für die keine Unterstützung von Microsoft bereitstellt. Sollten Probleme erfasst werden [hier](https://github.com/madskristensen/BundlerMinifier/issues).

Mit der rechten Maustaste die *bundleconfig.json* im Projektmappen-Explorer und wählen Sie **Bundler & Minifier** > **konvertieren zu Gulp...** :

![Kontextmenüelement konvertieren zu Gulp](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

Die *"gulpfile.js"* und *"Package.JSON"* Dateien werden dem Projekt hinzugefügt. Die unterstützenden [Npm](https://www.npmjs.com/) Pakete aufgeführt, die der *"Package.JSON"* dateimodell `devDependencies` Abschnitt installiert sind.

Führen Sie den folgenden Befehl in der PMC-Fenster als globale Abhängigkeit der Gulp-CLI zu installieren:

```console
npm i -g gulp-cli
```

Die *"gulpfile.js"* Datei liest die *bundleconfig.json* -Datei für die Eingaben, Ausgaben und Einstellungen.

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a>Konvertieren Sie manuell

Wenn Visual Studio bzw. die Erweiterung "Bundler & Minifier" nicht verfügbar sind, konvertieren Sie manuell ein.

Hinzufügen einer *"Package.JSON"* -Datei mit den folgenden `devDependencies`, um das Stammverzeichnis des Projekts:

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

Installieren Sie die Abhängigkeiten mithilfe des folgenden Befehls auf der gleichen Ebene wie *"Package.JSON"*:

```console
npm i
```

Installieren Sie das Gulp-CLI als globale Abhängigkeit:

```console
npm i -g gulp-cli
```

Kopieren der *"gulpfile.js"* Datei unter dem Projektstamm:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a>Führen Sie Gulp-tasks

Fügen Sie zum Auslösen des Gulp-Tasks Minimierung, bevor das Projekt in Visual Studio erstellt die folgenden [MSBuild-Ziel](/visualstudio/msbuild/msbuild-targets) der *.csproj-Datei:

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

In diesem Beispiel werden alle Aufgaben in definiert die `MyPreCompileTarget` Ziel ausführen, bevor die vordefinierten `Build` Ziel. Im Ausgabefenster von Visual Studio wird eine Ausgabe ähnlich der folgenden angezeigt:

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


## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwenden von Grunt](xref:client-side/using-grunt)
* [Verwenden mehrerer Umgebungen](xref:fundamentals/environments)
* [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro)

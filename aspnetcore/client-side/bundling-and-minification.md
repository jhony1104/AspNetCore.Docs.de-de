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
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>Bündel und minimieren statischer Ressourcen in ASP.net Core

Von [Scott Adder](https://twitter.com/Scott_Addie) und [David Pine](https://twitter.com/davidpine7)

In diesem Artikel werden die Vorteile der Anwendung von Bündelung und Minimierung erläutert, einschließlich der Art und Weise, wie diese Funktionen mit ASP.net Core-Web-Apps verwendet werden können.

## <a name="what-is-bundling-and-minification"></a>Was ist Bündelung und Minimierung?

Bündelung und Minimierung sind zwei unterschiedliche Leistungsoptimierungen, die Sie in einer Web-App anwenden können. Die gemeinsame Verwendung, Bündelung und Minimierung verbessern die Leistung, indem die Anzahl der Serveranforderungen reduziert und die Größe der angeforderten statischen Ressourcen verringert wird.

Bündelung und Minimierung verbessern hauptsächlich die Ladezeit für die erste Seiten Anforderung. Nachdem eine Webseite angefordert wurde, speichert der Browser die statischen Assets (JavaScript, CSS und Images) zwischen. Folglich verbessern die Bündelung und Minimierung die Leistung nicht, wenn die gleiche Seite oder Seiten auf derselben Website angefordert werden, die dieselben Assets anfordert. Wenn der Ablauf Header für die Objekte nicht ordnungsgemäß festgelegt ist und Bündelung und Minimierung nicht verwendet wird, kennzeichnet die Heuristik des Browsers die Assets nach einigen Tagen. Außerdem erfordert der Browser eine Validierungs Anforderung für jedes Asset. In diesem Fall bieten Bündelung und Minimierung eine Leistungsverbesserung, auch nach der ersten Seiten Anforderung.

### <a name="bundling"></a>Anbietet

Bei der Bündelung werden mehrere Dateien zu einer einzelnen Datei kombiniert. Die Bündelung reduziert die Anzahl der Serveranforderungen, die zum Rendering eines webassets erforderlich sind, z. b. eine Webseite. Sie können eine beliebige Anzahl einzelner Bündel speziell für CSS, JavaScript usw. erstellen. Weniger Dateien bedeuten weniger HTTP-Anforderungen vom Browser an den Server oder den Dienst, der Ihre Anwendung bereitstellt. Dies führt zu einer verbesserten Leistung der ersten Seiten Auslastung.

### <a name="minification"></a>Minimierung

Die minifizierung entfernt unnötige Zeichen aus dem Code, ohne die Funktionalität zu ändern. Das Ergebnis ist eine signifikante Größenreduzierung der angeforderten Assets (z. b. CSS, Bilder und JavaScript-Dateien). Zu den allgemeinen Nebeneffekten der Minimierung zählen das verkürzen von Variablennamen in ein Zeichen und das Entfernen von Kommentaren und unnötigen Leerraum.

Beachten Sie die folgende JavaScript-Funktion:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

Die minifizierung reduziert die-Funktion wie folgt:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

Zusätzlich zum Entfernen der Kommentare und unnötiger Leerzeichen wurden die folgenden Parameter-und Variablennamen wie folgt umbenannt:

Ursprünglich | Umbenannt
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a>Auswirkungen von Bündelung und Minimierung

In der folgenden Tabelle werden die Unterschiede zwischen dem individuellen Laden von Assets und der Verwendung von Bündelung und Minimierung erläutert:

-Aktion | Mit B/M | Ohne B/M | Anwendungseinstellungen
--- | :---: | :---: | :---:
Datei Anforderungen  | 7   | 18     | 157%
KB übertragen | 156 | 264.68 | 70%
Ladezeit (MS) | 885 | 2360   | 167%

Browser sind im Hinblick auf HTTP-Anforderungs Header recht ausführlich. Die Metrik "Insgesamt gesendete Bytes" hat beim bündeln eine erhebliche Reduzierung erkannt. Die Ladezeit zeigt eine bedeutende Verbesserung, dieses Beispiel wird jedoch lokal ausgeführt. Höhere Leistungssteigerungen werden erzielt, wenn Bündelung und Minimierung mit Assets verwendet werden, die über ein Netzwerk übertragen werden.

## <a name="choose-a-bundling-and-minification-strategy"></a>Auswählen einer Strategie für Bündelung und Minimierung

Die MVC-und Razor Pages-Projektvorlagen stellen eine sofort einstufige Lösung für die Bündelung und Minimierung dar, die aus einer JSON-Konfigurationsdatei besteht. Drittanbieter Tools, wie z. b. die [grunt](xref:client-side/using-grunt) -Aufgabenausführung, erfüllen die gleichen Aufgaben mit etwas mehr Komplexität. Ein Drittanbieter Tool eignet sich hervorragend, wenn Ihr Entwicklungs Workflow die Verarbeitung über die Bündelung und Minimierung hinaus erfordert&mdash;z. b. linting-und Image-Optimierung. Mithilfe der Entwurfszeit Bündelung und-Minimierung werden die minisierten Dateien vor der Bereitstellung der App erstellt. Das bündeln und minimieren vor der Bereitstellung bietet den Vorteil der reduzierten Serverauslastung. Es ist jedoch wichtig zu erkennen, dass die Entwurfszeit Bündelung und-Minimierung die Komplexität der Builds erhöht und nur mit statischen Dateien funktioniert.

## <a name="configure-bundling-and-minification"></a>Konfigurieren von Bündelung und Minimierung

::: moniker range="<= aspnetcore-2.0"

In ASP.net Core 2,0 oder früher bieten die MVC-und Razor Pages-Projektvorlagen eine *bundleconfig. JSON* -Konfigurationsdatei, die die Optionen für jedes Bündel definiert:

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Fügen Sie in ASP.net Core 2,1 oder höher eine neue JSON-Datei mit dem Namen *bundleconfig. JSON*in das MVC-oder Razor Pages-Projektstamm Verzeichnis ein. Fügen Sie den folgenden JSON-Code als Ausgangspunkt in diese Datei ein:

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

Die Datei *bundleconfig. JSON* definiert die Optionen für jedes Paket. Im vorherigen Beispiel wird eine einzelne Paketkonfiguration für die benutzerdefinierten JavaScript-Dateien (*wwwroot/js/Site. js*) und Stylesheet (*wwwroot/CSS/Site. CSS*) definiert.

Zu den Konfigurationsoptionen gehören:

* `outputFileName`: der Name der Paketdatei, die ausgegeben werden soll. Kann einen relativen Pfad aus der Datei " *bundleconfig. JSON* " enthalten. **erforderlich**
* `inputFiles`: ein Array von Dateien, die zusammengefasst werden sollen. Dabei handelt es sich um relative Pfade zur Konfigurationsdatei. **optional**: * ein leerer Wert führt zu einer leeren Ausgabedatei. [globmuster](https://www.tldp.org/LDP/abs/html/globbingref.html) werden unterstützt.
* `minify`: die Minimierung-Optionen für den Ausgabetyp. **optional**, *Standard-`minify: { enabled: true }`*
  * Konfigurationsoptionen sind pro Ausgabe Dateityp verfügbar.
    * [CSS Minifier](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [JavaScript-minifier](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [HTML Minifier](https://github.com/madskristensen/BundlerMinifier/wiki)
* `includeInProject`: Flag, das angibt, ob generierte Dateien der Projektdatei hinzugefügt werden sollen. **optional**, *Standard-false*
* `sourceMap`: Flag, das angibt, ob eine Quell Zuordnung für die gebündelte Datei generiert werden soll. **optional**, *Standard-false*
* `sourceMapRootPath`: der Stammpfad zum Speichern der generierten Quell Zuordnungs Datei.

## <a name="build-time-execution-of-bundling-and-minification"></a>Build-Zeit-Ausführung von Bündelung und Minimierung

Das [buildbundlerminifier](https://www.nuget.org/packages/BuildBundlerMinifier/) -nuget-Paket ermöglicht die Ausführung von Bündelung und Minimierung zur Buildzeit. Das Paket fügt [MSBuild-Ziele](/visualstudio/msbuild/msbuild-targets) ein, die zum Zeitpunkt der Erstellung und Bereinigung ausgeführt werden. Die Datei " *bundleconfig. JSON* " wird vom Buildprozess analysiert, um die Ausgabedateien basierend auf der definierten Konfiguration zu erstellen.

> [!NOTE]
> Buildbundlerminifier gehört zu einem Community-gesteuerten Projekt auf GitHub, für das Microsoft keine Unterstützung bietet. [Hier](https://github.com/madskristensen/BundlerMinifier/issues)sollten Probleme aufgeführt werden.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Fügen Sie dem Projekt das *buildbundlerminifier* -Paket hinzu.

Erstellen Sie das Projekt. Im Ausgabefenster wird Folgendes angezeigt:

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

Bereinigen Sie das Projekt. Im Ausgabefenster wird Folgendes angezeigt:

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Fügen Sie dem Projekt das *buildbundlerminifier* -Paket hinzu:

```dotnetcli
dotnet add package BuildBundlerMinifier
```

Wenn Sie ASP.net Core 1. x verwenden, stellen Sie das neu hinzugefügte Paket wieder her:

```dotnetcli
dotnet restore
```

Erstellen Sie das Projekt:

```dotnetcli
dotnet build
```

Folgendes wird angezeigt:

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

Bereinigen Sie das Projekt:

```dotnetcli
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

Es ist möglich, die Bündelung und Minimierung von Aufgaben auf Ad-hoc-Basis auszuführen, ohne das Projekt zu entwickeln. Fügen Sie dem Projekt das nuget-Paket [bundlerminifier. Core](https://www.nuget.org/packages/BundlerMinifier.Core/) hinzu:

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> Bundlerminifier. Core gehört zu einem Community-gesteuerten Projekt auf GitHub, für das Microsoft keine Unterstützung bietet. [Hier](https://github.com/madskristensen/BundlerMinifier/issues)sollten Probleme aufgeführt werden.

Dieses Paket erweitert das .net Core-CLI, um das *dotnet-Bundle-* Tool einzubeziehen. Der folgende Befehl kann im Fenster der Paket-Manager-Konsole (PMC) oder in einer Befehlsshell ausgeführt werden:

```dotnetcli
dotnet bundle
```

> [!IMPORTANT]
> Der nuget-Paket-Manager fügt der *. CSPROJ-Datei Abhängigkeiten als `<PackageReference />` Knoten hinzu. Der `dotnet bundle` Befehl wird nur bei der .net Core-CLI registriert, wenn ein `<DotNetCliToolReference />` Knoten verwendet wird. Ändern Sie die Datei *. csproj entsprechend.

## <a name="add-files-to-workflow"></a>Dateien zum Workflow hinzufügen

Sehen Sie sich ein Beispiel an, in dem eine zusätzliche *benutzerdefinierte CSS* -Datei ähnlich der folgenden hinzugefügt wird:

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

Fügen Sie den relativen Pfad zu *bundleconfig. JSON*hinzu, um " *Custom. CSS* " zu minimieren und mit " *Site. CSS* " in einer " *Site. min. CSS* "-Datei zu bündeln:

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> Alternativ könnte das folgende globmuster verwendet werden:
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> Dieses globmuster stimmt mit allen CSS-Dateien überein und schließt das minierte Dateimuster aus.

Erstellen Sie die Anwendung. Öffnen Sie " *Site. min. CSS* ", und beachten Sie, dass der Inhalt von " *Custom. CSS* " an das Ende der Datei angehängt wird.

## <a name="environment-based-bundling-and-minification"></a>Umgebungs basierte Bündelung und Minimierung

Als bewährte Vorgehensweise sollten die gebündelten und die minierten Dateien Ihrer APP in einer Produktionsumgebung verwendet werden. Während der Entwicklung werden die ursprünglichen Dateien zum einfacheren Debuggen der APP gemacht.

Geben Sie mithilfe des [Umgebungs-taghilfsprogramms](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in ihren Ansichten an, welche Dateien in Ihre Seiten eingeschlossen werden sollen. Das Umgebungs-taghilfsprogramm rendert nur seinen Inhalt, wenn es in bestimmten [Umgebungen](xref:fundamentals/environments)ausgeführt wird.

Das folgende `environment`-Tag rendert die nicht verarbeiteten CSS-Dateien, wenn Sie in der `Development`-Umgebung ausgeführt werden:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

Das folgende `environment`-Tag rendert die gebündelten und die minierten CSS-Dateien, wenn Sie in einer anderen Umgebung als `Development`ausgeführt werden. Wenn Sie z. b. in `Production` oder `Staging` ausführen, wird das Rendering dieser Stylesheets ausgelöst:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a>Verwenden von bundleconfig. JSON aus Gulp

Es gibt Fälle, in denen das bündeln und minimieren einer App eine zusätzliche Verarbeitung erfordert. Beispiele hierfür sind Bildoptimierung, Cache-Busting und CDN-Asset-Verarbeitung. Um diese Anforderungen zu erfüllen, können Sie den Bundle-und Minimierung-Workflow für die Verwendung von Gulp konvertieren.

### <a name="use-the-bundler--minifier-extension"></a>Verwenden der Bundler-& minifier-Erweiterung

Die Visual Studio [Bundler-& minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) -Erweiterung behandelt die Konvertierung in Gulp.

> [!NOTE]
> Die Bundler-& minifier-Erweiterung gehört zu einem Community-gesteuerten Projekt auf GitHub, für das Microsoft keine Unterstützung bietet. [Hier](https://github.com/madskristensen/BundlerMinifier/issues)sollten Probleme aufgeführt werden.

Klicken Sie in Projektmappen-Explorer mit der rechten Maustaste auf die Datei *bundleconfig. JSON* , und wählen Sie **Bundler & minifier** aus, > in **Gulp konvertieren...** :

![In Gulp-Kontextmenü Element konvertieren](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

Die Dateien " *gulpfile. js* " und " *Package. JSON* " werden dem Projekt hinzugefügt. Die unterstützenden [NPM](https://www.npmjs.com/) -Pakete, die im `devDependencies` Abschnitt der Datei " *Package. JSON* " aufgeführt sind, werden installiert.

Führen Sie den folgenden Befehl im PMC-Fenster aus, um die Gulp-CLI als globale Abhängigkeit zu installieren:

```console
npm i -g gulp-cli
```

Die Datei " *gulpfile. js* " liest die Datei " *bundleconfig. JSON* " für die Eingaben, Ausgaben und Einstellungen.

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a>Manuell konvertieren

Wenn Visual Studio und/oder die Bundler-& minifier-Erweiterung nicht verfügbar ist, konvertieren Sie manuell.

Fügen Sie dem Projektstamm eine Datei " *Package. JSON* " mit dem folgenden `devDependencies`hinzu:

> [!WARNING]
> Das `gulp-uglify`-Modul unterstützt ECMAScript (es) 2015/ES6 und höher nicht. Installieren Sie [Gulp-terser](https://www.npmjs.com/package/gulp-terser) anstelle von `gulp-uglify`, um ES2015/ES6 oder höher zu verwenden.

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

Installieren Sie die Abhängigkeiten, indem Sie den folgenden Befehl auf der gleichen Ebene wie " *Package. JSON*" ausführen:

```console
npm i
```

Installieren Sie die Gulp-CLI als globale Abhängigkeit:

```console
npm i -g gulp-cli
```

Kopieren Sie die nachfolgende Datei " *gulpfile. js* " in das Projektstamm Verzeichnis:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a>Ausführen von Gulp-Tasks

Fügen Sie das folgende [MSBuild-Ziel](/visualstudio/msbuild/msbuild-targets) der *. CSPROJ-Datei hinzu, um die Gulp-minierationsaufgabe vor dem Erstellen des Projekts in Visual Studio zu starten:

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

In diesem Beispiel werden alle Aufgaben, die im `MyPreCompileTarget` Ziel definiert sind, vor dem vordefinierten `Build` Ziel ausgeführt. Die Ausgabe ähnlich der folgenden wird im Ausgabefenster von Visual Studio angezeigt:

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

## <a name="additional-resources"></a>Weitere Ressourcen

* [Verwenden von Grunt](xref:client-side/using-grunt)
* [Verwenden mehrerer Umgebungen](xref:fundamentals/environments)
* [Tag Helpers (Taghilfsprogramme)](xref:mvc/views/tag-helpers/intro)

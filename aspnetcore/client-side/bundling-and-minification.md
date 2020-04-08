---
title: Bündelung und Minimierung statischer Ressourcen in ASP.NET Core
author: scottaddie
description: Hier erfahren Sie, wie Sie statische Ressourcen in einer ASP.NET Core-Webanwendung mithilfe von Bündelungs- und Minimierungsverfahren optimieren.
ms.author: scaddie
ms.custom: mvc
ms.date: 06/17/2019
uid: client-side/bundling-and-minification
ms.openlocfilehash: a7a5c40d6c31c4416212c02c1b491dd794f2a1d3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2020
ms.locfileid: "78646783"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>Bündelung und Minimierung statischer Ressourcen in ASP.NET Core

Von [Scott Addie](https://twitter.com/Scott_Addie) und [David Pine](https://twitter.com/davidpine7)

In diesem Artikel lernen Sie die Vorteile der Funktionen Bündelung und Minimierung sowie ihre Verwendung mit ASP.NET Core-Web-Apps kennen.

## <a name="what-is-bundling-and-minification"></a>Was versteht man unter Bündelung und Minimierung?

Die Bündelung und Minimierung sind zwei Methoden zur Leistungsoptimierung, die Sie unabhängig voneinander in einer Web-App anwenden können. Zusammengenommen führen diese Funktionen zu einer Leistungssteigerung, indem die Anzahl der Serveranforderungen sowie die Größe der angeforderten statischen Ressourcen reduziert werden.

Durch Bündelung und Minimierung wird in erster Linie die Ladezeit für die Anforderung der ersten Seite verkürzt. Sobald eine Webseite angefordert wurde, werden die statischen Ressourcen (JavaScript, CSS und Bilder) vom Browser zwischengespeichert. Die Bündelung und Minimierung haben folglich keine Auswirkungen auf die Leistung, wenn dieselbe Seite (oder Seiten) auf derselben Website angefordert wird, die ihrerseits dieselben Ressourcen anfordert. Wurde der Expires-Header für die Ressourcen nicht korrekt festgelegt und die Bündelungs- und Minimierungsfunktionen nicht verwendet, kennzeichnen die Aktualitätsheuristiken des Browsers die Ressourcen nach ein paar Tagen als veraltet. Zusätzlich benötigt der Browser für jede Ressource eine Überprüfungsanforderung. In solchen Fällen führt die Bündelung und Minimierung auch nach der Anforderung der ersten Seite zu einer Leistungsverbesserung.

### <a name="bundling"></a>Bündelung

Bei der Bündelung werden mehrere Dateien zu einer einzelnen Datei kombiniert. Durch die Bündelung wird die zum Rendern einer Webressource, z. B. einer Webseite, erforderliche Anzahl von Serveranforderungen reduziert. Sie können beliebig viele Einzelbündel erstellen, insbesondere für CSS, JavaScript usw. Je kleiner die Anzahl von Dateien, desto weniger HTTP-Anforderungen müssen zwischen Browser und Server oder vom Dienst verarbeitet werden, der Ihre Anwendung bereitstellt. Dadurch kann die Leistung des Ladevorgangs der ersten Seite gesteigert werden.

### <a name="minification"></a>Minimierung

Bei der Minimierung werden unnötige Zeichen aus dem Code entfernt, ohne dass die Funktionalität geändert wird. Dadurch lässt sich die Größe der angeforderten Ressourcen (wie CSS, Bilder oder JavaScript-Dateien) deutlich reduzieren. Häufiger Nebeneffekt einer Minimierung sind auf ein Zeichen verkürzte Variablennamen und entfernte Kommentare und Leerzeichen.

Betrachten Sie die folgende JavaScript-Funktion:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

Durch Minimierung kann die Funktion auf folgenden Code verkürzt werden:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

Zusätzlich zur Entfernung von Kommentaren und unnötigen Leerzeichen werden die folgenden Parameter und Variablennamen folgendermaßen umbenannt:

Ursprünglich | Umbenannt
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a>Auswirkungen der Bündelung und Minimierung

In der folgenden Tabelle werden die Unterschiede bei den Ladezeiten für einzelne Ressourcen mit und ohne Bündelung und Minimierung veranschaulicht:

Aktion | Mit B/M | Ohne B/M | Änderung
--- | :---: | :---: | :---:
Dateianforderungen  | 7   | 18     | 157 %
Übertragene KB | 156 | 264,68 | 70 %
Ladezeit (ms) | 885 | 2360   | 167 %

Browser arbeiten in Bezug auf HTTP-Anforderungsheader ziemlich ausführlich. Die Gesamtmetrik der gesendeten Bytes ergibt bei der Bündelung eine deutliche Verringerung. Auch die Ladezeit wird deutlich verbessert. Und dies obwohl das vorangegangene Beispiel lokal ausgeführt wurde. Bei einer Übertragung der Ressourcen über ein Netzwerk können durch Bündelung und Minimierung noch größere Leistungssteigerungen erzielt werden.

## <a name="choose-a-bundling-and-minification-strategy"></a>Auswählen einer Bündelungs- und Minimierungsstrategie

Die MVC- und Razor Pages-Projektvorlagen enthalten eine einsatzbereite Bündelungs- und Minimierungslösung bestehend aus einer JSON-Konfigurationsdatei. Drittanbietertools, wie die [Grunt](xref:client-side/using-grunt)-Aufgabenausführung, führen dieselben Aufgaben auf etwas komplexere Weise aus. Ein Drittanbietertool eignet sich hervorragend in Fällen, in denen Ihr Entwicklungsworkflow Verarbeitungsschritte erfordert, die über die Bündelung und Minimierung hinausgehen, wie etwa Linting und Bildoptimierung. Bei der Bündelung und Minimierung zur Entwurfszeit werden die minimierten Dateien vor Bereitstellung der App erstellt. Werden die Bündelung und Minimierung vor der Bereitstellung durchgeführt, kann die Serverauslastung reduziert werden. Beachten Sie jedoch, dass eine Bündelung und Minimierung zur Entwurfszeit die Komplexität des Builds erhöht und auch nur mit statischen Dateien ausgeführt werden kann.

## <a name="configure-bundling-and-minification"></a>Konfigurieren der Bündelung und Minimierung

::: moniker range="<= aspnetcore-2.0"

In ASP.NET Core 2.0 oder früher enthalten die MVC- und Razor Pages-Projektvorlagen eine Konfigurationsdatei mit dem Namen *bundleconfig.json*, in der die Optionen für jedes Bündel definiert werden:

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Fügen Sie ab ASP.NET Core 2.1 dem MVC- oder Razor Pages-Projektstamm eine neue JSON-Datei mit dem Namen *bundleconfig.json* hinzu. Fügen Sie dieser Datei als Startpunkt den folgenden JSON-Code hinzu:

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

In der Datei *bundleconfig.json* werden die Optionen für jedes Bündel definiert. Im vorherigen Beispiel wird für die benutzerdefinierten JavaScript- (*wwwroot/js/site.js*) und Stylesheet-Dateien (*wwwroot/css/site.css*) eine einzelne Bündelkonfiguration definiert.

Zu den Konfigurationsoptionen gehören:

* `outputFileName`: Name der Bündeldatei, die ausgegeben werden soll. Kann einen relativen Pfad der Datei *bundleconfig.json* enthalten. **erforderlich**
* `inputFiles`: Array von Dateien, die gebündelt werden sollen. Enthält relative Pfade zur Konfigurationsdatei. **Optional**, *ein leerer Wert erzeugt eine leere Ausgabedatei. [Globmuster](https://www.tldp.org/LDP/abs/html/globbingref.html) werden unterstützt.
* `minify`: Minimierungsoptionen für den Ausgabetyp. **Optional**, *Standardwert: `minify: { enabled: true }`* .
  * Für jeden Ausgabedateityp stehen Konfigurationsoptionen zur Verfügung.
    * [CSS-Minifier](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [JavaScript-Minifier](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [HTML-Minifier](https://github.com/madskristensen/BundlerMinifier/wiki)
* `includeInProject`: Flag, das angibt, ob der Projektdatei generierte Dateien hinzugefügt werden sollen. **Optional**, *Standardwert: FALSE*.
* `sourceMap`: Flag, das angibt, ob für die Bündeldatei eine Quellzuordnungsdatei generiert werden soll. **Optional**, *Standardwert: FALSE*.
* `sourceMapRootPath`: Stammpfad zum Speichern der generierten Quellzuordnungsdatei.

## <a name="build-time-execution-of-bundling-and-minification"></a>Ausführen der Bündelung und Minimierung zur Buildzeit

Mit dem NuGet-Paket [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) können Sie die Bündelung und Minimierung zur Buildzeit ausführen. Durch das Paket werden [MSBuild-Ziele](/visualstudio/msbuild/msbuild-targets) eingefügt, die zum Zeitpunkt des Builds und der Bereinigung ausgeführt werden. Die Datei *bundleconfig.json* wird im Buildprozess analysiert und die Ausgabedateien basierend auf der definierten Konfiguration erstellt.

> [!NOTE]
> Das Paket „BuildBundlerMinifier“ gehört zu einem von der Community gesteuerten Projekt auf GitHub, für das Microsoft keine Unterstützung bereitstellt. Hilfe bei Problemen finden Sie [hier](https://github.com/madskristensen/BundlerMinifier/issues).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Fügen Sie Ihrem Projekt das Paket *BuildBundlerMinifier* hinzu.

Erstellen Sie das Projekt. Die folgende Ausgabe wird im Ausgabefenster angezeigt:

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

Bereinigen Sie das Projekt. Die folgende Ausgabe wird im Ausgabefenster angezeigt:

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Fügen Sie Ihrem Projekt das Paket *BuildBundlerMinifier* hinzu:

```dotnetcli
dotnet add package BuildBundlerMinifier
```

Wenn Sie ASP.NET Core 1.x verwenden, stellen Sie das neu hinzugefügte Paket wieder her:

```dotnetcli
dotnet restore
```

Erstellen Sie das Projekt:

```dotnetcli
dotnet build
```

Die folgende Ausgabe wird angezeigt:

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

## <a name="ad-hoc-execution-of-bundling-and-minification"></a>Ausführen der Bündelung und Minimierung auf Ad-hoc-Basis

Bündelungs- und Minimierungsaufgaben können auch ad hoc ausgeführt werden, ohne dass das Projekt erstellt werden muss. Fügen Sie Ihrem Projekt das NuGet-Paket [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) hinzu:

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> Das Paket „BundlerMinifier.Core“ gehört zu einem von der Community gesteuerten Projekt auf GitHub, für das Microsoft keine Unterstützung bereitstellt. Hilfe bei Problemen finden Sie [hier](https://github.com/madskristensen/BundlerMinifier/issues).

Mit diesem Paket wird die .NET Core-CLI erweitert, sodass sie anschließend das Tool *dotnet-bundle* enthält. Sie können den folgenden Befehl im Fenster der Paket-Manager-Konsole (PMC) oder in der Befehlsshell ausführen:

```dotnetcli
dotnet bundle
```

> [!IMPORTANT]
> Mit dem NuGet-Paket-Manager werden der Datei „*.csproj“ Abhängigkeiten als `<PackageReference />`-Knoten hinzugefügt. Der Befehl `dotnet bundle` wird nur bei Verwendung eines `<DotNetCliToolReference />`-Knotens bei der .NET Core-CLI registriert. Ändern Sie die Datei „*.csproj“ entsprechend ab.

## <a name="add-files-to-workflow"></a>Hinzufügen von Dateien zum Workflow

Angenommen, Sie möchten eine zusätzliche *custom.css*-Datei hinzufügen, die etwa folgendermaßen aussieht:

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

Fügen Sie der Datei *bundleconfig.json* den relativen Pfad hinzu, um die Datei *custom.css* zu minimieren und zusammen mit *site.css* in die Datei *site.min.css* zu bündeln:

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> Alternativ dazu können Sie auch das folgende Globmuster verwenden:
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> Dieses Globmuster stimmt mit allen CSS-Dateien überein und schließt das minierte Dateimuster aus.

Erstellen Sie die Anwendung. Öffnen Sie die Datei *site.min.css*. Dabei werden Sie erkennen, dass der Inhalt von *custom.css* an das Ende der Datei angefügt wurde.

## <a name="environment-based-bundling-and-minification"></a>Umgebungsbasierte Bündelung und Minimierung

Als bewährte Methode wird empfohlen, die gebündelten und minimierten Dateien Ihrer App in einer Produktionsumgebung zu verwenden. Während der Entwicklung wird das Debuggen der App durch die Originaldateien erleichtert.

Geben Sie mithilfe des [Umgebungstaghilfsprogramms](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in Ihren Ansichten die Dateien an, die in Ihre Seiten einbezogen werden sollen. Das Umgebungstaghilfsprogramm rendert die Inhalte nur, wenn es in bestimmten [Umgebungen](xref:fundamentals/environments) ausgeführt wird.

Das folgende `environment`-Tag rendert die nicht verarbeiteten CSS-Dateien bei der Ausführung in einer `Development`-Umgebung:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

Das folgende `environment`-Tag rendert die gebündelten und minimierten CSS-Dateien nur bei der Ausführung in einer Umgebung, die keine `Development`-Umgebung darstellt. Die Ausführung in einer `Production`- oder `Staging`-Umgebung löst beispielsweise das Rendern der folgenden Stylesheets aus:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a>Verarbeiten der Datei „bundleconfig.json“ mit Gulp

In einigen Fällen erfordert der Workflow für die Bündelung und Minimierung einer App zusätzliche Verarbeitungsschritte. Dazu gehören Bildoptimierung, Cache-Busting und die Verarbeitung von CDN-Ressourcen. Zur Durchführung dieser Aufgaben können Sie den Workflow für die Bündelung und Minimierung so konvertieren, dass Gulp verwendet wird.

### <a name="use-the-bundler--minifier-extension"></a>Verwenden der Erweiterung „Bundler & Minifier“

Die Konvertierung in Gulp wird durch die Visual Studio-Erweiterung [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) ausgeführt.

> [!NOTE]
> Die Erweiterung „Bundler & Minifier“ gehört zu einem von der Community gesteuerten Projekt auf GitHub, für das Microsoft keine Unterstützung bereitstellt. Hilfe bei Problemen finden Sie [hier](https://github.com/madskristensen/BundlerMinifier/issues).

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Datei *bundleconfig.json*, und klicken Sie anschließend auf **Bundler & Minifier** > **In Gulp konvertieren...** :

![Kontextmenüelement „In Gulp konvertieren“](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

Die Dateien *gulpfile.js* und *package.json* werden dem Projekt hinzugefügt. Die im Bereich `devDependencies` der Datei *package.json* aufgelisteten unterstützenden [npm](https://www.npmjs.com/)-Pakete werden installiert.

Führen Sie im PMC-Fenster folgenden Befehl aus, um die Gulp-CLI als globale Abhängigkeit zu installieren:

```console
npm i -g gulp-cli
```

Die Datei *gulpfile.js* liest die Datei *bundleconfig.json*, um die Eingaben, Ausgaben und Einstellungen zu ermitteln.

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a>Manuelles Konvertieren

Sind Visual Studio und/oder die Erweiterung „Bundler & Minifier“ nicht verfügbar, können Sie die Konvertierung manuell durchführen.

Fügen Sie dem Projektstamm eine *package.json*-Datei mit den folgenden `devDependencies` hinzu:

> [!WARNING]
> ECMAScript (ES) 2015/ES6 und höher wird vom `gulp-uglify`-Modul nicht unterstützt. Installieren Sie anstelle von `gulp-uglify` das Plug-In [gulp-terser](https://www.npmjs.com/package/gulp-terser), wenn Sie ES2015/ES6 oder höher verwenden möchten.

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

Installieren Sie die Abhängigkeiten, indem Sie den folgenden Befehl auf der Ebene von *package.json* ausführen:

```console
npm i
```

Installieren Sie die Gulp-CLI als globale Abhängigkeit:

```console
npm i -g gulp-cli
```

Kopieren Sie die folgende *gulpfile.js*-Datei in den Projektstamm:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a>Ausführen von Gulp-Aufgaben

Fügen Sie der Datei „*.csproj“ das folgende [MSBuild-Ziel](/visualstudio/msbuild/msbuild-targets) hinzu, um die Minimierungsaufgabe in Gulp vor dem Erstellen des Projekts in Visual Studio auszulösen:

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

In diesem Beispiel werden alle im Ziel `MyPreCompileTarget` definierten Aufgaben vor dem vordefinierten `Build`-Ziel ausgeführt. Etwa folgende Ausgabe wird im Visual Studio-Ausgabefenster angezeigt:

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

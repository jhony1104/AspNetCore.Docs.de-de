---
title: Verwenden von Gulp in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie Gulp in ASP.NET Core verwenden.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 10/04/2018
uid: client-side/using-gulp
ms.openlocfilehash: 9f6d03a1e8a81bceca15cb1e1aa664c22c31e1d3
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58209871"
---
# <a name="use-gulp-in-aspnet-core"></a>Verwenden von Gulp in ASP.NET Core

Durch [Scott Addie](https://scottaddie.com), [Shayne Boyer](https://twitter.com/spboyer), und [David Kiefer](https://twitter.com/davidpine7)

In einer typischen moderner Web-app können der Buildprozess ein:

* Bündelung und Minimierung von JavaScript- und CSS-Dateien.
* Führen Sie Tools zum Aufrufen der Bündelung und Minimierung Aufgaben vor jedem Build aus.
* Kompilieren Sie weniger oder SASS-Dateien, die CSS.
* Kompilieren Sie die Dateien CoffeeScript oder TypeScript in JavaScript.

Ein *aufgabenausführung* ist ein Tool, das diese routinemäßiger Entwicklungs- und weitere Aufgaben automatisiert. Visual Studio bietet integrierten Unterstützung für zwei beliebte JavaScript-basierten Task Runner: [Gulp](https://gulpjs.com/) und [Grunt](using-grunt.md).

## <a name="gulp"></a>Gulp

Gulp ist ein JavaScript-basiertes streaming Build Toolkit für clientseitigen Code. Es wird häufig verwendet, um clientseitige Dateien über eine Reihe von Prozessen zu streamen, wenn ein bestimmtes Ereignis in einer Buildumgebung ausgelöst wird. Z. B. Gulp dienen zum Automatisieren von [Bündelung und Minimierung](bundling-and-minification.md) oder dem Bereinigen von einer Entwicklungsumgebung, bevor Sie einen neuen Build.

Eine Reihe von Gulp-Aufgaben wird in definiert *"gulpfile.js"*. Das folgende JavaScript umfasst Gulp-Module und gibt die Dateipfade in die neue Aufgaben verwiesen werden:

```javascript
/// <binding Clean='clean' />
"use strict";

var gulp = require("gulp"),
    rimraf = require("rimraf"),
    concat = require("gulp-concat"),
    cssmin = require("gulp-cssmin"),
    uglify = require("gulp-uglify");

var paths = {
  webroot: "./wwwroot/"
};

paths.js = paths.webroot + "js/**/*.js";
paths.minJs = paths.webroot + "js/**/*.min.js";
paths.css = paths.webroot + "css/**/*.css";
paths.minCss = paths.webroot + "css/**/*.min.css";
paths.concatJsDest = paths.webroot + "js/site.min.js";
paths.concatCssDest = paths.webroot + "css/site.min.css";
```

Der obige Code gibt an, welche Module Knoten erforderlich sind. Die `require` Funktionsimporte jedes Modul, damit der abhängigen Aufgaben ihre Features nutzen können. Jedes der importierten Module wird einer Variablen zugewiesen. Die Module können entweder nach Name oder Pfad befinden. In diesem Beispiel die Module mit dem Namen `gulp`, `rimraf`, `gulp-concat`, `gulp-cssmin`, und `gulp-uglify` anhand des Namens abgerufen werden. Darüber hinaus eine Reihe von Pfaden werden erstellt, sodass die Standorte von CSS und JavaScript-Dateien können erneut verwendet und die Aufgaben auf die verwiesen wird. Die folgende Tabelle enthält Beschreibungen der in enthaltenen Module *"gulpfile.js"*.

| Modulname | Beschreibung |
| ----------- | ----------- |
| Gulp        | Erstellen Sie das Gulp-streaming auf System. Weitere Informationen finden Sie unter [gulp](https://www.npmjs.com/package/gulp). |
| rimraf      | Ein Modul, Knoten löschen. Weitere Informationen finden Sie unter [Rimraf](https://www.npmjs.com/package/rimraf). |
| gulp-concat | Ein Modul, das verkettet Dateien auf Grundlage des Betriebssystems Zeilenumbruchzeichen. Weitere Informationen finden Sie unter [Gulp-Concat](https://www.npmjs.com/package/gulp-concat). |
| gulp-cssmin | Ein Modul, das CSS-Dateien verkleinert. Weitere Informationen finden Sie unter [Gulp-Cssmin](https://www.npmjs.com/package/gulp-cssmin). |
| gulp-uglify | Ein Modul, das verkleinert *js* Dateien. Weitere Informationen finden Sie unter [Gulp-uglify](https://www.npmjs.com/package/gulp-uglify). |

Nachdem die erforderlichen Module importiert wurden, können die Aufgaben angegeben werden. Es gibt sechs Aufgaben registriert, mit dem folgenden Code dargestellt:

```javascript
gulp.task("clean:js", done => rimraf(paths.concatJsDest, done));
gulp.task("clean:css", done => rimraf(paths.concatCssDest, done));
gulp.task("clean", gulp.series(["clean:js", "clean:css"]));

gulp.task("min:js", () => {
  return gulp.src([paths.js, "!" + paths.minJs], { base: "." })
    .pipe(concat(paths.concatJsDest))
    .pipe(uglify())
    .pipe(gulp.dest("."));
});

gulp.task("min:css", () => {
  return gulp.src([paths.css, "!" + paths.minCss])
    .pipe(concat(paths.concatCssDest))
    .pipe(cssmin())
    .pipe(gulp.dest("."));
});

gulp.task("min", gulp.series(["min:js", "min:css"]));

// A 'default' task is required by Gulp v4
gulp.task("default", gulp.series(["min"]));
```

---

Die folgende Tabelle enthält eine Erläuterung der Aufgaben im obigen Code angegeben:

|Aufgabenname|Beschreibung|
|--- |--- |
|clean:js|Eine Aufgabe, die die Löschung Rimraf knotenmodul verwendet, um die minimierte Version von der Datei "site.js" zu entfernen.|
|Bereinigen: Css|Eine Aufgabe, die die Löschung Rimraf knotenmodul verwendet, um die minimierte Version der Datei "Site.CSS" zu entfernen.|
|Bereinigen|Eine Aufgabe, die Aufrufe der `clean:js` Aufgabe, gefolgt von der `clean:css` Aufgabe.|
|min:js|Eine Aufgabe, die verkleinert und verkettet alle JS-Dateien im Ordner "Js". Die. min.js-Dateien ausgeschlossen sind.|
|min:css|Eine Aufgabe, die verkleinert und alle CSS-Dateien im Ordner "Css" verkettet. Die. min.css Dateien ausgeschlossen sind.|
|Min.|Eine Aufgabe, die Aufrufe der `min:js` Aufgabe, gefolgt von der `min:css` Aufgabe.|

## <a name="running-default-tasks"></a>Ausführen von Standardaufgaben

Wenn Sie eine neue Web-app bereits erstellt haben, erstellen Sie ein neues ASP.NET Web Application-Projekt in Visual Studio.

1. Öffnen der *"Package.JSON"* Datei (Hinzufügen Falls nicht vorhanden), und fügen Sie Folgendes hinzu.

    ```json
    {
      "devDependencies": {
        "gulp": "^4.0.0",
        "gulp-concat": "2.6.1",
        "gulp-cssmin": "0.2.0",
        "gulp-uglify": "3.0.0",
        "rimraf": "2.6.1"
      }
    }
    ```

2. Fügen Sie eine neue JavaScript-Datei zu Ihrem Projekt, und nennen Sie sie *"gulpfile.js"*, kopieren Sie den folgenden Code.

    ```javascript
    /// <binding Clean='clean' />
    "use strict";

    const gulp = require("gulp"),
          rimraf = require("rimraf"),
          concat = require("gulp-concat"),
          cssmin = require("gulp-cssmin"),
          uglify = require("gulp-uglify");

    const paths = {
      webroot: "./wwwroot/"
    };

    paths.js = paths.webroot + "js/**/*.js";
    paths.minJs = paths.webroot + "js/**/*.min.js";
    paths.css = paths.webroot + "css/**/*.css";
    paths.minCss = paths.webroot + "css/**/*.min.css";
    paths.concatJsDest = paths.webroot + "js/site.min.js";
    paths.concatCssDest = paths.webroot + "css/site.min.css";

    gulp.task("clean:js", done => rimraf(paths.concatJsDest, done));
    gulp.task("clean:css", done => rimraf(paths.concatCssDest, done));
    gulp.task("clean", gulp.series(["clean:js", "clean:css"]));

    gulp.task("min:js", () => {
      return gulp.src([paths.js, "!" + paths.minJs], { base: "." })
      .pipe(concat(paths.concatJsDest))
      .pipe(uglify())
      .pipe(gulp.dest("."));
    });

    gulp.task("min:css", () => {
      return gulp.src([paths.css, "!" + paths.minCss])
      .pipe(concat(paths.concatCssDest))
      .pipe(cssmin())
      .pipe(gulp.dest("."));
    });

    gulp.task("min", gulp.series(["min:js", "min:css"]));

    // A 'default' task is required by Gulp v4
    gulp.task("default", gulp.series(["min"]));
    ```

3. In **Projektmappen-Explorer**, mit der rechten Maustaste *"gulpfile.js"*, und wählen Sie **Task Runner-Explorer**.

    ![Task Runner-Explorer im Projektmappen-Explorer öffnen](using-gulp/_static/02-SolutionExplorer-TaskRunnerExplorer.png)

    **Task Runner Explorer** zeigt die Liste der Gulp-Tasks. (Möglicherweise müssen Sie klicken Sie auf die **aktualisieren** Schaltfläche, auf der linken Seite, der den Namen des Projekts angezeigt wird.)

    ![Task Runner-Explorer](using-gulp/_static/03-TaskRunnerExplorer.png)

    > [!IMPORTANT]
    > Die **Task Runner-Explorer** Kontextmenüelement erscheint nur, wenn *"gulpfile.js"* befindet sich im Stammverzeichnis des Projekts.

4. Darunter **Aufgaben** in **Task Runner-Explorer**, mit der rechten Maustaste **Bereinigen**, und wählen Sie **ausführen** aus dem Popupmenü.

    ![Task Runner-Explorer-clean-Aufgabe](using-gulp/_static/04-TaskRunner-clean.png)

    **Task Runner Explorer** erstellt eine neue Registerkarte mit dem Namen **Bereinigen** , und führen Sie die clean-Aufgabe in definierten *"gulpfile.js"*.

5. Mit der rechten Maustaste die **Bereinigen** Aufgabe aus, und wählen Sie dann **Bindungen** > **vor dem Erstellen**.

    ![Binden von BeforeBuild Task Runner-Explorer](using-gulp/_static/05-TaskRunner-BeforeBuild.png)

    Die **vor dem Erstellen** Bindung konfiguriert, wird die clean-Aufgabe, die vor jedem Build des Projekts automatisch ausgeführt.

Die Bindungen Sie mit einrichten **Task Runner-Explorer** befinden sich in einen Kommentar am oberen Rand der Form Ihrer *"gulpfile.js"* und gelten nur in Visual Studio. Eine Alternative, die keine Visual Studio erfordert wird, so konfigurieren Sie die automatische Ausführung von Gulp-Aufgaben in Ihrem *csproj* Datei. Fügen Sie diesen z. B. Ihrem *csproj* Datei:

```xml
<Target Name="MyPreCompileTarget" BeforeTargets="Build">
  <Exec Command="gulp clean" />
</Target>
```

Nachdem die clean-Aufgabe ausgeführt wird, wenn Sie das Projekt in Visual Studio oder von einer Eingabeaufforderung Ausführen der ["Dotnet run"](/dotnet/core/tools/dotnet-run) Befehl (führen Sie `npm install` erste).

## <a name="defining-and-running-a-new-task"></a>Definieren und Ausführen eines neuen Tasks

Um ein neuer Gulp-Task zu definieren, ändern Sie *"gulpfile.js"*.

1. Fügen Sie den folgenden JavaScript-Code am Ende der *"gulpfile.js"*:

    ```javascript
    gulp.task('first', done => {
      console.log('first task! <-----');
      done(); // signal completion
    });
    ```

    Diese Aufgabe heißt `first`, und zeigt einfach eine Zeichenfolge.

2. Speichern Sie *"gulpfile.js"*.

3. In **Projektmappen-Explorer**, mit der rechten Maustaste *"gulpfile.js"*, und wählen Sie *Task Runner-Explorer*.

4. In **Task Runner-Explorer**, mit der rechten Maustaste **erste**, und wählen Sie **ausführen**.

    ![Führen Sie die erste Aufgabe Task Runner-Explorer](using-gulp/_static/06-TaskRunner-First.png)

    Der Ausgabetext wird angezeigt. Beispiele, die basierend auf allgemeine Szenarien finden Sie unter [Gulp Recipes](#gulp-recipes).

## <a name="defining-and-running-tasks-in-a-series"></a>Definieren und Ausführen von Aufgaben in einer Reihe

Wenn Sie mehrere Aufgaben ausführen, werden die Aufgaben gleichzeitig werden standardmäßig ausgeführt. Aber wenn Sie Aufgaben in einer bestimmten Reihenfolge ausführen müssen, müssen Sie angeben Wenn jede Aufgabe abgeschlossen ist, sowie ist als die Aufgaben auf den Abschluss einer anderen Aufgabe abhängig sind.

1. Um eine Reihe von Aufgaben zur Ausführung in der Reihenfolge zu definieren, ersetzen die `first` Aufgabe, die Sie oben hinzugefügt, im haben *"gulpfile.js"* durch Folgendes:

    ```javascript
    gulp.task('series:first', done => {
      console.log('first task! <-----');
      done(); // signal completion
    });
    gulp.task('series:second', done => {
      console.log('second task! <-----');
        done(); // signal completion
    });

    gulp.task('series', gulp.series(['series:first', 'series:second']), () => { });

    // A 'default' task is required by Gulp v4
      gulp.task('default', gulp.series('series'));
    ```

    Sie verfügen jetzt über drei Aufgaben: `series:first`, `series:second`, und `series`. Die `series:second` Aufgabe umfasst einen zweiten Parameter die gibt ein Array von Aufgaben ausgeführt und abgeschlossen, bevor die `series:second` Aufgabe wird ausgeführt. Gemäß den Angaben in den Code oben kann nur die `series:first` Aufgabe abgeschlossen werden muss, bevor Sie die `series:second` Aufgabe wird ausgeführt.

2. Speichern Sie *"gulpfile.js"*.

3. In **Projektmappen-Explorer**, mit der rechten Maustaste *"gulpfile.js"* , und wählen Sie **Task Runner-Explorer** , wenn es nicht bereits geöffnet ist.

4. In **Task Runner-Explorer**, mit der rechten Maustaste **Reihe** , und wählen Sie **ausführen**.

    ![Task Runner-Explorer Serie ausführen](using-gulp/_static/07-TaskRunner-Series.png)

## <a name="intellisense"></a>IntelliSense

IntelliSense bietet codevervollständigung, parameterbeschreibungen und andere Funktionen, um die Produktivität zu steigern und Fehler zu reduzieren. Gulp-Tasks werden in JavaScript geschrieben; aus diesem Grund bieten IntelliSense Unterstützung zu erhalten, während der Entwicklung. Wie Sie mit JavaScript arbeiten, listet IntelliSense die Objekte, Funktionen, Eigenschaften und verfügbaren Parameter basierend auf den aktuellen Kontext. Wählen Sie eine Codierungsoption aus der Popupliste aus, die von IntelliSense, um den Code fertig bereitgestellt.

![gulp-IntelliSense](using-gulp/_static/08-IntelliSense.png)

Weitere Informationen zu IntelliSense finden Sie unter [JavaScript IntelliSense](/visualstudio/ide/javascript-intellisense).

## <a name="development-staging-and-production-environments"></a>Entwicklungs-, Staging-und produktionsumgebungen

Wenn Gulp zur Optimierung des clientseitigen Dateien für Staging und Produktion verwendet wird, werden die verarbeiteten Dateien in einen lokalen Speicherort für Staging und Produktion gespeichert. Die *"_Layout.cshtml"* Datei verwendet die **Umgebung** taghilfsprogramm um zwei verschiedene Versionen von CSS-Dateien zu ermöglichen. Eine Version von CSS-Dateien für die Entwicklung, die andere Version ist für die Staging- und produktionsumgebungen optimiert. In Visual Studio 2017, wenn Sie ändern die **"aspnetcore_environment"** Umgebungsvariable `Production`, Visual Studio erstellt die Web-app und ein Link zu den minimierten CSS-Dateien. Das folgende Markup zeigt die **Umgebung** taghilfsprogramme, die mit Linktags, die `Development` CSS-Dateien und die minimierte `Staging, Production` CSS-Dateien.

```html
<environment names="Development">
    <script src="~/lib/jquery/dist/jquery.js"></script>
    <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
    <script src="~/js/site.js" asp-append-version="true"></script>
</environment>
<environment names="Staging,Production">
    <script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-2.2.0.min.js"
            asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
            asp-fallback-test="window.jQuery"
            crossorigin="anonymous"
            integrity="sha384-K+ctZQ+LL8q6tP7I94W+qzQsfRV2a+AfHIi9k8z8l9ggpc8X+Ytst4yBo/hH+8Fk">
    </script>
    <script src="https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/bootstrap.min.js"
            asp-fallback-src="~/lib/bootstrap/dist/js/bootstrap.min.js"
            asp-fallback-test="window.jQuery && window.jQuery.fn && window.jQuery.fn.modal"
            crossorigin="anonymous"
            integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa">
    </script>
    <script src="~/js/site.min.js" asp-append-version="true"></script>
</environment>
```

## <a name="switching-between-environments"></a>Wechseln zwischen Umgebungen

Um zwischen dem Kompilieren für verschiedene Umgebungen zu wechseln, ändern Sie die **"aspnetcore_environment"** Umgebung des Variablenwerts.

1. In **Task Runner-Explorer**, überprüfen Sie, ob die **min** auszuführende Aufgabe festgelegt wurde **vor dem Erstellen**.

2. In **Projektmappen-Explorer**mit der rechten Maustaste auf den Projektnamen, und wählen Sie **Eigenschaften**.

    Das Eigenschaftenblatt für die Web-app wird angezeigt.

3. Klicken Sie auf die Registerkarte **Debuggen**.

4. Legen Sie den Wert, der die **Hostingumgebung:** -Umgebungsvariable so fest, `Production`.

5. Drücken Sie **F5** zum Ausführen der Anwendung in einem Browser.

6. Klicken Sie im Browserfenster mit der rechten Maustaste in der Seite, und wählen **Quelltext anzeigen** um den HTML-Code für die Seite anzuzeigen.

    Beachten Sie, dass der Stylesheet-Links auf die minimierten CSS-Dateien verweisen.

7. Schließen Sie den Browser, um die Web-app zu beenden.

8. Klicken Sie in Visual Studio zurück, um das Eigenschaftenfenster für die Web-app, und ändern Sie die **Hostingumgebung:** Umgebungsvariablen zurück zum `Development`.

9. Drücken Sie **F5** zum Ausführen der Anwendung in einem Browser erneut.

10. Klicken Sie im Browserfenster mit der rechten Maustaste in der Seite, und wählen **Quelltext anzeigen** um den HTML-Code für die Seite anzuzeigen.

    Beachten Sie, dass der Stylesheet-Links auf die unminified Versionen der CSS-Dateien verweisen.

Weitere Informationen zu Umgebungen in ASP.NET Core, finden Sie unter [Verwenden mehrerer Umgebungen](../fundamentals/environments.md).

## <a name="task-and-module-details"></a>Task und -Modul-details

Ein Gulp-Task ist ein Funktionsname registriert. Sie können Abhängigkeiten angeben, wenn andere Aufgaben vor der aktuellen Aufgabe ausgeführt werden müssen. Zusätzliche Funktionen ermöglichen es Ihnen, ausführen und sehen Sie sich die Gulp-Aufgaben, als auch legen Sie die Quelle (*Src*) und das Ziel (*Dest*) der Dateien, die geändert wird. Im folgenden sind die primären Gulp-API-Funktionen:

|Gulp-Funktion|Syntax|Beschreibung|
|---   |--- |--- |
|Aufgabe  |`gulp.task(name[, deps], fn) { }`|Die `task` Funktion erstellt eine Aufgabe. Die `name` Parameter definiert den Namen der Aufgabe. Die `deps` Parameter enthält ein Array von Aufgaben abgeschlossen werden, bevor diese Aufgabe ausgeführt wird. Die `fn` Parameter darstellt, eine Rückruffunktion, die die Vorgänge des Tasks ausgeführt werden.|
|Sehen Sie sich |`gulp.watch(glob [, opts], tasks) { }`|Die `watch` Funktion überwacht Dateien und führt Tasks aus, wenn eine Datei geändert wird. Die `glob` -Parameter ist ein `string` oder `array` , der bestimmt, welche Dateien überwacht. Die `opts` Parameter stellt zusätzliche Optionen für die Dateiliste bereit.|
|src   |`gulp.src(globs[, options]) { }`|Die `src` Funktion enthält Dateien, die die Glob-Werten entsprechen. Die `glob` -Parameter ist ein `string` oder `array` , der bestimmt, welche Dateien um zu lesen. Die `options` Parameter enthält zusätzliche Dateioptionen.|
|dest  |`gulp.dest(path[, options]) { }`|Die `dest` Funktion definiert einen Speicherort, auf die Dateien geschrieben werden können. Die `path` -Parameter ist eine Zeichenfolge oder eine Funktion, die den Zielordner bestimmt. Die `options` -Parameter ist ein Objekt, Ausgabe Ordneroptionen angibt.|

Zusätzliche Gulp-API-Referenzinformationen finden Sie unter [Gulp-API-Dokumentation-](https://gulpjs.org/API.html).

## <a name="gulp-recipes"></a>Gulp recipes

Die Gulp-Community bietet die Gulp [Rezepte](https://github.com/gulpjs/gulp/blob/master/docs/recipes/README.md). Diese Anleitungen bestehen aus Gulp-Tasks für allgemeine Szenarien.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Gulp-Dokumentation](https://github.com/gulpjs/gulp/blob/master/docs/README.md)
* [Bündelung und Minimierung in ASP.NET Core](bundling-and-minification.md)
* [Verwenden von Grunt in ASP.NET Core](using-grunt.md)

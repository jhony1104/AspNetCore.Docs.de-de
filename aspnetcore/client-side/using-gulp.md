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
# <a name="use-gulp-in-aspnet-core"></a><span data-ttu-id="8f175-103">Verwenden von Gulp in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8f175-103">Use Gulp in ASP.NET Core</span></span>

<span data-ttu-id="8f175-104">Durch [Scott Addie](https://scottaddie.com), [Shayne Boyer](https://twitter.com/spboyer), und [David Kiefer](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="8f175-104">By [Scott Addie](https://scottaddie.com), [Shayne Boyer](https://twitter.com/spboyer), and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="8f175-105">In einer typischen moderner Web-app können der Buildprozess ein:</span><span class="sxs-lookup"><span data-stu-id="8f175-105">In a typical modern web app, the build process might:</span></span>

* <span data-ttu-id="8f175-106">Bündelung und Minimierung von JavaScript- und CSS-Dateien.</span><span class="sxs-lookup"><span data-stu-id="8f175-106">Bundle and minify JavaScript and CSS files.</span></span>
* <span data-ttu-id="8f175-107">Führen Sie Tools zum Aufrufen der Bündelung und Minimierung Aufgaben vor jedem Build aus.</span><span class="sxs-lookup"><span data-stu-id="8f175-107">Run tools to call the bundling and minification tasks before each build.</span></span>
* <span data-ttu-id="8f175-108">Kompilieren Sie weniger oder SASS-Dateien, die CSS.</span><span class="sxs-lookup"><span data-stu-id="8f175-108">Compile LESS or SASS files to CSS.</span></span>
* <span data-ttu-id="8f175-109">Kompilieren Sie die Dateien CoffeeScript oder TypeScript in JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8f175-109">Compile CoffeeScript or TypeScript files to JavaScript.</span></span>

<span data-ttu-id="8f175-110">Ein *aufgabenausführung* ist ein Tool, das diese routinemäßiger Entwicklungs- und weitere Aufgaben automatisiert.</span><span class="sxs-lookup"><span data-stu-id="8f175-110">A *task runner* is a tool which automates these routine development tasks and more.</span></span> <span data-ttu-id="8f175-111">Visual Studio bietet integrierten Unterstützung für zwei beliebte JavaScript-basierten Task Runner: [Gulp](https://gulpjs.com/) und [Grunt](using-grunt.md).</span><span class="sxs-lookup"><span data-stu-id="8f175-111">Visual Studio provides built-in support for two popular JavaScript-based task runners: [Gulp](https://gulpjs.com/) and [Grunt](using-grunt.md).</span></span>

## <a name="gulp"></a><span data-ttu-id="8f175-112">Gulp</span><span class="sxs-lookup"><span data-stu-id="8f175-112">Gulp</span></span>

<span data-ttu-id="8f175-113">Gulp ist ein JavaScript-basiertes streaming Build Toolkit für clientseitigen Code.</span><span class="sxs-lookup"><span data-stu-id="8f175-113">Gulp is a JavaScript-based streaming build toolkit for client-side code.</span></span> <span data-ttu-id="8f175-114">Es wird häufig verwendet, um clientseitige Dateien über eine Reihe von Prozessen zu streamen, wenn ein bestimmtes Ereignis in einer Buildumgebung ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="8f175-114">It's commonly used to stream client-side files through a series of processes when a specific event is triggered in a build environment.</span></span> <span data-ttu-id="8f175-115">Z. B. Gulp dienen zum Automatisieren von [Bündelung und Minimierung](bundling-and-minification.md) oder dem Bereinigen von einer Entwicklungsumgebung, bevor Sie einen neuen Build.</span><span class="sxs-lookup"><span data-stu-id="8f175-115">For instance, Gulp can be used to automate [bundling and minification](bundling-and-minification.md) or the cleaning of a development environment before a new build.</span></span>

<span data-ttu-id="8f175-116">Eine Reihe von Gulp-Aufgaben wird in definiert *"gulpfile.js"*.</span><span class="sxs-lookup"><span data-stu-id="8f175-116">A set of Gulp tasks is defined in *gulpfile.js*.</span></span> <span data-ttu-id="8f175-117">Das folgende JavaScript umfasst Gulp-Module und gibt die Dateipfade in die neue Aufgaben verwiesen werden:</span><span class="sxs-lookup"><span data-stu-id="8f175-117">The following JavaScript includes Gulp modules and specifies file paths to be referenced within the forthcoming tasks:</span></span>

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

<span data-ttu-id="8f175-118">Der obige Code gibt an, welche Module Knoten erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="8f175-118">The above code specifies which Node modules are required.</span></span> <span data-ttu-id="8f175-119">Die `require` Funktionsimporte jedes Modul, damit der abhängigen Aufgaben ihre Features nutzen können.</span><span class="sxs-lookup"><span data-stu-id="8f175-119">The `require` function imports each module so that the dependent tasks can utilize their features.</span></span> <span data-ttu-id="8f175-120">Jedes der importierten Module wird einer Variablen zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="8f175-120">Each of the imported modules is assigned to a variable.</span></span> <span data-ttu-id="8f175-121">Die Module können entweder nach Name oder Pfad befinden.</span><span class="sxs-lookup"><span data-stu-id="8f175-121">The modules can be located either by name or path.</span></span> <span data-ttu-id="8f175-122">In diesem Beispiel die Module mit dem Namen `gulp`, `rimraf`, `gulp-concat`, `gulp-cssmin`, und `gulp-uglify` anhand des Namens abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="8f175-122">In this example, the modules named `gulp`, `rimraf`, `gulp-concat`, `gulp-cssmin`, and `gulp-uglify` are retrieved by name.</span></span> <span data-ttu-id="8f175-123">Darüber hinaus eine Reihe von Pfaden werden erstellt, sodass die Standorte von CSS und JavaScript-Dateien können erneut verwendet und die Aufgaben auf die verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="8f175-123">Additionally, a series of paths are created so that the locations of CSS and JavaScript files can be reused and referenced within the tasks.</span></span> <span data-ttu-id="8f175-124">Die folgende Tabelle enthält Beschreibungen der in enthaltenen Module *"gulpfile.js"*.</span><span class="sxs-lookup"><span data-stu-id="8f175-124">The following table provides descriptions of the modules included in *gulpfile.js*.</span></span>

| <span data-ttu-id="8f175-125">Modulname</span><span class="sxs-lookup"><span data-stu-id="8f175-125">Module Name</span></span> | <span data-ttu-id="8f175-126">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="8f175-126">Description</span></span> |
| ----------- | ----------- |
| <span data-ttu-id="8f175-127">Gulp</span><span class="sxs-lookup"><span data-stu-id="8f175-127">gulp</span></span>        | <span data-ttu-id="8f175-128">Erstellen Sie das Gulp-streaming auf System.</span><span class="sxs-lookup"><span data-stu-id="8f175-128">The Gulp streaming build system.</span></span> <span data-ttu-id="8f175-129">Weitere Informationen finden Sie unter [gulp](https://www.npmjs.com/package/gulp).</span><span class="sxs-lookup"><span data-stu-id="8f175-129">For more information, see [gulp](https://www.npmjs.com/package/gulp).</span></span> |
| <span data-ttu-id="8f175-130">rimraf</span><span class="sxs-lookup"><span data-stu-id="8f175-130">rimraf</span></span>      | <span data-ttu-id="8f175-131">Ein Modul, Knoten löschen.</span><span class="sxs-lookup"><span data-stu-id="8f175-131">A Node deletion module.</span></span> <span data-ttu-id="8f175-132">Weitere Informationen finden Sie unter [Rimraf](https://www.npmjs.com/package/rimraf).</span><span class="sxs-lookup"><span data-stu-id="8f175-132">For more information, see [rimraf](https://www.npmjs.com/package/rimraf).</span></span> |
| <span data-ttu-id="8f175-133">gulp-concat</span><span class="sxs-lookup"><span data-stu-id="8f175-133">gulp-concat</span></span> | <span data-ttu-id="8f175-134">Ein Modul, das verkettet Dateien auf Grundlage des Betriebssystems Zeilenumbruchzeichen.</span><span class="sxs-lookup"><span data-stu-id="8f175-134">A module that concatenates files based on the operating system's newline character.</span></span> <span data-ttu-id="8f175-135">Weitere Informationen finden Sie unter [Gulp-Concat](https://www.npmjs.com/package/gulp-concat).</span><span class="sxs-lookup"><span data-stu-id="8f175-135">For more information, see [gulp-concat](https://www.npmjs.com/package/gulp-concat).</span></span> |
| <span data-ttu-id="8f175-136">gulp-cssmin</span><span class="sxs-lookup"><span data-stu-id="8f175-136">gulp-cssmin</span></span> | <span data-ttu-id="8f175-137">Ein Modul, das CSS-Dateien verkleinert.</span><span class="sxs-lookup"><span data-stu-id="8f175-137">A module that minifies CSS files.</span></span> <span data-ttu-id="8f175-138">Weitere Informationen finden Sie unter [Gulp-Cssmin](https://www.npmjs.com/package/gulp-cssmin).</span><span class="sxs-lookup"><span data-stu-id="8f175-138">For more information, see [gulp-cssmin](https://www.npmjs.com/package/gulp-cssmin).</span></span> |
| <span data-ttu-id="8f175-139">gulp-uglify</span><span class="sxs-lookup"><span data-stu-id="8f175-139">gulp-uglify</span></span> | <span data-ttu-id="8f175-140">Ein Modul, das verkleinert *js* Dateien.</span><span class="sxs-lookup"><span data-stu-id="8f175-140">A module that minifies *.js* files.</span></span> <span data-ttu-id="8f175-141">Weitere Informationen finden Sie unter [Gulp-uglify](https://www.npmjs.com/package/gulp-uglify).</span><span class="sxs-lookup"><span data-stu-id="8f175-141">For more information, see [gulp-uglify](https://www.npmjs.com/package/gulp-uglify).</span></span> |

<span data-ttu-id="8f175-142">Nachdem die erforderlichen Module importiert wurden, können die Aufgaben angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="8f175-142">Once the requisite modules are imported, the tasks can be specified.</span></span> <span data-ttu-id="8f175-143">Es gibt sechs Aufgaben registriert, mit dem folgenden Code dargestellt:</span><span class="sxs-lookup"><span data-stu-id="8f175-143">Here there are six tasks registered, represented by the following code:</span></span>

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

<span data-ttu-id="8f175-144">Die folgende Tabelle enthält eine Erläuterung der Aufgaben im obigen Code angegeben:</span><span class="sxs-lookup"><span data-stu-id="8f175-144">The following table provides an explanation of the tasks specified in the code above:</span></span>

|<span data-ttu-id="8f175-145">Aufgabenname</span><span class="sxs-lookup"><span data-stu-id="8f175-145">Task Name</span></span>|<span data-ttu-id="8f175-146">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="8f175-146">Description</span></span>|
|--- |--- |
|<span data-ttu-id="8f175-147">clean:js</span><span class="sxs-lookup"><span data-stu-id="8f175-147">clean:js</span></span>|<span data-ttu-id="8f175-148">Eine Aufgabe, die die Löschung Rimraf knotenmodul verwendet, um die minimierte Version von der Datei "site.js" zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="8f175-148">A task that uses the rimraf Node deletion module to remove the minified version of the site.js file.</span></span>|
|<span data-ttu-id="8f175-149">Bereinigen: Css</span><span class="sxs-lookup"><span data-stu-id="8f175-149">clean:css</span></span>|<span data-ttu-id="8f175-150">Eine Aufgabe, die die Löschung Rimraf knotenmodul verwendet, um die minimierte Version der Datei "Site.CSS" zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="8f175-150">A task that uses the rimraf Node deletion module to remove the minified version of the site.css file.</span></span>|
|<span data-ttu-id="8f175-151">Bereinigen</span><span class="sxs-lookup"><span data-stu-id="8f175-151">clean</span></span>|<span data-ttu-id="8f175-152">Eine Aufgabe, die Aufrufe der `clean:js` Aufgabe, gefolgt von der `clean:css` Aufgabe.</span><span class="sxs-lookup"><span data-stu-id="8f175-152">A task that calls the `clean:js` task, followed by the `clean:css` task.</span></span>|
|<span data-ttu-id="8f175-153">min:js</span><span class="sxs-lookup"><span data-stu-id="8f175-153">min:js</span></span>|<span data-ttu-id="8f175-154">Eine Aufgabe, die verkleinert und verkettet alle JS-Dateien im Ordner "Js".</span><span class="sxs-lookup"><span data-stu-id="8f175-154">A task that minifies and concatenates all .js files within the js folder.</span></span> <span data-ttu-id="8f175-155">Die. min.js-Dateien ausgeschlossen sind.</span><span class="sxs-lookup"><span data-stu-id="8f175-155">The .min.js files are excluded.</span></span>|
|<span data-ttu-id="8f175-156">min:css</span><span class="sxs-lookup"><span data-stu-id="8f175-156">min:css</span></span>|<span data-ttu-id="8f175-157">Eine Aufgabe, die verkleinert und alle CSS-Dateien im Ordner "Css" verkettet.</span><span class="sxs-lookup"><span data-stu-id="8f175-157">A task that minifies and concatenates all .css files within the css folder.</span></span> <span data-ttu-id="8f175-158">Die. min.css Dateien ausgeschlossen sind.</span><span class="sxs-lookup"><span data-stu-id="8f175-158">The .min.css files are excluded.</span></span>|
|<span data-ttu-id="8f175-159">Min.</span><span class="sxs-lookup"><span data-stu-id="8f175-159">min</span></span>|<span data-ttu-id="8f175-160">Eine Aufgabe, die Aufrufe der `min:js` Aufgabe, gefolgt von der `min:css` Aufgabe.</span><span class="sxs-lookup"><span data-stu-id="8f175-160">A task that calls the `min:js` task, followed by the `min:css` task.</span></span>|

## <a name="running-default-tasks"></a><span data-ttu-id="8f175-161">Ausführen von Standardaufgaben</span><span class="sxs-lookup"><span data-stu-id="8f175-161">Running default tasks</span></span>

<span data-ttu-id="8f175-162">Wenn Sie eine neue Web-app bereits erstellt haben, erstellen Sie ein neues ASP.NET Web Application-Projekt in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8f175-162">If you haven't already created a new Web app, create a new ASP.NET Web Application project in Visual Studio.</span></span>

1. <span data-ttu-id="8f175-163">Öffnen der *"Package.JSON"* Datei (Hinzufügen Falls nicht vorhanden), und fügen Sie Folgendes hinzu.</span><span class="sxs-lookup"><span data-stu-id="8f175-163">Open the *package.json* file (add if not there) and add the following.</span></span>

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

2. <span data-ttu-id="8f175-164">Fügen Sie eine neue JavaScript-Datei zu Ihrem Projekt, und nennen Sie sie *"gulpfile.js"*, kopieren Sie den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="8f175-164">Add a new JavaScript file to your project and name it *gulpfile.js*, then copy the following code.</span></span>

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

3. <span data-ttu-id="8f175-165">In **Projektmappen-Explorer**, mit der rechten Maustaste *"gulpfile.js"*, und wählen Sie **Task Runner-Explorer**.</span><span class="sxs-lookup"><span data-stu-id="8f175-165">In **Solution Explorer**, right-click *gulpfile.js*, and select **Task Runner Explorer**.</span></span>

    ![Task Runner-Explorer im Projektmappen-Explorer öffnen](using-gulp/_static/02-SolutionExplorer-TaskRunnerExplorer.png)

    <span data-ttu-id="8f175-167">**Task Runner Explorer** zeigt die Liste der Gulp-Tasks.</span><span class="sxs-lookup"><span data-stu-id="8f175-167">**Task Runner Explorer** shows the list of Gulp tasks.</span></span> <span data-ttu-id="8f175-168">(Möglicherweise müssen Sie klicken Sie auf die **aktualisieren** Schaltfläche, auf der linken Seite, der den Namen des Projekts angezeigt wird.)</span><span class="sxs-lookup"><span data-stu-id="8f175-168">(You might have to click the **Refresh** button that appears to the left of the project name.)</span></span>

    ![Task Runner-Explorer](using-gulp/_static/03-TaskRunnerExplorer.png)

    > [!IMPORTANT]
    > <span data-ttu-id="8f175-170">Die **Task Runner-Explorer** Kontextmenüelement erscheint nur, wenn *"gulpfile.js"* befindet sich im Stammverzeichnis des Projekts.</span><span class="sxs-lookup"><span data-stu-id="8f175-170">The **Task Runner Explorer** context menu item appears only if *gulpfile.js* is in the root project directory.</span></span>

4. <span data-ttu-id="8f175-171">Darunter **Aufgaben** in **Task Runner-Explorer**, mit der rechten Maustaste **Bereinigen**, und wählen Sie **ausführen** aus dem Popupmenü.</span><span class="sxs-lookup"><span data-stu-id="8f175-171">Underneath **Tasks** in **Task Runner Explorer**, right-click **clean**, and select **Run** from the pop-up menu.</span></span>

    ![Task Runner-Explorer-clean-Aufgabe](using-gulp/_static/04-TaskRunner-clean.png)

    <span data-ttu-id="8f175-173">**Task Runner Explorer** erstellt eine neue Registerkarte mit dem Namen **Bereinigen** , und führen Sie die clean-Aufgabe in definierten *"gulpfile.js"*.</span><span class="sxs-lookup"><span data-stu-id="8f175-173">**Task Runner Explorer** will create a new tab named **clean** and execute the clean task as it's defined in *gulpfile.js*.</span></span>

5. <span data-ttu-id="8f175-174">Mit der rechten Maustaste die **Bereinigen** Aufgabe aus, und wählen Sie dann **Bindungen** > **vor dem Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="8f175-174">Right-click the **clean** task, then select **Bindings** > **Before Build**.</span></span>

    ![Binden von BeforeBuild Task Runner-Explorer](using-gulp/_static/05-TaskRunner-BeforeBuild.png)

    <span data-ttu-id="8f175-176">Die **vor dem Erstellen** Bindung konfiguriert, wird die clean-Aufgabe, die vor jedem Build des Projekts automatisch ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="8f175-176">The **Before Build** binding configures the clean task to run automatically before each build of the project.</span></span>

<span data-ttu-id="8f175-177">Die Bindungen Sie mit einrichten **Task Runner-Explorer** befinden sich in einen Kommentar am oberen Rand der Form Ihrer *"gulpfile.js"* und gelten nur in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8f175-177">The bindings you set up with **Task Runner Explorer** are stored in the form of a comment at the top of your *gulpfile.js* and are effective only in Visual Studio.</span></span> <span data-ttu-id="8f175-178">Eine Alternative, die keine Visual Studio erfordert wird, so konfigurieren Sie die automatische Ausführung von Gulp-Aufgaben in Ihrem *csproj* Datei.</span><span class="sxs-lookup"><span data-stu-id="8f175-178">An alternative that doesn't require Visual Studio is to configure automatic execution of gulp tasks in your *.csproj* file.</span></span> <span data-ttu-id="8f175-179">Fügen Sie diesen z. B. Ihrem *csproj* Datei:</span><span class="sxs-lookup"><span data-stu-id="8f175-179">For example, put this in your *.csproj* file:</span></span>

```xml
<Target Name="MyPreCompileTarget" BeforeTargets="Build">
  <Exec Command="gulp clean" />
</Target>
```

<span data-ttu-id="8f175-180">Nachdem die clean-Aufgabe ausgeführt wird, wenn Sie das Projekt in Visual Studio oder von einer Eingabeaufforderung Ausführen der ["Dotnet run"](/dotnet/core/tools/dotnet-run) Befehl (führen Sie `npm install` erste).</span><span class="sxs-lookup"><span data-stu-id="8f175-180">Now the clean task is executed when you run the project in Visual Studio or from a command prompt using the [dotnet run](/dotnet/core/tools/dotnet-run) command (run `npm install` first).</span></span>

## <a name="defining-and-running-a-new-task"></a><span data-ttu-id="8f175-181">Definieren und Ausführen eines neuen Tasks</span><span class="sxs-lookup"><span data-stu-id="8f175-181">Defining and running a new task</span></span>

<span data-ttu-id="8f175-182">Um ein neuer Gulp-Task zu definieren, ändern Sie *"gulpfile.js"*.</span><span class="sxs-lookup"><span data-stu-id="8f175-182">To define a new Gulp task, modify *gulpfile.js*.</span></span>

1. <span data-ttu-id="8f175-183">Fügen Sie den folgenden JavaScript-Code am Ende der *"gulpfile.js"*:</span><span class="sxs-lookup"><span data-stu-id="8f175-183">Add the following JavaScript to the end of *gulpfile.js*:</span></span>

    ```javascript
    gulp.task('first', done => {
      console.log('first task! <-----');
      done(); // signal completion
    });
    ```

    <span data-ttu-id="8f175-184">Diese Aufgabe heißt `first`, und zeigt einfach eine Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="8f175-184">This task is named `first`, and it simply displays a string.</span></span>

2. <span data-ttu-id="8f175-185">Speichern Sie *"gulpfile.js"*.</span><span class="sxs-lookup"><span data-stu-id="8f175-185">Save *gulpfile.js*.</span></span>

3. <span data-ttu-id="8f175-186">In **Projektmappen-Explorer**, mit der rechten Maustaste *"gulpfile.js"*, und wählen Sie *Task Runner-Explorer*.</span><span class="sxs-lookup"><span data-stu-id="8f175-186">In **Solution Explorer**, right-click *gulpfile.js*, and select *Task Runner Explorer*.</span></span>

4. <span data-ttu-id="8f175-187">In **Task Runner-Explorer**, mit der rechten Maustaste **erste**, und wählen Sie **ausführen**.</span><span class="sxs-lookup"><span data-stu-id="8f175-187">In **Task Runner Explorer**, right-click **first**, and select **Run**.</span></span>

    ![Führen Sie die erste Aufgabe Task Runner-Explorer](using-gulp/_static/06-TaskRunner-First.png)

    <span data-ttu-id="8f175-189">Der Ausgabetext wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="8f175-189">The output text is displayed.</span></span> <span data-ttu-id="8f175-190">Beispiele, die basierend auf allgemeine Szenarien finden Sie unter [Gulp Recipes](#gulp-recipes).</span><span class="sxs-lookup"><span data-stu-id="8f175-190">To see examples based on common scenarios, see [Gulp Recipes](#gulp-recipes).</span></span>

## <a name="defining-and-running-tasks-in-a-series"></a><span data-ttu-id="8f175-191">Definieren und Ausführen von Aufgaben in einer Reihe</span><span class="sxs-lookup"><span data-stu-id="8f175-191">Defining and running tasks in a series</span></span>

<span data-ttu-id="8f175-192">Wenn Sie mehrere Aufgaben ausführen, werden die Aufgaben gleichzeitig werden standardmäßig ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="8f175-192">When you run multiple tasks, the tasks run concurrently by default.</span></span> <span data-ttu-id="8f175-193">Aber wenn Sie Aufgaben in einer bestimmten Reihenfolge ausführen müssen, müssen Sie angeben Wenn jede Aufgabe abgeschlossen ist, sowie ist als die Aufgaben auf den Abschluss einer anderen Aufgabe abhängig sind.</span><span class="sxs-lookup"><span data-stu-id="8f175-193">However, if you need to run tasks in a specific order, you must specify when each task is complete, as well as which tasks depend on the completion of another task.</span></span>

1. <span data-ttu-id="8f175-194">Um eine Reihe von Aufgaben zur Ausführung in der Reihenfolge zu definieren, ersetzen die `first` Aufgabe, die Sie oben hinzugefügt, im haben *"gulpfile.js"* durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="8f175-194">To define a series of tasks to run in order, replace the `first` task that you added above in *gulpfile.js* with the following:</span></span>

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

    <span data-ttu-id="8f175-195">Sie verfügen jetzt über drei Aufgaben: `series:first`, `series:second`, und `series`.</span><span class="sxs-lookup"><span data-stu-id="8f175-195">You now have three tasks: `series:first`, `series:second`, and `series`.</span></span> <span data-ttu-id="8f175-196">Die `series:second` Aufgabe umfasst einen zweiten Parameter die gibt ein Array von Aufgaben ausgeführt und abgeschlossen, bevor die `series:second` Aufgabe wird ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="8f175-196">The `series:second` task includes a second parameter which specifies an array of tasks to be run and completed before the `series:second` task will run.</span></span> <span data-ttu-id="8f175-197">Gemäß den Angaben in den Code oben kann nur die `series:first` Aufgabe abgeschlossen werden muss, bevor Sie die `series:second` Aufgabe wird ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="8f175-197">As specified in the code above, only the `series:first` task must be completed before the `series:second` task will run.</span></span>

2. <span data-ttu-id="8f175-198">Speichern Sie *"gulpfile.js"*.</span><span class="sxs-lookup"><span data-stu-id="8f175-198">Save *gulpfile.js*.</span></span>

3. <span data-ttu-id="8f175-199">In **Projektmappen-Explorer**, mit der rechten Maustaste *"gulpfile.js"* , und wählen Sie **Task Runner-Explorer** , wenn es nicht bereits geöffnet ist.</span><span class="sxs-lookup"><span data-stu-id="8f175-199">In **Solution Explorer**, right-click *gulpfile.js* and select **Task Runner Explorer** if it isn't already open.</span></span>

4. <span data-ttu-id="8f175-200">In **Task Runner-Explorer**, mit der rechten Maustaste **Reihe** , und wählen Sie **ausführen**.</span><span class="sxs-lookup"><span data-stu-id="8f175-200">In **Task Runner Explorer**, right-click **series** and select **Run**.</span></span>

    ![Task Runner-Explorer Serie ausführen](using-gulp/_static/07-TaskRunner-Series.png)

## <a name="intellisense"></a><span data-ttu-id="8f175-202">IntelliSense</span><span class="sxs-lookup"><span data-stu-id="8f175-202">IntelliSense</span></span>

<span data-ttu-id="8f175-203">IntelliSense bietet codevervollständigung, parameterbeschreibungen und andere Funktionen, um die Produktivität zu steigern und Fehler zu reduzieren.</span><span class="sxs-lookup"><span data-stu-id="8f175-203">IntelliSense provides code completion, parameter descriptions, and other features to boost productivity and to decrease errors.</span></span> <span data-ttu-id="8f175-204">Gulp-Tasks werden in JavaScript geschrieben; aus diesem Grund bieten IntelliSense Unterstützung zu erhalten, während der Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="8f175-204">Gulp tasks are written in JavaScript; therefore, IntelliSense can provide assistance while developing.</span></span> <span data-ttu-id="8f175-205">Wie Sie mit JavaScript arbeiten, listet IntelliSense die Objekte, Funktionen, Eigenschaften und verfügbaren Parameter basierend auf den aktuellen Kontext.</span><span class="sxs-lookup"><span data-stu-id="8f175-205">As you work with JavaScript, IntelliSense lists the objects, functions, properties, and parameters that are available based on your current context.</span></span> <span data-ttu-id="8f175-206">Wählen Sie eine Codierungsoption aus der Popupliste aus, die von IntelliSense, um den Code fertig bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="8f175-206">Select a coding option from the pop-up list provided by IntelliSense to complete the code.</span></span>

![gulp-IntelliSense](using-gulp/_static/08-IntelliSense.png)

<span data-ttu-id="8f175-208">Weitere Informationen zu IntelliSense finden Sie unter [JavaScript IntelliSense](/visualstudio/ide/javascript-intellisense).</span><span class="sxs-lookup"><span data-stu-id="8f175-208">For more information about IntelliSense, see [JavaScript IntelliSense](/visualstudio/ide/javascript-intellisense).</span></span>

## <a name="development-staging-and-production-environments"></a><span data-ttu-id="8f175-209">Entwicklungs-, Staging-und produktionsumgebungen</span><span class="sxs-lookup"><span data-stu-id="8f175-209">Development, staging, and production environments</span></span>

<span data-ttu-id="8f175-210">Wenn Gulp zur Optimierung des clientseitigen Dateien für Staging und Produktion verwendet wird, werden die verarbeiteten Dateien in einen lokalen Speicherort für Staging und Produktion gespeichert.</span><span class="sxs-lookup"><span data-stu-id="8f175-210">When Gulp is used to optimize client-side files for staging and production, the processed files are saved to a local staging and production location.</span></span> <span data-ttu-id="8f175-211">Die *"_Layout.cshtml"* Datei verwendet die **Umgebung** taghilfsprogramm um zwei verschiedene Versionen von CSS-Dateien zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="8f175-211">The *_Layout.cshtml* file uses the **environment** tag helper to provide two different versions of CSS files.</span></span> <span data-ttu-id="8f175-212">Eine Version von CSS-Dateien für die Entwicklung, die andere Version ist für die Staging- und produktionsumgebungen optimiert.</span><span class="sxs-lookup"><span data-stu-id="8f175-212">One version of CSS files is for development and the other version is optimized for both staging and production.</span></span> <span data-ttu-id="8f175-213">In Visual Studio 2017, wenn Sie ändern die **"aspnetcore_environment"** Umgebungsvariable `Production`, Visual Studio erstellt die Web-app und ein Link zu den minimierten CSS-Dateien.</span><span class="sxs-lookup"><span data-stu-id="8f175-213">In Visual Studio 2017, when you change the **ASPNETCORE_ENVIRONMENT** environment variable to `Production`, Visual Studio will build the Web app and link to the minimized CSS files.</span></span> <span data-ttu-id="8f175-214">Das folgende Markup zeigt die **Umgebung** taghilfsprogramme, die mit Linktags, die `Development` CSS-Dateien und die minimierte `Staging, Production` CSS-Dateien.</span><span class="sxs-lookup"><span data-stu-id="8f175-214">The following markup shows the **environment** tag helpers containing link tags to the `Development` CSS files and the minified `Staging, Production` CSS files.</span></span>

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

## <a name="switching-between-environments"></a><span data-ttu-id="8f175-215">Wechseln zwischen Umgebungen</span><span class="sxs-lookup"><span data-stu-id="8f175-215">Switching between environments</span></span>

<span data-ttu-id="8f175-216">Um zwischen dem Kompilieren für verschiedene Umgebungen zu wechseln, ändern Sie die **"aspnetcore_environment"** Umgebung des Variablenwerts.</span><span class="sxs-lookup"><span data-stu-id="8f175-216">To switch between compiling for different environments, modify the **ASPNETCORE_ENVIRONMENT** environment variable's value.</span></span>

1. <span data-ttu-id="8f175-217">In **Task Runner-Explorer**, überprüfen Sie, ob die **min** auszuführende Aufgabe festgelegt wurde **vor dem Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="8f175-217">In **Task Runner Explorer**, verify that the **min** task has been set to run **Before Build**.</span></span>

2. <span data-ttu-id="8f175-218">In **Projektmappen-Explorer**mit der rechten Maustaste auf den Projektnamen, und wählen Sie **Eigenschaften**.</span><span class="sxs-lookup"><span data-stu-id="8f175-218">In **Solution Explorer**, right-click the project name and select **Properties**.</span></span>

    <span data-ttu-id="8f175-219">Das Eigenschaftenblatt für die Web-app wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="8f175-219">The property sheet for the Web app is displayed.</span></span>

3. <span data-ttu-id="8f175-220">Klicken Sie auf die Registerkarte **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="8f175-220">Click the **Debug** tab.</span></span>

4. <span data-ttu-id="8f175-221">Legen Sie den Wert, der die **Hostingumgebung:** -Umgebungsvariable so fest, `Production`.</span><span class="sxs-lookup"><span data-stu-id="8f175-221">Set the value of the **Hosting:Environment** environment variable to `Production`.</span></span>

5. <span data-ttu-id="8f175-222">Drücken Sie **F5** zum Ausführen der Anwendung in einem Browser.</span><span class="sxs-lookup"><span data-stu-id="8f175-222">Press **F5** to run the application in a browser.</span></span>

6. <span data-ttu-id="8f175-223">Klicken Sie im Browserfenster mit der rechten Maustaste in der Seite, und wählen **Quelltext anzeigen** um den HTML-Code für die Seite anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="8f175-223">In the browser window, right-click the page and select **View Source** to view the HTML for the page.</span></span>

    <span data-ttu-id="8f175-224">Beachten Sie, dass der Stylesheet-Links auf die minimierten CSS-Dateien verweisen.</span><span class="sxs-lookup"><span data-stu-id="8f175-224">Notice that the stylesheet links point to the minified CSS files.</span></span>

7. <span data-ttu-id="8f175-225">Schließen Sie den Browser, um die Web-app zu beenden.</span><span class="sxs-lookup"><span data-stu-id="8f175-225">Close the browser to stop the Web app.</span></span>

8. <span data-ttu-id="8f175-226">Klicken Sie in Visual Studio zurück, um das Eigenschaftenfenster für die Web-app, und ändern Sie die **Hostingumgebung:** Umgebungsvariablen zurück zum `Development`.</span><span class="sxs-lookup"><span data-stu-id="8f175-226">In Visual Studio, return to the property sheet for the Web app and change the **Hosting:Environment** environment variable back to `Development`.</span></span>

9. <span data-ttu-id="8f175-227">Drücken Sie **F5** zum Ausführen der Anwendung in einem Browser erneut.</span><span class="sxs-lookup"><span data-stu-id="8f175-227">Press **F5** to run the application in a browser again.</span></span>

10. <span data-ttu-id="8f175-228">Klicken Sie im Browserfenster mit der rechten Maustaste in der Seite, und wählen **Quelltext anzeigen** um den HTML-Code für die Seite anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="8f175-228">In the browser window, right-click the page and select **View Source** to see the HTML for the page.</span></span>

    <span data-ttu-id="8f175-229">Beachten Sie, dass der Stylesheet-Links auf die unminified Versionen der CSS-Dateien verweisen.</span><span class="sxs-lookup"><span data-stu-id="8f175-229">Notice that the stylesheet links point to the unminified versions of the CSS files.</span></span>

<span data-ttu-id="8f175-230">Weitere Informationen zu Umgebungen in ASP.NET Core, finden Sie unter [Verwenden mehrerer Umgebungen](../fundamentals/environments.md).</span><span class="sxs-lookup"><span data-stu-id="8f175-230">For more information related to environments in ASP.NET Core, see [Use multiple environments](../fundamentals/environments.md).</span></span>

## <a name="task-and-module-details"></a><span data-ttu-id="8f175-231">Task und -Modul-details</span><span class="sxs-lookup"><span data-stu-id="8f175-231">Task and module details</span></span>

<span data-ttu-id="8f175-232">Ein Gulp-Task ist ein Funktionsname registriert.</span><span class="sxs-lookup"><span data-stu-id="8f175-232">A Gulp task is registered with a function name.</span></span> <span data-ttu-id="8f175-233">Sie können Abhängigkeiten angeben, wenn andere Aufgaben vor der aktuellen Aufgabe ausgeführt werden müssen.</span><span class="sxs-lookup"><span data-stu-id="8f175-233">You can specify dependencies if other tasks must run before the current task.</span></span> <span data-ttu-id="8f175-234">Zusätzliche Funktionen ermöglichen es Ihnen, ausführen und sehen Sie sich die Gulp-Aufgaben, als auch legen Sie die Quelle (*Src*) und das Ziel (*Dest*) der Dateien, die geändert wird.</span><span class="sxs-lookup"><span data-stu-id="8f175-234">Additional functions allow you to run and watch the Gulp tasks, as well as set the source (*src*) and destination (*dest*) of the files being modified.</span></span> <span data-ttu-id="8f175-235">Im folgenden sind die primären Gulp-API-Funktionen:</span><span class="sxs-lookup"><span data-stu-id="8f175-235">The following are the primary Gulp API functions:</span></span>

|<span data-ttu-id="8f175-236">Gulp-Funktion</span><span class="sxs-lookup"><span data-stu-id="8f175-236">Gulp Function</span></span>|<span data-ttu-id="8f175-237">Syntax</span><span class="sxs-lookup"><span data-stu-id="8f175-237">Syntax</span></span>|<span data-ttu-id="8f175-238">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="8f175-238">Description</span></span>|
|---   |--- |--- |
|<span data-ttu-id="8f175-239">Aufgabe</span><span class="sxs-lookup"><span data-stu-id="8f175-239">task</span></span>  |`gulp.task(name[, deps], fn) { }`|<span data-ttu-id="8f175-240">Die `task` Funktion erstellt eine Aufgabe.</span><span class="sxs-lookup"><span data-stu-id="8f175-240">The `task` function creates a task.</span></span> <span data-ttu-id="8f175-241">Die `name` Parameter definiert den Namen der Aufgabe.</span><span class="sxs-lookup"><span data-stu-id="8f175-241">The `name` parameter defines the name of the task.</span></span> <span data-ttu-id="8f175-242">Die `deps` Parameter enthält ein Array von Aufgaben abgeschlossen werden, bevor diese Aufgabe ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="8f175-242">The `deps` parameter contains an array of tasks to be completed before this task runs.</span></span> <span data-ttu-id="8f175-243">Die `fn` Parameter darstellt, eine Rückruffunktion, die die Vorgänge des Tasks ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="8f175-243">The `fn` parameter represents a callback function which performs the operations of the task.</span></span>|
|<span data-ttu-id="8f175-244">Sehen Sie sich</span><span class="sxs-lookup"><span data-stu-id="8f175-244">watch</span></span> |`gulp.watch(glob [, opts], tasks) { }`|<span data-ttu-id="8f175-245">Die `watch` Funktion überwacht Dateien und führt Tasks aus, wenn eine Datei geändert wird.</span><span class="sxs-lookup"><span data-stu-id="8f175-245">The `watch` function monitors files and runs tasks when a file change occurs.</span></span> <span data-ttu-id="8f175-246">Die `glob` -Parameter ist ein `string` oder `array` , der bestimmt, welche Dateien überwacht.</span><span class="sxs-lookup"><span data-stu-id="8f175-246">The `glob` parameter is a `string` or `array` that determines which files to watch.</span></span> <span data-ttu-id="8f175-247">Die `opts` Parameter stellt zusätzliche Optionen für die Dateiliste bereit.</span><span class="sxs-lookup"><span data-stu-id="8f175-247">The `opts` parameter provides additional file watching options.</span></span>|
|<span data-ttu-id="8f175-248">src</span><span class="sxs-lookup"><span data-stu-id="8f175-248">src</span></span>   |`gulp.src(globs[, options]) { }`|<span data-ttu-id="8f175-249">Die `src` Funktion enthält Dateien, die die Glob-Werten entsprechen.</span><span class="sxs-lookup"><span data-stu-id="8f175-249">The `src` function provides files that match the glob value(s).</span></span> <span data-ttu-id="8f175-250">Die `glob` -Parameter ist ein `string` oder `array` , der bestimmt, welche Dateien um zu lesen.</span><span class="sxs-lookup"><span data-stu-id="8f175-250">The `glob` parameter is a `string` or `array` that determines which files to read.</span></span> <span data-ttu-id="8f175-251">Die `options` Parameter enthält zusätzliche Dateioptionen.</span><span class="sxs-lookup"><span data-stu-id="8f175-251">The `options` parameter provides additional file options.</span></span>|
|<span data-ttu-id="8f175-252">dest</span><span class="sxs-lookup"><span data-stu-id="8f175-252">dest</span></span>  |`gulp.dest(path[, options]) { }`|<span data-ttu-id="8f175-253">Die `dest` Funktion definiert einen Speicherort, auf die Dateien geschrieben werden können.</span><span class="sxs-lookup"><span data-stu-id="8f175-253">The `dest` function defines a location to which files can be written.</span></span> <span data-ttu-id="8f175-254">Die `path` -Parameter ist eine Zeichenfolge oder eine Funktion, die den Zielordner bestimmt.</span><span class="sxs-lookup"><span data-stu-id="8f175-254">The `path` parameter is a string or function that determines the destination folder.</span></span> <span data-ttu-id="8f175-255">Die `options` -Parameter ist ein Objekt, Ausgabe Ordneroptionen angibt.</span><span class="sxs-lookup"><span data-stu-id="8f175-255">The `options` parameter is an object that specifies output folder options.</span></span>|

<span data-ttu-id="8f175-256">Zusätzliche Gulp-API-Referenzinformationen finden Sie unter [Gulp-API-Dokumentation-](https://gulpjs.org/API.html).</span><span class="sxs-lookup"><span data-stu-id="8f175-256">For additional Gulp API reference information, see [Gulp Docs API](https://gulpjs.org/API.html).</span></span>

## <a name="gulp-recipes"></a><span data-ttu-id="8f175-257">Gulp recipes</span><span class="sxs-lookup"><span data-stu-id="8f175-257">Gulp recipes</span></span>

<span data-ttu-id="8f175-258">Die Gulp-Community bietet die Gulp [Rezepte](https://github.com/gulpjs/gulp/blob/master/docs/recipes/README.md).</span><span class="sxs-lookup"><span data-stu-id="8f175-258">The Gulp community provides Gulp [Recipes](https://github.com/gulpjs/gulp/blob/master/docs/recipes/README.md).</span></span> <span data-ttu-id="8f175-259">Diese Anleitungen bestehen aus Gulp-Tasks für allgemeine Szenarien.</span><span class="sxs-lookup"><span data-stu-id="8f175-259">These recipes consist of Gulp tasks to address common scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8f175-260">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="8f175-260">Additional resources</span></span>

* [<span data-ttu-id="8f175-261">Gulp-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="8f175-261">Gulp documentation</span></span>](https://github.com/gulpjs/gulp/blob/master/docs/README.md)
* [<span data-ttu-id="8f175-262">Bündelung und Minimierung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8f175-262">Bundling and minification in ASP.NET Core</span></span>](bundling-and-minification.md)
* [<span data-ttu-id="8f175-263">Verwenden von Grunt in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8f175-263">Use Grunt in ASP.NET Core</span></span>](using-grunt.md)

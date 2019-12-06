---
title: Verwenden von grunt in ASP.net Core
author: rick-anderson
description: Verwenden von grunt in ASP.net Core
ms.author: riande
ms.date: 12/05/2019
uid: client-side/using-grunt
ms.openlocfilehash: e516b85da7e94d0c93be642086fede0a11fea3c2
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74879794"
---
# <a name="use-grunt-in-aspnet-core"></a><span data-ttu-id="303e6-103">Verwenden von grunt in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="303e6-103">Use Grunt in ASP.NET Core</span></span>

<span data-ttu-id="303e6-104">Grunt ist eine JavaScript-Aufgabenausführung, die Skript Minimierung, typescript-Kompilierung, lint-Tools für die Codequalität, CSS-Präprozessoren und fast alle wiederkehrenden Aufgabe automatisiert, die zur Unterstützung der Client Entwicklung erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="303e6-104">Grunt is a JavaScript task runner that automates script minification, TypeScript compilation, code quality "lint" tools, CSS pre-processors, and just about any repetitive chore that needs doing to support client development.</span></span> <span data-ttu-id="303e6-105">Grunt wird in Visual Studio vollständig unterstützt.</span><span class="sxs-lookup"><span data-stu-id="303e6-105">Grunt is fully supported in Visual Studio.</span></span>

<span data-ttu-id="303e6-106">In diesem Beispiel wird ein leeres ASP.net Core Projekt als Ausgangspunkt verwendet, um anzuzeigen, wie der clientbuildprozess von Grund auf neu automatisiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="303e6-106">This example uses an empty ASP.NET Core project as its starting point, to show how to automate the client build process from scratch.</span></span>

<span data-ttu-id="303e6-107">Das fertige Beispiel bereinigt das Ziel Bereitstellungs Verzeichnis, kombiniert JavaScript-Dateien, überprüft die Codequalität, fasst JavaScript-Dateiinhalte zusammen und stellt Sie im Stammverzeichnis Ihrer Webanwendung bereit.</span><span class="sxs-lookup"><span data-stu-id="303e6-107">The finished example cleans the target deployment directory, combines JavaScript files, checks code quality, condenses JavaScript file content and deploys to the root of your web application.</span></span> <span data-ttu-id="303e6-108">Wir verwenden die folgenden Pakete:</span><span class="sxs-lookup"><span data-stu-id="303e6-108">We will use the following packages:</span></span>

* <span data-ttu-id="303e6-109">**grunt**: das grunt Task Runner-Paket.</span><span class="sxs-lookup"><span data-stu-id="303e6-109">**grunt**: The Grunt task runner package.</span></span>

* <span data-ttu-id="303e6-110">**grunt-contrib-Clean**: ein Plug-in, mit dem Dateien oder Verzeichnisse entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="303e6-110">**grunt-contrib-clean**: A plugin that removes files or directories.</span></span>

* <span data-ttu-id="303e6-111">**grunt-contrib-jshint**: ein Plug-in, das die Qualität von JavaScript-Code überprüft.</span><span class="sxs-lookup"><span data-stu-id="303e6-111">**grunt-contrib-jshint**: A plugin that reviews JavaScript code quality.</span></span>

* <span data-ttu-id="303e6-112">**grunt-contrib-Concat**: ein Plug-in, das Dateien zu einer einzelnen Datei verknüpft.</span><span class="sxs-lookup"><span data-stu-id="303e6-112">**grunt-contrib-concat**: A plugin that joins files into a single file.</span></span>

* <span data-ttu-id="303e6-113">**grunt-contrib-uglify**: ein Plug-in, das JavaScript minimiert, um die Größe zu verringern.</span><span class="sxs-lookup"><span data-stu-id="303e6-113">**grunt-contrib-uglify**: A plugin that minifies JavaScript to reduce size.</span></span>

* <span data-ttu-id="303e6-114">**grunt-contrib-Watch**: ein Plug-in, das die Datei Aktivität überwacht.</span><span class="sxs-lookup"><span data-stu-id="303e6-114">**grunt-contrib-watch**: A plugin that watches file activity.</span></span>

## <a name="preparing-the-application"></a><span data-ttu-id="303e6-115">Vorbereiten der Anwendung</span><span class="sxs-lookup"><span data-stu-id="303e6-115">Preparing the application</span></span>

<span data-ttu-id="303e6-116">Richten Sie zunächst eine neue leere Webanwendung ein, und fügen Sie typescript-Beispieldateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="303e6-116">To begin, set up a new empty web application and add TypeScript example files.</span></span> <span data-ttu-id="303e6-117">Typescript-Dateien werden automatisch mithilfe von Visual Studio-Standardeinstellungen in JavaScript kompiliert und sind das Rohmaterial, das mit grunt verarbeitet werden soll.</span><span class="sxs-lookup"><span data-stu-id="303e6-117">TypeScript files are automatically compiled into JavaScript using default Visual Studio settings and will be our raw material to process using Grunt.</span></span>

1. <span data-ttu-id="303e6-118">Erstellen Sie in Visual Studio eine neue `ASP.NET Web Application`.</span><span class="sxs-lookup"><span data-stu-id="303e6-118">In Visual Studio, create a new `ASP.NET Web Application`.</span></span>

2. <span data-ttu-id="303e6-119">Wählen Sie im Dialogfeld **Neues ASP.net-Projekt** das ASP.net Core **leere** Vorlage aus, und klicken Sie auf die Schaltfläche OK.</span><span class="sxs-lookup"><span data-stu-id="303e6-119">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template and click the OK button.</span></span>

3. <span data-ttu-id="303e6-120">Überprüfen Sie im Projektmappen-Explorer die Projektstruktur.</span><span class="sxs-lookup"><span data-stu-id="303e6-120">In the Solution Explorer, review the project structure.</span></span> <span data-ttu-id="303e6-121">Der `\src` Ordner enthält leere `wwwroot` und `Dependencies` Knoten.</span><span class="sxs-lookup"><span data-stu-id="303e6-121">The `\src` folder includes empty `wwwroot` and `Dependencies` nodes.</span></span>

    ![leere Weblösung](using-grunt/_static/grunt-solution-explorer.png)

4. <span data-ttu-id="303e6-123">Fügen Sie dem Projektverzeichnis einen neuen Ordner mit dem Namen `TypeScript` hinzu.</span><span class="sxs-lookup"><span data-stu-id="303e6-123">Add a new folder named `TypeScript` to your project directory.</span></span>

5. <span data-ttu-id="303e6-124">Vergewissern Sie sich vor dem Hinzufügen von Dateien, dass Visual Studio die Option "beim Speichern kompilieren" für typescript-Dateien aktiviert hat.</span><span class="sxs-lookup"><span data-stu-id="303e6-124">Before adding any files, make sure that Visual Studio has the option 'compile on save' for TypeScript files checked.</span></span> <span data-ttu-id="303e6-125">Navigieren Sie **zu** Extras > **Optionen** > **Text-Editor** > **typescript** > **Projekt**:</span><span class="sxs-lookup"><span data-stu-id="303e6-125">Navigate to **Tools** > **Options** > **Text Editor** > **Typescript** > **Project**:</span></span>

    ![Optionen zum Festlegen der automatischen Kompilierung von typescript-Dateien](using-grunt/_static/typescript-options.png)

6. <span data-ttu-id="303e6-127">Klicken Sie mit der rechten Maustaste auf das `TypeScript` Verzeichnis, und wählen Sie im Kontextmenü **> Neues Element hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="303e6-127">Right-click the `TypeScript` directory and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="303e6-128">Wählen Sie das **JavaScript-Datei** Element aus, und nennen Sie die Datei " *Geschmacks. TS* " (Beachten Sie die \*. TS-Erweiterung)</span><span class="sxs-lookup"><span data-stu-id="303e6-128">Select the **JavaScript file** item and name the file *Tastes.ts* (note the \*.ts extension).</span></span> <span data-ttu-id="303e6-129">Kopieren Sie die Zeile des typescript-Codes weiter unten in die Datei (wenn Sie speichern, wird eine neue Datei " *Geschmäcker. js* " mit der JavaScript-Quelle angezeigt).</span><span class="sxs-lookup"><span data-stu-id="303e6-129">Copy the line of TypeScript code below into the file (when you save, a new *Tastes.js* file will appear with the JavaScript source).</span></span>

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. <span data-ttu-id="303e6-130">Fügen Sie dem **typescript** -Verzeichnis eine zweite Datei hinzu, und benennen Sie Sie `Food.ts`.</span><span class="sxs-lookup"><span data-stu-id="303e6-130">Add a second file to the **TypeScript** directory and name it `Food.ts`.</span></span> <span data-ttu-id="303e6-131">Kopieren Sie den folgenden Code in die Datei.</span><span class="sxs-lookup"><span data-stu-id="303e6-131">Copy the code below into the file.</span></span>

    ```typescript
    class Food {
      constructor(name: string, calories: number) {
        this._name = name;
        this._calories = calories;
      }

      private _name: string;
      get Name() {
        return this._name;
      }

      private _calories: number;
      get Calories() {
        return this._calories;
      }

      private _taste: Tastes;
      get Taste(): Tastes { return this._taste }
      set Taste(value: Tastes) {
        this._taste = value;
      }
    }
    ```

## <a name="configuring-npm"></a><span data-ttu-id="303e6-132">Konfigurieren von NPM</span><span class="sxs-lookup"><span data-stu-id="303e6-132">Configuring NPM</span></span>

<span data-ttu-id="303e6-133">Als Nächstes konfigurieren Sie NPM, um grunt-und grunt-Tasks herunterzuladen.</span><span class="sxs-lookup"><span data-stu-id="303e6-133">Next, configure NPM to download grunt and grunt-tasks.</span></span>

1. <span data-ttu-id="303e6-134">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie im Kontextmenü **> Neues Element hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="303e6-134">In the Solution Explorer, right-click the project and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="303e6-135">Wählen Sie das Element **NPM-Konfigurationsdatei** aus, belassen Sie den Standardnamen *Package. JSON*, und klicken Sie auf die Schaltfläche **Hinzufügen** .</span><span class="sxs-lookup"><span data-stu-id="303e6-135">Select the **NPM configuration file** item, leave the default name, *package.json*, and click the **Add** button.</span></span>

2. <span data-ttu-id="303e6-136">Geben Sie in der Datei " *Package. JSON* " in den Klammern des `devDependencies` Objekts "Grunt" ein.</span><span class="sxs-lookup"><span data-stu-id="303e6-136">In the *package.json* file, inside the `devDependencies` object braces, enter "grunt".</span></span> <span data-ttu-id="303e6-137">Wählen Sie `grunt` aus der IntelliSense-Liste aus, und drücken Sie die EINGABETASTE.</span><span class="sxs-lookup"><span data-stu-id="303e6-137">Select `grunt` from the Intellisense list and press the Enter key.</span></span> <span data-ttu-id="303e6-138">Visual Studio gibt den Namen des grunt-Pakets an und fügt einen Doppelpunkt hinzu.</span><span class="sxs-lookup"><span data-stu-id="303e6-138">Visual Studio will quote the grunt package name, and add a colon.</span></span> <span data-ttu-id="303e6-139">Wählen Sie rechts neben dem Doppelpunkt die neueste stabile Version des Pakets am oberen Rand der IntelliSense-Liste aus (drücken Sie `Ctrl-Space`, wenn IntelliSense nicht angezeigt wird).</span><span class="sxs-lookup"><span data-stu-id="303e6-139">To the right of the colon, select the latest stable version of the package from the top of the Intellisense list (press `Ctrl-Space` if Intellisense doesn't appear).</span></span>

    ![grunt IntelliSense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > <span data-ttu-id="303e6-141">NPM verwendet die [semantische Versions](https://semver.org/) Verwaltung, um Abhängigkeiten zu organisieren.</span><span class="sxs-lookup"><span data-stu-id="303e6-141">NPM uses [semantic versioning](https://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="303e6-142">Die semantische Versionierung, auch bekannt als semver, identifiziert Pakete mit dem Nummerierungschema \<Haupt >.\<neben >.\<Patch >.</span><span class="sxs-lookup"><span data-stu-id="303e6-142">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>.</span></span> <span data-ttu-id="303e6-143">IntelliSense vereinfacht die semantische Versionsverwaltung, da nur einige allgemeine Optionen angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="303e6-143">Intellisense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="303e6-144">Das oberste Element in der IntelliSense-Liste (0.4.5 im obigen Beispiel) wird als neueste stabile Version des Pakets betrachtet.</span><span class="sxs-lookup"><span data-stu-id="303e6-144">The top item in the Intellisense list (0.4.5 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="303e6-145">Das Caretzeichen (^) stimmt mit der aktuellen Hauptversion überein, und die Tilde (~) entspricht der neuesten neben Version.</span><span class="sxs-lookup"><span data-stu-id="303e6-145">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span> <span data-ttu-id="303e6-146">Weitere Informationen zur vollständigen Ausdrucksweise von semver finden Sie in der [Referenz zu NPM semver Version Parser](https://www.npmjs.com/package/semver) als Leitfaden.</span><span class="sxs-lookup"><span data-stu-id="303e6-146">See the [NPM semver version parser reference](https://www.npmjs.com/package/semver) as a guide to the full expressivity that SemVer provides.</span></span>

3. <span data-ttu-id="303e6-147">Fügen Sie weitere Abhängigkeiten hinzu, um grunt-contrib-\* Pakete für *Clean*, *jshint*, *Concat*, *uglify*und *Watch* zu laden, wie im folgenden Beispiel gezeigt.</span><span class="sxs-lookup"><span data-stu-id="303e6-147">Add more dependencies to load grunt-contrib-\* packages for *clean*, *jshint*, *concat*, *uglify*, and *watch* as shown in the example below.</span></span> <span data-ttu-id="303e6-148">Die Versionen müssen nicht mit dem Beispiel identisch sein.</span><span class="sxs-lookup"><span data-stu-id="303e6-148">The versions don't need to match the example.</span></span>

    ```json
    "devDependencies": {
      "grunt": "0.4.5",
      "grunt-contrib-clean": "0.6.0",
      "grunt-contrib-jshint": "0.11.0",
      "grunt-contrib-concat": "0.5.1",
      "grunt-contrib-uglify": "0.8.0",
      "grunt-contrib-watch": "0.6.1"
    }
    ```

4. <span data-ttu-id="303e6-149">Speichern Sie die Datei " *Package. JSON* ".</span><span class="sxs-lookup"><span data-stu-id="303e6-149">Save the *package.json* file.</span></span>

<span data-ttu-id="303e6-150">Die Pakete für die einzelnen `devDependencies` Elemente werden zusammen mit allen Dateien heruntergeladen, die von den einzelnen Paketen benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="303e6-150">The packages for each `devDependencies` item will download, along with any files that each package requires.</span></span> <span data-ttu-id="303e6-151">Sie finden die Paketdateien im *node_modules* Verzeichnis, indem Sie die Schaltfläche **alle Dateien anzeigen** in **Projektmappen-Explorer**aktivieren.</span><span class="sxs-lookup"><span data-stu-id="303e6-151">You can find the package files in the *node_modules* directory by enabling the **Show All Files** button in **Solution Explorer**.</span></span>

![grunt-node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> <span data-ttu-id="303e6-153">Wenn erforderlich, können Sie Abhängigkeiten in **Projektmappen-Explorer** manuell wiederherstellen, indem Sie mit der rechten Maustaste auf `Dependencies\NPM` klicken und die Menüoption **Pakete wiederherstellen** auswählen.</span><span class="sxs-lookup"><span data-stu-id="303e6-153">If you need to, you can manually restore dependencies in **Solution Explorer** by right-clicking on `Dependencies\NPM` and selecting the **Restore Packages** menu option.</span></span>

![Pakete wiederherstellen](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a><span data-ttu-id="303e6-155">Konfigurieren von grunt</span><span class="sxs-lookup"><span data-stu-id="303e6-155">Configuring Grunt</span></span>

<span data-ttu-id="303e6-156">Grunt wird mithilfe eines Manifests namens *gruntfile. js* konfiguriert, das Aufgaben definiert, lädt und registriert, die manuell ausgeführt oder für die automatische Ausführung auf der Grundlage von Ereignissen in Visual Studio konfiguriert werden können.</span><span class="sxs-lookup"><span data-stu-id="303e6-156">Grunt is configured using a manifest named *Gruntfile.js* that defines, loads and registers tasks that can be run manually or configured to run automatically based on events in Visual Studio.</span></span>

1. <span data-ttu-id="303e6-157">Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie > **Neues Element** **Hinzufügen**</span><span class="sxs-lookup"><span data-stu-id="303e6-157">Right-click the project and select **Add** > **New Item**.</span></span> <span data-ttu-id="303e6-158">Wählen Sie die **JavaScript-Datei** Element Vorlage aus, ändern Sie den Namen in *gruntfile. js*, und klicken Sie auf die Schaltfläche **Hinzufügen** .</span><span class="sxs-lookup"><span data-stu-id="303e6-158">Select the **JavaScript File** item template, change the name to *Gruntfile.js*, and click the **Add** button.</span></span>

1. <span data-ttu-id="303e6-159">Fügen Sie " *gruntfile. js*" den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="303e6-159">Add the following code to *Gruntfile.js*.</span></span> <span data-ttu-id="303e6-160">Mit der `initConfig`-Funktion werden Optionen für jedes Paket festgelegt, und der Rest des Moduls lädt und registriert Tasks.</span><span class="sxs-lookup"><span data-stu-id="303e6-160">The `initConfig` function sets options for each package, and the remainder of the module loads and register tasks.</span></span>

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. <span data-ttu-id="303e6-161">Fügen Sie innerhalb der `initConfig`-Funktion Optionen für die `clean`-Aufgabe hinzu, wie im folgenden Beispiel gezeigt: *gruntfile. js* .</span><span class="sxs-lookup"><span data-stu-id="303e6-161">Inside the `initConfig` function, add options for the `clean` task as shown in the example *Gruntfile.js* below.</span></span> <span data-ttu-id="303e6-162">Die `clean` Aufgabe akzeptiert ein Array von Verzeichnis Zeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="303e6-162">The `clean` task accepts an array of directory strings.</span></span> <span data-ttu-id="303e6-163">Mit diesem Task werden Dateien aus *wwwroot/lib* entfernt und das gesamte */Temp abgelegt* -Verzeichnis entfernt.</span><span class="sxs-lookup"><span data-stu-id="303e6-163">This task removes files from *wwwroot/lib* and removes the entire */temp* directory.</span></span>

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. <span data-ttu-id="303e6-164">Fügen Sie unter der `initConfig`-Funktion einen-Aufruf`grunt.loadNpmTasks`hinzu.</span><span class="sxs-lookup"><span data-stu-id="303e6-164">Below the `initConfig` function, add a call to `grunt.loadNpmTasks`.</span></span> <span data-ttu-id="303e6-165">Dadurch wird die Aufgabe aus Visual Studio entfernt.</span><span class="sxs-lookup"><span data-stu-id="303e6-165">This will make the task runnable from Visual Studio.</span></span>

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. <span data-ttu-id="303e6-166">Speichern Sie *gruntfile. js*.</span><span class="sxs-lookup"><span data-stu-id="303e6-166">Save *Gruntfile.js*.</span></span> <span data-ttu-id="303e6-167">Die Datei sollte in etwa wie der folgende Screenshot aussehen.</span><span class="sxs-lookup"><span data-stu-id="303e6-167">The file should look something like the screenshot below.</span></span>

    ![anfängliche gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. <span data-ttu-id="303e6-169">Klicken Sie mit der rechten Maustaste auf *gruntfile. js* , und wählen Sie im Kontextmenü **Task Runner-Explorer** aus.</span><span class="sxs-lookup"><span data-stu-id="303e6-169">Right-click *Gruntfile.js* and select **Task Runner Explorer** from the context menu.</span></span> <span data-ttu-id="303e6-170">Das Fenster **Task Runner Explorer** wird geöffnet.</span><span class="sxs-lookup"><span data-stu-id="303e6-170">The **Task Runner Explorer** window will open.</span></span>

    ![Menü "Aufgaben Runner-Explorer"](using-grunt/_static/task-runner-explorer-menu.png)

1. <span data-ttu-id="303e6-172">Vergewissern Sie sich, dass `clean` unter **Aufgaben** im **Task Runner-Explorer**angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="303e6-172">Verify that `clean` shows under **Tasks** in the **Task Runner Explorer**.</span></span>

    ![Aufgaben Ausführungs-Explorer-Aufgabenliste](using-grunt/_static/task-runner-explorer-tasks.png)

1. <span data-ttu-id="303e6-174">Klicken Sie mit der rechten Maustaste auf die Aufgabe bereinigen, und wählen Sie im Kontextmenü **Ausführen**</span><span class="sxs-lookup"><span data-stu-id="303e6-174">Right-click the clean task and select **Run** from the context menu.</span></span> <span data-ttu-id="303e6-175">Im Befehlsfenster wird der Fortschritt der Aufgabe angezeigt.</span><span class="sxs-lookup"><span data-stu-id="303e6-175">A command window displays progress of the task.</span></span>

    ![Task Runner-Explorer ausführen von Clean Task](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > <span data-ttu-id="303e6-177">Es sind noch keine Dateien oder Verzeichnisse zum Bereinigen vorhanden.</span><span class="sxs-lookup"><span data-stu-id="303e6-177">There are no files or directories to clean yet.</span></span> <span data-ttu-id="303e6-178">Wenn Sie möchten, können Sie diese manuell im Projektmappen-Explorer erstellen und dann die Aufgabe bereinigen als Test ausführen.</span><span class="sxs-lookup"><span data-stu-id="303e6-178">If you like, you can manually create them in the Solution Explorer and then run the clean task as a test.</span></span>

1. <span data-ttu-id="303e6-179">Fügen Sie in der `initConfig`-Funktion einen Eintrag für `concat` mit dem folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="303e6-179">In the `initConfig` function, add an entry for `concat` using the code below.</span></span>

    <span data-ttu-id="303e6-180">Das `src`-Eigenschaften Array listet die zu kombinierenden Dateien in der Reihenfolge auf, in der Sie kombiniert werden sollten.</span><span class="sxs-lookup"><span data-stu-id="303e6-180">The `src` property array lists files to combine, in the order that they should be combined.</span></span> <span data-ttu-id="303e6-181">Die `dest`-Eigenschaft weist den Pfad der kombinierten Datei zu, die erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="303e6-181">The `dest` property assigns the path to the combined file that's produced.</span></span>

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="303e6-182">Die `all`-Eigenschaft im obigen Code ist der Name eines Ziels.</span><span class="sxs-lookup"><span data-stu-id="303e6-182">The `all` property in the code above is the name of a target.</span></span> <span data-ttu-id="303e6-183">Ziele werden in einigen grunt-Aufgaben verwendet, um mehrere Buildumgebungen zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="303e6-183">Targets are used in some Grunt tasks to allow multiple build environments.</span></span> <span data-ttu-id="303e6-184">Sie können die integrierten Ziele mithilfe von IntelliSense anzeigen oder eigene Ziele zuweisen.</span><span class="sxs-lookup"><span data-stu-id="303e6-184">You can view the built-in targets using IntelliSense or assign your own.</span></span>

1. <span data-ttu-id="303e6-185">Fügen Sie die `jshint` Aufgabe mit dem folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="303e6-185">Add the `jshint` task using the code below.</span></span>

    <span data-ttu-id="303e6-186">Das Hilfsprogramm jshint `code-quality` wird für jede JavaScript-Datei im Verzeichnis *Temp* ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="303e6-186">The jshint `code-quality` utility is run against every JavaScript file found in the *temp* directory.</span></span>

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="303e6-187">Die Option "-W069" ist ein Fehler, der von jshint erzeugt wird, wenn JavaScript die Klammer Syntax verwendet, um eine Eigenschaft anstelle der Punkt Notation zuzuweisen, d. h. `Tastes["Sweet"]` statt `Tastes.Sweet`.</span><span class="sxs-lookup"><span data-stu-id="303e6-187">The option "-W069" is an error produced by jshint when JavaScript uses bracket syntax to assign a property instead of dot notation, i.e. `Tastes["Sweet"]` instead of `Tastes.Sweet`.</span></span> <span data-ttu-id="303e6-188">Mit der-Option wird die Warnung deaktiviert, damit der restliche Prozess fortgesetzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="303e6-188">The option turns off the warning to allow the rest of the process to continue.</span></span>

1. <span data-ttu-id="303e6-189">Fügen Sie die `uglify` Aufgabe mit dem folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="303e6-189">Add the `uglify` task using the code below.</span></span>

    <span data-ttu-id="303e6-190">Der Task miniert die *kombinierte js* -Datei im Verzeichnis "Temp" und erstellt die Ergebnisdatei in wwwroot/lib gemäß der Standard Benennungs Konvention *\<Dateiname\>. min. js*.</span><span class="sxs-lookup"><span data-stu-id="303e6-190">The task minifies the *combined.js* file found in the temp directory and creates the result file in wwwroot/lib following the standard naming convention *\<file name\>.min.js*.</span></span>

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. <span data-ttu-id="303e6-191">Fügen Sie unter dem Aufrufen von `grunt.loadNpmTasks`, der `grunt-contrib-clean`lädt, den gleichen-Befehl für jshint, Concat und uglify mit dem folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="303e6-191">Under the call to `grunt.loadNpmTasks` that loads `grunt-contrib-clean`, include the same call for jshint, concat, and uglify using the code below.</span></span>

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. <span data-ttu-id="303e6-192">Speichern Sie *gruntfile. js*.</span><span class="sxs-lookup"><span data-stu-id="303e6-192">Save *Gruntfile.js*.</span></span> <span data-ttu-id="303e6-193">Die Datei sollte in etwa wie im folgenden Beispiel aussehen.</span><span class="sxs-lookup"><span data-stu-id="303e6-193">The file should look something like the example below.</span></span>

    ![Beispiel für eine komplette grunt-Datei](using-grunt/_static/gruntfile-js-complete.png)

1. <span data-ttu-id="303e6-195">Beachten Sie, dass Aufgaben **Runner-Explorer** -Aufgabenliste `clean`, `concat`, `jshint` und `uglify` Tasks enthält.</span><span class="sxs-lookup"><span data-stu-id="303e6-195">Notice that the **Task Runner Explorer** Tasks list includes `clean`, `concat`, `jshint` and `uglify` tasks.</span></span> <span data-ttu-id="303e6-196">Führen Sie jede Aufgabe nacheinander aus, und beobachten Sie die Ergebnisse in **Projektmappen-Explorer**.</span><span class="sxs-lookup"><span data-stu-id="303e6-196">Run each task in order and observe the results in **Solution Explorer**.</span></span> <span data-ttu-id="303e6-197">Jede Aufgabe sollte ohne Fehler ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="303e6-197">Each task should run without errors.</span></span>

    ![Task Runner-Explorer ausführen der einzelnen Aufgaben](using-grunt/_static/task-runner-explorer-run-each-task.png)

    <span data-ttu-id="303e6-199">Der Concat-Task erstellt eine neue *kombinierte js* -Datei und platziert Sie im temporären Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="303e6-199">The concat task creates a new *combined.js* file and places it into the temp directory.</span></span> <span data-ttu-id="303e6-200">Der `jshint` Task wird einfach ausgeführt, und es wird keine Ausgabe erzeugt.</span><span class="sxs-lookup"><span data-stu-id="303e6-200">The `jshint` task simply runs and doesn't produce output.</span></span> <span data-ttu-id="303e6-201">Der `uglify` Task erstellt eine neue *kombinierte. min. js* -Datei und platziert Sie in *wwwroot/lib*.</span><span class="sxs-lookup"><span data-stu-id="303e6-201">The `uglify` task creates a new *combined.min.js* file and places it into *wwwroot/lib*.</span></span> <span data-ttu-id="303e6-202">Nach Abschluss sollte die Projekt Mappe in etwa wie im folgenden Screenshot aussehen:</span><span class="sxs-lookup"><span data-stu-id="303e6-202">On completion, the solution should look something like the screenshot below:</span></span>

    ![Projektmappen-Explorer nach allen Aufgaben](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > <span data-ttu-id="303e6-204">Weitere Informationen zu den Optionen für jedes Paket finden Sie unter [https://www.npmjs.com/](https://www.npmjs.com/) und Nachschlagen des Paket namens im Suchfeld auf der Hauptseite.</span><span class="sxs-lookup"><span data-stu-id="303e6-204">For more information on the options for each package, visit [https://www.npmjs.com/](https://www.npmjs.com/) and lookup the package name in the search box on the main page.</span></span> <span data-ttu-id="303e6-205">Beispielsweise können Sie das grunt-contrib-Clean-Paket suchen, um einen Dokumentations Link zu erhalten, in dem alle seine Parameter erläutert werden.</span><span class="sxs-lookup"><span data-stu-id="303e6-205">For example, you can look up the grunt-contrib-clean package to get a documentation link that explains all of its parameters.</span></span>

### <a name="all-together-now"></a><span data-ttu-id="303e6-206">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="303e6-206">All together now</span></span>

<span data-ttu-id="303e6-207">Verwenden Sie die grunt-`registerTask()`-Methode, um eine Reihe von Aufgaben in einer bestimmten Sequenz auszuführen.</span><span class="sxs-lookup"><span data-stu-id="303e6-207">Use the Grunt `registerTask()` method to run a series of tasks in a particular sequence.</span></span> <span data-ttu-id="303e6-208">Um z. b. die obigen Beispiel Schritte in der Order Clean-> Concat-> jshint-> uglify auszuführen, fügen Sie dem-Modul den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="303e6-208">For example, to run the example steps above in the order clean -> concat -> jshint -> uglify, add the code below to the module.</span></span> <span data-ttu-id="303e6-209">Der Code sollte der gleichen Ebene wie die loadnpmtasks ()-Aufrufe außerhalb von InitConfig hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="303e6-209">The code should be added to the same level as the loadNpmTasks() calls, outside initConfig.</span></span>

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

<span data-ttu-id="303e6-210">Die neue Aufgabe wird im Task Runner-Explorer unter Alias Tasks angezeigt.</span><span class="sxs-lookup"><span data-stu-id="303e6-210">The new task shows up in Task Runner Explorer under Alias Tasks.</span></span> <span data-ttu-id="303e6-211">Sie können mit der rechten Maustaste darauf klicken und Sie genauso wie andere Aufgaben ausführen.</span><span class="sxs-lookup"><span data-stu-id="303e6-211">You can right-click and run it just as you would other tasks.</span></span> <span data-ttu-id="303e6-212">Der `all` Task führt `clean`, `concat`, `jshint` und `uglify`in der richtigen Reihenfolge aus.</span><span class="sxs-lookup"><span data-stu-id="303e6-212">The `all` task will run `clean`, `concat`, `jshint` and `uglify`, in order.</span></span>

![Alias-grunt-Aufgaben](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a><span data-ttu-id="303e6-214">Erkennen von Änderungen</span><span class="sxs-lookup"><span data-stu-id="303e6-214">Watching for changes</span></span>

<span data-ttu-id="303e6-215">Mit einer `watch` Aufgabe werden Dateien und Verzeichnisse im Auge behalten.</span><span class="sxs-lookup"><span data-stu-id="303e6-215">A `watch` task keeps an eye on files and directories.</span></span> <span data-ttu-id="303e6-216">Die Überwachung löst Aufgaben automatisch aus, wenn Änderungen erkannt werden.</span><span class="sxs-lookup"><span data-stu-id="303e6-216">The watch triggers tasks automatically if it detects changes.</span></span> <span data-ttu-id="303e6-217">Fügen Sie in InitConfig den folgenden Code hinzu, um Änderungen an \*. js-Dateien im typescript-Verzeichnis zu überwachen.</span><span class="sxs-lookup"><span data-stu-id="303e6-217">Add the code below to initConfig to watch for changes to \*.js files in the TypeScript directory.</span></span> <span data-ttu-id="303e6-218">Wenn eine JavaScript-Datei geändert wird, wird `watch` die `all` Aufgabe ausführen.</span><span class="sxs-lookup"><span data-stu-id="303e6-218">If a JavaScript file is changed, `watch` will run the `all` task.</span></span>

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

<span data-ttu-id="303e6-219">Fügen Sie einen `loadNpmTasks()` hinzu, um die `watch` Aufgabe im Task Runner-Explorer anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="303e6-219">Add a call to `loadNpmTasks()` to show the `watch` task in Task Runner Explorer.</span></span>

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

<span data-ttu-id="303e6-220">Klicken Sie im Task Runner Explorer mit der rechten Maustaste auf die Aufgabe überwachen, und wählen Sie im Kontextmenü Ausführen aus.</span><span class="sxs-lookup"><span data-stu-id="303e6-220">Right-click the watch task in Task Runner Explorer and select Run from the context menu.</span></span> <span data-ttu-id="303e6-221">Im Befehlsfenster, in dem die ausgestellte Aufgabe "überwachen" angezeigt wird, wird ein "warten..." Nachricht.</span><span class="sxs-lookup"><span data-stu-id="303e6-221">The command window that shows the watch task running will display a "Waiting…" message.</span></span> <span data-ttu-id="303e6-222">Öffnen Sie eine der typescript-Dateien, fügen Sie ein Leerzeichen hinzu, und speichern Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="303e6-222">Open one of the TypeScript files, add a space, and then save the file.</span></span> <span data-ttu-id="303e6-223">Dadurch wird die Aufgabe "überwachen" auslöst und die anderen Tasks in der Reihenfolge ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="303e6-223">This will trigger the watch task and trigger the other tasks to run in order.</span></span> <span data-ttu-id="303e6-224">Der folgende Screenshot zeigt eine Beispiel Testlauf.</span><span class="sxs-lookup"><span data-stu-id="303e6-224">The screenshot below shows a sample run.</span></span>

![Ausführen der Aufgaben Ausgabe](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a><span data-ttu-id="303e6-226">Binden an Visual Studio-Ereignisse</span><span class="sxs-lookup"><span data-stu-id="303e6-226">Binding to Visual Studio events</span></span>

<span data-ttu-id="303e6-227">Wenn Sie die Aufgaben nicht jedes Mal manuell starten möchten, wenn Sie in Visual Studio arbeiten, binden Sie Aufgaben an, **bevor Sie erstellt**werden, **nachdem**Sie erstellt, **bereinigt**und Ereignisse **geöffnet** haben.</span><span class="sxs-lookup"><span data-stu-id="303e6-227">Unless you want to manually start your tasks every time you work in Visual Studio, bind tasks to **Before Build**, **After Build**, **Clean**, and **Project Open** events.</span></span>

<span data-ttu-id="303e6-228">Binden Sie `watch` so, dass er jedes Mal ausgeführt wird, wenn Visual Studio geöffnet wird.</span><span class="sxs-lookup"><span data-stu-id="303e6-228">Bind `watch` so that it runs every time Visual Studio opens.</span></span> <span data-ttu-id="303e6-229">Klicken Sie im Task Runner Explorer mit der rechten Maustaste auf die Aufgabe überwachen, und wählen Sie im Kontextmenü die Option **Bindungen** > **Projekt öffnen** aus.</span><span class="sxs-lookup"><span data-stu-id="303e6-229">In Task Runner Explorer, right-click the watch task and select **Bindings** > **Project Open** from the context menu.</span></span>

![Binden einer Aufgabe an das Projekt öffnen](using-grunt/_static/bindings-project-open.png)

<span data-ttu-id="303e6-231">Entladen und laden Sie das Projekt erneut.</span><span class="sxs-lookup"><span data-stu-id="303e6-231">Unload and reload the project.</span></span> <span data-ttu-id="303e6-232">Wenn das Projekt erneut geladen wird, wird der Überwachungs Task automatisch gestartet.</span><span class="sxs-lookup"><span data-stu-id="303e6-232">When the project loads again, the watch task starts running automatically.</span></span>

## <a name="summary"></a><span data-ttu-id="303e6-233">Summary</span><span class="sxs-lookup"><span data-stu-id="303e6-233">Summary</span></span>

<span data-ttu-id="303e6-234">Grunt ist ein leistungsfähiger Aufgaben Runner, der zum Automatisieren der meisten clientbuildaufgaben verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="303e6-234">Grunt is a powerful task runner that can be used to automate most client-build tasks.</span></span> <span data-ttu-id="303e6-235">Grunt nutzt NPM für die Bereitstellung der Pakete und bietet Funktionen für die Tools-Integration in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="303e6-235">Grunt leverages NPM to deliver its packages, and features tooling integration with Visual Studio.</span></span> <span data-ttu-id="303e6-236">Der Task Runner-Explorer von Visual Studio erkennt Änderungen an Konfigurationsdateien und stellt eine bequeme Oberfläche zum Ausführen von Aufgaben, zum Anzeigen laufender Aufgaben und zum Binden von Aufgaben an Visual Studio-Ereignisse bereit.</span><span class="sxs-lookup"><span data-stu-id="303e6-236">Visual Studio's Task Runner Explorer detects changes to configuration files and provides a convenient interface to run tasks, view running tasks, and bind tasks to Visual Studio events.</span></span>

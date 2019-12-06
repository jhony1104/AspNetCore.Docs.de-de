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
# <a name="use-grunt-in-aspnet-core"></a>Verwenden von grunt in ASP.net Core

Grunt ist eine JavaScript-Aufgabenausführung, die Skript Minimierung, typescript-Kompilierung, lint-Tools für die Codequalität, CSS-Präprozessoren und fast alle wiederkehrenden Aufgabe automatisiert, die zur Unterstützung der Client Entwicklung erforderlich sind. Grunt wird in Visual Studio vollständig unterstützt.

In diesem Beispiel wird ein leeres ASP.net Core Projekt als Ausgangspunkt verwendet, um anzuzeigen, wie der clientbuildprozess von Grund auf neu automatisiert werden kann.

Das fertige Beispiel bereinigt das Ziel Bereitstellungs Verzeichnis, kombiniert JavaScript-Dateien, überprüft die Codequalität, fasst JavaScript-Dateiinhalte zusammen und stellt Sie im Stammverzeichnis Ihrer Webanwendung bereit. Wir verwenden die folgenden Pakete:

* **grunt**: das grunt Task Runner-Paket.

* **grunt-contrib-Clean**: ein Plug-in, mit dem Dateien oder Verzeichnisse entfernt werden.

* **grunt-contrib-jshint**: ein Plug-in, das die Qualität von JavaScript-Code überprüft.

* **grunt-contrib-Concat**: ein Plug-in, das Dateien zu einer einzelnen Datei verknüpft.

* **grunt-contrib-uglify**: ein Plug-in, das JavaScript minimiert, um die Größe zu verringern.

* **grunt-contrib-Watch**: ein Plug-in, das die Datei Aktivität überwacht.

## <a name="preparing-the-application"></a>Vorbereiten der Anwendung

Richten Sie zunächst eine neue leere Webanwendung ein, und fügen Sie typescript-Beispieldateien hinzu. Typescript-Dateien werden automatisch mithilfe von Visual Studio-Standardeinstellungen in JavaScript kompiliert und sind das Rohmaterial, das mit grunt verarbeitet werden soll.

1. Erstellen Sie in Visual Studio eine neue `ASP.NET Web Application`.

2. Wählen Sie im Dialogfeld **Neues ASP.net-Projekt** das ASP.net Core **leere** Vorlage aus, und klicken Sie auf die Schaltfläche OK.

3. Überprüfen Sie im Projektmappen-Explorer die Projektstruktur. Der `\src` Ordner enthält leere `wwwroot` und `Dependencies` Knoten.

    ![leere Weblösung](using-grunt/_static/grunt-solution-explorer.png)

4. Fügen Sie dem Projektverzeichnis einen neuen Ordner mit dem Namen `TypeScript` hinzu.

5. Vergewissern Sie sich vor dem Hinzufügen von Dateien, dass Visual Studio die Option "beim Speichern kompilieren" für typescript-Dateien aktiviert hat. Navigieren Sie **zu** Extras > **Optionen** > **Text-Editor** > **typescript** > **Projekt**:

    ![Optionen zum Festlegen der automatischen Kompilierung von typescript-Dateien](using-grunt/_static/typescript-options.png)

6. Klicken Sie mit der rechten Maustaste auf das `TypeScript` Verzeichnis, und wählen Sie im Kontextmenü **> Neues Element hinzufügen** aus. Wählen Sie das **JavaScript-Datei** Element aus, und nennen Sie die Datei " *Geschmacks. TS* " (Beachten Sie die \*. TS-Erweiterung) Kopieren Sie die Zeile des typescript-Codes weiter unten in die Datei (wenn Sie speichern, wird eine neue Datei " *Geschmäcker. js* " mit der JavaScript-Quelle angezeigt).

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. Fügen Sie dem **typescript** -Verzeichnis eine zweite Datei hinzu, und benennen Sie Sie `Food.ts`. Kopieren Sie den folgenden Code in die Datei.

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

## <a name="configuring-npm"></a>Konfigurieren von NPM

Als Nächstes konfigurieren Sie NPM, um grunt-und grunt-Tasks herunterzuladen.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie im Kontextmenü **> Neues Element hinzufügen** aus. Wählen Sie das Element **NPM-Konfigurationsdatei** aus, belassen Sie den Standardnamen *Package. JSON*, und klicken Sie auf die Schaltfläche **Hinzufügen** .

2. Geben Sie in der Datei " *Package. JSON* " in den Klammern des `devDependencies` Objekts "Grunt" ein. Wählen Sie `grunt` aus der IntelliSense-Liste aus, und drücken Sie die EINGABETASTE. Visual Studio gibt den Namen des grunt-Pakets an und fügt einen Doppelpunkt hinzu. Wählen Sie rechts neben dem Doppelpunkt die neueste stabile Version des Pakets am oberen Rand der IntelliSense-Liste aus (drücken Sie `Ctrl-Space`, wenn IntelliSense nicht angezeigt wird).

    ![grunt IntelliSense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > NPM verwendet die [semantische Versions](https://semver.org/) Verwaltung, um Abhängigkeiten zu organisieren. Die semantische Versionierung, auch bekannt als semver, identifiziert Pakete mit dem Nummerierungschema \<Haupt >.\<neben >.\<Patch >. IntelliSense vereinfacht die semantische Versionsverwaltung, da nur einige allgemeine Optionen angezeigt werden. Das oberste Element in der IntelliSense-Liste (0.4.5 im obigen Beispiel) wird als neueste stabile Version des Pakets betrachtet. Das Caretzeichen (^) stimmt mit der aktuellen Hauptversion überein, und die Tilde (~) entspricht der neuesten neben Version. Weitere Informationen zur vollständigen Ausdrucksweise von semver finden Sie in der [Referenz zu NPM semver Version Parser](https://www.npmjs.com/package/semver) als Leitfaden.

3. Fügen Sie weitere Abhängigkeiten hinzu, um grunt-contrib-\* Pakete für *Clean*, *jshint*, *Concat*, *uglify*und *Watch* zu laden, wie im folgenden Beispiel gezeigt. Die Versionen müssen nicht mit dem Beispiel identisch sein.

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

4. Speichern Sie die Datei " *Package. JSON* ".

Die Pakete für die einzelnen `devDependencies` Elemente werden zusammen mit allen Dateien heruntergeladen, die von den einzelnen Paketen benötigt werden. Sie finden die Paketdateien im *node_modules* Verzeichnis, indem Sie die Schaltfläche **alle Dateien anzeigen** in **Projektmappen-Explorer**aktivieren.

![grunt-node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> Wenn erforderlich, können Sie Abhängigkeiten in **Projektmappen-Explorer** manuell wiederherstellen, indem Sie mit der rechten Maustaste auf `Dependencies\NPM` klicken und die Menüoption **Pakete wiederherstellen** auswählen.

![Pakete wiederherstellen](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a>Konfigurieren von grunt

Grunt wird mithilfe eines Manifests namens *gruntfile. js* konfiguriert, das Aufgaben definiert, lädt und registriert, die manuell ausgeführt oder für die automatische Ausführung auf der Grundlage von Ereignissen in Visual Studio konfiguriert werden können.

1. Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie > **Neues Element** **Hinzufügen** Wählen Sie die **JavaScript-Datei** Element Vorlage aus, ändern Sie den Namen in *gruntfile. js*, und klicken Sie auf die Schaltfläche **Hinzufügen** .

1. Fügen Sie " *gruntfile. js*" den folgenden Code hinzu. Mit der `initConfig`-Funktion werden Optionen für jedes Paket festgelegt, und der Rest des Moduls lädt und registriert Tasks.

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. Fügen Sie innerhalb der `initConfig`-Funktion Optionen für die `clean`-Aufgabe hinzu, wie im folgenden Beispiel gezeigt: *gruntfile. js* . Die `clean` Aufgabe akzeptiert ein Array von Verzeichnis Zeichenfolgen. Mit diesem Task werden Dateien aus *wwwroot/lib* entfernt und das gesamte */Temp abgelegt* -Verzeichnis entfernt.

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. Fügen Sie unter der `initConfig`-Funktion einen-Aufruf`grunt.loadNpmTasks`hinzu. Dadurch wird die Aufgabe aus Visual Studio entfernt.

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. Speichern Sie *gruntfile. js*. Die Datei sollte in etwa wie der folgende Screenshot aussehen.

    ![anfängliche gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. Klicken Sie mit der rechten Maustaste auf *gruntfile. js* , und wählen Sie im Kontextmenü **Task Runner-Explorer** aus. Das Fenster **Task Runner Explorer** wird geöffnet.

    ![Menü "Aufgaben Runner-Explorer"](using-grunt/_static/task-runner-explorer-menu.png)

1. Vergewissern Sie sich, dass `clean` unter **Aufgaben** im **Task Runner-Explorer**angezeigt wird.

    ![Aufgaben Ausführungs-Explorer-Aufgabenliste](using-grunt/_static/task-runner-explorer-tasks.png)

1. Klicken Sie mit der rechten Maustaste auf die Aufgabe bereinigen, und wählen Sie im Kontextmenü **Ausführen** Im Befehlsfenster wird der Fortschritt der Aufgabe angezeigt.

    ![Task Runner-Explorer ausführen von Clean Task](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > Es sind noch keine Dateien oder Verzeichnisse zum Bereinigen vorhanden. Wenn Sie möchten, können Sie diese manuell im Projektmappen-Explorer erstellen und dann die Aufgabe bereinigen als Test ausführen.

1. Fügen Sie in der `initConfig`-Funktion einen Eintrag für `concat` mit dem folgenden Code hinzu.

    Das `src`-Eigenschaften Array listet die zu kombinierenden Dateien in der Reihenfolge auf, in der Sie kombiniert werden sollten. Die `dest`-Eigenschaft weist den Pfad der kombinierten Datei zu, die erstellt wird.

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > Die `all`-Eigenschaft im obigen Code ist der Name eines Ziels. Ziele werden in einigen grunt-Aufgaben verwendet, um mehrere Buildumgebungen zuzulassen. Sie können die integrierten Ziele mithilfe von IntelliSense anzeigen oder eigene Ziele zuweisen.

1. Fügen Sie die `jshint` Aufgabe mit dem folgenden Code hinzu.

    Das Hilfsprogramm jshint `code-quality` wird für jede JavaScript-Datei im Verzeichnis *Temp* ausgeführt.

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > Die Option "-W069" ist ein Fehler, der von jshint erzeugt wird, wenn JavaScript die Klammer Syntax verwendet, um eine Eigenschaft anstelle der Punkt Notation zuzuweisen, d. h. `Tastes["Sweet"]` statt `Tastes.Sweet`. Mit der-Option wird die Warnung deaktiviert, damit der restliche Prozess fortgesetzt werden kann.

1. Fügen Sie die `uglify` Aufgabe mit dem folgenden Code hinzu.

    Der Task miniert die *kombinierte js* -Datei im Verzeichnis "Temp" und erstellt die Ergebnisdatei in wwwroot/lib gemäß der Standard Benennungs Konvention *\<Dateiname\>. min. js*.

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. Fügen Sie unter dem Aufrufen von `grunt.loadNpmTasks`, der `grunt-contrib-clean`lädt, den gleichen-Befehl für jshint, Concat und uglify mit dem folgenden Code hinzu.

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. Speichern Sie *gruntfile. js*. Die Datei sollte in etwa wie im folgenden Beispiel aussehen.

    ![Beispiel für eine komplette grunt-Datei](using-grunt/_static/gruntfile-js-complete.png)

1. Beachten Sie, dass Aufgaben **Runner-Explorer** -Aufgabenliste `clean`, `concat`, `jshint` und `uglify` Tasks enthält. Führen Sie jede Aufgabe nacheinander aus, und beobachten Sie die Ergebnisse in **Projektmappen-Explorer**. Jede Aufgabe sollte ohne Fehler ausgeführt werden.

    ![Task Runner-Explorer ausführen der einzelnen Aufgaben](using-grunt/_static/task-runner-explorer-run-each-task.png)

    Der Concat-Task erstellt eine neue *kombinierte js* -Datei und platziert Sie im temporären Verzeichnis. Der `jshint` Task wird einfach ausgeführt, und es wird keine Ausgabe erzeugt. Der `uglify` Task erstellt eine neue *kombinierte. min. js* -Datei und platziert Sie in *wwwroot/lib*. Nach Abschluss sollte die Projekt Mappe in etwa wie im folgenden Screenshot aussehen:

    ![Projektmappen-Explorer nach allen Aufgaben](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > Weitere Informationen zu den Optionen für jedes Paket finden Sie unter [https://www.npmjs.com/](https://www.npmjs.com/) und Nachschlagen des Paket namens im Suchfeld auf der Hauptseite. Beispielsweise können Sie das grunt-contrib-Clean-Paket suchen, um einen Dokumentations Link zu erhalten, in dem alle seine Parameter erläutert werden.

### <a name="all-together-now"></a>Zusammenfassung

Verwenden Sie die grunt-`registerTask()`-Methode, um eine Reihe von Aufgaben in einer bestimmten Sequenz auszuführen. Um z. b. die obigen Beispiel Schritte in der Order Clean-> Concat-> jshint-> uglify auszuführen, fügen Sie dem-Modul den folgenden Code hinzu. Der Code sollte der gleichen Ebene wie die loadnpmtasks ()-Aufrufe außerhalb von InitConfig hinzugefügt werden.

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

Die neue Aufgabe wird im Task Runner-Explorer unter Alias Tasks angezeigt. Sie können mit der rechten Maustaste darauf klicken und Sie genauso wie andere Aufgaben ausführen. Der `all` Task führt `clean`, `concat`, `jshint` und `uglify`in der richtigen Reihenfolge aus.

![Alias-grunt-Aufgaben](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a>Erkennen von Änderungen

Mit einer `watch` Aufgabe werden Dateien und Verzeichnisse im Auge behalten. Die Überwachung löst Aufgaben automatisch aus, wenn Änderungen erkannt werden. Fügen Sie in InitConfig den folgenden Code hinzu, um Änderungen an \*. js-Dateien im typescript-Verzeichnis zu überwachen. Wenn eine JavaScript-Datei geändert wird, wird `watch` die `all` Aufgabe ausführen.

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

Fügen Sie einen `loadNpmTasks()` hinzu, um die `watch` Aufgabe im Task Runner-Explorer anzuzeigen.

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

Klicken Sie im Task Runner Explorer mit der rechten Maustaste auf die Aufgabe überwachen, und wählen Sie im Kontextmenü Ausführen aus. Im Befehlsfenster, in dem die ausgestellte Aufgabe "überwachen" angezeigt wird, wird ein "warten..." Nachricht. Öffnen Sie eine der typescript-Dateien, fügen Sie ein Leerzeichen hinzu, und speichern Sie die Datei. Dadurch wird die Aufgabe "überwachen" auslöst und die anderen Tasks in der Reihenfolge ausgeführt. Der folgende Screenshot zeigt eine Beispiel Testlauf.

![Ausführen der Aufgaben Ausgabe](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a>Binden an Visual Studio-Ereignisse

Wenn Sie die Aufgaben nicht jedes Mal manuell starten möchten, wenn Sie in Visual Studio arbeiten, binden Sie Aufgaben an, **bevor Sie erstellt**werden, **nachdem**Sie erstellt, **bereinigt**und Ereignisse **geöffnet** haben.

Binden Sie `watch` so, dass er jedes Mal ausgeführt wird, wenn Visual Studio geöffnet wird. Klicken Sie im Task Runner Explorer mit der rechten Maustaste auf die Aufgabe überwachen, und wählen Sie im Kontextmenü die Option **Bindungen** > **Projekt öffnen** aus.

![Binden einer Aufgabe an das Projekt öffnen](using-grunt/_static/bindings-project-open.png)

Entladen und laden Sie das Projekt erneut. Wenn das Projekt erneut geladen wird, wird der Überwachungs Task automatisch gestartet.

## <a name="summary"></a>Summary

Grunt ist ein leistungsfähiger Aufgaben Runner, der zum Automatisieren der meisten clientbuildaufgaben verwendet werden kann. Grunt nutzt NPM für die Bereitstellung der Pakete und bietet Funktionen für die Tools-Integration in Visual Studio. Der Task Runner-Explorer von Visual Studio erkennt Änderungen an Konfigurationsdateien und stellt eine bequeme Oberfläche zum Ausführen von Aufgaben, zum Anzeigen laufender Aufgaben und zum Binden von Aufgaben an Visual Studio-Ereignisse bereit.

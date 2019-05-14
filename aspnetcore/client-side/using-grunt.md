---
title: Verwenden von Grunt in ASP.NET Core
author: rick-anderson
description: Verwenden von Grunt in ASP.NET Core
ms.author: riande
ms.date: 05/10/2019
uid: client-side/using-grunt
ms.openlocfilehash: 718a1358c0474711b05bb2c90dc86ec9edacbf1e
ms.sourcegitcommit: 6afe57fb8d9055f88fedb92b16470398c4b9b24a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65610216"
---
# <a name="use-grunt-in-aspnet-core"></a>Verwenden von Grunt in ASP.NET Core

Durch [Noel Reis](https://blog.falafel.com/falafel-software-recognized-sitefinity-website-year/)

Grunt ist eine JavaScript-aufgabenausführung, die automatisiert Skript Minimierung, TypeScript-Kompilierung, Code "linten" Qualitätstools, Pre CSS-Prozessoren und fast jeder sich wiederholende Aufgabe, die vor, um die Cliententwicklung zu unterstützen. Grunt wird vollständig in Visual Studio unterstützt.

Dieses Beispiel verwendet ein leeres ASP.NET Core-Projekt als Ausgangspunkt, veranschaulichen, wie Sie die Client-Buildprozess von Grund auf neu zu automatisieren.

Das fertige Beispiel löscht das Zielverzeichnis für die Bereitstellung, kombiniert JavaScript-Dateien, überprüft der Codequalität, fasst der Inhalt der JavaScript-Datei und in das Stammverzeichnis der Webanwendung bereitgestellt. Wir verwenden die folgenden Pakete:

* **grunt**: Das Grunt Task Runner-Paket.

* **grunt-contrib-clean**: Ein Plug-in, das Dateien oder Verzeichnisse entfernt.

* **grunt-contrib-jshint**: Ein Plug-in, das Qualität der JavaScript-Code überprüft.

* **grunt-contrib-concat**: Ein Plug-in, das Dateien in einer einzelnen Datei verknüpft.

* **grunt-contrib-uglify**: Ein Plug-in, das JavaScript zur Reduzierung der Größe verkleinert wird.

* **grunt-contrib-watch**: Ein Plug-in, die Aktivität "Datei" überwacht wird.

## <a name="preparing-the-application"></a>Vorbereiten der Anwendung

Zunächst richten Sie eine neue leere Web-Anwendung, und fügen Sie TypeScript-Beispiel-Dateien hinzu. TypeScript-Dateien werden in JavaScript mithilfe der Standardeinstellungen für Visual Studio automatisch kompiliert und werden unsere Rohmaterial zum Verarbeiten von Verwenden von Grunt.

1. Erstellen Sie in Visual Studio ein neues `ASP.NET Web Application`.

2. In der **neues ASP.NET-Projekt** Dialogfeld Wählen Sie die ASP.NET Core **leere** Vorlage, und klicken Sie auf die Schaltfläche "OK".

3. Überprüfen Sie im Projektmappen-Explorer die Struktur des Projekts aus. Die `\src` Ordner enthält leere `wwwroot` und `Dependencies` Knoten.

    ![leere Web-Lösung](using-grunt/_static/grunt-solution-explorer.png)

4. Fügen Sie einen neuen Ordner namens `TypeScript` zu Ihrem Projektverzeichnis.

5. Vor dem Hinzufügen von Dateien, stellen Sie sicher, dass Visual Studio die Option "Kompilieren speichern" für TypeScript-Dateien ausgecheckt. Navigieren Sie zu **Tools** > **Optionen** > **Text-Editor** > **Typescript**  >  **Projekt**:

    ![Optionen, die automatische Kompilierung von TypeScript-Dateien festlegen](using-grunt/_static/typescript-options.png)

6. Mit der rechten Maustaste die `TypeScript` und wählen **hinzufügen > Neues Element** aus dem Kontextmenü. Wählen Sie die **JavaScript-Datei** Element aus, und nennen Sie die Datei *Tastes.ts* (Beachten Sie die \*Dateierweiterung TS). Kopieren Sie die Zeile der folgenden TypeScript-Code in die Datei (Wenn Sie gespeichert haben, ein neues *Tastes.js* Datei wird angezeigt, mit der JavaScript-Quelle).

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. Fügen Sie eine zweite Datei, die **TypeScript** Verzeichnis und nennen Sie sie `Food.ts`. Kopieren Sie den folgenden Code in die Datei an.

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

Als Nächstes konfigurieren Sie NPM, Grunt und Grunt-Aufgaben herunterladen.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste in des Projekts, und wählen **hinzufügen > Neues Element** aus dem Kontextmenü. Wählen Sie die **NPM-Konfigurationsdatei** Element, übernehmen Sie den Standardnamen *"Package.JSON"*, und klicken Sie auf die **hinzufügen** Schaltfläche.

2. In der *"Package.JSON"* -Datei im der `devDependencies` Objekt geschweifte Klammern, geben Sie "grunt". Wählen Sie `grunt` leseformulare in Intellisense aus, und drücken Sie die EINGABETASTE. Visual Studio quote der Grunt-Paketname, und fügen einen Doppelpunkt. Wählen Sie rechts neben dem Doppelpunkt, die neueste stabile Version des Pakets vom oberen Rand der Intellisense-Liste (drücken Sie die `Ctrl-Space` Wenn Intellisense nicht angezeigt wird).

    ![grunt Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > Der NPM verwendet [semantische Versionierung](http://semver.org/) zum Organisieren von Abhängigkeiten. Semantischer versionsverwaltung, auch bekannt als SemVer, identifiziert die Pakete mit den Nummerierungsschema \<wichtigen >.\< kleinere >. \<Patch >. IntelliSense vereinfacht semantische Versionierung, indem Sie nur einige gängige Optionen angezeigt. Das oberste Element in der Intellisense-Liste (0.4.5 im obigen Beispiel), gilt die neueste stabile Version des Pakets. Das Caretzeichen (^) Symbol entspricht, die aktuellste Version, und die Tilde (~) entspricht der aktuellsten Nebenversion. Finden Sie unter den [NPM Semver Version Parser Verweis](https://www.npmjs.com/package/semver) als Leitfaden für die vollständige ausdrucksmöglichkeiten, die SemVer bereitstellt.

3. Fügen Sie weitere Abhängigkeiten laden grunt-Contrib -\* Pakete für *Bereinigen*, *Jshint*, *"concat"*, *uglify*, und *Watch* wie im folgenden Beispiel gezeigt. Die Versionen müssen nicht im Beispiel übereinstimmen.

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

4. Speichern Sie die *"Package.JSON"* Datei.

Die Pakete für jedes Element "devdependencies" werden zusammen mit der alle Dateien heruntergeladen, die jedes Paket erfordert. Sie finden die Paketdateien in der `node_modules` Verzeichnis durch Aktivieren der **alle Dateien anzeigen** Schaltfläche im Projektmappen-Explorer.

![Grunt "node_modules"](using-grunt/_static/node-modules.png)

> [!NOTE]
> Wenn Sie möchten, können Sie Abhängigkeiten im Projektmappen-Explorer manuell wiederherstellen, indem Sie mit der rechten Maustaste auf `Dependencies\NPM` und Auswählen der **-Pakete wiederherstellen** Option des Menüs.

![Wiederherstellen von Paketen](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a>Konfigurieren von Grunt

Grunt erfolgt mithilfe eines Manifests, das mit dem Namen *"gruntfile.js"* , die definiert, lädt und registriert Sie Aufgaben, die manuell ausführen oder so konfiguriert, dass automatisch basierend auf Ereignissen in Visual Studio ausgeführt werden können.

1. Mit der rechten Maustaste in des Projekts, und wählen Sie **hinzufügen > Neues Element**. Wählen Sie die **Grunt-Konfigurationsdatei** aus, übernehmen Sie den Standardnamen *"gruntfile.js"*, und klicken Sie auf die **hinzufügen** Schaltfläche.

   Der erste Code enthält eine Moduldefinition und `grunt.initConfig()` Methode. Die `initConfig()` dient zum Festlegen von Optionen für jedes Paket und der Rest des Moduls werden geladen und die Tasks werden registriert.

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

2. In der `initConfig()` -Methode, Hinzufügen von Optionen für die `clean` Aufgabe wie im Beispiel gezeigt *"gruntfile.js"* unten. Die clean-Aufgabe akzeptiert ein Array von Verzeichniszeichenfolgen. Dieser Task entfernt Dateien, die von Wwwroot/Lib und das gesamte/temp-Verzeichnis entfernt.

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

3. Fügen Sie unterhalb der initConfig()-Methode, die einen Aufruf von `grunt.loadNpmTasks()`. Dadurch wird die Aufgabe von Visual Studio ausführbar.

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

4. Speichern Sie *"gruntfile.js"*. Die Datei sollte etwa wie im folgenden Screenshot aussehen.

    ![ersten gruntfile](using-grunt/_static/gruntfile-js-initial.png)

5. Mit der rechten Maustaste *"gruntfile.js"* , und wählen Sie **Task Runner-Explorer** aus dem Kontextmenü. Der Task Runner-Explorer-Fenster wird geöffnet.

    ![Task Runner-Explorer-Menü](using-grunt/_static/task-runner-explorer-menu.png)

6. Überprüfen Sie, ob `clean` wird unter **Aufgaben** im Task Runner-Explorer.

    ![Task Runner-Explorer-Aufgabenliste](using-grunt/_static/task-runner-explorer-tasks.png)

7. Mit der rechten Maustaste der clean-Aufgabe, und wählen Sie **ausführen** aus dem Kontextmenü. Ein Befehlsfenster zeigt Fortschritt der Aufgabe.

    ![Task Runner-Explorer ausführen clean-Aufgabe](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > Es gibt keine Dateien oder Verzeichnisse noch bereinigt. Wenn Sie möchten, können Sie sie im Projektmappen-Explorer manuell erstellen und die clean-Aufgabe als Test führen.

8. Fügen Sie in der initConfig()-Methode, einen Eintrag für `concat` anhand des folgenden Codes.

    Die `src` Eigenschaftsarray Listet die Dateien in der Reihenfolge kombiniert, dass sie kombiniert werden sollen. Die `dest` Eigenschaft weist den Pfad zu der Datei, die erzeugt wird.

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > Die `all` Eigenschaft im obigen Code ist der Name eines Ziels. Ziele werden in einigen Grunt-Aufgaben verwendet, um Buildumgebungen mit mehreren zu ermöglichen. Sie können Anzeigen der integrierten Ziele mithilfe von Intellisense oder weisen Sie Ihre eigenen.

9. Hinzufügen der `jshint` Aufgabe mit dem folgenden Code.

    Das Jshint Codequalität Dienstprogramm wird für alle JavaScript-Datei finden Sie im Verzeichnis "temp" ausgeführt.

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > Die Option "-W069" wird ein Fehler erzeugt, durch Jshint Wenn JavaScript verwendet die Syntax So weisen eine Eigenschaft anstatt die punktierte Schreibweise, d. h. Klammer `Tastes["Sweet"]` anstelle von `Tastes.Sweet`. Die Option deaktiviert die Warnung, um den Rest des Prozesses, der weiterhin ermöglichen.

10. Hinzufügen der `uglify` Aufgabe mit dem folgenden Code.

    Verkleinert die Aufgabe der *combined.js* -Datei finden Sie im Verzeichnis "temp" und erstellt die Ergebnisdatei im Wwwroot/Lib die standardmäßige Benennungskonvention  *\<Dateiname\>. min.js*.

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

11. Die Aufruf-grunt.loadNpmTasks(), die Grunt-Contrib-clean lädt, enthalten die gleichen Aufruf für Jshint, "concat", und uglify anhand des folgenden Codes.

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

12. Speichern Sie *"gruntfile.js"*. Die Datei sollte etwa wie im folgenden Beispiel aussehen.

    ![Beispiel für eine vollständige grunt](using-grunt/_static/gruntfile-js-complete.png)

13. Beachten Sie, die die Task Runner-Explorer-Tasks-Liste enthält `clean`, `concat`, `jshint` und `uglify` Aufgaben. Jede Aufgabe in der Reihenfolge ausgeführt, und beobachten Sie die Ergebnisse im Projektmappen-Explorer. Jede Aufgabe sollte ohne Fehler ausgeführt.

    ![Task Runner-Explorer, die jeder Task ausführen](using-grunt/_static/task-runner-explorer-run-each-task.png)

    Der Task "concat" erstellt ein neues *combined.js* Datei, und setzt es in das temporäre Verzeichnis. Der Task Jshint einfach ausgeführt wird und keine Ausgabe erzeugt. Der Task Uglify erstellt ein neues *combined.min.js* Datei, und setzt es in Wwwroot/Lib. Nach Abschluss des Vorgangs sollte die Lösung etwa wie im folgenden Screenshot aussehen:

    ![Projektmappen-Explorer nachdem alle Aufgaben](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > Weitere Informationen zu den Optionen für jedes Paket finden Sie unter [ https://www.npmjs.com/ ](https://www.npmjs.com/) und suchen Sie den Paketnamen in das Suchfeld auf der Hauptseite. Beispielsweise können Sie das Paket Grunt-Contrib-clean, um einen Link zur Dokumentation zu erhalten, der allen Parametern erklärt nachschlagen.

### <a name="all-together-now"></a>Und jetzt alle zusammen

Verwenden Sie die Grunt `registerTask()` Methode, um eine Reihe von Aufgaben in einer bestimmten Reihenfolge auszuführen. Z. B. zum Ausführen des Beispiels, die oben genannten Schritte, in der Reihenfolge bereinigen -> "concat" -> Jshint -> uglify, fügen Sie den folgenden Code hinzu, an das Modul. Der Code sollte auf der gleichen Ebene wie die Aufrufe loadNpmTasks() außerhalb InitConfig hinzugefügt werden.

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

Die neue Aufgabe wird im Task Runner-Explorer unter Aliastasks angezeigt. Sie können mit der rechten Maustaste, und genau wie andere Aufgaben ausführen. Die `all` Task ausgeführt `clean`, `concat`, `jshint` und `uglify`, in der Reihenfolge.

![Alias-Grunt-Aufgaben](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a>Erkennen von Änderungen

Ein `watch` -Task auf Dateien und Verzeichnisse. Die Apple Watch löst Aufgaben automatisch, wenn Änderungen erkannt. InitConfig, damit die Änderungen sehen Sie sich den folgenden Code hinzugefügt \*JS-Dateien in das TypeScript-Verzeichnis. Wenn eine JavaScript-Datei geändert wird, `watch` führt die `all` Aufgabe.

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

Fügen Sie einen Aufruf von `loadNpmTasks()` zum Anzeigen der `watch` Aufgabe im Task Runner-Explorer.

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

Mit der rechten Maustaste in der Watch-Aufgabe in Task Runner-Explorer, und führen Sie aus dem Kontextmenü auswählen. Das Befehlsfenster, das zeigt die Watch-Aufgabe ausgeführt wird eine "gewartet..." angezeigt. Nachricht. Öffnen Sie die TypeScript-Dateien, ein Leerzeichen hinzu, und speichern Sie die Datei. Wird, lösen die Aufgabe überwachen und lösen die anderen Tasks in der Reihenfolge ausgeführt. Der folgende Screenshot zeigt eine beispielausführung.

![Ausführen von Aufgaben-Ausgabe](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a>Binden an Visual Studio-Ereignisse

Es sei denn, Sie möchten Ihre Aufgaben manuell zu starten, jedes Mal, wenn Sie in Visual Studio arbeiten, können Sie Aufgaben zum Binden **vor dem Erstellen**, **nach dem Erstellen**, **Bereinigen**, und  **Projekt öffnen** Ereignisse.

Binden wir `watch` , damit es ausgeführt wird, jedes Mal, wenn Visual Studio wird geöffnet. Klicken Sie im Task Runner-Explorer mit der rechten Maustaste in der Aufgabe überwachen, und wählen **Bindungen > Project Open** aus dem Kontextmenü.

![Binden Sie eine Aufgabe an das öffnende Projekt](using-grunt/_static/bindings-project-open.png)

Entladen Sie und Laden Sie das Projekt. Wenn das Projekt erneut geladen wird, wird die Aufgabe überwachen automatisch gestartet.

## <a name="summary"></a>Zusammenfassung

Grunt ist eine leistungsstarke-aufgabenausführung erstellt, die zum Automatisieren von Aufgaben für die meisten Client-Build verwendet werden kann. Grunt nutzt NPM, um die Pakete und Funktionen, die Integration in Visual Studio-Tools bereitzustellen. Task Runner-Explorer von Visual Studio erkennt Änderungen an Konfigurationsdateien und bietet eine benutzerfreundliche Oberfläche zum Ausführen von Aufgaben, ausgeführte Aufgaben anzeigen und Aufgaben an Visual Studio-Ereignisse binden.

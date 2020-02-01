---
title: Verwenden der Libman-CLI mit ASP.net Core
author: scottaddie
description: Erfahren Sie, wie Sie die Libman CLI in einem ASP.net Core-Projekt verwenden.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: client-side/libman/libman-cli
ms.openlocfilehash: 02d88d09805bd23a86ef924766373245fec7ff52
ms.sourcegitcommit: 0b0e485a8a6dfcc65a7a58b365622b3839f4d624
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/01/2020
ms.locfileid: "76928363"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a>Verwenden der Libman-CLI mit ASP.net Core

Von [Scott Addie](https://twitter.com/Scott_Addie)

Die [Libman](xref:client-side/libman/index) CLI ist ein plattformübergreifendes Tool, das überall unterstützt wird, wo .net Core unterstützt wird.

## <a name="prerequisites"></a>Erforderliche Komponenten

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a>Installation

So installieren Sie die Libman CLI:

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

Ein [globales .net Core-Tool](/dotnet/core/tools/global-tools#install-a-global-tool) wird aus dem nuget-Paket [Microsoft. Web. LibraryManager. CLI](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) installiert.

So installieren Sie die Libman-CLI über eine bestimmte nuget-Paketquelle:

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

Im vorherigen Beispiel wird ein globales .net Core-Tool aus der *C:\Temp\Microsoft.Web.LibraryManager.CLI.1.0.94-g606058a278.nupkg* -Datei des lokalen Windows-Computers installiert.

## <a name="usage"></a>Verwendungs-

Nach der erfolgreichen Installation der CLI kann der folgende Befehl verwendet werden:

```console
libman
```

Anzeigen der installierten CLI-Version:

```console
libman --version
```

So zeigen Sie die verfügbaren CLI-Befehle an:

```console
libman --help
```

Der vorherige Befehl zeigt eine Ausgabe ähnlich der folgenden an:

```console
 1.0.163+g45474d37ed

Usage: libman [options] [command]

Options:
  --help|-h  Show help information
  --version  Show version information

Commands:
  cache      List or clean libman cache contents
  clean      Deletes all library files defined in libman.json from the project
  init       Create a new libman.json
  install    Add a library definition to the libman.json file, and download the 
             library to the specified location
  restore    Downloads all files from provider and saves them to specified 
             destination
  uninstall  Deletes all files for the specified library from their specified 
             destination, then removes the specified library definition from 
             libman.json
  update     Updates the specified library

Use "libman [command] --help" for more information about a command.
```

In den folgenden Abschnitten werden die verfügbaren CLI-Befehle erläutert.

## <a name="initialize-libman-in-the-project"></a>Initialisieren von Libman im Projekt

Der `libman init`-Befehl erstellt eine *Libman. JSON* -Datei, falls noch keine vorhanden ist. Die Datei wird mit dem Inhalt der Standardelement Vorlage erstellt.

### <a name="synopsis"></a>Übersicht

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a>Options

Für den Befehl `libman init` sind die folgenden Optionen verfügbar:

* `-d|--default-destination <PATH>`

  Ein Pfad relativ zum aktuellen Ordner. Bibliotheksdateien werden an diesem Speicherort installiert, wenn keine `destination` Eigenschaft für eine Bibliothek in " *Libman. JSON*" definiert ist. Der `<PATH>`-Wert wird in die `defaultDestination`-Eigenschaft von *Libman. JSON*geschrieben.

* `-p|--default-provider <PROVIDER>`

  Der zu verwendende Anbieter, wenn für eine bestimmte Bibliothek kein Anbieter definiert ist. Der `<PROVIDER>`-Wert wird in die `defaultProvider`-Eigenschaft von *Libman. JSON*geschrieben. Ersetzen Sie `<PROVIDER>` durch einen der folgenden Werte:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Beispiele

So erstellen Sie eine *Libman. JSON* -Datei in einem ASP.net Core Projekt:

* Navigieren Sie zum Projektstamm.
* Führen Sie den folgenden Befehl aus:

  ```console
  libman init
  ```

* Geben Sie den Namen des Standard Anbieters ein, oder drücken Sie `Enter`, um den standardmäßigen cdnjs-Anbieter zu verwenden. Gültige Werte sind:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![Libman init-Befehl-Standardanbieter](_static/libman-init-provider.png)

Eine *Libman. JSON* -Datei wird dem Projektstamm mit folgendem Inhalt hinzugefügt:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a>Bibliotheksdateien hinzufügen

Mit dem Befehl `libman install` werden Bibliotheksdateien heruntergeladen und in das Projekt installiert. Wenn noch keiner vorhanden ist, wird eine *Libman. JSON* -Datei hinzugefügt. Die Datei " *Libman. JSON* " wird so geändert, dass Konfigurationsdetails für die Bibliotheksdateien gespeichert werden.

### <a name="synopsis"></a>Übersicht

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a>Argumente

`LIBRARY`

Der Name der zu installierenden Bibliothek. Dieser Name kann die Notation der Versionsnummer (z. b. `@1.2.0`) enthalten.

### <a name="options"></a>Options

Für den Befehl `libman install` sind die folgenden Optionen verfügbar:

* `-d|--destination <PATH>`

  Der Speicherort für die Installation der Bibliothek. Wenn nicht angegeben, wird der Standard Speicherort verwendet. Wenn in *Libman. JSON*keine `defaultDestination` Eigenschaft angegeben ist, ist diese Option erforderlich.

* `--files <FILE>`

  Geben Sie den Namen der Datei an, die aus der Bibliothek installiert werden soll. Wenn nicht angegeben, werden alle Dateien aus der Bibliothek installiert. Geben Sie eine `--files` Option pro zu installierenden Datei an. Relative Pfade werden ebenfalls unterstützt. Beispiel: `--files dist/browser/signalr.js`.

* `-p|--provider <PROVIDER>`

  Der Name des Anbieters, der für die Bibliothek Beschaffung verwendet werden soll. Ersetzen Sie `<PROVIDER>` durch einen der folgenden Werte:
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  Wenn nichts angegeben ist, wird die `defaultProvider`-Eigenschaft in *Libman. JSON* verwendet. Wenn in *Libman. JSON*keine `defaultProvider` Eigenschaft angegeben ist, ist diese Option erforderlich.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Beispiele

Sehen Sie sich die folgende *Libman. JSON* -Datei an:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

So installieren Sie die jQuery *. min. js* -Datei der jQuery-Version 3.2.1 im Ordner *wwwroot/Scripts/jQuery* mithilfe des cdnjs-Anbieters:

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

Die Datei " *Libman. JSON* " ähnelt der folgenden:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    }
  ]
}
```

So installieren Sie die Dateien " *Calendar. js* " und " *Calendar. CSS* " von *C:\\Temp\\condesocalendar\\* mithilfe des Dateisystem Anbieters:

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

Die folgende Eingabeaufforderung wird aus zwei Gründen angezeigt:

* Die *Libman. JSON* -Datei enthält keine `defaultDestination`-Eigenschaft.
* Der `libman install`-Befehl enthält die `-d|--destination`-Option nicht.

![Libman-Installations Befehl-Ziel](_static/libman-install-destination.png)

Nachdem Sie das Standardziel akzeptiert haben, ähnelt die Datei " *Libman. JSON* " folgendem:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    },
    {
      "library": "C:\\temp\\contosoCalendar\\",
      "provider": "filesystem",
      "destination": "wwwroot/lib/contosoCalendar",
      "files": [
        "calendar.js",
        "calendar.css"
      ]
    }
  ]
}
```

## <a name="restore-library-files"></a>Bibliotheksdateien wiederherstellen

Mit dem Befehl `libman restore` werden Bibliotheksdateien installiert, die in *Libman. JSON*definiert sind. Dabei gelten folgende Regeln:

* Wenn keine *Libman. JSON* -Datei im Projektstamm vorhanden ist, wird ein Fehler zurückgegeben.
* Wenn eine Bibliothek einen Anbieter angibt, wird die `defaultProvider`-Eigenschaft in *Libman. JSON* ignoriert.
* Wenn eine Bibliothek ein Ziel angibt, wird die `defaultDestination`-Eigenschaft in *Libman. JSON* ignoriert.

### <a name="synopsis"></a>Übersicht

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a>Options

Für den Befehl `libman restore` sind die folgenden Optionen verfügbar:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Beispiele

So stellen Sie die in " *Libman. JSON*" definierten Bibliotheksdateien wieder her:

```console
libman restore
```

## <a name="delete-library-files"></a>Löschen von Bibliotheksdateien

Der `libman clean` Befehl löscht Bibliotheksdateien, die zuvor über Libman wieder hergestellt wurden. Ordner, die nach dem Löschen des Vorgangs leer werden. Die zugeordneten Konfigurationen der Bibliotheksdateien in der `libraries`-Eigenschaft von " *Libman. JSON* " werden nicht entfernt.

### <a name="synopsis"></a>Übersicht

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a>Options

Für den Befehl `libman clean` sind die folgenden Optionen verfügbar:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Beispiele

So löschen Sie Bibliotheksdateien, die über Libman installiert wurden:

```console
libman clean
```

## <a name="uninstall-library-files"></a>Bibliotheksdateien deinstallieren

Der `libman uninstall` Befehl:

* Löscht alle Dateien, die der angegebenen Bibliothek zugeordnet sind, aus dem Ziel in *Libman. JSON*.
* Entfernt die zugehörige Bibliotheks Konfiguration aus *Libman. JSON*.

Fehler tritt auf, wenn Folgendes zutrifft:

* Im Stammverzeichnis des Projekts ist keine *Libman. JSON* -Datei vorhanden.
* Die angegebene Bibliothek ist nicht vorhanden.

Wenn mehr als eine Bibliothek mit dem gleichen Namen installiert ist, werden Sie aufgefordert, einen auszuwählen.

### <a name="synopsis"></a>Übersicht

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a>Argumente

`LIBRARY`

Der Name der Bibliothek, die deinstalliert werden soll. Dieser Name kann die Notation der Versionsnummer (z. b. `@1.2.0`) enthalten.

### <a name="options"></a>Options

Für den Befehl `libman uninstall` sind die folgenden Optionen verfügbar:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Beispiele

Sehen Sie sich die folgende *Libman. JSON* -Datei an:

[!code-json[](samples/LibManSample/libman.json)]

* Wenn Sie jQuery deinstallieren möchten, wird einer der folgenden Befehle erfolgreich ausgeführt:

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* So deinstallieren Sie die über den `filesystem` Anbieter installierten lodash-Dateien:

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a>Version der Update Bibliothek

Der `libman update`-Befehl aktualisiert eine Bibliothek, die über Libman installiert wurde, auf die angegebene Version.

Fehler tritt auf, wenn Folgendes zutrifft:

* Im Stammverzeichnis des Projekts ist keine *Libman. JSON* -Datei vorhanden.
* Die angegebene Bibliothek ist nicht vorhanden.

Wenn mehr als eine Bibliothek mit dem gleichen Namen installiert ist, werden Sie aufgefordert, einen auszuwählen.

### <a name="synopsis"></a>Übersicht

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a>Argumente

`LIBRARY`

Der Name der zu aktualisierenden Bibliothek.

### <a name="options"></a>Options

Für den Befehl `libman update` sind die folgenden Optionen verfügbar:

* `-pre`

  Rufen Sie die neueste Vorabversion der Bibliothek ab.

* `--to <VERSION>`

  Beziehen Sie eine bestimmte Version der Bibliothek.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Beispiele

* So aktualisieren Sie jQuery auf die neueste Version:

  ```console
  libman update jquery
  ```

* So aktualisieren Sie jQuery auf Version 3.3.1:

  ```console
  libman update jquery --to 3.3.1
  ```

* So aktualisieren Sie jQuery auf die neueste Vorabversion:

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a>Bibliotheks Cache verwalten

Der `libman cache`-Befehl verwaltet den Libman-Bibliotheks Cache. Der `filesystem`-Anbieter verwendet den Bibliotheks Cache nicht.

### <a name="synopsis"></a>Übersicht

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a>Argumente

`PROVIDER`

Wird nur mit dem `clean`-Befehl verwendet. Gibt den zu bereinigen Anbieter Cache an. Gültige Werte sind:

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a>Options

Für den Befehl `libman cache` sind die folgenden Optionen verfügbar:

* `--files`

  Listet die Namen der Dateien auf, die zwischengespeichert werden.

* `--libraries`

  Listet die Namen der Bibliotheken auf, die zwischengespeichert werden.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Beispiele

* Verwenden Sie einen der folgenden Befehle, um die Namen der zwischengespeicherten Bibliotheken pro Anbieter anzuzeigen:

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  Dadurch werden Informationen angezeigt, die mit denen der folgenden Ausgabe vergleichbar sind:

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      font-awesome
      jquery
      knockout
      lodash.js
      react
  ```

* So zeigen Sie die Namen der zwischengespeicherten Bibliotheksdateien pro Anbieter an:

  ```console
  libman cache list --files
  ```

  Dadurch werden Informationen angezeigt, die mit denen der folgenden Ausgabe vergleichbar sind:

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout:
          <list omitted for brevity>
      react:
          <list omitted for brevity>
      vue:
          <list omitted for brevity>
  cdnjs:
      font-awesome
          metadata.json
      jquery
          metadata.json
          3.2.1\core.js
          3.2.1\jquery.js
          3.2.1\jquery.min.js
          3.2.1\jquery.min.map
          3.2.1\jquery.slim.js
          3.2.1\jquery.slim.min.js
          3.2.1\jquery.slim.min.map
          3.3.1\core.js
          3.3.1\jquery.js
          3.3.1\jquery.min.js
          3.3.1\jquery.min.map
          3.3.1\jquery.slim.js
          3.3.1\jquery.slim.min.js
          3.3.1\jquery.slim.min.map
      knockout
          metadata.json
          3.4.2\knockout-debug.js
          3.4.2\knockout-min.js
      lodash.js
          metadata.json
          4.17.10\lodash.js
          4.17.10\lodash.min.js
      react
          metadata.json
  ```

  Beachten Sie, dass die vorherige Ausgabe zeigt, dass die jQuery-Versionen 3.2.1 und 3.3.1 unter dem cdnjs-Anbieter zwischengespeichert werden.

* So leeren Sie den Bibliotheks Cache für den cdnjs-Anbieter:

  ```console
  libman cache clean cdnjs
  ```

  Nach dem leeren des cdnjs-Anbieter Caches zeigt der `libman cache list` Befehl Folgendes an:

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      (empty)
  ```

* So leeren Sie den Cache für alle unterstützten Anbieter:

  ```console
  libman cache clean
  ```

  Nach dem leeren aller Anbieter Caches zeigt der `libman cache list` Befehl Folgendes an:

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a>Weitere Ressourcen

* [Globales Tool installieren](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [GitHub-Repository für LibMan](https://github.com/aspnet/LibraryManager)

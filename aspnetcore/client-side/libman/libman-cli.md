---
title: Verwenden Sie die LibMan-Befehlszeilenschnittstelle (CLI) mit ASP.NET Core
author: scottaddie
description: Erfahren Sie, wie die LibMan-Befehlszeilenschnittstelle (CLI) in einem ASP.NET Core-Projekt verwenden.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/30/2018
uid: client-side/libman/libman-cli
ms.openlocfilehash: 5667f79648a60b8fd9496f8041ef08891ab766af
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894757"
---
# <a name="use-the-libman-command-line-interface-cli-with-aspnet-core"></a>Verwenden Sie die LibMan-Befehlszeilenschnittstelle (CLI) mit ASP.NET Core

Von [Scott Addie](https://twitter.com/Scott_Addie)

Die [LibMan](xref:client-side/libman/index) -Befehlszeilenschnittstelle ist ein plattformübergreifendes Tool, die überall unterstützt .NET Core wird unterstützt.

## <a name="prerequisites"></a>Vorraussetzungen

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a>Installation

So installieren Sie die LibMan CLI:

```console
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

Ein [Global .NET Core-Tool](/dotnet/core/tools/global-tools#install-a-global-tool) installiert ist, aus der [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet-Paket.

So installieren Sie die LibMan CLI über eine bestimmte NuGet-Paketquelle

```console
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

Im vorherigen Beispiel ist eine .NET Core-Global-Tool aus dem lokalen Windows-Computer installiert *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* Datei.

## <a name="usage"></a>Verwendung

Der folgende Befehl kann verwendet werden, nach der erfolgreichen Installation der CLI:

```console
libman
```

So zeigen Sie die installierten CLI-Version an:

```console
libman --version
```

So zeigen Sie die verfügbaren CLI-Befehlen an:

```console
libman --help
```

Der vorhergehende Befehl zeigt eine Ausgabe ähnlich der folgenden:

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

Den folgenden Abschnitten werden die verfügbaren CLI-Befehlen.

## <a name="initialize-libman-in-the-project"></a>LibMan in das Projekt initialisieren

Die `libman init` Befehl erstellt eine *libman.json* Datei, wenn es nicht vorhanden ist. Die Datei wird mit den standardmäßigen Element Vorlage-Inhalt erstellt.

### <a name="synopsis"></a>Übersicht

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a>Optionen

Die folgenden Optionen stehen für die `libman init` Befehl:

* `-d|--default-destination <PATH>`

  Ein Pfad relativ zum aktuellen Ordner. Bibliotheksdateien werden an diesem Speicherort installiert, wenn keine `destination` Eigenschaft wird definiert, für eine Bibliothek in *libman.json*. Die `<PATH>` Wert wird geschrieben, um die `defaultDestination` Eigenschaft *libman.json*.

* `-p|--default-provider <PROVIDER>`

  Der Anbieter verwenden, wenn kein Anbieter für eine bestimmte Bibliothek definiert ist. Die `<PROVIDER>` Wert wird geschrieben, um die `defaultProvider` Eigenschaft *libman.json*. Ersetzen Sie dies `<PROVIDER>` mit einem der folgenden Werte:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Beispiele

Zum Erstellen einer *libman.json* -Datei in einem ASP.NET Core-Projekt:

* Navigieren Sie zu das Stammverzeichnis des Projekts.
* Führen Sie den folgenden Befehl aus:

  ```console
  libman init
  ```

* Geben Sie den Namen des Standardanbieters oder drücken Sie `Enter` den CDNJS-Standardanbieter verwendet. Gültige Werte sind:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![Libman Init-Befehl – Standard-Anbieter](_static/libman-init-provider.png)

Ein *libman.json* Datei wird hinzugefügt, um das Stammverzeichnis des Projekts mit folgendem Inhalt:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a>Fügen Sie Bibliotheksdateien

Die `libman install` Befehl heruntergeladen und installiert Sie Bibliotheksdateien in das Projekt. Ein *libman.json* Datei wird hinzugefügt, wenn es nicht vorhanden ist. Die *libman.json* Datei wird geändert, um die Konfigurationsdetails für die Bibliotheksdateien speichern.

### <a name="synopsis"></a>Übersicht

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a>Argumente

`LIBRARY`

Der Name der Bibliothek zu installieren. Dieser Name kann die Anzahl-versionsnotation enthalten (z. B. `@1.2.0`).

### <a name="options"></a>Optionen

Die folgenden Optionen stehen für die `libman install` Befehl:

* `-d|--destination <PATH>`

  Der Speicherort, der die Bibliothek zu installieren. Wenn nicht angegeben, wird der Standardspeicherort verwendet. Wenn kein `defaultDestination` -Eigenschaft angegeben wird, im *libman.json*, diese Option ist erforderlich.

* `--files <FILE>`

  Geben Sie den Namen der Datei, die aus der Bibliothek zu installieren. Wenn nicht angegeben, werden alle Dateien aus der Bibliothek installiert. Geben Sie einen `--files` Option pro Datei installiert werden. Relative Pfade werden ebenfalls unterstützt. Beispiel: `--files dist/browser/signalr.js`.

* `-p|--provider <PROVIDER>`

  Der Name des Anbieters, um für die Übernahme der Bibliothek verwenden. Ersetzen Sie dies `<PROVIDER>` mit einem der folgenden Werte:
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  Wenn nicht angegeben, die `defaultProvider` -Eigenschaft in *libman.json* verwendet wird. Wenn kein `defaultProvider` -Eigenschaft angegeben wird, im *libman.json*, diese Option ist erforderlich.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Beispiele

Beachten Sie Folgendes *libman.json* Datei:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

So installieren Sie die jQuery-Version 3.2.1 *"jQuery.Min.js"* -Datei in die *Wwwroot/Skripts/Jquery* Ordner mit dem Anbieter CDNJS:

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

Die *libman.json* Datei ähnelt der folgenden:

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

So installieren Sie die *calendar.js* und *calendar.css* Dateien von *C:\\Temp\\ContosoCalendar\\*  mit dem Dateisystem Anbieter:

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

Die folgende Aufforderung angezeigt wird, aus zwei Gründen:

* Die *libman.json* Datei keine `defaultDestination` Eigenschaft.
* Die `libman install` Befehl enthält nicht die `-d|--destination` Option.

![Libman Installationsbefehl - Ziel](_static/libman-install-destination.png)

Nach dem Akzeptieren des Standardziel, dem *libman.json* Datei ähnelt der folgenden:

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

## <a name="restore-library-files"></a>Wiederherstellen von Bibliotheksdateien

Die `libman restore` Befehl installiert die Bibliotheksdateien, die in definierten *libman.json*. Dabei gelten folgende Regeln:

* Wenn kein *libman.json* Datei in das Stammverzeichnis des Projekts vorhanden ist, wird ein Fehler zurückgegeben.
* Wenn eine Bibliothek gibt an, einen Anbieter, der `defaultProvider` -Eigenschaft in *libman.json* wird ignoriert.
* Wenn eine Bibliothek ein Ziel, gibt die `defaultDestination` -Eigenschaft in *libman.json* wird ignoriert.

### <a name="synopsis"></a>Übersicht

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a>Optionen

Die folgenden Optionen stehen für die `libman restore` Befehl:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Beispiele

Zum Wiederherstellen der Library-Dateien, die in definierten *libman.json*:

```console
libman restore
```

## <a name="delete-library-files"></a>Löschen Sie Bibliotheksdateien

Die `libman clean` Befehl löscht die Bibliotheksdateien, die zuvor über LibMan wiederhergestellt. Ordner, die nach diesem Vorgang leer sind, werden gelöscht. Der Bibliotheksdateien verknüpft ist, Konfigurationen in der `libraries` Eigenschaft *libman.json* werden nicht entfernt.

### <a name="synopsis"></a>Übersicht

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a>Optionen

Die folgenden Optionen stehen für die `libman clean` Befehl:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Beispiele

So löschen Sie Bibliotheksdateien, die über LibMan installiert:

```console
libman clean
```

## <a name="uninstall-library-files"></a>Deinstallieren Sie Bibliotheksdateien

Die `libman uninstall` Befehl:

* Löscht alle Dateien im Zusammenhang mit der angegebenen Bibliothek aus dem Ziel in *libman.json*.
* Entfernt den zugeordneten Netzwerkbibliotheks-Konfiguration von *libman.json*.

Ein Fehler tritt auf, wenn:

* Keine *libman.json* Datei vorhanden ist, in das Stammverzeichnis des Projekts.
* Die angegebene Bibliothek ist nicht vorhanden.

Wenn mehr als eine Bibliothek mit demselben Namen installiert ist, werden Sie aufgefordert, eine auszuwählen.

### <a name="synopsis"></a>Übersicht

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a>Argumente

`LIBRARY`

Der Name der Bibliothek zu deinstallieren. Dieser Name kann die Anzahl-versionsnotation enthalten (z. B. `@1.2.0`).

### <a name="options"></a>Optionen

Die folgenden Optionen stehen für die `libman uninstall` Befehl:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Beispiele

Beachten Sie Folgendes *libman.json* Datei:

[!code-json[](samples/LibManSample/libman.json)]

* Erfolgreich ausgeführt um jQuery zu deinstallieren, einen der folgenden Befehle aus:

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* So deinstallieren Sie die Lodash-Dateien installiert, über die `filesystem` Anbieter:

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a>Update-Bibliotheksversion

Die `libman update` Befehl wird eine Bibliothek, die über LibMan installiert wird, der angegebenen Version aktualisiert.

Ein Fehler tritt auf, wenn:

* Keine *libman.json* Datei vorhanden ist, in das Stammverzeichnis des Projekts.
* Die angegebene Bibliothek ist nicht vorhanden.

Wenn mehr als eine Bibliothek mit demselben Namen installiert ist, werden Sie aufgefordert, eine auszuwählen.

### <a name="synopsis"></a>Übersicht

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a>Argumente

`LIBRARY`

Der Name der Bibliothek aktualisieren.

### <a name="options"></a>Optionen

Die folgenden Optionen stehen für die `libman update` Befehl:

* `-pre`

  Rufen Sie die aktuelle Vorabversion der Bibliothek.

* `--to <VERSION>`

  Rufen Sie eine bestimmte Version der Bibliothek.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Beispiele

* JQuery auf die neueste Version zu aktualisieren:

  ```console
  libman update jquery
  ```

* So aktualisieren Sie jQuery Version 3.3.1:

  ```console
  libman update jquery --to 3.3.1
  ```

* JQuery auf die aktuelle Vorabversion zu aktualisieren:

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a>Verwalten von Bibliothek-cache

Die `libman cache` Befehl den LibMan-Bibliothek-Cache verwaltet. Die `filesystem` -Anbieter nicht den Cache für die Bibliothek verwenden.

### <a name="synopsis"></a>Übersicht

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a>Argumente

`PROVIDER`

Nur der Verwendung der `clean` Befehl. Gibt an, die der Cache bereinigt. Gültige Werte sind:

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a>Optionen

Die folgenden Optionen stehen für die `libman cache` Befehl:

* `--files`

  Listet die Namen der Dateien, die zwischengespeichert werden.

* `--libraries`

  Listet die Namen von Bibliotheken, die zwischengespeichert werden.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Beispiele

* Verwenden Sie zum Anzeigen der Namen von zwischengespeicherten Bibliotheken pro Anbieter, eine der folgenden Befehle aus:

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

  Beachten Sie, dass der obigen Ausgabe zeigt, dass jQuery, Version 3.2.1 und 3.3.1 unter dem Anbieter CDNJS zwischengespeichert werden.

* Um den Cache für die Bibliothek für den Anbieter CDNJS zu leeren:

  ```console
  libman cache clean cdnjs
  ```

  Nach dem leeren des Caches CDNJS-Anbieter, der `libman cache list` Befehl werden folgende Informationen angezeigt:

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

* Zum Leeren des Caches für alle unterstützten Anbieter:

  ```console
  libman cache clean
  ```

  Nach dem leeren des Caches für alle Anbieter, der `libman cache list` Befehl werden folgende Informationen angezeigt:

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Installieren Sie ein globales Tool](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [GitHub-Repository für LibMan](https://github.com/aspnet/LibraryManager)

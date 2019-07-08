---
title: Befehl „dotnet aspnet-codegenerator“
author: rick-anderson
ms.author: riande
description: Der Befehl „dotnet aspnet-codegenerator“ erstellt das Gerüst für ASP.NET-Projekte.
ms.date: 07/04/2019
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 55b592d9d203970777c84438e210519957abb35d
ms.sourcegitcommit: f6e6730872a7d6f039f97d1df762f0d0bd5e34cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561683"
---
# <a name="dotnet-aspnet-codegenerator"></a>dotnet aspnet-codegenerator

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

`dotnet aspnet-codegenerator`: Führt die ASP.NET Core-Gerüstbauengine aus. `dotnet aspnet-codegenerator` wird nur für den Gerüstbau von der Befehlszeile aus benötigt, nicht zum Gerüstbau mit Visual Studio.

Dieser Artikel gilt für [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.2) höher.

## <a name="installing-aspnet-codegenerator"></a>Installieren von „aspnet-codegenerator“

`aspnet-codegenerator` ist ein [globales Tool](/dotnet/core/tools/global-tools), das installiert werden muss. Über den folgenden Befehl wird die neueste stabile Version des `aspnet-codegenerator`-Tools installiert:

```console
dotnet tool install -g aspnet-codegenerator
```

Mit dem folgenden Befehl wird `aspnet-codegenerator` auf die neueste stabile Version aktualisiert, die in den installierten .NET Core SDKs verfügbar ist:

```console
dotnet tool update -g aspnet-codegenerator
```

## <a name="synopsis"></a>Übersicht

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a>BESCHREIBUNG

Der globale Befehl `dotnet aspnet-codegenerator ` führt den ASP.NET Code-Codegenerator und die Gerüstbauengine aus.

## <a name="arguments"></a>Argumente

`generator`

Der auszuführende Codegenerator. Folgende Generatoren sind verfügbar:

| Generator | Vorgang |
| ----------------- | ------------ | 
| area      | [Gerüstbau für einen Bereich](/aspnet/core/mvc/controllers/areas) |
  Controller| [Gerüstbau für einen Controller](/aspnet/core/tutorials/first-mvc-app/adding-model) |
  identity  | [Gerüstbau für eine Identität](/aspnet/core/security/authentication/scaffold-identity) |
  razorpage | [Gerüstbau für Razor Pages](/aspnet/core/tutorials/razor-pages/model) |
  view      | [Gerüstbau für eine Ansicht](/aspnet/core/mvc/views/overview) |

## <a name="options"></a>Optionen

`-n|--nuget-package-dir`

Gibt das NuGet-Paketverzeichnis an.

`-c|--configuration {Debug|Release}`

Legt die Buildkonfiguration fest. Der Standardwert ist `Debug`sein.

`-tfm|--target-framework`

Das zu verwendende [Zielframework](/dotnet/standard/frameworks). Beispielsweise `net46`.

`-b|--build-base-path`

Der Basispfad für den Build.

`-h|--help`

Druckt eine kurze Hilfe für den Befehl.

`--no-build`

Erstellt das Projekt nicht vor der Ausführung. Zudem wird das Flag `--no-restore` implizit festgelegt.

`-p|--project <PATH>`

Gibt den Pfad der auszuführenden Projektdatei an (Ordnername oder vollständiger Pfad). Wenn nicht angegeben, wird standardmäßig das aktuelle Verzeichnis gewählt.

## <a name="generator-options"></a>Generatoroptionen

In den folgenden Abschnitte werden die verfügbaren Optionen für die unterstützten Generatoren vorgestellt:

* Bereich
* Controller
* Identität  
* Razor Pages
* Ansicht

<a name="area"></a>

### <a name="area-options"></a>Bereichsoptionen

Dieses Tool ist für ASP.NET Core-Webprojekte mit Controllern und Ansichten vorgesehen. Es ist nicht für Razor Pages-Apps gedacht.

Verwendung: `dotnet aspnet-codegenerator area AreaNameToGenerate`

Der oben gezeigte Befehl generiert die folgenden Ordner:

* *Bereiche*
  * *AreaNameToGenerate*
    * *Controller*
    * *Data*
    * *Models*
    * *Ansichten*

<a name="ctl"></a>

### <a name="controller-options"></a>Controlleroptionen

Die folgende Tabelle zeigt die Optionen für `aspnet-codegenerator` `controller` und `razorpage`:

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

Die folgende Tabelle zeigt die Optionen für `aspnet-codegenerator controller`:

| Option               | BESCHREIBUNG|
| ----------------- | ------------ |
| --controllerName oder -name | Der Name des Controllers. |
| --useAsyncActions oder -async | Generiert asynchrone Controlleraktionen. |
| --noViews oder -nv | Generiert **keine** Ansichten. |
| --restWithNoViews oder -api  | Generiert einen Controller mit einer API im REST-Stil. `noViews` wird vorausgesetzt, und ansichtsbezogene Optionen werden ignoriert. |
| --readWriteActions oder -actions | Generiert einen Controller mit Lese-/Schreibaktionen ohne Modell. |

Verwenden Sie den Schalter `-h`, um Hilfe zum `aspnet-codegenerator controller`-Befehl zu erhalten:

```console
dotnet aspnet-codegenerator controller -h
```

Unter [Erstellen des Gerüsts für das Filmmodell](/aspnet/core/tutorials/razor-pages/model) finden Sie ein Beispiel für `dotnet aspnet-codegenerator controller`.

### <a name="razorpage"></a>Razor Pages

<a name="rp"></a>

Für Razor Pages können jeweils einzelne Gerüste erstellt werden, indem der Name der neuen Seite und der zu verwendenden Vorlage angegeben wird. Folgende Vorlagen werden unterstützt:

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

Beispielsweise verwendet der folgende Befehl die Bearbeitungsvorlage, um *MyEdit.cshtml* und *MyEdit.cshtml.cs* zu generieren:

```console
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

Typischerweise werden die Vorlage und der Name der generierten Datei nicht angegeben, und es werden die folgenden Vorlagen erstellt:

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

Die folgende Tabelle zeigt die Optionen für `aspnet-codegenerator` `razorpage` und `controller`:

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

Die folgende Tabelle zeigt die Optionen für `aspnet-codegenerator razorpage`:

| Option               | BESCHREIBUNG|
| ----------------- | ------------ |
|   --namespaceName oder -namespace | Der Name des Namespace, der für das generierte PageModel verwendet wird. |
| --partialView oder -partial | Generiert eine Teilansicht. Die Layoutoptionen -l und -udl werden ignoriert, wenn diese Option angegeben wird. |
| --noPageModel oder -npm | Schalter, um keine PageModel-Klasse für eine leere Vorlage zu generieren. |

Verwenden Sie den Schalter `-h`, um Hilfe zum `aspnet-codegenerator razorpage`-Befehl zu erhalten:

```console
dotnet aspnet-codegenerator razorpage -h
```

Unter [Erstellen des Gerüsts für das Filmmodell](/aspnet/core/tutorials/razor-pages/model) finden Sie ein Beispiel für `dotnet aspnet-codegenerator razorpage`.

### <a name="identity"></a>Identität

Siehe [Gerüst für Identität](/aspnet/core/security/authentication/scaffold-identity)
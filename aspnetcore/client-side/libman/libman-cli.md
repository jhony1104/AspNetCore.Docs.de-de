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
# <a name="use-the-libman-cli-with-aspnet-core"></a><span data-ttu-id="5a4d0-103">Verwenden der Libman-CLI mit ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="5a4d0-103">Use the LibMan CLI with ASP.NET Core</span></span>

<span data-ttu-id="5a4d0-104">Von [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="5a4d0-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="5a4d0-105">Die [Libman](xref:client-side/libman/index) CLI ist ein plattformübergreifendes Tool, das überall unterstützt wird, wo .net Core unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-105">The [LibMan](xref:client-side/libman/index) CLI is a cross-platform tool that's supported everywhere .NET Core is supported.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5a4d0-106">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="5a4d0-106">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="5a4d0-107">Installation</span><span class="sxs-lookup"><span data-stu-id="5a4d0-107">Installation</span></span>

<span data-ttu-id="5a4d0-108">So installieren Sie die Libman CLI:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-108">To install the LibMan CLI:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

<span data-ttu-id="5a4d0-109">Ein [globales .net Core-Tool](/dotnet/core/tools/global-tools#install-a-global-tool) wird aus dem nuget-Paket [Microsoft. Web. LibraryManager. CLI](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) installiert.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-109">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet package.</span></span>

<span data-ttu-id="5a4d0-110">So installieren Sie die Libman-CLI über eine bestimmte nuget-Paketquelle:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-110">To install the LibMan CLI from a specific NuGet package source:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

<span data-ttu-id="5a4d0-111">Im vorherigen Beispiel wird ein globales .net Core-Tool aus der *C:\Temp\Microsoft.Web.LibraryManager.CLI.1.0.94-g606058a278.nupkg* -Datei des lokalen Windows-Computers installiert.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-111">In the preceding example, a .NET Core Global Tool is installed from the local Windows machine's *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* file.</span></span>

## <a name="usage"></a><span data-ttu-id="5a4d0-112">Verwendungs-</span><span class="sxs-lookup"><span data-stu-id="5a4d0-112">Usage</span></span>

<span data-ttu-id="5a4d0-113">Nach der erfolgreichen Installation der CLI kann der folgende Befehl verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-113">After successful installation of the CLI, the following command can be used:</span></span>

```console
libman
```

<span data-ttu-id="5a4d0-114">Anzeigen der installierten CLI-Version:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-114">To view the installed CLI version:</span></span>

```console
libman --version
```

<span data-ttu-id="5a4d0-115">So zeigen Sie die verfügbaren CLI-Befehle an:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-115">To view the available CLI commands:</span></span>

```console
libman --help
```

<span data-ttu-id="5a4d0-116">Der vorherige Befehl zeigt eine Ausgabe ähnlich der folgenden an:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-116">The preceding command displays output similar to the following:</span></span>

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

<span data-ttu-id="5a4d0-117">In den folgenden Abschnitten werden die verfügbaren CLI-Befehle erläutert.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-117">The following sections outline the available CLI commands.</span></span>

## <a name="initialize-libman-in-the-project"></a><span data-ttu-id="5a4d0-118">Initialisieren von Libman im Projekt</span><span class="sxs-lookup"><span data-stu-id="5a4d0-118">Initialize LibMan in the project</span></span>

<span data-ttu-id="5a4d0-119">Der `libman init`-Befehl erstellt eine *Libman. JSON* -Datei, falls noch keine vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-119">The `libman init` command creates a *libman.json* file if one doesn't exist.</span></span> <span data-ttu-id="5a4d0-120">Die Datei wird mit dem Inhalt der Standardelement Vorlage erstellt.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-120">The file is created with the default item template content.</span></span>

### <a name="synopsis"></a><span data-ttu-id="5a4d0-121">Übersicht</span><span class="sxs-lookup"><span data-stu-id="5a4d0-121">Synopsis</span></span>

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a><span data-ttu-id="5a4d0-122">Options</span><span class="sxs-lookup"><span data-stu-id="5a4d0-122">Options</span></span>

<span data-ttu-id="5a4d0-123">Für den Befehl `libman init` sind die folgenden Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-123">The following options are available for the `libman init` command:</span></span>

* `-d|--default-destination <PATH>`

  <span data-ttu-id="5a4d0-124">Ein Pfad relativ zum aktuellen Ordner.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-124">A path relative to the current folder.</span></span> <span data-ttu-id="5a4d0-125">Bibliotheksdateien werden an diesem Speicherort installiert, wenn keine `destination` Eigenschaft für eine Bibliothek in " *Libman. JSON*" definiert ist.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-125">Library files are installed in this location if no `destination` property is defined for a library in *libman.json*.</span></span> <span data-ttu-id="5a4d0-126">Der `<PATH>`-Wert wird in die `defaultDestination`-Eigenschaft von *Libman. JSON*geschrieben.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-126">The `<PATH>` value is written to the `defaultDestination` property of *libman.json*.</span></span>

* `-p|--default-provider <PROVIDER>`

  <span data-ttu-id="5a4d0-127">Der zu verwendende Anbieter, wenn für eine bestimmte Bibliothek kein Anbieter definiert ist.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-127">The provider to use if no provider is defined for a given library.</span></span> <span data-ttu-id="5a4d0-128">Der `<PROVIDER>`-Wert wird in die `defaultProvider`-Eigenschaft von *Libman. JSON*geschrieben.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-128">The `<PROVIDER>` value is written to the `defaultProvider` property of *libman.json*.</span></span> <span data-ttu-id="5a4d0-129">Ersetzen Sie `<PROVIDER>` durch einen der folgenden Werte:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-129">Replace `<PROVIDER>` with one of the following values:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="5a4d0-130">Beispiele</span><span class="sxs-lookup"><span data-stu-id="5a4d0-130">Examples</span></span>

<span data-ttu-id="5a4d0-131">So erstellen Sie eine *Libman. JSON* -Datei in einem ASP.net Core Projekt:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-131">To create a *libman.json* file in an ASP.NET Core project:</span></span>

* <span data-ttu-id="5a4d0-132">Navigieren Sie zum Projektstamm.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-132">Navigate to the project root.</span></span>
* <span data-ttu-id="5a4d0-133">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-133">Run the following command:</span></span>

  ```console
  libman init
  ```

* <span data-ttu-id="5a4d0-134">Geben Sie den Namen des Standard Anbieters ein, oder drücken Sie `Enter`, um den standardmäßigen cdnjs-Anbieter zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-134">Type the name of the default provider, or press `Enter` to use the default CDNJS provider.</span></span> <span data-ttu-id="5a4d0-135">Gültige Werte sind:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-135">Valid values include:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![Libman init-Befehl-Standardanbieter](_static/libman-init-provider.png)

<span data-ttu-id="5a4d0-137">Eine *Libman. JSON* -Datei wird dem Projektstamm mit folgendem Inhalt hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-137">A *libman.json* file is added to the project root with the following content:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a><span data-ttu-id="5a4d0-138">Bibliotheksdateien hinzufügen</span><span class="sxs-lookup"><span data-stu-id="5a4d0-138">Add library files</span></span>

<span data-ttu-id="5a4d0-139">Mit dem Befehl `libman install` werden Bibliotheksdateien heruntergeladen und in das Projekt installiert.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-139">The `libman install` command downloads and installs library files into the project.</span></span> <span data-ttu-id="5a4d0-140">Wenn noch keiner vorhanden ist, wird eine *Libman. JSON* -Datei hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-140">A *libman.json* file is added if one doesn't exist.</span></span> <span data-ttu-id="5a4d0-141">Die Datei " *Libman. JSON* " wird so geändert, dass Konfigurationsdetails für die Bibliotheksdateien gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-141">The *libman.json* file is modified to store configuration details for the library files.</span></span>

### <a name="synopsis"></a><span data-ttu-id="5a4d0-142">Übersicht</span><span class="sxs-lookup"><span data-stu-id="5a4d0-142">Synopsis</span></span>

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="5a4d0-143">Argumente</span><span class="sxs-lookup"><span data-stu-id="5a4d0-143">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="5a4d0-144">Der Name der zu installierenden Bibliothek.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-144">The name of the library to install.</span></span> <span data-ttu-id="5a4d0-145">Dieser Name kann die Notation der Versionsnummer (z. b. `@1.2.0`) enthalten.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-145">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="5a4d0-146">Options</span><span class="sxs-lookup"><span data-stu-id="5a4d0-146">Options</span></span>

<span data-ttu-id="5a4d0-147">Für den Befehl `libman install` sind die folgenden Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-147">The following options are available for the `libman install` command:</span></span>

* `-d|--destination <PATH>`

  <span data-ttu-id="5a4d0-148">Der Speicherort für die Installation der Bibliothek.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-148">The location to install the library.</span></span> <span data-ttu-id="5a4d0-149">Wenn nicht angegeben, wird der Standard Speicherort verwendet.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-149">If not specified, the default location is used.</span></span> <span data-ttu-id="5a4d0-150">Wenn in *Libman. JSON*keine `defaultDestination` Eigenschaft angegeben ist, ist diese Option erforderlich.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-150">If no `defaultDestination` property is specified in *libman.json*, this option is required.</span></span>

* `--files <FILE>`

  <span data-ttu-id="5a4d0-151">Geben Sie den Namen der Datei an, die aus der Bibliothek installiert werden soll.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-151">Specify the name of the file to install from the library.</span></span> <span data-ttu-id="5a4d0-152">Wenn nicht angegeben, werden alle Dateien aus der Bibliothek installiert.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-152">If not specified, all files from the library are installed.</span></span> <span data-ttu-id="5a4d0-153">Geben Sie eine `--files` Option pro zu installierenden Datei an.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-153">Provide one `--files` option per file to be installed.</span></span> <span data-ttu-id="5a4d0-154">Relative Pfade werden ebenfalls unterstützt.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-154">Relative paths are supported too.</span></span> <span data-ttu-id="5a4d0-155">Beispiel: `--files dist/browser/signalr.js`.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-155">For example: `--files dist/browser/signalr.js`.</span></span>

* `-p|--provider <PROVIDER>`

  <span data-ttu-id="5a4d0-156">Der Name des Anbieters, der für die Bibliothek Beschaffung verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-156">The name of the provider to use for the library acquisition.</span></span> <span data-ttu-id="5a4d0-157">Ersetzen Sie `<PROVIDER>` durch einen der folgenden Werte:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-157">Replace `<PROVIDER>` with one of the following values:</span></span>
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  <span data-ttu-id="5a4d0-158">Wenn nichts angegeben ist, wird die `defaultProvider`-Eigenschaft in *Libman. JSON* verwendet.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-158">If not specified, the `defaultProvider` property in *libman.json* is used.</span></span> <span data-ttu-id="5a4d0-159">Wenn in *Libman. JSON*keine `defaultProvider` Eigenschaft angegeben ist, ist diese Option erforderlich.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-159">If no `defaultProvider` property is specified in *libman.json*, this option is required.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="5a4d0-160">Beispiele</span><span class="sxs-lookup"><span data-stu-id="5a4d0-160">Examples</span></span>

<span data-ttu-id="5a4d0-161">Sehen Sie sich die folgende *Libman. JSON* -Datei an:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-161">Consider the following *libman.json* file:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

<span data-ttu-id="5a4d0-162">So installieren Sie die jQuery *. min. js* -Datei der jQuery-Version 3.2.1 im Ordner *wwwroot/Scripts/jQuery* mithilfe des cdnjs-Anbieters:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-162">To install the jQuery version 3.2.1 *jquery.min.js* file to the *wwwroot/scripts/jquery* folder using the CDNJS provider:</span></span>

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

<span data-ttu-id="5a4d0-163">Die Datei " *Libman. JSON* " ähnelt der folgenden:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-163">The *libman.json* file resembles the following:</span></span>

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

<span data-ttu-id="5a4d0-164">So installieren Sie die Dateien " *Calendar. js* " und " *Calendar. CSS* " von *C:\\Temp\\condesocalendar\\* mithilfe des Dateisystem Anbieters:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-164">To install the *calendar.js* and *calendar.css* files from *C:\\temp\\contosoCalendar\\* using the file system provider:</span></span>

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

<span data-ttu-id="5a4d0-165">Die folgende Eingabeaufforderung wird aus zwei Gründen angezeigt:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-165">The following prompt appears for two reasons:</span></span>

* <span data-ttu-id="5a4d0-166">Die *Libman. JSON* -Datei enthält keine `defaultDestination`-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-166">The *libman.json* file doesn't contain a `defaultDestination` property.</span></span>
* <span data-ttu-id="5a4d0-167">Der `libman install`-Befehl enthält die `-d|--destination`-Option nicht.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-167">The `libman install` command doesn't contain the `-d|--destination` option.</span></span>

![Libman-Installations Befehl-Ziel](_static/libman-install-destination.png)

<span data-ttu-id="5a4d0-169">Nachdem Sie das Standardziel akzeptiert haben, ähnelt die Datei " *Libman. JSON* " folgendem:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-169">After accepting the default destination, the *libman.json* file resembles the following:</span></span>

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

## <a name="restore-library-files"></a><span data-ttu-id="5a4d0-170">Bibliotheksdateien wiederherstellen</span><span class="sxs-lookup"><span data-stu-id="5a4d0-170">Restore library files</span></span>

<span data-ttu-id="5a4d0-171">Mit dem Befehl `libman restore` werden Bibliotheksdateien installiert, die in *Libman. JSON*definiert sind.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-171">The `libman restore` command installs library files defined in *libman.json*.</span></span> <span data-ttu-id="5a4d0-172">Dabei gelten folgende Regeln:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-172">The following rules apply:</span></span>

* <span data-ttu-id="5a4d0-173">Wenn keine *Libman. JSON* -Datei im Projektstamm vorhanden ist, wird ein Fehler zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-173">If no *libman.json* file exists in the project root, an error is returned.</span></span>
* <span data-ttu-id="5a4d0-174">Wenn eine Bibliothek einen Anbieter angibt, wird die `defaultProvider`-Eigenschaft in *Libman. JSON* ignoriert.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-174">If a library specifies a provider, the `defaultProvider` property in *libman.json* is ignored.</span></span>
* <span data-ttu-id="5a4d0-175">Wenn eine Bibliothek ein Ziel angibt, wird die `defaultDestination`-Eigenschaft in *Libman. JSON* ignoriert.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-175">If a library specifies a destination, the `defaultDestination` property in *libman.json* is ignored.</span></span>

### <a name="synopsis"></a><span data-ttu-id="5a4d0-176">Übersicht</span><span class="sxs-lookup"><span data-stu-id="5a4d0-176">Synopsis</span></span>

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a><span data-ttu-id="5a4d0-177">Options</span><span class="sxs-lookup"><span data-stu-id="5a4d0-177">Options</span></span>

<span data-ttu-id="5a4d0-178">Für den Befehl `libman restore` sind die folgenden Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-178">The following options are available for the `libman restore` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="5a4d0-179">Beispiele</span><span class="sxs-lookup"><span data-stu-id="5a4d0-179">Examples</span></span>

<span data-ttu-id="5a4d0-180">So stellen Sie die in " *Libman. JSON*" definierten Bibliotheksdateien wieder her:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-180">To restore the library files defined in *libman.json*:</span></span>

```console
libman restore
```

## <a name="delete-library-files"></a><span data-ttu-id="5a4d0-181">Löschen von Bibliotheksdateien</span><span class="sxs-lookup"><span data-stu-id="5a4d0-181">Delete library files</span></span>

<span data-ttu-id="5a4d0-182">Der `libman clean` Befehl löscht Bibliotheksdateien, die zuvor über Libman wieder hergestellt wurden.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-182">The `libman clean` command deletes library files previously restored via LibMan.</span></span> <span data-ttu-id="5a4d0-183">Ordner, die nach dem Löschen des Vorgangs leer werden.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-183">Folders that become empty after this operation are deleted.</span></span> <span data-ttu-id="5a4d0-184">Die zugeordneten Konfigurationen der Bibliotheksdateien in der `libraries`-Eigenschaft von " *Libman. JSON* " werden nicht entfernt.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-184">The library files' associated configurations in the `libraries` property of *libman.json* aren't removed.</span></span>

### <a name="synopsis"></a><span data-ttu-id="5a4d0-185">Übersicht</span><span class="sxs-lookup"><span data-stu-id="5a4d0-185">Synopsis</span></span>

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a><span data-ttu-id="5a4d0-186">Options</span><span class="sxs-lookup"><span data-stu-id="5a4d0-186">Options</span></span>

<span data-ttu-id="5a4d0-187">Für den Befehl `libman clean` sind die folgenden Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-187">The following options are available for the `libman clean` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="5a4d0-188">Beispiele</span><span class="sxs-lookup"><span data-stu-id="5a4d0-188">Examples</span></span>

<span data-ttu-id="5a4d0-189">So löschen Sie Bibliotheksdateien, die über Libman installiert wurden:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-189">To delete library files installed via LibMan:</span></span>

```console
libman clean
```

## <a name="uninstall-library-files"></a><span data-ttu-id="5a4d0-190">Bibliotheksdateien deinstallieren</span><span class="sxs-lookup"><span data-stu-id="5a4d0-190">Uninstall library files</span></span>

<span data-ttu-id="5a4d0-191">Der `libman uninstall` Befehl:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-191">The `libman uninstall` command:</span></span>

* <span data-ttu-id="5a4d0-192">Löscht alle Dateien, die der angegebenen Bibliothek zugeordnet sind, aus dem Ziel in *Libman. JSON*.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-192">Deletes all files associated with the specified library from the destination in *libman.json*.</span></span>
* <span data-ttu-id="5a4d0-193">Entfernt die zugehörige Bibliotheks Konfiguration aus *Libman. JSON*.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-193">Removes the associated library configuration from *libman.json*.</span></span>

<span data-ttu-id="5a4d0-194">Fehler tritt auf, wenn Folgendes zutrifft:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-194">An error occurs when:</span></span>

* <span data-ttu-id="5a4d0-195">Im Stammverzeichnis des Projekts ist keine *Libman. JSON* -Datei vorhanden.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-195">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="5a4d0-196">Die angegebene Bibliothek ist nicht vorhanden.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-196">The specified library doesn't exist.</span></span>

<span data-ttu-id="5a4d0-197">Wenn mehr als eine Bibliothek mit dem gleichen Namen installiert ist, werden Sie aufgefordert, einen auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-197">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="5a4d0-198">Übersicht</span><span class="sxs-lookup"><span data-stu-id="5a4d0-198">Synopsis</span></span>

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="5a4d0-199">Argumente</span><span class="sxs-lookup"><span data-stu-id="5a4d0-199">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="5a4d0-200">Der Name der Bibliothek, die deinstalliert werden soll.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-200">The name of the library to uninstall.</span></span> <span data-ttu-id="5a4d0-201">Dieser Name kann die Notation der Versionsnummer (z. b. `@1.2.0`) enthalten.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-201">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="5a4d0-202">Options</span><span class="sxs-lookup"><span data-stu-id="5a4d0-202">Options</span></span>

<span data-ttu-id="5a4d0-203">Für den Befehl `libman uninstall` sind die folgenden Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-203">The following options are available for the `libman uninstall` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="5a4d0-204">Beispiele</span><span class="sxs-lookup"><span data-stu-id="5a4d0-204">Examples</span></span>

<span data-ttu-id="5a4d0-205">Sehen Sie sich die folgende *Libman. JSON* -Datei an:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-205">Consider the following *libman.json* file:</span></span>

[!code-json[](samples/LibManSample/libman.json)]

* <span data-ttu-id="5a4d0-206">Wenn Sie jQuery deinstallieren möchten, wird einer der folgenden Befehle erfolgreich ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-206">To uninstall jQuery, either of the following commands succeed:</span></span>

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* <span data-ttu-id="5a4d0-207">So deinstallieren Sie die über den `filesystem` Anbieter installierten lodash-Dateien:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-207">To uninstall the Lodash files installed via the `filesystem` provider:</span></span>

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a><span data-ttu-id="5a4d0-208">Version der Update Bibliothek</span><span class="sxs-lookup"><span data-stu-id="5a4d0-208">Update library version</span></span>

<span data-ttu-id="5a4d0-209">Der `libman update`-Befehl aktualisiert eine Bibliothek, die über Libman installiert wurde, auf die angegebene Version.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-209">The `libman update` command updates a library installed via LibMan to the specified version.</span></span>

<span data-ttu-id="5a4d0-210">Fehler tritt auf, wenn Folgendes zutrifft:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-210">An error occurs when:</span></span>

* <span data-ttu-id="5a4d0-211">Im Stammverzeichnis des Projekts ist keine *Libman. JSON* -Datei vorhanden.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-211">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="5a4d0-212">Die angegebene Bibliothek ist nicht vorhanden.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-212">The specified library doesn't exist.</span></span>

<span data-ttu-id="5a4d0-213">Wenn mehr als eine Bibliothek mit dem gleichen Namen installiert ist, werden Sie aufgefordert, einen auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-213">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="5a4d0-214">Übersicht</span><span class="sxs-lookup"><span data-stu-id="5a4d0-214">Synopsis</span></span>

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="5a4d0-215">Argumente</span><span class="sxs-lookup"><span data-stu-id="5a4d0-215">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="5a4d0-216">Der Name der zu aktualisierenden Bibliothek.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-216">The name of the library to update.</span></span>

### <a name="options"></a><span data-ttu-id="5a4d0-217">Options</span><span class="sxs-lookup"><span data-stu-id="5a4d0-217">Options</span></span>

<span data-ttu-id="5a4d0-218">Für den Befehl `libman update` sind die folgenden Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-218">The following options are available for the `libman update` command:</span></span>

* `-pre`

  <span data-ttu-id="5a4d0-219">Rufen Sie die neueste Vorabversion der Bibliothek ab.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-219">Obtain the latest prerelease version of the library.</span></span>

* `--to <VERSION>`

  <span data-ttu-id="5a4d0-220">Beziehen Sie eine bestimmte Version der Bibliothek.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-220">Obtain a specific version of the library.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="5a4d0-221">Beispiele</span><span class="sxs-lookup"><span data-stu-id="5a4d0-221">Examples</span></span>

* <span data-ttu-id="5a4d0-222">So aktualisieren Sie jQuery auf die neueste Version:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-222">To update jQuery to the latest version:</span></span>

  ```console
  libman update jquery
  ```

* <span data-ttu-id="5a4d0-223">So aktualisieren Sie jQuery auf Version 3.3.1:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-223">To update jQuery to version 3.3.1:</span></span>

  ```console
  libman update jquery --to 3.3.1
  ```

* <span data-ttu-id="5a4d0-224">So aktualisieren Sie jQuery auf die neueste Vorabversion:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-224">To update jQuery to the latest prerelease version:</span></span>

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a><span data-ttu-id="5a4d0-225">Bibliotheks Cache verwalten</span><span class="sxs-lookup"><span data-stu-id="5a4d0-225">Manage library cache</span></span>

<span data-ttu-id="5a4d0-226">Der `libman cache`-Befehl verwaltet den Libman-Bibliotheks Cache.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-226">The `libman cache` command manages the LibMan library cache.</span></span> <span data-ttu-id="5a4d0-227">Der `filesystem`-Anbieter verwendet den Bibliotheks Cache nicht.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-227">The `filesystem` provider doesn't use the library cache.</span></span>

### <a name="synopsis"></a><span data-ttu-id="5a4d0-228">Übersicht</span><span class="sxs-lookup"><span data-stu-id="5a4d0-228">Synopsis</span></span>

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="5a4d0-229">Argumente</span><span class="sxs-lookup"><span data-stu-id="5a4d0-229">Arguments</span></span>

`PROVIDER`

<span data-ttu-id="5a4d0-230">Wird nur mit dem `clean`-Befehl verwendet.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-230">Only used with the `clean` command.</span></span> <span data-ttu-id="5a4d0-231">Gibt den zu bereinigen Anbieter Cache an.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-231">Specifies the provider cache to clean.</span></span> <span data-ttu-id="5a4d0-232">Gültige Werte sind:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-232">Valid values include:</span></span>

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a><span data-ttu-id="5a4d0-233">Options</span><span class="sxs-lookup"><span data-stu-id="5a4d0-233">Options</span></span>

<span data-ttu-id="5a4d0-234">Für den Befehl `libman cache` sind die folgenden Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-234">The following options are available for the `libman cache` command:</span></span>

* `--files`

  <span data-ttu-id="5a4d0-235">Listet die Namen der Dateien auf, die zwischengespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-235">List the names of files that are cached.</span></span>

* `--libraries`

  <span data-ttu-id="5a4d0-236">Listet die Namen der Bibliotheken auf, die zwischengespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-236">List the names of libraries that are cached.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="5a4d0-237">Beispiele</span><span class="sxs-lookup"><span data-stu-id="5a4d0-237">Examples</span></span>

* <span data-ttu-id="5a4d0-238">Verwenden Sie einen der folgenden Befehle, um die Namen der zwischengespeicherten Bibliotheken pro Anbieter anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-238">To view the names of cached libraries per provider, use one of the following commands:</span></span>

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  <span data-ttu-id="5a4d0-239">Dadurch werden Informationen angezeigt, die mit denen der folgenden Ausgabe vergleichbar sind:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-239">Output similar to the following is displayed:</span></span>

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

* <span data-ttu-id="5a4d0-240">So zeigen Sie die Namen der zwischengespeicherten Bibliotheksdateien pro Anbieter an:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-240">To view the names of cached library files per provider:</span></span>

  ```console
  libman cache list --files
  ```

  <span data-ttu-id="5a4d0-241">Dadurch werden Informationen angezeigt, die mit denen der folgenden Ausgabe vergleichbar sind:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-241">Output similar to the following is displayed:</span></span>

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

  <span data-ttu-id="5a4d0-242">Beachten Sie, dass die vorherige Ausgabe zeigt, dass die jQuery-Versionen 3.2.1 und 3.3.1 unter dem cdnjs-Anbieter zwischengespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="5a4d0-242">Notice the preceding output shows that jQuery versions 3.2.1 and 3.3.1 are cached under the CDNJS provider.</span></span>

* <span data-ttu-id="5a4d0-243">So leeren Sie den Bibliotheks Cache für den cdnjs-Anbieter:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-243">To empty the library cache for the CDNJS provider:</span></span>

  ```console
  libman cache clean cdnjs
  ```

  <span data-ttu-id="5a4d0-244">Nach dem leeren des cdnjs-Anbieter Caches zeigt der `libman cache list` Befehl Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-244">After emptying the CDNJS provider cache, the `libman cache list` command displays the following:</span></span>

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

* <span data-ttu-id="5a4d0-245">So leeren Sie den Cache für alle unterstützten Anbieter:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-245">To empty the cache for all supported providers:</span></span>

  ```console
  libman cache clean
  ```

  <span data-ttu-id="5a4d0-246">Nach dem leeren aller Anbieter Caches zeigt der `libman cache list` Befehl Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="5a4d0-246">After emptying all provider caches, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a><span data-ttu-id="5a4d0-247">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5a4d0-247">Additional resources</span></span>

* [<span data-ttu-id="5a4d0-248">Globales Tool installieren</span><span class="sxs-lookup"><span data-stu-id="5a4d0-248">Install a Global Tool</span></span>](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [<span data-ttu-id="5a4d0-249">GitHub-Repository für LibMan</span><span class="sxs-lookup"><span data-stu-id="5a4d0-249">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)

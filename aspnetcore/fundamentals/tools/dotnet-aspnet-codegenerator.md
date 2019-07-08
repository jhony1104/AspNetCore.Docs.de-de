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
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="fcc83-103">dotnet aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="fcc83-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="fcc83-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fcc83-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="fcc83-105">`dotnet aspnet-codegenerator`: Führt die ASP.NET Core-Gerüstbauengine aus.</span><span class="sxs-lookup"><span data-stu-id="fcc83-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="fcc83-106">`dotnet aspnet-codegenerator` wird nur für den Gerüstbau von der Befehlszeile aus benötigt, nicht zum Gerüstbau mit Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="fcc83-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not need to use scaffolding with Visual Studio.</span></span>

<span data-ttu-id="fcc83-107">Dieser Artikel gilt für [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.2) höher.</span><span class="sxs-lookup"><span data-stu-id="fcc83-107">This article applies to [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.2) and later.</span></span>

## <a name="installing-aspnet-codegenerator"></a><span data-ttu-id="fcc83-108">Installieren von „aspnet-codegenerator“</span><span class="sxs-lookup"><span data-stu-id="fcc83-108">Installing aspnet-codegenerator</span></span>

<span data-ttu-id="fcc83-109">`aspnet-codegenerator` ist ein [globales Tool](/dotnet/core/tools/global-tools), das installiert werden muss.</span><span class="sxs-lookup"><span data-stu-id="fcc83-109">`aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="fcc83-110">Über den folgenden Befehl wird die neueste stabile Version des `aspnet-codegenerator`-Tools installiert:</span><span class="sxs-lookup"><span data-stu-id="fcc83-110">The following command installs the latest stable version of the `aspnet-codegenerator` tool:</span></span>

```console
dotnet tool install -g aspnet-codegenerator
```

<span data-ttu-id="fcc83-111">Mit dem folgenden Befehl wird `aspnet-codegenerator` auf die neueste stabile Version aktualisiert, die in den installierten .NET Core SDKs verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="fcc83-111">The following command updates `aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```console
dotnet tool update -g aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="fcc83-112">Übersicht</span><span class="sxs-lookup"><span data-stu-id="fcc83-112">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="fcc83-113">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fcc83-113">Description</span></span>

<span data-ttu-id="fcc83-114">Der globale Befehl `dotnet aspnet-codegenerator ` führt den ASP.NET Code-Codegenerator und die Gerüstbauengine aus.</span><span class="sxs-lookup"><span data-stu-id="fcc83-114">The `dotnet aspnet-codegenerator ` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="fcc83-115">Argumente</span><span class="sxs-lookup"><span data-stu-id="fcc83-115">Arguments</span></span>

`generator`

<span data-ttu-id="fcc83-116">Der auszuführende Codegenerator.</span><span class="sxs-lookup"><span data-stu-id="fcc83-116">The code generator to run.</span></span> <span data-ttu-id="fcc83-117">Folgende Generatoren sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="fcc83-117">The following generators are available:</span></span>

| <span data-ttu-id="fcc83-118">Generator</span><span class="sxs-lookup"><span data-stu-id="fcc83-118">Generator</span></span> | <span data-ttu-id="fcc83-119">Vorgang</span><span class="sxs-lookup"><span data-stu-id="fcc83-119">Operation</span></span> |
| ----------------- | ------------ | 
| <span data-ttu-id="fcc83-120">area</span><span class="sxs-lookup"><span data-stu-id="fcc83-120">area</span></span>      | [<span data-ttu-id="fcc83-121">Gerüstbau für einen Bereich</span><span class="sxs-lookup"><span data-stu-id="fcc83-121">Scaffolds an Area</span></span>](/aspnet/core/mvc/controllers/areas) |
  <span data-ttu-id="fcc83-122">Controller</span><span class="sxs-lookup"><span data-stu-id="fcc83-122">controller</span></span>| [<span data-ttu-id="fcc83-123">Gerüstbau für einen Controller</span><span class="sxs-lookup"><span data-stu-id="fcc83-123">Scaffolds a controller</span></span>](/aspnet/core/tutorials/first-mvc-app/adding-model) |
  <span data-ttu-id="fcc83-124">identity</span><span class="sxs-lookup"><span data-stu-id="fcc83-124">identity</span></span>  | [<span data-ttu-id="fcc83-125">Gerüstbau für eine Identität</span><span class="sxs-lookup"><span data-stu-id="fcc83-125">Scaffolds Identity</span></span>](/aspnet/core/security/authentication/scaffold-identity) |
  <span data-ttu-id="fcc83-126">razorpage</span><span class="sxs-lookup"><span data-stu-id="fcc83-126">razorpage</span></span> | [<span data-ttu-id="fcc83-127">Gerüstbau für Razor Pages</span><span class="sxs-lookup"><span data-stu-id="fcc83-127">Scaffolds Razor Pages</span></span>](/aspnet/core/tutorials/razor-pages/model) |
  <span data-ttu-id="fcc83-128">view</span><span class="sxs-lookup"><span data-stu-id="fcc83-128">view</span></span>      | [<span data-ttu-id="fcc83-129">Gerüstbau für eine Ansicht</span><span class="sxs-lookup"><span data-stu-id="fcc83-129">Scaffolds a view</span></span>](/aspnet/core/mvc/views/overview) |

## <a name="options"></a><span data-ttu-id="fcc83-130">Optionen</span><span class="sxs-lookup"><span data-stu-id="fcc83-130">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="fcc83-131">Gibt das NuGet-Paketverzeichnis an.</span><span class="sxs-lookup"><span data-stu-id="fcc83-131">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="fcc83-132">Legt die Buildkonfiguration fest.</span><span class="sxs-lookup"><span data-stu-id="fcc83-132">Defines the build configuration.</span></span> <span data-ttu-id="fcc83-133">Der Standardwert ist `Debug`sein.</span><span class="sxs-lookup"><span data-stu-id="fcc83-133">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="fcc83-134">Das zu verwendende [Zielframework](/dotnet/standard/frameworks).</span><span class="sxs-lookup"><span data-stu-id="fcc83-134">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="fcc83-135">Beispielsweise `net46`.</span><span class="sxs-lookup"><span data-stu-id="fcc83-135">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="fcc83-136">Der Basispfad für den Build.</span><span class="sxs-lookup"><span data-stu-id="fcc83-136">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="fcc83-137">Druckt eine kurze Hilfe für den Befehl.</span><span class="sxs-lookup"><span data-stu-id="fcc83-137">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="fcc83-138">Erstellt das Projekt nicht vor der Ausführung.</span><span class="sxs-lookup"><span data-stu-id="fcc83-138">Doesn't build the project before running.</span></span> <span data-ttu-id="fcc83-139">Zudem wird das Flag `--no-restore` implizit festgelegt.</span><span class="sxs-lookup"><span data-stu-id="fcc83-139">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="fcc83-140">Gibt den Pfad der auszuführenden Projektdatei an (Ordnername oder vollständiger Pfad).</span><span class="sxs-lookup"><span data-stu-id="fcc83-140">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="fcc83-141">Wenn nicht angegeben, wird standardmäßig das aktuelle Verzeichnis gewählt.</span><span class="sxs-lookup"><span data-stu-id="fcc83-141">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="fcc83-142">Generatoroptionen</span><span class="sxs-lookup"><span data-stu-id="fcc83-142">Generator options</span></span>

<span data-ttu-id="fcc83-143">In den folgenden Abschnitte werden die verfügbaren Optionen für die unterstützten Generatoren vorgestellt:</span><span class="sxs-lookup"><span data-stu-id="fcc83-143">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="fcc83-144">Bereich</span><span class="sxs-lookup"><span data-stu-id="fcc83-144">Area</span></span>
* <span data-ttu-id="fcc83-145">Controller</span><span class="sxs-lookup"><span data-stu-id="fcc83-145">Controller</span></span>
* <span data-ttu-id="fcc83-146">Identität</span><span class="sxs-lookup"><span data-stu-id="fcc83-146">Identity</span></span>  
* <span data-ttu-id="fcc83-147">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="fcc83-147">Razorpage</span></span>
* <span data-ttu-id="fcc83-148">Ansicht</span><span class="sxs-lookup"><span data-stu-id="fcc83-148">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="fcc83-149">Bereichsoptionen</span><span class="sxs-lookup"><span data-stu-id="fcc83-149">Area options</span></span>

<span data-ttu-id="fcc83-150">Dieses Tool ist für ASP.NET Core-Webprojekte mit Controllern und Ansichten vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="fcc83-150">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="fcc83-151">Es ist nicht für Razor Pages-Apps gedacht.</span><span class="sxs-lookup"><span data-stu-id="fcc83-151">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="fcc83-152">Verwendung: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="fcc83-152">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="fcc83-153">Der oben gezeigte Befehl generiert die folgenden Ordner:</span><span class="sxs-lookup"><span data-stu-id="fcc83-153">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="fcc83-154">*Bereiche*</span><span class="sxs-lookup"><span data-stu-id="fcc83-154">*Areas*</span></span>
  * <span data-ttu-id="fcc83-155">*AreaNameToGenerate*</span><span class="sxs-lookup"><span data-stu-id="fcc83-155">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="fcc83-156">*Controller*</span><span class="sxs-lookup"><span data-stu-id="fcc83-156">*Controllers*</span></span>
    * <span data-ttu-id="fcc83-157">*Data*</span><span class="sxs-lookup"><span data-stu-id="fcc83-157">*Data*</span></span>
    * <span data-ttu-id="fcc83-158">*Models*</span><span class="sxs-lookup"><span data-stu-id="fcc83-158">*Models*</span></span>
    * <span data-ttu-id="fcc83-159">*Ansichten*</span><span class="sxs-lookup"><span data-stu-id="fcc83-159">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="fcc83-160">Controlleroptionen</span><span class="sxs-lookup"><span data-stu-id="fcc83-160">Controller options</span></span>

<span data-ttu-id="fcc83-161">Die folgende Tabelle zeigt die Optionen für `aspnet-codegenerator` `controller` und `razorpage`:</span><span class="sxs-lookup"><span data-stu-id="fcc83-161">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="fcc83-162">Die folgende Tabelle zeigt die Optionen für `aspnet-codegenerator controller`:</span><span class="sxs-lookup"><span data-stu-id="fcc83-162">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="fcc83-163">Option</span><span class="sxs-lookup"><span data-stu-id="fcc83-163">Option</span></span>               | <span data-ttu-id="fcc83-164">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fcc83-164">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="fcc83-165">--controllerName oder -name</span><span class="sxs-lookup"><span data-stu-id="fcc83-165">--controllerName or -name</span></span> | <span data-ttu-id="fcc83-166">Der Name des Controllers.</span><span class="sxs-lookup"><span data-stu-id="fcc83-166">Name of the controller.</span></span> |
| <span data-ttu-id="fcc83-167">--useAsyncActions oder -async</span><span class="sxs-lookup"><span data-stu-id="fcc83-167">--useAsyncActions or -async</span></span> | <span data-ttu-id="fcc83-168">Generiert asynchrone Controlleraktionen.</span><span class="sxs-lookup"><span data-stu-id="fcc83-168">Generate async controller actions.</span></span> |
| <span data-ttu-id="fcc83-169">--noViews oder -nv</span><span class="sxs-lookup"><span data-stu-id="fcc83-169">--noViews or -nv</span></span> | <span data-ttu-id="fcc83-170">Generiert **keine** Ansichten.</span><span class="sxs-lookup"><span data-stu-id="fcc83-170">Generate **no** views.</span></span> |
| <span data-ttu-id="fcc83-171">--restWithNoViews oder -api</span><span class="sxs-lookup"><span data-stu-id="fcc83-171">--restWithNoViews or -api</span></span>  | <span data-ttu-id="fcc83-172">Generiert einen Controller mit einer API im REST-Stil.</span><span class="sxs-lookup"><span data-stu-id="fcc83-172">Generate a Controller with REST style API.</span></span> <span data-ttu-id="fcc83-173">`noViews` wird vorausgesetzt, und ansichtsbezogene Optionen werden ignoriert.</span><span class="sxs-lookup"><span data-stu-id="fcc83-173">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="fcc83-174">--readWriteActions oder -actions</span><span class="sxs-lookup"><span data-stu-id="fcc83-174">--readWriteActions or -actions</span></span> | <span data-ttu-id="fcc83-175">Generiert einen Controller mit Lese-/Schreibaktionen ohne Modell.</span><span class="sxs-lookup"><span data-stu-id="fcc83-175">Generate controller with read/write actions without a model.</span></span> |

<span data-ttu-id="fcc83-176">Verwenden Sie den Schalter `-h`, um Hilfe zum `aspnet-codegenerator controller`-Befehl zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="fcc83-176">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```console
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="fcc83-177">Unter [Erstellen des Gerüsts für das Filmmodell](/aspnet/core/tutorials/razor-pages/model) finden Sie ein Beispiel für `dotnet aspnet-codegenerator controller`.</span><span class="sxs-lookup"><span data-stu-id="fcc83-177">See [Scaffold the movie model](/aspnet/core/tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="razorpage"></a><span data-ttu-id="fcc83-178">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="fcc83-178">Razorpage</span></span>

<a name="rp"></a>

<span data-ttu-id="fcc83-179">Für Razor Pages können jeweils einzelne Gerüste erstellt werden, indem der Name der neuen Seite und der zu verwendenden Vorlage angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="fcc83-179">Razor Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="fcc83-180">Folgende Vorlagen werden unterstützt:</span><span class="sxs-lookup"><span data-stu-id="fcc83-180">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="fcc83-181">Beispielsweise verwendet der folgende Befehl die Bearbeitungsvorlage, um *MyEdit.cshtml* und *MyEdit.cshtml.cs* zu generieren:</span><span class="sxs-lookup"><span data-stu-id="fcc83-181">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs*:</span></span>

```console
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="fcc83-182">Typischerweise werden die Vorlage und der Name der generierten Datei nicht angegeben, und es werden die folgenden Vorlagen erstellt:</span><span class="sxs-lookup"><span data-stu-id="fcc83-182">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="fcc83-183">Die folgende Tabelle zeigt die Optionen für `aspnet-codegenerator` `razorpage` und `controller`:</span><span class="sxs-lookup"><span data-stu-id="fcc83-183">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="fcc83-184">Die folgende Tabelle zeigt die Optionen für `aspnet-codegenerator razorpage`:</span><span class="sxs-lookup"><span data-stu-id="fcc83-184">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="fcc83-185">Option</span><span class="sxs-lookup"><span data-stu-id="fcc83-185">Option</span></span>               | <span data-ttu-id="fcc83-186">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="fcc83-186">Description</span></span>|
| ----------------- | ------------ |
|   <span data-ttu-id="fcc83-187">--namespaceName oder -namespace</span><span class="sxs-lookup"><span data-stu-id="fcc83-187">--namespaceName or -namespace</span></span> | <span data-ttu-id="fcc83-188">Der Name des Namespace, der für das generierte PageModel verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="fcc83-188">The name of the namespace to use for the generated PageModel</span></span> |
| <span data-ttu-id="fcc83-189">--partialView oder -partial</span><span class="sxs-lookup"><span data-stu-id="fcc83-189">--partialView or -partial</span></span> | <span data-ttu-id="fcc83-190">Generiert eine Teilansicht.</span><span class="sxs-lookup"><span data-stu-id="fcc83-190">Generate a partial view.</span></span> <span data-ttu-id="fcc83-191">Die Layoutoptionen -l und -udl werden ignoriert, wenn diese Option angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="fcc83-191">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="fcc83-192">--noPageModel oder -npm</span><span class="sxs-lookup"><span data-stu-id="fcc83-192">--noPageModel or -npm</span></span> | <span data-ttu-id="fcc83-193">Schalter, um keine PageModel-Klasse für eine leere Vorlage zu generieren.</span><span class="sxs-lookup"><span data-stu-id="fcc83-193">Switch to not generate a PageModel class for Empty template</span></span> |

<span data-ttu-id="fcc83-194">Verwenden Sie den Schalter `-h`, um Hilfe zum `aspnet-codegenerator razorpage`-Befehl zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="fcc83-194">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```console
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="fcc83-195">Unter [Erstellen des Gerüsts für das Filmmodell](/aspnet/core/tutorials/razor-pages/model) finden Sie ein Beispiel für `dotnet aspnet-codegenerator razorpage`.</span><span class="sxs-lookup"><span data-stu-id="fcc83-195">See [Scaffold the movie model](/aspnet/core/tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### <a name="identity"></a><span data-ttu-id="fcc83-196">Identität</span><span class="sxs-lookup"><span data-stu-id="fcc83-196">Identity</span></span>

<span data-ttu-id="fcc83-197">Siehe [Gerüst für Identität](/aspnet/core/security/authentication/scaffold-identity)</span><span class="sxs-lookup"><span data-stu-id="fcc83-197">See [Scaffold Identity](/aspnet/core/security/authentication/scaffold-identity)</span></span>
---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: Erfahren Sie, wie Razor Pages in ASP.NET Core codierungsseitige Szenarios einfacher und produktiver gestalten als MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/18/2019
uid: razor-pages/sdk
ms.openlocfilehash: 1dc001c7c5fe320629835e06fe6db7fadabff94d
ms.sourcegitcommit: 6189b0ced9c115248c6ede02efcd0b29d31f2115
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2019
ms.locfileid: "69985401"
---
# <a name="aspnet-core-razor-sdk"></a><span data-ttu-id="64ad6-103">ASP.NET Core Razor SDK</span><span class="sxs-lookup"><span data-stu-id="64ad6-103">ASP.NET Core Razor SDK</span></span>

<span data-ttu-id="64ad6-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="64ad6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="overview"></a><span data-ttu-id="64ad6-105">Übersicht</span><span class="sxs-lookup"><span data-stu-id="64ad6-105">Overview</span></span>

<span data-ttu-id="64ad6-106">Die [!INCLUDE[](~/includes/2.1-SDK.md)] enthält die `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor-SDK).</span><span class="sxs-lookup"><span data-stu-id="64ad6-106">The [!INCLUDE[](~/includes/2.1-SDK.md)] includes the `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK).</span></span> <span data-ttu-id="64ad6-107">Das Razor SDK:</span><span class="sxs-lookup"><span data-stu-id="64ad6-107">The Razor SDK:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"
* <span data-ttu-id="64ad6-108">Standardisiert die Vorgänge zum Erstellen, Verpacken und Veröffentlichen von Projekten, die [Razor](xref:mvc/views/razor)-Dateien für auf ASP.NET Core MVC basierende Projekte enthalten.</span><span class="sxs-lookup"><span data-stu-id="64ad6-108">Standardizes the experience around building, packaging, and publishing projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based projects.</span></span>
* <span data-ttu-id="64ad6-109">Enthält eine vordefinierte Reihe von Zielen, Eigenschaften und Elementen, die das Anpassen der Razor-Dateien ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="64ad6-109">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor files.</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
* <span data-ttu-id="64ad6-110">ist erforderlich, um Projekte zu erstellen, zu verpacken und zu veröffentlichen, die [Razor](xref:mvc/views/razor) -Dateien für ASP.net Core MVC-basierte Projekte oder [blazor](xref:blazor/index) -Projekte enthalten.</span><span class="sxs-lookup"><span data-stu-id="64ad6-110">is required to build, package, and publish projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based projects, or [Blazor](xref:blazor/index) projects</span></span>
* <span data-ttu-id="64ad6-111">Enthält einen Satz vordefinierter Ziele, Eigenschaften und Elemente, die das Anpassen der Kompilierung von Razor-Dateien (cshtml-oder Razor-Dateien) ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="64ad6-111">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor (.cshtml or .razor) files.</span></span>
::: moniker-end


::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="64ad6-112">Das Razor SDK enthält ein `<Content>` -Element mit `Include` einem-Attribut, `**\*.cshtml` das auf das globmuster festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="64ad6-112">The Razor SDK includes a `<Content>` element with an `Include` attribute set to the `**\*.cshtml` globbing pattern.</span></span> <span data-ttu-id="64ad6-113">Übereinstimmende Dateien werden veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="64ad6-113">Matching files are published.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="64ad6-114">Das Razor SDK umfasst `<Content>` Elemente mit `Include` Attributen, die auf `**\*.cshtml` die `**\*.razor` globmuster und festgelegt sind.</span><span class="sxs-lookup"><span data-stu-id="64ad6-114">The Razor SDK includes `<Content>` elements with `Include` attributes set to the `**\*.cshtml` and `**\*.razor` globbing patterns.</span></span> <span data-ttu-id="64ad6-115">Übereinstimmende Dateien werden veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="64ad6-115">Matching files are published.</span></span>

::: moniker-end

## <a name="prerequisites"></a><span data-ttu-id="64ad6-116">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="64ad6-116">Prerequisites</span></span>

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a><span data-ttu-id="64ad6-117">Verwenden des Razor SDK</span><span class="sxs-lookup"><span data-stu-id="64ad6-117">Use the Razor SDK</span></span>

<span data-ttu-id="64ad6-118">Die meisten Web-apps müssen nicht explizit das Razor-SDK zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="64ad6-118">Most web apps aren't required to explicitly reference the Razor SDK.</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"
<span data-ttu-id="64ad6-119">So verwenden Sie das Razor SDK zum Erstellen von Klassenbibliotheken, die Razor-Ansichten oder Razor-Seiten enthalten:</span><span class="sxs-lookup"><span data-stu-id="64ad6-119">To use the Razor SDK to build class libraries containing Razor views or Razor Pages:</span></span>

* <span data-ttu-id="64ad6-120">Verwenden Sie `Microsoft.NET.Sdk.Razor` anstelle von `Microsoft.NET.Sdk`:</span><span class="sxs-lookup"><span data-stu-id="64ad6-120">Use `Microsoft.NET.Sdk.Razor` instead of `Microsoft.NET.Sdk`:</span></span>

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* <span data-ttu-id="64ad6-121">In der Regel einen Paketverweis auf `Microsoft.AspNetCore.Mvc` ist erforderlich, um zusätzliche Abhängigkeiten zu erhalten, die zum Erstellen und Kompilieren von Razor Pages und Razor-Ansichten erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="64ad6-121">Typically, a package reference to `Microsoft.AspNetCore.Mvc` is required to receive additional dependencies that are required to build and compile Razor Pages and Razor views.</span></span> <span data-ttu-id="64ad6-122">Zumindest sollten Ihr Projekt Paketverweise zum Hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="64ad6-122">At a minimum, your project should add package references to:</span></span>

  * `Microsoft.AspNetCore.Razor.Design` 
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`
    
  <span data-ttu-id="64ad6-123">Die `Microsoft.AspNetCore.Razor.Design` Paket stellt die Razor-Kompilierung-Aufgaben und-Ziele für das Projekt bereit.</span><span class="sxs-lookup"><span data-stu-id="64ad6-123">The `Microsoft.AspNetCore.Razor.Design` package provides the Razor compilation tasks and targets for the project.</span></span>

  <span data-ttu-id="64ad6-124">Diese Pakete sind in `Microsoft.AspNetCore.Mvc` enthalten.</span><span class="sxs-lookup"><span data-stu-id="64ad6-124">The preceding packages are included in `Microsoft.AspNetCore.Mvc`.</span></span> <span data-ttu-id="64ad6-125">Das folgende Markup zeigt eine Projektdatei, die die Razor-SDK verwendet, um die Razor-Dateien für eine ASP.NET Core Razor Pages-app zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="64ad6-125">The following markup shows a project file that uses the Razor SDK to build Razor files for an ASP.NET Core Razor Pages app:</span></span>
    
  [!code-xml[](sdk/sample/RazorSDK.csproj)]
  
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="64ad6-126">Um das Razor SDK zum Erstellen von Klassenbibliotheken mit Razor-Ansichten oder Razor Pages zu verwenden, wird empfohlen, mit der Projektvorlage für die Razor-Klassenbibliothek zu beginnen.</span><span class="sxs-lookup"><span data-stu-id="64ad6-126">To use the Razor SDK to build class libraries containing Razor views or Razor Pages we recommend starting with the Razor Class Library project template.</span></span> <span data-ttu-id="64ad6-127">Eine Razor-Klassenbibliothek, die zum Erstellen von blazor-Dateien (Razor-Dateien) verwendet wird, erfordert mindestens `Microsoft.AspNetCore.Components` einen Verweis auf das Paket.</span><span class="sxs-lookup"><span data-stu-id="64ad6-127">A Razor class library that is used to build Blazor (.razor) files will at minimum require a reference to the `Microsoft.AspNetCore.Components` package.</span></span> <span data-ttu-id="64ad6-128">Eine Razor-Klassenbibliothek, die verwendet wird, um Razor-Sichten oder-Seiten (cshtml-Dateien) `netcoreapp3.0` zu erstellen, erfordert die `FrameworkReference` Ziel `Microsoft.AspNetCore.App`Plattform oder höher und eine auf.</span><span class="sxs-lookup"><span data-stu-id="64ad6-128">A Razor class library that is used to build Razor views or pages (.cshtml files), will require targeting `netcoreapp3.0` or newer and have a `FrameworkReference` to `Microsoft.AspNetCore.App`.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> <span data-ttu-id="64ad6-129">Die `Microsoft.AspNetCore.Razor.Design` und `Microsoft.AspNetCore.Mvc.Razor.Extensions` Pakete befinden sich der [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="64ad6-129">The `Microsoft.AspNetCore.Razor.Design` and `Microsoft.AspNetCore.Mvc.Razor.Extensions` packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="64ad6-130">Allerdings das Version-kleiner `Microsoft.AspNetCore.App` Paketverweis bietet ein metapaket an die app, die die neueste Version von keine `Microsoft.AspNetCore.Razor.Design`.</span><span class="sxs-lookup"><span data-stu-id="64ad6-130">However, the version-less `Microsoft.AspNetCore.App` package reference provides a metapackage to the app that doesn't include the latest version of `Microsoft.AspNetCore.Razor.Design`.</span></span> <span data-ttu-id="64ad6-131">Projekte müssen eine einheitliche Version des verweisen `Microsoft.AspNetCore.Razor.Design` (oder `Microsoft.AspNetCore.Mvc`) so, dass die neueste Fixes der Buildzeit für Razor enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="64ad6-131">Projects must reference a consistent version of `Microsoft.AspNetCore.Razor.Design` (or `Microsoft.AspNetCore.Mvc`) so that the latest build-time fixes for Razor are included.</span></span> <span data-ttu-id="64ad6-132">Weitere Informationen finden Sie unter [GitHub-Problem](https://github.com/aspnet/Razor/issues/2553).</span><span class="sxs-lookup"><span data-stu-id="64ad6-132">For more information, see [this GitHub issue](https://github.com/aspnet/Razor/issues/2553).</span></span>

::: moniker-end

### <a name="properties"></a><span data-ttu-id="64ad6-133">Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="64ad6-133">Properties</span></span>

<span data-ttu-id="64ad6-134">Die folgenden Eigenschaften steuern das Verhalten des Razor SDK im Rahmen eines Projektbuilds:</span><span class="sxs-lookup"><span data-stu-id="64ad6-134">The following properties control the Razor's SDK behavior as part of a project build:</span></span>

* <span data-ttu-id="64ad6-135">`RazorCompileOnBuild` &ndash; Wenn `true`, kompiliert, und gibt die Razor-Assembly im Rahmen der Erstellung des Projekts.</span><span class="sxs-lookup"><span data-stu-id="64ad6-135">`RazorCompileOnBuild` &ndash; When `true`, compiles and emits the Razor assembly as part of building the project.</span></span> <span data-ttu-id="64ad6-136">Wird standardmäßig auf `true` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="64ad6-136">Defaults to `true`.</span></span>
* <span data-ttu-id="64ad6-137">`RazorCompileOnPublish` &ndash; Wenn `true`, kompiliert, und gibt die Razor-Assembly als Teil der Veröffentlichung des Projekts.</span><span class="sxs-lookup"><span data-stu-id="64ad6-137">`RazorCompileOnPublish` &ndash; When `true`, compiles and emits the Razor assembly as part of publishing the project.</span></span> <span data-ttu-id="64ad6-138">Wird standardmäßig auf `true` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="64ad6-138">Defaults to `true`.</span></span>

<span data-ttu-id="64ad6-139">Die Eigenschaften und Elemente in der folgenden Tabelle werden zum Konfigurieren von ein- und Ausgabe an die Razor-SDK verwendet.</span><span class="sxs-lookup"><span data-stu-id="64ad6-139">The properties and items in the following table are used to configure inputs and output to the Razor SDK.</span></span>

::: moniker range=">= aspnetcore-3.0"
> [!WARNING]
<span data-ttu-id="64ad6-140">Ab ASP.net Core 3,0 werden MVC-Sichten oder-Razor Pages nicht standardmäßig bedient, wenn `RazorCompileOnBuild` oder `RazorCompileOnPublish` deaktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="64ad6-140">Starting with ASP.NET Core 3.0, MVC Views or Razor Pages will not be served by default if `RazorCompileOnBuild` or `RazorCompileOnPublish` is disabled.</span></span> <span data-ttu-id="64ad6-141">Anwendungen müssen einen expliziten Verweis auf `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` das-Paket hinzufügen, um die Unterstützung für die Lauf Zeit Kompilierung hinzuzufügen, wenn Sie zur Verarbeitung von cshtml-Dateien auf</span><span class="sxs-lookup"><span data-stu-id="64ad6-141">Applications must add an explicit reference to `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` package to add support for runtime compilation if they rely on runtime compilation to process cshtml files.</span></span>
::: moniker-end


| <span data-ttu-id="64ad6-142">Elemente</span><span class="sxs-lookup"><span data-stu-id="64ad6-142">Items</span></span> | <span data-ttu-id="64ad6-143">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="64ad6-143">Description</span></span> |
| ----- | ----------- |
| `RazorGenerate` | <span data-ttu-id="64ad6-144">Element Elemente (*cshtml* -Dateien), die Eingaben für die Codegenerierung sind.</span><span class="sxs-lookup"><span data-stu-id="64ad6-144">Item elements (*.cshtml* files) that are inputs to code generation.</span></span> |
| `RazorComponent` | <span data-ttu-id="64ad6-145">Element Elemente (*Razor* -Dateien), die Eingaben für die Generierung von Komponenten Code sind.</span><span class="sxs-lookup"><span data-stu-id="64ad6-145">Item elements (*.razor* files) that are inputs to Component code generation.</span></span>
| `RazorCompile` | <span data-ttu-id="64ad6-146">Element Elemente (*CS* -Dateien), die Eingaben für Razor-Kompilierungs Ziele sind.</span><span class="sxs-lookup"><span data-stu-id="64ad6-146">Item elements (*.cs* files) that are inputs to Razor compilation targets.</span></span> <span data-ttu-id="64ad6-147">Verwenden Sie `ItemGroup` diese Angabe, um zusätzliche Dateien anzugeben, die in die Razor-Assembly kompiliert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="64ad6-147">Use this `ItemGroup` to specify additional files to be compiled into the Razor assembly.</span></span> |
| `RazorTargetAssemblyAttribute` | <span data-ttu-id="64ad6-148">Item-Elemente, die für das Codieren von Generate-Attributen für die Razor-Assembly verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="64ad6-148">Item elements used to code generate attributes for the Razor assembly.</span></span> <span data-ttu-id="64ad6-149">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="64ad6-149">For example:</span></span>  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | <span data-ttu-id="64ad6-150">Elemente, die als eingebettete Ressourcen für die generierte Razor-Assembly hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="64ad6-150">Item elements added as embedded resources to the generated Razor assembly.</span></span> |

| <span data-ttu-id="64ad6-151">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="64ad6-151">Property</span></span> | <span data-ttu-id="64ad6-152">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="64ad6-152">Description</span></span> |
| -------- | ----------- |
| `RazorTargetName` | <span data-ttu-id="64ad6-153">Dateiname (ohne Erweiterung) der Assembly, die von Razor erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="64ad6-153">File name (without extension) of the assembly produced by Razor.</span></span> | 
| `RazorOutputPath` | <span data-ttu-id="64ad6-154">Das Razor-Ausgabeverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="64ad6-154">The Razor output directory.</span></span> |
| `RazorCompileToolset` | <span data-ttu-id="64ad6-155">Wird verwendet, um das Toolset für die Erstellung der Razor-Assembly zu bestimmen.</span><span class="sxs-lookup"><span data-stu-id="64ad6-155">Used to determine the toolset used to build the Razor assembly.</span></span> <span data-ttu-id="64ad6-156">Gültige Werte sind `Implicit`, `RazorSDK` und `PrecompilationTool`.</span><span class="sxs-lookup"><span data-stu-id="64ad6-156">Valid values are `Implicit`, `RazorSDK`, and `PrecompilationTool`.</span></span> |
| [<span data-ttu-id="64ad6-157">EnableDefaultContentItems</span><span class="sxs-lookup"><span data-stu-id="64ad6-157">EnableDefaultContentItems</span></span>](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | <span data-ttu-id="64ad6-158">Der Standardwert ist `true`.</span><span class="sxs-lookup"><span data-stu-id="64ad6-158">Default is `true`.</span></span> <span data-ttu-id="64ad6-159">Wenn `true`, werden die Dateien *Web. config*, *. JSON*und *. cshtml* als Inhalt in das Projekt eingeschlossen.</span><span class="sxs-lookup"><span data-stu-id="64ad6-159">When `true`, includes *web.config*, *.json*, and *.cshtml* files as content in the project.</span></span> <span data-ttu-id="64ad6-160">Wenn auf die verwiesen wird über `Microsoft.NET.Sdk.Web`, Dateien unter *"Wwwroot"* und Konfigurationsdateien sind ebenfalls enthalten.</span><span class="sxs-lookup"><span data-stu-id="64ad6-160">When referenced via `Microsoft.NET.Sdk.Web`, files under *wwwroot* and config files are also included.</span></span> |
| `EnableDefaultRazorGenerateItems` | <span data-ttu-id="64ad6-161">Enthält *CSHTML*-Dateien aus `Content`-Elementen in `RazorGenerate`-Elementen, wenn `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="64ad6-161">When `true`, includes *.cshtml* files from `Content` items in `RazorGenerate` items.</span></span> |
| `GenerateRazorTargetAssemblyInfo` | <span data-ttu-id="64ad6-162">Wenn `true`, generiert eine *cs* -Datei mit der Attribute durch `RazorAssemblyAttribute` und die Datei in der Kompilierungsausgabe.</span><span class="sxs-lookup"><span data-stu-id="64ad6-162">When `true`, generates a *.cs* file containing attributes specified by `RazorAssemblyAttribute` and includes the file in the compile output.</span></span> |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | <span data-ttu-id="64ad6-163">Fügt einen Standardsatz von Assembly-Attributen zu `RazorAssemblyAttribute` hinzu, wenn `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="64ad6-163">When `true`, adds a default set of assembly attributes to `RazorAssemblyAttribute`.</span></span> |
| `CopyRazorGenerateFilesToPublishDirectory` | <span data-ttu-id="64ad6-164">Wenn `true`, Kopien `RazorGenerate` Elemente ( *.cshtml*) Dateien in das Veröffentlichungsverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="64ad6-164">When `true`, copies `RazorGenerate` items (*.cshtml*) files to the publish directory.</span></span> <span data-ttu-id="64ad6-165">Razor-Dateien nicht in der Regel für eine veröffentlichte app erforderlich, wenn sie an der Kompilierung zur Buildzeit oder veröffentlichen-Laufzeit beteiligt.</span><span class="sxs-lookup"><span data-stu-id="64ad6-165">Typically, Razor files aren't required for a published app if they participate in compilation at build-time or publish-time.</span></span> <span data-ttu-id="64ad6-166">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="64ad6-166">Defaults to `false`.</span></span> |
| `CopyRefAssembliesToPublishDirectory` | <span data-ttu-id="64ad6-167">Kopiert Referenzassembly-Elemente in das Veröffentlichungsverzeichnis, wenn `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="64ad6-167">When `true`, copy reference assembly items to the publish directory.</span></span> <span data-ttu-id="64ad6-168">In der Regel sind nicht referenzierten Assemblys für eine veröffentlichte app erforderlich, wenn Razor-Kompilierung zur Buildzeit oder veröffentlichen-Laufzeit auftritt.</span><span class="sxs-lookup"><span data-stu-id="64ad6-168">Typically, reference assemblies aren't required for a published app if Razor compilation occurs at build-time or publish-time.</span></span> <span data-ttu-id="64ad6-169">Legen Sie auf `true` Ihre veröffentlichte app-Runtime-Kompilierung benötigt.</span><span class="sxs-lookup"><span data-stu-id="64ad6-169">Set to `true` if your published app requires runtime compilation.</span></span> <span data-ttu-id="64ad6-170">Legen Sie den Wert beispielsweise auf `true` bei die app geändert *.cshtml* Dateien zur Laufzeit oder eingebettete Ansichten verwendet.</span><span class="sxs-lookup"><span data-stu-id="64ad6-170">For example, set the value to `true` if the app modifies *.cshtml* files at runtime or uses embedded views.</span></span> <span data-ttu-id="64ad6-171">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="64ad6-171">Defaults to `false`.</span></span> |
| `IncludeRazorContentInPack` | <span data-ttu-id="64ad6-172">Wenn `true`, alle Razor-Inhaltselemente ( *.cshtml* Dateien) für die Aufnahme in das generierte NuGet-Paket gekennzeichnet sind.</span><span class="sxs-lookup"><span data-stu-id="64ad6-172">When `true`, all Razor content items (*.cshtml* files) are marked for inclusion in the generated NuGet package.</span></span> <span data-ttu-id="64ad6-173">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="64ad6-173">Defaults to `false`.</span></span> |
| `EmbedRazorGenerateSources` | <span data-ttu-id="64ad6-174">Fügt RazorGenerate-Elemente (*CSHTML*-Dateien) als eingebettete Dateien in die generierte Razor-Assembly hinzu, wenn `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="64ad6-174">When `true`, adds RazorGenerate (*.cshtml*) items as embedded files to the generated Razor assembly.</span></span> <span data-ttu-id="64ad6-175">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="64ad6-175">Defaults to `false`.</span></span> |
| `UseRazorBuildServer` | <span data-ttu-id="64ad6-176">Verwendet einen dauerhaften Buildserverprozess, um die Auslastung durch die Codegenerierung zu verlagern, wenn `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="64ad6-176">When `true`, uses a persistent build server process to offload code generation work.</span></span> <span data-ttu-id="64ad6-177">Wird standardmäßig auf den Wert `UseSharedCompilation` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="64ad6-177">Defaults to the value of `UseSharedCompilation`.</span></span> |
| `GenerateMvcApplicationPartsAssemblyAttributes` | <span data-ttu-id="64ad6-178">Wenn `true`, generiert das SDK zusätzliche Attribute, die von MVC zur Laufzeit verwendet werden, um die Ermittlung von Anwendungs Teilen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="64ad6-178">When `true`, the SDK generates additional attributes used by MVC at runtime to perform application part discovery.</span></span> |

<span data-ttu-id="64ad6-179">Weitere Informationen zu Eigenschaften finden Sie unter [MSBuild-Eigenschaften](/visualstudio/msbuild/msbuild-properties).</span><span class="sxs-lookup"><span data-stu-id="64ad6-179">For more information on properties, see [MSBuild properties](/visualstudio/msbuild/msbuild-properties).</span></span>

### <a name="targets"></a><span data-ttu-id="64ad6-180">Ziele</span><span class="sxs-lookup"><span data-stu-id="64ad6-180">Targets</span></span>

<span data-ttu-id="64ad6-181">Das Razor SDK definiert zwei Hauptziele:</span><span class="sxs-lookup"><span data-stu-id="64ad6-181">The Razor SDK defines two primary targets:</span></span>

* <span data-ttu-id="64ad6-182">`RazorGenerate` &ndash; Generiert Code *cs* Dateien von `RazorGenerate` item-Elemente.</span><span class="sxs-lookup"><span data-stu-id="64ad6-182">`RazorGenerate` &ndash; Code generates *.cs* files from `RazorGenerate` item elements.</span></span> <span data-ttu-id="64ad6-183">Verwenden Sie die Eigenschaft `RazorGenerateDependsOn`, um zusätzliche Ziele anzugeben, die vor oder nach diesem Ziel ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="64ad6-183">Use `RazorGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="64ad6-184">`RazorCompile` &ndash; Kompiliert generiert *cs* Dateien im zu einer Razor-Assembly.</span><span class="sxs-lookup"><span data-stu-id="64ad6-184">`RazorCompile` &ndash; Compiles generated *.cs* files in to a Razor assembly.</span></span> <span data-ttu-id="64ad6-185">Verwenden Sie `RazorCompileDependsOn`, um zusätzliche Ziele anzugeben, die vor oder nach diesem Ziel ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="64ad6-185">Use `RazorCompileDependsOn` to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="64ad6-186">`RazorComponentGenerate`Code generiert *CS* -Dateien für `RazorComponent` Element Elemente. &ndash;</span><span class="sxs-lookup"><span data-stu-id="64ad6-186">`RazorComponentGenerate` &ndash; Code generates *.cs* files for `RazorComponent` item elements.</span></span> <span data-ttu-id="64ad6-187">Verwenden Sie die Eigenschaft `RazorComponentGenerateDependsOn`, um zusätzliche Ziele anzugeben, die vor oder nach diesem Ziel ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="64ad6-187">Use `RazorComponentGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>

### <a name="runtime-compilation-of-razor-views"></a><span data-ttu-id="64ad6-188">Kompilierung von Razor-Ansichten zur Laufzeit</span><span class="sxs-lookup"><span data-stu-id="64ad6-188">Runtime compilation of Razor views</span></span>

* <span data-ttu-id="64ad6-189">Standardmäßig veröffentlicht das Razor SDK keine Verweisassemblys, die für das Durchführen der Laufzeitkompilierung erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="64ad6-189">By default, the Razor SDK doesn't publish reference assemblies that are required to perform runtime compilation.</span></span> <span data-ttu-id="64ad6-190">Dadurch kommt es zu Kompilierungsfehlern, wenn das Anwendungsmodell von der Laufzeitkompilierung abhängt (z.B. wenn die App eingebettete Ansichten verwendet oder Ansichten nach dem Veröffentlichen der App verändert werden).</span><span class="sxs-lookup"><span data-stu-id="64ad6-190">This results in compilation failures when the application model relies on runtime compilation&mdash;for example, the app uses embedded views or changes views after the app is published.</span></span> <span data-ttu-id="64ad6-191">Legen Sie `CopyRefAssembliesToPublishDirectory` auf `true` fest, um mit dem Veröffentlichen von Verweisassemblys fortzufahren.</span><span class="sxs-lookup"><span data-stu-id="64ad6-191">Set `CopyRefAssembliesToPublishDirectory` to `true` to continue publishing reference assemblies.</span></span>

* <span data-ttu-id="64ad6-192">Für eine Web-app, stellen Sie sicher auf die Ihre app die `Microsoft.NET.Sdk.Web` SDK.</span><span class="sxs-lookup"><span data-stu-id="64ad6-192">For a web app, ensure your app is targeting the `Microsoft.NET.Sdk.Web` SDK.</span></span>

## <a name="razor-language-version"></a><span data-ttu-id="64ad6-193">Razor-Sprachversion</span><span class="sxs-lookup"><span data-stu-id="64ad6-193">Razor language version</span></span>

<span data-ttu-id="64ad6-194">Wenn das SDK als Zielversion verwendet wird, wird die Razor-Sprachversion von der Ziel Framework-Version der APP abgeleitet. `Microsoft.NET.Sdk.Web`</span><span class="sxs-lookup"><span data-stu-id="64ad6-194">When targeting the `Microsoft.NET.Sdk.Web` SDK, the Razor language version is inferred from the the app's target framework version.</span></span> <span data-ttu-id="64ad6-195">Für Projekte, die `Microsoft.NET.Sdk.Razor` auf das SDK abzielen, oder in seltenen Fällen, in denen die APP eine andere Version der Razor-Sprache erfordert als der abherzustehende Wert `<RazorLangVersion>` , kann eine Version konfiguriert werden, indem die-Eigenschaft in der Projektdatei der APP festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="64ad6-195">For projects targeting the `Microsoft.NET.Sdk.Razor` SDK or in the rare case that the app requires a different Razor language version than the inferred value, a version can be configured by setting the `<RazorLangVersion>` property in the app's project file:</span></span>

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

<span data-ttu-id="64ad6-196">Die Sprachversion von Razor ist eng in die Version der Laufzeit integriert, für die Sie erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="64ad6-196">Razor's language version is tightly integrated with the version of the runtime that it was built for.</span></span> <span data-ttu-id="64ad6-197">Das Ziel einer Sprachversion, die nicht für die Laufzeit konzipiert ist, wird nicht unterstützt und erzeugt wahrscheinlich Buildfehler.</span><span class="sxs-lookup"><span data-stu-id="64ad6-197">Targeting a language version that is not designed for the runtime is unsupported, and will likely produce build errors.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="64ad6-198">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="64ad6-198">Additional resources</span></span>

* [<span data-ttu-id="64ad6-199">Erweiterungen des CSPROJ-Formats für .NET Core</span><span class="sxs-lookup"><span data-stu-id="64ad6-199">Additions to the csproj format for .NET Core</span></span>](/dotnet/core/tools/csproj)
* [<span data-ttu-id="64ad6-200">Gemeinsame MSBuild-Projektelemente</span><span class="sxs-lookup"><span data-stu-id="64ad6-200">Common MSBuild project items</span></span>](/visualstudio/msbuild/common-msbuild-project-items)

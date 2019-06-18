---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: Erfahren Sie, wie Razor Pages in ASP.NET Core codierungsseitige Szenarios einfacher und produktiver gestalten als MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/18/2019
uid: razor-pages/sdk
ms.openlocfilehash: fa69e4840377e0c1c8291c7ba9305a27bd3e6b82
ms.sourcegitcommit: 516f166c5f7cec54edf3d9c71e6e2ba53fb3b0e5
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67196363"
---
# <a name="aspnet-core-razor-sdk"></a><span data-ttu-id="008f0-103">ASP.NET Core Razor SDK</span><span class="sxs-lookup"><span data-stu-id="008f0-103">ASP.NET Core Razor SDK</span></span>

<span data-ttu-id="008f0-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="008f0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="overview"></a><span data-ttu-id="008f0-105">Übersicht</span><span class="sxs-lookup"><span data-stu-id="008f0-105">Overview</span></span>

<span data-ttu-id="008f0-106">Die [!INCLUDE[](~/includes/2.1-SDK.md)] enthält die `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor-SDK).</span><span class="sxs-lookup"><span data-stu-id="008f0-106">The [!INCLUDE[](~/includes/2.1-SDK.md)] includes the `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK).</span></span> <span data-ttu-id="008f0-107">Das Razor SDK:</span><span class="sxs-lookup"><span data-stu-id="008f0-107">The Razor SDK:</span></span>

* <span data-ttu-id="008f0-108">Standardisiert die Vorgänge zum Erstellen, Verpacken und Veröffentlichen von Projekten, die [Razor](xref:mvc/views/razor)-Dateien für auf ASP.NET Core MVC basierende Projekte enthalten.</span><span class="sxs-lookup"><span data-stu-id="008f0-108">Standardizes the experience around building, packaging, and publishing projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based projects.</span></span>
* <span data-ttu-id="008f0-109">Enthält eine vordefinierte Reihe von Zielen, Eigenschaften und Elementen, die das Anpassen der Razor-Dateien ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="008f0-109">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor files.</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="008f0-110">Das Razor-SDK enthält eine `<Content>` -Element mit einer `Include` -Attributsatz auf die `**\*.cshtml` Platzhaltermuster.</span><span class="sxs-lookup"><span data-stu-id="008f0-110">The Razor SDK includes a `<Content>` element with an `Include` attribute set to the `**\*.cshtml` globbing pattern.</span></span> <span data-ttu-id="008f0-111">Übereinstimmende Dateien werden veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="008f0-111">Matching files are published.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="008f0-112">Das Razor-SDK enthält `<Content>` Elemente mit `Include` Attribute festgelegt werden, um die `**\*.cshtml` und `**\*.razor` Platzhaltermuster.</span><span class="sxs-lookup"><span data-stu-id="008f0-112">The Razor SDK includes `<Content>` elements with `Include` attributes set to the `**\*.cshtml` and `**\*.razor` globbing patterns.</span></span> <span data-ttu-id="008f0-113">Übereinstimmende Dateien werden veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="008f0-113">Matching files are published.</span></span>

::: moniker-end

## <a name="prerequisites"></a><span data-ttu-id="008f0-114">Vorraussetzungen</span><span class="sxs-lookup"><span data-stu-id="008f0-114">Prerequisites</span></span>

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a><span data-ttu-id="008f0-115">Verwenden Sie die Razor-SDK</span><span class="sxs-lookup"><span data-stu-id="008f0-115">Use the Razor SDK</span></span>

<span data-ttu-id="008f0-116">Die meisten Web-apps müssen nicht explizit das Razor-SDK zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="008f0-116">Most web apps aren't required to explicitly reference the Razor SDK.</span></span>

<span data-ttu-id="008f0-117">So verwenden Sie das Razor SDK zum Erstellen von Klassenbibliotheken, die Razor-Ansichten oder Razor-Seiten enthalten:</span><span class="sxs-lookup"><span data-stu-id="008f0-117">To use the Razor SDK to build class libraries containing Razor views or Razor Pages:</span></span>

* <span data-ttu-id="008f0-118">Verwenden Sie `Microsoft.NET.Sdk.Razor` anstelle von `Microsoft.NET.Sdk`:</span><span class="sxs-lookup"><span data-stu-id="008f0-118">Use `Microsoft.NET.Sdk.Razor` instead of `Microsoft.NET.Sdk`:</span></span>

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* <span data-ttu-id="008f0-119">In der Regel einen Paketverweis auf `Microsoft.AspNetCore.Mvc` ist erforderlich, um zusätzliche Abhängigkeiten zu erhalten, die zum Erstellen und Kompilieren von Razor Pages und Razor-Ansichten erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="008f0-119">Typically, a package reference to `Microsoft.AspNetCore.Mvc` is required to receive additional dependencies that are required to build and compile Razor Pages and Razor views.</span></span> <span data-ttu-id="008f0-120">Zumindest sollten Ihr Projekt Paketverweise zum Hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="008f0-120">At a minimum, your project should add package references to:</span></span>

  * `Microsoft.AspNetCore.Razor.Design` 
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`
    
  <span data-ttu-id="008f0-121">Die `Microsoft.AspNetCore.Razor.Design` Paket stellt die Razor-Kompilierung-Aufgaben und-Ziele für das Projekt bereit.</span><span class="sxs-lookup"><span data-stu-id="008f0-121">The `Microsoft.AspNetCore.Razor.Design` package provides the Razor compilation tasks and targets for the project.</span></span>

  <span data-ttu-id="008f0-122">Diese Pakete sind in `Microsoft.AspNetCore.Mvc` enthalten.</span><span class="sxs-lookup"><span data-stu-id="008f0-122">The preceding packages are included in `Microsoft.AspNetCore.Mvc`.</span></span> <span data-ttu-id="008f0-123">Das folgende Markup zeigt eine Projektdatei, die die Razor-SDK verwendet, um die Razor-Dateien für eine ASP.NET Core Razor Pages-app zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="008f0-123">The following markup shows a project file that uses the Razor SDK to build Razor files for an ASP.NET Core Razor Pages app:</span></span>
    
  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> <span data-ttu-id="008f0-124">Die `Microsoft.AspNetCore.Razor.Design` und `Microsoft.AspNetCore.Mvc.Razor.Extensions` Pakete befinden sich der [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="008f0-124">The `Microsoft.AspNetCore.Razor.Design` and `Microsoft.AspNetCore.Mvc.Razor.Extensions` packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="008f0-125">Allerdings das Version-kleiner `Microsoft.AspNetCore.App` Paketverweis bietet ein metapaket an die app, die die neueste Version von keine `Microsoft.AspNetCore.Razor.Design`.</span><span class="sxs-lookup"><span data-stu-id="008f0-125">However, the version-less `Microsoft.AspNetCore.App` package reference provides a metapackage to the app that doesn't include the latest version of `Microsoft.AspNetCore.Razor.Design`.</span></span> <span data-ttu-id="008f0-126">Projekte müssen eine einheitliche Version des verweisen `Microsoft.AspNetCore.Razor.Design` (oder `Microsoft.AspNetCore.Mvc`) so, dass die neueste Fixes der Buildzeit für Razor enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="008f0-126">Projects must reference a consistent version of `Microsoft.AspNetCore.Razor.Design` (or `Microsoft.AspNetCore.Mvc`) so that the latest build-time fixes for Razor are included.</span></span> <span data-ttu-id="008f0-127">Weitere Informationen finden Sie unter [GitHub-Problem](https://github.com/aspnet/Razor/issues/2553).</span><span class="sxs-lookup"><span data-stu-id="008f0-127">For more information, see [this GitHub issue](https://github.com/aspnet/Razor/issues/2553).</span></span>

::: moniker-end

### <a name="properties"></a><span data-ttu-id="008f0-128">Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="008f0-128">Properties</span></span>

<span data-ttu-id="008f0-129">Die folgenden Eigenschaften steuern das Verhalten des Razor SDK im Rahmen eines Projektbuilds:</span><span class="sxs-lookup"><span data-stu-id="008f0-129">The following properties control the Razor's SDK behavior as part of a project build:</span></span>

* <span data-ttu-id="008f0-130">`RazorCompileOnBuild` &ndash; Wenn `true`, kompiliert, und gibt die Razor-Assembly im Rahmen der Erstellung des Projekts.</span><span class="sxs-lookup"><span data-stu-id="008f0-130">`RazorCompileOnBuild` &ndash; When `true`, compiles and emits the Razor assembly as part of building the project.</span></span> <span data-ttu-id="008f0-131">Wird standardmäßig auf `true` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="008f0-131">Defaults to `true`.</span></span>
* <span data-ttu-id="008f0-132">`RazorCompileOnPublish` &ndash; Wenn `true`, kompiliert, und gibt die Razor-Assembly als Teil der Veröffentlichung des Projekts.</span><span class="sxs-lookup"><span data-stu-id="008f0-132">`RazorCompileOnPublish` &ndash; When `true`, compiles and emits the Razor assembly as part of publishing the project.</span></span> <span data-ttu-id="008f0-133">Wird standardmäßig auf `true` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="008f0-133">Defaults to `true`.</span></span>

<span data-ttu-id="008f0-134">Die Eigenschaften und Elemente in der folgenden Tabelle werden zum Konfigurieren von ein- und Ausgabe an die Razor-SDK verwendet.</span><span class="sxs-lookup"><span data-stu-id="008f0-134">The properties and items in the following table are used to configure inputs and output to the Razor SDK.</span></span>

| <span data-ttu-id="008f0-135">Elemente</span><span class="sxs-lookup"><span data-stu-id="008f0-135">Items</span></span> | <span data-ttu-id="008f0-136">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="008f0-136">Description</span></span> |
| ----- | ----------- |
| `RazorGenerate` | <span data-ttu-id="008f0-137">Item-Elemente (*CSHTML*-Dateien), die Eingaben für Codegenerierungsziele sind.</span><span class="sxs-lookup"><span data-stu-id="008f0-137">Item elements (*.cshtml* files) that are inputs to code generation targets.</span></span> |
| `RazorCompile` | <span data-ttu-id="008f0-138">Die Item-Elemente (*cs* Dateien), die Eingaben für Razor-kompilierungsziele sind.</span><span class="sxs-lookup"><span data-stu-id="008f0-138">Item elements (*.cs* files) that are inputs to Razor compilation targets.</span></span> <span data-ttu-id="008f0-139">Verwenden Sie diese Option `ItemGroup` zusätzliche Dateien angeben, in der Razor-Assembly kompiliert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="008f0-139">Use this `ItemGroup` to specify additional files to be compiled into the Razor assembly.</span></span> |
| `RazorTargetAssemblyAttribute` | <span data-ttu-id="008f0-140">Item-Elemente, die für das Codieren von Generate-Attributen für die Razor-Assembly verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="008f0-140">Item elements used to code generate attributes for the Razor assembly.</span></span> <span data-ttu-id="008f0-141">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="008f0-141">For example:</span></span>  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | <span data-ttu-id="008f0-142">Elemente, die als eingebettete Ressourcen für die generierte Razor-Assembly hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="008f0-142">Item elements added as embedded resources to the generated Razor assembly.</span></span> |

| <span data-ttu-id="008f0-143">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="008f0-143">Property</span></span> | <span data-ttu-id="008f0-144">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="008f0-144">Description</span></span> |
| -------- | ----------- |
| `RazorTargetName` | <span data-ttu-id="008f0-145">Dateiname (ohne Erweiterung) der Assembly, die von Razor erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="008f0-145">File name (without extension) of the assembly produced by Razor.</span></span> | 
| `RazorOutputPath` | <span data-ttu-id="008f0-146">Das Razor-Ausgabeverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="008f0-146">The Razor output directory.</span></span> |
| `RazorCompileToolset` | <span data-ttu-id="008f0-147">Wird verwendet, um das Toolset für die Erstellung der Razor-Assembly zu bestimmen.</span><span class="sxs-lookup"><span data-stu-id="008f0-147">Used to determine the toolset used to build the Razor assembly.</span></span> <span data-ttu-id="008f0-148">Gültige Werte sind `Implicit`, `RazorSDK` und `PrecompilationTool`.</span><span class="sxs-lookup"><span data-stu-id="008f0-148">Valid values are `Implicit`, `RazorSDK`, and `PrecompilationTool`.</span></span> |
| [<span data-ttu-id="008f0-149">EnableDefaultContentItems</span><span class="sxs-lookup"><span data-stu-id="008f0-149">EnableDefaultContentItems</span></span>](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | <span data-ttu-id="008f0-150">Der Standardwert ist `true`.</span><span class="sxs-lookup"><span data-stu-id="008f0-150">Default is `true`.</span></span> <span data-ttu-id="008f0-151">Wenn `true`, umfasst *"Web.config"* , *JSON*, und *.cshtml* -Dateien als Inhalt in das Projekt.</span><span class="sxs-lookup"><span data-stu-id="008f0-151">When `true`, includes *web.config*, *.json*, and *.cshtml* files as content in the project.</span></span> <span data-ttu-id="008f0-152">Wenn auf die verwiesen wird über `Microsoft.NET.Sdk.Web`, Dateien unter *"Wwwroot"* und Konfigurationsdateien sind ebenfalls enthalten.</span><span class="sxs-lookup"><span data-stu-id="008f0-152">When referenced via `Microsoft.NET.Sdk.Web`, files under *wwwroot* and config files are also included.</span></span> |
| `EnableDefaultRazorGenerateItems` | <span data-ttu-id="008f0-153">Enthält *CSHTML*-Dateien aus `Content`-Elementen in `RazorGenerate`-Elementen, wenn `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="008f0-153">When `true`, includes *.cshtml* files from `Content` items in `RazorGenerate` items.</span></span> |
| `GenerateRazorTargetAssemblyInfo` | <span data-ttu-id="008f0-154">Wenn `true`, generiert eine *cs* -Datei mit der Attribute durch `RazorAssemblyAttribute` und die Datei in der Kompilierungsausgabe.</span><span class="sxs-lookup"><span data-stu-id="008f0-154">When `true`, generates a *.cs* file containing attributes specified by `RazorAssemblyAttribute` and includes the file in the compile output.</span></span> |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | <span data-ttu-id="008f0-155">Fügt einen Standardsatz von Assembly-Attributen zu `RazorAssemblyAttribute` hinzu, wenn `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="008f0-155">When `true`, adds a default set of assembly attributes to `RazorAssemblyAttribute`.</span></span> |
| `CopyRazorGenerateFilesToPublishDirectory` | <span data-ttu-id="008f0-156">Wenn `true`, Kopien `RazorGenerate` Elemente ( *.cshtml*) Dateien in das Veröffentlichungsverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="008f0-156">When `true`, copies `RazorGenerate` items (*.cshtml*) files to the publish directory.</span></span> <span data-ttu-id="008f0-157">Razor-Dateien nicht in der Regel für eine veröffentlichte app erforderlich, wenn sie an der Kompilierung zur Buildzeit oder veröffentlichen-Laufzeit beteiligt.</span><span class="sxs-lookup"><span data-stu-id="008f0-157">Typically, Razor files aren't required for a published app if they participate in compilation at build-time or publish-time.</span></span> <span data-ttu-id="008f0-158">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="008f0-158">Defaults to `false`.</span></span> |
| `CopyRefAssembliesToPublishDirectory` | <span data-ttu-id="008f0-159">Kopiert Referenzassembly-Elemente in das Veröffentlichungsverzeichnis, wenn `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="008f0-159">When `true`, copy reference assembly items to the publish directory.</span></span> <span data-ttu-id="008f0-160">In der Regel sind nicht referenzierten Assemblys für eine veröffentlichte app erforderlich, wenn Razor-Kompilierung zur Buildzeit oder veröffentlichen-Laufzeit auftritt.</span><span class="sxs-lookup"><span data-stu-id="008f0-160">Typically, reference assemblies aren't required for a published app if Razor compilation occurs at build-time or publish-time.</span></span> <span data-ttu-id="008f0-161">Legen Sie auf `true` Ihre veröffentlichte app-Runtime-Kompilierung benötigt.</span><span class="sxs-lookup"><span data-stu-id="008f0-161">Set to `true` if your published app requires runtime compilation.</span></span> <span data-ttu-id="008f0-162">Legen Sie den Wert beispielsweise auf `true` bei die app geändert *.cshtml* Dateien zur Laufzeit oder eingebettete Ansichten verwendet.</span><span class="sxs-lookup"><span data-stu-id="008f0-162">For example, set the value to `true` if the app modifies *.cshtml* files at runtime or uses embedded views.</span></span> <span data-ttu-id="008f0-163">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="008f0-163">Defaults to `false`.</span></span> |
| `IncludeRazorContentInPack` | <span data-ttu-id="008f0-164">Wenn `true`, alle Razor-Inhaltselemente ( *.cshtml* Dateien) für die Aufnahme in das generierte NuGet-Paket gekennzeichnet sind.</span><span class="sxs-lookup"><span data-stu-id="008f0-164">When `true`, all Razor content items (*.cshtml* files) are marked for inclusion in the generated NuGet package.</span></span> <span data-ttu-id="008f0-165">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="008f0-165">Defaults to `false`.</span></span> |
| `EmbedRazorGenerateSources` | <span data-ttu-id="008f0-166">Fügt RazorGenerate-Elemente (*CSHTML*-Dateien) als eingebettete Dateien in die generierte Razor-Assembly hinzu, wenn `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="008f0-166">When `true`, adds RazorGenerate (*.cshtml*) items as embedded files to the generated Razor assembly.</span></span> <span data-ttu-id="008f0-167">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="008f0-167">Defaults to `false`.</span></span> |
| `UseRazorBuildServer` | <span data-ttu-id="008f0-168">Verwendet einen dauerhaften Buildserverprozess, um die Auslastung durch die Codegenerierung zu verlagern, wenn `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="008f0-168">When `true`, uses a persistent build server process to offload code generation work.</span></span> <span data-ttu-id="008f0-169">Wird standardmäßig auf den Wert `UseSharedCompilation` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="008f0-169">Defaults to the value of `UseSharedCompilation`.</span></span> |

<span data-ttu-id="008f0-170">Weitere Informationen zu Eigenschaften finden Sie unter [MSBuild-Eigenschaften](/visualstudio/msbuild/msbuild-properties).</span><span class="sxs-lookup"><span data-stu-id="008f0-170">For more information on properties, see [MSBuild properties](/visualstudio/msbuild/msbuild-properties).</span></span>

### <a name="targets"></a><span data-ttu-id="008f0-171">Ziele</span><span class="sxs-lookup"><span data-stu-id="008f0-171">Targets</span></span>

<span data-ttu-id="008f0-172">Das Razor SDK definiert zwei Hauptziele:</span><span class="sxs-lookup"><span data-stu-id="008f0-172">The Razor SDK defines two primary targets:</span></span>

* <span data-ttu-id="008f0-173">`RazorGenerate` &ndash; Generiert Code *cs* Dateien von `RazorGenerate` item-Elemente.</span><span class="sxs-lookup"><span data-stu-id="008f0-173">`RazorGenerate` &ndash; Code generates *.cs* files from `RazorGenerate` item elements.</span></span> <span data-ttu-id="008f0-174">Verwenden Sie die Eigenschaft `RazorGenerateDependsOn`, um zusätzliche Ziele anzugeben, die vor oder nach diesem Ziel ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="008f0-174">Use `RazorGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="008f0-175">`RazorCompile` &ndash; Kompiliert generiert *cs* Dateien im zu einer Razor-Assembly.</span><span class="sxs-lookup"><span data-stu-id="008f0-175">`RazorCompile` &ndash; Compiles generated *.cs* files in to a Razor assembly.</span></span> <span data-ttu-id="008f0-176">Verwenden Sie `RazorCompileDependsOn`, um zusätzliche Ziele anzugeben, die vor oder nach diesem Ziel ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="008f0-176">Use `RazorCompileDependsOn` to specify additional targets that can run before or after this target.</span></span>

### <a name="runtime-compilation-of-razor-views"></a><span data-ttu-id="008f0-177">Kompilierung von Razor-Ansichten zur Laufzeit</span><span class="sxs-lookup"><span data-stu-id="008f0-177">Runtime compilation of Razor views</span></span>

* <span data-ttu-id="008f0-178">Standardmäßig veröffentlicht das Razor SDK keine Verweisassemblys, die für das Durchführen der Laufzeitkompilierung erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="008f0-178">By default, the Razor SDK doesn't publish reference assemblies that are required to perform runtime compilation.</span></span> <span data-ttu-id="008f0-179">Dadurch kommt es zu Kompilierungsfehlern, wenn das Anwendungsmodell von der Laufzeitkompilierung abhängt (z.B. wenn die App eingebettete Ansichten verwendet oder Ansichten nach dem Veröffentlichen der App verändert werden).</span><span class="sxs-lookup"><span data-stu-id="008f0-179">This results in compilation failures when the application model relies on runtime compilation&mdash;for example, the app uses embedded views or changes views after the app is published.</span></span> <span data-ttu-id="008f0-180">Legen Sie `CopyRefAssembliesToPublishDirectory` auf `true` fest, um mit dem Veröffentlichen von Verweisassemblys fortzufahren.</span><span class="sxs-lookup"><span data-stu-id="008f0-180">Set `CopyRefAssembliesToPublishDirectory` to `true` to continue publishing reference assemblies.</span></span>

* <span data-ttu-id="008f0-181">Für eine Web-app, stellen Sie sicher auf die Ihre app die `Microsoft.NET.Sdk.Web` SDK.</span><span class="sxs-lookup"><span data-stu-id="008f0-181">For a web app, ensure your app is targeting the `Microsoft.NET.Sdk.Web` SDK.</span></span>

## <a name="razor-language-version"></a><span data-ttu-id="008f0-182">Version der Razor-Sprache</span><span class="sxs-lookup"><span data-stu-id="008f0-182">Razor language version</span></span>

<span data-ttu-id="008f0-183">Wenn die `Microsoft.NET.Sdk.Web` SDK, die Razor-Sprachversion wird abgeleitet von der Zielframework-Version der app.</span><span class="sxs-lookup"><span data-stu-id="008f0-183">When targeting the `Microsoft.NET.Sdk.Web` SDK, the Razor language version is inferred from the the app's target framework version.</span></span> <span data-ttu-id="008f0-184">Für Projekte für die `Microsoft.NET.Sdk.Razor` SDK oder in dem seltenen Fall, dass die app eine andere Version der Razor-Sprache als der abgeleitete Wert erfordert, kann durch Festlegen eine Version konfiguriert werden die `<RazorLangVersion>` -Eigenschaft in der app-Projektdatei:</span><span class="sxs-lookup"><span data-stu-id="008f0-184">For projects targeting the `Microsoft.NET.Sdk.Razor` SDK or in the rare case that the app requires a different Razor language version than the inferred value, a version can be configured by setting the `<RazorLangVersion>` property in the app's project file:</span></span>

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

## <a name="additional-resources"></a><span data-ttu-id="008f0-185">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="008f0-185">Additional resources</span></span>

* [<span data-ttu-id="008f0-186">Erweiterungen des CSPROJ-Formats für .NET Core</span><span class="sxs-lookup"><span data-stu-id="008f0-186">Additions to the csproj format for .NET Core</span></span>](/dotnet/core/tools/csproj)
* [<span data-ttu-id="008f0-187">Gemeinsame MSBuild-Projektelemente</span><span class="sxs-lookup"><span data-stu-id="008f0-187">Common MSBuild project items</span></span>](/visualstudio/msbuild/common-msbuild-project-items)

---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: Erfahren Sie, wie Razor Pages in ASP.NET Core codierungsseitige Szenarios einfacher und produktiver gestalten als MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 08/23/2019
no-loc:
- Blazor
uid: razor-pages/sdk
ms.openlocfilehash: 2fbdf95d02d7918236981c7fee8ebcbedf5c55e1
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963265"
---
# <a name="aspnet-core-razor-sdk"></a><span data-ttu-id="65dc9-103">ASP.NET Core Razor SDK</span><span class="sxs-lookup"><span data-stu-id="65dc9-103">ASP.NET Core Razor SDK</span></span>

<span data-ttu-id="65dc9-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="65dc9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="overview"></a><span data-ttu-id="65dc9-105">Übersicht</span><span class="sxs-lookup"><span data-stu-id="65dc9-105">Overview</span></span>

<span data-ttu-id="65dc9-106">Die [!INCLUDE[](~/includes/2.1-SDK.md)] umfasst das `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK).</span><span class="sxs-lookup"><span data-stu-id="65dc9-106">The [!INCLUDE[](~/includes/2.1-SDK.md)] includes the `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK).</span></span> <span data-ttu-id="65dc9-107">Das Razor SDK:</span><span class="sxs-lookup"><span data-stu-id="65dc9-107">The Razor SDK:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="65dc9-108">Ist erforderlich, um Projekte zu erstellen, zu verpacken und zu veröffentlichen, die [Razor](xref:mvc/views/razor) -Dateien für ASP.net Core MVC-basierte-oder [Blazor](xref:blazor/index) -Projekte enthalten.</span><span class="sxs-lookup"><span data-stu-id="65dc9-108">Is required to build, package, and publish projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based or [Blazor](xref:blazor/index) projects.</span></span>
* <span data-ttu-id="65dc9-109">Enthält einen Satz vordefinierter Ziele, Eigenschaften und Elemente, die das Anpassen der Kompilierung von Razor-Dateien (*cshtml* -oder *Razor*-Dateien) ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="65dc9-109">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor (*.cshtml* or *.razor*) files.</span></span>

<span data-ttu-id="65dc9-110">Das Razor SDK umfasst `Content`-Elemente, für die `Include`-Attribute auf die globmuster `**\*.cshtml` und `**\*.razor` festgelegt sind.</span><span class="sxs-lookup"><span data-stu-id="65dc9-110">The Razor SDK includes `Content` items with `Include` attributes set to the `**\*.cshtml` and `**\*.razor` globbing patterns.</span></span> <span data-ttu-id="65dc9-111">Übereinstimmende Dateien werden veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="65dc9-111">Matching files are published.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="65dc9-112">Standardisiert die Vorgänge zum Erstellen, Verpacken und Veröffentlichen von Projekten, die [Razor](xref:mvc/views/razor)-Dateien für auf ASP.NET Core MVC basierende Projekte enthalten.</span><span class="sxs-lookup"><span data-stu-id="65dc9-112">Standardizes the experience around building, packaging, and publishing projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based projects.</span></span>
* <span data-ttu-id="65dc9-113">Enthält eine vordefinierte Reihe von Zielen, Eigenschaften und Elementen, die das Anpassen der Razor-Dateien ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="65dc9-113">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor files.</span></span>

<span data-ttu-id="65dc9-114">Das Razor SDK enthält ein `Content`-Element mit einem `Include`-Attribut, das auf das `**\*.cshtml`-globmuster festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="65dc9-114">The Razor SDK includes a `Content` item with an `Include` attribute set to the `**\*.cshtml` globbing pattern.</span></span> <span data-ttu-id="65dc9-115">Übereinstimmende Dateien werden veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="65dc9-115">Matching files are published.</span></span>

::: moniker-end

## <a name="prerequisites"></a><span data-ttu-id="65dc9-116">Erforderliche Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="65dc9-116">Prerequisites</span></span>

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a><span data-ttu-id="65dc9-117">Verwenden des Razor SDK</span><span class="sxs-lookup"><span data-stu-id="65dc9-117">Use the Razor SDK</span></span>

<span data-ttu-id="65dc9-118">Die meisten Web-Apps müssen nicht explizit auf das Razor-SDK verweisen.</span><span class="sxs-lookup"><span data-stu-id="65dc9-118">Most web apps aren't required to explicitly reference the Razor SDK.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="65dc9-119">Um das Razor SDK zum Erstellen von Klassenbibliotheken mit Razor-Ansichten oder Razor Pages zu verwenden, wird empfohlen, mit der Projektvorlage für die Razor-Klassenbibliothek (RCL) zu beginnen.</span><span class="sxs-lookup"><span data-stu-id="65dc9-119">To use the Razor SDK to build class libraries containing Razor views or Razor Pages, we recommend starting with the Razor class library (RCL) project template.</span></span> <span data-ttu-id="65dc9-120">Eine RCL, die verwendet wird, um Blazor ( *. Razor*)-Dateien zu erstellen, erfordert mindestens einen Verweis auf das Paket [Microsoft. aspnetcore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) .</span><span class="sxs-lookup"><span data-stu-id="65dc9-120">An RCL that's used to build Blazor (*.razor*) files minimally requires a reference to the [Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) package.</span></span> <span data-ttu-id="65dc9-121">Eine RCL, die zum Erstellen von Razor-Sichten oder-Seiten (*cshtml* -Dateien) verwendet wird, erfordert mindestens `netcoreapp3.0` oder höher und verfügt über eine `FrameworkReference` bis zum [Microsoft. aspnetcore. app-Metapaket](xref:fundamentals/metapackage-app) in der Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="65dc9-121">An RCL that's used to build Razor views or pages (*.cshtml* files) minimally requires targeting `netcoreapp3.0` or later and has a `FrameworkReference` to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in its project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="65dc9-122">So verwenden Sie das Razor SDK zum Erstellen von Klassenbibliotheken, die Razor-Ansichten oder Razor-Seiten enthalten:</span><span class="sxs-lookup"><span data-stu-id="65dc9-122">To use the Razor SDK to build class libraries containing Razor views or Razor Pages:</span></span>

* <span data-ttu-id="65dc9-123">Verwenden Sie `Microsoft.NET.Sdk.Razor` anstelle von `Microsoft.NET.Sdk`:</span><span class="sxs-lookup"><span data-stu-id="65dc9-123">Use `Microsoft.NET.Sdk.Razor` instead of `Microsoft.NET.Sdk`:</span></span>

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* <span data-ttu-id="65dc9-124">In der Regel ist ein Paket Verweis auf `Microsoft.AspNetCore.Mvc` erforderlich, um zusätzliche Abhängigkeiten zu erhalten, die zum Erstellen und Kompilieren von Razor Pages-und Razor-Ansichten erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="65dc9-124">Typically, a package reference to `Microsoft.AspNetCore.Mvc` is required to receive additional dependencies that are required to build and compile Razor Pages and Razor views.</span></span> <span data-ttu-id="65dc9-125">Das Projekt sollte mindestens Paket Verweise hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="65dc9-125">At a minimum, your project should add package references to:</span></span>

  * `Microsoft.AspNetCore.Razor.Design` 
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`
    
  <span data-ttu-id="65dc9-126">Das `Microsoft.AspNetCore.Razor.Design`-Paket stellt die Razor-Kompilierungs Aufgaben und-Ziele für das Projekt bereit.</span><span class="sxs-lookup"><span data-stu-id="65dc9-126">The `Microsoft.AspNetCore.Razor.Design` package provides the Razor compilation tasks and targets for the project.</span></span>

  <span data-ttu-id="65dc9-127">Diese Pakete sind in `Microsoft.AspNetCore.Mvc` enthalten.</span><span class="sxs-lookup"><span data-stu-id="65dc9-127">The preceding packages are included in `Microsoft.AspNetCore.Mvc`.</span></span> <span data-ttu-id="65dc9-128">Das folgende Markup zeigt eine Projektdatei, die das Razor-SDK verwendet, um Razor-Dateien für eine ASP.net Core Razor Pages-APP zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="65dc9-128">The following markup shows a project file that uses the Razor SDK to build Razor files for an ASP.NET Core Razor Pages app:</span></span>
    
  [!code-xml[](sdk/sample/RazorSDK.csproj)]
  
::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> <span data-ttu-id="65dc9-129">Die Pakete "`Microsoft.AspNetCore.Razor.Design`" und "`Microsoft.AspNetCore.Mvc.Razor.Extensions`" sind im [Metapaket "Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)" enthalten.</span><span class="sxs-lookup"><span data-stu-id="65dc9-129">The `Microsoft.AspNetCore.Razor.Design` and `Microsoft.AspNetCore.Mvc.Razor.Extensions` packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="65dc9-130">Der Versions lose `Microsoft.AspNetCore.App`-Paket Verweis stellt jedoch ein Metapaket für die APP bereit, das nicht die neueste Version von `Microsoft.AspNetCore.Razor.Design` enthält.</span><span class="sxs-lookup"><span data-stu-id="65dc9-130">However, the version-less `Microsoft.AspNetCore.App` package reference provides a metapackage to the app that doesn't include the latest version of `Microsoft.AspNetCore.Razor.Design`.</span></span> <span data-ttu-id="65dc9-131">Projekte müssen auf eine konsistente Version von `Microsoft.AspNetCore.Razor.Design` (oder `Microsoft.AspNetCore.Mvc`) verweisen, damit die neuesten Build-Time-Korrekturen für Razor eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="65dc9-131">Projects must reference a consistent version of `Microsoft.AspNetCore.Razor.Design` (or `Microsoft.AspNetCore.Mvc`) so that the latest build-time fixes for Razor are included.</span></span> <span data-ttu-id="65dc9-132">Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/aspnet/Razor/issues/2553).</span><span class="sxs-lookup"><span data-stu-id="65dc9-132">For more information, see [this GitHub issue](https://github.com/aspnet/Razor/issues/2553).</span></span>

::: moniker-end

### <a name="properties"></a><span data-ttu-id="65dc9-133">Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="65dc9-133">Properties</span></span>

<span data-ttu-id="65dc9-134">Die folgenden Eigenschaften steuern das Verhalten des Razor SDK im Rahmen eines Projektbuilds:</span><span class="sxs-lookup"><span data-stu-id="65dc9-134">The following properties control the Razor's SDK behavior as part of a project build:</span></span>

* <span data-ttu-id="65dc9-135">`RazorCompileOnBuild` &ndash;, wenn `true` ist, wird die Razor-Assembly im Rahmen der Projekt Erstellung kompiliert und ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="65dc9-135">`RazorCompileOnBuild` &ndash; When `true`, compiles and emits the Razor assembly as part of building the project.</span></span> <span data-ttu-id="65dc9-136">Wird standardmäßig auf `true` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="65dc9-136">Defaults to `true`.</span></span>
* <span data-ttu-id="65dc9-137">`RazorCompileOnPublish` &ndash;, wenn `true` ist, kompiliert und gibt die Razor-Assembly als Teil der Veröffentlichung des Projekts aus.</span><span class="sxs-lookup"><span data-stu-id="65dc9-137">`RazorCompileOnPublish` &ndash; When `true`, compiles and emits the Razor assembly as part of publishing the project.</span></span> <span data-ttu-id="65dc9-138">Wird standardmäßig auf `true` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="65dc9-138">Defaults to `true`.</span></span>

<span data-ttu-id="65dc9-139">Die Eigenschaften und Elemente in der folgenden Tabelle werden verwendet, um Eingaben und Ausgaben für das Razor SDK zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="65dc9-139">The properties and items in the following table are used to configure inputs and output to the Razor SDK.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="65dc9-140">Ab ASP.net Core 3,0 werden MVC-Sichten oder-Razor Pages standardmäßig nicht bereitgestellt, wenn die MSBuild-Eigenschaften `RazorCompileOnBuild` oder `RazorCompileOnPublish` in der Projektdatei deaktiviert sind.</span><span class="sxs-lookup"><span data-stu-id="65dc9-140">Starting with ASP.NET Core 3.0, MVC Views or Razor Pages aren't served by default if the `RazorCompileOnBuild` or `RazorCompileOnPublish` MSBuild properties in the project file are disabled.</span></span> <span data-ttu-id="65dc9-141">Anwendungen müssen einen expliziten Verweis auf das Paket [Microsoft. aspnetcore. MVC. Razor. runtimecompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) hinzufügen, wenn die APP zur Verarbeitung von *cshtml* -Dateien auf der Lauf Zeit Kompilierung basiert.</span><span class="sxs-lookup"><span data-stu-id="65dc9-141">Applications must add an explicit reference to the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) package if the app relies on runtime compilation to process *.cshtml* files.</span></span>

::: moniker-end

| <span data-ttu-id="65dc9-142">Elemente</span><span class="sxs-lookup"><span data-stu-id="65dc9-142">Items</span></span> | <span data-ttu-id="65dc9-143">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="65dc9-143">Description</span></span> |
| ----- | ----------- |
| `RazorGenerate` | <span data-ttu-id="65dc9-144">Element Elemente (*cshtml* -Dateien), die Eingaben für die Codegenerierung sind.</span><span class="sxs-lookup"><span data-stu-id="65dc9-144">Item elements (*.cshtml* files) that are inputs to code generation.</span></span> |
| `RazorComponent` | <span data-ttu-id="65dc9-145">Element Elemente (*Razor* -Dateien), die Eingaben für die Codegenerierung von Razor-Komponenten sind.</span><span class="sxs-lookup"><span data-stu-id="65dc9-145">Item elements (*.razor* files) that are inputs to Razor component code generation.</span></span> |
| `RazorCompile` | <span data-ttu-id="65dc9-146">Element Elemente (*CS* -Dateien), die Eingaben für Razor-Kompilierungs Ziele sind.</span><span class="sxs-lookup"><span data-stu-id="65dc9-146">Item elements (*.cs* files) that are inputs to Razor compilation targets.</span></span> <span data-ttu-id="65dc9-147">Verwenden Sie diesen `ItemGroup`, um zusätzliche Dateien anzugeben, die in die Razor-Assembly kompiliert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="65dc9-147">Use this `ItemGroup` to specify additional files to be compiled into the Razor assembly.</span></span> |
| `RazorTargetAssemblyAttribute` | <span data-ttu-id="65dc9-148">Item-Elemente, die für das Codieren von Generate-Attributen für die Razor-Assembly verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="65dc9-148">Item elements used to code generate attributes for the Razor assembly.</span></span> <span data-ttu-id="65dc9-149">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="65dc9-149">For example:</span></span>  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | <span data-ttu-id="65dc9-150">Element Elemente, die der generierten Razor-Assembly als eingebettete Ressourcen hinzugefügt wurden.</span><span class="sxs-lookup"><span data-stu-id="65dc9-150">Item elements added as embedded resources to the generated Razor assembly.</span></span> |

| <span data-ttu-id="65dc9-151">property</span><span class="sxs-lookup"><span data-stu-id="65dc9-151">Property</span></span> | <span data-ttu-id="65dc9-152">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="65dc9-152">Description</span></span> |
| -------- | ----------- |
| `RazorTargetName` | <span data-ttu-id="65dc9-153">Dateiname (ohne Erweiterung) der Assembly, die von Razor erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="65dc9-153">File name (without extension) of the assembly produced by Razor.</span></span> | 
| `RazorOutputPath` | <span data-ttu-id="65dc9-154">Das Razor-Ausgabeverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="65dc9-154">The Razor output directory.</span></span> |
| `RazorCompileToolset` | <span data-ttu-id="65dc9-155">Wird verwendet, um das Toolset für die Erstellung der Razor-Assembly zu bestimmen.</span><span class="sxs-lookup"><span data-stu-id="65dc9-155">Used to determine the toolset used to build the Razor assembly.</span></span> <span data-ttu-id="65dc9-156">Gültige Werte sind `Implicit`, `RazorSDK` und `PrecompilationTool`.</span><span class="sxs-lookup"><span data-stu-id="65dc9-156">Valid values are `Implicit`, `RazorSDK`, and `PrecompilationTool`.</span></span> |
| [<span data-ttu-id="65dc9-157">Enabledefaultcontentitems</span><span class="sxs-lookup"><span data-stu-id="65dc9-157">EnableDefaultContentItems</span></span>](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | <span data-ttu-id="65dc9-158">Der Standardwert ist `true`.</span><span class="sxs-lookup"><span data-stu-id="65dc9-158">Default is `true`.</span></span> <span data-ttu-id="65dc9-159">Wenn `true` ist, enthält die Dateien *Web. config*, *. JSON*und *. cshtml* als Inhalt in das Projekt.</span><span class="sxs-lookup"><span data-stu-id="65dc9-159">When `true`, includes *web.config*, *.json*, and *.cshtml* files as content in the project.</span></span> <span data-ttu-id="65dc9-160">Wenn Sie über `Microsoft.NET.Sdk.Web` referenziert werden, sind auch Dateien unter den Dateien " *wwwroot* " und "config" enthalten.</span><span class="sxs-lookup"><span data-stu-id="65dc9-160">When referenced via `Microsoft.NET.Sdk.Web`, files under *wwwroot* and config files are also included.</span></span> |
| `EnableDefaultRazorGenerateItems` | <span data-ttu-id="65dc9-161">Enthält *CSHTML*-Dateien aus `Content`-Elementen in `RazorGenerate`-Elementen, wenn `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="65dc9-161">When `true`, includes *.cshtml* files from `Content` items in `RazorGenerate` items.</span></span> |
| `GenerateRazorTargetAssemblyInfo` | <span data-ttu-id="65dc9-162">Wenn `true`, generiert eine *CS* -Datei mit Attributen, die von `RazorAssemblyAttribute` angegeben werden, und schließt die Datei in die Kompilierungs Ausgabe ein.</span><span class="sxs-lookup"><span data-stu-id="65dc9-162">When `true`, generates a *.cs* file containing attributes specified by `RazorAssemblyAttribute` and includes the file in the compile output.</span></span> |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | <span data-ttu-id="65dc9-163">Fügt einen Standardsatz von Assembly-Attributen zu `RazorAssemblyAttribute` hinzu, wenn `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="65dc9-163">When `true`, adds a default set of assembly attributes to `RazorAssemblyAttribute`.</span></span> |
| `CopyRazorGenerateFilesToPublishDirectory` | <span data-ttu-id="65dc9-164">Wenn `true`, werden `RazorGenerate` Items-Dateien ( *. cshtml*) in das Veröffentlichungs Verzeichnis kopiert.</span><span class="sxs-lookup"><span data-stu-id="65dc9-164">When `true`, copies `RazorGenerate` items (*.cshtml*) files to the publish directory.</span></span> <span data-ttu-id="65dc9-165">In der Regel sind Razor-Dateien für eine veröffentlichte APP nicht erforderlich, wenn Sie zur Buildzeit oder zum Zeitpunkt der Veröffentlichung an der Kompilierung teilnehmen.</span><span class="sxs-lookup"><span data-stu-id="65dc9-165">Typically, Razor files aren't required for a published app if they participate in compilation at build-time or publish-time.</span></span> <span data-ttu-id="65dc9-166">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="65dc9-166">Defaults to `false`.</span></span> |
| `CopyRefAssembliesToPublishDirectory` | <span data-ttu-id="65dc9-167">Kopiert Referenzassembly-Elemente in das Veröffentlichungsverzeichnis, wenn `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="65dc9-167">When `true`, copy reference assembly items to the publish directory.</span></span> <span data-ttu-id="65dc9-168">In der Regel sind Verweisassemblys für eine veröffentlichte APP nicht erforderlich, wenn die Razor-Kompilierung zum Zeitpunkt der Erstellung oder zur Veröffentlichung erfolgt.</span><span class="sxs-lookup"><span data-stu-id="65dc9-168">Typically, reference assemblies aren't required for a published app if Razor compilation occurs at build-time or publish-time.</span></span> <span data-ttu-id="65dc9-169">Legen Sie den Wert auf `true` fest, wenn die veröffentlichte App eine Lauf Zeit Kompilierung</span><span class="sxs-lookup"><span data-stu-id="65dc9-169">Set to `true` if your published app requires runtime compilation.</span></span> <span data-ttu-id="65dc9-170">Legen Sie beispielsweise den Wert `true` fest, wenn die APP *cshtml* -Dateien zur Laufzeit ändert oder eingebettete Sichten verwendet.</span><span class="sxs-lookup"><span data-stu-id="65dc9-170">For example, set the value to `true` if the app modifies *.cshtml* files at runtime or uses embedded views.</span></span> <span data-ttu-id="65dc9-171">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="65dc9-171">Defaults to `false`.</span></span> |
| `IncludeRazorContentInPack` | <span data-ttu-id="65dc9-172">Wenn `true`, werden alle Razor-Inhaltselemente (*cshtml* -Dateien) für die Einbindung in das generierte nuget-Paket markiert.</span><span class="sxs-lookup"><span data-stu-id="65dc9-172">When `true`, all Razor content items (*.cshtml* files) are marked for inclusion in the generated NuGet package.</span></span> <span data-ttu-id="65dc9-173">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="65dc9-173">Defaults to `false`.</span></span> |
| `EmbedRazorGenerateSources` | <span data-ttu-id="65dc9-174">Fügt RazorGenerate-Elemente (*CSHTML*-Dateien) als eingebettete Dateien in die generierte Razor-Assembly hinzu, wenn `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="65dc9-174">When `true`, adds RazorGenerate (*.cshtml*) items as embedded files to the generated Razor assembly.</span></span> <span data-ttu-id="65dc9-175">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="65dc9-175">Defaults to `false`.</span></span> |
| `UseRazorBuildServer` | <span data-ttu-id="65dc9-176">Verwendet einen dauerhaften Buildserverprozess, um die Auslastung durch die Codegenerierung zu verlagern, wenn `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="65dc9-176">When `true`, uses a persistent build server process to offload code generation work.</span></span> <span data-ttu-id="65dc9-177">Wird standardmäßig auf den Wert `UseSharedCompilation` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="65dc9-177">Defaults to the value of `UseSharedCompilation`.</span></span> |
| `GenerateMvcApplicationPartsAssemblyAttributes` | <span data-ttu-id="65dc9-178">Wenn `true`, generiert das SDK zusätzliche Attribute, die von MVC zur Laufzeit verwendet werden, um die Ermittlung von Anwendungs Teilen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="65dc9-178">When `true`, the SDK generates additional attributes used by MVC at runtime to perform application part discovery.</span></span> |

<span data-ttu-id="65dc9-179">Weitere Informationen zu Eigenschaften finden Sie unter [MSBuild-Eigenschaften](/visualstudio/msbuild/msbuild-properties).</span><span class="sxs-lookup"><span data-stu-id="65dc9-179">For more information on properties, see [MSBuild properties](/visualstudio/msbuild/msbuild-properties).</span></span>

### <a name="targets"></a><span data-ttu-id="65dc9-180">Ziele</span><span class="sxs-lookup"><span data-stu-id="65dc9-180">Targets</span></span>

<span data-ttu-id="65dc9-181">Das Razor SDK definiert zwei Hauptziele:</span><span class="sxs-lookup"><span data-stu-id="65dc9-181">The Razor SDK defines two primary targets:</span></span>

* <span data-ttu-id="65dc9-182">`RazorGenerate`-&ndash;-Code generiert *CS* -Dateien aus `RazorGenerate` item-Elementen.</span><span class="sxs-lookup"><span data-stu-id="65dc9-182">`RazorGenerate` &ndash; Code generates *.cs* files from `RazorGenerate` item elements.</span></span> <span data-ttu-id="65dc9-183">Verwenden Sie die `RazorGenerateDependsOn`-Eigenschaft, um zusätzliche Ziele anzugeben, die vor oder nach diesem Ziel ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="65dc9-183">Use the `RazorGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="65dc9-184">mit `RazorCompile` &ndash; werden generierte *CS* -Dateien in in eine Razor-Assembly kompiliert.</span><span class="sxs-lookup"><span data-stu-id="65dc9-184">`RazorCompile` &ndash; Compiles generated *.cs* files in to a Razor assembly.</span></span> <span data-ttu-id="65dc9-185">Verwenden Sie den `RazorCompileDependsOn`, um zusätzliche Ziele anzugeben, die vor oder nach diesem Ziel ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="65dc9-185">Use the `RazorCompileDependsOn` to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="65dc9-186">`RazorComponentGenerate`-&ndash;-Code generiert *CS* -Dateien für `RazorComponent`-Element Elemente.</span><span class="sxs-lookup"><span data-stu-id="65dc9-186">`RazorComponentGenerate` &ndash; Code generates *.cs* files for `RazorComponent` item elements.</span></span> <span data-ttu-id="65dc9-187">Verwenden Sie die `RazorComponentGenerateDependsOn`-Eigenschaft, um zusätzliche Ziele anzugeben, die vor oder nach diesem Ziel ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="65dc9-187">Use the `RazorComponentGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>

### <a name="runtime-compilation-of-razor-views"></a><span data-ttu-id="65dc9-188">Kompilierung von Razor-Ansichten zur Laufzeit</span><span class="sxs-lookup"><span data-stu-id="65dc9-188">Runtime compilation of Razor views</span></span>

* <span data-ttu-id="65dc9-189">Standardmäßig veröffentlicht das Razor SDK keine Verweisassemblys, die für das Durchführen der Laufzeitkompilierung erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="65dc9-189">By default, the Razor SDK doesn't publish reference assemblies that are required to perform runtime compilation.</span></span> <span data-ttu-id="65dc9-190">Dadurch kommt es zu Kompilierungsfehlern, wenn das Anwendungsmodell von der Laufzeitkompilierung abhängt (z.B. wenn die App eingebettete Ansichten verwendet oder Ansichten nach dem Veröffentlichen der App verändert werden).</span><span class="sxs-lookup"><span data-stu-id="65dc9-190">This results in compilation failures when the application model relies on runtime compilation&mdash;for example, the app uses embedded views or changes views after the app is published.</span></span> <span data-ttu-id="65dc9-191">Legen Sie `CopyRefAssembliesToPublishDirectory` auf `true` fest, um mit dem Veröffentlichen von Verweisassemblys fortzufahren.</span><span class="sxs-lookup"><span data-stu-id="65dc9-191">Set `CopyRefAssembliesToPublishDirectory` to `true` to continue publishing reference assemblies.</span></span>

* <span data-ttu-id="65dc9-192">Stellen Sie für eine Web-App sicher, dass Ihre APP auf das `Microsoft.NET.Sdk.Web`-SDK abzielt.</span><span class="sxs-lookup"><span data-stu-id="65dc9-192">For a web app, ensure your app is targeting the `Microsoft.NET.Sdk.Web` SDK.</span></span>

## <a name="razor-language-version"></a><span data-ttu-id="65dc9-193">Razor-Sprachversion</span><span class="sxs-lookup"><span data-stu-id="65dc9-193">Razor language version</span></span>

<span data-ttu-id="65dc9-194">Wenn das `Microsoft.NET.Sdk.Web`-SDK als Ziel verwendet wird, wird die Razor-Sprachversion von der Zielframeworkversion der APP abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="65dc9-194">When targeting the `Microsoft.NET.Sdk.Web` SDK, the Razor language version is inferred from the app's target framework version.</span></span> <span data-ttu-id="65dc9-195">Für Projekte, die auf das `Microsoft.NET.Sdk.Razor`-SDK abzielen, oder in dem seltenen Fall, dass die APP eine andere Version der Razor-Sprache benötigt als der abherzustehende Wert, kann eine Version konfiguriert werden, indem die Eigenschaft `<RazorLangVersion>` in der Projektdatei der APP festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="65dc9-195">For projects targeting the `Microsoft.NET.Sdk.Razor` SDK or in the rare case that the app requires a different Razor language version than the inferred value, a version can be configured by setting the `<RazorLangVersion>` property in the app's project file:</span></span>

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

<span data-ttu-id="65dc9-196">Die Sprachversion von Razor ist eng in die Version der Laufzeit integriert, für die Sie erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="65dc9-196">Razor's language version is tightly integrated with the version of the runtime that it was built for.</span></span> <span data-ttu-id="65dc9-197">Das Ziel einer Sprachversion, die nicht für die Laufzeit konzipiert ist, wird nicht unterstützt und erzeugt wahrscheinlich Buildfehler.</span><span class="sxs-lookup"><span data-stu-id="65dc9-197">Targeting a language version that isn't designed for the runtime is unsupported and likely produces build errors.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="65dc9-198">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="65dc9-198">Additional resources</span></span>

* [<span data-ttu-id="65dc9-199">Erweiterungen des CSPROJ-Formats für .NET Core</span><span class="sxs-lookup"><span data-stu-id="65dc9-199">Additions to the csproj format for .NET Core</span></span>](/dotnet/core/tools/csproj)
* [<span data-ttu-id="65dc9-200">Gemeinsame MSBuild-Projektelemente</span><span class="sxs-lookup"><span data-stu-id="65dc9-200">Common MSBuild project items</span></span>](/visualstudio/msbuild/common-msbuild-project-items)

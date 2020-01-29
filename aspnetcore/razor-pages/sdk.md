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
ms.openlocfilehash: 872d90662494735dc0e4caa01c46fcdcc2606bc6
ms.sourcegitcommit: b5ceb0a46d0254cc3425578116e2290142eec0f0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2020
ms.locfileid: "76809132"
---
# <a name="aspnet-core-razor-sdk"></a><span data-ttu-id="9b0b3-103">ASP.NET Core Razor SDK</span><span class="sxs-lookup"><span data-stu-id="9b0b3-103">ASP.NET Core Razor SDK</span></span>

<span data-ttu-id="9b0b3-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9b0b3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="overview"></a><span data-ttu-id="9b0b3-105">Übersicht über</span><span class="sxs-lookup"><span data-stu-id="9b0b3-105">Overview</span></span>

<span data-ttu-id="9b0b3-106">Die [!INCLUDE[](~/includes/2.1-SDK.md)] enthält die `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor-SDK).</span><span class="sxs-lookup"><span data-stu-id="9b0b3-106">The [!INCLUDE[](~/includes/2.1-SDK.md)] includes the `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK).</span></span> <span data-ttu-id="9b0b3-107">Das Razor SDK:</span><span class="sxs-lookup"><span data-stu-id="9b0b3-107">The Razor SDK:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="9b0b3-108">Ist erforderlich, um Projekte zu erstellen, zu verpacken und zu veröffentlichen, die [Razor](xref:mvc/views/razor) -Dateien für ASP.net Core MVC-basierte oder [blazor](xref:blazor/index) -Projekte enthalten.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-108">Is required to build, package, and publish projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based or [Blazor](xref:blazor/index) projects.</span></span>
* <span data-ttu-id="9b0b3-109">Enthält einen Satz vordefinierter Ziele, Eigenschaften und Elemente, die das Anpassen der Kompilierung von Razor-Dateien (*cshtml* -oder *Razor*-Dateien) ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-109">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor (*.cshtml* or *.razor*) files.</span></span>

<span data-ttu-id="9b0b3-110">Das Razor SDK umfasst `Content` Elemente, deren `Include` Attribute auf die `**\*.cshtml` und `**\*.razor` globmuster festgelegt sind.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-110">The Razor SDK includes `Content` items with `Include` attributes set to the `**\*.cshtml` and `**\*.razor` globbing patterns.</span></span> <span data-ttu-id="9b0b3-111">Übereinstimmende Dateien werden veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-111">Matching files are published.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="9b0b3-112">Standardisiert die Vorgänge zum Erstellen, Verpacken und Veröffentlichen von Projekten, die [Razor](xref:mvc/views/razor)-Dateien für auf ASP.NET Core MVC basierende Projekte enthalten.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-112">Standardizes the experience around building, packaging, and publishing projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based projects.</span></span>
* <span data-ttu-id="9b0b3-113">Enthält eine vordefinierte Reihe von Zielen, Eigenschaften und Elementen, die das Anpassen der Razor-Dateien ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-113">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor files.</span></span>

<span data-ttu-id="9b0b3-114">Das Razor SDK enthält ein `Content` Element mit einem `Include`-Attribut, das auf das `**\*.cshtml` globmuster festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-114">The Razor SDK includes a `Content` item with an `Include` attribute set to the `**\*.cshtml` globbing pattern.</span></span> <span data-ttu-id="9b0b3-115">Übereinstimmende Dateien werden veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-115">Matching files are published.</span></span>

::: moniker-end

## <a name="prerequisites"></a><span data-ttu-id="9b0b3-116">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="9b0b3-116">Prerequisites</span></span>

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a><span data-ttu-id="9b0b3-117">Verwenden des Razor SDK</span><span class="sxs-lookup"><span data-stu-id="9b0b3-117">Use the Razor SDK</span></span>

<span data-ttu-id="9b0b3-118">Die meisten Web-apps müssen nicht explizit das Razor-SDK zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-118">Most web apps aren't required to explicitly reference the Razor SDK.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9b0b3-119">Um das Razor SDK zum Erstellen von Klassenbibliotheken mit Razor-Ansichten oder Razor Pages zu verwenden, wird empfohlen, mit der Projektvorlage für die Razor-Klassenbibliothek (RCL) zu beginnen.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-119">To use the Razor SDK to build class libraries containing Razor views or Razor Pages, we recommend starting with the Razor class library (RCL) project template.</span></span> <span data-ttu-id="9b0b3-120">Eine RCL, die zum Erstellen von blazor-Dateien (*Razor*-Dateien) verwendet wird, erfordert mindestens einen Verweis auf das Paket " [Microsoft. aspnetcore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) ".</span><span class="sxs-lookup"><span data-stu-id="9b0b3-120">An RCL that's used to build Blazor (*.razor*) files minimally requires a reference to the [Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) package.</span></span> <span data-ttu-id="9b0b3-121">Eine RCL, die zum Erstellen von Razor-Sichten oder-Seiten (*cshtml* -Dateien) verwendet wird, erfordert mindestens `netcoreapp3.0` oder höher und verfügt über eine `FrameworkReference` für das [Metapaket "Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app) " in der Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-121">An RCL that's used to build Razor views or pages (*.cshtml* files) minimally requires targeting `netcoreapp3.0` or later and has a `FrameworkReference` to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in its project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9b0b3-122">So verwenden Sie das Razor SDK zum Erstellen von Klassenbibliotheken, die Razor-Ansichten oder Razor-Seiten enthalten:</span><span class="sxs-lookup"><span data-stu-id="9b0b3-122">To use the Razor SDK to build class libraries containing Razor views or Razor Pages:</span></span>

* <span data-ttu-id="9b0b3-123">Verwenden Sie `Microsoft.NET.Sdk.Razor` anstelle von `Microsoft.NET.Sdk`:</span><span class="sxs-lookup"><span data-stu-id="9b0b3-123">Use `Microsoft.NET.Sdk.Razor` instead of `Microsoft.NET.Sdk`:</span></span>

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* <span data-ttu-id="9b0b3-124">In der Regel einen Paketverweis auf `Microsoft.AspNetCore.Mvc` ist erforderlich, um zusätzliche Abhängigkeiten zu erhalten, die zum Erstellen und Kompilieren von Razor Pages und Razor-Ansichten erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-124">Typically, a package reference to `Microsoft.AspNetCore.Mvc` is required to receive additional dependencies that are required to build and compile Razor Pages and Razor views.</span></span> <span data-ttu-id="9b0b3-125">Zumindest sollten Ihr Projekt Paketverweise zum Hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="9b0b3-125">At a minimum, your project should add package references to:</span></span>

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  <span data-ttu-id="9b0b3-126">Die `Microsoft.AspNetCore.Razor.Design` Paket stellt die Razor-Kompilierung-Aufgaben und-Ziele für das Projekt bereit.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-126">The `Microsoft.AspNetCore.Razor.Design` package provides the Razor compilation tasks and targets for the project.</span></span>

  <span data-ttu-id="9b0b3-127">Diese Pakete sind in `Microsoft.AspNetCore.Mvc` enthalten.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-127">The preceding packages are included in `Microsoft.AspNetCore.Mvc`.</span></span> <span data-ttu-id="9b0b3-128">Das folgende Markup zeigt eine Projektdatei, die die Razor-SDK verwendet, um die Razor-Dateien für eine ASP.NET Core Razor Pages-app zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="9b0b3-128">The following markup shows a project file that uses the Razor SDK to build Razor files for an ASP.NET Core Razor Pages app:</span></span>

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> <span data-ttu-id="9b0b3-129">Die `Microsoft.AspNetCore.Razor.Design` und `Microsoft.AspNetCore.Mvc.Razor.Extensions` Pakete befinden sich der [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="9b0b3-129">The `Microsoft.AspNetCore.Razor.Design` and `Microsoft.AspNetCore.Mvc.Razor.Extensions` packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="9b0b3-130">Allerdings das Version-kleiner `Microsoft.AspNetCore.App` Paketverweis bietet ein metapaket an die app, die die neueste Version von keine `Microsoft.AspNetCore.Razor.Design`.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-130">However, the version-less `Microsoft.AspNetCore.App` package reference provides a metapackage to the app that doesn't include the latest version of `Microsoft.AspNetCore.Razor.Design`.</span></span> <span data-ttu-id="9b0b3-131">Projekte müssen eine einheitliche Version des verweisen `Microsoft.AspNetCore.Razor.Design` (oder `Microsoft.AspNetCore.Mvc`) so, dass die neueste Fixes der Buildzeit für Razor enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-131">Projects must reference a consistent version of `Microsoft.AspNetCore.Razor.Design` (or `Microsoft.AspNetCore.Mvc`) so that the latest build-time fixes for Razor are included.</span></span> <span data-ttu-id="9b0b3-132">Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/aspnet/Razor/issues/2553).</span><span class="sxs-lookup"><span data-stu-id="9b0b3-132">For more information, see [this GitHub issue](https://github.com/aspnet/Razor/issues/2553).</span></span>

::: moniker-end

### <a name="properties"></a><span data-ttu-id="9b0b3-133">Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="9b0b3-133">Properties</span></span>

<span data-ttu-id="9b0b3-134">Die folgenden Eigenschaften steuern das Verhalten des Razor SDK im Rahmen eines Projektbuilds:</span><span class="sxs-lookup"><span data-stu-id="9b0b3-134">The following properties control the Razor's SDK behavior as part of a project build:</span></span>

* <span data-ttu-id="9b0b3-135">`RazorCompileOnBuild` &ndash; Wenn `true`, kompiliert, und gibt die Razor-Assembly im Rahmen der Erstellung des Projekts.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-135">`RazorCompileOnBuild` &ndash; When `true`, compiles and emits the Razor assembly as part of building the project.</span></span> <span data-ttu-id="9b0b3-136">Wird standardmäßig auf `true` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-136">Defaults to `true`.</span></span>
* <span data-ttu-id="9b0b3-137">`RazorCompileOnPublish` &ndash; Wenn `true`, kompiliert, und gibt die Razor-Assembly als Teil der Veröffentlichung des Projekts.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-137">`RazorCompileOnPublish` &ndash; When `true`, compiles and emits the Razor assembly as part of publishing the project.</span></span> <span data-ttu-id="9b0b3-138">Wird standardmäßig auf `true` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-138">Defaults to `true`.</span></span>

<span data-ttu-id="9b0b3-139">Die Eigenschaften und Elemente in der folgenden Tabelle werden zum Konfigurieren von ein- und Ausgabe an die Razor-SDK verwendet.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-139">The properties and items in the following table are used to configure inputs and output to the Razor SDK.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="9b0b3-140">Ab ASP.net Core 3,0 werden MVC-Sichten oder-Razor Pages standardmäßig nicht bereitgestellt, wenn die Eigenschaften `RazorCompileOnBuild` oder `RazorCompileOnPublish` MSBuild in der Projektdatei deaktiviert sind.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-140">Starting with ASP.NET Core 3.0, MVC Views or Razor Pages aren't served by default if the `RazorCompileOnBuild` or `RazorCompileOnPublish` MSBuild properties in the project file are disabled.</span></span> <span data-ttu-id="9b0b3-141">Anwendungen müssen einen expliziten Verweis auf das Paket [Microsoft. aspnetcore. MVC. Razor. runtimecompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) hinzufügen, wenn die APP zur Verarbeitung von *cshtml* -Dateien auf der Lauf Zeit Kompilierung basiert.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-141">Applications must add an explicit reference to the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) package if the app relies on runtime compilation to process *.cshtml* files.</span></span>

::: moniker-end

| <span data-ttu-id="9b0b3-142">Elemente</span><span class="sxs-lookup"><span data-stu-id="9b0b3-142">Items</span></span> | <span data-ttu-id="9b0b3-143">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="9b0b3-143">Description</span></span> |
| ----- | ----------- |
| `RazorGenerate` | <span data-ttu-id="9b0b3-144">Element Elemente (*cshtml* -Dateien), die Eingaben für die Codegenerierung sind.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-144">Item elements (*.cshtml* files) that are inputs to code generation.</span></span> |
| `RazorComponent` | <span data-ttu-id="9b0b3-145">Element Elemente (*Razor* -Dateien), die Eingaben für die Codegenerierung von Razor-Komponenten sind.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-145">Item elements (*.razor* files) that are inputs to Razor component code generation.</span></span> |
| `RazorCompile` | <span data-ttu-id="9b0b3-146">Element Elemente (*CS* -Dateien), die Eingaben für Razor-Kompilierungs Ziele sind.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-146">Item elements (*.cs* files) that are inputs to Razor compilation targets.</span></span> <span data-ttu-id="9b0b3-147">Verwenden Sie diesen `ItemGroup`, um zusätzliche Dateien anzugeben, die in die Razor-Assembly kompiliert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-147">Use this `ItemGroup` to specify additional files to be compiled into the Razor assembly.</span></span> |
| `RazorTargetAssemblyAttribute` | <span data-ttu-id="9b0b3-148">Item-Elemente, die für das Codieren von Generate-Attributen für die Razor-Assembly verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-148">Item elements used to code generate attributes for the Razor assembly.</span></span> <span data-ttu-id="9b0b3-149">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9b0b3-149">For example:</span></span>  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | <span data-ttu-id="9b0b3-150">Elemente, die als eingebettete Ressourcen für die generierte Razor-Assembly hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-150">Item elements added as embedded resources to the generated Razor assembly.</span></span> |

| <span data-ttu-id="9b0b3-151">Die Eigenschaften-</span><span class="sxs-lookup"><span data-stu-id="9b0b3-151">Property</span></span> | <span data-ttu-id="9b0b3-152">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="9b0b3-152">Description</span></span> |
| -------- | ----------- |
| `RazorTargetName` | <span data-ttu-id="9b0b3-153">Dateiname (ohne Erweiterung) der Assembly, die von Razor erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-153">File name (without extension) of the assembly produced by Razor.</span></span> |
| `RazorOutputPath` | <span data-ttu-id="9b0b3-154">Das Razor-Ausgabeverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-154">The Razor output directory.</span></span> |
| `RazorCompileToolset` | <span data-ttu-id="9b0b3-155">Wird verwendet, um das Toolset für die Erstellung der Razor-Assembly zu bestimmen.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-155">Used to determine the toolset used to build the Razor assembly.</span></span> <span data-ttu-id="9b0b3-156">Gültige Werte sind `Implicit`, `RazorSDK` und `PrecompilationTool`.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-156">Valid values are `Implicit`, `RazorSDK`, and `PrecompilationTool`.</span></span> |
| [<span data-ttu-id="9b0b3-157">EnableDefaultContentItems</span><span class="sxs-lookup"><span data-stu-id="9b0b3-157">EnableDefaultContentItems</span></span>](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | <span data-ttu-id="9b0b3-158">Der Standardwert ist `true`.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-158">Default is `true`.</span></span> <span data-ttu-id="9b0b3-159">Wenn `true`, schließt die Dateien *Web. config*, *. JSON*und *. cshtml* als Inhalt in das Projekt ein.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-159">When `true`, includes *web.config*, *.json*, and *.cshtml* files as content in the project.</span></span> <span data-ttu-id="9b0b3-160">Wenn auf die verwiesen wird über `Microsoft.NET.Sdk.Web`, Dateien unter *"Wwwroot"* und Konfigurationsdateien sind ebenfalls enthalten.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-160">When referenced via `Microsoft.NET.Sdk.Web`, files under *wwwroot* and config files are also included.</span></span> |
| `EnableDefaultRazorGenerateItems` | <span data-ttu-id="9b0b3-161">Enthält *CSHTML*-Dateien aus `Content`-Elementen in `RazorGenerate`-Elementen, wenn `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-161">When `true`, includes *.cshtml* files from `Content` items in `RazorGenerate` items.</span></span> |
| `GenerateRazorTargetAssemblyInfo` | <span data-ttu-id="9b0b3-162">Wenn `true`, generiert eine *cs* -Datei mit der Attribute durch `RazorAssemblyAttribute` und die Datei in der Kompilierungsausgabe.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-162">When `true`, generates a *.cs* file containing attributes specified by `RazorAssemblyAttribute` and includes the file in the compile output.</span></span> |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | <span data-ttu-id="9b0b3-163">Fügt einen Standardsatz von Assembly-Attributen zu `RazorAssemblyAttribute` hinzu, wenn `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-163">When `true`, adds a default set of assembly attributes to `RazorAssemblyAttribute`.</span></span> |
| `CopyRazorGenerateFilesToPublishDirectory` | <span data-ttu-id="9b0b3-164">Wenn `true`, Kopien `RazorGenerate` Elemente ( *.cshtml*) Dateien in das Veröffentlichungsverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-164">When `true`, copies `RazorGenerate` items (*.cshtml*) files to the publish directory.</span></span> <span data-ttu-id="9b0b3-165">Razor-Dateien nicht in der Regel für eine veröffentlichte app erforderlich, wenn sie an der Kompilierung zur Buildzeit oder veröffentlichen-Laufzeit beteiligt.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-165">Typically, Razor files aren't required for a published app if they participate in compilation at build-time or publish-time.</span></span> <span data-ttu-id="9b0b3-166">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-166">Defaults to `false`.</span></span> |
| `CopyRefAssembliesToPublishDirectory` | <span data-ttu-id="9b0b3-167">Kopiert Referenzassembly-Elemente in das Veröffentlichungsverzeichnis, wenn `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-167">When `true`, copy reference assembly items to the publish directory.</span></span> <span data-ttu-id="9b0b3-168">In der Regel sind nicht referenzierten Assemblys für eine veröffentlichte app erforderlich, wenn Razor-Kompilierung zur Buildzeit oder veröffentlichen-Laufzeit auftritt.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-168">Typically, reference assemblies aren't required for a published app if Razor compilation occurs at build-time or publish-time.</span></span> <span data-ttu-id="9b0b3-169">Legen Sie auf `true` Ihre veröffentlichte app-Runtime-Kompilierung benötigt.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-169">Set to `true` if your published app requires runtime compilation.</span></span> <span data-ttu-id="9b0b3-170">Legen Sie den Wert beispielsweise auf `true` bei die app geändert *.cshtml* Dateien zur Laufzeit oder eingebettete Ansichten verwendet.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-170">For example, set the value to `true` if the app modifies *.cshtml* files at runtime or uses embedded views.</span></span> <span data-ttu-id="9b0b3-171">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-171">Defaults to `false`.</span></span> |
| `IncludeRazorContentInPack` | <span data-ttu-id="9b0b3-172">Wenn `true`, alle Razor-Inhaltselemente ( *.cshtml* Dateien) für die Aufnahme in das generierte NuGet-Paket gekennzeichnet sind.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-172">When `true`, all Razor content items (*.cshtml* files) are marked for inclusion in the generated NuGet package.</span></span> <span data-ttu-id="9b0b3-173">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-173">Defaults to `false`.</span></span> |
| `EmbedRazorGenerateSources` | <span data-ttu-id="9b0b3-174">Fügt RazorGenerate-Elemente (*CSHTML*-Dateien) als eingebettete Dateien in die generierte Razor-Assembly hinzu, wenn `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-174">When `true`, adds RazorGenerate (*.cshtml*) items as embedded files to the generated Razor assembly.</span></span> <span data-ttu-id="9b0b3-175">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-175">Defaults to `false`.</span></span> |
| `UseRazorBuildServer` | <span data-ttu-id="9b0b3-176">Verwendet einen dauerhaften Buildserverprozess, um die Auslastung durch die Codegenerierung zu verlagern, wenn `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-176">When `true`, uses a persistent build server process to offload code generation work.</span></span> <span data-ttu-id="9b0b3-177">Wird standardmäßig auf den Wert `UseSharedCompilation` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-177">Defaults to the value of `UseSharedCompilation`.</span></span> |
| `GenerateMvcApplicationPartsAssemblyAttributes` | <span data-ttu-id="9b0b3-178">Wenn `true`, generiert das SDK zusätzliche Attribute, die von MVC zur Laufzeit verwendet werden, um die Ermittlung von Anwendungs Teilen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-178">When `true`, the SDK generates additional attributes used by MVC at runtime to perform application part discovery.</span></span> |
| `DefaultWebContentItemExcludes` | <span data-ttu-id="9b0b3-179">Ein globmuster für Element Elemente, die aus der `Content` Element Gruppe in Projekten ausgeschlossen werden sollen, die auf das Web-oder Razor-SDK abzielen.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-179">A globbing pattern for item elements that are to be excluded from the `Content` item group in projects targeting the Web or Razor SDK</span></span> |
| `ExcludeConfigFilesFromBuildOutput` | <span data-ttu-id="9b0b3-180">Wenn `true`, werden *config* -und *JSON* -Dateien nicht in das Buildausgabeverzeichnis kopiert.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-180">When `true`, *.config* and *.json* files do not get copied to the build output directory.</span></span> |
| `AddRazorSupportForMvc` | <span data-ttu-id="9b0b3-181">Wenn `true`, wird das Razor-SDK konfiguriert, um die Unterstützung für die MVC-Konfiguration hinzuzufügen, die beim Entwickeln von Anwendungen mit MVC-Ansichten oder Razor Pages erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-181">When `true`, configures the Razor SDK to add support for the MVC configuration that is required when building applications containing MVC views or Razor Pages.</span></span> <span data-ttu-id="9b0b3-182">Diese Eigenschaft ist implizit für .net Core 3,0-Projekte oder spätere Projekte festgelegt, die auf das Web SDK abzielen.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-182">This property is implicitly set for .NET Core 3.0 or later projects targeting the Web SDK</span></span> |
| `RazorLangVersion` | <span data-ttu-id="9b0b3-183">Die Version der zu zielenden Razor-Sprache.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-183">The version of the Razor Language to target.</span></span> |

<span data-ttu-id="9b0b3-184">Weitere Informationen zu Eigenschaften finden Sie unter [MSBuild-Eigenschaften](/visualstudio/msbuild/msbuild-properties).</span><span class="sxs-lookup"><span data-stu-id="9b0b3-184">For more information on properties, see [MSBuild properties](/visualstudio/msbuild/msbuild-properties).</span></span>

### <a name="targets"></a><span data-ttu-id="9b0b3-185">Ziele</span><span class="sxs-lookup"><span data-stu-id="9b0b3-185">Targets</span></span>

<span data-ttu-id="9b0b3-186">Das Razor SDK definiert zwei Hauptziele:</span><span class="sxs-lookup"><span data-stu-id="9b0b3-186">The Razor SDK defines two primary targets:</span></span>

* <span data-ttu-id="9b0b3-187">`RazorGenerate` &ndash; Generiert Code *cs* Dateien von `RazorGenerate` item-Elemente.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-187">`RazorGenerate` &ndash; Code generates *.cs* files from `RazorGenerate` item elements.</span></span> <span data-ttu-id="9b0b3-188">Verwenden Sie die `RazorGenerateDependsOn`-Eigenschaft, um zusätzliche Ziele anzugeben, die vor oder nach diesem Ziel ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-188">Use the `RazorGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="9b0b3-189">`RazorCompile` &ndash; Kompiliert generiert *cs* Dateien im zu einer Razor-Assembly.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-189">`RazorCompile` &ndash; Compiles generated *.cs* files in to a Razor assembly.</span></span> <span data-ttu-id="9b0b3-190">Verwenden Sie die `RazorCompileDependsOn`, um zusätzliche Ziele anzugeben, die vor oder nach diesem Ziel ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-190">Use the `RazorCompileDependsOn` to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="9b0b3-191">`RazorComponentGenerate` &ndash; Code generiert *CS* -Dateien für `RazorComponent` Element Elemente.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-191">`RazorComponentGenerate` &ndash; Code generates *.cs* files for `RazorComponent` item elements.</span></span> <span data-ttu-id="9b0b3-192">Verwenden Sie die `RazorComponentGenerateDependsOn`-Eigenschaft, um zusätzliche Ziele anzugeben, die vor oder nach diesem Ziel ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-192">Use the `RazorComponentGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>

### <a name="runtime-compilation-of-razor-views"></a><span data-ttu-id="9b0b3-193">Kompilierung von Razor-Ansichten zur Laufzeit</span><span class="sxs-lookup"><span data-stu-id="9b0b3-193">Runtime compilation of Razor views</span></span>

* <span data-ttu-id="9b0b3-194">Standardmäßig veröffentlicht das Razor SDK keine Verweisassemblys, die für das Durchführen der Laufzeitkompilierung erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-194">By default, the Razor SDK doesn't publish reference assemblies that are required to perform runtime compilation.</span></span> <span data-ttu-id="9b0b3-195">Dadurch kommt es zu Kompilierungsfehlern, wenn das Anwendungsmodell von der Laufzeitkompilierung abhängt (z.B. wenn die App eingebettete Ansichten verwendet oder Ansichten nach dem Veröffentlichen der App verändert werden).</span><span class="sxs-lookup"><span data-stu-id="9b0b3-195">This results in compilation failures when the application model relies on runtime compilation&mdash;for example, the app uses embedded views or changes views after the app is published.</span></span> <span data-ttu-id="9b0b3-196">Legen Sie `CopyRefAssembliesToPublishDirectory` auf `true` fest, um mit dem Veröffentlichen von Verweisassemblys fortzufahren.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-196">Set `CopyRefAssembliesToPublishDirectory` to `true` to continue publishing reference assemblies.</span></span>

* <span data-ttu-id="9b0b3-197">Für eine Web-app, stellen Sie sicher auf die Ihre app die `Microsoft.NET.Sdk.Web` SDK.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-197">For a web app, ensure your app is targeting the `Microsoft.NET.Sdk.Web` SDK.</span></span>

## <a name="razor-language-version"></a><span data-ttu-id="9b0b3-198">Razor-Sprachversion</span><span class="sxs-lookup"><span data-stu-id="9b0b3-198">Razor language version</span></span>

<span data-ttu-id="9b0b3-199">Wenn das `Microsoft.NET.Sdk.Web` SDK als Ziel verwendet wird, wird die Razor-Sprachversion von der Ziel Framework-Version der APP abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-199">When targeting the `Microsoft.NET.Sdk.Web` SDK, the Razor language version is inferred from the app's target framework version.</span></span> <span data-ttu-id="9b0b3-200">Für Projekte, die auf das `Microsoft.NET.Sdk.Razor` SDK abzielen, oder in seltenen Fällen, in denen die APP eine andere Version der Razor-Sprache benötigt als der abherzustehende Wert, kann eine Version konfiguriert werden, indem die `<RazorLangVersion>`-Eigenschaft in der Projektdatei der APP festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="9b0b3-200">For projects targeting the `Microsoft.NET.Sdk.Razor` SDK or in the rare case that the app requires a different Razor language version than the inferred value, a version can be configured by setting the `<RazorLangVersion>` property in the app's project file:</span></span>

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

<span data-ttu-id="9b0b3-201">Die Sprachversion von Razor ist eng in die Version der Laufzeit integriert, für die Sie erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-201">Razor's language version is tightly integrated with the version of the runtime that it was built for.</span></span> <span data-ttu-id="9b0b3-202">Das Ziel einer Sprachversion, die nicht für die Laufzeit konzipiert ist, wird nicht unterstützt und erzeugt wahrscheinlich Buildfehler.</span><span class="sxs-lookup"><span data-stu-id="9b0b3-202">Targeting a language version that isn't designed for the runtime is unsupported and likely produces build errors.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9b0b3-203">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="9b0b3-203">Additional resources</span></span>

* [<span data-ttu-id="9b0b3-204">Erweiterungen des CSPROJ-Formats für .NET Core</span><span class="sxs-lookup"><span data-stu-id="9b0b3-204">Additions to the csproj format for .NET Core</span></span>](/dotnet/core/tools/csproj)
* [<span data-ttu-id="9b0b3-205">Gemeinsame MSBuild-Projektelemente</span><span class="sxs-lookup"><span data-stu-id="9b0b3-205">Common MSBuild project items</span></span>](/visualstudio/msbuild/common-msbuild-project-items)

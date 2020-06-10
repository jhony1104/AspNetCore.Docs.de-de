---
title: "title: 'ASP.NET Core Razor SDK' author: description: 'So können Razor Pages in ASP.NET Core Ihre Codepageszenarios einfacher und produktiver gestalten als MVC.'"
author: Rick-Anderson
description: 'monikerRange: ms.author: ms.custom: ms.date: no-loc:'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/sdk
ms.openlocfilehash: 555965b96b0d4d541c70f8a137d4f098dc06a4d6
ms.sourcegitcommit: 58722eb309767e462bdbf3082bd38737a4ef168f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84106623"
---
# <a name="aspnet-core-razor-sdk"></a><span data-ttu-id="66fe9-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="66fe9-103">'Blazor'</span></span>

<span data-ttu-id="66fe9-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="66fe9-104">'Identity'</span></span>

## <a name="overview"></a><span data-ttu-id="66fe9-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="66fe9-105">'Let's Encrypt'</span></span>

<span data-ttu-id="66fe9-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="66fe9-106">'Razor'</span></span> <span data-ttu-id="66fe9-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="66fe9-107">'SignalR' uid:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="66fe9-108">ASP.NET Core Razor SDK</span><span class="sxs-lookup"><span data-stu-id="66fe9-108">ASP.NET Core Razor SDK</span></span>
* <span data-ttu-id="66fe9-109">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="66fe9-109">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="66fe9-110">Übersicht</span><span class="sxs-lookup"><span data-stu-id="66fe9-110">Overview</span></span> <span data-ttu-id="66fe9-111">Das [!INCLUDE[](~/includes/2.1-SDK.md)] enthält das MSBuild SDK `Microsoft.NET.Sdk.Razor` (Razor SDK).</span><span class="sxs-lookup"><span data-stu-id="66fe9-111">The [!INCLUDE[](~/includes/2.1-SDK.md)] includes the `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="66fe9-112">Das Razor SDK...</span><span class="sxs-lookup"><span data-stu-id="66fe9-112">The Razor SDK:</span></span>
* <span data-ttu-id="66fe9-113">ist zum Erstellen, Packen und Veröffentlichen von Paketen erforderlich, die [Razor](xref:mvc/views/razor)-Dateien für ASP.NET Core MVC-basierte oder [Blazor](xref:blazor/index)-Projekte enthalten.</span><span class="sxs-lookup"><span data-stu-id="66fe9-113">Is required to build, package, and publish projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based or [Blazor](xref:blazor/index) projects.</span></span>

<span data-ttu-id="66fe9-114">enthält vordefinierte Ziele, Eigenschaften und Elemente, die das Anpassen der Razor-Dateien (*CSHTML* oder *RAZOR*) ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="66fe9-114">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor (*.cshtml* or *.razor*) files.</span></span> <span data-ttu-id="66fe9-115">Das Razor SDK enthält `Content`-Elemente mit `Include`-Attributen, die auf die `**\*.cshtml`- und `**\*.razor`-Globmuster festgelegt sind.</span><span class="sxs-lookup"><span data-stu-id="66fe9-115">The Razor SDK includes `Content` items with `Include` attributes set to the `**\*.cshtml` and `**\*.razor` globbing patterns.</span></span>

::: moniker-end

## <a name="prerequisites"></a><span data-ttu-id="66fe9-116">Übereinstimmende Dateien werden veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="66fe9-116">Matching files are published.</span></span>

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a><span data-ttu-id="66fe9-117">Standardisiert die Vorgänge zum Erstellen, Packen und Veröffentlichen von Projekten, die [Razor](xref:mvc/views/razor)-Dateien für ASP.NET Core MVC-basierte Projekte enthalten.</span><span class="sxs-lookup"><span data-stu-id="66fe9-117">Standardizes the experience around building, packaging, and publishing projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based projects.</span></span>

<span data-ttu-id="66fe9-118">Enthält vordefinierte Ziele, Eigenschaften und Elemente, die das Anpassen der Razor-Dateien ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="66fe9-118">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor files.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="66fe9-119">Das Razor SDK enthält ein `Content`-Element mit einem `Include`-Attribut, das auf das `**\*.cshtml`-Globmuster festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="66fe9-119">The Razor SDK includes a `Content` item with an `Include` attribute set to the `**\*.cshtml` globbing pattern.</span></span> <span data-ttu-id="66fe9-120">Übereinstimmende Dateien werden veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="66fe9-120">Matching files are published.</span></span> <span data-ttu-id="66fe9-121">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="66fe9-121">Prerequisites</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="66fe9-122">Verwenden des Razor SDK</span><span class="sxs-lookup"><span data-stu-id="66fe9-122">Use the Razor SDK</span></span>

* <span data-ttu-id="66fe9-123">Die meisten Web-Apps müssen nicht explizit auf das Razor SDK verweisen.</span><span class="sxs-lookup"><span data-stu-id="66fe9-123">Most web apps aren't required to explicitly reference the Razor SDK.</span></span>

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* <span data-ttu-id="66fe9-124">Damit Sie das Razor SDK zur Erstellung von Klassenbibliotheken mit Razor Views oder Razor Pages verwenden können, wird empfohlen, mit der Projektvorlage für die Razor-Klassenbibliothek (Razor Class Library, RCL) zu beginnen.</span><span class="sxs-lookup"><span data-stu-id="66fe9-124">To use the Razor SDK to build class libraries containing Razor views or Razor Pages, we recommend starting with the Razor class library (RCL) project template.</span></span> <span data-ttu-id="66fe9-125">Eine RCL, die zum Erstellen von Blazor-Dateien (*RAZOR*) verwendet wird, erfordert mindestens einen Verweis auf das Paket [Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components).</span><span class="sxs-lookup"><span data-stu-id="66fe9-125">An RCL that's used to build Blazor (*.razor*) files minimally requires a reference to the [Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) package.</span></span>

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  <span data-ttu-id="66fe9-126">Eine RCL, die zum Erstellen von Razor Views oder Razor Pages (*CSHTML-Dateien*) verwendet wird, erfordert mindestens die Ausrichtung auf `netcoreapp3.0` oder höher und verfügt über ein `FrameworkReference`-Element für das [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) in der Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="66fe9-126">An RCL that's used to build Razor views or pages (*.cshtml* files) minimally requires targeting `netcoreapp3.0` or later and has a `FrameworkReference` to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in its project file.</span></span>

  <span data-ttu-id="66fe9-127">So verwenden Sie das Razor SDK zum Erstellen von Klassenbibliotheken, die Razor Views oder Razor Pages enthalten:</span><span class="sxs-lookup"><span data-stu-id="66fe9-127">To use the Razor SDK to build class libraries containing Razor views or Razor Pages:</span></span> <span data-ttu-id="66fe9-128">Verwenden Sie `Microsoft.NET.Sdk.Razor` anstelle von `Microsoft.NET.Sdk`:</span><span class="sxs-lookup"><span data-stu-id="66fe9-128">Use `Microsoft.NET.Sdk.Razor` instead of `Microsoft.NET.Sdk`:</span></span>

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> <span data-ttu-id="66fe9-129">In der Regel wird ein Paketverweis auf `Microsoft.AspNetCore.Mvc` benötigt, um zusätzliche Abhängigkeiten zum Erstellen und Kompilieren von Razor Pages und Razor Views zu empfangen.</span><span class="sxs-lookup"><span data-stu-id="66fe9-129">Typically, a package reference to `Microsoft.AspNetCore.Mvc` is required to receive additional dependencies that are required to build and compile Razor Pages and Razor views.</span></span> <span data-ttu-id="66fe9-130">Ihr Projekt sollte mindestens Paketverweise zu Folgendem hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="66fe9-130">At a minimum, your project should add package references to:</span></span> <span data-ttu-id="66fe9-131">Das `Microsoft.AspNetCore.Razor.Design`-Paket enthält die Aufgaben und Ziele der Razor-Kompilierung für das Projekt.</span><span class="sxs-lookup"><span data-stu-id="66fe9-131">The `Microsoft.AspNetCore.Razor.Design` package provides the Razor compilation tasks and targets for the project.</span></span> <span data-ttu-id="66fe9-132">Diese Pakete sind in `Microsoft.AspNetCore.Mvc` enthalten.</span><span class="sxs-lookup"><span data-stu-id="66fe9-132">The preceding packages are included in `Microsoft.AspNetCore.Mvc`.</span></span>

::: moniker-end

### <a name="properties"></a><span data-ttu-id="66fe9-133">Das folgende Markup zeigt eine Projektdatei, die das Razor SDK verwendet, um Razor-Dateien für eine Razor Pages-App in ASP.NET Core zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="66fe9-133">The following markup shows a project file that uses the Razor SDK to build Razor files for an ASP.NET Core Razor Pages app:</span></span>

<span data-ttu-id="66fe9-134">Die Pakete `Microsoft.AspNetCore.Razor.Design` und `Microsoft.AspNetCore.Mvc.Razor.Extensions` sind im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="66fe9-134">The `Microsoft.AspNetCore.Razor.Design` and `Microsoft.AspNetCore.Mvc.Razor.Extensions` packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

* <span data-ttu-id="66fe9-135">Der versionslose Paketverweis `Microsoft.AspNetCore.App` bietet jedoch ein Metapaket für die App, das nicht die neueste Version von `Microsoft.AspNetCore.Razor.Design` enthält.</span><span class="sxs-lookup"><span data-stu-id="66fe9-135">However, the version-less `Microsoft.AspNetCore.App` package reference provides a metapackage to the app that doesn't include the latest version of `Microsoft.AspNetCore.Razor.Design`.</span></span> <span data-ttu-id="66fe9-136">Projekte müssen auf eine konsistente Version von `Microsoft.AspNetCore.Razor.Design` (oder `Microsoft.AspNetCore.Mvc`) verweisen, sodass die neuesten Buildzeitfehlerbehebungen für Razor enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="66fe9-136">Projects must reference a consistent version of `Microsoft.AspNetCore.Razor.Design` (or `Microsoft.AspNetCore.Mvc`) so that the latest build-time fixes for Razor are included.</span></span>
* <span data-ttu-id="66fe9-137">Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/aspnet/Razor/issues/2553).</span><span class="sxs-lookup"><span data-stu-id="66fe9-137">For more information, see [this GitHub issue](https://github.com/aspnet/Razor/issues/2553).</span></span> <span data-ttu-id="66fe9-138">Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="66fe9-138">Properties</span></span>

<span data-ttu-id="66fe9-139">Die folgenden Eigenschaften steuern das Verhalten des Razor SDK im Rahmen eines Projektbuilds:</span><span class="sxs-lookup"><span data-stu-id="66fe9-139">The following properties control the Razor's SDK behavior as part of a project build:</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="66fe9-140">`RazorCompileOnBuild`: Kompiliert die Razor-Assembly im Rahmen der Projekterstellung und gibt sie aus, wenn `true` festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="66fe9-140">`RazorCompileOnBuild`: When `true`, compiles and emits the Razor assembly as part of building the project.</span></span> <span data-ttu-id="66fe9-141">Wird standardmäßig auf `true` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="66fe9-141">Defaults to `true`.</span></span>

::: moniker-end

| <span data-ttu-id="66fe9-142">`RazorCompileOnPublish`: Kompiliert die Razor-Assembly im Rahmen der Projektveröffentlichung und gibt sie aus, wenn `true` festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="66fe9-142">`RazorCompileOnPublish`: When `true`, compiles and emits the Razor assembly as part of publishing the project.</span></span> | <span data-ttu-id="66fe9-143">Wird standardmäßig auf `true` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="66fe9-143">Defaults to `true`.</span></span> |
| ----- | ----------- |
| `RazorGenerate` | <span data-ttu-id="66fe9-144">Die Eigenschaften und Elemente in der folgenden Tabelle werden zum Konfigurieren der Eingabe und Ausgabe an das Razor SDK verwendet.</span><span class="sxs-lookup"><span data-stu-id="66fe9-144">The properties and items in the following table are used to configure inputs and output to the Razor SDK.</span></span> |
| `RazorComponent` | <span data-ttu-id="66fe9-145">Ab ASP.NET Core 3.0 werden MVC Views oder Razor Pages standardmäßig nicht mehr verarbeitet, wenn die MSBuild-Eigenschaften `RazorCompileOnBuild` oder `RazorCompileOnPublish` in der Projektdatei deaktiviert sind.</span><span class="sxs-lookup"><span data-stu-id="66fe9-145">Starting with ASP.NET Core 3.0, MVC Views or Razor Pages aren't served by default if the `RazorCompileOnBuild` or `RazorCompileOnPublish` MSBuild properties in the project file are disabled.</span></span> |
| `RazorCompile` | <span data-ttu-id="66fe9-146">Anwendungen müssen einen expliziten Verweis auf das Paket [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) hinzufügen, wenn die App auf die Laufzeitkompilierung angewiesen ist, um *CSHTML-Dateien* zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="66fe9-146">Applications must add an explicit reference to the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) package if the app relies on runtime compilation to process *.cshtml* files.</span></span> <span data-ttu-id="66fe9-147">Elemente</span><span class="sxs-lookup"><span data-stu-id="66fe9-147">Items</span></span> |
| `RazorTargetAssemblyAttribute` | <span data-ttu-id="66fe9-148">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="66fe9-148">Description</span></span> <span data-ttu-id="66fe9-149">title: 'ASP.NET Core Razor SDK' author: description: 'So können Razor Pages in ASP.NET Core Ihre Codepageszenarios einfacher und produktiver gestalten als MVC.'</span><span class="sxs-lookup"><span data-stu-id="66fe9-149">title: 'ASP.NET Core Razor SDK' author: description: 'Learn how Razor Pages in ASP.NET Core makes coding page-focused scenarios easier and more productive than using MVC.'</span></span>  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | <span data-ttu-id="66fe9-150">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="66fe9-150">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> |

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="66fe9-151">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="66fe9-151">'Blazor'</span></span> | <span data-ttu-id="66fe9-152">'Identity'</span><span class="sxs-lookup"><span data-stu-id="66fe9-152">'Identity'</span></span> |
| -------- | ----------- |
| `RazorTargetName` | <span data-ttu-id="66fe9-153">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="66fe9-153">'Let's Encrypt'</span></span> |
| `RazorOutputPath` | <span data-ttu-id="66fe9-154">'Razor'</span><span class="sxs-lookup"><span data-stu-id="66fe9-154">'Razor'</span></span> |
| `RazorCompileToolset` | <span data-ttu-id="66fe9-155">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="66fe9-155">'SignalR' uid:</span></span> <span data-ttu-id="66fe9-156">title: 'ASP.NET Core Razor SDK' author: description: 'So können Razor Pages in ASP.NET Core Ihre Codepageszenarios einfacher und produktiver gestalten als MVC.'</span><span class="sxs-lookup"><span data-stu-id="66fe9-156">title: 'ASP.NET Core Razor SDK' author: description: 'Learn how Razor Pages in ASP.NET Core makes coding page-focused scenarios easier and more productive than using MVC.'</span></span> |
| <span data-ttu-id="66fe9-157">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="66fe9-157">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> | <span data-ttu-id="66fe9-158">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="66fe9-158">'Blazor'</span></span> <span data-ttu-id="66fe9-159">'Identity'</span><span class="sxs-lookup"><span data-stu-id="66fe9-159">'Identity'</span></span> <span data-ttu-id="66fe9-160">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="66fe9-160">'Let's Encrypt'</span></span> |
| `EnableDefaultRazorGenerateItems` | <span data-ttu-id="66fe9-161">'Razor'</span><span class="sxs-lookup"><span data-stu-id="66fe9-161">'Razor'</span></span> |
| `GenerateRazorTargetAssemblyInfo` | <span data-ttu-id="66fe9-162">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="66fe9-162">'SignalR' uid:</span></span> |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | <span data-ttu-id="66fe9-163">title: 'ASP.NET Core Razor SDK' author: description: 'So können Razor Pages in ASP.NET Core Ihre Codepageszenarios einfacher und produktiver gestalten als MVC.'</span><span class="sxs-lookup"><span data-stu-id="66fe9-163">title: 'ASP.NET Core Razor SDK' author: description: 'Learn how Razor Pages in ASP.NET Core makes coding page-focused scenarios easier and more productive than using MVC.'</span></span> |
| `CopyRazorGenerateFilesToPublishDirectory` | <span data-ttu-id="66fe9-164">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="66fe9-164">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="66fe9-165">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="66fe9-165">'Blazor'</span></span> <span data-ttu-id="66fe9-166">'Identity'</span><span class="sxs-lookup"><span data-stu-id="66fe9-166">'Identity'</span></span> |
| `PreserveCompilationReferences` | <span data-ttu-id="66fe9-167">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="66fe9-167">'Let's Encrypt'</span></span> <span data-ttu-id="66fe9-168">'Razor'</span><span class="sxs-lookup"><span data-stu-id="66fe9-168">'Razor'</span></span> <span data-ttu-id="66fe9-169">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="66fe9-169">'SignalR' uid:</span></span> <span data-ttu-id="66fe9-170">------ | | `RazorGenerate` | Item-Elemente (*CSHTML-Dateien*), die Eingaben für die Codegenerierung sind.</span><span class="sxs-lookup"><span data-stu-id="66fe9-170">------ | | `RazorGenerate` | Item elements (*.cshtml* files) that are inputs to code generation.</span></span> <span data-ttu-id="66fe9-171">| | `RazorComponent` | Item-Elemente (*RAZOR-Dateien*), die Eingaben für die Codegenerierung für Razor-Komponenten sind.</span><span class="sxs-lookup"><span data-stu-id="66fe9-171">| | `RazorComponent` | Item elements (*.razor* files) that are inputs to Razor component code generation.</span></span> |
| `IncludeRazorContentInPack` | <span data-ttu-id="66fe9-172">| | `RazorCompile` | Item-Elemente (*CS-Dateien*), die Eingaben für Razor-Kompilierungsziele sind.</span><span class="sxs-lookup"><span data-stu-id="66fe9-172">| | `RazorCompile` | Item elements (*.cs* files) that are inputs to Razor compilation targets.</span></span> <span data-ttu-id="66fe9-173">Verwenden Sie dieses `ItemGroup`-Element, um zusätzliche Dateien für die Kompilierung in die Razor-Assembly anzugeben.</span><span class="sxs-lookup"><span data-stu-id="66fe9-173">Use this `ItemGroup` to specify additional files to be compiled into the Razor assembly.</span></span> |
| `EmbedRazorGenerateSources` | <span data-ttu-id="66fe9-174">| | `RazorTargetAssemblyAttribute` | Item-Elemente, die für das Programmieren von Generate-Attributen für die Razor-Assembly verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="66fe9-174">| | `RazorTargetAssemblyAttribute` | Item elements used to code generate attributes for the Razor assembly.</span></span> <span data-ttu-id="66fe9-175">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66fe9-175">For example:</span></span> |
| `UseRazorBuildServer` | <span data-ttu-id="66fe9-176">`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` | | `RazorEmbeddedResource` | Item-Elemente, die als eingebettete Ressourcen in die Razor-Assembly hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="66fe9-176">`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` | | `RazorEmbeddedResource` | Item elements added as embedded resources to the generated Razor assembly.</span></span> <span data-ttu-id="66fe9-177">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="66fe9-177">Property</span></span> |
| `GenerateMvcApplicationPartsAssemblyAttributes` | <span data-ttu-id="66fe9-178">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="66fe9-178">Description</span></span> |
| `DefaultWebContentItemExcludes` | <span data-ttu-id="66fe9-179">title: 'ASP.NET Core Razor SDK' author: description: 'So können Razor Pages in ASP.NET Core Ihre Codepageszenarios einfacher und produktiver gestalten als MVC.'</span><span class="sxs-lookup"><span data-stu-id="66fe9-179">title: 'ASP.NET Core Razor SDK' author: description: 'Learn how Razor Pages in ASP.NET Core makes coding page-focused scenarios easier and more productive than using MVC.'</span></span> |
| `ExcludeConfigFilesFromBuildOutput` | <span data-ttu-id="66fe9-180">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="66fe9-180">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> |
| `AddRazorSupportForMvc` | <span data-ttu-id="66fe9-181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="66fe9-181">'Blazor'</span></span> <span data-ttu-id="66fe9-182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="66fe9-182">'Identity'</span></span> |
| `RazorLangVersion` | <span data-ttu-id="66fe9-183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="66fe9-183">'Let's Encrypt'</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="66fe9-184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="66fe9-184">'Razor'</span></span> | <span data-ttu-id="66fe9-185">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="66fe9-185">'SignalR' uid:</span></span> |
| -------- | ----------- |
| `RazorTargetName` | <span data-ttu-id="66fe9-186">title: 'ASP.NET Core Razor SDK' author: description: 'So können Razor Pages in ASP.NET Core Ihre Codepageszenarios einfacher und produktiver gestalten als MVC.'</span><span class="sxs-lookup"><span data-stu-id="66fe9-186">title: 'ASP.NET Core Razor SDK' author: description: 'Learn how Razor Pages in ASP.NET Core makes coding page-focused scenarios easier and more productive than using MVC.'</span></span> |
| `RazorOutputPath` | <span data-ttu-id="66fe9-187">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="66fe9-187">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> |
| `RazorCompileToolset` | <span data-ttu-id="66fe9-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="66fe9-188">'Blazor'</span></span> <span data-ttu-id="66fe9-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="66fe9-189">'Identity'</span></span> |
| <span data-ttu-id="66fe9-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="66fe9-190">'Let's Encrypt'</span></span> | <span data-ttu-id="66fe9-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="66fe9-191">'Razor'</span></span> <span data-ttu-id="66fe9-192">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="66fe9-192">'SignalR' uid:</span></span> <span data-ttu-id="66fe9-193">---- | --- title: 'ASP.NET Core Razor SDK' author: description: 'So können Razor Pages in ASP.NET Core Ihre Codepageszenarios einfacher und produktiver gestalten als MVC.'</span><span class="sxs-lookup"><span data-stu-id="66fe9-193">---- | --- title: 'ASP.NET Core Razor SDK' author: description: 'Learn how Razor Pages in ASP.NET Core makes coding page-focused scenarios easier and more productive than using MVC.'</span></span> |
| `EnableDefaultRazorGenerateItems` | <span data-ttu-id="66fe9-194">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="66fe9-194">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> |
| `GenerateRazorTargetAssemblyInfo` | <span data-ttu-id="66fe9-195">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="66fe9-195">'Blazor'</span></span> |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | <span data-ttu-id="66fe9-196">'Identity'</span><span class="sxs-lookup"><span data-stu-id="66fe9-196">'Identity'</span></span> |
| `CopyRazorGenerateFilesToPublishDirectory` | <span data-ttu-id="66fe9-197">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="66fe9-197">'Let's Encrypt'</span></span> <span data-ttu-id="66fe9-198">'Razor'</span><span class="sxs-lookup"><span data-stu-id="66fe9-198">'Razor'</span></span> <span data-ttu-id="66fe9-199">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="66fe9-199">'SignalR' uid:</span></span> |
| `CopyRefAssembliesToPublishDirectory` | <span data-ttu-id="66fe9-200">title: 'ASP.NET Core Razor SDK' author: description: 'So können Razor Pages in ASP.NET Core Ihre Codepageszenarios einfacher und produktiver gestalten als MVC.'</span><span class="sxs-lookup"><span data-stu-id="66fe9-200">title: 'ASP.NET Core Razor SDK' author: description: 'Learn how Razor Pages in ASP.NET Core makes coding page-focused scenarios easier and more productive than using MVC.'</span></span> <span data-ttu-id="66fe9-201">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="66fe9-201">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="66fe9-202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="66fe9-202">'Blazor'</span></span> <span data-ttu-id="66fe9-203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="66fe9-203">'Identity'</span></span> <span data-ttu-id="66fe9-204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="66fe9-204">'Let's Encrypt'</span></span> |
| `IncludeRazorContentInPack` | <span data-ttu-id="66fe9-205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="66fe9-205">'Razor'</span></span> <span data-ttu-id="66fe9-206">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="66fe9-206">'SignalR' uid:</span></span> |
| `EmbedRazorGenerateSources` | <span data-ttu-id="66fe9-207">title: 'ASP.NET Core Razor SDK' author: description: 'So können Razor Pages in ASP.NET Core Ihre Codepageszenarios einfacher und produktiver gestalten als MVC.'</span><span class="sxs-lookup"><span data-stu-id="66fe9-207">title: 'ASP.NET Core Razor SDK' author: description: 'Learn how Razor Pages in ASP.NET Core makes coding page-focused scenarios easier and more productive than using MVC.'</span></span> <span data-ttu-id="66fe9-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="66fe9-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> |
| `UseRazorBuildServer` | <span data-ttu-id="66fe9-209">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="66fe9-209">'Blazor'</span></span> <span data-ttu-id="66fe9-210">'Identity'</span><span class="sxs-lookup"><span data-stu-id="66fe9-210">'Identity'</span></span> |
| `GenerateMvcApplicationPartsAssemblyAttributes` | <span data-ttu-id="66fe9-211">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="66fe9-211">'Let's Encrypt'</span></span> |
| `DefaultWebContentItemExcludes` | <span data-ttu-id="66fe9-212">'Razor'</span><span class="sxs-lookup"><span data-stu-id="66fe9-212">'Razor'</span></span> |
| `ExcludeConfigFilesFromBuildOutput` | <span data-ttu-id="66fe9-213">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="66fe9-213">'SignalR' uid:</span></span> |
| `AddRazorSupportForMvc` | <span data-ttu-id="66fe9-214">------ | | `RazorTargetName` | Dateiname (ohne Erweiterung) der Assembly, die von Razor erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="66fe9-214">------ | | `RazorTargetName` | File name (without extension) of the assembly produced by Razor.</span></span> <span data-ttu-id="66fe9-215">| | `RazorOutputPath` | Das Razor-Ausgabeverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="66fe9-215">| | `RazorOutputPath` | The Razor output directory.</span></span> |
| `RazorLangVersion` | <span data-ttu-id="66fe9-216">| | `RazorCompileToolset` | Wird verwendet, um das Toolset für die Erstellung der Razor-Assembly zu bestimmen.</span><span class="sxs-lookup"><span data-stu-id="66fe9-216">| | `RazorCompileToolset` | Used to determine the toolset used to build the Razor assembly.</span></span> |

::: moniker-end

<span data-ttu-id="66fe9-217">Gültige Werte sind `Implicit`, `RazorSDK` und `PrecompilationTool`.</span><span class="sxs-lookup"><span data-stu-id="66fe9-217">Valid values are `Implicit`, `RazorSDK`, and `PrecompilationTool`.</span></span>

### <a name="targets"></a><span data-ttu-id="66fe9-218">| | [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Der Standardwert ist `true`.</span><span class="sxs-lookup"><span data-stu-id="66fe9-218">| | [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Default is `true`.</span></span>

<span data-ttu-id="66fe9-219">Bei `true` werden *web.config*-, *JSON*- und *CSHTML*-Dateien als Inhalt in das Projekt einbezogen.</span><span class="sxs-lookup"><span data-stu-id="66fe9-219">When `true`, includes *web.config*, *.json*, and *.cshtml* files as content in the project.</span></span>

* <span data-ttu-id="66fe9-220">Bei einem Verweis über `Microsoft.NET.Sdk.Web` sind auch Dateien unter *wwwroot* und Konfigurationsdateien enthalten.</span><span class="sxs-lookup"><span data-stu-id="66fe9-220">When referenced via `Microsoft.NET.Sdk.Web`, files under *wwwroot* and config files are also included.</span></span> <span data-ttu-id="66fe9-221">| | `EnableDefaultRazorGenerateItems` | Enthält *CSHTML-Dateien* aus `Content`-Elementen in `RazorGenerate`-Elementen, wenn `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="66fe9-221">| | `EnableDefaultRazorGenerateItems` | When `true`, includes *.cshtml* files from `Content` items in `RazorGenerate` items.</span></span>
* <span data-ttu-id="66fe9-222">| | `GenerateRazorTargetAssemblyInfo` | Generiert eine *CS-Datei*, die von `RazorAssemblyAttribute` angegebene Attribute enthält, und schließt die Datei in der Kompilierungsausgabe ein, wenn `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="66fe9-222">| | `GenerateRazorTargetAssemblyInfo` | When `true`, generates a *.cs* file containing attributes specified by `RazorAssemblyAttribute` and includes the file in the compile output.</span></span> <span data-ttu-id="66fe9-223">| | `EnableDefaultRazorTargetAssemblyInfoAttributes` | Fügt Standardattribute für Assemblys zu `RazorAssemblyAttribute` hinzu, wenn `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="66fe9-223">| | `EnableDefaultRazorTargetAssemblyInfoAttributes` | When `true`, adds a default set of assembly attributes to `RazorAssemblyAttribute`.</span></span>
* <span data-ttu-id="66fe9-224">| | `CopyRazorGenerateFilesToPublishDirectory` | Kopiert `RazorGenerate`-Elemente (*CSHTML-Dateien*) in das Veröffentlichungsverzeichnis, wenn `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="66fe9-224">| | `CopyRazorGenerateFilesToPublishDirectory` | When `true`, copies `RazorGenerate` items (*.cshtml*) files to the publish directory.</span></span> <span data-ttu-id="66fe9-225">In der Regel sind Razor-Dateien nicht für eine veröffentlichte App erforderlich, wenn sie an der Kompilierung zum Zeitpunkt der Erstellung oder Veröffentlichung beteiligt sind.</span><span class="sxs-lookup"><span data-stu-id="66fe9-225">Typically, Razor files aren't required for a published app if they participate in compilation at build-time or publish-time.</span></span>

### <a name="runtime-compilation-of-razor-views"></a><span data-ttu-id="66fe9-226">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="66fe9-226">Defaults to `false`.</span></span>

* <span data-ttu-id="66fe9-227">| | `PreserveCompilationReferences` | Kopiert Referenzassembly-Elemente in das Veröffentlichungsverzeichnis, wenn `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="66fe9-227">| | `PreserveCompilationReferences` | When `true`, copy reference assembly items to the publish directory.</span></span> <span data-ttu-id="66fe9-228">In der Regel sind Referenzassemblys nicht für eine veröffentlichte App erforderlich, wenn die Razor-Kompilierung zum Zeitpunkt der Erstellung oder Veröffentlichung stattfindet.</span><span class="sxs-lookup"><span data-stu-id="66fe9-228">Typically, reference assemblies aren't required for a published app if Razor compilation occurs at build-time or publish-time.</span></span> <span data-ttu-id="66fe9-229">Legen Sie den Wert `true` fest, wenn Ihre veröffentlichte App eine Laufzeitkompilierung erfordert.</span><span class="sxs-lookup"><span data-stu-id="66fe9-229">Set to `true` if your published app requires runtime compilation.</span></span>

* <span data-ttu-id="66fe9-230">Legen Sie z. B. den Wert auf `true` fest, wenn die App zur Laufzeit *CSHTML*-Dateien ändert oder eingebettete Ansichten verwendet.</span><span class="sxs-lookup"><span data-stu-id="66fe9-230">For example, set the value to `true` if the app modifies *.cshtml* files at runtime or uses embedded views.</span></span>

## <a name="razor-language-version"></a><span data-ttu-id="66fe9-231">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="66fe9-231">Defaults to `false`.</span></span>

<span data-ttu-id="66fe9-232">| | `IncludeRazorContentInPack` | Alle Razor-Inhaltselemente (*CSHTML-Dateien*) werden für die Aufnahme in das generierte NuGet-Paket markiert, wenn `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="66fe9-232">| | `IncludeRazorContentInPack` | When `true`, all Razor content items (*.cshtml* files) are marked for inclusion in the generated NuGet package.</span></span> <span data-ttu-id="66fe9-233">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="66fe9-233">Defaults to `false`.</span></span>

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

<span data-ttu-id="66fe9-234">| | `EmbedRazorGenerateSources` | Fügt RazorGenerate-Elemente (*CSHTML-Dateien*) als eingebettete Dateien in die generierte Razor-Assembly hinzu, wenn `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="66fe9-234">| | `EmbedRazorGenerateSources` | When `true`, adds RazorGenerate (*.cshtml*) items as embedded files to the generated Razor assembly.</span></span> <span data-ttu-id="66fe9-235">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="66fe9-235">Defaults to `false`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="66fe9-236">| | `UseRazorBuildServer` | Verwendet einen dauerhaften Buildserverprozess, um die Auslastung durch die Codegenerierung zu verlagern, wenn `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="66fe9-236">| | `UseRazorBuildServer` | When `true`, uses a persistent build server process to offload code generation work.</span></span>

* <span data-ttu-id="66fe9-237">Wird standardmäßig auf den Wert `UseSharedCompilation` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="66fe9-237">Defaults to the value of `UseSharedCompilation`.</span></span>
* <span data-ttu-id="66fe9-238">| | `GenerateMvcApplicationPartsAssemblyAttributes` | Das SDK generiert zusätzliche Attribute, die von MVC zur Laufzeit zur Durchführung der Anwendungsteilerkennung verwendet werden, wenn `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="66fe9-238">| | `GenerateMvcApplicationPartsAssemblyAttributes` | When `true`, the SDK generates additional attributes used by MVC at runtime to perform application part discovery.</span></span>

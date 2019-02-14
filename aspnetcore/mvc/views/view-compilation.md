---
title: Kompilierung und Vorkompilierung einer Razor-Datei in ASP.NET Core
author: rick-anderson
description: Erfahren Sie mehr über die Vorteile der Vorkompilierung von Razor-Dateien und über das Erreichen der Vorkompilierung einer Razor-Datei in einer ASP.NET Core-App.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/13/2019
uid: mvc/views/view-compilation
ms.openlocfilehash: c4e8f722fdf3d3f64807cc35ff9f349af7f32abd
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248185"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="abe65-103">Kompilieren einer Razor-Datei in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="abe65-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="abe65-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="abe65-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range="= aspnetcore-1.1"

<span data-ttu-id="abe65-105">Eine Razor-Datei wird zur Laufzeit kompiliert, wenn die zugeordnete MVC-Ansicht aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="abe65-105">A Razor file is compiled at runtime, when the associated MVC view is invoked.</span></span> <span data-ttu-id="abe65-106">Die Veröffentlichung von Razor-Dateien zum Zeitpunkt der Erstellung wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="abe65-106">Build-time Razor file publishing is unsupported.</span></span> <span data-ttu-id="abe65-107">Razor-Dateien können optional zur Veröffentlichung kompiliert und mit der App über das Vorkompilierungstool bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="abe65-107">Razor files can optionally be compiled at publish time and deployed with the app&mdash;using the precompilation tool.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="abe65-108">Eine Razor-Datei wird zur Laufzeit kompiliert, wenn die zugeordnete Razor-Seite oder MVC-Ansicht aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="abe65-108">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="abe65-109">Die Veröffentlichung von Razor-Dateien zum Zeitpunkt der Erstellung wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="abe65-109">Build-time Razor file publishing is unsupported.</span></span> <span data-ttu-id="abe65-110">Razor-Dateien können optional zur Veröffentlichung kompiliert und mit der App über das Vorkompilierungstool bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="abe65-110">Razor files can optionally be compiled at publish time and deployed with the app&mdash;using the precompilation tool.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="abe65-111">Eine Razor-Datei wird zur Laufzeit kompiliert, wenn die zugeordnete Razor-Seite oder MVC-Ansicht aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="abe65-111">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="abe65-112">Razor-Dateien werden mit dem [Razor SDK](xref:razor-pages/sdk) zur Erstellung und zur Veröffentlichung kompiliert.</span><span class="sxs-lookup"><span data-stu-id="abe65-112">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

::: moniker-end

## <a name="precompilation-considerations"></a><span data-ttu-id="abe65-113">Was vor der Kompilierung beachtet werden muss</span><span class="sxs-lookup"><span data-stu-id="abe65-113">Precompilation considerations</span></span>

<span data-ttu-id="abe65-114">Im Folgenden werden Nebeneffekte der Vorkompilierung von Razor-Dateien aufgeführt:</span><span class="sxs-lookup"><span data-stu-id="abe65-114">The following are side effects of precompiling Razor files:</span></span>

* <span data-ttu-id="abe65-115">Kleineres veröffentlichtes Paket</span><span class="sxs-lookup"><span data-stu-id="abe65-115">A smaller published bundle</span></span>
* <span data-ttu-id="abe65-116">Schnellere Startzeit</span><span class="sxs-lookup"><span data-stu-id="abe65-116">A faster startup time</span></span>
* <span data-ttu-id="abe65-117">Razor-Dateien können nicht bearbeitet werden. Der zugehörige Inhalt fehlt im veröffentlichten Paket.</span><span class="sxs-lookup"><span data-stu-id="abe65-117">You can't edit Razor files&mdash;the associated content is absent from the published bundle.</span></span>

## <a name="deploy-precompiled-files"></a><span data-ttu-id="abe65-118">Bereitstellen vorkompilierter Dateien</span><span class="sxs-lookup"><span data-stu-id="abe65-118">Deploy precompiled files</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="abe65-119">Die Kompilierung zur Erstellung und Veröffentlichung von Razor-Dateien wird standardmäßig vom Razor SDK aktiviert.</span><span class="sxs-lookup"><span data-stu-id="abe65-119">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="abe65-120">Das Bearbeiten von Razor-Dateien, nachdem sie aktualisiert wurden, wird zum Zeitpunkt der Erstellung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="abe65-120">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="abe65-121">Standardmäßig wird nur die kompilierte Datei *Views.dll*, ohne *CSHTML*-Dateien, mit Ihrer App bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="abe65-121">By default, only the compiled *Views.dll* and no *.cshtml* files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="abe65-122">Das Vorkompilierungstool wird in ASP.NET Core 3.0 entfernt.</span><span class="sxs-lookup"><span data-stu-id="abe65-122">The precompilation tool will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="abe65-123">Wir empfehlen die Migration zu [Razor Sdk](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="abe65-123">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="abe65-124">Das Razor SDK ist nur dann wirksam, wenn keine für die Vorkompilierung spezifischen Eigenschaften in der Projektdatei festgelegt sind.</span><span class="sxs-lookup"><span data-stu-id="abe65-124">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="abe65-125">Durch Festlegen der Eigenschaft `MvcRazorCompileOnPublish` der *CSPROJ*-Datei auf `true` wird das Razor SDK beispielsweise deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="abe65-125">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="abe65-126">Wenn Ihr Projekt .NET Framework als Ziel verwendet, installieren Sie das NuGet-Paket [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/):</span><span class="sxs-lookup"><span data-stu-id="abe65-126">If your project targets .NET Framework, install the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet package:</span></span>

[!code-xml[](view-compilation/sample/DotNetFrameworkProject.csproj?name=snippet_ViewCompilationPackage)]

<span data-ttu-id="abe65-127">Wenn Ihr Paket für .NET Core gedacht ist, sind keine Änderungen erforderlich.</span><span class="sxs-lookup"><span data-stu-id="abe65-127">If your project targets .NET Core, no changes are necessary.</span></span>

<span data-ttu-id="abe65-128">Mit den Projektvorlagen von ASP.NET Core 2.x wird die Eigenschaft `MvcRazorCompileOnPublish` standardmäßig explizit auf `true` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="abe65-128">The ASP.NET Core 2.x project templates implicitly set the `MvcRazorCompileOnPublish` property to `true` by default.</span></span> <span data-ttu-id="abe65-129">Dieses Element kann folglich sicher aus der *CSPROJ*-Datei entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="abe65-129">Consequently, this element can be safely removed from the *.csproj* file.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="abe65-130">Das Vorkompilierungstool wird in ASP.NET Core 3.0 entfernt.</span><span class="sxs-lookup"><span data-stu-id="abe65-130">The precompilation tool will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="abe65-131">Wir empfehlen die Migration zu [Razor Sdk](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="abe65-131">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="abe65-132">Die Vorkompilierung von Razor-Dateien steht aktuell beim Durchführen einer [eigenständigen Bereitstellung (Self-Contained Deployment, SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) in ASP.NET Core 2.0 nicht zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="abe65-132">Razor file precompilation is unavailable when performing a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) in ASP.NET Core 2.0.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-1.1"

<span data-ttu-id="abe65-133">Legen Sie die Eigenschaft `MvcRazorCompileOnPublish` auf `true` fest und installieren Sie das NuGet-Paket [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/).</span><span class="sxs-lookup"><span data-stu-id="abe65-133">Set the `MvcRazorCompileOnPublish` property to `true`, and install the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet package.</span></span> <span data-ttu-id="abe65-134">Das folgende *CSPROJ*-Beispiel veranschaulicht diese Einstellungen:</span><span class="sxs-lookup"><span data-stu-id="abe65-134">The following *.csproj* sample highlights these settings:</span></span>

[!code-xml[](view-compilation/sample/MvcRazorCompileOnPublish.csproj?highlight=4,10)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="abe65-135">Bereiten Sie die App mit dem [Veröffentlichungsbefehl der .NET Core-CLI](/dotnet/core/tools/dotnet-publish) für eine [Framework-abhängige Bereitstellung](/dotnet/core/deploying/#framework-dependent-deployments-fdd) vor.</span><span class="sxs-lookup"><span data-stu-id="abe65-135">Prepare the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd) with the [.NET Core CLI publish command](/dotnet/core/tools/dotnet-publish).</span></span> <span data-ttu-id="abe65-136">Führen Sie z. B. den folgenden Befehl auf der Stammebene des Projekts aus:</span><span class="sxs-lookup"><span data-stu-id="abe65-136">For example, execute the following command at the project root:</span></span>

```console
dotnet publish -c Release
```

<span data-ttu-id="abe65-137">Die Datei *<project_name>.PrecompiledViews.dll*, welche die kompilierten Razor-Dateien enthält, wird erzeugt, wenn die Vorkompilierung erfolgreich abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="abe65-137">A *<project_name>.PrecompiledViews.dll* file, containing the compiled Razor files, is produced when precompilation succeeds.</span></span> <span data-ttu-id="abe65-138">Im unten stehenden Screenshot wird beispielsweise der Inhalt von *Index.cshtml* in *WebApplication1.PrecompiledViews.dll* dargestellt:</span><span class="sxs-lookup"><span data-stu-id="abe65-138">For example, the screenshot below depicts the contents of *Index.cshtml* within *WebApplication1.PrecompiledViews.dll*:</span></span>

![Razor-Ansichten in einer DLL](view-compilation/_static/razor-views-in-dll.png)

::: moniker-end

## <a name="recompile-razor-files-on-change"></a><span data-ttu-id="abe65-140">Erneutes Kompilieren der Razor-Dateien bei Änderung</span><span class="sxs-lookup"><span data-stu-id="abe65-140">Recompile Razor files on change</span></span>

<span data-ttu-id="abe65-141"><xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> übernimmt oder bestimmt einen Wert, der bestimmt, ob Razor-Dateien (Razor-Ansichten und Razor Pages) neu kompiliert und aktualisiert werden, wenn Dateien auf dem Datenträger sich ändern.</span><span class="sxs-lookup"><span data-stu-id="abe65-141">The <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> gets or sets a value that determines if Razor files (Razor views and Razor Pages) are recompiled and updated if files change on disk.</span></span>

<span data-ttu-id="abe65-142">Bei Festlegung auf `true` überwacht [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) Änderungen an den Razor-Dateien in konfigurierten <xref:Microsoft.Extensions.FileProviders.IFileProvider>-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="abe65-142">When set to `true`, [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) watches for changes to Razor files in configured <xref:Microsoft.Extensions.FileProviders.IFileProvider> instances.</span></span>

<span data-ttu-id="abe65-143">Der Standardwert ist `true` für:</span><span class="sxs-lookup"><span data-stu-id="abe65-143">The default value is `true` for:</span></span>

* <span data-ttu-id="abe65-144">ASP.NET Core 2.1 oder frühere Apps.</span><span class="sxs-lookup"><span data-stu-id="abe65-144">ASP.NET Core 2.1 or earlier apps.</span></span>
* <span data-ttu-id="abe65-145">ASP.NET Core 2.2 oder höhere Apps in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="abe65-145">ASP.NET Core 2.2 or later apps in the Development environment.</span></span>

<span data-ttu-id="abe65-146"><xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> ist einem Kompatibilitätsswitch zugeordnet und kann je nach der für die App konfigurierten Kompatibilitätsversion unterschiedliches Verhalten zeigen.</span><span class="sxs-lookup"><span data-stu-id="abe65-146"><xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> is associated with a compatibility switch and can provide different behavior depending on the configured compatibility version for the app.</span></span> <span data-ttu-id="abe65-147">Konfigurieren der App durch Festlegen von <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> hat Vorrang vor dem durch die Kompatibilitätsversion implizierten Wert.</span><span class="sxs-lookup"><span data-stu-id="abe65-147">Configuring the app by setting <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> takes precedence over the value implied by the app's compatibility version.</span></span>

<span data-ttu-id="abe65-148">Wenn die Kompatibilitätsversion der App auf <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> oder früher festgelegt ist, wird <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> auf `true` festgelegt, sofern nicht explizit konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="abe65-148">If the app's compatibility version is set to <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> or earlier, <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> is set to `true` unless explicitly configured.</span></span>

<span data-ttu-id="abe65-149">Wenn die Kompatibilitätsversion der App auf <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_2> oder höher festgelegt ist, wird <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> auf `false` festgelegt, wenn es sich nicht um eine Entwicklungsumgebung handelt oder der Wert explizit konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="abe65-149">If the app's compatibility version is set to <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_2> or later, <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> is set to `false` unless the environment is Development or the value is explicitly configured.</span></span>

<span data-ttu-id="abe65-150">Anleitungen und Beispiele zum Festlegen der Kompatibilitätsversion der App finden Sie unter <xref:mvc/compatibility-version>.</span><span class="sxs-lookup"><span data-stu-id="abe65-150">For guidance and examples of setting the app's compatibility version, see <xref:mvc/compatibility-version>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="abe65-151">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="abe65-151">Additional resources</span></span>

::: moniker range="= aspnetcore-1.1"

* <xref:mvc/views/overview>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <xref:razor-pages/index>
* <xref:mvc/views/overview>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

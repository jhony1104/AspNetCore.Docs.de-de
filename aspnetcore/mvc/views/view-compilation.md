---
title: Kompilieren einer Razor-Datei in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie die Kompilierung von Razor-Dateien in einer ASP.NET Core-App auftreten kann.
ms.author: riande
ms.custom: mvc
ms.date: 4/8/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 0afd39fdb5a6f570e0e78ad54f6c436460bad3a6
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81223958"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="878f7-103">Kompilieren einer Razor-Datei in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="878f7-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="878f7-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="878f7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="878f7-105">Razor-Dateien mit einer *.cshtml*-Erweiterung werden mit dem [Razor SDK](xref:razor-pages/sdk) zur Erstellung und zur Veröffentlichung kompiliert.</span><span class="sxs-lookup"><span data-stu-id="878f7-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="878f7-106">Die Laufzeitkompilierung kann optional aktiviert werden, indem Ihre Anwendung konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="878f7-106">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="878f7-107">Razor-Kompilierung</span><span class="sxs-lookup"><span data-stu-id="878f7-107">Razor compilation</span></span>

<span data-ttu-id="878f7-108">Die Kompilierung zur Erstellung und Veröffentlichung von Razor-Dateien wird standardmäßig vom Razor SDK aktiviert.</span><span class="sxs-lookup"><span data-stu-id="878f7-108">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="878f7-109">Wenn die Laufzeitkompilierung aktiviert ist, ergänzt diese die Kompilierung zur Buildzeit, wodurch Razor-Dateien aktualisiert werden können, wenn sie bearbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="878f7-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they are edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="878f7-110">Laufzeitkompilierung</span><span class="sxs-lookup"><span data-stu-id="878f7-110">Runtime compilation</span></span>

<span data-ttu-id="878f7-111">So aktivieren Sie die Laufzeitkompilierung für alle Umgebungen und Konfigurationsmodi:</span><span class="sxs-lookup"><span data-stu-id="878f7-111">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="878f7-112">das NuGet-Paket [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) installieren.</span><span class="sxs-lookup"><span data-stu-id="878f7-112">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="878f7-113">Aktualisieren Sie die `Startup.ConfigureServices`-Methode des Projekts so, dass diese einen Aufruf von <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> enthält.</span><span class="sxs-lookup"><span data-stu-id="878f7-113">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="878f7-114">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="878f7-114">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="878f7-115">Bedingte Aktivierung der Laufzeitkompilierung</span><span class="sxs-lookup"><span data-stu-id="878f7-115">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="878f7-116">Die Laufzeitkompilierung kann so aktiviert werden, dass sie nur für die lokale Entwicklung verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="878f7-116">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="878f7-117">Eine bedingte Aktivierung auf diese Weise stellt für die veröffentlichte Ausgabe Folgendes sicher:</span><span class="sxs-lookup"><span data-stu-id="878f7-117">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="878f7-118">Es werden kompilierte Ansichten verwendet.</span><span class="sxs-lookup"><span data-stu-id="878f7-118">Uses compiled views.</span></span>
* <span data-ttu-id="878f7-119">Die Größe ist geringer.</span><span class="sxs-lookup"><span data-stu-id="878f7-119">Is smaller in size.</span></span>
* <span data-ttu-id="878f7-120">Dateiüberwachungselemente in der Produktionsumgebung werden nicht aktiviert.</span><span class="sxs-lookup"><span data-stu-id="878f7-120">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="878f7-121">So aktivieren Sie die Laufzeitkompilierung basierend auf Umgebung und Konfigurationsmodus:</span><span class="sxs-lookup"><span data-stu-id="878f7-121">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="878f7-122">Geben Sie einen bedingten Verweis auf das [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)-Paket basierend auf dem aktiven `Configuration`-Wert an:</span><span class="sxs-lookup"><span data-stu-id="878f7-122">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="878f7-123">Aktualisieren Sie die `Startup.ConfigureServices`-Methode des Projekts so, dass diese einen Aufruf von `AddRazorRuntimeCompilation` enthält.</span><span class="sxs-lookup"><span data-stu-id="878f7-123">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="878f7-124">Führen Sie `AddRazorRuntimeCompilation` bedingt so aus, dass die Ausführung nur dann im Debugmodus erfolgt, wenn die `ASPNETCORE_ENVIRONMENT`-Variable auf `Development` festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="878f7-124">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

  [!code-csharp[](~/mvc/views/view-compilation/sample/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="878f7-125">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="878f7-125">Additional resources</span></span>

* <span data-ttu-id="878f7-126">[RazorCompileOnBuild- und RazorCompileOnPublish-Eigenschaften.](xref:razor-pages/sdk#properties)</span><span class="sxs-lookup"><span data-stu-id="878f7-126">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="878f7-127">Im Beispiel für die [Laufzeitkompilierung auf GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) finden Sie ein Beispiel, das zeigt, dass die Laufzeitkompilierung projektübergreifend funktioniert.</span><span class="sxs-lookup"><span data-stu-id="878f7-127">See the [runtimecompilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="878f7-128">Eine Razor-Datei wird zur Laufzeit kompiliert, wenn die zugeordnete Razor-Seite oder MVC-Ansicht aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="878f7-128">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="878f7-129">Razor-Dateien werden mit dem [Razor SDK](xref:razor-pages/sdk) zur Erstellung und zur Veröffentlichung kompiliert.</span><span class="sxs-lookup"><span data-stu-id="878f7-129">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="878f7-130">Razor-Kompilierung</span><span class="sxs-lookup"><span data-stu-id="878f7-130">Razor compilation</span></span>

<span data-ttu-id="878f7-131">Die Kompilierung zur Erstellung und Veröffentlichung von Razor-Dateien wird standardmäßig vom Razor SDK aktiviert.</span><span class="sxs-lookup"><span data-stu-id="878f7-131">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="878f7-132">Das Bearbeiten von Razor-Dateien, nachdem sie aktualisiert wurden, wird zum Zeitpunkt der Erstellung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="878f7-132">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="878f7-133">Standardmäßig werden nur die kompilierten *Views.dll*- und keine *.cshtml*-Dateien oder -Verweise auf Assemblys, die für die Kompilierung von Razor-Dateien benötigt werden, in Ihrer App bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="878f7-133">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="878f7-134">Das Vorkompilierungstool ist veraltet und wird in ASP.NET Core 3.0 entfernt.</span><span class="sxs-lookup"><span data-stu-id="878f7-134">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="878f7-135">Wir empfehlen die Migration zu [Razor Sdk](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="878f7-135">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="878f7-136">Das Razor SDK ist nur dann wirksam, wenn keine für die Vorkompilierung spezifischen Eigenschaften in der Projektdatei festgelegt sind.</span><span class="sxs-lookup"><span data-stu-id="878f7-136">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="878f7-137">Durch Festlegen der Eigenschaft `MvcRazorCompileOnPublish` der *CSPROJ*-Datei auf `true` wird das Razor SDK beispielsweise deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="878f7-137">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="878f7-138">Laufzeitkompilierung</span><span class="sxs-lookup"><span data-stu-id="878f7-138">Runtime compilation</span></span>

<span data-ttu-id="878f7-139">Die Kompilierung zur Buildzeit wird durch die Laufzeitkompilierung von Razor-Dateien ergänzt.</span><span class="sxs-lookup"><span data-stu-id="878f7-139">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="878f7-140">Der ASP.NET Core-MVC kompiliert Razor-Dateien erneut, wenn sich die Inhalte einer *.cshtml*-Datei ändern.</span><span class="sxs-lookup"><span data-stu-id="878f7-140">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="878f7-141">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="878f7-141">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

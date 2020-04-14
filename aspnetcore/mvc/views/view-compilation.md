---
title: Kompilieren einer Razor-Datei in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie die Kompilierung von Razor-Dateien in einer ASP.NET Core-App auftreten kann.
ms.author: riande
ms.custom: mvc
ms.date: 04/13/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 67bbeb88cd944791b522900b69bd10cff38c9f3a
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277267"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="4f588-103">Kompilieren einer Razor-Datei in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4f588-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="4f588-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4f588-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="4f588-105">Razor-Dateien mit einer *.cshtml*-Erweiterung werden mit dem [Razor SDK](xref:razor-pages/sdk) zur Erstellung und zur Veröffentlichung kompiliert.</span><span class="sxs-lookup"><span data-stu-id="4f588-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="4f588-106">Die Laufzeitkompilierung kann optional durch Konfigurieren des Projekts aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="4f588-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="4f588-107">Razor-Kompilierung</span><span class="sxs-lookup"><span data-stu-id="4f588-107">Razor compilation</span></span>

<span data-ttu-id="4f588-108">Die Erstellung von Razor-Dateien zur Erstellung von Razor-Dateien ist standardmäßig durch das Razor SDK aktiviert.</span><span class="sxs-lookup"><span data-stu-id="4f588-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="4f588-109">Wenn diese Option aktiviert ist, ergänzt die Laufzeitkompilierung die Erstellung von Buildzeit, sodass Razor-Dateien aktualisiert werden können, wenn sie bearbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="4f588-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="4f588-110">Aktivieren der Laufzeitkompilierung bei der Projekterstellung</span><span class="sxs-lookup"><span data-stu-id="4f588-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="4f588-111">Die Vorlagen für Razor Pages und MVC-Projekt enthalten eine Option zum Aktivieren der Laufzeitkompilierung beim Erstellen des Projekts.</span><span class="sxs-lookup"><span data-stu-id="4f588-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="4f588-112">Diese Option wird in ASP.NET Core 3.1 und höher unterstützt.</span><span class="sxs-lookup"><span data-stu-id="4f588-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4f588-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f588-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4f588-114">Im **Dialogfeld Erstellen eines neuen ASP.NET Core-Webanwendungsdialogfeld:**</span><span class="sxs-lookup"><span data-stu-id="4f588-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="4f588-115">Wählen Sie entweder die **Projektvorlage Webanwendung** oder **Webanwendung (Model-View-Controller)** aus.</span><span class="sxs-lookup"><span data-stu-id="4f588-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="4f588-116">Aktivieren Sie das Kontrollkästchen **Razor-Laufzeitkompilierung aktivieren.**</span><span class="sxs-lookup"><span data-stu-id="4f588-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4f588-117">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="4f588-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="4f588-118">Verwenden `-rrc` Sie `--razor-runtime-compilation` die Option oder Vorlage.</span><span class="sxs-lookup"><span data-stu-id="4f588-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="4f588-119">Der folgende Befehl erstellt beispielsweise ein neues Razor Pages-Projekt mit aktivierter Laufzeitkompilierung:</span><span class="sxs-lookup"><span data-stu-id="4f588-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="4f588-120">Aktivieren der Laufzeitkompilierung in einem vorhandenen Projekt</span><span class="sxs-lookup"><span data-stu-id="4f588-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="4f588-121">So aktivieren Sie die Laufzeitkompilierung für alle Umgebungen in einem vorhandenen Projekt:</span><span class="sxs-lookup"><span data-stu-id="4f588-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="4f588-122">das NuGet-Paket [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) installieren.</span><span class="sxs-lookup"><span data-stu-id="4f588-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="4f588-123">Aktualisieren Sie die `Startup.ConfigureServices`-Methode des Projekts so, dass diese einen Aufruf von <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> enthält.</span><span class="sxs-lookup"><span data-stu-id="4f588-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="4f588-124">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="4f588-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="4f588-125">Bedingtes Aktivieren der Laufzeitkompilierung in einem vorhandenen Projekt</span><span class="sxs-lookup"><span data-stu-id="4f588-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="4f588-126">Die Laufzeitkompilierung kann so aktiviert werden, dass sie nur für die lokale Entwicklung verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="4f588-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="4f588-127">Eine bedingte Aktivierung auf diese Weise stellt für die veröffentlichte Ausgabe Folgendes sicher:</span><span class="sxs-lookup"><span data-stu-id="4f588-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="4f588-128">Es werden kompilierte Ansichten verwendet.</span><span class="sxs-lookup"><span data-stu-id="4f588-128">Uses compiled views.</span></span>
* <span data-ttu-id="4f588-129">Dateiüberwachungselemente in der Produktionsumgebung werden nicht aktiviert.</span><span class="sxs-lookup"><span data-stu-id="4f588-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="4f588-130">So aktivieren Sie die Laufzeitkompilierung nur in der Entwicklungsumgebung:</span><span class="sxs-lookup"><span data-stu-id="4f588-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="4f588-131">das NuGet-Paket [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) installieren.</span><span class="sxs-lookup"><span data-stu-id="4f588-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="4f588-132">Ändern Sie `environmentVariables` den Abschnitt "Startprofil" in *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="4f588-132">Modify the launch profile `environmentVariables` section in *launchSettings.json*:</span></span>
    * <span data-ttu-id="4f588-133">Verify `ASPNETCORE_ENVIRONMENT` ist `"Development"`auf festgelegt.</span><span class="sxs-lookup"><span data-stu-id="4f588-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="4f588-134">Legen Sie `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` auf `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"` fest.</span><span class="sxs-lookup"><span data-stu-id="4f588-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="4f588-135">Im folgenden Beispiel ist die Laufzeitkompilierung in `IIS Express` `RazorPagesApp` der Entwicklungsumgebung für die und Startprofile aktiviert:</span><span class="sxs-lookup"><span data-stu-id="4f588-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="4f588-136">In der `Startup` Projektklasse sind keine Codeänderungen erforderlich.</span><span class="sxs-lookup"><span data-stu-id="4f588-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="4f588-137">Zur Laufzeit sucht ASP.NET Core nach einem [HostingStartup-Attribut](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) auf Assemblyebene in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span><span class="sxs-lookup"><span data-stu-id="4f588-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="4f588-138">Das `HostingStartup` Attribut gibt den auszuführenden App-Startcode an.</span><span class="sxs-lookup"><span data-stu-id="4f588-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="4f588-139">Dieser Startcode ermöglicht die Laufzeitkompilierung.</span><span class="sxs-lookup"><span data-stu-id="4f588-139">That startup code enables runtime compilation.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4f588-140">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="4f588-140">Additional resources</span></span>

* <span data-ttu-id="4f588-141">[RazorCompileOnBuild- und RazorCompileOnPublish-Eigenschaften.](xref:razor-pages/sdk#properties)</span><span class="sxs-lookup"><span data-stu-id="4f588-141">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="4f588-142">Im Beispiel für die [Laufzeitkompilierung auf GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) finden Sie ein Beispiel, das zeigt, dass die Laufzeitkompilierung projektübergreifend funktioniert.</span><span class="sxs-lookup"><span data-stu-id="4f588-142">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="4f588-143">Razor-Dateien mit einer *.cshtml*-Erweiterung werden mit dem [Razor SDK](xref:razor-pages/sdk) zur Erstellung und zur Veröffentlichung kompiliert.</span><span class="sxs-lookup"><span data-stu-id="4f588-143">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="4f588-144">Die Laufzeitkompilierung kann optional aktiviert werden, indem Ihre Anwendung konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="4f588-144">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="4f588-145">Razor-Kompilierung</span><span class="sxs-lookup"><span data-stu-id="4f588-145">Razor compilation</span></span>

<span data-ttu-id="4f588-146">Die Erstellung von Razor-Dateien zur Erstellung von Razor-Dateien ist standardmäßig durch das Razor SDK aktiviert.</span><span class="sxs-lookup"><span data-stu-id="4f588-146">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="4f588-147">Wenn diese Option aktiviert ist, ergänzt die Laufzeitkompilierung die Erstellung von Buildzeit, sodass Razor-Dateien aktualisiert werden können, wenn sie bearbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="4f588-147">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="4f588-148">Laufzeitkompilierung</span><span class="sxs-lookup"><span data-stu-id="4f588-148">Runtime compilation</span></span>

<span data-ttu-id="4f588-149">So aktivieren Sie die Laufzeitkompilierung für alle Umgebungen und Konfigurationsmodi:</span><span class="sxs-lookup"><span data-stu-id="4f588-149">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="4f588-150">das NuGet-Paket [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) installieren.</span><span class="sxs-lookup"><span data-stu-id="4f588-150">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="4f588-151">Aktualisieren Sie die `Startup.ConfigureServices`-Methode des Projekts so, dass diese einen Aufruf von <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> enthält.</span><span class="sxs-lookup"><span data-stu-id="4f588-151">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="4f588-152">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="4f588-152">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="4f588-153">Bedingte Aktivierung der Laufzeitkompilierung</span><span class="sxs-lookup"><span data-stu-id="4f588-153">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="4f588-154">Die Laufzeitkompilierung kann so aktiviert werden, dass sie nur für die lokale Entwicklung verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="4f588-154">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="4f588-155">Eine bedingte Aktivierung auf diese Weise stellt für die veröffentlichte Ausgabe Folgendes sicher:</span><span class="sxs-lookup"><span data-stu-id="4f588-155">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="4f588-156">Es werden kompilierte Ansichten verwendet.</span><span class="sxs-lookup"><span data-stu-id="4f588-156">Uses compiled views.</span></span>
* <span data-ttu-id="4f588-157">Die Größe ist geringer.</span><span class="sxs-lookup"><span data-stu-id="4f588-157">Is smaller in size.</span></span>
* <span data-ttu-id="4f588-158">Dateiüberwachungselemente in der Produktionsumgebung werden nicht aktiviert.</span><span class="sxs-lookup"><span data-stu-id="4f588-158">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="4f588-159">So aktivieren Sie die Laufzeitkompilierung basierend auf Umgebung und Konfigurationsmodus:</span><span class="sxs-lookup"><span data-stu-id="4f588-159">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="4f588-160">Geben Sie einen bedingten Verweis auf das [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)-Paket basierend auf dem aktiven `Configuration`-Wert an:</span><span class="sxs-lookup"><span data-stu-id="4f588-160">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="4f588-161">Aktualisieren Sie die `Startup.ConfigureServices`-Methode des Projekts so, dass diese einen Aufruf von `AddRazorRuntimeCompilation` enthält.</span><span class="sxs-lookup"><span data-stu-id="4f588-161">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="4f588-162">Führen Sie `AddRazorRuntimeCompilation` bedingt so aus, dass die Ausführung nur dann im Debugmodus erfolgt, wenn die `ASPNETCORE_ENVIRONMENT`-Variable auf `Development` festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="4f588-162">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="4f588-163">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="4f588-163">Additional resources</span></span>

* <span data-ttu-id="4f588-164">[RazorCompileOnBuild- und RazorCompileOnPublish-Eigenschaften.](xref:razor-pages/sdk#properties)</span><span class="sxs-lookup"><span data-stu-id="4f588-164">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="4f588-165">Im Beispiel für die [Laufzeitkompilierung auf GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) finden Sie ein Beispiel, das zeigt, dass die Laufzeitkompilierung projektübergreifend funktioniert.</span><span class="sxs-lookup"><span data-stu-id="4f588-165">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4f588-166">Eine Razor-Datei wird zur Laufzeit kompiliert, wenn die zugeordnete Razor-Seite oder MVC-Ansicht aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="4f588-166">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="4f588-167">Razor-Dateien werden mit dem [Razor SDK](xref:razor-pages/sdk) zur Erstellung und zur Veröffentlichung kompiliert.</span><span class="sxs-lookup"><span data-stu-id="4f588-167">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="4f588-168">Razor-Kompilierung</span><span class="sxs-lookup"><span data-stu-id="4f588-168">Razor compilation</span></span>

<span data-ttu-id="4f588-169">Die Kompilierung zur Erstellung und Veröffentlichung von Razor-Dateien wird standardmäßig vom Razor SDK aktiviert.</span><span class="sxs-lookup"><span data-stu-id="4f588-169">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="4f588-170">Das Bearbeiten von Razor-Dateien, nachdem sie aktualisiert wurden, wird zum Zeitpunkt der Erstellung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="4f588-170">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="4f588-171">Standardmäßig werden nur die kompilierten *Views.dll*- und keine *.cshtml*-Dateien oder -Verweise auf Assemblys, die für die Kompilierung von Razor-Dateien benötigt werden, in Ihrer App bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="4f588-171">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4f588-172">Das Vorkompilierungstool ist veraltet und wird in ASP.NET Core 3.0 entfernt.</span><span class="sxs-lookup"><span data-stu-id="4f588-172">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="4f588-173">Wir empfehlen die Migration zu [Razor Sdk](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="4f588-173">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="4f588-174">Das Razor SDK ist nur dann wirksam, wenn keine für die Vorkompilierung spezifischen Eigenschaften in der Projektdatei festgelegt sind.</span><span class="sxs-lookup"><span data-stu-id="4f588-174">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="4f588-175">Durch Festlegen der Eigenschaft `MvcRazorCompileOnPublish` der *CSPROJ*-Datei auf `true` wird das Razor SDK beispielsweise deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="4f588-175">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="4f588-176">Laufzeitkompilierung</span><span class="sxs-lookup"><span data-stu-id="4f588-176">Runtime compilation</span></span>

<span data-ttu-id="4f588-177">Die Kompilierung zur Buildzeit wird durch die Laufzeitkompilierung von Razor-Dateien ergänzt.</span><span class="sxs-lookup"><span data-stu-id="4f588-177">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="4f588-178">Der ASP.NET Core-MVC kompiliert Razor-Dateien erneut, wenn sich die Inhalte einer *.cshtml*-Datei ändern.</span><span class="sxs-lookup"><span data-stu-id="4f588-178">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4f588-179">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="4f588-179">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

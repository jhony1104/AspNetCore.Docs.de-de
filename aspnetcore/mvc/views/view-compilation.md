---
title: Kompilieren einer Razor-Datei in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie die Kompilierung von Razor-Dateien in einer ASP.NET Core-App auftreten kann.
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 3d871ab960de28a565280d9e4cb2c597832e2455
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440934"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="78e18-103">Kompilieren einer Razor-Datei in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="78e18-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="78e18-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="78e18-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="78e18-105">Razor-Dateien mit einer *.cshtml*-Erweiterung werden mit dem [Razor SDK](xref:razor-pages/sdk) zur Erstellung und zur Veröffentlichung kompiliert.</span><span class="sxs-lookup"><span data-stu-id="78e18-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="78e18-106">Die Laufzeitkompilierung kann optional durch Konfigurieren des Projekts aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="78e18-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="78e18-107">Razor-Kompilierung</span><span class="sxs-lookup"><span data-stu-id="78e18-107">Razor compilation</span></span>

<span data-ttu-id="78e18-108">Die Erstellung von Razor-Dateien zur Erstellung von Razor-Dateien ist standardmäßig durch das Razor SDK aktiviert.</span><span class="sxs-lookup"><span data-stu-id="78e18-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="78e18-109">Wenn diese Option aktiviert ist, ergänzt die Laufzeitkompilierung die Erstellung von Buildzeit, sodass Razor-Dateien aktualisiert werden können, wenn sie bearbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="78e18-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="78e18-110">Aktivieren der Laufzeitkompilierung bei der Projekterstellung</span><span class="sxs-lookup"><span data-stu-id="78e18-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="78e18-111">Die Vorlagen für Razor Pages und MVC-Projekt enthalten eine Option zum Aktivieren der Laufzeitkompilierung beim Erstellen des Projekts.</span><span class="sxs-lookup"><span data-stu-id="78e18-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="78e18-112">Diese Option wird in ASP.NET Core 3.1 und höher unterstützt.</span><span class="sxs-lookup"><span data-stu-id="78e18-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="78e18-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="78e18-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="78e18-114">Im **Dialogfeld Erstellen eines neuen ASP.NET Core-Webanwendungsdialogfeld:**</span><span class="sxs-lookup"><span data-stu-id="78e18-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="78e18-115">Wählen Sie entweder die **Projektvorlage Webanwendung** oder **Webanwendung (Model-View-Controller)** aus.</span><span class="sxs-lookup"><span data-stu-id="78e18-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="78e18-116">Aktivieren Sie das Kontrollkästchen **Razor-Laufzeitkompilierung aktivieren.**</span><span class="sxs-lookup"><span data-stu-id="78e18-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="78e18-117">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="78e18-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="78e18-118">Verwenden `-rrc` Sie `--razor-runtime-compilation` die Option oder Vorlage.</span><span class="sxs-lookup"><span data-stu-id="78e18-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="78e18-119">Der folgende Befehl erstellt beispielsweise ein neues Razor Pages-Projekt mit aktivierter Laufzeitkompilierung:</span><span class="sxs-lookup"><span data-stu-id="78e18-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="78e18-120">Aktivieren der Laufzeitkompilierung in einem vorhandenen Projekt</span><span class="sxs-lookup"><span data-stu-id="78e18-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="78e18-121">So aktivieren Sie die Laufzeitkompilierung für alle Umgebungen in einem vorhandenen Projekt:</span><span class="sxs-lookup"><span data-stu-id="78e18-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="78e18-122">das NuGet-Paket [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) installieren.</span><span class="sxs-lookup"><span data-stu-id="78e18-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="78e18-123">Aktualisieren Sie die `Startup.ConfigureServices`-Methode des Projekts so, dass diese einen Aufruf von <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> enthält.</span><span class="sxs-lookup"><span data-stu-id="78e18-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="78e18-124">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="78e18-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="78e18-125">Bedingtes Aktivieren der Laufzeitkompilierung in einem vorhandenen Projekt</span><span class="sxs-lookup"><span data-stu-id="78e18-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="78e18-126">Die Laufzeitkompilierung kann so aktiviert werden, dass sie nur für die lokale Entwicklung verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="78e18-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="78e18-127">Eine bedingte Aktivierung auf diese Weise stellt für die veröffentlichte Ausgabe Folgendes sicher:</span><span class="sxs-lookup"><span data-stu-id="78e18-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="78e18-128">Es werden kompilierte Ansichten verwendet.</span><span class="sxs-lookup"><span data-stu-id="78e18-128">Uses compiled views.</span></span>
* <span data-ttu-id="78e18-129">Dateiüberwachungselemente in der Produktionsumgebung werden nicht aktiviert.</span><span class="sxs-lookup"><span data-stu-id="78e18-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="78e18-130">So aktivieren Sie die Laufzeitkompilierung nur in der Entwicklungsumgebung:</span><span class="sxs-lookup"><span data-stu-id="78e18-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="78e18-131">das NuGet-Paket [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) installieren.</span><span class="sxs-lookup"><span data-stu-id="78e18-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="78e18-132">Ändern Sie `environmentVariables` den Abschnitt "Startprofil" in *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="78e18-132">Modify the launch profile `environmentVariables` section in *launchSettings.json*:</span></span>
    * <span data-ttu-id="78e18-133">Verify `ASPNETCORE_ENVIRONMENT` ist `"Development"`auf festgelegt.</span><span class="sxs-lookup"><span data-stu-id="78e18-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="78e18-134">Legen Sie `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` auf `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"` fest.</span><span class="sxs-lookup"><span data-stu-id="78e18-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="78e18-135">Im folgenden Beispiel ist die Laufzeitkompilierung in `IIS Express` `RazorPagesApp` der Entwicklungsumgebung für die und Startprofile aktiviert:</span><span class="sxs-lookup"><span data-stu-id="78e18-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="78e18-136">In der `Startup` Projektklasse sind keine Codeänderungen erforderlich.</span><span class="sxs-lookup"><span data-stu-id="78e18-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="78e18-137">Zur Laufzeit sucht ASP.NET Core nach einem [HostingStartup-Attribut](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) auf Assemblyebene in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span><span class="sxs-lookup"><span data-stu-id="78e18-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="78e18-138">Das `HostingStartup` Attribut gibt den auszuführenden App-Startcode an.</span><span class="sxs-lookup"><span data-stu-id="78e18-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="78e18-139">Dieser Startcode ermöglicht die Laufzeitkompilierung.</span><span class="sxs-lookup"><span data-stu-id="78e18-139">That startup code enables runtime compilation.</span></span>

## <a name="enable-runtime-compilation-for-a-razor-class-library"></a><span data-ttu-id="78e18-140">Aktivieren der Laufzeitkompilierung für eine Razor-Klassenbibliothek</span><span class="sxs-lookup"><span data-stu-id="78e18-140">Enable runtime compilation for a Razor Class Library</span></span>

<span data-ttu-id="78e18-141">Betrachten Sie ein Szenario, in dem ein Razor Pages-Projekt auf eine [Razor-Klassenbibliothek (RCL)](xref:razor-pages/ui-class) mit dem Namen *MyClassLib*verweist.</span><span class="sxs-lookup"><span data-stu-id="78e18-141">Consider a scenario in which a Razor Pages project references a [Razor Class Library (RCL)](xref:razor-pages/ui-class) named *MyClassLib*.</span></span> <span data-ttu-id="78e18-142">Die RCL enthält eine *_Layout.cshtml-Datei,* die alle MVC- und Razor Pages-Projekte Ihres Teams verwenden.</span><span class="sxs-lookup"><span data-stu-id="78e18-142">The RCL contains a *_Layout.cshtml* file that all of your team's MVC and Razor Pages projects consume.</span></span> <span data-ttu-id="78e18-143">Sie möchten die Laufzeitkompilierung für die *Datei _Layout.cshtml* in dieser RCL aktivieren.</span><span class="sxs-lookup"><span data-stu-id="78e18-143">You want to enable runtime compilation for the *_Layout.cshtml* file in that RCL.</span></span> <span data-ttu-id="78e18-144">Nehmen Sie die folgenden Änderungen im Razor Pages-Projekt vor:</span><span class="sxs-lookup"><span data-stu-id="78e18-144">Make the following changes in the Razor Pages project:</span></span>

1. <span data-ttu-id="78e18-145">Aktivieren Sie die Laufzeitkompilierung mit den Anweisungen unter [Conditionally enable runtime compilation in einem vorhandenen Projekt](#conditionally-enable-runtime-compilation-in-an-existing-project).</span><span class="sxs-lookup"><span data-stu-id="78e18-145">Enable runtime compilation with the instructions at [Conditionally enable runtime compilation in an existing project](#conditionally-enable-runtime-compilation-in-an-existing-project).</span></span>
1. <span data-ttu-id="78e18-146">Konfigurieren Sie die Laufzeitkompilierungsoptionen in: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="78e18-146">Configure the runtime compilation options in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    <span data-ttu-id="78e18-147">Im vorherigen Code wird ein absoluter Pfad zur *MyClassLib-RCL* erstellt.</span><span class="sxs-lookup"><span data-stu-id="78e18-147">In the preceding code, an absolute path to the *MyClassLib* RCL is constructed.</span></span> <span data-ttu-id="78e18-148">Die [PhysicalFileProvider-API](xref:fundamentals/file-providers#physicalfileprovider) wird verwendet, um Verzeichnisse und Dateien in diesem absoluten Pfad zu suchen.</span><span class="sxs-lookup"><span data-stu-id="78e18-148">The [PhysicalFileProvider API](xref:fundamentals/file-providers#physicalfileprovider) is used to locate directories and files at that absolute path.</span></span> <span data-ttu-id="78e18-149">Schließlich wird `PhysicalFileProvider` die Instanz einer Dateianbietersammlung hinzugefügt, die den Zugriff auf die *.cshtml-Dateien* der RCL ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="78e18-149">Finally, the `PhysicalFileProvider` instance is added to a file providers collection, which allows access to the RCL's *.cshtml* files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="78e18-150">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="78e18-150">Additional resources</span></span>

* <span data-ttu-id="78e18-151">[RazorCompileOnBuild- und RazorCompileOnPublish-Eigenschaften.](xref:razor-pages/sdk#properties)</span><span class="sxs-lookup"><span data-stu-id="78e18-151">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="78e18-152">Razor-Dateien mit einer *.cshtml*-Erweiterung werden mit dem [Razor SDK](xref:razor-pages/sdk) zur Erstellung und zur Veröffentlichung kompiliert.</span><span class="sxs-lookup"><span data-stu-id="78e18-152">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="78e18-153">Die Laufzeitkompilierung kann optional aktiviert werden, indem Ihre Anwendung konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="78e18-153">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="78e18-154">Razor-Kompilierung</span><span class="sxs-lookup"><span data-stu-id="78e18-154">Razor compilation</span></span>

<span data-ttu-id="78e18-155">Die Erstellung von Razor-Dateien zur Erstellung von Razor-Dateien ist standardmäßig durch das Razor SDK aktiviert.</span><span class="sxs-lookup"><span data-stu-id="78e18-155">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="78e18-156">Wenn diese Option aktiviert ist, ergänzt die Laufzeitkompilierung die Erstellung von Buildzeit, sodass Razor-Dateien aktualisiert werden können, wenn sie bearbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="78e18-156">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="78e18-157">Laufzeitkompilierung</span><span class="sxs-lookup"><span data-stu-id="78e18-157">Runtime compilation</span></span>

<span data-ttu-id="78e18-158">So aktivieren Sie die Laufzeitkompilierung für alle Umgebungen und Konfigurationsmodi:</span><span class="sxs-lookup"><span data-stu-id="78e18-158">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="78e18-159">das NuGet-Paket [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) installieren.</span><span class="sxs-lookup"><span data-stu-id="78e18-159">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="78e18-160">Aktualisieren Sie die `Startup.ConfigureServices`-Methode des Projekts so, dass diese einen Aufruf von <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> enthält.</span><span class="sxs-lookup"><span data-stu-id="78e18-160">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="78e18-161">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="78e18-161">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="78e18-162">Bedingte Aktivierung der Laufzeitkompilierung</span><span class="sxs-lookup"><span data-stu-id="78e18-162">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="78e18-163">Die Laufzeitkompilierung kann so aktiviert werden, dass sie nur für die lokale Entwicklung verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="78e18-163">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="78e18-164">Eine bedingte Aktivierung auf diese Weise stellt für die veröffentlichte Ausgabe Folgendes sicher:</span><span class="sxs-lookup"><span data-stu-id="78e18-164">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="78e18-165">Es werden kompilierte Ansichten verwendet.</span><span class="sxs-lookup"><span data-stu-id="78e18-165">Uses compiled views.</span></span>
* <span data-ttu-id="78e18-166">Die Größe ist geringer.</span><span class="sxs-lookup"><span data-stu-id="78e18-166">Is smaller in size.</span></span>
* <span data-ttu-id="78e18-167">Dateiüberwachungselemente in der Produktionsumgebung werden nicht aktiviert.</span><span class="sxs-lookup"><span data-stu-id="78e18-167">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="78e18-168">So aktivieren Sie die Laufzeitkompilierung basierend auf Umgebung und Konfigurationsmodus:</span><span class="sxs-lookup"><span data-stu-id="78e18-168">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="78e18-169">Geben Sie einen bedingten Verweis auf das [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)-Paket basierend auf dem aktiven `Configuration`-Wert an:</span><span class="sxs-lookup"><span data-stu-id="78e18-169">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="78e18-170">Aktualisieren Sie die `Startup.ConfigureServices`-Methode des Projekts so, dass diese einen Aufruf von `AddRazorRuntimeCompilation` enthält.</span><span class="sxs-lookup"><span data-stu-id="78e18-170">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="78e18-171">Führen Sie `AddRazorRuntimeCompilation` bedingt so aus, dass die Ausführung nur dann im Debugmodus erfolgt, wenn die `ASPNETCORE_ENVIRONMENT`-Variable auf `Development` festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="78e18-171">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="78e18-172">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="78e18-172">Additional resources</span></span>

* <span data-ttu-id="78e18-173">[RazorCompileOnBuild- und RazorCompileOnPublish-Eigenschaften.](xref:razor-pages/sdk#properties)</span><span class="sxs-lookup"><span data-stu-id="78e18-173">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="78e18-174">Im Beispiel für die [Laufzeitkompilierung auf GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) finden Sie ein Beispiel, das zeigt, dass die Laufzeitkompilierung projektübergreifend funktioniert.</span><span class="sxs-lookup"><span data-stu-id="78e18-174">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="78e18-175">Eine Razor-Datei wird zur Laufzeit kompiliert, wenn die zugeordnete Razor-Seite oder MVC-Ansicht aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="78e18-175">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="78e18-176">Razor-Dateien werden mit dem [Razor SDK](xref:razor-pages/sdk) zur Erstellung und zur Veröffentlichung kompiliert.</span><span class="sxs-lookup"><span data-stu-id="78e18-176">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="78e18-177">Razor-Kompilierung</span><span class="sxs-lookup"><span data-stu-id="78e18-177">Razor compilation</span></span>

<span data-ttu-id="78e18-178">Die Kompilierung zur Erstellung und Veröffentlichung von Razor-Dateien wird standardmäßig vom Razor SDK aktiviert.</span><span class="sxs-lookup"><span data-stu-id="78e18-178">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="78e18-179">Das Bearbeiten von Razor-Dateien, nachdem sie aktualisiert wurden, wird zum Zeitpunkt der Erstellung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="78e18-179">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="78e18-180">Standardmäßig werden nur die kompilierten *Views.dll*- und keine *.cshtml*-Dateien oder -Verweise auf Assemblys, die für die Kompilierung von Razor-Dateien benötigt werden, in Ihrer App bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="78e18-180">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="78e18-181">Das Vorkompilierungstool ist veraltet und wird in ASP.NET Core 3.0 entfernt.</span><span class="sxs-lookup"><span data-stu-id="78e18-181">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="78e18-182">Wir empfehlen die Migration zu [Razor Sdk](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="78e18-182">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="78e18-183">Das Razor SDK ist nur dann wirksam, wenn keine für die Vorkompilierung spezifischen Eigenschaften in der Projektdatei festgelegt sind.</span><span class="sxs-lookup"><span data-stu-id="78e18-183">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="78e18-184">Durch Festlegen der Eigenschaft `MvcRazorCompileOnPublish` der *CSPROJ*-Datei auf `true` wird das Razor SDK beispielsweise deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="78e18-184">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="78e18-185">Laufzeitkompilierung</span><span class="sxs-lookup"><span data-stu-id="78e18-185">Runtime compilation</span></span>

<span data-ttu-id="78e18-186">Die Kompilierung zur Buildzeit wird durch die Laufzeitkompilierung von Razor-Dateien ergänzt.</span><span class="sxs-lookup"><span data-stu-id="78e18-186">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="78e18-187">Der ASP.NET Core-MVC kompiliert Razor-Dateien erneut, wenn sich die Inhalte einer *.cshtml*-Datei ändern.</span><span class="sxs-lookup"><span data-stu-id="78e18-187">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="78e18-188">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="78e18-188">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

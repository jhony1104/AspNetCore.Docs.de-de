---
title: RazorDatei Kompilierung in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie die Kompilierung von Razor Dateien in einer ASP.net Core-App erfolgt.
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/view-compilation
ms.openlocfilehash: 71487ff2d5d7d7cf96835778f386e5f30fa32254
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405444"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>Razor<span data-ttu-id="a3434-103">Datei Kompilierung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="a3434-103"> file compilation in ASP.NET Core</span></span>

<span data-ttu-id="a3434-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a3434-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

Razor<span data-ttu-id="a3434-105">Dateien mit der Erweiterung *. cshtml* werden zum Zeitpunkt der Erstellung und Veröffentlichung mit dem [ Razor SDK](xref:razor-pages/sdk)kompiliert.</span><span class="sxs-lookup"><span data-stu-id="a3434-105"> files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="a3434-106">Die Runtime-Kompilierung kann optional durch Konfigurieren des Projekts aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="a3434-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="razor-compilation"></a>Razor<span data-ttu-id="a3434-107">Neuauflage</span><span class="sxs-lookup"><span data-stu-id="a3434-107"> compilation</span></span>

<span data-ttu-id="a3434-108">Kompilierzeit-und Veröffentlichungszeit Kompilierung von Razor Dateien wird vom SDK standardmäßig aktiviert Razor .</span><span class="sxs-lookup"><span data-stu-id="a3434-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="a3434-109">Wenn diese Option aktiviert ist, ergänzt die Lauf Zeit Kompilierung die Kompilierungs Kompilierung, sodass Razor Dateien aktualisiert werden können, wenn Sie bearbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="a3434-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="a3434-110">Aktivieren der Lauf Zeit Kompilierung bei der Projekt Erstellung</span><span class="sxs-lookup"><span data-stu-id="a3434-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="a3434-111">Die Razor Seiten und MVC-Projektvorlagen enthalten eine Option zum Aktivieren der Lauf Zeit Kompilierung, wenn das Projekt erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="a3434-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="a3434-112">Diese Option wird in ASP.net Core 3,1 und höher unterstützt.</span><span class="sxs-lookup"><span data-stu-id="a3434-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a3434-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a3434-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a3434-114">Erstellen Sie im Dialogfeld **neue ASP.net Core Webanwendung erstellen** Folgendes:</span><span class="sxs-lookup"><span data-stu-id="a3434-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="a3434-115">Wählen Sie entweder die Projektvorlage **Webanwendung** oder **Webanwendung (Model-View-Controller)** aus.</span><span class="sxs-lookup"><span data-stu-id="a3434-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="a3434-116">Aktivieren Sie das Kontrollkästchen \*\* Razor Lauf Zeit Kompilierung aktivieren\*\* .</span><span class="sxs-lookup"><span data-stu-id="a3434-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a3434-117">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="a3434-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a3434-118">Verwenden Sie `-rrc` die `--razor-runtime-compilation` Vorlagen Option oder.</span><span class="sxs-lookup"><span data-stu-id="a3434-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="a3434-119">Der folgende Befehl erstellt z. b. ein neues Razor pages-Projekt mit aktivierter Runtime-Kompilierung:</span><span class="sxs-lookup"><span data-stu-id="a3434-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="a3434-120">Aktivieren der Lauf Zeit Kompilierung in einem vorhandenen Projekt</span><span class="sxs-lookup"><span data-stu-id="a3434-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="a3434-121">So aktivieren Sie die Lauf Zeit Kompilierung für alle Umgebungen in einem vorhandenen Projekt:</span><span class="sxs-lookup"><span data-stu-id="a3434-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="a3434-122">Installieren Sie [Microsoft. aspnetcore. MVC. Razor . Runtimecompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) -nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="a3434-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="a3434-123">Aktualisieren Sie die `Startup.ConfigureServices`-Methode des Projekts so, dass diese einen Aufruf von <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> enthält.</span><span class="sxs-lookup"><span data-stu-id="a3434-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="a3434-124">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a3434-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="a3434-125">Bedingte Aktivierung der Lauf Zeit Kompilierung in einem vorhandenen Projekt</span><span class="sxs-lookup"><span data-stu-id="a3434-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="a3434-126">Die Laufzeitkompilierung kann so aktiviert werden, dass sie nur für die lokale Entwicklung verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="a3434-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="a3434-127">Eine bedingte Aktivierung auf diese Weise stellt für die veröffentlichte Ausgabe Folgendes sicher:</span><span class="sxs-lookup"><span data-stu-id="a3434-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="a3434-128">Es werden kompilierte Ansichten verwendet.</span><span class="sxs-lookup"><span data-stu-id="a3434-128">Uses compiled views.</span></span>
* <span data-ttu-id="a3434-129">Dateiüberwachungselemente in der Produktionsumgebung werden nicht aktiviert.</span><span class="sxs-lookup"><span data-stu-id="a3434-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="a3434-130">So aktivieren Sie die Lauf Zeit Kompilierung nur in der Entwicklungsumgebung:</span><span class="sxs-lookup"><span data-stu-id="a3434-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="a3434-131">Installieren Sie [Microsoft. aspnetcore. MVC. Razor . Runtimecompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) -nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="a3434-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="a3434-132">Ändern Sie den Abschnitt Start Profil `environmentVariables` in *launchSettings.js*:</span><span class="sxs-lookup"><span data-stu-id="a3434-132">Modify the launch profile `environmentVariables` section in *launchSettings.json*:</span></span>
    * <span data-ttu-id="a3434-133">Überprüfen `ASPNETCORE_ENVIRONMENT` ist auf festgelegt `"Development"` .</span><span class="sxs-lookup"><span data-stu-id="a3434-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="a3434-134">Legen Sie `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` auf `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"` fest.</span><span class="sxs-lookup"><span data-stu-id="a3434-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="a3434-135">Im folgenden Beispiel ist die Lauf Zeit Kompilierung in der Entwicklungsumgebung für die `IIS Express` `RazorPagesApp` Start Profile und aktiviert:</span><span class="sxs-lookup"><span data-stu-id="a3434-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="a3434-136">In der-Klasse des Projekts sind keine Codeänderungen erforderlich `Startup` .</span><span class="sxs-lookup"><span data-stu-id="a3434-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="a3434-137">Zur Laufzeit sucht ASP.net Core nach einem [hostingstartup-Attribut](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) auf Assemblyebene in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` .</span><span class="sxs-lookup"><span data-stu-id="a3434-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="a3434-138">Das- `HostingStartup` Attribut gibt den auszuführenden App-Startcode an.</span><span class="sxs-lookup"><span data-stu-id="a3434-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="a3434-139">Dieser Startcode ermöglicht die Lauf Zeit Kompilierung.</span><span class="sxs-lookup"><span data-stu-id="a3434-139">That startup code enables runtime compilation.</span></span>

## <a name="enable-runtime-compilation-for-a-razor-class-library"></a><span data-ttu-id="a3434-140">Aktivieren der Lauf Zeit Kompilierung für eine Razor Klassenbibliothek</span><span class="sxs-lookup"><span data-stu-id="a3434-140">Enable runtime compilation for a Razor Class Library</span></span>

<span data-ttu-id="a3434-141">Stellen Sie sich ein Szenario vor, in dem ein Razor Seiten Projekt auf eine [ Razor Klassenbibliothek (RCL)](xref:razor-pages/ui-class) mit dem Namen *myclasslib*verweist.</span><span class="sxs-lookup"><span data-stu-id="a3434-141">Consider a scenario in which a Razor Pages project references a [Razor Class Library (RCL)](xref:razor-pages/ui-class) named *MyClassLib*.</span></span> <span data-ttu-id="a3434-142">Die RCL enthält eine *_Layout. cshtml* -Datei, die von allen MVC-und Pages-Projekten Ihres Teams genutzt wird Razor .</span><span class="sxs-lookup"><span data-stu-id="a3434-142">The RCL contains a *_Layout.cshtml* file that all of your team's MVC and Razor Pages projects consume.</span></span> <span data-ttu-id="a3434-143">Sie möchten die Runtime-Kompilierung für die Datei *_Layout. cshtml* in dieser RCL aktivieren.</span><span class="sxs-lookup"><span data-stu-id="a3434-143">You want to enable runtime compilation for the *_Layout.cshtml* file in that RCL.</span></span> <span data-ttu-id="a3434-144">Nehmen Sie die folgenden Änderungen im Razor pages-Projekt vor:</span><span class="sxs-lookup"><span data-stu-id="a3434-144">Make the following changes in the Razor Pages project:</span></span>

1. <span data-ttu-id="a3434-145">Aktivieren Sie die Lauf Zeit Kompilierung mit den Anweisungen unter [bedingt Aktivieren der Lauf Zeit Kompilierung in einem vorhandenen Projekt](#conditionally-enable-runtime-compilation-in-an-existing-project).</span><span class="sxs-lookup"><span data-stu-id="a3434-145">Enable runtime compilation with the instructions at [Conditionally enable runtime compilation in an existing project](#conditionally-enable-runtime-compilation-in-an-existing-project).</span></span>
1. <span data-ttu-id="a3434-146">Konfigurieren Sie die Optionen für die Lauf Zeit Kompilierung in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a3434-146">Configure the runtime compilation options in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    <span data-ttu-id="a3434-147">Im vorangehenden Code wird ein absoluter Pfad zur *myclasslib* -RCL erstellt.</span><span class="sxs-lookup"><span data-stu-id="a3434-147">In the preceding code, an absolute path to the *MyClassLib* RCL is constructed.</span></span> <span data-ttu-id="a3434-148">Die [physicalfileprovider-API](xref:fundamentals/file-providers#physicalfileprovider) wird verwendet, um Verzeichnisse und Dateien in diesem absoluten Pfad zu suchen.</span><span class="sxs-lookup"><span data-stu-id="a3434-148">The [PhysicalFileProvider API](xref:fundamentals/file-providers#physicalfileprovider) is used to locate directories and files at that absolute path.</span></span> <span data-ttu-id="a3434-149">Schließlich wird die `PhysicalFileProvider` Instanz einer Datei Anbieter Auflistung hinzugefügt, die den Zugriff auf die *cshtml* -Dateien der RCL ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="a3434-149">Finally, the `PhysicalFileProvider` instance is added to a file providers collection, which allows access to the RCL's *.cshtml* files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a3434-150">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a3434-150">Additional resources</span></span>

* <span data-ttu-id="a3434-151">[Razorcompileonbuild-und razorcompileonpublish-](xref:razor-pages/sdk#properties) Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="a3434-151">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

Razor<span data-ttu-id="a3434-152">Dateien mit der Erweiterung *. cshtml* werden zum Zeitpunkt der Erstellung und Veröffentlichung mit dem [ Razor SDK](xref:razor-pages/sdk)kompiliert.</span><span class="sxs-lookup"><span data-stu-id="a3434-152"> files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="a3434-153">Die Laufzeitkompilierung kann optional aktiviert werden, indem Ihre Anwendung konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="a3434-153">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a>Razor<span data-ttu-id="a3434-154">Neuauflage</span><span class="sxs-lookup"><span data-stu-id="a3434-154"> compilation</span></span>

<span data-ttu-id="a3434-155">Kompilierzeit-und Veröffentlichungszeit Kompilierung von Razor Dateien wird vom SDK standardmäßig aktiviert Razor .</span><span class="sxs-lookup"><span data-stu-id="a3434-155">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="a3434-156">Wenn diese Option aktiviert ist, ergänzt die Lauf Zeit Kompilierung die Kompilierungs Kompilierung, sodass Razor Dateien aktualisiert werden können, wenn Sie bearbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="a3434-156">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="a3434-157">Laufzeitkompilierung</span><span class="sxs-lookup"><span data-stu-id="a3434-157">Runtime compilation</span></span>

<span data-ttu-id="a3434-158">So aktivieren Sie die Laufzeitkompilierung für alle Umgebungen und Konfigurationsmodi:</span><span class="sxs-lookup"><span data-stu-id="a3434-158">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="a3434-159">Installieren Sie [Microsoft. aspnetcore. MVC. Razor . Runtimecompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) -nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="a3434-159">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="a3434-160">Aktualisieren Sie die `Startup.ConfigureServices`-Methode des Projekts so, dass diese einen Aufruf von <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> enthält.</span><span class="sxs-lookup"><span data-stu-id="a3434-160">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="a3434-161">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a3434-161">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="a3434-162">Bedingte Aktivierung der Laufzeitkompilierung</span><span class="sxs-lookup"><span data-stu-id="a3434-162">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="a3434-163">Die Laufzeitkompilierung kann so aktiviert werden, dass sie nur für die lokale Entwicklung verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="a3434-163">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="a3434-164">Eine bedingte Aktivierung auf diese Weise stellt für die veröffentlichte Ausgabe Folgendes sicher:</span><span class="sxs-lookup"><span data-stu-id="a3434-164">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="a3434-165">Es werden kompilierte Ansichten verwendet.</span><span class="sxs-lookup"><span data-stu-id="a3434-165">Uses compiled views.</span></span>
* <span data-ttu-id="a3434-166">Die Größe ist geringer.</span><span class="sxs-lookup"><span data-stu-id="a3434-166">Is smaller in size.</span></span>
* <span data-ttu-id="a3434-167">Dateiüberwachungselemente in der Produktionsumgebung werden nicht aktiviert.</span><span class="sxs-lookup"><span data-stu-id="a3434-167">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="a3434-168">So aktivieren Sie die Laufzeitkompilierung basierend auf Umgebung und Konfigurationsmodus:</span><span class="sxs-lookup"><span data-stu-id="a3434-168">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="a3434-169">Verweisen Sie bedingt auf [Microsoft. aspnetcore. MVC. Razor . Runtimecompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) -Paket basierend auf dem aktiven `Configuration` Wert:</span><span class="sxs-lookup"><span data-stu-id="a3434-169">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="a3434-170">Aktualisieren Sie die `Startup.ConfigureServices`-Methode des Projekts so, dass diese einen Aufruf von `AddRazorRuntimeCompilation` enthält.</span><span class="sxs-lookup"><span data-stu-id="a3434-170">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="a3434-171">Führen Sie `AddRazorRuntimeCompilation` bedingt so aus, dass die Ausführung nur dann im Debugmodus erfolgt, wenn die `ASPNETCORE_ENVIRONMENT`-Variable auf `Development` festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="a3434-171">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="a3434-172">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a3434-172">Additional resources</span></span>

* <span data-ttu-id="a3434-173">[Razorcompileonbuild-und razorcompileonpublish-](xref:razor-pages/sdk#properties) Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="a3434-173">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="a3434-174">Im [Beispiel zur Lauf Zeit Kompilierung auf GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) finden Sie ein Beispiel, das zeigt, wie die Lauf Zeit Kompilierung in Projekten funktioniert.</span><span class="sxs-lookup"><span data-stu-id="a3434-174">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a3434-175">Eine Razor Datei wird zur Laufzeit kompiliert, wenn die zugehörige Razor Seite oder MVC-Ansicht aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="a3434-175">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> Razor<span data-ttu-id="a3434-176">Dateien werden zum Zeitpunkt der Erstellung und Veröffentlichung mit dem [ Razor SDK](xref:razor-pages/sdk)kompiliert.</span><span class="sxs-lookup"><span data-stu-id="a3434-176"> files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a>Razor<span data-ttu-id="a3434-177">Neuauflage</span><span class="sxs-lookup"><span data-stu-id="a3434-177"> compilation</span></span>

<span data-ttu-id="a3434-178">Build-und Veröffentlichungszeit Kompilierung von Razor Dateien wird vom SDK standardmäßig aktiviert Razor .</span><span class="sxs-lookup"><span data-stu-id="a3434-178">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="a3434-179">Das Bearbeiten Razor von Dateien nach ihrer Aktualisierung wird zur Buildzeit unterstützt.</span><span class="sxs-lookup"><span data-stu-id="a3434-179">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="a3434-180">Standardmäßig werden nur die Dateien für kompilierte *Views.dll* und keine *cshtml* -Dateien oder Verweise, die zum Kompilieren von Razor Dateien erforderlich sind, mit Ihrer APP bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="a3434-180">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a3434-181">Das Vorkompilierungstool ist veraltet und wird in ASP.NET Core 3.0 entfernt.</span><span class="sxs-lookup"><span data-stu-id="a3434-181">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="a3434-182">Es wird empfohlen, zu [ Razor SDK](xref:razor-pages/sdk)zu migrieren.</span><span class="sxs-lookup"><span data-stu-id="a3434-182">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="a3434-183">Das Razor SDK ist nur wirksam, wenn keine vorkompilierungs spezifischen Eigenschaften in der Projektdatei festgelegt sind.</span><span class="sxs-lookup"><span data-stu-id="a3434-183">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="a3434-184">Wenn Sie z. b. die-Eigenschaft der *csproj* -Datei festlegen, wird `MvcRazorCompileOnPublish` `true` das Razor SDK deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="a3434-184">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="a3434-185">Laufzeitkompilierung</span><span class="sxs-lookup"><span data-stu-id="a3434-185">Runtime compilation</span></span>

<span data-ttu-id="a3434-186">Die Kompilierung der Kompilierzeit wird durch die Lauf Zeit Kompilierung von Razor Dateien ergänzt.</span><span class="sxs-lookup"><span data-stu-id="a3434-186">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="a3434-187">ASP.net Core MVC kompiliert Dateien neu, Razor Wenn sich der Inhalt einer *cshtml* -Datei ändert.</span><span class="sxs-lookup"><span data-stu-id="a3434-187">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a3434-188">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a3434-188">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

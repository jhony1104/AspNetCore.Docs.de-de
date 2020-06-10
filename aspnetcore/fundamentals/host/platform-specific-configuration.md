---
title: 'title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:'
author: rick-anderson
description: "'Blazor'"
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: 8cf6a4467f041fa71b75ee8d1e7a08d8f572acf3
ms.sourcegitcommit: 58722eb309767e462bdbf3082bd38737a4ef168f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84106350"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="b66c4-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b66c4-103">'Identity'</span></span>

<span data-ttu-id="b66c4-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b66c4-104">'Let's Encrypt'</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b66c4-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b66c4-105">'Razor'</span></span> <span data-ttu-id="b66c4-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="b66c4-106">'SignalR' uid:</span></span>

<span data-ttu-id="b66c4-107">Verwenden von Hostingstartassemblys in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b66c4-107">Use hosting startup assemblies in ASP.NET Core</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="b66c4-108">Von [Pavel Krymets](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="b66c4-108">By [Pavel Krymets](https://github.com/pakrym)</span></span>

<span data-ttu-id="b66c4-109">Mit einer <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>-Implementierung (Hostingstart) werden Verbesserungen an einer App beim Start von einer externen Assembly aus vorgenommen.</span><span class="sxs-lookup"><span data-stu-id="b66c4-109">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span>

<span data-ttu-id="b66c4-110">Eine externe Bibliothek kann beispielsweise eine Hostingstartimplementierung verwenden, um zusätzliche Konfigurationsanbieter oder -dienste für eine App bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="b66c4-110">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span> <span data-ttu-id="b66c4-111">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b66c4-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span> <span data-ttu-id="b66c4-112">Das Attribut HostingStartup</span><span class="sxs-lookup"><span data-stu-id="b66c4-112">HostingStartup attribute</span></span>

<span data-ttu-id="b66c4-113">Ein [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute)-Attribut gibt an, ob eine Hostingstartassembly vorhanden ist, die zur Laufzeit aktiviert werden kann.</span><span class="sxs-lookup"><span data-stu-id="b66c4-113">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span> <span data-ttu-id="b66c4-114">Bei der Einstiegsassembly oder bei der Assembly, die die Klasse `Startup` enthält, wird automatisch geprüft, ob sie das Attribut `HostingStartup` enthält.</span><span class="sxs-lookup"><span data-stu-id="b66c4-114">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="b66c4-115">Die Liste der Assemblys für die Suche nach `HostingStartup`-Attributen wird zur Laufzeit aus der Konfiguration in [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey) geladen.</span><span class="sxs-lookup"><span data-stu-id="b66c4-115">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="b66c4-116">Die Liste der Assemblys, die aus der Ermittlung ausgeschlossen werden sollen, wird aus [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey) geladen.</span><span class="sxs-lookup"><span data-stu-id="b66c4-116">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span>

<span data-ttu-id="b66c4-117">Im folgenden Beispiel lautet der Namespace der Hostingstartassembly `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="b66c4-117">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="b66c4-118">`StartupEnhancementHostingStartup` ist die Klasse mit dem Hostingstartcode:</span><span class="sxs-lookup"><span data-stu-id="b66c4-118">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span> <span data-ttu-id="b66c4-119">Das Attribut `HostingStartup` befindet sich in der Regel in der `IHostingStartup`-Implementierungsklassendatei der Hostingstartassembly.</span><span class="sxs-lookup"><span data-stu-id="b66c4-119">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="b66c4-120">Ermitteln geladener Hostingstartassemblys</span><span class="sxs-lookup"><span data-stu-id="b66c4-120">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="b66c4-121">Zum Ermitteln von geladenen Hostingstartassemblys aktivieren Sie die Protokollierung und überprüfen die Protokolle der App.</span><span class="sxs-lookup"><span data-stu-id="b66c4-121">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span>

* <span data-ttu-id="b66c4-122">Fehler, die beim Laden von Assemblys auftreten, werden protokolliert.</span><span class="sxs-lookup"><span data-stu-id="b66c4-122">Errors that occur when loading assemblies are logged.</span></span>

  * <span data-ttu-id="b66c4-123">Geladene Hostingstartassemblys werden auf der Debugebene protokolliert. Außerdem werden alle Fehler protokolliert.</span><span class="sxs-lookup"><span data-stu-id="b66c4-123">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.PreventHostingStartupKey, "true")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="b66c4-124">Deaktivieren des automatischen Ladens von Hostingstartassemblys</span><span class="sxs-lookup"><span data-stu-id="b66c4-124">Disable automatic loading of hosting startup assemblies</span></span>

* <span data-ttu-id="b66c4-125">Um das automatische Laden von Hostingstartassemblys zu deaktivieren, verwenden Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="b66c4-125">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

  * <span data-ttu-id="b66c4-126">Um zu verhindern, dass alle Hostingstartassemblys geladen werden, legen Sie eine der folgenden Einstellungen auf `true` oder `1` fest:</span><span class="sxs-lookup"><span data-stu-id="b66c4-126">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.HostingStartupExcludeAssembliesKey, 
                        "{ASSEMBLY1;ASSEMBLY2; ...}")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="b66c4-127">Hostkonfigurationseinstellung „Verhindern des Hostingstarts“:</span><span class="sxs-lookup"><span data-stu-id="b66c4-127">Prevent Hosting Startup host configuration setting:</span></span>

<span data-ttu-id="b66c4-128">Die Umgebungsvariable `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="b66c4-128">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

<span data-ttu-id="b66c4-129">Um zu verhindern, dass bestimmte Hostingstartassemblys geladen werden, legen Sie eine der folgenden Einstellungen auf eine durch Semikolons getrennte Zeichenfolge mit Hostingstartassemblys fest, die beim Starten ausgeschlossen werden sollen:</span><span class="sxs-lookup"><span data-stu-id="b66c4-129">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>

## <a name="project"></a><span data-ttu-id="b66c4-130">Hostkonfigurationseinstellung „Hostingstartausschlussassemblys“:</span><span class="sxs-lookup"><span data-stu-id="b66c4-130">Hosting Startup Exclude Assemblies host configuration setting:</span></span>

<span data-ttu-id="b66c4-131">Die Umgebungsvariable `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="b66c4-131">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

* <span data-ttu-id="b66c4-132">Wenn sowohl die Konfigurationseinstellung für den Host als auch die Umgebungsvariable festgelegt werden, wird das Verhalten durch die Hosteinstellung gesteuert.</span><span class="sxs-lookup"><span data-stu-id="b66c4-132">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>
* <span data-ttu-id="b66c4-133">Durch das Deaktivieren von Hostingstartassemblys mithilfe der Hosteinstellung oder der Umgebungsvariable wird die Assembly global deaktiviert. Einige Eigenschaften einer App können ebenfalls deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="b66c4-133">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

### <a name="class-library"></a><span data-ttu-id="b66c4-134">Projekt</span><span class="sxs-lookup"><span data-stu-id="b66c4-134">Project</span></span>

<span data-ttu-id="b66c4-135">Erstellen Sie einen Hostingstart mit einem der folgenden Projekttypen:</span><span class="sxs-lookup"><span data-stu-id="b66c4-135">Create a hosting startup with either of the following project types:</span></span> [<span data-ttu-id="b66c4-136">Klassenbibliothek</span><span class="sxs-lookup"><span data-stu-id="b66c4-136">Class library</span></span>](#class-library)

[<span data-ttu-id="b66c4-137">Konsolen-App ohne Einstiegspunkt</span><span class="sxs-lookup"><span data-stu-id="b66c4-137">Console app without an entry point</span></span>](#console-app-without-an-entry-point) <span data-ttu-id="b66c4-138">Klassenbibliothek</span><span class="sxs-lookup"><span data-stu-id="b66c4-138">Class library</span></span>

* <span data-ttu-id="b66c4-139">In einer Klassenbibliothek kann eine Hostingstarterweiterung bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="b66c4-139">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="b66c4-140">Die Bibliothek enthält ein `HostingStartup`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="b66c4-140">The library contains a `HostingStartup` attribute.</span></span>
* <span data-ttu-id="b66c4-141">Der [Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) enthält die Razor Pages-App *HostingStartupApp* und die Klassenbibliothek *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="b66c4-141">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span>

<span data-ttu-id="b66c4-142">Die Klassenbibliothek:</span><span class="sxs-lookup"><span data-stu-id="b66c4-142">The class library:</span></span>

<span data-ttu-id="b66c4-143">Enthält die Hostingstartklasse `ServiceKeyInjection`, die `IHostingStartup` implementiert.</span><span class="sxs-lookup"><span data-stu-id="b66c4-143">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="b66c4-144">`ServiceKeyInjection` fügt mithilfe des Anbieters der Konfiguration im Arbeitsspeicher ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)) der App-Konfiguration zwei Dienstzeichenfolgen hinzu.</span><span class="sxs-lookup"><span data-stu-id="b66c4-144">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>

<span data-ttu-id="b66c4-145">Enthält ein `HostingStartup`-Attribut, das den Namespace und die Klasse des Hostingstarts angibt.</span><span class="sxs-lookup"><span data-stu-id="b66c4-145">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="b66c4-146">Die <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>-Methode der `ServiceKeyInjection`-Klasse verwendet einen <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>, um einer App Erweiterungen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="b66c4-146">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="b66c4-147">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="b66c4-147">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span> <span data-ttu-id="b66c4-148">Die Indexseite der App liest und rendert die Konfigurationswerte für die beiden Schlüssel, die von der Hostingstartassembly der Klassenbibliothek festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="b66c4-148">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

* <span data-ttu-id="b66c4-149">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="b66c4-149">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span> <span data-ttu-id="b66c4-150">Der [Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) enthält darüber hinaus auch ein NuGet-Paketprojekt, das den separaten Hostingstart *HostingStartupPackage* bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="b66c4-150">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span>
* <span data-ttu-id="b66c4-151">Das Paket weist dieselben Merkmale wie die bereits beschriebene Klassenbibliothek auf.</span><span class="sxs-lookup"><span data-stu-id="b66c4-151">The package has the same characteristics of the class library described earlier.</span></span>

<span data-ttu-id="b66c4-152">Das Paket:</span><span class="sxs-lookup"><span data-stu-id="b66c4-152">The package:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="b66c4-153">Enthält die Hostingstartklasse `ServiceKeyInjection`, die `IHostingStartup` implementiert.</span><span class="sxs-lookup"><span data-stu-id="b66c4-153">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span>

<span data-ttu-id="b66c4-154">`ServiceKeyInjection` fügt der App-Konfiguration zwei Dienstzeichenfolgen hinzu.</span><span class="sxs-lookup"><span data-stu-id="b66c4-154">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="b66c4-155">Enthält ein `HostingStartup`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="b66c4-155">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="b66c4-156">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="b66c4-156">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

<span data-ttu-id="b66c4-157">Die Indexseite der App liest und rendert die Konfigurationswerte für die beiden Schlüssel, die von der Hostingstartassembly der Bibliothek festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="b66c4-157">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span> <span data-ttu-id="b66c4-158">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="b66c4-158">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

<span data-ttu-id="b66c4-159">Konsolen-App ohne Einstiegspunkt</span><span class="sxs-lookup"><span data-stu-id="b66c4-159">Console app without an entry point</span></span>

* <span data-ttu-id="b66c4-160">*Dieses Verfahren ist nur für .NET Core-Apps, nicht jedoch für .NET Framework verfügbar.*</span><span class="sxs-lookup"><span data-stu-id="b66c4-160">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span> <span data-ttu-id="b66c4-161">Eine dynamische Hostingstarterweiterung, die zur Aktivierung keinen Kompilierungszeitverweis erfordert, kann in einer Konsolen-App ohne Einstiegspunkt bereitgestellt werden, die ein `HostingStartup`-Attribut enthält.</span><span class="sxs-lookup"><span data-stu-id="b66c4-161">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span>
* <span data-ttu-id="b66c4-162">Durch die Veröffentlichung der Konsolen-App wird eine Hostingstartassembly erstellt, die über den Laufzeitspeicher genutzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="b66c4-162">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="b66c4-163">In diesem Prozess wird aus folgenden Gründen eine Konsolen-App ohne Einstiegspunkt verwendet:</span><span class="sxs-lookup"><span data-stu-id="b66c4-163">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="b66c4-164">Eine Abhängigkeitendatei ist erforderlich, um den Hostingstart in der Hostingstartassembly zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="b66c4-164">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span>
  * <span data-ttu-id="b66c4-165">Eine Abhängigkeitendatei ist eine ausführbare App-Ressource, die durch das Veröffentlichen einer App, nicht einer Bibliothek, erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="b66c4-165">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
  * <span data-ttu-id="b66c4-166">Eine Bibliothek kann dem [Laufzeitpaketspeicher](/dotnet/core/deploying/runtime-store), der ein ausführbares Projekt benötigt, das die freigegebene Laufzeit als Ziel verwendet, nicht direkt hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="b66c4-166">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>
* <span data-ttu-id="b66c4-167">Bei der Erstellung eines dynamischen Hostingstarts geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="b66c4-167">In the creation of a dynamic hosting startup:</span></span> <span data-ttu-id="b66c4-168">Eine Hostingstartassembly wird über die Konsolen-App ohne Einstiegspunkt erstellt, für die Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="b66c4-168">A hosting startup assembly is created from the console app without an entry point that:</span></span>
* <span data-ttu-id="b66c4-169">Sie enthält eine Klasse mit der `IHostingStartup`-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="b66c4-169">Includes a class that contains the `IHostingStartup` implementation.</span></span>
* <span data-ttu-id="b66c4-170">Sie enthält ein [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute)-Attribut zum Identifizieren der `IHostingStartup`-Implementierungsklasse.</span><span class="sxs-lookup"><span data-stu-id="b66c4-170">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span> <span data-ttu-id="b66c4-171">Die Konsolen-App wird veröffentlicht, um die Abhängigkeiten des Hostingstarts abzurufen.</span><span class="sxs-lookup"><span data-stu-id="b66c4-171">The console app is published to obtain the hosting startup's dependencies.</span></span>

<span data-ttu-id="b66c4-172">Die Veröffentlichung der Konsolen-App hat unter anderem zur Folge, dass nicht verwendete Abhängigkeiten aus der Abhängigkeitendatei entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="b66c4-172">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

<span data-ttu-id="b66c4-173">Die Abhängigkeitendatei wird so geändert, dass der Laufzeitspeicherort der Hostingstartassembly festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="b66c4-173">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span> <span data-ttu-id="b66c4-174">Die Hostingstartassembly und die dazu gehörende Abhängigkeitendatei werden im Laufzeitpaketspeicher abgelegt.</span><span class="sxs-lookup"><span data-stu-id="b66c4-174">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="b66c4-175">Damit die Hostingstartassembly und die entsprechende Abhängigkeitendatei erkannt werden, werden sie in einem Paar von Umgebungsvariablen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="b66c4-175">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span> <span data-ttu-id="b66c4-176">Die Konsolen-App verweist auf das Paket [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):</span><span class="sxs-lookup"><span data-stu-id="b66c4-176">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span> <span data-ttu-id="b66c4-177">Ein [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute)-Attribut identifiziert eine Klasse bei der Erstellung von <xref:Microsoft.AspNetCore.Hosting.IWebHost> als eine Implementierung von `IHostingStartup` zum Laden und Ausführen.</span><span class="sxs-lookup"><span data-stu-id="b66c4-177">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="b66c4-178">Im folgenden Beispiel ist der Namespace `StartupEnhancement` und die Klasse ist `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="b66c4-178">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="b66c4-179">Eine Klasse implementiert `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b66c4-179">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="b66c4-180">Die <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>-Methode der Klasse verwendet einen <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>, um einer App Erweiterungen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="b66c4-180">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="b66c4-181">`IHostingStartup.Configure` wird in der Hoststartassembly von der Runtime vor `Startup.Configure` im Benutzercode aufgerufen. Dadurch kann Benutzercode jede Konfiguration der Hoststartassembly überschreiben.</span><span class="sxs-lookup"><span data-stu-id="b66c4-181">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

<span data-ttu-id="b66c4-182">Die Datei für die Abhängigkeiten ( *.deps.json*) legt beim Erstellen eine `IHostingStartup`-Projekts den `runtime`-Speicherort der Assembly auf den *bin*-Ordner fest:</span><span class="sxs-lookup"><span data-stu-id="b66c4-182">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

1. <span data-ttu-id="b66c4-183">Nur ein Teil der Datei wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b66c4-183">Only part of the file is shown.</span></span> <span data-ttu-id="b66c4-184">`StartupEnhancement` ist der Name der Assembly im Beispiel.</span><span class="sxs-lookup"><span data-stu-id="b66c4-184">The assembly name in the example is `StartupEnhancement`.</span></span>
1. <span data-ttu-id="b66c4-185">Beim Hostingstart bereitgestellte Konfiguration</span><span class="sxs-lookup"><span data-stu-id="b66c4-185">Configuration provided by the hosting startup</span></span> <span data-ttu-id="b66c4-186">Es gibt zwei Ansätze zur Verarbeitung der Konfiguration, je nachdem, ob die Konfiguration des Hostingstarts oder die der App Vorrang haben soll:</span><span class="sxs-lookup"><span data-stu-id="b66c4-186">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="b66c4-187">Bereitstellen der Konfiguration für die App mit <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> zum Laden der Konfiguration, nachdem die <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>-Delegaten der App ausgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="b66c4-187">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span>

<span data-ttu-id="b66c4-188">Bei diesem Ansatz hat die Hostingstartkonfiguration Vorrang vor der Konfiguration der App.</span><span class="sxs-lookup"><span data-stu-id="b66c4-188">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span> <span data-ttu-id="b66c4-189">Bereitstellen der Konfiguration für die App mit <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> zum Laden der Konfiguration, bevor die <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>-Delegaten der App ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="b66c4-189">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span>

<span data-ttu-id="b66c4-190">Die Konfigurationswerte der App haben bei diesem Ansatz Vorrang vor denen des Hostingstarts.</span><span class="sxs-lookup"><span data-stu-id="b66c4-190">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span> <span data-ttu-id="b66c4-191">Angeben der Hostingstartassembly</span><span class="sxs-lookup"><span data-stu-id="b66c4-191">Specify the hosting startup assembly</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="b66c4-192">Geben Sie für einen von der Klassenbibliothek oder von der Konsolen-App bereitgestellten Hostingstart den Namen der Hostingstartassembly in der Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` an.</span><span class="sxs-lookup"><span data-stu-id="b66c4-192">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseSetting(
                    WebHostDefaults.HostingStartupAssembliesKey, 
                    "{ASSEMBLY1;ASSEMBLY2; ...}")
                .UseStartup<Startup>();
        });
```

<span data-ttu-id="b66c4-193">Die Umgebungsvariable ist eine durch Semikolons getrennte Liste von Assemblys.</span><span class="sxs-lookup"><span data-stu-id="b66c4-193">The environment variable is a semicolon-delimited list of assemblies.</span></span>

## <a name="activation"></a><span data-ttu-id="b66c4-194">Nur in Hostingstartassemblys wird nach dem `HostingStartup`-Attribut gesucht.</span><span class="sxs-lookup"><span data-stu-id="b66c4-194">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span>

<span data-ttu-id="b66c4-195">Damit die Beispiel-App *HostingStartupApp* die weiter oben beschriebenen Hostingstarts erkennt, wird für die Umgebungsvariable der folgende Wert festgelegt:</span><span class="sxs-lookup"><span data-stu-id="b66c4-195">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

* <span data-ttu-id="b66c4-196">Eine Hostingstartassembly kann auch mithilfe der Hostkonfigurationseinstellung „Hostingstartassemblys“ festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="b66c4-196">A hosting startup assembly can also be set using the Hosting Startup Assemblies host configuration setting:</span></span> <span data-ttu-id="b66c4-197">Wenn mehrere Hoststartassemblys vorhanden sind, werden ihre <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>-Methoden in der Reihenfolge der aufgelisteten Assemblys ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="b66c4-197">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span> <span data-ttu-id="b66c4-198">Aktivierung</span><span class="sxs-lookup"><span data-stu-id="b66c4-198">Activation</span></span>
* <span data-ttu-id="b66c4-199">Optionen für die Hostingstartaktivierung:</span><span class="sxs-lookup"><span data-stu-id="b66c4-199">Options for hosting startup activation are:</span></span>
  * <span data-ttu-id="b66c4-200">[Laufzeitspeicher:](#runtime-store) Für die Aktivierung ist kein Kompilierzeitverweis erforderlich.</span><span class="sxs-lookup"><span data-stu-id="b66c4-200">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span>
  * <span data-ttu-id="b66c4-201">Die Beispiel-App legt die Hostingstartassembly und die Abhängigkeitendateien im Ordner *deployment* ab, um die Bereitstellung des Hostingstarts in einer Umgebung mit mehreren Computern zu erleichtern.</span><span class="sxs-lookup"><span data-stu-id="b66c4-201">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span>

### <a name="runtime-store"></a><span data-ttu-id="b66c4-202">Der Ordner *deployment* enthält darüber hinaus auch ein PowerShell-Skript, das im Bereitstellungssystem Umgebungsvariablen erstellt oder ändert, um den Hostingstart zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="b66c4-202">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>

<span data-ttu-id="b66c4-203">Kompilierzeitverweis für die Aktivierung erforderlich</span><span class="sxs-lookup"><span data-stu-id="b66c4-203">Compile-time reference required for activation</span></span> [<span data-ttu-id="b66c4-204">NuGet-Paket</span><span class="sxs-lookup"><span data-stu-id="b66c4-204">NuGet package</span></span>](#nuget-package)

[<span data-ttu-id="b66c4-205">Ordner „Bin“ des Projekts</span><span class="sxs-lookup"><span data-stu-id="b66c4-205">Project bin folder</span></span>](#project-bin-folder)

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="b66c4-206">Laufzeitspeicher</span><span class="sxs-lookup"><span data-stu-id="b66c4-206">Runtime store</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="b66c4-207">Die Hostingstartimplementierung wird im [Laufzeitspeicher](/dotnet/core/deploying/runtime-store) abgelegt.</span><span class="sxs-lookup"><span data-stu-id="b66c4-207">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span>

<span data-ttu-id="b66c4-208">Ein Kompilierzeitverweis auf die Assembly wird von der erweiterten App nicht benötigt.</span><span class="sxs-lookup"><span data-stu-id="b66c4-208">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="b66c4-209">Nach der Erstellung des Hostingstarts wird mithilfe der Manifestprojektdatei und des Befehls [dotnet store](/dotnet/core/tools/dotnet-store) ein Laufzeitspeicher generiert.</span><span class="sxs-lookup"><span data-stu-id="b66c4-209">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span> <span data-ttu-id="b66c4-210">In der Beispiel-App (Projekt *RuntimeStore*) wird der folgende Befehl verwendet:</span><span class="sxs-lookup"><span data-stu-id="b66c4-210">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

* <span data-ttu-id="b66c4-211">Damit die Runtime den Laufzeitspeicher ermitteln kann, wird der Speicherort des Laufzeitspeichers der Umgebungsvariablen `DOTNET_SHARED_STORE` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b66c4-211">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>
* <span data-ttu-id="b66c4-212">**Ändern und Ablegen der Abhängigkeitendatei des Hostingstarts**</span><span class="sxs-lookup"><span data-stu-id="b66c4-212">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="b66c4-213">Um die Erweiterung ohne einen Paketverweis auf die Erweiterung zu aktivieren, geben Sie mit `additionalDeps` zusätzliche Abhängigkeiten zur Laufzeit an.</span><span class="sxs-lookup"><span data-stu-id="b66c4-213">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span>

 1. <span data-ttu-id="b66c4-214">`additionalDeps` ermöglicht Ihnen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="b66c4-214">`additionalDeps` allows you to:</span></span>
 1. <span data-ttu-id="b66c4-215">Erweitern des App-Bibliotheksdiagramms durch Bereitstellen einer Reihe zusätzlicher *.deps.json*-Dateien, die beim Start mit der App-eigenen *.deps.json*-Datei zusammengeführt werden.</span><span class="sxs-lookup"><span data-stu-id="b66c4-215">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>

<span data-ttu-id="b66c4-216">Bereitstellen der Hostingstartassembly, sodass sie ermittelt und geladen werden kann</span><span class="sxs-lookup"><span data-stu-id="b66c4-216">Make the hosting startup assembly discoverable and loadable.</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v3.0",
    "signature": ""
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v3.0": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp3.0/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-xrhzuNSyM5/f4ZswhooJ9dmIYLP64wMnqUJSyTKVDKDVj5T+qtzypl8JmM/aFJLLpYrf0FYpVWvGujd7/FfMEw==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="b66c4-217">Folgender Ansatz wird zum Generieren der zusätzlichen Abhängigkeitendatei empfohlen:</span><span class="sxs-lookup"><span data-stu-id="b66c4-217">The recommended approach for generating the additional dependencies file is to:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="b66c4-218">Führen Sie `dotnet publish` für die Manifestdatei des Laufzeitspeichers aus, auf die im vorherigen Abschnitt verwiesen wurde.</span><span class="sxs-lookup"><span data-stu-id="b66c4-218">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
* <span data-ttu-id="b66c4-219">Entfernen Sie den Manifestverweis aus den Bibliotheken und dem Abschnitt `runtime` der daraus resultierenden *.deps.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="b66c4-219">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>
* <span data-ttu-id="b66c4-220">Im Beispielprojekt wird die Eigenschaft `store.manifest/1.0.0` aus den Abschnitten `targets` und `libraries` entfernt:</span><span class="sxs-lookup"><span data-stu-id="b66c4-220">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>
* <span data-ttu-id="b66c4-221">Legen Sie die *.deps.json*-Datei an folgendem Speicherort ab:</span><span class="sxs-lookup"><span data-stu-id="b66c4-221">Place the *.deps.json* file into the following location:</span></span>

<span data-ttu-id="b66c4-222">`{ADDITIONAL DEPENDENCIES PATH}`: Der Speicherort wurde zur Umgebungsvariable `DOTNET_ADDITIONAL_DEPS` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b66c4-222">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="b66c4-223">`{SHARED FRAMEWORK NAME}`: Dies ist das freigegebene Framework, das für diese zusätzliche Abhängigkeitsdatei erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="b66c4-223">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>

<span data-ttu-id="b66c4-224">`{SHARED FRAMEWORK VERSION}`: Dies ist die Mindestversion des freigegebenen Frameworks.</span><span class="sxs-lookup"><span data-stu-id="b66c4-224">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>

<span data-ttu-id="b66c4-225">`{ENHANCEMENT ASSEMBLY NAME}`: Dies ist der Name der Erweiterungsassembly.</span><span class="sxs-lookup"><span data-stu-id="b66c4-225">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="b66c4-226">In der Beispiel-App (Projekt *RuntimeStore*) wird die zusätzliche Abhängigkeitendatei an folgendem Speicherort abgelegt:</span><span class="sxs-lookup"><span data-stu-id="b66c4-226">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

<span data-ttu-id="b66c4-227">Damit die Runtime den Speicherort des Laufzeitspeichers ermitteln kann, wir die zusätzliche Abhängigkeitendatei der Umgebungsvariablen `DOTNET_ADDITIONAL_DEPS` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b66c4-227">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

* <span data-ttu-id="b66c4-228">In der Beispiel-App (Projekt *RuntimeStore*) wird das Erstellen des Laufzeitspeichers und das Generieren der zusätzlichen Abhängigkeitendatei durch ein [PowerShell](/powershell/scripting/powershell-scripting)-Skript durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="b66c4-228">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>
* <span data-ttu-id="b66c4-229">Beispiele zum Festlegen von Umgebungsvariablen für verschiedene Betriebssysteme finden Sie unter [Use multiple environments (Verwenden mehrerer Umgebungen)](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="b66c4-229">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>
* <span data-ttu-id="b66c4-230">**Bereitstellung**</span><span class="sxs-lookup"><span data-stu-id="b66c4-230">**Deployment**</span></span> <span data-ttu-id="b66c4-231">Um die Bereitstellung eines Hostingstarts in einer Umgebung mit mehreren Computern zu erleichtern, erstellt die Beispiel-App in der veröffentlichten Ausgabe einen *deployment*-Ordner, der Folgendes enthält:</span><span class="sxs-lookup"><span data-stu-id="b66c4-231">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

### <a name="nuget-package"></a><span data-ttu-id="b66c4-232">Der Laufzeitspeicher des Hostingstarts.</span><span class="sxs-lookup"><span data-stu-id="b66c4-232">The hosting startup runtime store.</span></span>

<span data-ttu-id="b66c4-233">Die Abhängigkeitendatei des Hostingstarts.</span><span class="sxs-lookup"><span data-stu-id="b66c4-233">The hosting startup dependencies file.</span></span> <span data-ttu-id="b66c4-234">Ein PowerShell-Skript, das `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` und `DOTNET_ADDITIONAL_DEPS` erstellt oder ändert, um die Aktivierung des Hostingstarts zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="b66c4-234">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="b66c4-235">Führen Sie das Skript über eine PowerShell-Administratoreingabeaufforderung auf dem Bereitstellungssystem aus.</span><span class="sxs-lookup"><span data-stu-id="b66c4-235">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

* <span data-ttu-id="b66c4-236">NuGet-Paket</span><span class="sxs-lookup"><span data-stu-id="b66c4-236">NuGet package</span></span> <span data-ttu-id="b66c4-237">In einem NuGet-Paket kann eine Hostingstarterweiterung bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="b66c4-237">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="b66c4-238">Das Paket enthält ein `HostingStartup`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="b66c4-238">The package has a `HostingStartup` attribute.</span></span>
* <span data-ttu-id="b66c4-239">Die vom Paket bereitgestellten Hostingstarttypen werden für die App mit einem der folgenden Verfahren verfügbar gemacht:</span><span class="sxs-lookup"><span data-stu-id="b66c4-239">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

<span data-ttu-id="b66c4-240">Die Projektdatei der erweiterten App erstellt einen Paketverweis für den Hostingstart in der Projektdatei der App (Kompilierzeitverweis).</span><span class="sxs-lookup"><span data-stu-id="b66c4-240">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span>

* <span data-ttu-id="b66c4-241">Wenn ein Kompilierzeitverweis eingerichtet ist, werden die Hostingstartassembly und alle dazu gehörenden Abhängigkeiten in die Abhängigkeitendatei der App ( *.deps.json*) eingebunden.</span><span class="sxs-lookup"><span data-stu-id="b66c4-241">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span>
* <span data-ttu-id="b66c4-242">Dieses Verfahren gilt für ein in [nuget.org](https://www.nuget.org/) veröffentlichtes Hostingstartassembly-Paket.</span><span class="sxs-lookup"><span data-stu-id="b66c4-242">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="b66c4-243">Die Abhängigkeitendatei des Hostingstarts wird für die erweiterte App wie im Abschnitt [Laufzeitspeicher](#runtime-store) beschrieben (ohne Kompilierzeitverweis) verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="b66c4-243">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

### <a name="project-bin-folder"></a><span data-ttu-id="b66c4-244">Weitere Informationen zu NuGet-Paketen und den Laufzeitspeicher finden Sie in den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="b66c4-244">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

[<span data-ttu-id="b66c4-245">So erstellen Sie ein NuGet-Paket mit plattformübergreifenden Tools</span><span class="sxs-lookup"><span data-stu-id="b66c4-245">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages) [<span data-ttu-id="b66c4-246">Veröffentlichen von Paketen</span><span class="sxs-lookup"><span data-stu-id="b66c4-246">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)

* [<span data-ttu-id="b66c4-247">Laufzeitpaketspeicher</span><span class="sxs-lookup"><span data-stu-id="b66c4-247">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store) <span data-ttu-id="b66c4-248">Ordner „Bin“ des Projekts</span><span class="sxs-lookup"><span data-stu-id="b66c4-248">Project bin folder</span></span> <span data-ttu-id="b66c4-249">Eine Hostingstarterweiterung kann durch eine mittels *bin* bereitgestellte Assembly in der erweiterten App bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="b66c4-249">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span>
  * <span data-ttu-id="b66c4-250">Die von der Assembly bereitgestellten Hostingstarttypen werden für die App mit einem der folgenden Verfahren verfügbar gemacht:</span><span class="sxs-lookup"><span data-stu-id="b66c4-250">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>
  * <span data-ttu-id="b66c4-251">Die Projektdatei der erweiterten App enthält einen Assemblyverweis auf den Hostingstart (Kompilierzeitverweis).</span><span class="sxs-lookup"><span data-stu-id="b66c4-251">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span>
* <span data-ttu-id="b66c4-252">Wenn ein Kompilierzeitverweis eingerichtet ist, werden die Hostingstartassembly und alle dazu gehörenden Abhängigkeiten in die Abhängigkeitendatei der App ( *.deps.json*) eingebunden.</span><span class="sxs-lookup"><span data-stu-id="b66c4-252">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span>
* <span data-ttu-id="b66c4-253">Dieser Ansatz gilt, wenn das Bereitstellungsszenario erfordert, dass ein Kompilierzeitverweis auf die Assembly des Hostingstarts (*DLL*-Datei) erstellt und die Assembly in eine der beiden Dateien verschoben wird:</span><span class="sxs-lookup"><span data-stu-id="b66c4-253">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="b66c4-254">Das verbrauchende Projekt.</span><span class="sxs-lookup"><span data-stu-id="b66c4-254">The consuming project.</span></span>
  * <span data-ttu-id="b66c4-255">Einen Speicherort, auf den das verbrauchende Projekt zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="b66c4-255">A location accessible by the consuming project.</span></span> <span data-ttu-id="b66c4-256">Die Abhängigkeitendatei des Hostingstarts wird für die erweiterte App wie im Abschnitt [Laufzeitspeicher](#runtime-store) beschrieben (ohne Kompilierzeitverweis) verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="b66c4-256">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

## <a name="sample-code"></a><span data-ttu-id="b66c4-257">Wenn das Ziel .NET Framework ist, kann die Assembly in den Standardladekontext geladen werden, was bei .NET Framework bedeutet, dass sich die Assembly an einem der folgenden Speicherorte befindet:</span><span class="sxs-lookup"><span data-stu-id="b66c4-257">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>

<span data-ttu-id="b66c4-258">Anwendungsbasispfad: Dabei handelt es sich um den Ordner *bin*, in dem die ausführbare Datei ( *.exe*) der App gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="b66c4-258">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>

* <span data-ttu-id="b66c4-259">Globaler Assemblycache (GAC): Der GAC speichert Assemblys, die mehrere .NET Framework-Apps gemeinsam nutzen.</span><span class="sxs-lookup"><span data-stu-id="b66c4-259">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span>
  * <span data-ttu-id="b66c4-260">Weitere Informationen finden Sie unter [Vorgehensweise: Installieren einer Assembly in den globalen Assemblycache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in der .NET Framework-Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="b66c4-260">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>
  * <span data-ttu-id="b66c4-261">Beispielcode</span><span class="sxs-lookup"><span data-stu-id="b66c4-261">Sample code</span></span>
* <span data-ttu-id="b66c4-262">Der [Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Herunterladen](xref:index#how-to-download-a-sample)) zeigt Szenarios für die Hostingstartimplementierung:</span><span class="sxs-lookup"><span data-stu-id="b66c4-262">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span> <span data-ttu-id="b66c4-263">Zwei Hostingstartassemblys (Klassenbibliotheken) legen zwei Schlüssel-Wert-Paare der Konfiguration im Arbeitsspeicher fest:</span><span class="sxs-lookup"><span data-stu-id="b66c4-263">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="b66c4-264">NuGet-Paket (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="b66c4-264">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="b66c4-265">Klassenbibliothek (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="b66c4-265">Class library (*HostingStartupLibrary*)</span></span>
  * <span data-ttu-id="b66c4-266">Ein Hostingstart wird über eine im Laufzeitspeicher bereitgestellte Assembly (*StartupDiagnostics*) aktiviert.</span><span class="sxs-lookup"><span data-stu-id="b66c4-266">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span>
  * <span data-ttu-id="b66c4-267">Die Assembly fügt der App zum Start zwei Middlewares hinzu, die Diagnoseinformationen zu folgenden Komponenten bereitstellen:</span><span class="sxs-lookup"><span data-stu-id="b66c4-267">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="b66c4-268">Registrierte Dienste</span><span class="sxs-lookup"><span data-stu-id="b66c4-268">Registered services</span></span>

<span data-ttu-id="b66c4-269">Adresse (Schema, Host, Basispfad, Pfad, Abfragezeichenfolge)</span><span class="sxs-lookup"><span data-stu-id="b66c4-269">Address (scheme, host, path base, path, query string)</span></span>

<span data-ttu-id="b66c4-270">Verbindung (Remote-IP, Remoteport, lokale IP, lokaler Port, Clientzertifikat)</span><span class="sxs-lookup"><span data-stu-id="b66c4-270">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>

1. <span data-ttu-id="b66c4-271">Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="b66c4-271">Request headers</span></span>
1. <span data-ttu-id="b66c4-272">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="b66c4-272">Environment variables</span></span>
1. <span data-ttu-id="b66c4-273">So führen Sie das Beispiel aus:</span><span class="sxs-lookup"><span data-stu-id="b66c4-273">To run the sample:</span></span> <span data-ttu-id="b66c4-274">**Aktivierung über ein NuGet-Paket**</span><span class="sxs-lookup"><span data-stu-id="b66c4-274">**Activation from a NuGet package**</span></span> <span data-ttu-id="b66c4-275">Kompilieren Sie das Paket *HostingStartupPackage* mit dem Befehl [dotnet pack](/dotnet/core/tools/dotnet-pack).</span><span class="sxs-lookup"><span data-stu-id="b66c4-275">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span> <span data-ttu-id="b66c4-276">Fügen Sie den Assemblynamen des Pakets *HostingStartupPackage* zur Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` hinzu.</span><span class="sxs-lookup"><span data-stu-id="b66c4-276">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="b66c4-277">Kompilieren Sie die App, und führen Sie diese aus.</span><span class="sxs-lookup"><span data-stu-id="b66c4-277">Compile and run the app.</span></span>

<span data-ttu-id="b66c4-278">In der erweiterten App ist ein Paketverweis vorhanden (Kompilierzeitverweis).</span><span class="sxs-lookup"><span data-stu-id="b66c4-278">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="b66c4-279">Eine `<PropertyGroup>` in der Projektdatei der App gibt die Ausgabe des Paketprojekts ( *../HostingStartupPackage/bin/Debug*) als Paketquelle an.</span><span class="sxs-lookup"><span data-stu-id="b66c4-279">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="b66c4-280">Dadurch kann die Anwendung das Paket verwenden, ohne das Paket in [nuget.org](https://www.nuget.org/) hochzuladen. Weitere Informationen finden Sie in den Hinweisen in der Projektdatei von HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="b66c4-280">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

1. <span data-ttu-id="b66c4-281">Die von der Indexseite gerenderten Schlüsselwerte der Dienstkonfiguration entsprechen den durch die `ServiceKeyInjection.Configure`-Methode des Pakets festgelegten Werten.</span><span class="sxs-lookup"><span data-stu-id="b66c4-281">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>
1. <span data-ttu-id="b66c4-282">Wenn Sie am Projekt *HostingStartupPackage* Änderungen vornehmen und das Projekt erneut kompilieren, bereinigen Sie die lokalen NuGet-Paketcaches, um sicherzustellen, dass die *HostingStartupApp* das aktualisierte Paket erhält und kein veraltetes Paket aus dem lokalen Cache.</span><span class="sxs-lookup"><span data-stu-id="b66c4-282">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span>
1. <span data-ttu-id="b66c4-283">Um die lokalen NuGet-Caches zu bereinigen, führen Sie den folgenden [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals)-Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="b66c4-283">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>
1. <span data-ttu-id="b66c4-284">**Aktivierung über eine Klassenbibliothek**</span><span class="sxs-lookup"><span data-stu-id="b66c4-284">**Activation from a class library**</span></span> <span data-ttu-id="b66c4-285">Kompilieren Sie die Klassenbibliothek *HostingStartupLibrary* mit dem Befehl [dotnet build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="b66c4-285">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span> <span data-ttu-id="b66c4-286">Fügen Sie der Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` den Assemblynamen *HostingStartupLibrary* der Klassenbibliothek hinzu.</span><span class="sxs-lookup"><span data-stu-id="b66c4-286">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="b66c4-287">Stellen Sie die Assembly der Klassenbibliothek mittels *bin* für die App bereit, indem Sie die Datei *HostingStartupLibrary.dll* aus der kompilierten Ausgabe der Klassenbibliothek in den Ordner *bin/Debug* der App kopieren.</span><span class="sxs-lookup"><span data-stu-id="b66c4-287">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>

<span data-ttu-id="b66c4-288">Kompilieren Sie die App, und führen Sie diese aus.</span><span class="sxs-lookup"><span data-stu-id="b66c4-288">Compile and run the app.</span></span>

1. <span data-ttu-id="b66c4-289">Eine `<ItemGroup>` in der Projektdatei der App verweist auf die Assembly der Klassenbibliothek ( *.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) – Kompilierzeitverweis.</span><span class="sxs-lookup"><span data-stu-id="b66c4-289">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="b66c4-290">Weitere Informationen finden Sie in den Hinweisen in der Projektdatei von HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="b66c4-290">For more information, see the notes in the HostingStartupApp's project file.</span></span> <span data-ttu-id="b66c4-291">Die von der Indexseite gerenderten Schlüsselwerte der Dienstkonfiguration entsprechen den durch die `ServiceKeyInjection.Configure`-Methode der Klassenbibliothek festgelegten Werten.</span><span class="sxs-lookup"><span data-stu-id="b66c4-291">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>
1. <span data-ttu-id="b66c4-292">**Aktivierung über eine mittels Laufzeitspeicher bereitgestellten Assembly**</span><span class="sxs-lookup"><span data-stu-id="b66c4-292">**Activation from a runtime store-deployed assembly**</span></span> <span data-ttu-id="b66c4-293">Im Projekt *StartupDiagnostics* wird [PowerShell](/powershell/scripting/powershell-scripting) verwendet, um die Datei *StartupDiagnostics.deps.json* zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="b66c4-293">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span>
   * <span data-ttu-id="b66c4-294">PowerShell ist standardmäßig auf Windows-Betriebssystemen ab Windows 7 SP1 und Windows Server 2008 R2 SP1 installiert.</span><span class="sxs-lookup"><span data-stu-id="b66c4-294">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span>
   * <span data-ttu-id="b66c4-295">Im Artikel [Installieren verschiedener Versionen von PowerShell](/powershell/scripting/install/installing-powershell) erfahren Sie, wie Sie PowerShell auf anderen Plattformen erwerben können.</span><span class="sxs-lookup"><span data-stu-id="b66c4-295">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span> <span data-ttu-id="b66c4-296">Führen Sie das Skript *build.ps1* im Ordner *RuntimeStore* aus.</span><span class="sxs-lookup"><span data-stu-id="b66c4-296">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="b66c4-297">Das Skript:</span><span class="sxs-lookup"><span data-stu-id="b66c4-297">The script:</span></span> <span data-ttu-id="b66c4-298">Generiert das `StartupDiagnostics`-Paket im Ordner *obj\packages* .</span><span class="sxs-lookup"><span data-stu-id="b66c4-298">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span> <span data-ttu-id="b66c4-299">Generiert den Laufzeitspeicher für `StartupDiagnostics` im Ordner *store*.</span><span class="sxs-lookup"><span data-stu-id="b66c4-299">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span>
   * <span data-ttu-id="b66c4-300">Der `dotnet store`-Befehl im Skript verwendet den [Laufzeitbezeichner (RID)](/dotnet/core/rid-catalog) `win7-x64` für einen für Windows bereitgestellten Hostingstart.</span><span class="sxs-lookup"><span data-stu-id="b66c4-300">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="b66c4-301">Wenn der Hostingstart für eine andere Laufzeit bereitgestellt wird, muss in Zeile 37 die entsprechende RID eingegeben werden.</span><span class="sxs-lookup"><span data-stu-id="b66c4-301">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="b66c4-302">Der Runtimespeicher für `StartupDiagnostics` würde später in den Runtimespeicher des Benutzers oder Systems auf dem Computer verschoben, auf dem die Assembly verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="b66c4-302">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span>
   * <span data-ttu-id="b66c4-303">Der Installationsspeicherort für den Benutzerruntimespeicher für die `StartupDiagnostics`-Assembly ist *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="b66c4-303">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span></span>
1. <span data-ttu-id="b66c4-304">Generiert die `additionalDeps` für `StartupDiagnostics` im Ordner *additionalDeps* .</span><span class="sxs-lookup"><span data-stu-id="b66c4-304">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="b66c4-305">Die zusätzlichen Abhängigkeiten würden später in die zusätzlichen Abhängigkeiten des Benutzers oder Systems verschoben.</span><span class="sxs-lookup"><span data-stu-id="b66c4-305">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span>
   * <span data-ttu-id="b66c4-306">Der Installationsspeicherort der zusätzlichen Abhängigkeiten des Benutzers `StartupDiagnostics` ist *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="b66c4-306">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="b66c4-307">Legt die Datei *deploy.ps1* im Ordner *deployment* ab.</span><span class="sxs-lookup"><span data-stu-id="b66c4-307">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
   * <span data-ttu-id="b66c4-308">Führen Sie das Skript *deploy.ps1* im Ordner *deployment* aus.</span><span class="sxs-lookup"><span data-stu-id="b66c4-308">Run the *deploy.ps1* script in the *deployment* folder.</span></span>
1. <span data-ttu-id="b66c4-309">Das Skript fügt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="b66c4-309">The script appends:</span></span>
1. <span data-ttu-id="b66c4-310">`StartupDiagnostics` an die Umgebungsvariable `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="b66c4-310">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="b66c4-311">Der Pfad der Hostingstartabhängigkeiten (im Ordner *deployment* des RuntimeStore-Projekts) zur `DOTNET_ADDITIONAL_DEPS`-Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="b66c4-311">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b66c4-312">Der Pfad des Runtimespeichers (im Ordner *deployment* des RuntimeStore-Projekts) zur `DOTNET_SHARED_STORE`-Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="b66c4-312">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span> <span data-ttu-id="b66c4-313">Führen Sie die Beispiel-App aus.</span><span class="sxs-lookup"><span data-stu-id="b66c4-313">Run the sample app.</span></span>

<span data-ttu-id="b66c4-314">Fordern Sie den Endpunkt `/services` an, um die registrierten Dienste der App anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="b66c4-314">Request the `/services` endpoint to see the app's registered services.</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="b66c4-315">Fordern Sie den Endpunkt `/diag` an, um Diagnoseinformationen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="b66c4-315">Request the `/diag` endpoint to see the diagnostic information.</span></span>

<span data-ttu-id="b66c4-316">Mit einer <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>-Implementierung (Hostingstart) werden Verbesserungen an einer App beim Start von einer externen Assembly aus vorgenommen.</span><span class="sxs-lookup"><span data-stu-id="b66c4-316">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span>

<span data-ttu-id="b66c4-317">Eine externe Bibliothek kann beispielsweise eine Hostingstartimplementierung verwenden, um zusätzliche Konfigurationsanbieter oder -dienste für eine App bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="b66c4-317">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span> <span data-ttu-id="b66c4-318">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b66c4-318">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span> <span data-ttu-id="b66c4-319">Das Attribut HostingStartup</span><span class="sxs-lookup"><span data-stu-id="b66c4-319">HostingStartup attribute</span></span> <span data-ttu-id="b66c4-320">Ein [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute)-Attribut gibt an, ob eine Hostingstartassembly vorhanden ist, die zur Laufzeit aktiviert werden kann.</span><span class="sxs-lookup"><span data-stu-id="b66c4-320">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="b66c4-321">Bei der Einstiegsassembly oder bei der Assembly, die die Klasse `Startup` enthält, wird automatisch geprüft, ob sie das Attribut `HostingStartup` enthält.</span><span class="sxs-lookup"><span data-stu-id="b66c4-321">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="b66c4-322">Die Liste der Assemblys für die Suche nach `HostingStartup`-Attributen wird zur Laufzeit aus der Konfiguration in [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey) geladen.</span><span class="sxs-lookup"><span data-stu-id="b66c4-322">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="b66c4-323">Die Liste der Assemblys, die aus der Ermittlung ausgeschlossen werden sollen, wird aus [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey) geladen.</span><span class="sxs-lookup"><span data-stu-id="b66c4-323">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="b66c4-324">Weitere Informationen finden Sie unter [Webhost: Hostingstartassemblys](xref:fundamentals/host/web-host#hosting-startup-assemblies)[Webhost: Auszuschließende Hostingstartassemblys](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="b66c4-324">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="b66c4-325">Im folgenden Beispiel lautet der Namespace der Hostingstartassembly `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="b66c4-325">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="b66c4-326">`StartupEnhancementHostingStartup` ist die Klasse mit dem Hostingstartcode:</span><span class="sxs-lookup"><span data-stu-id="b66c4-326">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span> <span data-ttu-id="b66c4-327">Das Attribut `HostingStartup` befindet sich in der Regel in der `IHostingStartup`-Implementierungsklassendatei der Hostingstartassembly.</span><span class="sxs-lookup"><span data-stu-id="b66c4-327">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="b66c4-328">Ermitteln geladener Hostingstartassemblys</span><span class="sxs-lookup"><span data-stu-id="b66c4-328">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="b66c4-329">Zum Ermitteln von geladenen Hostingstartassemblys aktivieren Sie die Protokollierung und überprüfen die Protokolle der App.</span><span class="sxs-lookup"><span data-stu-id="b66c4-329">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span>

* <span data-ttu-id="b66c4-330">Fehler, die beim Laden von Assemblys auftreten, werden protokolliert.</span><span class="sxs-lookup"><span data-stu-id="b66c4-330">Errors that occur when loading assemblies are logged.</span></span>
  * <span data-ttu-id="b66c4-331">Geladene Hostingstartassemblys werden auf der Debugebene protokolliert. Außerdem werden alle Fehler protokolliert.</span><span class="sxs-lookup"><span data-stu-id="b66c4-331">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>
  * <span data-ttu-id="b66c4-332">Deaktivieren des automatischen Ladens von Hostingstartassemblys</span><span class="sxs-lookup"><span data-stu-id="b66c4-332">Disable automatic loading of hosting startup assemblies</span></span>
* <span data-ttu-id="b66c4-333">Um das automatische Laden von Hostingstartassemblys zu deaktivieren, verwenden Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="b66c4-333">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>
  * <span data-ttu-id="b66c4-334">Um zu verhindern, dass alle Hostingstartassemblys geladen werden, legen Sie eine der folgenden Einstellungen auf `true` oder `1` fest:</span><span class="sxs-lookup"><span data-stu-id="b66c4-334">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="b66c4-335">Hostkonfigurationseinstellung [Verhindern des Hostingstarts](xref:fundamentals/host/web-host#prevent-hosting-startup).</span><span class="sxs-lookup"><span data-stu-id="b66c4-335">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>

<span data-ttu-id="b66c4-336">Die Umgebungsvariable `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="b66c4-336">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

<span data-ttu-id="b66c4-337">Um zu verhindern, dass bestimmte Hostingstartassemblys geladen werden, legen Sie eine der folgenden Einstellungen auf eine durch Semikolons getrennte Zeichenfolge mit Hostingstartassemblys fest, die beim Starten ausgeschlossen werden sollen:</span><span class="sxs-lookup"><span data-stu-id="b66c4-337">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>

## <a name="project"></a><span data-ttu-id="b66c4-338">Hostkonfigurationseinstellung [Hostingstartausschlussassemblys](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="b66c4-338">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>

<span data-ttu-id="b66c4-339">Die Umgebungsvariable `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="b66c4-339">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

* <span data-ttu-id="b66c4-340">Wenn sowohl die Konfigurationseinstellung für den Host als auch die Umgebungsvariable festgelegt werden, wird das Verhalten durch die Hosteinstellung gesteuert.</span><span class="sxs-lookup"><span data-stu-id="b66c4-340">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>
* <span data-ttu-id="b66c4-341">Durch das Deaktivieren von Hostingstartassemblys mithilfe der Hosteinstellung oder der Umgebungsvariable wird die Assembly global deaktiviert. Einige Eigenschaften einer App können ebenfalls deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="b66c4-341">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

### <a name="class-library"></a><span data-ttu-id="b66c4-342">Projekt</span><span class="sxs-lookup"><span data-stu-id="b66c4-342">Project</span></span>

<span data-ttu-id="b66c4-343">Erstellen Sie einen Hostingstart mit einem der folgenden Projekttypen:</span><span class="sxs-lookup"><span data-stu-id="b66c4-343">Create a hosting startup with either of the following project types:</span></span> [<span data-ttu-id="b66c4-344">Klassenbibliothek</span><span class="sxs-lookup"><span data-stu-id="b66c4-344">Class library</span></span>](#class-library)

[<span data-ttu-id="b66c4-345">Konsolen-App ohne Einstiegspunkt</span><span class="sxs-lookup"><span data-stu-id="b66c4-345">Console app without an entry point</span></span>](#console-app-without-an-entry-point) <span data-ttu-id="b66c4-346">Klassenbibliothek</span><span class="sxs-lookup"><span data-stu-id="b66c4-346">Class library</span></span>

* <span data-ttu-id="b66c4-347">In einer Klassenbibliothek kann eine Hostingstarterweiterung bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="b66c4-347">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="b66c4-348">Die Bibliothek enthält ein `HostingStartup`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="b66c4-348">The library contains a `HostingStartup` attribute.</span></span>
* <span data-ttu-id="b66c4-349">Der [Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) enthält die Razor Pages-App *HostingStartupApp* und die Klassenbibliothek *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="b66c4-349">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span>

<span data-ttu-id="b66c4-350">Die Klassenbibliothek:</span><span class="sxs-lookup"><span data-stu-id="b66c4-350">The class library:</span></span>

<span data-ttu-id="b66c4-351">Enthält die Hostingstartklasse `ServiceKeyInjection`, die `IHostingStartup` implementiert.</span><span class="sxs-lookup"><span data-stu-id="b66c4-351">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="b66c4-352">`ServiceKeyInjection` fügt mithilfe des Anbieters der Konfiguration im Arbeitsspeicher ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)) der App-Konfiguration zwei Dienstzeichenfolgen hinzu.</span><span class="sxs-lookup"><span data-stu-id="b66c4-352">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>

<span data-ttu-id="b66c4-353">Enthält ein `HostingStartup`-Attribut, das den Namespace und die Klasse des Hostingstarts angibt.</span><span class="sxs-lookup"><span data-stu-id="b66c4-353">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="b66c4-354">Die <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>-Methode der `ServiceKeyInjection`-Klasse verwendet einen <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>, um einer App Erweiterungen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="b66c4-354">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="b66c4-355">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="b66c4-355">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span> <span data-ttu-id="b66c4-356">Die Indexseite der App liest und rendert die Konfigurationswerte für die beiden Schlüssel, die von der Hostingstartassembly der Klassenbibliothek festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="b66c4-356">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

* <span data-ttu-id="b66c4-357">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="b66c4-357">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span> <span data-ttu-id="b66c4-358">Der [Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) enthält darüber hinaus auch ein NuGet-Paketprojekt, das den separaten Hostingstart *HostingStartupPackage* bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="b66c4-358">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span>
* <span data-ttu-id="b66c4-359">Das Paket weist dieselben Merkmale wie die bereits beschriebene Klassenbibliothek auf.</span><span class="sxs-lookup"><span data-stu-id="b66c4-359">The package has the same characteristics of the class library described earlier.</span></span>

<span data-ttu-id="b66c4-360">Das Paket:</span><span class="sxs-lookup"><span data-stu-id="b66c4-360">The package:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="b66c4-361">Enthält die Hostingstartklasse `ServiceKeyInjection`, die `IHostingStartup` implementiert.</span><span class="sxs-lookup"><span data-stu-id="b66c4-361">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span>

<span data-ttu-id="b66c4-362">`ServiceKeyInjection` fügt der App-Konfiguration zwei Dienstzeichenfolgen hinzu.</span><span class="sxs-lookup"><span data-stu-id="b66c4-362">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="b66c4-363">Enthält ein `HostingStartup`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="b66c4-363">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="b66c4-364">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="b66c4-364">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

<span data-ttu-id="b66c4-365">Die Indexseite der App liest und rendert die Konfigurationswerte für die beiden Schlüssel, die von der Hostingstartassembly der Bibliothek festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="b66c4-365">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span> <span data-ttu-id="b66c4-366">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="b66c4-366">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

<span data-ttu-id="b66c4-367">Konsolen-App ohne Einstiegspunkt</span><span class="sxs-lookup"><span data-stu-id="b66c4-367">Console app without an entry point</span></span>

* <span data-ttu-id="b66c4-368">*Dieses Verfahren ist nur für .NET Core-Apps, nicht jedoch für .NET Framework verfügbar.*</span><span class="sxs-lookup"><span data-stu-id="b66c4-368">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span> <span data-ttu-id="b66c4-369">Eine dynamische Hostingstarterweiterung, die zur Aktivierung keinen Kompilierungszeitverweis erfordert, kann in einer Konsolen-App ohne Einstiegspunkt bereitgestellt werden, die ein `HostingStartup`-Attribut enthält.</span><span class="sxs-lookup"><span data-stu-id="b66c4-369">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span>
* <span data-ttu-id="b66c4-370">Durch die Veröffentlichung der Konsolen-App wird eine Hostingstartassembly erstellt, die über den Laufzeitspeicher genutzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="b66c4-370">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="b66c4-371">In diesem Prozess wird aus folgenden Gründen eine Konsolen-App ohne Einstiegspunkt verwendet:</span><span class="sxs-lookup"><span data-stu-id="b66c4-371">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="b66c4-372">Eine Abhängigkeitendatei ist erforderlich, um den Hostingstart in der Hostingstartassembly zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="b66c4-372">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span>
  * <span data-ttu-id="b66c4-373">Eine Abhängigkeitendatei ist eine ausführbare App-Ressource, die durch das Veröffentlichen einer App, nicht einer Bibliothek, erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="b66c4-373">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
  * <span data-ttu-id="b66c4-374">Eine Bibliothek kann dem [Laufzeitpaketspeicher](/dotnet/core/deploying/runtime-store), der ein ausführbares Projekt benötigt, das die freigegebene Laufzeit als Ziel verwendet, nicht direkt hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="b66c4-374">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>
* <span data-ttu-id="b66c4-375">Bei der Erstellung eines dynamischen Hostingstarts geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="b66c4-375">In the creation of a dynamic hosting startup:</span></span> <span data-ttu-id="b66c4-376">Eine Hostingstartassembly wird über die Konsolen-App ohne Einstiegspunkt erstellt, für die Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="b66c4-376">A hosting startup assembly is created from the console app without an entry point that:</span></span>
* <span data-ttu-id="b66c4-377">Sie enthält eine Klasse mit der `IHostingStartup`-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="b66c4-377">Includes a class that contains the `IHostingStartup` implementation.</span></span>
* <span data-ttu-id="b66c4-378">Sie enthält ein [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute)-Attribut zum Identifizieren der `IHostingStartup`-Implementierungsklasse.</span><span class="sxs-lookup"><span data-stu-id="b66c4-378">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span> <span data-ttu-id="b66c4-379">Die Konsolen-App wird veröffentlicht, um die Abhängigkeiten des Hostingstarts abzurufen.</span><span class="sxs-lookup"><span data-stu-id="b66c4-379">The console app is published to obtain the hosting startup's dependencies.</span></span>

<span data-ttu-id="b66c4-380">Die Veröffentlichung der Konsolen-App hat unter anderem zur Folge, dass nicht verwendete Abhängigkeiten aus der Abhängigkeitendatei entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="b66c4-380">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="b66c4-381">Die Abhängigkeitendatei wird so geändert, dass der Laufzeitspeicherort der Hostingstartassembly festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="b66c4-381">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span> <span data-ttu-id="b66c4-382">Die Hostingstartassembly und die dazu gehörende Abhängigkeitendatei werden im Laufzeitpaketspeicher abgelegt.</span><span class="sxs-lookup"><span data-stu-id="b66c4-382">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="b66c4-383">Damit die Hostingstartassembly und die entsprechende Abhängigkeitendatei erkannt werden, werden sie in einem Paar von Umgebungsvariablen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="b66c4-383">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span> <span data-ttu-id="b66c4-384">Die Konsolen-App verweist auf das Paket [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):</span><span class="sxs-lookup"><span data-stu-id="b66c4-384">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span> <span data-ttu-id="b66c4-385">Ein [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute)-Attribut identifiziert eine Klasse bei der Erstellung von <xref:Microsoft.AspNetCore.Hosting.IWebHost> als eine Implementierung von `IHostingStartup` zum Laden und Ausführen.</span><span class="sxs-lookup"><span data-stu-id="b66c4-385">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="b66c4-386">Im folgenden Beispiel ist der Namespace `StartupEnhancement` und die Klasse ist `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="b66c4-386">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="b66c4-387">Eine Klasse implementiert `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b66c4-387">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="b66c4-388">Die <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>-Methode der Klasse verwendet einen <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>, um einer App Erweiterungen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="b66c4-388">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="b66c4-389">`IHostingStartup.Configure` wird in der Hoststartassembly von der Runtime vor `Startup.Configure` im Benutzercode aufgerufen. Dadurch kann Benutzercode jede Konfiguration der Hoststartassembly überschreiben.</span><span class="sxs-lookup"><span data-stu-id="b66c4-389">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

<span data-ttu-id="b66c4-390">Die Datei für die Abhängigkeiten ( *.deps.json*) legt beim Erstellen eine `IHostingStartup`-Projekts den `runtime`-Speicherort der Assembly auf den *bin*-Ordner fest:</span><span class="sxs-lookup"><span data-stu-id="b66c4-390">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

1. <span data-ttu-id="b66c4-391">Nur ein Teil der Datei wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b66c4-391">Only part of the file is shown.</span></span> <span data-ttu-id="b66c4-392">`StartupEnhancement` ist der Name der Assembly im Beispiel.</span><span class="sxs-lookup"><span data-stu-id="b66c4-392">The assembly name in the example is `StartupEnhancement`.</span></span>
1. <span data-ttu-id="b66c4-393">Beim Hostingstart bereitgestellte Konfiguration</span><span class="sxs-lookup"><span data-stu-id="b66c4-393">Configuration provided by the hosting startup</span></span> <span data-ttu-id="b66c4-394">Es gibt zwei Ansätze zur Verarbeitung der Konfiguration, je nachdem, ob die Konfiguration des Hostingstarts oder die der App Vorrang haben soll:</span><span class="sxs-lookup"><span data-stu-id="b66c4-394">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="b66c4-395">Bereitstellen der Konfiguration für die App mit <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> zum Laden der Konfiguration, nachdem die <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>-Delegaten der App ausgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="b66c4-395">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span>

<span data-ttu-id="b66c4-396">Bei diesem Ansatz hat die Hostingstartkonfiguration Vorrang vor der Konfiguration der App.</span><span class="sxs-lookup"><span data-stu-id="b66c4-396">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span> <span data-ttu-id="b66c4-397">Bereitstellen der Konfiguration für die App mit <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> zum Laden der Konfiguration, bevor die <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>-Delegaten der App ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="b66c4-397">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span>

<span data-ttu-id="b66c4-398">Die Konfigurationswerte der App haben bei diesem Ansatz Vorrang vor denen des Hostingstarts.</span><span class="sxs-lookup"><span data-stu-id="b66c4-398">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span> <span data-ttu-id="b66c4-399">Angeben der Hostingstartassembly</span><span class="sxs-lookup"><span data-stu-id="b66c4-399">Specify the hosting startup assembly</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="b66c4-400">Geben Sie für einen von der Klassenbibliothek oder von der Konsolen-App bereitgestellten Hostingstart den Namen der Hostingstartassembly in der Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` an.</span><span class="sxs-lookup"><span data-stu-id="b66c4-400">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="b66c4-401">Die Umgebungsvariable ist eine durch Semikolons getrennte Liste von Assemblys.</span><span class="sxs-lookup"><span data-stu-id="b66c4-401">The environment variable is a semicolon-delimited list of assemblies.</span></span>

## <a name="activation"></a><span data-ttu-id="b66c4-402">Nur in Hostingstartassemblys wird nach dem `HostingStartup`-Attribut gesucht.</span><span class="sxs-lookup"><span data-stu-id="b66c4-402">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span>

<span data-ttu-id="b66c4-403">Damit die Beispiel-App *HostingStartupApp* die weiter oben beschriebenen Hostingstarts erkennt, wird für die Umgebungsvariable der folgende Wert festgelegt:</span><span class="sxs-lookup"><span data-stu-id="b66c4-403">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

* <span data-ttu-id="b66c4-404">Eine Hostingstartassembly kann auch mithilfe der Hostkonfigurationseinstellung [Hostingstartassemblys](xref:fundamentals/host/web-host#hosting-startup-assemblies) festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="b66c4-404">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span> <span data-ttu-id="b66c4-405">Wenn mehrere Hoststartassemblys vorhanden sind, werden ihre <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>-Methoden in der Reihenfolge der aufgelisteten Assemblys ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="b66c4-405">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span> <span data-ttu-id="b66c4-406">Aktivierung</span><span class="sxs-lookup"><span data-stu-id="b66c4-406">Activation</span></span>
* <span data-ttu-id="b66c4-407">Optionen für die Hostingstartaktivierung:</span><span class="sxs-lookup"><span data-stu-id="b66c4-407">Options for hosting startup activation are:</span></span>
  * <span data-ttu-id="b66c4-408">[Laufzeitspeicher:](#runtime-store) Für die Aktivierung ist kein Kompilierzeitverweis erforderlich.</span><span class="sxs-lookup"><span data-stu-id="b66c4-408">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span>
  * <span data-ttu-id="b66c4-409">Die Beispiel-App legt die Hostingstartassembly und die Abhängigkeitendateien im Ordner *deployment* ab, um die Bereitstellung des Hostingstarts in einer Umgebung mit mehreren Computern zu erleichtern.</span><span class="sxs-lookup"><span data-stu-id="b66c4-409">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span>

### <a name="runtime-store"></a><span data-ttu-id="b66c4-410">Der Ordner *deployment* enthält darüber hinaus auch ein PowerShell-Skript, das im Bereitstellungssystem Umgebungsvariablen erstellt oder ändert, um den Hostingstart zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="b66c4-410">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>

<span data-ttu-id="b66c4-411">Kompilierzeitverweis für die Aktivierung erforderlich</span><span class="sxs-lookup"><span data-stu-id="b66c4-411">Compile-time reference required for activation</span></span> [<span data-ttu-id="b66c4-412">NuGet-Paket</span><span class="sxs-lookup"><span data-stu-id="b66c4-412">NuGet package</span></span>](#nuget-package)

[<span data-ttu-id="b66c4-413">Ordner „Bin“ des Projekts</span><span class="sxs-lookup"><span data-stu-id="b66c4-413">Project bin folder</span></span>](#project-bin-folder)

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="b66c4-414">Laufzeitspeicher</span><span class="sxs-lookup"><span data-stu-id="b66c4-414">Runtime store</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="b66c4-415">Die Hostingstartimplementierung wird im [Laufzeitspeicher](/dotnet/core/deploying/runtime-store) abgelegt.</span><span class="sxs-lookup"><span data-stu-id="b66c4-415">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span>

<span data-ttu-id="b66c4-416">Ein Kompilierzeitverweis auf die Assembly wird von der erweiterten App nicht benötigt.</span><span class="sxs-lookup"><span data-stu-id="b66c4-416">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="b66c4-417">Nach der Erstellung des Hostingstarts wird mithilfe der Manifestprojektdatei und des Befehls [dotnet store](/dotnet/core/tools/dotnet-store) ein Laufzeitspeicher generiert.</span><span class="sxs-lookup"><span data-stu-id="b66c4-417">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span> <span data-ttu-id="b66c4-418">In der Beispiel-App (Projekt *RuntimeStore*) wird der folgende Befehl verwendet:</span><span class="sxs-lookup"><span data-stu-id="b66c4-418">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

* <span data-ttu-id="b66c4-419">Damit die Runtime den Laufzeitspeicher ermitteln kann, wird der Speicherort des Laufzeitspeichers der Umgebungsvariablen `DOTNET_SHARED_STORE` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b66c4-419">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>
* <span data-ttu-id="b66c4-420">**Ändern und Ablegen der Abhängigkeitendatei des Hostingstarts**</span><span class="sxs-lookup"><span data-stu-id="b66c4-420">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="b66c4-421">Um die Erweiterung ohne einen Paketverweis auf die Erweiterung zu aktivieren, geben Sie mit `additionalDeps` zusätzliche Abhängigkeiten zur Laufzeit an.</span><span class="sxs-lookup"><span data-stu-id="b66c4-421">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span>

 1. <span data-ttu-id="b66c4-422">`additionalDeps` ermöglicht Ihnen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="b66c4-422">`additionalDeps` allows you to:</span></span>
 1. <span data-ttu-id="b66c4-423">Erweitern des App-Bibliotheksdiagramms durch Bereitstellen einer Reihe zusätzlicher *.deps.json*-Dateien, die beim Start mit der App-eigenen *.deps.json*-Datei zusammengeführt werden.</span><span class="sxs-lookup"><span data-stu-id="b66c4-423">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>

<span data-ttu-id="b66c4-424">Bereitstellen der Hostingstartassembly, sodass sie ermittelt und geladen werden kann</span><span class="sxs-lookup"><span data-stu-id="b66c4-424">Make the hosting startup assembly discoverable and loadable.</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="b66c4-425">Folgender Ansatz wird zum Generieren der zusätzlichen Abhängigkeitendatei empfohlen:</span><span class="sxs-lookup"><span data-stu-id="b66c4-425">The recommended approach for generating the additional dependencies file is to:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="b66c4-426">Führen Sie `dotnet publish` für die Manifestdatei des Laufzeitspeichers aus, auf die im vorherigen Abschnitt verwiesen wurde.</span><span class="sxs-lookup"><span data-stu-id="b66c4-426">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
* <span data-ttu-id="b66c4-427">Entfernen Sie den Manifestverweis aus den Bibliotheken und dem Abschnitt `runtime` der daraus resultierenden *.deps.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="b66c4-427">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>
* <span data-ttu-id="b66c4-428">Im Beispielprojekt wird die Eigenschaft `store.manifest/1.0.0` aus den Abschnitten `targets` und `libraries` entfernt:</span><span class="sxs-lookup"><span data-stu-id="b66c4-428">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>
* <span data-ttu-id="b66c4-429">Legen Sie die *.deps.json*-Datei an folgendem Speicherort ab:</span><span class="sxs-lookup"><span data-stu-id="b66c4-429">Place the *.deps.json* file into the following location:</span></span>

<span data-ttu-id="b66c4-430">`{ADDITIONAL DEPENDENCIES PATH}`: Der Speicherort wurde zur Umgebungsvariable `DOTNET_ADDITIONAL_DEPS` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b66c4-430">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="b66c4-431">`{SHARED FRAMEWORK NAME}`: Dies ist das freigegebene Framework, das für diese zusätzliche Abhängigkeitsdatei erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="b66c4-431">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>

<span data-ttu-id="b66c4-432">`{SHARED FRAMEWORK VERSION}`: Dies ist die Mindestversion des freigegebenen Frameworks.</span><span class="sxs-lookup"><span data-stu-id="b66c4-432">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>

<span data-ttu-id="b66c4-433">`{ENHANCEMENT ASSEMBLY NAME}`: Dies ist der Name der Erweiterungsassembly.</span><span class="sxs-lookup"><span data-stu-id="b66c4-433">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="b66c4-434">In der Beispiel-App (Projekt *RuntimeStore*) wird die zusätzliche Abhängigkeitendatei an folgendem Speicherort abgelegt:</span><span class="sxs-lookup"><span data-stu-id="b66c4-434">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

<span data-ttu-id="b66c4-435">Damit die Runtime den Speicherort des Laufzeitspeichers ermitteln kann, wir die zusätzliche Abhängigkeitendatei der Umgebungsvariablen `DOTNET_ADDITIONAL_DEPS` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b66c4-435">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

* <span data-ttu-id="b66c4-436">In der Beispiel-App (Projekt *RuntimeStore*) wird das Erstellen des Laufzeitspeichers und das Generieren der zusätzlichen Abhängigkeitendatei durch ein [PowerShell](/powershell/scripting/powershell-scripting)-Skript durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="b66c4-436">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>
* <span data-ttu-id="b66c4-437">Beispiele zum Festlegen von Umgebungsvariablen für verschiedene Betriebssysteme finden Sie unter [Use multiple environments (Verwenden mehrerer Umgebungen)](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="b66c4-437">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>
* <span data-ttu-id="b66c4-438">**Bereitstellung**</span><span class="sxs-lookup"><span data-stu-id="b66c4-438">**Deployment**</span></span> <span data-ttu-id="b66c4-439">Um die Bereitstellung eines Hostingstarts in einer Umgebung mit mehreren Computern zu erleichtern, erstellt die Beispiel-App in der veröffentlichten Ausgabe einen *deployment*-Ordner, der Folgendes enthält:</span><span class="sxs-lookup"><span data-stu-id="b66c4-439">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

### <a name="nuget-package"></a><span data-ttu-id="b66c4-440">Der Laufzeitspeicher des Hostingstarts.</span><span class="sxs-lookup"><span data-stu-id="b66c4-440">The hosting startup runtime store.</span></span>

<span data-ttu-id="b66c4-441">Die Abhängigkeitendatei des Hostingstarts.</span><span class="sxs-lookup"><span data-stu-id="b66c4-441">The hosting startup dependencies file.</span></span> <span data-ttu-id="b66c4-442">Ein PowerShell-Skript, das `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` und `DOTNET_ADDITIONAL_DEPS` erstellt oder ändert, um die Aktivierung des Hostingstarts zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="b66c4-442">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="b66c4-443">Führen Sie das Skript über eine PowerShell-Administratoreingabeaufforderung auf dem Bereitstellungssystem aus.</span><span class="sxs-lookup"><span data-stu-id="b66c4-443">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

* <span data-ttu-id="b66c4-444">NuGet-Paket</span><span class="sxs-lookup"><span data-stu-id="b66c4-444">NuGet package</span></span> <span data-ttu-id="b66c4-445">In einem NuGet-Paket kann eine Hostingstarterweiterung bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="b66c4-445">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="b66c4-446">Das Paket enthält ein `HostingStartup`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="b66c4-446">The package has a `HostingStartup` attribute.</span></span>
* <span data-ttu-id="b66c4-447">Die vom Paket bereitgestellten Hostingstarttypen werden für die App mit einem der folgenden Verfahren verfügbar gemacht:</span><span class="sxs-lookup"><span data-stu-id="b66c4-447">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

<span data-ttu-id="b66c4-448">Die Projektdatei der erweiterten App erstellt einen Paketverweis für den Hostingstart in der Projektdatei der App (Kompilierzeitverweis).</span><span class="sxs-lookup"><span data-stu-id="b66c4-448">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span>

* <span data-ttu-id="b66c4-449">Wenn ein Kompilierzeitverweis eingerichtet ist, werden die Hostingstartassembly und alle dazu gehörenden Abhängigkeiten in die Abhängigkeitendatei der App ( *.deps.json*) eingebunden.</span><span class="sxs-lookup"><span data-stu-id="b66c4-449">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span>
* <span data-ttu-id="b66c4-450">Dieses Verfahren gilt für ein in [nuget.org](https://www.nuget.org/) veröffentlichtes Hostingstartassembly-Paket.</span><span class="sxs-lookup"><span data-stu-id="b66c4-450">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="b66c4-451">Die Abhängigkeitendatei des Hostingstarts wird für die erweiterte App wie im Abschnitt [Laufzeitspeicher](#runtime-store) beschrieben (ohne Kompilierzeitverweis) verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="b66c4-451">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

### <a name="project-bin-folder"></a><span data-ttu-id="b66c4-452">Weitere Informationen zu NuGet-Paketen und den Laufzeitspeicher finden Sie in den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="b66c4-452">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

[<span data-ttu-id="b66c4-453">So erstellen Sie ein NuGet-Paket mit plattformübergreifenden Tools</span><span class="sxs-lookup"><span data-stu-id="b66c4-453">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages) [<span data-ttu-id="b66c4-454">Veröffentlichen von Paketen</span><span class="sxs-lookup"><span data-stu-id="b66c4-454">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)

* [<span data-ttu-id="b66c4-455">Laufzeitpaketspeicher</span><span class="sxs-lookup"><span data-stu-id="b66c4-455">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store) <span data-ttu-id="b66c4-456">Ordner „Bin“ des Projekts</span><span class="sxs-lookup"><span data-stu-id="b66c4-456">Project bin folder</span></span> <span data-ttu-id="b66c4-457">Eine Hostingstarterweiterung kann durch eine mittels *bin* bereitgestellte Assembly in der erweiterten App bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="b66c4-457">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span>
  * <span data-ttu-id="b66c4-458">Die von der Assembly bereitgestellten Hostingstarttypen werden für die App mit einem der folgenden Verfahren verfügbar gemacht:</span><span class="sxs-lookup"><span data-stu-id="b66c4-458">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>
  * <span data-ttu-id="b66c4-459">Die Projektdatei der erweiterten App enthält einen Assemblyverweis auf den Hostingstart (Kompilierzeitverweis).</span><span class="sxs-lookup"><span data-stu-id="b66c4-459">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span>
* <span data-ttu-id="b66c4-460">Wenn ein Kompilierzeitverweis eingerichtet ist, werden die Hostingstartassembly und alle dazu gehörenden Abhängigkeiten in die Abhängigkeitendatei der App ( *.deps.json*) eingebunden.</span><span class="sxs-lookup"><span data-stu-id="b66c4-460">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span>
* <span data-ttu-id="b66c4-461">Dieser Ansatz gilt, wenn das Bereitstellungsszenario erfordert, dass ein Kompilierzeitverweis auf die Assembly des Hostingstarts (*DLL*-Datei) erstellt und die Assembly in eine der beiden Dateien verschoben wird:</span><span class="sxs-lookup"><span data-stu-id="b66c4-461">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="b66c4-462">Das verbrauchende Projekt.</span><span class="sxs-lookup"><span data-stu-id="b66c4-462">The consuming project.</span></span>
  * <span data-ttu-id="b66c4-463">Einen Speicherort, auf den das verbrauchende Projekt zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="b66c4-463">A location accessible by the consuming project.</span></span> <span data-ttu-id="b66c4-464">Die Abhängigkeitendatei des Hostingstarts wird für die erweiterte App wie im Abschnitt [Laufzeitspeicher](#runtime-store) beschrieben (ohne Kompilierzeitverweis) verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="b66c4-464">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

## <a name="sample-code"></a><span data-ttu-id="b66c4-465">Wenn das Ziel .NET Framework ist, kann die Assembly in den Standardladekontext geladen werden, was bei .NET Framework bedeutet, dass sich die Assembly an einem der folgenden Speicherorte befindet:</span><span class="sxs-lookup"><span data-stu-id="b66c4-465">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>

<span data-ttu-id="b66c4-466">Anwendungsbasispfad: Dabei handelt es sich um den Ordner *bin*, in dem die ausführbare Datei ( *.exe*) der App gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="b66c4-466">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>

* <span data-ttu-id="b66c4-467">Globaler Assemblycache (GAC): Der GAC speichert Assemblys, die mehrere .NET Framework-Apps gemeinsam nutzen.</span><span class="sxs-lookup"><span data-stu-id="b66c4-467">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span>
  * <span data-ttu-id="b66c4-468">Weitere Informationen finden Sie unter [Vorgehensweise: Installieren einer Assembly in den globalen Assemblycache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in der .NET Framework-Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="b66c4-468">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>
  * <span data-ttu-id="b66c4-469">Beispielcode</span><span class="sxs-lookup"><span data-stu-id="b66c4-469">Sample code</span></span>
* <span data-ttu-id="b66c4-470">Der [Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Herunterladen](xref:index#how-to-download-a-sample)) zeigt Szenarios für die Hostingstartimplementierung:</span><span class="sxs-lookup"><span data-stu-id="b66c4-470">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span> <span data-ttu-id="b66c4-471">Zwei Hostingstartassemblys (Klassenbibliotheken) legen zwei Schlüssel-Wert-Paare der Konfiguration im Arbeitsspeicher fest:</span><span class="sxs-lookup"><span data-stu-id="b66c4-471">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="b66c4-472">NuGet-Paket (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="b66c4-472">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="b66c4-473">Klassenbibliothek (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="b66c4-473">Class library (*HostingStartupLibrary*)</span></span>
  * <span data-ttu-id="b66c4-474">Ein Hostingstart wird über eine im Laufzeitspeicher bereitgestellte Assembly (*StartupDiagnostics*) aktiviert.</span><span class="sxs-lookup"><span data-stu-id="b66c4-474">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span>
  * <span data-ttu-id="b66c4-475">Die Assembly fügt der App zum Start zwei Middlewares hinzu, die Diagnoseinformationen zu folgenden Komponenten bereitstellen:</span><span class="sxs-lookup"><span data-stu-id="b66c4-475">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="b66c4-476">Registrierte Dienste</span><span class="sxs-lookup"><span data-stu-id="b66c4-476">Registered services</span></span>

<span data-ttu-id="b66c4-477">Adresse (Schema, Host, Basispfad, Pfad, Abfragezeichenfolge)</span><span class="sxs-lookup"><span data-stu-id="b66c4-477">Address (scheme, host, path base, path, query string)</span></span>

<span data-ttu-id="b66c4-478">Verbindung (Remote-IP, Remoteport, lokale IP, lokaler Port, Clientzertifikat)</span><span class="sxs-lookup"><span data-stu-id="b66c4-478">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>

1. <span data-ttu-id="b66c4-479">Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="b66c4-479">Request headers</span></span>
1. <span data-ttu-id="b66c4-480">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="b66c4-480">Environment variables</span></span>
1. <span data-ttu-id="b66c4-481">So führen Sie das Beispiel aus:</span><span class="sxs-lookup"><span data-stu-id="b66c4-481">To run the sample:</span></span> <span data-ttu-id="b66c4-482">**Aktivierung über ein NuGet-Paket**</span><span class="sxs-lookup"><span data-stu-id="b66c4-482">**Activation from a NuGet package**</span></span> <span data-ttu-id="b66c4-483">Kompilieren Sie das Paket *HostingStartupPackage* mit dem Befehl [dotnet pack](/dotnet/core/tools/dotnet-pack).</span><span class="sxs-lookup"><span data-stu-id="b66c4-483">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span> <span data-ttu-id="b66c4-484">Fügen Sie den Assemblynamen des Pakets *HostingStartupPackage* zur Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` hinzu.</span><span class="sxs-lookup"><span data-stu-id="b66c4-484">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="b66c4-485">Kompilieren Sie die App, und führen Sie diese aus.</span><span class="sxs-lookup"><span data-stu-id="b66c4-485">Compile and run the app.</span></span>

<span data-ttu-id="b66c4-486">In der erweiterten App ist ein Paketverweis vorhanden (Kompilierzeitverweis).</span><span class="sxs-lookup"><span data-stu-id="b66c4-486">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="b66c4-487">Eine `<PropertyGroup>` in der Projektdatei der App gibt die Ausgabe des Paketprojekts ( *../HostingStartupPackage/bin/Debug*) als Paketquelle an.</span><span class="sxs-lookup"><span data-stu-id="b66c4-487">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="b66c4-488">Dadurch kann die Anwendung das Paket verwenden, ohne das Paket in [nuget.org](https://www.nuget.org/) hochzuladen. Weitere Informationen finden Sie in den Hinweisen in der Projektdatei von HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="b66c4-488">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

1. <span data-ttu-id="b66c4-489">Die von der Indexseite gerenderten Schlüsselwerte der Dienstkonfiguration entsprechen den durch die `ServiceKeyInjection.Configure`-Methode des Pakets festgelegten Werten.</span><span class="sxs-lookup"><span data-stu-id="b66c4-489">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>
1. <span data-ttu-id="b66c4-490">Wenn Sie am Projekt *HostingStartupPackage* Änderungen vornehmen und das Projekt erneut kompilieren, bereinigen Sie die lokalen NuGet-Paketcaches, um sicherzustellen, dass die *HostingStartupApp* das aktualisierte Paket erhält und kein veraltetes Paket aus dem lokalen Cache.</span><span class="sxs-lookup"><span data-stu-id="b66c4-490">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span>
1. <span data-ttu-id="b66c4-491">Um die lokalen NuGet-Caches zu bereinigen, führen Sie den folgenden [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals)-Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="b66c4-491">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>
1. <span data-ttu-id="b66c4-492">**Aktivierung über eine Klassenbibliothek**</span><span class="sxs-lookup"><span data-stu-id="b66c4-492">**Activation from a class library**</span></span> <span data-ttu-id="b66c4-493">Kompilieren Sie die Klassenbibliothek *HostingStartupLibrary* mit dem Befehl [dotnet build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="b66c4-493">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span> <span data-ttu-id="b66c4-494">Fügen Sie der Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` den Assemblynamen *HostingStartupLibrary* der Klassenbibliothek hinzu.</span><span class="sxs-lookup"><span data-stu-id="b66c4-494">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="b66c4-495">Stellen Sie die Assembly der Klassenbibliothek mittels *bin* für die App bereit, indem Sie die Datei *HostingStartupLibrary.dll* aus der kompilierten Ausgabe der Klassenbibliothek in den Ordner *bin/Debug* der App kopieren.</span><span class="sxs-lookup"><span data-stu-id="b66c4-495">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>

<span data-ttu-id="b66c4-496">Kompilieren Sie die App, und führen Sie diese aus.</span><span class="sxs-lookup"><span data-stu-id="b66c4-496">Compile and run the app.</span></span>

1. <span data-ttu-id="b66c4-497">Eine `<ItemGroup>` in der Projektdatei der App verweist auf die Assembly der Klassenbibliothek ( *.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (Kompilierzeitverweis).</span><span class="sxs-lookup"><span data-stu-id="b66c4-497">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="b66c4-498">Weitere Informationen finden Sie in den Hinweisen in der Projektdatei von HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="b66c4-498">For more information, see the notes in the HostingStartupApp's project file.</span></span> <span data-ttu-id="b66c4-499">Die von der Indexseite gerenderten Schlüsselwerte der Dienstkonfiguration entsprechen den durch die `ServiceKeyInjection.Configure`-Methode der Klassenbibliothek festgelegten Werten.</span><span class="sxs-lookup"><span data-stu-id="b66c4-499">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>
1. <span data-ttu-id="b66c4-500">**Aktivierung über eine mittels Laufzeitspeicher bereitgestellten Assembly**</span><span class="sxs-lookup"><span data-stu-id="b66c4-500">**Activation from a runtime store-deployed assembly**</span></span> <span data-ttu-id="b66c4-501">Im Projekt *StartupDiagnostics* wird [PowerShell](/powershell/scripting/powershell-scripting) verwendet, um die Datei *StartupDiagnostics.deps.json* zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="b66c4-501">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span>
   * <span data-ttu-id="b66c4-502">PowerShell ist standardmäßig auf Windows-Betriebssystemen ab Windows 7 SP1 und Windows Server 2008 R2 SP1 installiert.</span><span class="sxs-lookup"><span data-stu-id="b66c4-502">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span>
   * <span data-ttu-id="b66c4-503">Im Artikel [Installieren verschiedener Versionen von PowerShell](/powershell/scripting/install/installing-powershell) erfahren Sie, wie Sie PowerShell auf anderen Plattformen erwerben können.</span><span class="sxs-lookup"><span data-stu-id="b66c4-503">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span> <span data-ttu-id="b66c4-504">Führen Sie das Skript *build.ps1* im Ordner *RuntimeStore* aus.</span><span class="sxs-lookup"><span data-stu-id="b66c4-504">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="b66c4-505">Das Skript:</span><span class="sxs-lookup"><span data-stu-id="b66c4-505">The script:</span></span> <span data-ttu-id="b66c4-506">Generiert das `StartupDiagnostics`-Paket im Ordner *obj\packages* .</span><span class="sxs-lookup"><span data-stu-id="b66c4-506">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span> <span data-ttu-id="b66c4-507">Generiert den Laufzeitspeicher für `StartupDiagnostics` im Ordner *store*.</span><span class="sxs-lookup"><span data-stu-id="b66c4-507">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span>
   * <span data-ttu-id="b66c4-508">Der `dotnet store`-Befehl im Skript verwendet den [Laufzeitbezeichner (RID)](/dotnet/core/rid-catalog) `win7-x64` für einen für Windows bereitgestellten Hostingstart.</span><span class="sxs-lookup"><span data-stu-id="b66c4-508">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="b66c4-509">Wenn der Hostingstart für eine andere Laufzeit bereitgestellt wird, muss in Zeile 37 die entsprechende RID eingegeben werden.</span><span class="sxs-lookup"><span data-stu-id="b66c4-509">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="b66c4-510">Der Runtimespeicher für `StartupDiagnostics` würde später in den Runtimespeicher des Benutzers oder Systems auf dem Computer verschoben, auf dem die Assembly verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="b66c4-510">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span>
   * <span data-ttu-id="b66c4-511">Der Installationsspeicherort für den Benutzerruntimespeicher für die `StartupDiagnostics`-Assembly ist *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="b66c4-511">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span></span>
1. <span data-ttu-id="b66c4-512">Generiert die `additionalDeps` für `StartupDiagnostics` im Ordner *additionalDeps* .</span><span class="sxs-lookup"><span data-stu-id="b66c4-512">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="b66c4-513">Die zusätzlichen Abhängigkeiten würden später in die zusätzlichen Abhängigkeiten des Benutzers oder Systems verschoben.</span><span class="sxs-lookup"><span data-stu-id="b66c4-513">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span>
   * <span data-ttu-id="b66c4-514">Der Installationsspeicherort der zusätzlichen Abhängigkeiten des Benutzers `StartupDiagnostics` ist *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="b66c4-514">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="b66c4-515">Legt die Datei *deploy.ps1* im Ordner *deployment* ab.</span><span class="sxs-lookup"><span data-stu-id="b66c4-515">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
   * <span data-ttu-id="b66c4-516">Führen Sie das Skript *deploy.ps1* im Ordner *deployment* aus.</span><span class="sxs-lookup"><span data-stu-id="b66c4-516">Run the *deploy.ps1* script in the *deployment* folder.</span></span>
1. <span data-ttu-id="b66c4-517">Das Skript fügt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="b66c4-517">The script appends:</span></span>
1. <span data-ttu-id="b66c4-518">`StartupDiagnostics` an die Umgebungsvariable `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="b66c4-518">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="b66c4-519">Der Pfad der Hostingstartabhängigkeiten (im Ordner *deployment* des RuntimeStore-Projekts) zur `DOTNET_ADDITIONAL_DEPS`-Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="b66c4-519">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

::: moniker-end

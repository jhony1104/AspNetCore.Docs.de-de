---
title: Verwenden von Hostingstartassemblys in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie eine ASP.NET Core-App aus einer externen Assembly mithilfe einer Implementierung von IHostingStartup erweitern.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/14/2019
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: cffad201c84414ee4788877d80d3619a9013ae99
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410494"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="9e51b-103">Verwenden von Hostingstartassemblys in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9e51b-103">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="9e51b-104">Von [Luke Latham](https://github.com/guardrex) und [Pavel Krymets](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="9e51b-104">By [Luke Latham](https://github.com/guardrex) and [Pavel Krymets](https://github.com/pakrym)</span></span>

<span data-ttu-id="9e51b-105">Mit einer [IHostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup)-Implementierung (Hostingstart) werden Verbesserungen an einer App beim Start von einer externen Assemblys aus vorgenommen.</span><span class="sxs-lookup"><span data-stu-id="9e51b-105">An [IHostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup) (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="9e51b-106">Eine externe Bibliothek kann beispielsweise eine Hostingstartimplementierung verwenden, um zusätzliche Konfigurationsanbieter oder -dienste für eine App bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="9e51b-106">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span> <span data-ttu-id="9e51b-107">`IHostingStartup` *ist in ASP.NET Core 2.0 und höher verfügbar*.</span><span class="sxs-lookup"><span data-stu-id="9e51b-107">`IHostingStartup` *is available in ASP.NET Core 2.0 or later.*</span></span>

<span data-ttu-id="9e51b-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9e51b-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="9e51b-109">Das Attribut HostingStartup</span><span class="sxs-lookup"><span data-stu-id="9e51b-109">HostingStartup attribute</span></span>

<span data-ttu-id="9e51b-110">Ein [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute)-Attribut gibt an, ob eine Hostingstartassembly vorhanden ist, die zur Laufzeit aktiviert werden kann.</span><span class="sxs-lookup"><span data-stu-id="9e51b-110">A [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="9e51b-111">Bei der Einstiegsassembly oder bei der Assembly, die die Klasse `Startup` enthält, wird automatisch geprüft, ob sie das Attribut `HostingStartup` enthält.</span><span class="sxs-lookup"><span data-stu-id="9e51b-111">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="9e51b-112">Die Liste der Assemblys für die Suche nach `HostingStartup`-Attributen wird zur Laufzeit aus der Konfiguration in [WebHostDefaults.HostingStartupAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupassemblieskey) geladen.</span><span class="sxs-lookup"><span data-stu-id="9e51b-112">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupassemblieskey).</span></span> <span data-ttu-id="9e51b-113">Die Liste der Assemblys, die aus der Ermittlung ausgeschlossen werden sollen, wird aus [WebHostDefaults.HostingStartupExcludeAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupexcludeassemblieskey) geladen.</span><span class="sxs-lookup"><span data-stu-id="9e51b-113">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupexcludeassemblieskey).</span></span> <span data-ttu-id="9e51b-114">Weitere Informationen finden Sie unter [Webhost: Hostingstartassemblys](xref:fundamentals/host/web-host#hosting-startup-assemblies)[Webhost: Auszuschließende Hostingstartassemblys](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="9e51b-114">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="9e51b-115">Im folgenden Beispiel lautet der Namespace der Hostingstartassembly `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="9e51b-115">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="9e51b-116">`StartupEnhancementHostingStartup` ist die Klasse mit dem Hostingstartcode:</span><span class="sxs-lookup"><span data-stu-id="9e51b-116">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="9e51b-117">Das Attribut `HostingStartup` befindet sich in der Regel in der `IHostingStartup`-Implementierungsklassendatei der Hostingstartassembly.</span><span class="sxs-lookup"><span data-stu-id="9e51b-117">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="9e51b-118">Ermitteln geladener Hostingstartassemblys</span><span class="sxs-lookup"><span data-stu-id="9e51b-118">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="9e51b-119">Zum Ermitteln von geladenen Hostingstartassemblys aktivieren Sie die Protokollierung und überprüfen die Protokolle der App.</span><span class="sxs-lookup"><span data-stu-id="9e51b-119">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="9e51b-120">Fehler, die beim Laden von Assemblys auftreten, werden protokolliert.</span><span class="sxs-lookup"><span data-stu-id="9e51b-120">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="9e51b-121">Geladene Hostingstartassemblys werden auf der Debugebene protokolliert. Außerdem werden alle Fehler protokolliert.</span><span class="sxs-lookup"><span data-stu-id="9e51b-121">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="9e51b-122">Deaktivieren des automatischen Ladens von Hostingstartassemblys</span><span class="sxs-lookup"><span data-stu-id="9e51b-122">Disable automatic loading of hosting startup assemblies</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="9e51b-123">Um das automatische Laden von Hostingstartassemblys zu deaktivieren, verwenden Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="9e51b-123">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="9e51b-124">Um zu verhindern, dass alle Hostingstartassemblys geladen werden, legen Sie eine der folgenden Einstellungen auf `true` oder `1` fest:</span><span class="sxs-lookup"><span data-stu-id="9e51b-124">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="9e51b-125">Hostkonfigurationseinstellung [Verhindern des Hostingstarts](xref:fundamentals/host/web-host#prevent-hosting-startup).</span><span class="sxs-lookup"><span data-stu-id="9e51b-125">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="9e51b-126">Die Umgebungsvariable `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="9e51b-126">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="9e51b-127">Um zu verhindern, dass bestimmte Hostingstartassemblys geladen werden, legen Sie eine der folgenden Einstellungen auf eine durch Semikolons getrennte Zeichenfolge mit Hostingstartassemblys fest, die beim Starten ausgeschlossen werden sollen:</span><span class="sxs-lookup"><span data-stu-id="9e51b-127">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="9e51b-128">Hostkonfigurationseinstellung [Hostingstartausschlussassemblys](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="9e51b-128">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="9e51b-129">Die Umgebungsvariable `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="9e51b-129">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="9e51b-130">Um das automatische Laden von Hostingstartassemblys zu deaktivieren, legen Sie eine der folgenden Einstellungen auf `true` oder `1` fest:</span><span class="sxs-lookup"><span data-stu-id="9e51b-130">To disable automatic loading of hosting startup assemblies, set one of the following to `true` or `1`:</span></span>

* <span data-ttu-id="9e51b-131">Hostkonfigurationseinstellung [Verhindern des Hostingstarts](xref:fundamentals/host/web-host#prevent-hosting-startup).</span><span class="sxs-lookup"><span data-stu-id="9e51b-131">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
* <span data-ttu-id="9e51b-132">Die Umgebungsvariable `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="9e51b-132">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

::: moniker-end

<span data-ttu-id="9e51b-133">Wenn sowohl die Konfigurationseinstellung für den Host als auch die Umgebungsvariable festgelegt werden, wird das Verhalten durch die Hosteinstellung gesteuert.</span><span class="sxs-lookup"><span data-stu-id="9e51b-133">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="9e51b-134">Durch das Deaktivieren von Hostingstartassemblys mithilfe der Hosteinstellung oder der Umgebungsvariable wird die Assembly global deaktiviert. Einige Eigenschaften einer App können ebenfalls deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="9e51b-134">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="9e51b-135">Projekt</span><span class="sxs-lookup"><span data-stu-id="9e51b-135">Project</span></span>

<span data-ttu-id="9e51b-136">Erstellen Sie einen Hostingstart mit einem der folgenden Projekttypen:</span><span class="sxs-lookup"><span data-stu-id="9e51b-136">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="9e51b-137">Klassenbibliothek</span><span class="sxs-lookup"><span data-stu-id="9e51b-137">Class library</span></span>](#class-library)
* [<span data-ttu-id="9e51b-138">Konsolen-App ohne Einstiegspunkt</span><span class="sxs-lookup"><span data-stu-id="9e51b-138">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="9e51b-139">Klassenbibliothek</span><span class="sxs-lookup"><span data-stu-id="9e51b-139">Class library</span></span>

<span data-ttu-id="9e51b-140">In einer Klassenbibliothek kann eine Hostingstarterweiterung bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="9e51b-140">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="9e51b-141">Die Bibliothek enthält ein `HostingStartup`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="9e51b-141">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="9e51b-142">Der [Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) enthält die Razor Pages-App *HostingStartupApp* und die Klassenbibliothek *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="9e51b-142">The [sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="9e51b-143">Die Klassenbibliothek:</span><span class="sxs-lookup"><span data-stu-id="9e51b-143">The class library:</span></span>

* <span data-ttu-id="9e51b-144">Enthält die Hostingstartklasse `ServiceKeyInjection`, die `IHostingStartup` implementiert.</span><span class="sxs-lookup"><span data-stu-id="9e51b-144">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="9e51b-145">`ServiceKeyInjection` fügt mithilfe des Anbieters der Konfiguration im Arbeitsspeicher ([AddInMemoryCollection](/dotnet/api/microsoft.extensions.configuration.memoryconfigurationbuilderextensions.addinmemorycollection)) der App-Konfiguration zwei Dienstzeichenfolgen hinzu.</span><span class="sxs-lookup"><span data-stu-id="9e51b-145">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](/dotnet/api/microsoft.extensions.configuration.memoryconfigurationbuilderextensions.addinmemorycollection)).</span></span>
* <span data-ttu-id="9e51b-146">Enthält ein `HostingStartup`-Attribut, das den Namespace und die Klasse des Hostingstarts angibt.</span><span class="sxs-lookup"><span data-stu-id="9e51b-146">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="9e51b-147">Die [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure)-Methode der Klasse `ServiceKeyInjection` verwendet [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder), um einer App Erweiterungen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="9e51b-147">The `ServiceKeyInjection` class's [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) method uses an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) to add enhancements to an app.</span></span>

<span data-ttu-id="9e51b-148">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="9e51b-148">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="9e51b-149">Die Indexseite der App liest und rendert die Konfigurationswerte für die beiden Schlüssel, die von der Hostingstartassembly der Klassenbibliothek festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="9e51b-149">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="9e51b-150">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="9e51b-150">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="9e51b-151">Der [Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) enthält darüber hinaus auch ein NuGet-Paketprojekt, das den separaten Hostingstart *HostingStartupPackage* bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="9e51b-151">The [sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="9e51b-152">Das Paket weist dieselben Merkmale wie die bereits beschriebene Klassenbibliothek auf.</span><span class="sxs-lookup"><span data-stu-id="9e51b-152">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="9e51b-153">Das Paket:</span><span class="sxs-lookup"><span data-stu-id="9e51b-153">The package:</span></span>

* <span data-ttu-id="9e51b-154">Enthält die Hostingstartklasse `ServiceKeyInjection`, die `IHostingStartup` implementiert.</span><span class="sxs-lookup"><span data-stu-id="9e51b-154">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="9e51b-155">`ServiceKeyInjection` fügt der App-Konfiguration zwei Dienstzeichenfolgen hinzu.</span><span class="sxs-lookup"><span data-stu-id="9e51b-155">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="9e51b-156">Enthält ein `HostingStartup`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="9e51b-156">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="9e51b-157">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="9e51b-157">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="9e51b-158">Die Indexseite der App liest und rendert die Konfigurationswerte für die beiden Schlüssel, die von der Hostingstartassembly der Bibliothek festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="9e51b-158">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="9e51b-159">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="9e51b-159">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="9e51b-160">Konsolen-App ohne Einstiegspunkt</span><span class="sxs-lookup"><span data-stu-id="9e51b-160">Console app without an entry point</span></span>

<span data-ttu-id="9e51b-161">*Dieses Verfahren ist nur für .NET Core-Apps, nicht jedoch für .NET Framework verfügbar.*</span><span class="sxs-lookup"><span data-stu-id="9e51b-161">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="9e51b-162">Eine dynamische Hostingstarterweiterung, die zur Aktivierung keinen Kompilierungszeitverweis erfordert, kann in einer Konsolen-App ohne Einstiegspunkt bereitgestellt werden, die ein `HostingStartup`-Attribut enthält.</span><span class="sxs-lookup"><span data-stu-id="9e51b-162">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="9e51b-163">Durch die Veröffentlichung der Konsolen-App wird eine Hostingstartassembly erstellt, die über den Laufzeitspeicher genutzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="9e51b-163">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="9e51b-164">In diesem Prozess wird aus folgenden Gründen eine Konsolen-App ohne Einstiegspunkt verwendet:</span><span class="sxs-lookup"><span data-stu-id="9e51b-164">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="9e51b-165">Eine Abhängigkeitendatei ist erforderlich, um den Hostingstart in der Hostingstartassembly zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="9e51b-165">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="9e51b-166">Eine Abhängigkeitendatei ist eine ausführbare App-Ressource, die durch das Veröffentlichen einer App, nicht einer Bibliothek, erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="9e51b-166">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="9e51b-167">Eine Bibliothek kann dem [Laufzeitpaketspeicher](/dotnet/core/deploying/runtime-store), der ein ausführbares Projekt benötigt, das die freigegebene Laufzeit als Ziel verwendet, nicht direkt hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="9e51b-167">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="9e51b-168">Bei der Erstellung eines dynamischen Hostingstarts geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="9e51b-168">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="9e51b-169">Eine Hostingstartassembly wird über die Konsolen-App ohne Einstiegspunkt erstellt, für die Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="9e51b-169">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="9e51b-170">Sie enthält eine Klasse mit der `IHostingStartup`-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="9e51b-170">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="9e51b-171">Sie enthält ein [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute)-Attribut zum Identifizieren der `IHostingStartup`-Implementierungsklasse.</span><span class="sxs-lookup"><span data-stu-id="9e51b-171">Includes a [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="9e51b-172">Die Konsolen-App wird veröffentlicht, um die Abhängigkeiten des Hostingstarts abzurufen.</span><span class="sxs-lookup"><span data-stu-id="9e51b-172">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="9e51b-173">Die Veröffentlichung der Konsolen-App hat unter anderem zur Folge, dass nicht verwendete Abhängigkeiten aus der Abhängigkeitendatei entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="9e51b-173">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="9e51b-174">Die Abhängigkeitendatei wird so geändert, dass der Laufzeitspeicherort der Hostingstartassembly festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="9e51b-174">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="9e51b-175">Die Hostingstartassembly und die dazu gehörende Abhängigkeitendatei werden im Laufzeitpaketspeicher abgelegt.</span><span class="sxs-lookup"><span data-stu-id="9e51b-175">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="9e51b-176">Damit die Hostingstartassembly und die entsprechende Abhängigkeitendatei erkannt werden, werden sie in einem Paar von Umgebungsvariablen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="9e51b-176">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="9e51b-177">Die Konsolen-App verweist auf das Paket [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):</span><span class="sxs-lookup"><span data-stu-id="9e51b-177">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="9e51b-178">Ein [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute)-Attribut identifiziert eine Klasse bei der Erstellung von [IWebHost](/dotnet/api/microsoft.aspnetcore.hosting.iwebhost) als eine Implementierung von `IHostingStartup` zum Laden und Ausführen.</span><span class="sxs-lookup"><span data-stu-id="9e51b-178">A [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the [IWebHost](/dotnet/api/microsoft.aspnetcore.hosting.iwebhost).</span></span> <span data-ttu-id="9e51b-179">Im folgenden Beispiel ist der Namespace `StartupEnhancement` und die Klasse ist `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="9e51b-179">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="9e51b-180">Eine Klasse implementiert `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="9e51b-180">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="9e51b-181">Die [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure)-Methode der Klasse verwendet [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder), um einer App Erweiterungen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="9e51b-181">The class's [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) method uses an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) to add enhancements to an app.</span></span> <span data-ttu-id="9e51b-182">`IHostingStartup.Configure` wird in der Hoststartassembly von der Runtime vor `Startup.Configure` im Benutzercode aufgerufen. Dadurch kann Benutzercode jede Konfiguration der Hoststartassembly überschreiben.</span><span class="sxs-lookup"><span data-stu-id="9e51b-182">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="9e51b-183">Die Datei für die Abhängigkeiten (*\*.deps.json*) legt beim Erstellen eine `IHostingStartup`-Projekts den `runtime`-Speicherort der Assembly auf den *bin*-Ordner fest:</span><span class="sxs-lookup"><span data-stu-id="9e51b-183">When building an `IHostingStartup` project, the dependencies file (*\*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="9e51b-184">Nur ein Teil der Datei wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="9e51b-184">Only part of the file is shown.</span></span> <span data-ttu-id="9e51b-185">`StartupEnhancement` ist der Name der Assembly im Beispiel.</span><span class="sxs-lookup"><span data-stu-id="9e51b-185">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="9e51b-186">Beim Hostingstart bereitgestellte Konfiguration</span><span class="sxs-lookup"><span data-stu-id="9e51b-186">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="9e51b-187">Es gibt zwei Ansätze zur Verarbeitung der Konfiguration, je nachdem, ob die Konfiguration des Hostingstarts oder die der App Vorrang haben soll:</span><span class="sxs-lookup"><span data-stu-id="9e51b-187">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="9e51b-188">Bereitstellen der Konfiguration für die App mit <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> zum Laden der Konfiguration, nachdem die <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>-Delegaten der App ausgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="9e51b-188">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="9e51b-189">Bei diesem Ansatz hat die Hostingstartkonfiguration Vorrang vor der Konfiguration der App.</span><span class="sxs-lookup"><span data-stu-id="9e51b-189">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="9e51b-190">Bereitstellen der Konfiguration für die App mit <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> zum Laden der Konfiguration, bevor die <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>-Delegaten der App ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="9e51b-190">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="9e51b-191">Die Konfigurationswerte der App haben bei diesem Ansatz Vorrang vor denen des Hostingstarts.</span><span class="sxs-lookup"><span data-stu-id="9e51b-191">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

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
                    "From IHostingStartup: Higher priority than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="9e51b-192">Angeben der Hostingstartassembly</span><span class="sxs-lookup"><span data-stu-id="9e51b-192">Specify the hosting startup assembly</span></span>

<span data-ttu-id="9e51b-193">Geben Sie für einen von der Klassenbibliothek oder von der Konsolen-App bereitgestellten Hostingstart den Namen der Hostingstartassembly in der Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` an.</span><span class="sxs-lookup"><span data-stu-id="9e51b-193">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="9e51b-194">Die Umgebungsvariable ist eine durch Semikolons getrennte Liste von Assemblys.</span><span class="sxs-lookup"><span data-stu-id="9e51b-194">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="9e51b-195">Nur in Hostingstartassemblys wird nach dem `HostingStartup`-Attribut gesucht.</span><span class="sxs-lookup"><span data-stu-id="9e51b-195">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="9e51b-196">Damit die Beispiel-App *HostingStartupApp* die weiter oben beschriebenen Hostingstarts erkennt, wird für die Umgebungsvariable der folgende Wert festgelegt:</span><span class="sxs-lookup"><span data-stu-id="9e51b-196">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="9e51b-197">Eine Hostingstartassembly kann auch mithilfe der Hostkonfigurationseinstellung [Hostingstartassemblys](xref:fundamentals/host/web-host#hosting-startup-assemblies) festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="9e51b-197">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="9e51b-198">Wenn mehrere Hoststartassemblys vorhanden sind, werden ihre [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure)-Methoden in der Reihenfolge der aufgelisteten Assemblys ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="9e51b-198">When multiple hosting startup assembles are present, their [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="9e51b-199">Aktivierung</span><span class="sxs-lookup"><span data-stu-id="9e51b-199">Activation</span></span>

<span data-ttu-id="9e51b-200">Optionen für die Hostingstartaktivierung:</span><span class="sxs-lookup"><span data-stu-id="9e51b-200">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="9e51b-201">[Laufzeitspeicher](#runtime-store) &ndash; Für die Aktivierung ist kein Kompilierzeitverweis erforderlich.</span><span class="sxs-lookup"><span data-stu-id="9e51b-201">[Runtime store](#runtime-store) &ndash; Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="9e51b-202">Die Beispiel-App legt die Hostingstartassembly und die Abhängigkeitendateien im Ordner *deployment* ab, um die Bereitstellung des Hostingstarts in einer Umgebung mit mehreren Computern zu erleichtern.</span><span class="sxs-lookup"><span data-stu-id="9e51b-202">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="9e51b-203">Der Ordner *deployment* enthält darüber hinaus auch ein PowerShell-Skript, das im Bereitstellungssystem Umgebungsvariablen erstellt oder ändert, um den Hostingstart zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="9e51b-203">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="9e51b-204">Kompilierzeitverweis für die Aktivierung erforderlich</span><span class="sxs-lookup"><span data-stu-id="9e51b-204">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="9e51b-205">NuGet-Paket</span><span class="sxs-lookup"><span data-stu-id="9e51b-205">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="9e51b-206">Ordner „Bin“ des Projekts</span><span class="sxs-lookup"><span data-stu-id="9e51b-206">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="9e51b-207">Laufzeitspeicher</span><span class="sxs-lookup"><span data-stu-id="9e51b-207">Runtime store</span></span>

<span data-ttu-id="9e51b-208">Die Hostingstartimplementierung wird im [Laufzeitspeicher](/dotnet/core/deploying/runtime-store) abgelegt.</span><span class="sxs-lookup"><span data-stu-id="9e51b-208">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="9e51b-209">Ein Kompilierzeitverweis auf die Assembly wird von der erweiterten App nicht benötigt.</span><span class="sxs-lookup"><span data-stu-id="9e51b-209">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="9e51b-210">Nach der Erstellung des Hostingstarts wird mithilfe der Manifestprojektdatei und des Befehls [dotnet store](/dotnet/core/tools/dotnet-store) ein Laufzeitspeicher generiert.</span><span class="sxs-lookup"><span data-stu-id="9e51b-210">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```console
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="9e51b-211">In der Beispiel-App (Projekt *RuntimeStore*) wird der folgende Befehl verwendet:</span><span class="sxs-lookup"><span data-stu-id="9e51b-211">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

``` console
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="9e51b-212">Damit die Runtime den Laufzeitspeicher ermitteln kann, wird der Speicherort des Laufzeitspeichers der Umgebungsvariablen `DOTNET_SHARED_STORE` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="9e51b-212">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="9e51b-213">**Ändern und Ablegen der Abhängigkeitendatei des Hostingstarts**</span><span class="sxs-lookup"><span data-stu-id="9e51b-213">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="9e51b-214">Um die Erweiterung ohne einen Paketverweis auf die Erweiterung zu aktivieren, geben Sie mit `additionalDeps` zusätzliche Abhängigkeiten zur Laufzeit an.</span><span class="sxs-lookup"><span data-stu-id="9e51b-214">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="9e51b-215">`additionalDeps` ermöglicht Ihnen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="9e51b-215">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="9e51b-216">Erweitern des App-Bibliotheksdiagramms durch Bereitstellen einer Reihe zusätzlicher *\*.deps.json*-Dateien, die beim Start mit der App-eigenen *\*.deps.json*-Datei zusammengeführt werden</span><span class="sxs-lookup"><span data-stu-id="9e51b-216">Extend the app's library graph by providing a set of additional *\*.deps.json* files to merge with the app's own *\*.deps.json* file on startup.</span></span>
* <span data-ttu-id="9e51b-217">Bereitstellen der Hostingstartassembly, sodass sie ermittelt und geladen werden kann</span><span class="sxs-lookup"><span data-stu-id="9e51b-217">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="9e51b-218">Folgender Ansatz wird zum Generieren der zusätzlichen Abhängigkeitendatei empfohlen:</span><span class="sxs-lookup"><span data-stu-id="9e51b-218">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="9e51b-219">Führen Sie `dotnet publish` für die Manifestdatei des Laufzeitspeichers aus, auf die im vorherigen Abschnitt verwiesen wurde.</span><span class="sxs-lookup"><span data-stu-id="9e51b-219">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="9e51b-220">Entfernen Sie den Manifestverweis aus den Bibliotheken und dem Abschnitt `runtime` der daraus resultierenden *\*.deps.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="9e51b-220">Remove the manifest reference from libraries and the `runtime` section of the resulting *\*deps.json* file.</span></span>

<span data-ttu-id="9e51b-221">Im Beispielprojekt wird die Eigenschaft `store.manifest/1.0.0` aus den Abschnitten `targets` und `libraries` entfernt:</span><span class="sxs-lookup"><span data-stu-id="9e51b-221">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

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

<span data-ttu-id="9e51b-222">Legen Sie die *\*.deps.json*-Datei an folgendem Speicherort ab:</span><span class="sxs-lookup"><span data-stu-id="9e51b-222">Place the *\*.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="9e51b-223">`{ADDITIONAL DEPENDENCIES PATH}` &ndash; Der Umgebungsvariablen `DOTNET_ADDITIONAL_DEPS` hinzugefügter Speicherort.</span><span class="sxs-lookup"><span data-stu-id="9e51b-223">`{ADDITIONAL DEPENDENCIES PATH}` &ndash; Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="9e51b-224">`{SHARED FRAMEWORK NAME}` &ndash; Freigegebenes Framework, das für diese zusätzliche Abhängigkeitendatei erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="9e51b-224">`{SHARED FRAMEWORK NAME}` &ndash; Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="9e51b-225">`{SHARED FRAMEWORK VERSION}` &ndash; Mindestversion des freigegebenen Frameworks.</span><span class="sxs-lookup"><span data-stu-id="9e51b-225">`{SHARED FRAMEWORK VERSION}` &ndash; Minimum shared framework version.</span></span>
* <span data-ttu-id="9e51b-226">`{ENHANCEMENT ASSEMBLY NAME}` &ndash; Der Name der Erweiterungsassembly.</span><span class="sxs-lookup"><span data-stu-id="9e51b-226">`{ENHANCEMENT ASSEMBLY NAME}` &ndash; The enhancement's assembly name.</span></span>

<span data-ttu-id="9e51b-227">In der Beispiel-App (Projekt *RuntimeStore*) wird die zusätzliche Abhängigkeitendatei an folgendem Speicherort abgelegt:</span><span class="sxs-lookup"><span data-stu-id="9e51b-227">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="9e51b-228">Damit die Runtime den Speicherort des Laufzeitspeichers ermitteln kann, wir die zusätzliche Abhängigkeitendatei der Umgebungsvariablen `DOTNET_ADDITIONAL_DEPS` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="9e51b-228">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="9e51b-229">In der Beispiel-App (Projekt *RuntimeStore*) wird das Erstellen des Laufzeitspeichers und das Generieren der zusätzlichen Abhängigkeitendatei durch ein [PowerShell](/powershell/scripting/powershell-scripting)-Skript durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="9e51b-229">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="9e51b-230">Beispiele zum Festlegen von Umgebungsvariablen für verschiedene Betriebssysteme finden Sie unter [Use multiple environments (Verwenden mehrerer Umgebungen)](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="9e51b-230">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="9e51b-231">**Bereitstellung**</span><span class="sxs-lookup"><span data-stu-id="9e51b-231">**Deployment**</span></span>

<span data-ttu-id="9e51b-232">Um die Bereitstellung eines Hostingstarts in einer Umgebung mit mehreren Computern zu erleichtern, erstellt die Beispiel-App in der veröffentlichten Ausgabe einen *deployment*-Ordner, der Folgendes enthält:</span><span class="sxs-lookup"><span data-stu-id="9e51b-232">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="9e51b-233">Der Laufzeitspeicher des Hostingstarts.</span><span class="sxs-lookup"><span data-stu-id="9e51b-233">The hosting startup runtime store.</span></span>
* <span data-ttu-id="9e51b-234">Die Abhängigkeitendatei des Hostingstarts.</span><span class="sxs-lookup"><span data-stu-id="9e51b-234">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="9e51b-235">Ein PowerShell-Skript, das `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` und `DOTNET_ADDITIONAL_DEPS` erstellt oder ändert, um die Aktivierung des Hostingstarts zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="9e51b-235">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="9e51b-236">Führen Sie das Skript über eine PowerShell-Administratoreingabeaufforderung auf dem Bereitstellungssystem aus.</span><span class="sxs-lookup"><span data-stu-id="9e51b-236">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="9e51b-237">NuGet-Paket</span><span class="sxs-lookup"><span data-stu-id="9e51b-237">NuGet package</span></span>

<span data-ttu-id="9e51b-238">In einem NuGet-Paket kann eine Hostingstarterweiterung bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="9e51b-238">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="9e51b-239">Das Paket enthält ein `HostingStartup`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="9e51b-239">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="9e51b-240">Die vom Paket bereitgestellten Hostingstarttypen werden für die App mit einem der folgenden Verfahren verfügbar gemacht:</span><span class="sxs-lookup"><span data-stu-id="9e51b-240">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="9e51b-241">Die Projektdatei der erweiterten App erstellt einen Paketverweis für den Hostingstart in der Projektdatei der App (Kompilierzeitverweis).</span><span class="sxs-lookup"><span data-stu-id="9e51b-241">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="9e51b-242">Wenn ein Kompilierzeitverweis eingerichtet ist, werden die Hostingstartassembly und alle dazu gehörenden Abhängigkeiten in die Abhängigkeitendatei der App (*\*.deps.json*) eingebunden.</span><span class="sxs-lookup"><span data-stu-id="9e51b-242">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*\*.deps.json*).</span></span> <span data-ttu-id="9e51b-243">Dieses Verfahren gilt für ein in [nuget.org](https://www.nuget.org/) veröffentlichtes Hostingstartassembly-Paket.</span><span class="sxs-lookup"><span data-stu-id="9e51b-243">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="9e51b-244">Die Abhängigkeitendatei des Hostingstarts wird für die erweiterte App wie im Abschnitt [Laufzeitspeicher](#runtime-store) beschrieben (ohne Kompilierzeitverweis) verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="9e51b-244">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="9e51b-245">Weitere Informationen zu NuGet-Paketen und den Laufzeitspeicher finden Sie in den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="9e51b-245">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="9e51b-246">So erstellen Sie ein NuGet-Paket mit plattformübergreifenden Tools</span><span class="sxs-lookup"><span data-stu-id="9e51b-246">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="9e51b-247">Veröffentlichen von Paketen</span><span class="sxs-lookup"><span data-stu-id="9e51b-247">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="9e51b-248">Laufzeitpaketspeicher</span><span class="sxs-lookup"><span data-stu-id="9e51b-248">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="9e51b-249">Ordner „Bin“ des Projekts</span><span class="sxs-lookup"><span data-stu-id="9e51b-249">Project bin folder</span></span>

<span data-ttu-id="9e51b-250">Eine Hostingstarterweiterung kann durch eine mittels *bin* bereitgestellte Assembly in der erweiterten App bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="9e51b-250">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="9e51b-251">Die von der Assembly bereitgestellten Hostingstarttypen werden für die App mit einem der folgenden Verfahren verfügbar gemacht:</span><span class="sxs-lookup"><span data-stu-id="9e51b-251">The hosting startup types provided by the assembly are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="9e51b-252">Die Projektdatei der erweiterten App enthält einen Assemblyverweis auf den Hostingstart (Kompilierzeitverweis).</span><span class="sxs-lookup"><span data-stu-id="9e51b-252">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="9e51b-253">Wenn ein Kompilierzeitverweis eingerichtet ist, werden die Hostingstartassembly und alle dazu gehörenden Abhängigkeiten in die Abhängigkeitendatei der App (*\*.deps.json*) eingebunden.</span><span class="sxs-lookup"><span data-stu-id="9e51b-253">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*\*.deps.json*).</span></span> <span data-ttu-id="9e51b-254">Dieses Verfahren wird angewendet, wenn das Bereitstellungsszenario verlangt, dass die kompilierte Assembly der Hostingstartbibliothek (DLL-Datei) in das verwendete Projekt oder an einen Speicherort verschoben wird, auf den das verwendete Projekt zugreifen kann, und ein Kompilierzeitverweis auf die Assembly des Hostingstarts erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="9e51b-254">This approach applies when the deployment scenario calls for moving the compiled hosting startup library's assembly (DLL file) to the consuming project or to a location accessible by the consuming project and a compile-time reference is made to the hosting startup's assembly.</span></span>
* <span data-ttu-id="9e51b-255">Die Abhängigkeitendatei des Hostingstarts wird für die erweiterte App wie im Abschnitt [Laufzeitspeicher](#runtime-store) beschrieben (ohne Kompilierzeitverweis) verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="9e51b-255">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

## <a name="sample-code"></a><span data-ttu-id="9e51b-256">Beispielcode</span><span class="sxs-lookup"><span data-stu-id="9e51b-256">Sample code</span></span>

<span data-ttu-id="9e51b-257">Der [Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Herunterladen](xref:index#how-to-download-a-sample)) zeigt Szenarios für die Hostingstartimplementierung:</span><span class="sxs-lookup"><span data-stu-id="9e51b-257">The [sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="9e51b-258">Zwei Hostingstartassemblys (Klassenbibliotheken) legen zwei Schlüssel-Wert-Paare der Konfiguration im Arbeitsspeicher fest:</span><span class="sxs-lookup"><span data-stu-id="9e51b-258">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="9e51b-259">NuGet-Paket (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="9e51b-259">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="9e51b-260">Klassenbibliothek (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="9e51b-260">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="9e51b-261">Ein Hostingstart wird über eine im Laufzeitspeicher bereitgestellte Assembly (*StartupDiagnostics*) aktiviert.</span><span class="sxs-lookup"><span data-stu-id="9e51b-261">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="9e51b-262">Die Assembly fügt der App zum Start zwei Middlewares hinzu, die Diagnoseinformationen zu folgenden Komponenten bereitstellen:</span><span class="sxs-lookup"><span data-stu-id="9e51b-262">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="9e51b-263">Registrierte Dienste</span><span class="sxs-lookup"><span data-stu-id="9e51b-263">Registered services</span></span>
  * <span data-ttu-id="9e51b-264">Adresse (Schema, Host, Basispfad, Pfad, Abfragezeichenfolge)</span><span class="sxs-lookup"><span data-stu-id="9e51b-264">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="9e51b-265">Verbindung (Remote-IP, Remoteport, lokale IP, lokaler Port, Clientzertifikat)</span><span class="sxs-lookup"><span data-stu-id="9e51b-265">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="9e51b-266">Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="9e51b-266">Request headers</span></span>
  * <span data-ttu-id="9e51b-267">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="9e51b-267">Environment variables</span></span>

<span data-ttu-id="9e51b-268">So führen Sie das Beispiel aus:</span><span class="sxs-lookup"><span data-stu-id="9e51b-268">To run the sample:</span></span>

<span data-ttu-id="9e51b-269">**Aktivierung über ein NuGet-Paket**</span><span class="sxs-lookup"><span data-stu-id="9e51b-269">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="9e51b-270">Kompilieren Sie das Paket *HostingStartupPackage* mit dem Befehl [dotnet pack](/dotnet/core/tools/dotnet-pack).</span><span class="sxs-lookup"><span data-stu-id="9e51b-270">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="9e51b-271">Fügen Sie den Assemblynamen des Pakets *HostingStartupPackage* zur Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` hinzu.</span><span class="sxs-lookup"><span data-stu-id="9e51b-271">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="9e51b-272">Kompilieren Sie die App, und führen Sie diese aus.</span><span class="sxs-lookup"><span data-stu-id="9e51b-272">Compile and run the app.</span></span> <span data-ttu-id="9e51b-273">In der erweiterten App ist ein Paketverweis vorhanden (Kompilierzeitverweis).</span><span class="sxs-lookup"><span data-stu-id="9e51b-273">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="9e51b-274">Eine `<PropertyGroup>` in der Projektdatei der App gibt die Ausgabe des Paketprojekts (*../HostingStartupPackage/bin/Debug*) als Paketquelle an.</span><span class="sxs-lookup"><span data-stu-id="9e51b-274">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="9e51b-275">Dadurch kann die Anwendung das Paket verwenden, ohne das Paket in [nuget.org](https://www.nuget.org/) hochzuladen. Weitere Informationen finden Sie in den Hinweisen in der Projektdatei von HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="9e51b-275">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```
1. <span data-ttu-id="9e51b-276">Die von der Indexseite gerenderten Schlüsselwerte der Dienstkonfiguration entsprechen den durch die `ServiceKeyInjection.Configure`-Methode des Pakets festgelegten Werten.</span><span class="sxs-lookup"><span data-stu-id="9e51b-276">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="9e51b-277">Wenn Sie am Projekt *HostingStartupPackage* Änderungen vornehmen und das Projekt erneut kompilieren, bereinigen Sie die lokalen NuGet-Paketcaches, um sicherzustellen, dass die *HostingStartupApp* das aktualisierte Paket erhält und kein veraltetes Paket aus dem lokalen Cache.</span><span class="sxs-lookup"><span data-stu-id="9e51b-277">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="9e51b-278">Um die lokalen NuGet-Caches zu bereinigen, führen Sie den folgenden [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals)-Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="9e51b-278">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```console
dotnet nuget locals all --clear
```

<span data-ttu-id="9e51b-279">**Aktivierung über eine Klassenbibliothek**</span><span class="sxs-lookup"><span data-stu-id="9e51b-279">**Activation from a class library**</span></span>

1. <span data-ttu-id="9e51b-280">Kompilieren Sie die Klassenbibliothek *HostingStartupLibrary* mit dem Befehl [dotnet build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="9e51b-280">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="9e51b-281">Fügen Sie der Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` den Assemblynamen *HostingStartupLibrary* der Klassenbibliothek hinzu.</span><span class="sxs-lookup"><span data-stu-id="9e51b-281">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="9e51b-282">Stellen Sie die Assembly der Klassenbibliothek mittels *bin* für die App bereit, indem Sie die Datei *HostingStartupLibrary.dll* aus der kompilierten Ausgabe der Klassenbibliothek in den Ordner *bin/Debug* der App kopieren.</span><span class="sxs-lookup"><span data-stu-id="9e51b-282">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="9e51b-283">Kompilieren Sie die App, und führen Sie diese aus.</span><span class="sxs-lookup"><span data-stu-id="9e51b-283">Compile and run the app.</span></span> <span data-ttu-id="9e51b-284">Eine `<ItemGroup>` in der Projektdatei der App verweist auf die Assembly der Klassenbibliothek (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (Kompilierzeitverweis).</span><span class="sxs-lookup"><span data-stu-id="9e51b-284">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="9e51b-285">Weitere Informationen finden Sie in den Hinweisen in der Projektdatei von HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="9e51b-285">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```
1. <span data-ttu-id="9e51b-286">Die von der Indexseite gerenderten Schlüsselwerte der Dienstkonfiguration entsprechen den durch die `ServiceKeyInjection.Configure`-Methode der Klassenbibliothek festgelegten Werten.</span><span class="sxs-lookup"><span data-stu-id="9e51b-286">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="9e51b-287">**Aktivierung über eine mittels Laufzeitspeicher bereitgestellten Assembly**</span><span class="sxs-lookup"><span data-stu-id="9e51b-287">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="9e51b-288">Im Projekt *StartupDiagnostics* wird [PowerShell](/powershell/scripting/powershell-scripting) verwendet, um die Datei *StartupDiagnostics.deps.json* zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="9e51b-288">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="9e51b-289">PowerShell ist standardmäßig auf Windows-Betriebssystemen ab Windows 7 SP1 und Windows Server 2008 R2 SP1 installiert.</span><span class="sxs-lookup"><span data-stu-id="9e51b-289">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="9e51b-290">Im Artikel [Installing Windows PowerShell (Installieren von Windows PowerShell)](/powershell/scripting/setup/installing-powershell#powershell-core) erfahren Sie, wie Sie PowerShell auf anderen Plattformen nutzen können.</span><span class="sxs-lookup"><span data-stu-id="9e51b-290">To obtain PowerShell on other platforms, see [Installing Windows PowerShell](/powershell/scripting/setup/installing-powershell#powershell-core).</span></span>
1. <span data-ttu-id="9e51b-291">Erstellen Sie das Projekt *StartupDiagnostics*.</span><span class="sxs-lookup"><span data-stu-id="9e51b-291">Build the *StartupDiagnostics* project.</span></span> <span data-ttu-id="9e51b-292">Nachdem das Projekt erstellt wurde, werden von einem Buildziel in der Projektdatei automatisch folgende Aktionen ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="9e51b-292">After the project is built, a build target in the project file automatically:</span></span>
   * <span data-ttu-id="9e51b-293">Löst das PowerShell-Skript aus, um die *StartupDiagnostics.deps.json*-Datei zu ändern.</span><span class="sxs-lookup"><span data-stu-id="9e51b-293">Triggers the PowerShell script to modify the *StartupDiagnostics.deps.json* file.</span></span>
   * <span data-ttu-id="9e51b-294">Verschiebt die Datei *StartupDiagnostics.deps.json* in den Ordner *additionalDeps* des Benutzerprofils.</span><span class="sxs-lookup"><span data-stu-id="9e51b-294">Moves the *StartupDiagnostics.deps.json* file to the user profile's *additionalDeps* folder.</span></span>
1. <span data-ttu-id="9e51b-295">Führen Sie den Befehl `dotnet store` über eine Eingabeaufforderung im Verzeichnis des Hostingstarts aus, um die Assembly und die dazu gehörenden Abhängigkeiten im Laufzeitspeicher des Benutzerprofils zu speichern:</span><span class="sxs-lookup"><span data-stu-id="9e51b-295">Execute the `dotnet store` command at a command prompt in the hosting startup's directory to store the assembly and its dependencies in the user profile's runtime store:</span></span>

   ```console
   dotnet store --manifest StartupDiagnostics.csproj --runtime <RID>
   ```
   <span data-ttu-id="9e51b-296">Bei Windows wird für den Befehl der `win7-x64` [Laufzeitbezeichner (Runtime Identifier (RID))](/dotnet/core/rid-catalog) verwendet.</span><span class="sxs-lookup"><span data-stu-id="9e51b-296">For Windows, the command uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog).</span></span> <span data-ttu-id="9e51b-297">Wenn der Hostingstart für eine andere Laufzeit bereitgestellt wird, muss die entsprechende RID eingegeben werden.</span><span class="sxs-lookup"><span data-stu-id="9e51b-297">When providing the hosting startup for a different runtime, substitute the correct RID.</span></span>
1. <span data-ttu-id="9e51b-298">Legen Sie die folgenden Umgebungsvariablen fest:</span><span class="sxs-lookup"><span data-stu-id="9e51b-298">Set the environment variables:</span></span>
   * <span data-ttu-id="9e51b-299">Fügen Sie der Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` den Assemblynamen *StartupDiagnostics* hinzu.</span><span class="sxs-lookup"><span data-stu-id="9e51b-299">Add the assembly name of *StartupDiagnostics* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="9e51b-300">Legen Sie bei Windows die Umgebungsvariable `DOTNET_ADDITIONAL_DEPS` auf `%UserProfile%\.dotnet\x64\additionalDeps\StartupDiagnostics\` fest.</span><span class="sxs-lookup"><span data-stu-id="9e51b-300">On Windows, set the `DOTNET_ADDITIONAL_DEPS` environment variable to `%UserProfile%\.dotnet\x64\additionalDeps\StartupDiagnostics\`.</span></span> <span data-ttu-id="9e51b-301">Legen Sie bei macOS/Linux die Umgebungsvariable `DOTNET_ADDITIONAL_DEPS` auf `/Users/<USER>/.dotnet/x64/additionalDeps/StartupDiagnostics/`, wobei `<USER>` das Benutzerprofil ist, das den Hostingstart enthält.</span><span class="sxs-lookup"><span data-stu-id="9e51b-301">On macOS/Linux, set the `DOTNET_ADDITIONAL_DEPS` environment variable to `/Users/<USER>/.dotnet/x64/additionalDeps/StartupDiagnostics/`, where `<USER>` is the user profile that contains the hosting startup.</span></span>
1. <span data-ttu-id="9e51b-302">Führen Sie die Beispiel-App aus.</span><span class="sxs-lookup"><span data-stu-id="9e51b-302">Run the sample app.</span></span>
1. <span data-ttu-id="9e51b-303">Fordern Sie den Endpunkt `/services` an, um die registrierten Dienste der App anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="9e51b-303">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="9e51b-304">Fordern Sie den Endpunkt `/diag` an, um Diagnoseinformationen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="9e51b-304">Request the `/diag` endpoint to see the diagnostic information.</span></span>

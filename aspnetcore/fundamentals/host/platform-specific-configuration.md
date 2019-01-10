---
title: Verwenden von Hostingstartassemblys in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie eine ASP.NET Core-App aus einer externen Assembly mithilfe einer Implementierung von IHostingStartup erweitern.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 11/22/2018
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: cf7114698635ab2d61fa19eb15b6a8c61a751e5b
ms.sourcegitcommit: b34b25da2ab68e6495b2460ff570468f16a9bf0d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284720"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="ce4ad-103">Verwenden von Hostingstartassemblys in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ce4ad-103">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="ce4ad-104">Von [Luke Latham](https://github.com/guardrex) und [Pavel Krymets](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="ce4ad-104">By [Luke Latham](https://github.com/guardrex) and [Pavel Krymets](https://github.com/pakrym)</span></span>

<span data-ttu-id="ce4ad-105">Mit einer [IHostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup)-Implementierung (Hostingstart) werden Verbesserungen an einer App beim Start von einer externen Assemblys aus vorgenommen.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-105">An [IHostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup) (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="ce4ad-106">Eine externe Bibliothek kann beispielsweise eine Hostingstartimplementierung verwenden, um zusätzliche Konfigurationsanbieter oder -dienste für eine App bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-106">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span> <span data-ttu-id="ce4ad-107">`IHostingStartup` *ist in ASP.NET Core 2.0 und höher verfügbar*.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-107">`IHostingStartup` *is available in ASP.NET Core 2.0 or later.*</span></span>

<span data-ttu-id="ce4ad-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ce4ad-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="ce4ad-109">Das Attribut HostingStartup</span><span class="sxs-lookup"><span data-stu-id="ce4ad-109">HostingStartup attribute</span></span>

<span data-ttu-id="ce4ad-110">Ein [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute)-Attribut gibt an, ob eine Hostingstartassembly vorhanden ist, die zur Laufzeit aktiviert werden kann.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-110">A [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="ce4ad-111">Bei der Einstiegsassembly oder bei der Assembly, die die Klasse `Startup` enthält, wird automatisch geprüft, ob sie das Attribut `HostingStartup` enthält.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-111">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="ce4ad-112">Die Liste der Assemblys für die Suche nach `HostingStartup`-Attributen wird zur Laufzeit aus der Konfiguration in [WebHostDefaults.HostingStartupAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupassemblieskey) geladen.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-112">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupassemblieskey).</span></span> <span data-ttu-id="ce4ad-113">Die Liste der Assemblys, die aus der Ermittlung ausgeschlossen werden sollen, wird aus [WebHostDefaults.HostingStartupExcludeAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupexcludeassemblieskey) geladen.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-113">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupexcludeassemblieskey).</span></span> <span data-ttu-id="ce4ad-114">Weitere Informationen finden Sie unter [Webhost: Hostingstartassemblys](xref:fundamentals/host/web-host#hosting-startup-assemblies)[Webhost: Auszuschließende Hostingstartassemblys](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="ce4ad-114">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="ce4ad-115">Im folgenden Beispiel lautet der Namespace der Hostingstartassembly `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-115">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="ce4ad-116">`StartupEnhancementHostingStartup` ist die Klasse mit dem Hostingstartcode:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-116">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="ce4ad-117">Das Attribut `HostingStartup` befindet sich in der Regel in der `IHostingStartup`-Implementierungsklassendatei der Hostingstartassembly.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-117">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="ce4ad-118">Ermitteln geladener Hostingstartassemblys</span><span class="sxs-lookup"><span data-stu-id="ce4ad-118">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="ce4ad-119">Zum Ermitteln von geladenen Hostingstartassemblys aktivieren Sie die Protokollierung und überprüfen die Protokolle der App.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-119">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="ce4ad-120">Fehler, die beim Laden von Assemblys auftreten, werden protokolliert.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-120">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="ce4ad-121">Geladene Hostingstartassemblys werden auf der Debugebene protokolliert. Außerdem werden alle Fehler protokolliert.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-121">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="ce4ad-122">Deaktivieren des automatischen Ladens von Hostingstartassemblys</span><span class="sxs-lookup"><span data-stu-id="ce4ad-122">Disable automatic loading of hosting startup assemblies</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="ce4ad-123">Um das automatische Laden von Hostingstartassemblys zu deaktivieren, verwenden Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-123">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="ce4ad-124">Um zu verhindern, dass alle Hostingstartassemblys geladen werden, legen Sie eine der folgenden Einstellungen auf `true` oder `1` fest:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-124">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="ce4ad-125">Hostkonfigurationseinstellung [Verhindern des Hostingstarts](xref:fundamentals/host/web-host#prevent-hosting-startup).</span><span class="sxs-lookup"><span data-stu-id="ce4ad-125">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="ce4ad-126">Die Umgebungsvariable `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="ce4ad-126">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="ce4ad-127">Um zu verhindern, dass bestimmte Hostingstartassemblys geladen werden, legen Sie eine der folgenden Einstellungen auf eine durch Semikolons getrennte Zeichenfolge mit Hostingstartassemblys fest, die beim Starten ausgeschlossen werden sollen:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-127">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="ce4ad-128">Hostkonfigurationseinstellung [Hostingstartausschlussassemblys](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="ce4ad-128">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="ce4ad-129">Die Umgebungsvariable `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="ce4ad-129">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="ce4ad-130">Um das automatische Laden von Hostingstartassemblys zu deaktivieren, legen Sie eine der folgenden Einstellungen auf `true` oder `1` fest:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-130">To disable automatic loading of hosting startup assemblies, set one of the following to `true` or `1`:</span></span>

* <span data-ttu-id="ce4ad-131">Hostkonfigurationseinstellung [Verhindern des Hostingstarts](xref:fundamentals/host/web-host#prevent-hosting-startup).</span><span class="sxs-lookup"><span data-stu-id="ce4ad-131">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
* <span data-ttu-id="ce4ad-132">Die Umgebungsvariable `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="ce4ad-132">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

::: moniker-end

<span data-ttu-id="ce4ad-133">Wenn sowohl die Konfigurationseinstellung für den Host als auch die Umgebungsvariable festgelegt werden, wird das Verhalten durch die Hosteinstellung gesteuert.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-133">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="ce4ad-134">Durch das Deaktivieren von Hostingstartassemblys mithilfe der Hosteinstellung oder der Umgebungsvariable wird die Assembly global deaktiviert. Einige Eigenschaften einer App können ebenfalls deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-134">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="ce4ad-135">Projekt</span><span class="sxs-lookup"><span data-stu-id="ce4ad-135">Project</span></span>

<span data-ttu-id="ce4ad-136">Erstellen Sie einen Hostingstart mit einem der folgenden Projekttypen:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-136">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="ce4ad-137">Klassenbibliothek</span><span class="sxs-lookup"><span data-stu-id="ce4ad-137">Class library</span></span>](#class-library)
* [<span data-ttu-id="ce4ad-138">Konsolen-App ohne Einstiegspunkt</span><span class="sxs-lookup"><span data-stu-id="ce4ad-138">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="ce4ad-139">Klassenbibliothek</span><span class="sxs-lookup"><span data-stu-id="ce4ad-139">Class library</span></span>

<span data-ttu-id="ce4ad-140">In einer Klassenbibliothek kann eine Hostingstarterweiterung bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-140">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="ce4ad-141">Die Bibliothek enthält ein `HostingStartup`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-141">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="ce4ad-142">Der [Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) enthält die Razor Pages-App *HostingStartupApp* und die Klassenbibliothek *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-142">The [sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="ce4ad-143">Die Klassenbibliothek:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-143">The class library:</span></span>

* <span data-ttu-id="ce4ad-144">Enthält die Hostingstartklasse `ServiceKeyInjection`, die `IHostingStartup` implementiert.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-144">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="ce4ad-145">`ServiceKeyInjection` fügt mithilfe des Anbieters der Konfiguration im Arbeitsspeicher ([AddInMemoryCollection](/dotnet/api/microsoft.extensions.configuration.memoryconfigurationbuilderextensions.addinmemorycollection)) der App-Konfiguration zwei Dienstzeichenfolgen hinzu.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-145">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](/dotnet/api/microsoft.extensions.configuration.memoryconfigurationbuilderextensions.addinmemorycollection)).</span></span>
* <span data-ttu-id="ce4ad-146">Enthält ein `HostingStartup`-Attribut, das den Namespace und die Klasse des Hostingstarts angibt.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-146">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="ce4ad-147">Die [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure)-Methode der Klasse `ServiceKeyInjection` verwendet [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder), um einer App Erweiterungen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-147">The `ServiceKeyInjection` class's [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) method uses an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) to add enhancements to an app.</span></span> <span data-ttu-id="ce4ad-148">`IHostingStartup.Configure` wird in der Hoststartassembly von der Runtime vor `Startup.Configure` im Benutzercode aufgerufen. Dadurch kann Benutzercode jede Konfiguration der Hoststartassembly überschreiben.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-148">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

<span data-ttu-id="ce4ad-149">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-149">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="ce4ad-150">Die Indexseite der App liest und rendert die Konfigurationswerte für die beiden Schlüssel, die von der Hostingstartassembly der Klassenbibliothek festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-150">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="ce4ad-151">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-151">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="ce4ad-152">Der [Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) enthält darüber hinaus auch ein NuGet-Paketprojekt, das den separaten Hostingstart *HostingStartupPackage* bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-152">The [sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="ce4ad-153">Das Paket weist dieselben Merkmale wie die bereits beschriebene Klassenbibliothek auf.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-153">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="ce4ad-154">Das Paket:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-154">The package:</span></span>

* <span data-ttu-id="ce4ad-155">Enthält die Hostingstartklasse `ServiceKeyInjection`, die `IHostingStartup` implementiert.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-155">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="ce4ad-156">`ServiceKeyInjection` fügt der App-Konfiguration zwei Dienstzeichenfolgen hinzu.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-156">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="ce4ad-157">Enthält ein `HostingStartup`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-157">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="ce4ad-158">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-158">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="ce4ad-159">Die Indexseite der App liest und rendert die Konfigurationswerte für die beiden Schlüssel, die von der Hostingstartassembly der Bibliothek festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-159">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="ce4ad-160">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-160">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="ce4ad-161">Konsolen-App ohne Einstiegspunkt</span><span class="sxs-lookup"><span data-stu-id="ce4ad-161">Console app without an entry point</span></span>

<span data-ttu-id="ce4ad-162">*Dieses Verfahren ist nur für .NET Core-Apps, nicht jedoch für .NET Framework verfügbar.*</span><span class="sxs-lookup"><span data-stu-id="ce4ad-162">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="ce4ad-163">Eine dynamische Hostingstarterweiterung, die zur Aktivierung keinen Kompilierungszeitverweis erfordert, kann in einer Konsolen-App ohne Einstiegspunkt bereitgestellt werden, die ein `HostingStartup`-Attribut enthält.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-163">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="ce4ad-164">Durch die Veröffentlichung der Konsolen-App wird eine Hostingstartassembly erstellt, die über den Laufzeitspeicher genutzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-164">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="ce4ad-165">In diesem Prozess wird aus folgenden Gründen eine Konsolen-App ohne Einstiegspunkt verwendet:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-165">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="ce4ad-166">Eine Abhängigkeitendatei ist erforderlich, um den Hostingstart in der Hostingstartassembly zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-166">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="ce4ad-167">Eine Abhängigkeitendatei ist eine ausführbare App-Ressource, die durch das Veröffentlichen einer App, nicht einer Bibliothek, erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-167">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="ce4ad-168">Eine Bibliothek kann dem [Laufzeitpaketspeicher](/dotnet/core/deploying/runtime-store), der ein ausführbares Projekt benötigt, das die freigegebene Laufzeit als Ziel verwendet, nicht direkt hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-168">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="ce4ad-169">Bei der Erstellung eines dynamischen Hostingstarts geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-169">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="ce4ad-170">Eine Hostingstartassembly wird über die Konsolen-App ohne Einstiegspunkt erstellt, für die Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-170">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="ce4ad-171">Sie enthält eine Klasse mit der `IHostingStartup`-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-171">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="ce4ad-172">Sie enthält ein [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute)-Attribut zum Identifizieren der `IHostingStartup`-Implementierungsklasse.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-172">Includes a [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="ce4ad-173">Die Konsolen-App wird veröffentlicht, um die Abhängigkeiten des Hostingstarts abzurufen.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-173">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="ce4ad-174">Die Veröffentlichung der Konsolen-App hat unter anderem zur Folge, dass nicht verwendete Abhängigkeiten aus der Abhängigkeitendatei entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-174">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="ce4ad-175">Die Abhängigkeitendatei wird so geändert, dass der Laufzeitspeicherort der Hostingstartassembly festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-175">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="ce4ad-176">Die Hostingstartassembly und die dazu gehörende Abhängigkeitendatei werden im Laufzeitpaketspeicher abgelegt.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-176">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="ce4ad-177">Damit die Hostingstartassembly und die entsprechende Abhängigkeitendatei erkannt werden, werden sie in einem Paar von Umgebungsvariablen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-177">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="ce4ad-178">Die Konsolen-App verweist auf das Paket [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):</span><span class="sxs-lookup"><span data-stu-id="ce4ad-178">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="ce4ad-179">Ein [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute)-Attribut identifiziert eine Klasse bei der Erstellung von [IWebHost](/dotnet/api/microsoft.aspnetcore.hosting.iwebhost) als eine Implementierung von `IHostingStartup` zum Laden und Ausführen.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-179">A [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the [IWebHost](/dotnet/api/microsoft.aspnetcore.hosting.iwebhost).</span></span> <span data-ttu-id="ce4ad-180">Im folgenden Beispiel ist der Namespace `StartupEnhancement` und die Klasse ist `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-180">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="ce4ad-181">Eine Klasse implementiert `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-181">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="ce4ad-182">Die [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure)-Methode der Klasse verwendet [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder), um einer App Erweiterungen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-182">The class's [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) method uses an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) to add enhancements to an app.</span></span> <span data-ttu-id="ce4ad-183">`IHostingStartup.Configure` wird in der Hoststartassembly von der Runtime vor `Startup.Configure` im Benutzercode aufgerufen. Dadurch kann Benutzercode jede Konfiguration der Hoststartassembly überschreiben.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-183">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="ce4ad-184">Die Datei für die Abhängigkeiten (*\*.deps.json*) legt beim Erstellen eine `IHostingStartup`-Projekts den `runtime`-Speicherort der Assembly auf den *bin*-Ordner fest:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-184">When building an `IHostingStartup` project, the dependencies file (*\*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="ce4ad-185">Nur ein Teil der Datei wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-185">Only part of the file is shown.</span></span> <span data-ttu-id="ce4ad-186">`StartupEnhancement` ist der Name der Assembly im Beispiel.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-186">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="ce4ad-187">Angeben der Hostingstartassembly</span><span class="sxs-lookup"><span data-stu-id="ce4ad-187">Specify the hosting startup assembly</span></span>

<span data-ttu-id="ce4ad-188">Geben Sie für einen von der Klassenbibliothek oder von der Konsolen-App bereitgestellten Hostingstart den Namen der Hostingstartassembly in der Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` an.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-188">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="ce4ad-189">Die Umgebungsvariable ist eine durch Semikolons getrennte Liste von Assemblys.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-189">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="ce4ad-190">Nur in Hostingstartassemblys wird nach dem `HostingStartup`-Attribut gesucht.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-190">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="ce4ad-191">Damit die Beispiel-App *HostingStartupApp* die weiter oben beschriebenen Hostingstarts erkennt, wird für die Umgebungsvariable der folgende Wert festgelegt:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-191">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="ce4ad-192">Eine Hostingstartassembly kann auch mithilfe der Hostkonfigurationseinstellung [Hostingstartassemblys](xref:fundamentals/host/web-host#hosting-startup-assemblies) festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-192">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="ce4ad-193">Wenn mehrere Hoststartassemblys vorhanden sind, werden ihre [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure)-Methoden in der Reihenfolge der aufgelisteten Assemblys ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-193">When multiple hosting startup assembles are present, their [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="ce4ad-194">Aktivierung</span><span class="sxs-lookup"><span data-stu-id="ce4ad-194">Activation</span></span>

<span data-ttu-id="ce4ad-195">Optionen für die Hostingstartaktivierung:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-195">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="ce4ad-196">[Laufzeitspeicher](#runtime-store) &ndash; Für die Aktivierung ist kein Kompilierzeitverweis erforderlich.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-196">[Runtime store](#runtime-store) &ndash; Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="ce4ad-197">Die Beispiel-App legt die Hostingstartassembly und die Abhängigkeitendateien im Ordner *deployment* ab, um die Bereitstellung des Hostingstarts in einer Umgebung mit mehreren Computern zu erleichtern.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-197">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="ce4ad-198">Der Ordner *deployment* enthält darüber hinaus auch ein PowerShell-Skript, das im Bereitstellungssystem Umgebungsvariablen erstellt oder ändert, um den Hostingstart zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-198">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="ce4ad-199">Kompilierzeitverweis für die Aktivierung erforderlich</span><span class="sxs-lookup"><span data-stu-id="ce4ad-199">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="ce4ad-200">NuGet-Paket</span><span class="sxs-lookup"><span data-stu-id="ce4ad-200">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="ce4ad-201">Ordner „Bin“ des Projekts</span><span class="sxs-lookup"><span data-stu-id="ce4ad-201">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="ce4ad-202">Laufzeitspeicher</span><span class="sxs-lookup"><span data-stu-id="ce4ad-202">Runtime store</span></span>

<span data-ttu-id="ce4ad-203">Die Hostingstartimplementierung wird im [Laufzeitspeicher](/dotnet/core/deploying/runtime-store) abgelegt.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-203">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="ce4ad-204">Ein Kompilierzeitverweis auf die Assembly wird von der erweiterten App nicht benötigt.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-204">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="ce4ad-205">Nach der Erstellung des Hostingstarts wird mithilfe der Manifestprojektdatei und des Befehls [dotnet store](/dotnet/core/tools/dotnet-store) ein Laufzeitspeicher generiert.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-205">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```console
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="ce4ad-206">In der Beispiel-App (Projekt *RuntimeStore*) wird der folgende Befehl verwendet:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-206">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

``` console
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="ce4ad-207">Damit die Runtime den Laufzeitspeicher ermitteln kann, wird der Speicherort des Laufzeitspeichers der Umgebungsvariablen `DOTNET_SHARED_STORE` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-207">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="ce4ad-208">**Ändern und Ablegen der Abhängigkeitendatei des Hostingstarts**</span><span class="sxs-lookup"><span data-stu-id="ce4ad-208">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="ce4ad-209">Um die Erweiterung ohne einen Paketverweis auf die Erweiterung zu aktivieren, geben Sie mit `additionalDeps` zusätzliche Abhängigkeiten zur Laufzeit an.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-209">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="ce4ad-210">`additionalDeps` ermöglicht Ihnen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-210">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="ce4ad-211">Erweitern des App-Bibliotheksdiagramms durch Bereitstellen einer Reihe zusätzlicher *\*.deps.json*-Dateien, die beim Start mit der App-eigenen *\*.deps.json*-Datei zusammengeführt werden</span><span class="sxs-lookup"><span data-stu-id="ce4ad-211">Extend the app's library graph by providing a set of additional *\*.deps.json* files to merge with the app's own *\*.deps.json* file on startup.</span></span>
* <span data-ttu-id="ce4ad-212">Bereitstellen der Hostingstartassembly, sodass sie ermittelt und geladen werden kann</span><span class="sxs-lookup"><span data-stu-id="ce4ad-212">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="ce4ad-213">Folgender Ansatz wird zum Generieren der zusätzlichen Abhängigkeitendatei empfohlen:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-213">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="ce4ad-214">Führen Sie `dotnet publish` für die Manifestdatei des Laufzeitspeichers aus, auf die im vorherigen Abschnitt verwiesen wurde.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-214">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="ce4ad-215">Entfernen Sie den Manifestverweis aus den Bibliotheken und dem Abschnitt `runtime` der daraus resultierenden *\*.deps.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-215">Remove the manifest reference from libraries and the `runtime` section of the resulting *\*deps.json* file.</span></span>

<span data-ttu-id="ce4ad-216">Im Beispielprojekt wird die Eigenschaft `store.manifest/1.0.0` aus den Abschnitten `targets` und `libraries` entfernt:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-216">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

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

<span data-ttu-id="ce4ad-217">Legen Sie die *\*.deps.json*-Datei an folgendem Speicherort ab:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-217">Place the *\*.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="ce4ad-218">`{ADDITIONAL DEPENDENCIES PATH}` &ndash; Der Umgebungsvariablen `DOTNET_ADDITIONAL_DEPS` hinzugefügter Speicherort.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-218">`{ADDITIONAL DEPENDENCIES PATH}` &ndash; Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="ce4ad-219">`{SHARED FRAMEWORK NAME}` &ndash; Freigegebenes Framework, das für diese zusätzliche Abhängigkeitendatei erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-219">`{SHARED FRAMEWORK NAME}` &ndash; Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="ce4ad-220">`{SHARED FRAMEWORK VERSION}` &ndash; Mindestversion des freigegebenen Frameworks.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-220">`{SHARED FRAMEWORK VERSION}` &ndash; Minimum shared framework version.</span></span>
* <span data-ttu-id="ce4ad-221">`{ENHANCEMENT ASSEMBLY NAME}` &ndash; Der Name der Erweiterungsassembly.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-221">`{ENHANCEMENT ASSEMBLY NAME}` &ndash; The enhancement's assembly name.</span></span>

<span data-ttu-id="ce4ad-222">In der Beispiel-App (Projekt *RuntimeStore*) wird die zusätzliche Abhängigkeitendatei an folgendem Speicherort abgelegt:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-222">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="ce4ad-223">Damit die Runtime den Speicherort des Laufzeitspeichers ermitteln kann, wir die zusätzliche Abhängigkeitendatei der Umgebungsvariablen `DOTNET_ADDITIONAL_DEPS` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-223">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="ce4ad-224">In der Beispiel-App (Projekt *RuntimeStore*) wird das Erstellen des Laufzeitspeichers und das Generieren der zusätzlichen Abhängigkeitendatei durch ein [PowerShell](/powershell/scripting/powershell-scripting)-Skript durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-224">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="ce4ad-225">Beispiele zum Festlegen von Umgebungsvariablen für verschiedene Betriebssysteme finden Sie unter [Use multiple environments (Verwenden mehrerer Umgebungen)](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="ce4ad-225">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="ce4ad-226">**Bereitstellung**</span><span class="sxs-lookup"><span data-stu-id="ce4ad-226">**Deployment**</span></span>

<span data-ttu-id="ce4ad-227">Um die Bereitstellung eines Hostingstarts in einer Umgebung mit mehreren Computern zu erleichtern, erstellt die Beispiel-App in der veröffentlichten Ausgabe einen *deployment*-Ordner, der Folgendes enthält:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-227">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="ce4ad-228">Der Laufzeitspeicher des Hostingstarts.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-228">The hosting startup runtime store.</span></span>
* <span data-ttu-id="ce4ad-229">Die Abhängigkeitendatei des Hostingstarts.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-229">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="ce4ad-230">Ein PowerShell-Skript, das `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` und `DOTNET_ADDITIONAL_DEPS` erstellt oder ändert, um die Aktivierung des Hostingstarts zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-230">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="ce4ad-231">Führen Sie das Skript über eine PowerShell-Administratoreingabeaufforderung auf dem Bereitstellungssystem aus.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-231">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="ce4ad-232">NuGet-Paket</span><span class="sxs-lookup"><span data-stu-id="ce4ad-232">NuGet package</span></span>

<span data-ttu-id="ce4ad-233">In einem NuGet-Paket kann eine Hostingstarterweiterung bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-233">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="ce4ad-234">Das Paket enthält ein `HostingStartup`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-234">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="ce4ad-235">Die vom Paket bereitgestellten Hostingstarttypen werden für die App mit einem der folgenden Verfahren verfügbar gemacht:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-235">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="ce4ad-236">Die Projektdatei der erweiterten App erstellt einen Paketverweis für den Hostingstart in der Projektdatei der App (Kompilierzeitverweis).</span><span class="sxs-lookup"><span data-stu-id="ce4ad-236">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="ce4ad-237">Wenn ein Kompilierzeitverweis eingerichtet ist, werden die Hostingstartassembly und alle dazu gehörenden Abhängigkeiten in die Abhängigkeitendatei der App (*\*.deps.json*) eingebunden.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-237">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*\*.deps.json*).</span></span> <span data-ttu-id="ce4ad-238">Dieses Verfahren gilt für ein in [nuget.org](https://www.nuget.org/) veröffentlichtes Hostingstartassembly-Paket.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-238">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="ce4ad-239">Die Abhängigkeitendatei des Hostingstarts wird für die erweiterte App wie im Abschnitt [Laufzeitspeicher](#runtime-store) beschrieben (ohne Kompilierzeitverweis) verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-239">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="ce4ad-240">Weitere Informationen zu NuGet-Paketen und den Laufzeitspeicher finden Sie in den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-240">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="ce4ad-241">So erstellen Sie ein NuGet-Paket mit plattformübergreifenden Tools</span><span class="sxs-lookup"><span data-stu-id="ce4ad-241">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="ce4ad-242">Veröffentlichen von Paketen</span><span class="sxs-lookup"><span data-stu-id="ce4ad-242">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="ce4ad-243">Laufzeitpaketspeicher</span><span class="sxs-lookup"><span data-stu-id="ce4ad-243">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="ce4ad-244">Ordner „Bin“ des Projekts</span><span class="sxs-lookup"><span data-stu-id="ce4ad-244">Project bin folder</span></span>

<span data-ttu-id="ce4ad-245">Eine Hostingstarterweiterung kann durch eine mittels *bin* bereitgestellte Assembly in der erweiterten App bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-245">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="ce4ad-246">Die von der Assembly bereitgestellten Hostingstarttypen werden für die App mit einem der folgenden Verfahren verfügbar gemacht:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-246">The hosting startup types provided by the assembly are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="ce4ad-247">Die Projektdatei der erweiterten App enthält einen Assemblyverweis auf den Hostingstart (Kompilierzeitverweis).</span><span class="sxs-lookup"><span data-stu-id="ce4ad-247">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="ce4ad-248">Wenn ein Kompilierzeitverweis eingerichtet ist, werden die Hostingstartassembly und alle dazu gehörenden Abhängigkeiten in die Abhängigkeitendatei der App (*\*.deps.json*) eingebunden.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-248">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*\*.deps.json*).</span></span> <span data-ttu-id="ce4ad-249">Dieses Verfahren wird angewendet, wenn das Bereitstellungsszenario verlangt, dass die kompilierte Assembly der Hostingstartbibliothek (DLL-Datei) in das verwendete Projekt oder an einen Speicherort verschoben wird, auf den das verwendete Projekt zugreifen kann, und ein Kompilierzeitverweis auf die Assembly des Hostingstarts erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-249">This approach applies when the deployment scenario calls for moving the compiled hosting startup library's assembly (DLL file) to the consuming project or to a location accessible by the consuming project and a compile-time reference is made to the hosting startup's assembly.</span></span>
* <span data-ttu-id="ce4ad-250">Die Abhängigkeitendatei des Hostingstarts wird für die erweiterte App wie im Abschnitt [Laufzeitspeicher](#runtime-store) beschrieben (ohne Kompilierzeitverweis) verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-250">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

## <a name="sample-code"></a><span data-ttu-id="ce4ad-251">Beispielcode</span><span class="sxs-lookup"><span data-stu-id="ce4ad-251">Sample code</span></span>

<span data-ttu-id="ce4ad-252">Der [Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Herunterladen](xref:index#how-to-download-a-sample)) zeigt Szenarios für die Hostingstartimplementierung:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-252">The [sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="ce4ad-253">Zwei Hostingstartassemblys (Klassenbibliotheken) legen zwei Schlüssel-Wert-Paare der Konfiguration im Arbeitsspeicher fest:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-253">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="ce4ad-254">NuGet-Paket (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="ce4ad-254">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="ce4ad-255">Klassenbibliothek (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="ce4ad-255">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="ce4ad-256">Ein Hostingstart wird über eine im Laufzeitspeicher bereitgestellte Assembly (*StartupDiagnostics*) aktiviert.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-256">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="ce4ad-257">Die Assembly fügt der App zum Start zwei Middlewares hinzu, die Diagnoseinformationen zu folgenden Komponenten bereitstellen:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-257">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="ce4ad-258">Registrierte Dienste</span><span class="sxs-lookup"><span data-stu-id="ce4ad-258">Registered services</span></span>
  * <span data-ttu-id="ce4ad-259">Adresse (Schema, Host, Basispfad, Pfad, Abfragezeichenfolge)</span><span class="sxs-lookup"><span data-stu-id="ce4ad-259">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="ce4ad-260">Verbindung (Remote-IP, Remoteport, lokale IP, lokaler Port, Clientzertifikat)</span><span class="sxs-lookup"><span data-stu-id="ce4ad-260">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="ce4ad-261">Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="ce4ad-261">Request headers</span></span>
  * <span data-ttu-id="ce4ad-262">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="ce4ad-262">Environment variables</span></span>

<span data-ttu-id="ce4ad-263">So führen Sie das Beispiel aus:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-263">To run the sample:</span></span>

<span data-ttu-id="ce4ad-264">**Aktivierung über ein NuGet-Paket**</span><span class="sxs-lookup"><span data-stu-id="ce4ad-264">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="ce4ad-265">Kompilieren Sie das Paket *HostingStartupPackage* mit dem Befehl [dotnet pack](/dotnet/core/tools/dotnet-pack).</span><span class="sxs-lookup"><span data-stu-id="ce4ad-265">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="ce4ad-266">Fügen Sie den Assemblynamen des Pakets *HostingStartupPackage* zur Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` hinzu.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-266">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="ce4ad-267">Kompilieren Sie die App, und führen Sie diese aus.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-267">Compile and run the app.</span></span> <span data-ttu-id="ce4ad-268">In der erweiterten App ist ein Paketverweis vorhanden (Kompilierzeitverweis).</span><span class="sxs-lookup"><span data-stu-id="ce4ad-268">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="ce4ad-269">Eine `<PropertyGroup>` in der Projektdatei der App gibt die Ausgabe des Paketprojekts (*../HostingStartupPackage/bin/Debug*) als Paketquelle an.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-269">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="ce4ad-270">Dadurch kann die Anwendung das Paket verwenden, ohne das Paket in [nuget.org](https://www.nuget.org/) hochzuladen. Weitere Informationen finden Sie in den Hinweisen in der Projektdatei von HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-270">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```
1. <span data-ttu-id="ce4ad-271">Die von der Indexseite gerenderten Schlüsselwerte der Dienstkonfiguration entsprechen den durch die `ServiceKeyInjection.Configure`-Methode des Pakets festgelegten Werten.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-271">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="ce4ad-272">Wenn Sie am Projekt *HostingStartupPackage* Änderungen vornehmen und das Projekt erneut kompilieren, bereinigen Sie die lokalen NuGet-Paketcaches, um sicherzustellen, dass die *HostingStartupApp* das aktualisierte Paket erhält und kein veraltetes Paket aus dem lokalen Cache.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-272">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="ce4ad-273">Um die lokalen NuGet-Caches zu bereinigen, führen Sie den folgenden [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals)-Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-273">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```console
dotnet nuget locals all --clear
```

<span data-ttu-id="ce4ad-274">**Aktivierung über eine Klassenbibliothek**</span><span class="sxs-lookup"><span data-stu-id="ce4ad-274">**Activation from a class library**</span></span>

1. <span data-ttu-id="ce4ad-275">Kompilieren Sie die Klassenbibliothek *HostingStartupLibrary* mit dem Befehl [dotnet build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="ce4ad-275">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="ce4ad-276">Fügen Sie der Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` den Assemblynamen *HostingStartupLibrary* der Klassenbibliothek hinzu.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-276">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="ce4ad-277">Stellen Sie die Assembly der Klassenbibliothek mittels *bin* für die App bereit, indem Sie die Datei *HostingStartupLibrary.dll* aus der kompilierten Ausgabe der Klassenbibliothek in den Ordner *bin/Debug* der App kopieren.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-277">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="ce4ad-278">Kompilieren Sie die App, und führen Sie diese aus.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-278">Compile and run the app.</span></span> <span data-ttu-id="ce4ad-279">Eine `<ItemGroup>` in der Projektdatei der App verweist auf die Assembly der Klassenbibliothek (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (Kompilierzeitverweis).</span><span class="sxs-lookup"><span data-stu-id="ce4ad-279">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="ce4ad-280">Weitere Informationen finden Sie in den Hinweisen in der Projektdatei von HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-280">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```
1. <span data-ttu-id="ce4ad-281">Die von der Indexseite gerenderten Schlüsselwerte der Dienstkonfiguration entsprechen den durch die `ServiceKeyInjection.Configure`-Methode der Klassenbibliothek festgelegten Werten.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-281">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="ce4ad-282">**Aktivierung über eine mittels Laufzeitspeicher bereitgestellten Assembly**</span><span class="sxs-lookup"><span data-stu-id="ce4ad-282">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="ce4ad-283">Im Projekt *StartupDiagnostics* wird [PowerShell](/powershell/scripting/powershell-scripting) verwendet, um die Datei *StartupDiagnostics.deps.json* zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-283">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="ce4ad-284">PowerShell ist standardmäßig auf Windows-Betriebssystemen ab Windows 7 SP1 und Windows Server 2008 R2 SP1 installiert.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-284">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="ce4ad-285">Im Artikel [Installing Windows PowerShell (Installieren von Windows PowerShell)](/powershell/scripting/setup/installing-powershell#powershell-core) erfahren Sie, wie Sie PowerShell auf anderen Plattformen nutzen können.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-285">To obtain PowerShell on other platforms, see [Installing Windows PowerShell](/powershell/scripting/setup/installing-powershell#powershell-core).</span></span>
1. <span data-ttu-id="ce4ad-286">Erstellen Sie das Projekt *StartupDiagnostics*.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-286">Build the *StartupDiagnostics* project.</span></span> <span data-ttu-id="ce4ad-287">Nachdem das Projekt erstellt wurde, werden von einem Buildziel in der Projektdatei automatisch folgende Aktionen ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-287">After the project is built, a build target in the project file automatically:</span></span>
   * <span data-ttu-id="ce4ad-288">Löst das PowerShell-Skript aus, um die *StartupDiagnostics.deps.json*-Datei zu ändern.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-288">Triggers the PowerShell script to modify the *StartupDiagnostics.deps.json* file.</span></span>
   * <span data-ttu-id="ce4ad-289">Verschiebt die Datei *StartupDiagnostics.deps.json* in den Ordner *additionalDeps* des Benutzerprofils.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-289">Moves the *StartupDiagnostics.deps.json* file to the user profile's *additionalDeps* folder.</span></span>
1. <span data-ttu-id="ce4ad-290">Führen Sie den Befehl `dotnet store` über eine Eingabeaufforderung im Verzeichnis des Hostingstarts aus, um die Assembly und die dazu gehörenden Abhängigkeiten im Laufzeitspeicher des Benutzerprofils zu speichern:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-290">Execute the `dotnet store` command at a command prompt in the hosting startup's directory to store the assembly and its dependencies in the user profile's runtime store:</span></span>

   ```console
   dotnet store --manifest StartupDiagnostics.csproj --runtime <RID>
   ```
   <span data-ttu-id="ce4ad-291">Bei Windows wird für den Befehl der `win7-x64` [Laufzeitbezeichner (Runtime Identifier (RID))](/dotnet/core/rid-catalog) verwendet.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-291">For Windows, the command uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog).</span></span> <span data-ttu-id="ce4ad-292">Wenn der Hostingstart für eine andere Laufzeit bereitgestellt wird, muss die entsprechende RID eingegeben werden.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-292">When providing the hosting startup for a different runtime, substitute the correct RID.</span></span>
1. <span data-ttu-id="ce4ad-293">Legen Sie die folgenden Umgebungsvariablen fest:</span><span class="sxs-lookup"><span data-stu-id="ce4ad-293">Set the environment variables:</span></span>
   * <span data-ttu-id="ce4ad-294">Fügen Sie der Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` den Assemblynamen *StartupDiagnostics* hinzu.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-294">Add the assembly name of *StartupDiagnostics* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="ce4ad-295">Legen Sie bei Windows die Umgebungsvariable `DOTNET_ADDITIONAL_DEPS` auf `%UserProfile%\.dotnet\x64\additionalDeps\StartupDiagnostics\` fest.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-295">On Windows, set the `DOTNET_ADDITIONAL_DEPS` environment variable to `%UserProfile%\.dotnet\x64\additionalDeps\StartupDiagnostics\`.</span></span> <span data-ttu-id="ce4ad-296">Legen Sie bei macOS/Linux die Umgebungsvariable `DOTNET_ADDITIONAL_DEPS` auf `/Users/<USER>/.dotnet/x64/additionalDeps/StartupDiagnostics/`, wobei `<USER>` das Benutzerprofil ist, das den Hostingstart enthält.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-296">On macOS/Linux, set the `DOTNET_ADDITIONAL_DEPS` environment variable to `/Users/<USER>/.dotnet/x64/additionalDeps/StartupDiagnostics/`, where `<USER>` is the user profile that contains the hosting startup.</span></span>
1. <span data-ttu-id="ce4ad-297">Führen Sie die Beispiel-App aus.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-297">Run the sample app.</span></span>
1. <span data-ttu-id="ce4ad-298">Fordern Sie den Endpunkt `/services` an, um die registrierten Dienste der App anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-298">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="ce4ad-299">Fordern Sie den Endpunkt `/diag` an, um Diagnoseinformationen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="ce4ad-299">Request the `/diag` endpoint to see the diagnostic information.</span></span>

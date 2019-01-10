---
title: ASP.NET Core-Webhost
author: guardrex
description: Erfahren Sie mehr über den Webhost in ASP.NET Core, der für das Starten der App und das Verwalten der Lebensdauer verantwortlich ist.
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2018
uid: fundamentals/host/web-host
ms.openlocfilehash: 7215027a083c0ed0bc3b15196e390a31c5dcfc14
ms.sourcegitcommit: 816f39e852a8f453e8682081871a31bc66db153a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53637845"
---
# <a name="aspnet-core-web-host"></a><span data-ttu-id="4e51f-103">ASP.NET Core-Webhost</span><span class="sxs-lookup"><span data-stu-id="4e51f-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="4e51f-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4e51f-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="4e51f-105">Laden Sie für die Version 1.1 dieses Themas den [ASP.NET Core-Webhost (Version 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/Web-Host_1.1.pdf) herunter.</span><span class="sxs-lookup"><span data-stu-id="4e51f-105">For the 1.1 version of this topic, download [ASP.NET Core Web Host (version 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/Web-Host_1.1.pdf).</span></span>

::: moniker-end

<span data-ttu-id="4e51f-106">Durch ASP.NET Core-Apps kann ein *Host* gestartet und konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="4e51f-106">ASP.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="4e51f-107">Der Host ist verantwortlich für das Starten der App und das Verwalten der Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="4e51f-107">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="4e51f-108">Der Host konfiguriert mindestens einen Server und eine Pipeline für die Anforderungsverarbeitung.</span><span class="sxs-lookup"><span data-stu-id="4e51f-108">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="4e51f-109">In diesem Artikel wird der ASP.NET Core-Webhost ([IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)) behandelt, der für das Hosten von Web-Apps nützlich ist.</span><span class="sxs-lookup"><span data-stu-id="4e51f-109">This topic covers the ASP.NET Core Web Host ([IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)), which is useful for hosting web apps.</span></span> <span data-ttu-id="4e51f-110">Weitere Informationen zum generischen .NET-Host ([IHostBuilder](/dotnet/api/microsoft.extensions.hosting.ihostbuilder)) finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="4e51f-110">For coverage of the .NET Generic Host ([IHostBuilder](/dotnet/api/microsoft.extensions.hosting.ihostbuilder)), see <xref:fundamentals/host/generic-host>.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="4e51f-111">Einrichten eines Hosts</span><span class="sxs-lookup"><span data-stu-id="4e51f-111">Set up a host</span></span>

<span data-ttu-id="4e51f-112">Erstellen Sie einen Host mithilfe einer Instanz von [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="4e51f-112">Create a host using an instance of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="4e51f-113">Dies wird üblicherweise am Einstiegspunkt der App (die `Main`-Methode) durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="4e51f-113">This is typically performed in the app's entry point, the `Main` method.</span></span> <span data-ttu-id="4e51f-114">In den Projektvorlagen befindet `Main` sich in *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="4e51f-114">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="4e51f-115">Eine typische *Program.cs*-Datei ruft [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) auf, um die Einrichtung eines Hosts zu starten:</span><span class="sxs-lookup"><span data-stu-id="4e51f-115">A typical *Program.cs* calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

<span data-ttu-id="4e51f-116">`CreateDefaultBuilder` führt folgende Ausgaben aus:</span><span class="sxs-lookup"><span data-stu-id="4e51f-116">`CreateDefaultBuilder` performs the following tasks:</span></span>

* <span data-ttu-id="4e51f-117">Konfiguriert des [Kestrel](xref:fundamentals/servers/kestrel)-Servers als Webserver mithilfe der Hostkonfigurationsanbieter der App.</span><span class="sxs-lookup"><span data-stu-id="4e51f-117">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="4e51f-118">Die Standardoptionen des Kestrel-Servers finden Sie unter <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="4e51f-118">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="4e51f-119">Legt das Inhaltsstammverzeichnis auf den Pfad fest, der von [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory) zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="4e51f-119">Sets the content root to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="4e51f-120">Lädt [Hostkonfiguration](#host-configuration-values) aus:</span><span class="sxs-lookup"><span data-stu-id="4e51f-120">Loads [host configuration](#host-configuration-values) from:</span></span>
  * <span data-ttu-id="4e51f-121">Umgebungsvariablen mit dem Präfix `ASPNETCORE_` (z.B. `ASPNETCORE_ENVIRONMENT`)</span><span class="sxs-lookup"><span data-stu-id="4e51f-121">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`).</span></span>
  * <span data-ttu-id="4e51f-122">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="4e51f-122">Command-line arguments.</span></span>
* <span data-ttu-id="4e51f-123">Lädt die App-Konfiguration in der folgenden Reihenfolge:</span><span class="sxs-lookup"><span data-stu-id="4e51f-123">Loads app configuration in the following order from:</span></span>
  * <span data-ttu-id="4e51f-124">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="4e51f-124">*appsettings.json*.</span></span>
  * <span data-ttu-id="4e51f-125">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="4e51f-125">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="4e51f-126">[Geheimnis-Manager](xref:security/app-secrets), wenn die App in der `Development`-Umgebung mit der Einstiegsassembly ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="4e51f-126">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="4e51f-127">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="4e51f-127">Environment variables.</span></span>
  * <span data-ttu-id="4e51f-128">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="4e51f-128">Command-line arguments.</span></span>
* <span data-ttu-id="4e51f-129">Konfiguriert die [Protokollierung](xref:fundamentals/logging/index) für die Konsolen- und Debugausgabe</span><span class="sxs-lookup"><span data-stu-id="4e51f-129">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="4e51f-130">Die Protokollierung umfasst Regeln zur [Protokollfilterung](xref:fundamentals/logging/index#log-filtering), die im Abschnitt für die Protokollierungskonfiguration einer *appsettings.json*- oder *appsettings.{Environment}.json*-Datei angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="4e51f-130">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="4e51f-131">Wenn die Ausführung mit dem [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) hinter den IIS erfolgt, ermöglicht `CreateDefaultBuilder` die [IIS-Integration](xref:host-and-deploy/iis/index), die die Basisadresse und den Port der App konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="4e51f-131">When running behind IIS with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` enables [IIS Integration](xref:host-and-deploy/iis/index), which configures the app's base address and port.</span></span> <span data-ttu-id="4e51f-132">Die IIS-Integration konfiguriert die App auch für das [Erfassen von Startfehlern](#capture-startup-errors).</span><span class="sxs-lookup"><span data-stu-id="4e51f-132">IIS Integration also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="4e51f-133">Informationen zu den IIS-Standardoptionen finden Sie unter <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="4e51f-133">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>
* <span data-ttu-id="4e51f-134">Legt [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) auf `true` fest, wenn die Umgebung der App „Development“ ist.</span><span class="sxs-lookup"><span data-stu-id="4e51f-134">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="4e51f-135">Weitere Informationen finden Sie unter [Bereichsvalidierung](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="4e51f-135">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="4e51f-136">Die durch `CreateDefaultBuilder` definierte Konfiguration kann von [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) und anderen Methoden sowie Erweiterungsmethoden von [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) überschrieben und erweitert werden.</span><span class="sxs-lookup"><span data-stu-id="4e51f-136">The configuration defined by `CreateDefaultBuilder` can be overridden and augmented by [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), and other methods and extension methods of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="4e51f-137">Es folgen einige Beispiele:</span><span class="sxs-lookup"><span data-stu-id="4e51f-137">A few examples follow:</span></span>

* <span data-ttu-id="4e51f-138">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) wird zum zusätzlichen Festlegen von `IConfiguration` für die App verwendet.</span><span class="sxs-lookup"><span data-stu-id="4e51f-138">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) is used to specify additional `IConfiguration` for the app.</span></span> <span data-ttu-id="4e51f-139">Der folgende `ConfigureAppConfiguration`-Aufruf fügt einen Delegaten hinzu, um die App-Konfiguration in die Datei *appsettings.xml* einzufügen.</span><span class="sxs-lookup"><span data-stu-id="4e51f-139">The following `ConfigureAppConfiguration` call adds a delegate to include app configuration in the *appsettings.xml* file.</span></span> <span data-ttu-id="4e51f-140">`ConfigureAppConfiguration` kann mehrmals aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="4e51f-140">`ConfigureAppConfiguration` may be called multiple times.</span></span> <span data-ttu-id="4e51f-141">Beachten Sie, dass diese Konfiguration nicht für den Host gilt (z.B. Server-URLs oder Umgebungen).</span><span class="sxs-lookup"><span data-stu-id="4e51f-141">Note that this configuration doesn't apply to the host (for example, server URLs or environment).</span></span> <span data-ttu-id="4e51f-142">Informationen finden Sie im Abschnitt [Hostkonfigurationswerte](#host-configuration-values).</span><span class="sxs-lookup"><span data-stu-id="4e51f-142">See the [Host configuration values](#host-configuration-values) section.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* <span data-ttu-id="4e51f-143">Der folgende `ConfigureLogging`-Aufruf fügt einen Delegaten hinzu, um den Mindestprotokolliergrad ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) auf [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel) zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="4e51f-143">The following `ConfigureLogging` call adds a delegate to configure the minimum logging level ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) to [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="4e51f-144">Diese Einstellung überschreibt die Einstellungen in *appsettings.Development.json* (`LogLevel.Debug`) und *appsettings.Production.json* (`LogLevel.Error`), die durch `CreateDefaultBuilder` konfiguriert wurden.</span><span class="sxs-lookup"><span data-stu-id="4e51f-144">This setting overrides the settings in *appsettings.Development.json* (`LogLevel.Debug`) and *appsettings.Production.json* (`LogLevel.Error`) configured by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4e51f-145">`ConfigureLogging` kann mehrmals aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="4e51f-145">`ConfigureLogging` may be called multiple times.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="4e51f-146">Der folgende Aufruf von `ConfigureKestrel` überschreibt den Standardwert von 30.000.000 Bytes von [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize), der festgelegt wurde, als Kestrel durch `CreateDefaultBuilder` konfiguriert wurde:</span><span class="sxs-lookup"><span data-stu-id="4e51f-146">The following call to `ConfigureKestrel` overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="4e51f-147">Der folgende Aufruf von [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) überschreibt den Standardwert von 30.000.000 Bytes von [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize), der festgelegt wurde, als Kestrel durch `CreateDefaultBuilder` konfiguriert wurde:</span><span class="sxs-lookup"><span data-stu-id="4e51f-147">The following call to [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

<span data-ttu-id="4e51f-148">Das *Inhaltsstammverzeichnis* bestimmt, wo der Host nach Inhaltsdateien (z.B. MVC-Ansichtsdateien) sucht.</span><span class="sxs-lookup"><span data-stu-id="4e51f-148">The *content root* determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="4e51f-149">Wenn die App aus dem Stammordner des Projekts gestartet wird, wird dieser als Inhaltsstammverzeichnis verwendet.</span><span class="sxs-lookup"><span data-stu-id="4e51f-149">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="4e51f-150">Dies ist die Standardeinstellung in [Visual Studio](https://www.visualstudio.com/) und für [neue .NET-Vorlagen](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="4e51f-150">This is the default used in [Visual Studio](https://www.visualstudio.com/) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="4e51f-151">Weitere Informationen zur App-Konfiguration finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="4e51f-151">For more information on app configuration, see <xref:fundamentals/configuration/index>.</span></span>

> [!NOTE]
> <span data-ttu-id="4e51f-152">Als Alternative zur Verwendung der statischen `CreateDefaultBuilder`-Methode ist das Erstellen eines Hosts von [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) ein unterstützter Ansatz mit ASP.NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="4e51f-152">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span> <span data-ttu-id="4e51f-153">Weitere Informationen finden Sie auf der Registerkarte zu ASP.NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="4e51f-153">For more information, see the ASP.NET Core 1.x tab.</span></span>

<span data-ttu-id="4e51f-154">Beim Einrichten eines Hosts können die Methoden [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure?view=aspnetcore-1.1) und [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices?view=aspnetcore-1.1) bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="4e51f-154">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure?view=aspnetcore-1.1) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices?view=aspnetcore-1.1) methods can be provided.</span></span> <span data-ttu-id="4e51f-155">Wenn eine `Startup`-Klasse angegeben wird, muss diese eine `Configure`-Methode definieren.</span><span class="sxs-lookup"><span data-stu-id="4e51f-155">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="4e51f-156">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="4e51f-156">For more information, see <xref:fundamentals/startup>.</span></span> <span data-ttu-id="4e51f-157">Wenn `ConfigureServices` mehrmals aufgerufen wird, werden diese Aufrufe einander angefügt.</span><span class="sxs-lookup"><span data-stu-id="4e51f-157">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="4e51f-158">Durch mehrere Aufrufe von `Configure` oder `UseStartup` in `WebHostBuilder` werden die vorherigen Einstellungen ersetzt.</span><span class="sxs-lookup"><span data-stu-id="4e51f-158">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="4e51f-159">Hostkonfigurationswerte</span><span class="sxs-lookup"><span data-stu-id="4e51f-159">Host configuration values</span></span>

<span data-ttu-id="4e51f-160">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) basiert auf folgenden Ansätzen, um die Hostkonfigurationswerte festzulegen:</span><span class="sxs-lookup"><span data-stu-id="4e51f-160">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="4e51f-161">Hostbuilderkonfigurationen, die Umgebungsvariablen im Format `ASPNETCORE_{configurationKey}` enthalten.</span><span class="sxs-lookup"><span data-stu-id="4e51f-161">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="4e51f-162">Beispielsweise `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="4e51f-162">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="4e51f-163">Erweiterungen wie [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) und [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (siehe Abschnitt [Überschreiben der Konfiguration](#override-configuration)).</span><span class="sxs-lookup"><span data-stu-id="4e51f-163">Extensions such as [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) and [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (see the [Override configuration](#override-configuration) section).</span></span>
* <span data-ttu-id="4e51f-164">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) und der zugeordnete Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="4e51f-164">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="4e51f-165">Wenn Sie einen Wert mit `UseSetting` festlegen, wird dieser unabhängig vom Typ als Zeichenfolge festgelegt.</span><span class="sxs-lookup"><span data-stu-id="4e51f-165">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="4e51f-166">Der Host verwendet die Option, die zuletzt einen Wert festlegt.</span><span class="sxs-lookup"><span data-stu-id="4e51f-166">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="4e51f-167">Weitere Informationen finden Sie im nächsten Abschnitt unter [Außerkraftsetzen der Konfiguration](#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="4e51f-167">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="application-key-name"></a><span data-ttu-id="4e51f-168">Anwendungsschlüssel (Name)</span><span class="sxs-lookup"><span data-stu-id="4e51f-168">Application Key (Name)</span></span>

<span data-ttu-id="4e51f-169">Die Eigenschaft [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) wird automatisch festgelegt, wenn [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) oder [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) während der Hosterstellung aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="4e51f-169">The [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="4e51f-170">Der Wert wird auf den Namen der Assembly festgelegt, die den Einstiegspunkt der App enthält.</span><span class="sxs-lookup"><span data-stu-id="4e51f-170">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="4e51f-171">Um den Wert explizit festzulegen, verwenden Sie den [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span><span class="sxs-lookup"><span data-stu-id="4e51f-171">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

<span data-ttu-id="4e51f-172">**Schlüssel:** Anwendungsname</span><span class="sxs-lookup"><span data-stu-id="4e51f-172">**Key**: applicationName</span></span>  
<span data-ttu-id="4e51f-173">**Typ:** *Zeichenfolge*</span><span class="sxs-lookup"><span data-stu-id="4e51f-173">**Type**: *string*</span></span>  
<span data-ttu-id="4e51f-174">**Standardwert**: Der Name der Assembly, die den Einstiegspunkt der App enthält.</span><span class="sxs-lookup"><span data-stu-id="4e51f-174">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="4e51f-175">**Festlegen mit:** `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="4e51f-175">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="4e51f-176">**Umgebungsvariable:** `ASPNETCORE_APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="4e51f-176">**Environment variable**: `ASPNETCORE_APPLICATIONNAME`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a><span data-ttu-id="4e51f-177">Erfassen von Startfehlern</span><span class="sxs-lookup"><span data-stu-id="4e51f-177">Capture Startup Errors</span></span>

<span data-ttu-id="4e51f-178">Diese Einstellung steuert das Erfassen von Startfehlern.</span><span class="sxs-lookup"><span data-stu-id="4e51f-178">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="4e51f-179">**Schlüssel:** captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="4e51f-179">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="4e51f-180">**Typ:** *Boolesch* (`true` or `1`)</span><span class="sxs-lookup"><span data-stu-id="4e51f-180">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="4e51f-181">**Standardwert**: Die Standardeinstellung ist gleich `false`, es sei denn, die App wird mit Kestrel hinter IIS ausgeführt, dann ist sie gleich `true`.</span><span class="sxs-lookup"><span data-stu-id="4e51f-181">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="4e51f-182">**Festlegen mit:** `CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="4e51f-182">**Set using**: `CaptureStartupErrors`</span></span>  
<span data-ttu-id="4e51f-183">**Umgebungsvariable:** `ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="4e51f-183">**Environment variable**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="4e51f-184">Wenn diese `false` ist, führen Fehler beim Start zum Beenden des Hosts.</span><span class="sxs-lookup"><span data-stu-id="4e51f-184">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="4e51f-185">Wenn diese `true` ist, erfasst der Host Ausnahmen während des Starts und versucht, den Server zu starten.</span><span class="sxs-lookup"><span data-stu-id="4e51f-185">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a><span data-ttu-id="4e51f-186">Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="4e51f-186">Content Root</span></span>

<span data-ttu-id="4e51f-187">Diese Einstellung bestimmt, wo ASP.NET mit der Suche nach Inhaltsdateien (z.B. MVC-Ansichten) beginnt.</span><span class="sxs-lookup"><span data-stu-id="4e51f-187">This setting determines where ASP.NET Core begins searching for content files, such as MVC views.</span></span> 

<span data-ttu-id="4e51f-188">**Schlüssel:** contentRoot</span><span class="sxs-lookup"><span data-stu-id="4e51f-188">**Key**: contentRoot</span></span>  
<span data-ttu-id="4e51f-189">**Typ:** *Zeichenfolge*</span><span class="sxs-lookup"><span data-stu-id="4e51f-189">**Type**: *string*</span></span>  
<span data-ttu-id="4e51f-190">**Standardwert**: Entspricht standardmäßig dem Ordner, in dem die App-Assembly gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="4e51f-190">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="4e51f-191">**Festlegen mit:** `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="4e51f-191">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="4e51f-192">**Umgebungsvariable:** `ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="4e51f-192">**Environment variable**: `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="4e51f-193">Das Inhaltsstammverzeichnis wird ebenfalls als Basispfad für die [Webstammeinstellung](#web-root) verwendet.</span><span class="sxs-lookup"><span data-stu-id="4e51f-193">The content root is also used as the base path for the [Web Root setting](#web-root).</span></span> <span data-ttu-id="4e51f-194">Wenn der Pfad nicht vorhanden ist, kann der Host nicht gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="4e51f-194">If the path doesn't exist, the host fails to start.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

### <a name="detailed-errors"></a><span data-ttu-id="4e51f-195">Detaillierte Fehler</span><span class="sxs-lookup"><span data-stu-id="4e51f-195">Detailed Errors</span></span>

<span data-ttu-id="4e51f-196">Bestimmt, ob detaillierte Fehler erfasst werden sollen.</span><span class="sxs-lookup"><span data-stu-id="4e51f-196">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="4e51f-197">**Schlüssel:** detailedErrors</span><span class="sxs-lookup"><span data-stu-id="4e51f-197">**Key**: detailedErrors</span></span>  
<span data-ttu-id="4e51f-198">**Typ:** *Boolesch* (`true` or `1`)</span><span class="sxs-lookup"><span data-stu-id="4e51f-198">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="4e51f-199">**Standard:** FALSE</span><span class="sxs-lookup"><span data-stu-id="4e51f-199">**Default**: false</span></span>  
<span data-ttu-id="4e51f-200">**Festlegen mit:** `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="4e51f-200">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="4e51f-201">**Umgebungsvariable:** `ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="4e51f-201">**Environment variable**: `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="4e51f-202">Wenn diese Einstellung aktiviert ist (oder die <a href="#environment">Umgebung</a> auf `Development` festgelegt ist), erfasst die App detaillierte Ausnahmen.</span><span class="sxs-lookup"><span data-stu-id="4e51f-202">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a><span data-ttu-id="4e51f-203">Umgebung</span><span class="sxs-lookup"><span data-stu-id="4e51f-203">Environment</span></span>

<span data-ttu-id="4e51f-204">Legt die Umgebung der App fest.</span><span class="sxs-lookup"><span data-stu-id="4e51f-204">Sets the app's environment.</span></span>

<span data-ttu-id="4e51f-205">**Schlüssel:** environment</span><span class="sxs-lookup"><span data-stu-id="4e51f-205">**Key**: environment</span></span>  
<span data-ttu-id="4e51f-206">**Typ:** *Zeichenfolge*</span><span class="sxs-lookup"><span data-stu-id="4e51f-206">**Type**: *string*</span></span>  
<span data-ttu-id="4e51f-207">**Standardwert**: Produktion</span><span class="sxs-lookup"><span data-stu-id="4e51f-207">**Default**: Production</span></span>  
<span data-ttu-id="4e51f-208">**Festlegen mit:** `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="4e51f-208">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="4e51f-209">**Umgebungsvariable:** `ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="4e51f-209">**Environment variable**: `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="4e51f-210">Die Umgebung kann auf einen beliebigen Wert festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="4e51f-210">The environment can be set to any value.</span></span> <span data-ttu-id="4e51f-211">Zu den durch Frameworks definierten Werten zählen `Development`, `Staging` und `Production`.</span><span class="sxs-lookup"><span data-stu-id="4e51f-211">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="4e51f-212">Die Groß-/Kleinschreibung wird bei Werten nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="4e51f-212">Values aren't case sensitive.</span></span> <span data-ttu-id="4e51f-213">Standardmäßig wird die *Umgebung* aus der `ASPNETCORE_ENVIRONMENT`-Umgebungsvariablen gelesen.</span><span class="sxs-lookup"><span data-stu-id="4e51f-213">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="4e51f-214">Wenn Sie [Visual Studio](https://www.visualstudio.com/) verwenden, werden Umgebungsvariablen möglicherweise in der Datei *launchSettings.json* festgelegt.</span><span class="sxs-lookup"><span data-stu-id="4e51f-214">When using [Visual Studio](https://www.visualstudio.com/), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="4e51f-215">Weitere Informationen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="4e51f-215">For more information, see <xref:fundamentals/environments>.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="4e51f-216">Hostingstartassemblys</span><span class="sxs-lookup"><span data-stu-id="4e51f-216">Hosting Startup Assemblies</span></span>

<span data-ttu-id="4e51f-217">Legt die Hostingstartassemblys der App fest.</span><span class="sxs-lookup"><span data-stu-id="4e51f-217">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="4e51f-218">**Schlüssel:** hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="4e51f-218">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="4e51f-219">**Typ:** *Zeichenfolge*</span><span class="sxs-lookup"><span data-stu-id="4e51f-219">**Type**: *string*</span></span>  
<span data-ttu-id="4e51f-220">**Standardwert**: Leere Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="4e51f-220">**Default**: Empty string</span></span>  
<span data-ttu-id="4e51f-221">**Festlegen mit:** `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="4e51f-221">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="4e51f-222">**Umgebungsvariable:** `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="4e51f-222">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="4e51f-223">Eine durch Semikolons getrennte Zeichenfolge der Hostingstartassemblys, die beim Start geladen werden soll.</span><span class="sxs-lookup"><span data-stu-id="4e51f-223">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span>

<span data-ttu-id="4e51f-224">Obwohl der Konfigurationswert standardmäßig auf eine leere Zeichenfolge festgelegt ist, enthalten die Hostingstartassemblys immer die Assembly der App.</span><span class="sxs-lookup"><span data-stu-id="4e51f-224">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="4e51f-225">Wenn Hostingstartassemblys bereitgestellt werden, werden diese zur Assembly der App hinzugefügt, damit diese geladen werden, wenn die App während des Starts die allgemeinen Dienste erstellt.</span><span class="sxs-lookup"><span data-stu-id="4e51f-225">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a><span data-ttu-id="4e51f-226">HTTPS-Anschluss</span><span class="sxs-lookup"><span data-stu-id="4e51f-226">HTTPS Port</span></span>

<span data-ttu-id="4e51f-227">Legen Sie den HTTPS-Umleitungsport fest.</span><span class="sxs-lookup"><span data-stu-id="4e51f-227">Set the HTTPS redirect port.</span></span> <span data-ttu-id="4e51f-228">Wird in [Erzwingen von HTTPS](xref:security/enforcing-ssl) verwendet.</span><span class="sxs-lookup"><span data-stu-id="4e51f-228">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="4e51f-229">**Schlüssel**: https_port **Typ**: *Zeichenfolge*
**Standardwert**: Es ist kein Standardwert festgelegt.</span><span class="sxs-lookup"><span data-stu-id="4e51f-229">**Key**: https_port **Type**: *string*
**Default**: A default value isn't set.</span></span>
<span data-ttu-id="4e51f-230">**Festlegen mit**: `UseSetting`
**Umgebungsvariable**: `ASPNETCORE_HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="4e51f-230">**Set using**: `UseSetting`
**Environment variable**: `ASPNETCORE_HTTPS_PORT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a><span data-ttu-id="4e51f-231">Auszuschließende Hostingstartassemblys</span><span class="sxs-lookup"><span data-stu-id="4e51f-231">Hosting Startup Exclude Assemblies</span></span>

<span data-ttu-id="4e51f-232">Eine durch Semikolons getrennte Zeichenfolge der Hostingstartassemblys, die beim Start ausgeschlossen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="4e51f-232">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="4e51f-233">**Schlüssel**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="4e51f-233">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="4e51f-234">**Typ:** *Zeichenfolge*</span><span class="sxs-lookup"><span data-stu-id="4e51f-234">**Type**: *string*</span></span>  
<span data-ttu-id="4e51f-235">**Standardwert**: Leere Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="4e51f-235">**Default**: Empty string</span></span>  
<span data-ttu-id="4e51f-236">**Festlegen mit:** `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="4e51f-236">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="4e51f-237">**Umgebungsvariable:** `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="4e51f-237">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a><span data-ttu-id="4e51f-238">Bevorzugen von Hosting-URLs</span><span class="sxs-lookup"><span data-stu-id="4e51f-238">Prefer Hosting URLs</span></span>

<span data-ttu-id="4e51f-239">Gibt an, ob der Hosts den URLs lauschen soll, die mit `WebHostBuilder` konfiguriert wurden, statt denen, die mit der `IServer`-Implementierung konfiguriert wurden.</span><span class="sxs-lookup"><span data-stu-id="4e51f-239">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="4e51f-240">**Schlüssel:** preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="4e51f-240">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="4e51f-241">**Typ:** *Boolesch* (`true` or `1`)</span><span class="sxs-lookup"><span data-stu-id="4e51f-241">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="4e51f-242">**Standard:** TRUE</span><span class="sxs-lookup"><span data-stu-id="4e51f-242">**Default**: true</span></span>  
<span data-ttu-id="4e51f-243">**Festlegen mit:** `PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="4e51f-243">**Set using**: `PreferHostingUrls`</span></span>  
<span data-ttu-id="4e51f-244">**Umgebungsvariable:** `ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="4e51f-244">**Environment variable**: `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a><span data-ttu-id="4e51f-245">Verhindern des Hostingstarts</span><span class="sxs-lookup"><span data-stu-id="4e51f-245">Prevent Hosting Startup</span></span>

<span data-ttu-id="4e51f-246">Verhindert das automatische Laden der Hostingstartassemblys, einschließlich denen, die von der Assembly der App konfiguriert wurden.</span><span class="sxs-lookup"><span data-stu-id="4e51f-246">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="4e51f-247">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4e51f-247">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="4e51f-248">**Schlüssel:** preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="4e51f-248">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="4e51f-249">**Typ:** *Boolesch* (`true` or `1`)</span><span class="sxs-lookup"><span data-stu-id="4e51f-249">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="4e51f-250">**Standard:** FALSE</span><span class="sxs-lookup"><span data-stu-id="4e51f-250">**Default**: false</span></span>  
<span data-ttu-id="4e51f-251">**Festlegen mit:** `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="4e51f-251">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="4e51f-252">**Umgebungsvariable:** `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="4e51f-252">**Environment variable**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a><span data-ttu-id="4e51f-253">Server-URLs</span><span class="sxs-lookup"><span data-stu-id="4e51f-253">Server URLs</span></span>

<span data-ttu-id="4e51f-254">Gibt die IP-Adressen oder Hostadressen mit Ports und Protokollen an, denen der Server für Anforderungen lauschen soll.</span><span class="sxs-lookup"><span data-stu-id="4e51f-254">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="4e51f-255">**Schlüssel:** urls</span><span class="sxs-lookup"><span data-stu-id="4e51f-255">**Key**: urls</span></span>  
<span data-ttu-id="4e51f-256">**Typ:** *Zeichenfolge*</span><span class="sxs-lookup"><span data-stu-id="4e51f-256">**Type**: *string*</span></span>  
<span data-ttu-id="4e51f-257">**Standard**: http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="4e51f-257">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="4e51f-258">**Festlegen mit:** `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="4e51f-258">**Set using**: `UseUrls`</span></span>  
<span data-ttu-id="4e51f-259">**Umgebungsvariable:** `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="4e51f-259">**Environment variable**: `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="4e51f-260">Legt die Einstellung auf eine durch Semikolons (;) getrennte Liste von URL-Präfixen fest, auf die der Server reagieren soll.</span><span class="sxs-lookup"><span data-stu-id="4e51f-260">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="4e51f-261">Beispielsweise `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="4e51f-261">For example, `http://localhost:123`.</span></span> <span data-ttu-id="4e51f-262">Verwenden Sie \*, um anzugeben, dass der Server mithilfe des angegebenen Ports und Protokolls (z.B. `http://*:5000`) den Anfragen aller IP-Adressen oder Hostnamen lauschen soll.</span><span class="sxs-lookup"><span data-stu-id="4e51f-262">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="4e51f-263">Das Protokoll (`http://` oder `https://`) muss in jeder URL enthalten sein.</span><span class="sxs-lookup"><span data-stu-id="4e51f-263">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="4e51f-264">Die unterstützten Formate variieren bei den verschiedenen Servern.</span><span class="sxs-lookup"><span data-stu-id="4e51f-264">Supported formats vary among servers.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

<span data-ttu-id="4e51f-265">Kestrel verfügt über eine eigene API für die Endpunktkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="4e51f-265">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="4e51f-266">Weitere Informationen finden Sie unter <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4e51f-266">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="4e51f-267">Timeout beim Herunterfahren</span><span class="sxs-lookup"><span data-stu-id="4e51f-267">Shutdown Timeout</span></span>

<span data-ttu-id="4e51f-268">Gibt die Wartezeit an, bis der Webhost heruntergefahren wird.</span><span class="sxs-lookup"><span data-stu-id="4e51f-268">Specifies the amount of time to wait for the web host to shut down.</span></span>

<span data-ttu-id="4e51f-269">**Schlüssel:** shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="4e51f-269">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="4e51f-270">**Typ:** *Ganze Zahl*</span><span class="sxs-lookup"><span data-stu-id="4e51f-270">**Type**: *int*</span></span>  
<span data-ttu-id="4e51f-271">**Standardwert**: 5</span><span class="sxs-lookup"><span data-stu-id="4e51f-271">**Default**: 5</span></span>  
<span data-ttu-id="4e51f-272">**Festlegen mit:** `UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="4e51f-272">**Set using**: `UseShutdownTimeout`</span></span>  
<span data-ttu-id="4e51f-273">**Umgebungsvariable:** `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="4e51f-273">**Environment variable**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="4e51f-274">Obwohl der Schlüssel eine *ganze Zahl* mit `UseSetting` (z.B. `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`) akzeptiert, akzeptiert die Erweiterungsmethode [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) ein [TimeSpan](/dotnet/api/system.timespan)-Objekt.</span><span class="sxs-lookup"><span data-stu-id="4e51f-274">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span>

<span data-ttu-id="4e51f-275">Während des Zeitlimits, verhält sich das Hosting folgendermaßen:</span><span class="sxs-lookup"><span data-stu-id="4e51f-275">During the timeout period, hosting:</span></span>

* <span data-ttu-id="4e51f-276">Es löst [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping) aus.</span><span class="sxs-lookup"><span data-stu-id="4e51f-276">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="4e51f-277">Es versucht, gehostete Dienste zu beenden und protokolliert Fehler für Dienste, die nicht beendet werden können.</span><span class="sxs-lookup"><span data-stu-id="4e51f-277">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="4e51f-278">Wenn das Zeitlimit abläuft bevor alle gehosteten Dienste beendet sind, werden alle verbleibenden aktiven Dienste beendet, wenn die App herunterfährt.</span><span class="sxs-lookup"><span data-stu-id="4e51f-278">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="4e51f-279">Die Dienste werden beendet, selbst wenn die Verarbeitung noch nicht abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="4e51f-279">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="4e51f-280">Wenn die Dienste mehr Zeit zum Beenden benötigen, sollten Sie das Zeitlimit erhöhen.</span><span class="sxs-lookup"><span data-stu-id="4e51f-280">If services require additional time to stop, increase the timeout.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a><span data-ttu-id="4e51f-281">Startassembly</span><span class="sxs-lookup"><span data-stu-id="4e51f-281">Startup Assembly</span></span>

<span data-ttu-id="4e51f-282">Bestimmt die Assembly, die nach der `Startup`-Klasse suchen soll.</span><span class="sxs-lookup"><span data-stu-id="4e51f-282">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="4e51f-283">**Schlüssel:** startupAssembly</span><span class="sxs-lookup"><span data-stu-id="4e51f-283">**Key**: startupAssembly</span></span>  
<span data-ttu-id="4e51f-284">**Typ:** *Zeichenfolge*</span><span class="sxs-lookup"><span data-stu-id="4e51f-284">**Type**: *string*</span></span>  
<span data-ttu-id="4e51f-285">**Standardwert**: Die Assembly der App</span><span class="sxs-lookup"><span data-stu-id="4e51f-285">**Default**: The app's assembly</span></span>  
<span data-ttu-id="4e51f-286">**Festlegen mit:** `UseStartup`</span><span class="sxs-lookup"><span data-stu-id="4e51f-286">**Set using**: `UseStartup`</span></span>  
<span data-ttu-id="4e51f-287">**Umgebungsvariable:** `ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="4e51f-287">**Environment variable**: `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="4e51f-288">Es kann auf die Assembly nach Name (`string`) oder Typ (`TStartup`) verwiesen werden.</span><span class="sxs-lookup"><span data-stu-id="4e51f-288">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="4e51f-289">Wenn mehrere `UseStartup`-Methoden aufgerufen werden, hat die letzte Vorrang.</span><span class="sxs-lookup"><span data-stu-id="4e51f-289">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a><span data-ttu-id="4e51f-290">Webstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="4e51f-290">Web Root</span></span>

<span data-ttu-id="4e51f-291">Legt den relativen Pfad für die statischen Objekte der App fest.</span><span class="sxs-lookup"><span data-stu-id="4e51f-291">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="4e51f-292">**Schlüssel:** webroot</span><span class="sxs-lookup"><span data-stu-id="4e51f-292">**Key**: webroot</span></span>  
<span data-ttu-id="4e51f-293">**Typ:** *Zeichenfolge*</span><span class="sxs-lookup"><span data-stu-id="4e51f-293">**Type**: *string*</span></span>  
<span data-ttu-id="4e51f-294">**Standardwert**: Wenn nichts anderes angegeben und der Pfad vorhanden ist, ist der Standardwert „(Inhaltsstammverzeichnis)/wwwroot“.</span><span class="sxs-lookup"><span data-stu-id="4e51f-294">**Default**: If not specified, the default is "(Content Root)/wwwroot", if the path exists.</span></span> <span data-ttu-id="4e51f-295">Wenn der Pfad nicht vorhanden ist, wird ein Dateianbieter ohne Funktion verwendet.</span><span class="sxs-lookup"><span data-stu-id="4e51f-295">If the path doesn't exist, then a no-op file provider is used.</span></span>  
<span data-ttu-id="4e51f-296">**Festlegen mit:** `UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="4e51f-296">**Set using**: `UseWebRoot`</span></span>  
<span data-ttu-id="4e51f-297">**Umgebungsvariable:** `ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="4e51f-297">**Environment variable**: `ASPNETCORE_WEBROOT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

## <a name="override-configuration"></a><span data-ttu-id="4e51f-298">Außerkraftsetzen der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="4e51f-298">Override configuration</span></span>

<span data-ttu-id="4e51f-299">Verwenden Sie die [Konfiguration](xref:fundamentals/configuration/index), um den Webhost zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="4e51f-299">Use [Configuration](xref:fundamentals/configuration/index) to configure the web host.</span></span> <span data-ttu-id="4e51f-300">Im folgenden Beispiel wird die Hostkonfiguration optional in einer *hostsettings.json*-Datei angegeben.</span><span class="sxs-lookup"><span data-stu-id="4e51f-300">In the following example, host configuration is optionally specified in a *hostsettings.json* file.</span></span> <span data-ttu-id="4e51f-301">Jede Konfiguration, die aus der Datei *hostsettings.json* geladen wird, kann von Befehlszeilenargumenten überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="4e51f-301">Any configuration loaded from the *hostsettings.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="4e51f-302">Die erstellte Konfiguration (in `config`) wird verwendet, um den Host mit [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="4e51f-302">The built configuration (in `config`) is used to configure the host with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span></span> <span data-ttu-id="4e51f-303">Der Konfiguration der App wird die `IWebHostBuilder`-Konfiguration hinzugefügt, das Gegenteil gilt jedoch nicht. `ConfigureAppConfiguration` hat keinen Einfluss auf die `IWebHostBuilder`-Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="4e51f-303">`IWebHostBuilder` configuration is added to the app's configuration, but the converse isn't true&mdash;`ConfigureAppConfiguration` doesn't affect the `IWebHostBuilder` configuration.</span></span>

<span data-ttu-id="4e51f-304">Das Überschreiben der Konfiguration wird von `UseUrls` bereitgestellt, hierbei kommt die Konfiguration *hostsettings.json* zuerst und anschließend die Konfiguration der Befehlszeilenargumente:</span><span class="sxs-lookup"><span data-stu-id="4e51f-304">Overriding the configuration provided by `UseUrls` with *hostsettings.json* config first, command-line argument config second:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hostsettings.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            });
    }
}
```

<span data-ttu-id="4e51f-305">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="4e51f-305">*hostsettings.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> <span data-ttu-id="4e51f-306">Die [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration)-Erweiterungsmethode kann derzeit keine Konfigurationsabschnitte (z.B. `.UseConfiguration(Configuration.GetSection("section"))`) analysieren, die von `GetSection` zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="4e51f-306">The [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) extension method isn't currently capable of parsing a configuration section returned by `GetSection` (for example, `.UseConfiguration(Configuration.GetSection("section"))`.</span></span> <span data-ttu-id="4e51f-307">Die `GetSection`-Methode filtert die Konfigurationsschlüssel des angeforderten Abschnitts, behält jedoch den Abschnittsnamen in den Schlüsseln bei (z.B. `section:urls`, `section:environment`).</span><span class="sxs-lookup"><span data-stu-id="4e51f-307">The `GetSection` method filters the configuration keys to the section requested but leaves the section name on the keys (for example, `section:urls`, `section:environment`).</span></span> <span data-ttu-id="4e51f-308">Die `UseConfiguration`-Methode erwartet, dass die Schlüssel den `WebHostBuilder`-Schlüsseln entsprechen (z.B. `urls`, `environment`).</span><span class="sxs-lookup"><span data-stu-id="4e51f-308">The `UseConfiguration` method expects the keys to match the `WebHostBuilder` keys (for example, `urls`, `environment`).</span></span> <span data-ttu-id="4e51f-309">Das Vorhandensein des Abschnittsnamens in den Schlüsseln verhindert, dass die Werte des Abschnitts den Host konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="4e51f-309">The presence of the section name on the keys prevents the section's values from configuring the host.</span></span> <span data-ttu-id="4e51f-310">Dieses Problem wird in einer bevorstehenden Version behoben.</span><span class="sxs-lookup"><span data-stu-id="4e51f-310">This issue will be addressed in an upcoming release.</span></span> <span data-ttu-id="4e51f-311">Weitere Informationen und Problemumgehungen finden Sie unter [Passing configuration section into WebHostBuilder.UseConfiguration uses full keys (Beim Übergeben des Konfigurationsabschnitts an WebHostBuilder.UseConfiguration werden vollständige Schlüssel verwendet)](https://github.com/aspnet/Hosting/issues/839).</span><span class="sxs-lookup"><span data-stu-id="4e51f-311">For more information and workarounds, see [Passing configuration section into WebHostBuilder.UseConfiguration uses full keys](https://github.com/aspnet/Hosting/issues/839).</span></span>
>
> <span data-ttu-id="4e51f-312">`UseConfiguration` kopiert nur Schlüssel aus der bereitgestellten `IConfiguration` in die Konfiguration des Host-Generators.</span><span class="sxs-lookup"><span data-stu-id="4e51f-312">`UseConfiguration` only copies keys from the provided `IConfiguration` to the host builder configuration.</span></span> <span data-ttu-id="4e51f-313">Daher hat das Festlegen von `reloadOnChange: true` für JSON-, INI- und XML-Einstellungsdateien keine Auswirkung.</span><span class="sxs-lookup"><span data-stu-id="4e51f-313">Therefore, setting `reloadOnChange: true` for JSON, INI, and XML settings files has no effect.</span></span>

<span data-ttu-id="4e51f-314">Damit der Host auf einer bestimmten URL ausgeführt wird, kann der gewünschte Wert von der Befehlszeile aus übergeben werden, wenn [dotnet run](/dotnet/core/tools/dotnet-run) ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="4e51f-314">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="4e51f-315">Das Befehlszeilenargument überschreibt den `urls`-Wert der *hostsettings.json*-Datei, und der Server lauscht Port 8080:</span><span class="sxs-lookup"><span data-stu-id="4e51f-315">The command-line argument overrides the `urls` value from the *hostsettings.json* file, and the server listens on port 8080:</span></span>

```console
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="4e51f-316">Verwalten des Hosts</span><span class="sxs-lookup"><span data-stu-id="4e51f-316">Manage the host</span></span>

<span data-ttu-id="4e51f-317">**Run**</span><span class="sxs-lookup"><span data-stu-id="4e51f-317">**Run**</span></span>

<span data-ttu-id="4e51f-318">Die `Run`-Methode startet die Web-App und blockiert den aufrufenden Thread, bis der Host heruntergefahren wird:</span><span class="sxs-lookup"><span data-stu-id="4e51f-318">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="4e51f-319">**Starten**</span><span class="sxs-lookup"><span data-stu-id="4e51f-319">**Start**</span></span>

<span data-ttu-id="4e51f-320">Führen Sie den Host auf nicht blockierende Weise aus, indem Sie dessen `Start`-Methode aufrufen:</span><span class="sxs-lookup"><span data-stu-id="4e51f-320">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="4e51f-321">Wenn eine Liste mit URLs an die `Start`-Methode übergeben wird, lauscht diese den angegebenen URLs:</span><span class="sxs-lookup"><span data-stu-id="4e51f-321">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

<span data-ttu-id="4e51f-322">Die App kann einen neuen Host, der die vorab konfigurierten Standardwerte von `CreateDefaultBuilder` verwendet, mithilfe einer statischen Hilfsmethode initialisieren und starten.</span><span class="sxs-lookup"><span data-stu-id="4e51f-322">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="4e51f-323">Diese Methoden starten den Server ohne Konsolenausgabe und mit der [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown)-Methode, die auf eine Unterbrechung wartet (STRG+C/SIGINT oder SIGTERM):</span><span class="sxs-lookup"><span data-stu-id="4e51f-323">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="4e51f-324">**Start(RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="4e51f-324">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="4e51f-325">Beginnend mit einer `RequestDelegate`-Funktion:</span><span class="sxs-lookup"><span data-stu-id="4e51f-325">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="4e51f-326">Stellen Sie im Browser eine Anforderung an `http://localhost:5000`, um die Antwort „Hello World!“ zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="4e51f-326">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="4e51f-327">`WaitForShutdown` blockiert, bis eine Unterbrechung (STRG+C/SIGINT oder SIGTERM) ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="4e51f-327">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="4e51f-328">Die App zeigt die `Console.WriteLine`-Meldung an und wartet, bis diese durch Tastendruck beendet wird.</span><span class="sxs-lookup"><span data-stu-id="4e51f-328">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="4e51f-329">**Start(string url, RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="4e51f-329">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="4e51f-330">Beginnend mit einer URL und `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="4e51f-330">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="4e51f-331">Erzeugt das gleiche Ergebnis wie **Start(RequestDelegate app)**, mit dem Unterschied, dass die App auf `http://localhost:8080` reagiert.</span><span class="sxs-lookup"><span data-stu-id="4e51f-331">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="4e51f-332">**Start(Action&lt;IRouteBuilder&gt; routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="4e51f-332">**Start(Action&lt;IRouteBuilder&gt; routeBuilder)**</span></span>

<span data-ttu-id="4e51f-333">Verwenden Sie eine Instanz von `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)), um Routingmiddleware zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="4e51f-333">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="4e51f-334">Verwenden Sie folgende Browseranforderung mit dem Beispiel:</span><span class="sxs-lookup"><span data-stu-id="4e51f-334">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="4e51f-335">Anforderung</span><span class="sxs-lookup"><span data-stu-id="4e51f-335">Request</span></span>                                    | <span data-ttu-id="4e51f-336">Antwort</span><span class="sxs-lookup"><span data-stu-id="4e51f-336">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="4e51f-337">Hello, Martin!</span><span class="sxs-lookup"><span data-stu-id="4e51f-337">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="4e51f-338">Buenos dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="4e51f-338">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="4e51f-339">Löst eine Ausnahme mit der Zeichenfolge „ooops!“ aus.</span><span class="sxs-lookup"><span data-stu-id="4e51f-339">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="4e51f-340">Löst eine Ausnahme mit der Zeichenfolge „Uh oh!“ aus.</span><span class="sxs-lookup"><span data-stu-id="4e51f-340">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="4e51f-341">Sante, Kevin!</span><span class="sxs-lookup"><span data-stu-id="4e51f-341">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="4e51f-342">Hello World!</span><span class="sxs-lookup"><span data-stu-id="4e51f-342">Hello World!</span></span>                             |

<span data-ttu-id="4e51f-343">`WaitForShutdown` blockiert, bis eine Unterbrechung (STRG+C/SIGINT oder SIGTERM) ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="4e51f-343">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="4e51f-344">Die App zeigt die `Console.WriteLine`-Meldung an und wartet, bis diese durch Tastendruck beendet wird.</span><span class="sxs-lookup"><span data-stu-id="4e51f-344">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="4e51f-345">**Start(string url, Action&lt;IRouteBuilder&gt; routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="4e51f-345">**Start(string url, Action&lt;IRouteBuilder&gt; routeBuilder)**</span></span>

<span data-ttu-id="4e51f-346">Verwenden Sie eine URL und eine Instanz von `IRouteBuilder`:</span><span class="sxs-lookup"><span data-stu-id="4e51f-346">Use a URL and an instance of `IRouteBuilder`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="4e51f-347">Erzeugt das gleiche Ergebnis wie **Start(Action&lt;IRouteBuilder&gt; routeBuilder)**, mit dem Unterschied, dass die App auf `http://localhost:8080` reagiert.</span><span class="sxs-lookup"><span data-stu-id="4e51f-347">Produces the same result as **Start(Action&lt;IRouteBuilder&gt; routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="4e51f-348">**StartWith(Action&lt;IApplicationBuilder&gt; app)**</span><span class="sxs-lookup"><span data-stu-id="4e51f-348">**StartWith(Action&lt;IApplicationBuilder&gt; app)**</span></span>

<span data-ttu-id="4e51f-349">Stellen Sie einen Delegaten bereit, um eine `IApplicationBuilder`-Schnittstelle zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="4e51f-349">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="4e51f-350">Stellen Sie im Browser eine Anforderung an `http://localhost:5000`, um die Antwort „Hello World!“ zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="4e51f-350">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="4e51f-351">`WaitForShutdown` blockiert, bis eine Unterbrechung (STRG+C/SIGINT oder SIGTERM) ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="4e51f-351">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="4e51f-352">Die App zeigt die `Console.WriteLine`-Meldung an und wartet, bis diese durch Tastendruck beendet wird.</span><span class="sxs-lookup"><span data-stu-id="4e51f-352">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="4e51f-353">**StartWith(string url, Action&lt;IApplicationBuilder&gt; app)**</span><span class="sxs-lookup"><span data-stu-id="4e51f-353">**StartWith(string url, Action&lt;IApplicationBuilder&gt; app)**</span></span>

<span data-ttu-id="4e51f-354">Stellen Sie eine URL und einen Delegaten bereit, um eine `IApplicationBuilder`-Schnittstelle zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="4e51f-354">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="4e51f-355">Erzeugt das gleiche Ergebnis wie **StartWith(Action&lt;IApplicationBuilder&gt; app)**, mit dem Unterschied, dass die App auf `http://localhost:8080` reagiert.</span><span class="sxs-lookup"><span data-stu-id="4e51f-355">Produces the same result as **StartWith(Action&lt;IApplicationBuilder&gt; app)**, except the app responds on `http://localhost:8080`.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="4e51f-356">IHostingEnvironment-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="4e51f-356">IHostingEnvironment interface</span></span>

<span data-ttu-id="4e51f-357">Die [IHostingEnvironment-Schnittstelle](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) stellt Informationen über die Webhostingumgebung der App bereit.</span><span class="sxs-lookup"><span data-stu-id="4e51f-357">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="4e51f-358">Verwenden Sie [Constructor Injection](xref:fundamentals/dependency-injection) zum Abrufen der `IHostingEnvironment`-Schnittstelle, um deren Eigenschaften und Erweiterungsmethoden zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="4e51f-358">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="4e51f-359">Ein [konventionsbasierter Ansatz](xref:fundamentals/environments#environment-based-startup-class-and-methods) kann verwendet werden, um die App beim Start je nach Umgebung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="4e51f-359">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="4e51f-360">Fügen Sie alternativ die `IHostingEnvironment`-Schnittstelle in den `Startup`-Konstruktor für die Verwendung in `ConfigureServices` ein:</span><span class="sxs-lookup"><span data-stu-id="4e51f-360">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="4e51f-361">Zusätzlich zur `IsDevelopment`-Erweiterungsmethode stellt die `IHostingEnvironment`-Schnittstelle die `IsStaging`-, `IsProduction`- und `IsEnvironment(string environmentName)`-Methoden bereit.</span><span class="sxs-lookup"><span data-stu-id="4e51f-361">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="4e51f-362">Weitere Informationen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="4e51f-362">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="4e51f-363">Der `IHostingEnvironment`-Dienst kann ebenfalls direkt in die `Configure`-Methode eingefügt werden, um die Verarbeitungspipeline einzurichten:</span><span class="sxs-lookup"><span data-stu-id="4e51f-363">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the developer exception page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="4e51f-364">`IHostingEnvironment` kann in die `Invoke`-Methode eingefügt werden, wenn eine benutzerdefinierte [Middleware](xref:fundamentals/middleware/index#write-middleware) erstellt wird:</span><span class="sxs-lookup"><span data-stu-id="4e51f-364">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/index#write-middleware):</span></span>

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="4e51f-365">IApplicationLifetime-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="4e51f-365">IApplicationLifetime interface</span></span>

<span data-ttu-id="4e51f-366">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) ermöglicht Aktivitäten nach dem Starten und beim Herunterfahren.</span><span class="sxs-lookup"><span data-stu-id="4e51f-366">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="4e51f-367">Bei drei der Eigenschaften der Schnittstelle handelt es sich um Abbruchtokens, die zum Registrieren von `Action`-Methoden verwendet werden, durch die Ereignisse beim Starten und Herunterfahren definiert werden.</span><span class="sxs-lookup"><span data-stu-id="4e51f-367">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="4e51f-368">Abbruchtoken</span><span class="sxs-lookup"><span data-stu-id="4e51f-368">Cancellation Token</span></span>    | <span data-ttu-id="4e51f-369">Wird ausgelöst, wenn&#8230;</span><span class="sxs-lookup"><span data-stu-id="4e51f-369">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="4e51f-370">ApplicationStarted</span><span class="sxs-lookup"><span data-stu-id="4e51f-370">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="4e51f-371">Der Host vollständig gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="4e51f-371">The host has fully started.</span></span> |
| [<span data-ttu-id="4e51f-372">ApplicationStopped</span><span class="sxs-lookup"><span data-stu-id="4e51f-372">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="4e51f-373">Der Host ein ordnungsgemäßes Herunterfahren abschließt.</span><span class="sxs-lookup"><span data-stu-id="4e51f-373">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="4e51f-374">Alle Anforderungen sollten verarbeitet worden sein.</span><span class="sxs-lookup"><span data-stu-id="4e51f-374">All requests should be processed.</span></span> <span data-ttu-id="4e51f-375">Das Herunterfahren wird blockiert, bis das Ereignis abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="4e51f-375">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="4e51f-376">ApplicationStopping</span><span class="sxs-lookup"><span data-stu-id="4e51f-376">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="4e51f-377">Der Host ein ordnungsgemäßes Herunterfahren ausführt.</span><span class="sxs-lookup"><span data-stu-id="4e51f-377">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="4e51f-378">Anforderungen möglicherweise noch verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="4e51f-378">Requests may still be processing.</span></span> <span data-ttu-id="4e51f-379">Das Herunterfahren wird blockiert, bis das Ereignis abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="4e51f-379">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

<span data-ttu-id="4e51f-380">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) fordert das Beenden der Anwendung an.</span><span class="sxs-lookup"><span data-stu-id="4e51f-380">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="4e51f-381">Die folgende Klasse verwendet `StopApplication`, um eine App ordnungsgemäß herunterzufahren, wenn die `Shutdown`-Methode der Klasse aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="4e51f-381">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

## <a name="scope-validation"></a><span data-ttu-id="4e51f-382">Bereichsvalidierung</span><span class="sxs-lookup"><span data-stu-id="4e51f-382">Scope validation</span></span>

<span data-ttu-id="4e51f-383">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) legt [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) auf `true` fest, wenn die Umgebung der App „Entwicklung“ ist.</span><span class="sxs-lookup"><span data-stu-id="4e51f-383">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="4e51f-384">Wenn `ValidateScopes` auf `true` festgelegt wird, führt der Standarddienstanbieter Überprüfungen durch, um sicherzustellen, dass:</span><span class="sxs-lookup"><span data-stu-id="4e51f-384">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="4e51f-385">Bereichsbezogene Dienste nicht direkt oder indirekt vom Stammdienstanbieter aufgelöst werden</span><span class="sxs-lookup"><span data-stu-id="4e51f-385">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="4e51f-386">Bereichsbezogene Dienste nicht direkt oder indirekt in Singletons eingefügt werden</span><span class="sxs-lookup"><span data-stu-id="4e51f-386">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="4e51f-387">Der Stammdienstanbieter wird erstellt, wenn [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="4e51f-387">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="4e51f-388">Die Lebensdauer des Stammdienstanbieters entspricht der Lebensdauer der App/des Servers, wenn der Anbieter mit der App erstellt wird und verworfen wird, wenn die App beendet wird.</span><span class="sxs-lookup"><span data-stu-id="4e51f-388">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="4e51f-389">Bereichsbezogene Dienste werden von dem Container verworfen, der sie erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="4e51f-389">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="4e51f-390">Wenn ein bereichsbezogener Dienst im Stammcontainer erstellt wird, wird die Lebensdauer effektiv auf Singleton heraufgestuft, da er nur vom Stammcontainer verworfen wird, wenn die App/der Server heruntergefahren wird.</span><span class="sxs-lookup"><span data-stu-id="4e51f-390">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="4e51f-391">Die Überprüfung bereichsbezogener Dienste erfasst diese Situationen, wenn `BuildServiceProvider` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="4e51f-391">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="4e51f-392">Konfigurieren Sie [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) mit [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) auf dem Hostbuilder, um Bereiche immer zu validieren, auch in der Produktionsumgebung:</span><span class="sxs-lookup"><span data-stu-id="4e51f-392">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a><span data-ttu-id="4e51f-393">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="4e51f-393">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
